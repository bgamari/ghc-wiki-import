CONVERSION ERROR

Original source:

```trac
= Design of `IntegerGmp2` (#9281) =

[[PageOutline]]

This wiki page is meant as a scratch-pad to describe the plans/ideas behind the `integer-gmp2` rewrite

== Motivations ==

 - allow to avoid the custom GMP allocator hack, and thus
 - avoid issues when linking against other C libraries using GMP,
 - simplify code, as we would perform all heap allocations in Haskell code (and never inside Cmm/C code as its done now),
 - maybe even remove a few more superfluous temporary heap allocations.
 - link-time selectable integer-library backend (think `ghc --make -O -finteger-library=bsdnt` Hello.hs)
    - simplifies handling on platforms where GMP is not a system-library (mostly OSX and Windows)
    - user code can still link with GMP, even though the primitive operation use `bsdnt`
 - Allow to implement (big) Natural number type w/o redundant sign handling
 - somewhat more robust out-of-memory handling (since allocations occur in Haskell-land)

== Roadmap ==

{{{#!box note
This is not yet a definite accepted plan but rather wishful thinking at this point
}}}

=== For GHC 7.10.1

 - Switch to `integer-gmp2` as default `INTEGER_LIBRARY` [[BR]]
     - but leave `INTEGER_LIBRARY=integer-gmp` in place as build-option
 - We can leave the package name as `integer-gmp`, i.e. for
     - `INTEGER_LIBRARY=integer-gmp` we end up with `integer-gmp-0.5.1.0`, while for 
     - `INTEGER_LIBRARY=integer-gmp2` we end up with `integer-gmp-1.0.0.0`.
 - //(maybe)// update in-tree GMP version [[BR]]
   (by adding a `.patch` file for the existing GMP 5.0.3 tarball, or by retrieving a new GMP tarball from outside `ghc.git`, c.f. mingw `configure`-time autodownloading)
 - //(maybe)// implement `configure`-time selectable `bsdnt` backend [[BR]]
   (could be easily backported as out-of-tree patch from GHC 7.11 branch if there's demand)

=== For GHC 7.12

 - all "//(maybe)//"-items from above if they didn't make it into 7.10
 - Implement link-time backend selection as described below

== Rough Design ==

=== Haskell-side API Types ===

{{{#!hs
-- | Type representing /raw/ arbitrary-precision Naturals
--
-- This is common type used by 'Natural' and 'Integer'.  As this type
-- consists of a single constructor wrapping a 'ByteArray#' it can be
-- unpacked.
--
-- Essential invariants:
--
--  - 'ByteArray#' size is an exact multiple of 'Word#' size
--  - limbs are stored in least-significant-limb-first order,
--  - the most-significant limb must be non-zero, except for
--  - @0@ which is represented as a 1-limb.
data BigNat = BN# ByteArray#

-- | Invariant: 'Jn#' and 'Jp#' are used iff value doesn't fit in 'SI#'
--
-- Useful properties resulting from the invariants:
--
--  - @abs ('S#' _) <= abs ('Jp#' _)@
--  - @abs ('S#' _) <  abs ('Jn#' _)@
--
data Integer  = S#                  Int#
                -- ^ iff value in @[minBound::'Int', maxBound::'Int']@ range
              | Jp# {-# UNPACK #-} !BigNat
                -- ^ iff value in @]maxBound::'Int', +inf[@ range
              | Jn# {-# UNPACK #-} !BigNat
                -- ^ iff value in @]-inf, minBound::'Int'[@ range

-- | Type representing arbitrary-precision Naturals
--
-- Invariant: 'NatJ#' is used iff when value doesn't fit in 'NatS#'
data Natural = NatS#                 Word#    -- ^ @[0, maxBound::Word]@
             | NatJ# {-# UNPACK #-} !BigNat   -- ^ @]maxBound::GmpLimb, +inf[@
             deriving (Eq,Ord)
}}}

 - `BigNat` is a internal common type to `Integer` and `Natural` and not exposed through `base`
 - `Natural` finally fills the gap of the missing unsigned `Integer` counter-part in `base`
        - requires fallback implementation (on top of `Integer`) in `base` if `integer-{simple,gmp}` are still to be supported

 - Most operations can avoid calling into the FFI backend integer-library, if the values don't exceed the `SI#`/`NatS#` capacity.  This also means that the performance difference between using e.g. `bsdnt` or `gmp` is only noticeable for large operands.


=== Link-time integer-library selection ===

 - The basic requirements to be able to write an adapter for a FFI integer-library for `integer-gmp2`:
     - no memory allocation of result BigNums (but rather writes into caller-allocated buffers)
     - BigNums are represented as machine-word-sized arrays + length information

 - `integer-gmp2` needs only about a dozen of rather generic low-level primitive arithmetic BigNum operations, such as e.g.
{{{#!hs
-- mp_limb_t mpn_add_1 (mp_limb_t *rp, const mp_limb_t *s1p, mp_size_t n,
--                      mp_limb_t s2limb)
foreign import ccall unsafe "gmp.h __gmpn_add_1"
  c_mpn_add_1 :: MutableByteArray# s -> ByteArray# -> GmpSize# -> GmpLimb# -> IO GmpLimb

-- mp_limb_t mpn_add (mp_limb_t *rp, const mp_limb_t *s1p, mp_size_t s1n,
--                    const mp_limb_t *s2p, mp_size_t s2n)
foreign import ccall unsafe "gmp.h __gmpn_add"
  c_mpn_add :: MutableByteArray# s -> ByteArray# -> GmpSize# -> ByteArray# -> GmpSize# -> IO GmpLimb
}}}

 - The plan is to write a small shim-like C library that implements a uniform adapter for `integer-gmp2` to call into, that wraps libraries such as GMP or `bsdnt`. This small adapter is then selected at link-time by `ghc --make` similiar to how `ghc` selects one of its various runtimes (threaded, debugging, non-threaded, ...)


```