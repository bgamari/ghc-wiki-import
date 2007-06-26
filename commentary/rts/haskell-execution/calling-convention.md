# Calling Convention



Entry conventions are very conventional: the first N argumements in registers and the rest on the stack.


# Return Convention



All returns are now *direct*; that is, a return is made by jumping to the code associated with the [info table](commentary/rts/storage/heap-objects#info-tables) of the topmost [stack frame](commentary/rts/storage/stack).


