# Lookup Table

Using `OP_PICK` to implement lockup tables. This is particulariy useful if we want to randomly access multiple elements in sequence because we can reuse the lockup table without using any more opcodes.

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
	OP_PICK

	# Now the element at `index` is on the stack


	TOALTSTACK

	# At the end of the program we have to delete our lookup table
	OP_2DROP OP_2DROP
	OP_2DROP OP_2DROP
	OP_2DROP OP_2DROP

	FROMALTSTACK
# ]"
```
