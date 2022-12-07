# Bitwise Rotation of a 32-bit Integer

A Bitcoin Script implementing a bitwise rotation of a 32-bit word by 3 bits to the right. 

```
btcdeb "[
	
# 
# We have to start with a bit of gymnastics here
# to deal with the negative zero `0x80` and that it 
# cannot be the result of arithmetic opcodes.
# To work around Bitcoin's quirky arithmetic
# we simply return a constant in these cases
#

# Check if the input is the number that maps to negative zero when shifted 3 bits to the right
OP_DUP
0x04
OP_EQUAL
OP_IF
	# This is the number that maps to negative zero	
	# So we simply return the byte string of negative zero here
	OP_DROP
	OP_DROP
	OP_x01 [0x80]			# We return here the negative zero

OP_ELSE
	# This is not the number that maps to negative zero

	# Check if the number itself is the negative zero
	OP_DUP
	OP_x01 [0x80]			# This is the negative zero
	OP_EQUAL
	OP_IF
		
		# This number is the negative zero. This case is handled by a constant, too.
		# We return the negative zero rotated by 3 bits
		OP_DROP
		OP_DROP
		00000010		# We return here the rotated negative zero 

	OP_ELSE

		# Finally we know this is not an edge case but a regular number,
		# so we apply our algorithm:
		
		##################################################################


		# Split the number into its sign and the 31-bit value
		OP_DUP
		OP_ABS
		OP_TUCK
		OP_NUMNOTEQUAL

		# The sign rotated becomes the bit at index 31-3 = 28
		OP_IF
			268435456  # This is 2^28
		OP_ELSE
			0
		OP_ENDIF
		OP_SWAP
		OP_ROT

		
		#
		# Simultaneous integer division and modulus
		#
		# Computes div_rem of the value divided by 8 == 2^3
		# It puts both the quotient and the remainder on the stack
		#

		# We compute the result with the help of a "hint" 
		# provided by the unlocking script.
		# We expect that hint to be the quotient `value/8`
		# and the following verifies that this is acutally correct:

		# Copy the hint and multiply it by 8
		OP_DUP
		OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD

		# Subtract that from the original value and ensure
		# the remainder is a number in [0,1,2,...,7]
		OP_ROT
		OP_SWAP
		OP_SUB
		OP_DUP
		0
		8
		OP_WITHIN
		OP_VERIFY

		# Now remainder and quotient is on the stack


		#
		# Most of the following code is juggeling around bits
		# to deal with the sign of our result
		#

		
		# Cut off the highest bit of the remainder because this will be our new sign 
		# The remainder has 3 bits. So the highest bit is set from 4 = 2**(3-1) upwards

		# Check if the highest bit of the remainder is set
		OP_DUP
		4
		OP_GREATERTHANOREQUAL
		OP_IF
			# The bit is set. So cut it off
			4
			OP_SUB
			1 	# The new sign
		OP_ELSE
			0 	# The new sign
		OP_ENDIF
		# Push the new sign in the altstack for later
		OP_TOALTSTACK


		# Shift the remainder 32-3 = 29 bits to the left
		OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD OP_DUP OP_ADD
		OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD OP_DUP OP_ADD

		OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD OP_DUP OP_ADD
		OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD OP_DUP OP_ADD

		OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD OP_DUP OP_ADD
		OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD OP_DUP OP_ADD

		OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD OP_DUP OP_ADD
		OP_DUP OP_ADD

		
		# Now add up all three values
		OP_ADD 
		OP_ADD


		# Adjust the new sign
		OP_FROMALTSTACK
		OP_IF
			OP_NEGATE
		OP_ENDIF

		# And we're done. The result is on the stack

	OP_ENDIF
OP_ENDIF

# ]" 0x11111101 0x88888888


```

Inputs: 
- `<X>`, the value to shift. In our example `0x88888888`. The script produces the result `0x11111111`
- `<Hint: div(abs(X),8)>`, the absolute value of `X` divided by 8. In our example `0x11111101`. This hint allows us to apply the principle "Don't compute. Verify."

Inputs have to be minimally encoded. E.g.,`0x04000000 -> 0x04`. But the negative zero, `0x80`, is encoded as `0x00`

