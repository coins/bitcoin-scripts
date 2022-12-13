# Bitwise Rotation of a 32-bit Integer

A Bitcoin Script implementing a bitwise rotation of a 32-bit word by 3 bits to the right. If you are unfamiliar with [nondeterminism, check out this simple example first](https://github.com/coins/bitcoin-scripts/blob/master/composite-opcodes.md#op_2div).

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
		00000010			# We return here the rotated negative zero 

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

		# Subtract the result from the original value and ensure
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
- `<Hint: div(abs(X),8)>`, the absolute value of `X` divided by 8. In our example `0x11111101`. This hint allows us to apply the principle "Don't compute. Verify." You can find an explanation of _"hints"_ and _"nondeterministic programming"_ in the section "nondeterminism" in the [the Cairo language white paper](https://eprint.iacr.org/2021/1063.pdf).

Inputs have to be minimally encoded. E.g.,`0x04000000 -> 0x04`. But the negative zero, `0x80`, is encoded as `0x00`

The script here rotates three bits to the right. It can easily be modified to perform any other bitwise rotation. All rotations on 32-bit words require the same amount of instructions. In this script 118 instructions.




## Optimization 

The following implementation takes advantage of the special case of a rotation by 3 bits, which allows to optimize an expensive multiplication by hard coding 4 cases with constants. This reduces the step count to 78 instructions.

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
		00000010			# We return here the rotated negative zero 

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

		# Subtract the result from the original value and ensure
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
		OP_IF 					# Otherwise, we return 0 * 2^29
			# The unsigned remainder was not zero, so we have to add more than a zero
			OP_DUP
			1
			OP_EQUAL
			OP_IF
				OP_DROP
				00000020 		# This is 1 * 2^29
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


We can make this even more efficient and generalize it by [using lookup tables](op_lookup.md)


## Generic Implementation

Here's a [generic implementation using templates for a rotations by any number of bits](
https://coins.github.io/bitcoin-scripts/script-editor/#JHtrPTd9CgoKIyBDaGVjayBpZiB0aGUgaW5wdXQgaXMgdGhlIG51bWJlciB0aGF0IG1hcHMgdG8gbmVnYXRpdmUgemVybyB3aGVuIHNoaWZ0ZWQgJHtrfSBiaXRzIHRvIHRoZSByaWdodApPUF9EVVAKJHsyKiooay0xKX0KT1BfRVFVQUwKT1BfSUYKCSMgVGhpcyBpcyB0aGUgbnVtYmVyIHRoYXQgbWFwcyB0byBuZWdhdGl2ZSB6ZXJvCQoJIyBTbyB3ZSBzaW1wbHkgcmV0dXJuIHRoZSBieXRlIHN0cmluZyBvZiBuZWdhdGl2ZSB6ZXJvIGhlcmUKCU9QX0RST1AKCU9QX0RST1AKCTB4MDA4MAkJCSMgV2UgcmV0dXJuIGhlcmUgdGhlIG5lZ2F0aXZlIHplcm8KCk9QX0VMU0UKCSMgVGhpcyBpcyBub3QgdGhlIG51bWJlciB0aGF0IG1hcHMgdG8gbmVnYXRpdmUgemVybwoKCSMgQ2hlY2sgaWYgdGhlIG51bWJlciBpdHNlbGYgaXMgdGhlIG5lZ2F0aXZlIHplcm8KCU9QX0RVUAoJMHgwMDgwCQkJIyBUaGlzIHB1c2hlcyB0aGUgbmVnYXRpdmUgemVybyBvbnRvIHRoZSBzdGFjawoJT1BfRVFVQUwKCU9QX0lGCgkJCgkJIyBUaGlzIG51bWJlciBpcyB0aGUgbmVnYXRpdmUgemVyby4gVGhpcyBjYXNlIGlzIGhhbmRsZWQgYnkgYSBjb25zdGFudCwgdG9vLgoJCSMgV2UgcmV0dXJuIHRoZSBuZWdhdGl2ZSB6ZXJvIHJvdGF0ZWQgYnkgMyBiaXRzCgkJT1BfRFJPUAoJCU9QX0RST1AKCQkkezIqKigzMS1rKX0JIyBXZSByZXR1cm4gaGVyZSB0aGUgcm90YXRlZCBuZWdhdGl2ZSB6ZXJvIAoKCU9QX0VMU0UKCgkJIyBGaW5hbGx5IHdlIGtub3cgdGhpcyBpcyBub3QgYW4gZWRnZSBjYXNlIGJ1dCBhIHJlZ3VsYXIgbnVtYmVyLAoJCSMgc28gd2UgYXBwbHkgb3VyIGFsZ29yaXRobToKCQkKCQkjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMjIyMKCgoJCSMgU3BsaXQgdGhlIG51bWJlciBpbnRvIGl0cyBzaWduIGFuZCB0aGUgMzEtYml0IHZhbHVlCgkJT1BfRFVQCgkJT1BfQUJTCgkJT1BfVFVDSwoJCU9QX05VTU5PVEVRVUFMCgoJCSMgVGhlIHNpZ24gcm90YXRlZCBiZWNvbWVzIHRoZSBiaXQgYXQgaW5kZXggMzEtJHtrfSA9ICR7MzEta30KCQlPUF9JRgoJCQkkezIqKigzMS1rKX0gCgkJT1BfRUxTRQoJCQkwCgkJT1BfRU5ESUYKCQlPUF9TV0FQCgkJT1BfUk9UCgoJCQoJCSMKCQkjIFNpbXVsdGFuZW91cyBpbnRlZ2VyIGRpdmlzaW9uIGFuZCBtb2R1bHVzCgkJIwoJCSMgQ29tcHV0ZXMgZGl2X3JlbSBvZiB0aGUgdmFsdWUgZGl2aWRlZCBieSAkezIqKmt9ID09IDJeJHtrfQoJCSMgSXQgcHV0cyBib3RoIHRoZSBxdW90aWVudCBhbmQgdGhlIHJlbWFpbmRlciBvbiB0aGUgc3RhY2sKCQkjCgoJCSMgV2UgY29tcHV0ZSB0aGUgcmVzdWx0IHdpdGggdGhlIGhlbHAgb2YgYSAiaGludCIgCgkJIyBwcm92aWRlZCBieSB0aGUgdW5sb2NraW5nIHNjcmlwdC4KCQkjIFdlIGV4cGVjdCB0aGF0IGhpbnQgdG8gYmUgdGhlIHF1b3RpZW50IHZhbHVlLyR7Mioqa30KCQkjIGFuZCB0aGUgZm9sbG93aW5nIHZlcmlmaWVzIHRoYXQgdGhpcyBpcyBhY3V0YWxseSBjb3JyZWN0OgoKCQkjIENvcHkgdGhlIGhpbnQgYW5kIG11bHRpcGx5IGl0IGJ5IDIqKiR7a30KCQlPUF9EVVAKCQkke2xvb3AoaywgaSA9PiBgCiAgICAgICAgICAgICAgICAgICAgICAgIE9QX0RVUCBPUF9BRERgKX0KCgkJIyBTdWJ0cmFjdCB0aGUgcmVzdWx0IGZyb20gdGhlIG9yaWdpbmFsIHZhbHVlIGFuZCBlbnN1cmUKCQkjIHRoZSByZW1haW5kZXIgaXMgYSBudW1iZXIgaW4gWzAsMSwyLC4uLiwyKioke2t9LTFdCgkJT1BfUk9UCgkJT1BfU1dBUAoJCU9QX1NVQgoJCU9QX0RVUAoJCTAKCQkkezIqKmt9CgkJT1BfV0lUSElOCgkJT1BfVkVSSUZZCgoJCSMgTm93IHJlbWFpbmRlciBhbmQgcXVvdGllbnQgaXMgb24gdGhlIHN0YWNrCgoKCQkjCgkJIyBNb3N0IG9mIHRoZSBmb2xsb3dpbmcgY29kZSBpcyBqdWdnZWxpbmcgYXJvdW5kIGJpdHMKCQkjIHRvIGRlYWwgd2l0aCB0aGUgc2lnbiBvZiBvdXIgcmVzdWx0CgkJIwoKCQkKCQkjIEN1dCBvZmYgdGhlIGhpZ2hlc3QgYml0IG9mIHRoZSByZW1haW5kZXIgYmVjYXVzZSB0aGlzIHdpbGwgYmUgb3VyIG5ldyBzaWduIAoJCSMgVGhlIHJlbWFpbmRlciBoYXMgMyBiaXRzLiBTbyB0aGUgaGlnaGVzdCBiaXQgCiAgICAgICAgICAgICAgICAjIGlzIHNldCBmcm9tICR7MioqKGstMSl9ID0gMioqKCR7a30tMSkgdXB3YXJkcwoKCQkjIENoZWNrIGlmIHRoZSBoaWdoZXN0IGJpdCBvZiB0aGUgcmVtYWluZGVyIGlzIHNldAoJCU9QX0RVUAoJCSR7MioqKGstMSl9CgkJT1BfR1JFQVRFUlRIQU5PUkVRVUFMCgkJT1BfSUYKCQkJIyBUaGUgYml0IGlzIHNldC4gU28gY3V0IGl0IG9mZgoJCQkkezIqKihrLTEpfQoJCQlPUF9TVUIKCQkJMSAJIyBUaGUgbmV3IHNpZ24KCQlPUF9FTFNFCgkJCTAgCSMgVGhlIG5ldyBzaWduCgkJT1BfRU5ESUYKCQkjIFB1c2ggdGhlIG5ldyBzaWduIGluIHRoZSBhbHRzdGFjayBmb3IgbGF0ZXIKCQlPUF9UT0FMVFNUQUNLCgoKCQkjIFNoaWZ0IHRoZSB1bnNpZ25lZCByZW1haW5kZXIgMzItJHtrfSA9ICR7MzIta30gYml0cyB0byB0aGUgbGVmdAoKCQkke2xvb3AoMzItaywgaSA9PiBgCiAgICAgICAgICAgICAgICAgICAgICAgIE9QX0RVUCBPUF9BRERgKX0KCQkKCQkjIE5vdyBhZGQgdXAgYWxsIHRocmVlIHZhbHVlcwoJCU9QX0FERCAKCQlPUF9BREQKCgoJCSMgQWRqdXN0IHRoZSBuZXcgc2lnbgoJCU9QX0ZST01BTFRTVEFDSwoJCU9QX0lGCgkJCU9QX05FR0FURQoJCU9QX0VORElGCgoJCSMgQW5kIHdlJ3JlIGRvbmUuIFRoZSByZXN1bHQgaXMgb24gdGhlIHN0YWNrCgoJT1BfRU5ESUYKT1BfRU5ESUY=)
