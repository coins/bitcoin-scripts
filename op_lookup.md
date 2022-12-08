# Lookup Table

Using `OP_PICK` to implement lockup tables. This is particulariy useful if we want to randomly access multiple elements in sequence, since we can reuse the lockup table without using any more opcodes. The following example is a lookup table for powers of two.

```sh

btcdeb "[ 

	7				# An arbitrary index is on the stack
	TOALTSTACK

	
	# The elements of our lookup table are put onto the stack
	524288
	262144
	131072
	65536

	32768
	16384
	8192
	4096

	2048
	1024
	512
	256

	128
	64
	32
	16
	
	8
	4
	2
	1

	FROMALTSTACK
	OP_PICK

	# Now the element at index=7 is on the stack. It is 2^7 = 128


	TOALTSTACK

	# At the end of the program we have to delete our lookup table
	OP_2DROP OP_2DROP 
	OP_2DROP OP_2DROP
	OP_2DROP OP_2DROP 
	OP_2DROP OP_2DROP
	OP_2DROP OP_2DROP

	FROMALTSTACK
# ]"
```
