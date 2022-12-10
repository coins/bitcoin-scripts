# OP_MUL
The following is a bitcoin script implementing the opcode `OP_MUL` to multiply `a` by `b`. 

DISCLAIMER: THE CODE IS INSECURE! DO NOT USE IN PRODUCTION!!

## OP_2MUL Multiplication by the Constant 2
Multiply by 2
```
OP_2MUL = OP_DUP OP_ADD
```

## OP_MUL(2^k) Multiplication by a Constant Power of 2
Multiply by powers of 2 reduces to the following equation:
```
OP_MUL(2^k) = OP_MUL(2^(k-1)) OP_2MUL 
```
So, to multiply a number by `2^k` we need `k * 2` instructions.

Examples:
```
OP_4MUL = OP_DUP OP_ADD OP_DUP OP_ADD
OP_8MUL = OP_DUP OP_ADD OP_DUP OP_ADD OP_DUP OP_ADD
OP_16MUL = OP_DUP OP_ADD OP_DUP OP_ADD OP_DUP OP_ADD OP_DUP OP_ADD
...
```

## OP_3MUL Multiplication by the Constant 3

Multiply the top stack item by three
```
OP_DUP OP_DUP OP_ADD OP_ADD
```


## OP_MUL Multiplication with a 4-bit Variable
Multiply `a` by `b`. The result of `a * b` must fit into a signed 32-bit integer.
Additionally, the following script works only for `b<16` being a 4-bit integer.

```
btcdeb "[

  0 
  OP_TOALTSTACK

  OP_DUP
  8
  OP_GREATERTHANOREQUAL
  OP_IF
    8 
    OP_SUB
    OP_SWAP
    OP_DUP 
    OP_DUP OP_ADD OP_DUP OP_ADD OP_DUP OP_ADD
    OP_FROMALTSTACK
    OP_ADD
    OP_TOALTSTACK
    OP_SWAP
  OP_ENDIF

  OP_DUP
  4
  OP_GREATERTHANOREQUAL
  OP_IF
    4 
    OP_SUB
    OP_SWAP
    OP_DUP 
    OP_DUP OP_ADD OP_DUP OP_ADD
    OP_FROMALTSTACK
    OP_ADD
    OP_TOALTSTACK
    OP_SWAP
  OP_ENDIF

  OP_DUP
  2
  OP_GREATERTHANOREQUAL
  OP_IF
    2 
    OP_SUB
    OP_SWAP
    OP_DUP 
    OP_DUP OP_ADD
    OP_FROMALTSTACK
    OP_ADD
    OP_TOALTSTACK
    OP_SWAP
  OP_ENDIF

  OP_NOT
  OP_IF
    OP_DROP
    0
  OP_ENDIF

  OP_FROMALTSTACK
  OP_ADD

"] 60 14
```

#### Circuit Sizes 
The number of required opcodes is linear in bit size of `b`:
- `4 bits -> 52 opcodes`
- `5 bits -> 71 opcodes`
- `6 bits -> 92 opcodes`
- `7 bits -> 115 opcodes`
- `8 bits -> 140 opcodes`


## OP_127MUL Multiplication by a Constant close to a Powers of 2

In this example we multiply the top stack item by `127` which is close to `128 = 2**7`. We use that `x * 127 = x * 128 - x`:

```
btcdeb "[ 
	
	# Input: Some random number on the stack
	42	

	# Duplicate it
	DUP	

	# Multiply it by 128
	DUP ADD 
	DUP ADD 
	DUP ADD 
	DUP ADD 
	DUP ADD
	DUP ADD
	DUP ADD

	# Subtract the original input to get a multiplication by 127
	SWAP
	SUB
# ]" 

```

The above can be easily generalized to play all kinds of code golf to find shortest expressions for multiplications by a constant expressed as sums and differences of powers of two. Even mixing in powers of three might sometimes be the most efficient.

## OP_1143MUL Multiplication by a Constant by Multiplying its Factors

The following is a multiplication by `1143`, which uses its factorization `1143 = 127 * 9` and to compute 
```
  `x * 1143 
= (x * 127) * 9 = (x * (128-1)) * (8 + 1)`
= (x * 128 - x) * 8 + (x * 128 - x)
```

Here's the implementation
```
btcdeb "[ 
	
	# Input: Some random number on the stack
	42	

	#
	# Multiply by 127
	#

	# Duplicate it
	DUP	

	# Multiply it by 128
	DUP ADD 
	DUP ADD 
	DUP ADD 
	DUP ADD 
	DUP ADD
	DUP ADD
	DUP ADD

	# Subtract the original input to get a multiplication by 127
	SWAP
	SUB



	#
	# Multiply by 9 
	#

	# Duplicate it
	DUP	

	# Multiply it by 8
	DUP ADD 
	DUP ADD 
	DUP ADD

	# Add the original input to get a multiplication by 9
	ADD


	#
	# The result is a multiplication by 127 * 9
	# 

# ]" 

```
