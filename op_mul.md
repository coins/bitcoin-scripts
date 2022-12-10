# OP_MUL
The following is a bitcoin script implementing the opcode `OP_MUL` to multiply `a` by `b`. 

DISCLAIMER: THE CODE IS INSECURE! DO NOT USE IN PRODUCTION!!

## OP_2MUL
Multiply by 2
```
OP_2MUL = OP_DUP OP_ADD
```

## Powers of 2: OP_MUL(2^k)
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

## OP_3MUL

Multiply the top stack item by three
```
OP_DUP OP_DUP OP_ADD OP_ADD
```


## OP_MUL
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


## Multiplication by factors close to powers of 2

In this example we multiply the top stack item by `31` which is close to `32 = 2**5`. We use that `x * 31 = x * 32 - x`:

```
btcdeb "[ 
	
	# Input: Some random number on the stack
	42	

	# Duplicate it
	DUP	

	# Multiply it by 32
	DUP ADD 
	DUP ADD 
	DUP ADD 
	DUP ADD 
	DUP ADD

	# Subtract the original input to multiplication by 31
	SWAP
	SUB
# ]" 
```

The above can be easily generalized to play all kinds of code golf to find shortest expressions for multiplications by a constant expressed as sums and differences of powers of two. Even mxing in powers of three might sometimes be the most efficient.
