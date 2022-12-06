# Rotate a 32-bit Integer 3 bits to the right

WARNING: This script doesn't work yet because btcdeb keeps replacing the negative zero `00000080` with the positive zero `0`.
Compiling the script "by hand" to include `00000080` would probably solve the issue.

```
btcdeb "[
	
# Check if this is the input that returns a negative zero
# To work around Bitcoin's quirky arithmetic
# we simply return a constant byte string in this case
OP_DUP
0x04
OP_EQUAL
OP_IF

	# This is the input that returns negative zero	
	# So, we return the byte string that represents negative zero
	OP_DROP
	OP_DROP
	00000080

OP_ELSE
	# This is not the input that returns negative zero

	# Check if the input is the negative zero 00000080
	OP_DUP
	00000080
	OP_EQUAL
	OP_IF
		
		# The input was a negative zero
		# We also handle this case separately with a constant
		# which is the negative zero rotated by 3 bits
		OP_DROP
		00000010

	OP_ELSE
		# This is a normal input, so we apply our algorithm

		
		##################################################################



		# Split the input into its sign and the 31-bit value
		OP_DUP
		OP_ABS
		OP_TUCK
		OP_NUMNOTEQUAL

		OP_IF
			# 2^( 31 - 3 )
			268435456
		OP_ELSE
			0
		OP_ENDIF
		OP_SWAP
		OP_ROT

		# div8_rem

		OP_DUP
		OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD

		OP_ROT
		OP_SWAP
		OP_SUB
		OP_DUP
		0
		8
		OP_WITHIN
		OP_VERIFY


		OP_DUP
		# 2**(3-1) 
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


		# Shift x mod 8 to the left (31-3) bits
		OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD OP_DUP OP_ADD
		OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD OP_DUP OP_ADD

		OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD OP_DUP OP_ADD
		OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD OP_DUP OP_ADD

		OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD OP_DUP OP_ADD
		OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD OP_DUP OP_ADD

		OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD OP_DUP OP_ADD
		OP_DUP OP_ADD

		OP_ADD 
		OP_ADD

		OP_FROMALTSTACK

		OP_IF
			OP_NEGATE
		OP_ENDIF


		##################################################################

	OP_ENDIF
OP_ENDIF

# ]" 0x11111101 0x04


# Inputs have to be minimally encoded: 0x040000 -> 0x04
```
