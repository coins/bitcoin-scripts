# Lookup Table

Using `OP_PICK` to implement lockup tables:

```sh

btcdeb "[ 

	7			# An arbitrary index is on the stack
	TOALTSTACK

	# Our lookup table is put onto the stack
	2
	4
	8
	16
	32
	64
	128
	256
	512
	1024
	2048
	4096

	FROMALTSTACK
	42
	OP_ADD
	OP_PICK

	# Now the element at index is on the stack


	TOALTSTACK

	# At the end of the program we have to delete our lookup table
	OP_DROP OP_DROP OP_DROP OP_DROP
	OP_DROP OP_DROP OP_DROP OP_DROP
	OP_DROP OP_DROP OP_DROP OP_DROP

	FROMALTSTACK
# ]"
```
