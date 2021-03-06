CONVERSION ERROR

Original source:

```trac
= Block Objects: Marshalling Them Explicitly =

In the following, we use the standard Haskell 2010 FFI to explicitly marshal Haskell functions to C blocks and vice versa.

== The qsort_b example using a foreign wrapper ==

The most straight-forward approach is to use the existing FFI support for turning Haskell functions into C function pointers by way of a foreign import wrapper declaration.  We can then embed these C function pointers in block literals without the need for a explicit environment on the C side.

=== Block literals ===

Blocks are by default allocated on the stack in C and only promoted to the heap using an explicity `Block_copy()` function.  In Haskell, we always allocate them on the heap, which is indicated by the pointer constant that we place in every block literal's `isa` field:
{{{
foreign import ccall "& _NSConcreteGlobalBlock" nsConcreteGlobalBlock :: Ptr ()
}}}

A block literal with an empty environment has the following layout:
{{{
-- Layout of the block literal (64-bit runtime)
--
-- .quad	__NSConcreteGlobalBlock           # void *isa;
-- .long	1342177280                        # int  flags = 0x50000000;
-- .long	0                                 # int  reserved;
-- .quad	___block_invoke                   # void (*invoke)(void *, ...);
-- .quad	___block_descriptor               # struct Block_descriptor *descriptor;

long, quad :: Int
long = 4  -- long word = 32 bit
quad = 8  -- quad word = 64 bit

isaOffset, flagsOffset, invokeOffset, descriptorOffset, blockLiteralSize :: Int
isaOffset        = 0
flagsOffset      = isaOffset        + quad
invokeOffset     = flagsOffset      + long + long
descriptorOffset = invokeOffset     + quad
blockLiteralSize = descriptorOffset + quad
}}}

In Haskell, we represent block literals as opaque pointers:
{{{
newtype Block a = Block (Ptr (Block a))
}}}

When turning a Haskell function into a C function pointer to be included in a block literal as the `invoke` function, we need to take care to add a pointer to the block literal itself as a new first argument:
{{{
mkBlock :: ((Block f -> f) -> IO (FunPtr (Block f -> f))) -> f -> IO (Block f)
mkBlock mkWrapper f
  = do { fPtr     <- mkWrapper (const f)
       ; blockPtr <- mallocBytes blockLiteralSize
       ; poke (blockPtr `plusPtr` isaOffset)        nsConcreteGlobalBlock
       ; poke (blockPtr `plusPtr` flagsOffset)      (0x50000000 :: Word32)
       ; poke (blockPtr `plusPtr` invokeOffset)     fPtr
       ; poke (blockPtr `plusPtr` descriptorOffset) descriptorPtr
       ; return $ Block blockPtr
       }
}}}

The block descriptor is static, except for the signature that we omit for the moment.
{{{
-- Block descriptor structure shared between all blocks.
--
-- .quad	0                                 # unsigned long int reserved;
-- .quad	32                                # unsigned long int size = blockLiteralSize;
-- .quad	signature_str                     # const char *signature;
-- .quad	0                                 # <undocumented>

descriptorPtr :: Ptr ()
descriptorPtr
  = unsafePerformIO $ 
    do { descPtr <- mallocBytes (4 * quad)
       ; poke (descPtr `plusPtr` (0 * quad)) (0 :: Word64)
       ; poke (descPtr `plusPtr` (1 * quad)) blockLiteralSizeWord64
       ; poke (descPtr `plusPtr` (2 * quad)) nullPtr    -- gcc puts a NULL in; should be ok for now
       ; poke (descPtr `plusPtr` (3 * quad)) (0 :: Word64)
       ; return descPtr
       }
  where
    blockLiteralSizeWord64 :: Word64
    blockLiteralSizeWord64 = fromIntegral blockLiteralSize
}}}

=== Turning a comparison function into a block literal ===

The comparison function passed to `qsort_b`, gets pointers to the array elements it is to compare.  As these array elements are marshalled Haskell thunks, they are themselves '''stable''' pointers to the actual values that ought to be compared.
{{{
type CmpFun a = Ptr (StablePtr a) -> Ptr (StablePtr a) -> IO Int
}}}
We use a foreign import wrapper to perform the actual marshalling of the Haskell function
{{{
foreign import ccall "wrapper" mkCmpWrapper 
  :: (Block (CmpFun a) -> CmpFun a) -> IO (FunPtr (Block (CmpFun a) -> CmpFun a))
}}}
and pass that wrapper to the `mkBlock` function when creating a block:
{{{
mkCmpBlock :: CmpFun a -> IO (Block (CmpFun a))
mkCmpBlock = mkBlock mkCmpWrapper
}}}

=== Calling quicksort ===

With these auxiliary definitions, the actual invocation of `qsort_b` is straight forward.  We import `qsort_b` with an explicit block argument for the comparison function:
{{{
foreign import ccall "stdlib.h" qsort_b 
  :: Ptr (StablePtr a) -> CSize -> CSize -> Block (CmpFun a) -> IO ()
}}}
Then, we use `mkCmpBlock` to turn the Haskell comparison into a block literal that we pass as the last argument to `qsort_b`:
{{{
do {   -- convert a list of strings into a C array of stable pointers to those strings in the
       -- Haskell heap
   ; ptrs <- mapM newStablePtr myCharacters
   ; sortedPtrs <- withArray ptrs $ \myCharactersArray -> do
       {
           -- get the size in bytes of a stable pointer to a Haskell string
       ; let elemSize = fromIntegral $ sizeOf (undefined :: StablePtr String)

           -- invoke C land 'qsort_b' with a Haskell comparison function passed as a block
           -- object; mutates 'myCharactersArray'
       ; cmpBlock <- mkCmpBlock $ \lPtr rPtr -> do 
           { l <- deRefStablePtr =<< peek lPtr
           ; r <- deRefStablePtr =<< peek rPtr
           ; return $ fromOrdering (l `compare` r)
           }
       ; qsort_b myCharactersArray (genericLength myCharacters) elemSize cmpBlock

       ; peekArray (length ptrs) myCharactersArray
       }

      -- turn the array of Haskell strings back into a list of strings
   ; mySortedCharacters <- mapM deRefStablePtr sortedPtrs
   }
}}}
The complete code is in the attachment `QSortB_wrapper.hs`.

'''TODO:'''
 * Second version without involving an adjustor.
 * Produce an example marshalling a block from C to Haskell
 * Evaluate which parts of the code actually need to be generated and which parts could go into an extension of the FFI library (`Foreign.C.Blocks`) or into the RTS if it is C code.

```
