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

This assumes the input to be less than 64. The overhead is 7 instructions. This works up to `mod 2**31` for any 32-bit number, without overflow. 
