# Rotate a 32-bit Integer 3 bits to the right

WARNING: This script doesn't work yet because btcdeb keeps replacing the negative zero `00000080` with the positive zero `0`.
Compiling the script "by hand" to include `00000080` would probably solve the issue. The negative zero is only required to handle the two edge cases where it occurs. The actual algorithm seems to work.

```
btcdeb "[

# 
# We have to start with a bit of gymnastics here
# to deal with the negative zero `0x80` and that it 
# cannot be the result of arithmetic opcodes.
# To work around Bitcoin's quirky arithmetic
# we simply return a constant in these cases
#

# Check if this is the number that maps to negative zero when shifted 3 bits to the right
OP_DUP
0x04
OP_EQUAL
OP_IF
	# This is the number that maps to negative zero	
	# So we simply return the byte string of negative zero here
	OP_DROP
	OP_DROP
	0x80			# This is our "return statement"

OP_ELSE
	# This is not the number that maps to negative zero

	# Check if the number itself is the negative zero
	OP_DUP
	0x08
	OP_EQUAL
	OP_IF
		
		# This number is the negative zero. This case is handled by a constant, too.
		# We return the negative zero rotated by 3 bits
		OP_DROP
		0x10		# This is our "return statement"

	OP_ELSE

		# This is not an edge case but a regular number, so we apply our algorithm:

		
		##################################################################


		# Split the number into its sign and the 31-bit value
		OP_DUP
		OP_ABS
		OP_TUCK
		OP_NUMNOTEQUAL

		# The sign rotated becomes the bit at index 31-3 = 28
		OP_IF
			268435456 			# This is 2^28
		OP_ELSE
			0
		OP_ENDIF
		OP_SWAP
		OP_ROT


		#
		# Compute div_rem of the value divided by 8
		# it puts the quotient and the remainder on the stack.
		#

		# We compute the result with the help of a hint.
		# We expect the hint to be hint == value // 8
		# and the following verifies its correctness

		#  Copy the hint and multiply it by 8
		OP_DUP
		OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD

		# Subtract it from the value and ensure
		# the remainder is a number in [0,1,2,...,7]
		OP_ROT
		OP_SWAP
		OP_SUB
		OP_DUP
		0
		8
		OP_WITHIN
		OP_VERIFY


		OP_DUP
		# Cut off the highest bit of the remainder because this we be our new sign 2**(3-1) 
		4
		OP_GREATERTHANOREQUAL
		OP_IF
			4
			OP_SUB
			1
		OP_ELSE
			0
		OP_ENDIF
		OP_TOALTSTACK


		# Shift the remainder 32-3 = 29 bits to the left
		OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD
		OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD

		OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD
		OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD

		OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD
		OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD

		OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD
		OP_DUP OP_ADD


		# Now add all three value up
		OP_ADD 
		OP_ADD

		# Adjust the sign
		OP_FROMALTSTACK
		OP_IF
			OP_NEGATE
		OP_ENDIF

		# We're done. The result is on the stack

	OP_ENDIF
OP_ENDIF

# ]" 0x11111101 0x04

# Inputs: <Hint: div(abs(X),8)> <X>
# Inputs have to be minimally encoded. E.g., 0x04000000 -> 0x04
```
