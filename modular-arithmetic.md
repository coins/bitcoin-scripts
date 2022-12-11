# Modular Arithmetic 

## OP_2MUL_MOD64

The following script demonstrates doubling modulo a power of two. In this example the power of 2 is 64 so we compute `x * 2 mod 64`:

```
btcdeb "[ 
	
	# Some random number is on the stack
	42	

	# If x > 64/2 then subtract 64/2. This -64/2 will cancel out when doubling.
	DUP 32 GREATERTHANOREQUAL
	IF 32 SUB ENDIF

	# Perform the doubling
	DUP ADD
  
  
#]"
```

This assumes the input to be less than 64. The overhead is 7 instructions. This works up to `mod 2**31` for any positive 31-bit number, without overflow. 


## Right Shift by Multiplying the Inverse of 2
The following performs a right shift of a 8-bit word. It uses the multiplicative group mod `n = 2**8 - 1` where `1/2 == 2**7`

```sh
btcdeb "[ 
	
	# Input X is on the stack, some random uint8
	142

	# Compute 2**7 * X == 1/2 * X mod 255 
	DUP ADD
	DUP 255 GREATERTHANOREQUAL
	IF 255 SUB ENDIF

	DUP ADD
	DUP 255 GREATERTHANOREQUAL
	IF 255 SUB ENDIF

	DUP ADD
	DUP 255 GREATERTHANOREQUAL
	IF 255 SUB ENDIF

	DUP ADD
	DUP 255 GREATERTHANOREQUAL
	IF 255 SUB ENDIF

	DUP ADD
	DUP 255 GREATERTHANOREQUAL
	IF 255 SUB ENDIF

	DUP ADD
	DUP 255 GREATERTHANOREQUAL
	IF 255 SUB ENDIF

	DUP ADD
	DUP 255 GREATERTHANOREQUAL
	IF 255 SUB ENDIF
	
	
	# If X was odd we have to subtract 1/2 mod N
	DUP 128 GREATERTHANOREQUAL 
	IF 128 SUB ENDIF

]"

```