The script here rotates three bits to the right. It can easily be modified to perform any other bitwise rotation. All rotations on 32-bit words require the same amount of instructions. In this script 118 instructions.


## Optimization 

The following implementation takes advantage of the special case of a rotation by 3 bits, which allows to optimize by hard coding 4 cases with a constant to save an expensive multiplication. This reduces the step count to 78 instructions.

```
btcdeb "[
	
# 
# We have to start with a bit of gymnastics here
# to deal with the negative zero `0x80` and that it 
# cannot be the result of arithmetic opcodes.
# To work around Bitcoin's quirky arithmetic
# we simply return a constant in these cases
#

# Check if the input is the number that maps to negative zero when shifted 3 bits to the right
OP_DUP
0x04
OP_EQUAL
OP_IF
	# This is the number that maps to negative zero	
	# So we simply return the byte string of negative zero here
	OP_DROP
	OP_DROP
	OP_x01 [0x80]			# We return here the negative zero

OP_ELSE
	# This is not the number that maps to negative zero

	# Check if the number itself is the negative zero
	OP_DUP
	OP_x01 [0x80]			# This pushes the negative zero onto the stack
	OP_EQUAL
	OP_IF
		
		# This number is the negative zero. This case is handled by a constant, too.
		# We return the negative zero rotated by 3 bits
		OP_DROP
		OP_DROP
		00000010		# We return here the rotated negative zero 

	OP_ELSE

		# Finally we know this is not an edge case but a regular number,
		# so we apply our algorithm:
		
		##################################################################


		# Split the number into its sign and the 31-bit value
		OP_DUP
		OP_ABS
		OP_TUCK
		OP_NUMNOTEQUAL

		# The sign rotated becomes the bit at index 31-3 = 28
		OP_IF
			268435456  # This is 2^28
		OP_ELSE
			0
		OP_ENDIF
		OP_SWAP
		OP_ROT

		
		#
		# Simultaneous integer division and modulus
		#
		# Computes div_rem of the value divided by 8 == 2^3
		# It puts both the quotient and the remainder on the stack
		#

		# We compute the result with the help of a "hint" 
		# provided by the unlocking script.
		# We expect that hint to be the quotient `value/8`
		# and the following verifies that this is acutally correct:

		# Copy the hint and multiply it by 8
		OP_DUP
		OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD

		# Subtract that from the original value and ensure
		# the remainder is a number in [0,1,2,...,7]
		OP_ROT
		OP_SWAP
		OP_SUB
		OP_DUP
		0
		8
		OP_WITHIN
		OP_VERIFY

		# Now remainder and quotient is on the stack


		#
		# Most of the following code is juggeling around bits
		# to deal with the sign of our result
		#

		
		# Cut off the highest bit of the remainder because this will be our new sign 
		# The remainder has 3 bits. So the highest bit is set from 4 = 2**(3-1) upwards

		# Check if the highest bit of the remainder is set
		OP_DUP
		4
		OP_GREATERTHANOREQUAL
		OP_IF
			# The bit is set. So cut it off
			4
			OP_SUB
			1 	# The new sign
		OP_ELSE
			0 	# The new sign
		OP_ENDIF
		# Push the new sign in the altstack for later
		OP_TOALTSTACK


		# Shift the unsigned remainder 32-3 = 29 bits to the left

		#
		# The unsigned remainder has only 4 bits here, so we can handle all 4 cases with a constant
		# instead of multiplying the remainder by 2^29
		#

		OP_DUP
		OP_IF
			# The unsigned remainder was not zero, so we have to add more than a zero
			OP_DUP
			1
			OP_EQUAL
			OP_IF
				OP_DROP
				00000020 		# this is 2^29
			OP_ELSE
				2
				OP_EQUAL
				OP_IF
					00000040 	# This is 2 * 2^29
				OP_ELSE
					00000060 	# This is 3 * 2^29
				OP_ENDIF
			OP_ENDIF
		OP_ENDIF

		
		# Now add up all three values
		OP_ADD 
		OP_ADD


		# Adjust the new sign
		OP_FROMALTSTACK
		OP_IF
			OP_NEGATE
		OP_ENDIF

		# And we're done. The result is on the stack

	OP_ENDIF
OP_ENDIF

# ]" 0x11111101 0x88888888

```
