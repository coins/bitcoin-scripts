# Composite Opcodes

We can define custom opcodes by chaining existing opcodes. 

> DISCLAIMER: DO NOT USE IN PRODUCTION! THE CODE IS INSECURE!



## OP_MUL

You can find a [full implementation of `OP_MUL` here](op_mul.md).

### OP_2MUL
Multiplies the top stack item by 2.
```
OP_DUP OP_ADD
```

### OP_4MUL
Multiplies the top stack item by 4.
```
  OP_2MUL OP_2MUL
= OP_DUP OP_ADD OP_DUP OP_ADD
```

### OP_8MUL
Multiplies the top stack item by 8.
```
  OP_4MUL OP_2MUL
= OP_DUP OP_ADD OP_DUP OP_ADD OP_DUP OP_ADD
```

...

### OP_5MUL
Multiplies the top stack item by 5. It uses that `x * 5 = x * 4 + x`
```
OP_DUP
OP_4MUL
OP_ADD
```

### OP_13MUL
Multiplies the top stack item by 13.
```
OP_DUP
OP_TOALTSTACK

OP_8MUL

OP_FROMALTSTACK
OP_DUP
OP_TOALTSTACK

OP_4MUL

OP_FROMALTSTACK
OP_DUP
OP_TOALTSTACK

OP_ADD
OP_ADD
OP_ADD
```

## OP_MOD and OP_DIV

### OP_2DIV
Integer division by 2 implemented with a _"hint"_. That means the result of the operation is given to us in the unlocking script and we just _verify_ the correctness of the result. This is more efficient than computing the result ourselves.

```
# Integer division by 2 with the help of a hint
# In this example, we divide 119 by 2.
# In the unlocking script the prover provides the result
# as a hint 119//2 = 59, which we verify.

btcdeb "[ 

  119             # Some arbitrary input is on the stack
  OP_OVER         # Copy the hint to the top of the stack

  OP_DUP OP_ADD   # Multiply the hint by 2
  OP_SUB          # Subtract that from the 119
              

  # Now the remainder should be on the stack
  # We verify that it is exactly 0 or 1
              
  OP_DUP          # Make a copy
  OP_0NOTEQUAL    # Returns 0 if the input is 0. 1 otherwise.
  OP_EQUALVERIFY

# ]" 59           # The hint provided is 59 = 119/2
```

We can use our OP_MUL implementations to generalise this for other divisors than 2.


### OP_2MOD
Modulo 2 implemented with _"hints"_. The result of the operation is given to us in the unlocking script and we just _verify_ the correctness of the result. This is more efficient than computing the result ourselves.

```sh
# Modulo 2 with the help of a hint
#
# In this example, we compute 119 mod 2.
# In the unlocking script the prover provides the quotient
# as a hint 119//2 = 59, which we verify.

btcdeb "[ 

  119             # Some arbitrary input is on the stack
  OP_SWAP         # Swap the hint to the top of the stack

  OP_DUP OP_ADD   # Multiply the hint by 2
  OP_SUB          # Subtract that from the 119
	              

  # Now the remainder should be on the stack
  # We verify that it is exactly 0 or 1
              
  OP_DUP OP_DUP   # Make two copies
  OP_0NOTEQUAL    # Returns 0 if the input is 0. 1 otherwise.
  OP_EQUALVERIFY

  # We're done. The result is on the stack
  

# ]" 59           # The hint provided is 59 = 119/2
```





### OP_8DIV

This is an integer division by `8`. The number `123459` is divided by `8`. The result is `15432`. It is given to us as a hint from the unlocking script. We only verfiy its correctness because that's what matters and we can do that much more efficiently than calculating it ourselves.


```sh
btcdeb "[
	OP_DUP
	OP_DUP OP_ADD OP_DUP OP_ADD OP_DUP OP_ADD
	123459
	OP_SWAP
	OP_SUB
	0
	8
	OP_WITHIN
	OP_VERIFY
# ]" 15432
```

Here we use `OP_8MUL = OP_DUP OP_ADD OP_DUP OP_ADD OP_DUP OP_ADD`, which is easy to generalise for other divisors.

### OP_8DIV_REM

We can easily modify the above implementation to return both the result of the integer devision _and_ the remainder.
```sh
btcdeb "[

	OP_DUP
	OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD
	123459
	OP_SWAP
	OP_SUB
	OP_DUP
	0
	8
	OP_WITHIN
	OP_VERIFY

# ]" 15432
```


## Boolean Operators

### OP_BOOLXOR
Computes the logical XOR of the top two stack items.

```
OP_2DUP

OP_NOT
OP_BOOLAND

OP_TOALTSTACK

OP_SWAP
OP_NOT
OP_BOOLAND

OP_FROMALTSTACK

OP_BOOLOR
```

### OP_BOOLXOR Alternative (Credits: Brill Saton)
```
OP_NUMNOTEQUAL
```

Note: It might actually produce a valid result if the user supplies inputs other than 0 or 1, as long as their inputs are still numbers. So sanitizing inputs here is extra important, otherwise users might lose money by putting bad inputs into the transaction, which still executes, and results in a successful transaction where it really should have failed.

### OP_BOOLXNOR
```
OP_NUMEQUAL
```

### Non-Malleable Boolean value
The following scripts ensures an input is either exactly `1` or `0`, represented unambiguously as `01` or `0x` respectively. Any other variations of `1` or `0` results in an error.

```
OP_DUP 
OP_SIZE 
OP_EQUALVERIFY
```

This prevents malleability of script inputs.


## Bitwise Operators

### OP_LSHIFT
Shift all bits of a number one bit to the left.

```
<number>
OP_ABS
OP_DUP
ffffff3f
OP_GREATERTHAN
OP_IF
    00000040
    OP_SUB
    OP_DUP
    OP_ADD
    OP_NEGATE
OP_ELSE
    OP_DUP
    OP_ADD
OP_ENDIF
```

### Bitwise Complement

The following flips all bits of a 32-bit word.

```
btcdeb "[ 

	OP_DUP
	OP_ABS
	OP_TUCK
	OP_NUMNOTEQUAL

	OP_SWAP

	0xffffff7f
	OP_SWAP
	OP_SUB

	OP_SWAP
	OP_NOTIF
		OP_NEGATE
	OP_ENDIF

# ]" 0xAAAAAAAA
```

### Left rotate by 3 Bits

Here is an example of using `op_div_rem_8` to rotate all bits three bits to the left. For simplification the rotation is performed over 24 bit words such that we do not have to deal with the sign of the signed 32 bit words used by Bitcoin Script.

Described in simplified pseudo code:
```
<X>
OP_8DIV_REM
OP_MUL( 2^21 )
OP_ADD
```


The actual code is 
```
btcdeb "[

	OP_DUP
	OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD
	12345
	OP_SWAP
	OP_SUB
	OP_DUP
	0
	8
	OP_WITHIN
	OP_VERIFY

	OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD  
	OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD 

	OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD  
	OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD  

	OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD  OP_DUP OP_ADD  
	OP_DUP OP_ADD


	OP_ADD
# ]" 1543
```

Here you can find a [full implementation of a bitwise rotation of a 32-bit word](op_rotate.md). 

### Right rotate 

A right rotation by `k` bits can be performed with an integer division by `2^k`. To verify the according hint we require `k` many `OP_2MUL`.




### Verify the Binary Representation of a Number

```
btcdeb "[
0
OP_SWAP	
OP_IF
	1
	OP_ADD
OP_ENDIF

OP_SWAP	
OP_IF
	2
	OP_ADD
OP_ENDIF

OP_SWAP	
OP_IF
	4
	OP_ADD
OP_ENDIF

OP_SWAP	
OP_IF
	8
	OP_ADD
OP_ENDIF

OP_SWAP	
OP_IF
	16
	OP_ADD
OP_ENDIF

OP_SWAP	
OP_IF
	32
	OP_ADD
OP_ENDIF

OP_SWAP	
OP_IF
	64
	OP_ADD
OP_ENDIF

OP_SWAP	
OP_IF
	128
	OP_ADD
OP_ENDIF

147
OP_EQUAL

# ]" 1 0 0 1 0 0 1 1

```

### Nullify the First 8 bits of a 32-bit Number

(This also uses bash syntax for some syntactic sugar in Bitcoin Script)

```sh
#!/bin/sh

btcdeb "[ 

	# Input: Some random 32-bit word on the stack
	-1173741827

	
	ABS

	DUP $((2 ** 30)) GREATERTHANOREQUAL
	IF $((2 ** 30)) SUB ENDIF

	DUP $((2 ** 29)) GREATERTHANOREQUAL
	IF $((2 ** 29)) SUB ENDIF

	DUP $((2 ** 28)) GREATERTHANOREQUAL
	IF $((2 ** 28)) SUB ENDIF

	DUP $((2 ** 27)) GREATERTHANOREQUAL
	IF $((2 ** 27)) SUB ENDIF

	DUP $((2 ** 26)) GREATERTHANOREQUAL
	IF $((2 ** 26)) SUB ENDIF

	DUP $((2 ** 25)) GREATERTHANOREQUAL
	IF $((2 ** 25)) SUB ENDIF

	DUP $((2 ** 24)) GREATERTHANOREQUAL
	IF $((2 ** 24)) SUB ENDIF
	
	# Now the first 8 bits of the input are zero

#]" 
```


## Signatures

 
### OP_IFSIGSIZE

Controlling the program flow via signature length.

```
OP_DUP
OP_TOALTSTACK
OP_CHECKSIGVERIFY
OP_FROMALTSTACK
OP_SIZE
OP_TOALTSTACK
OP_DROP

OP_FROMALTSTACK
OP_DUP
OP_TOALTSTACK
OP1
OP_EQUAL
OP_IF
	< CASE 1 >
OP_ENDIF

OP_FROMALTSTACK
OP_DUP
OP_TOALTSTACK
OP2
OP_EQUAL
OP_IF
	< CASE 2 >
OP_ENDIF
	
...
```

### OP_SIGCOMMITMENT
Checks a hash commitment to a signature.

```
OP_DUP
OP_TOALTSTACK
OP_SHA256
<commitment>
OP_VERIFY
```
A signature can't sign itself. Thus, a signature commitment is possible only if the input is not signed. Currently the only way not to sign the input is by misusing `SIGHASH_SINGLE` to use hash `0000...0001` as [discussed here](https://bitcointalk.org/index.php?topic=260595.0). This is not very powerful. Signing the "hash" 0000...0001 is effectively giving away your private key because one could reuse that signature on any of your UTXOs.

 Yet, in future Bitcoin versions with `SIGHASH_NOINPUTS` this opcode enables covenants. We can link transactions with a 2-of-2 MultiSig:

- The first signature pre-commits to the follow-up transaction with `OP_SIGCOMMITMENT` and `SIGHASH_NOINPUTS`
- The second is a regular signature authorizing the transaction for execution 



## Time and Block Height 

The following script proves a minimum block height. Here the unlocking script provides height `700123` and the script verifies that the block height is at least that high

```
btcdeb "[

OP_DUP 0065cd1d OP_LESSTHAN	# The input is a block height iff it is less than 500'000'000
OP_CHECKLOCKTIMEVERIFY

# ]" 700123
```

The same technique applies to get a minimum network time. Furthermore, it can be applied to `OP_CHECKSEQUENCEVERIFY` to get a minimum age of the output spent in the TX. In a script where it is advantagous to provide the highest possible block height, the value on the stack would be the current block height. 

## Lookup Tables and Arrays 

Here you can find implementations of [lookup tables](https://github.com/coins/bitcoin-scripts/blob/master/op_lookup.md) and static maps.

## Sorting

### OP_2SORT

Sort the top two stack items

```
2DUP
MAX
TOALTSTACK
MIN
FROMALTSTACK
```

## Stack Manipulation and Dynamic Arrays

We can copy the item at the bottom of the stack  

```
OP_DEPTH
OP_1SUB
OP_PICK
```

We can pop the item at the bottom of the stack
```
OP_DEPTH
OP_1SUB
OP_ROLL
```


## Script Limits

- [Maximum number of op_codes in script](https://bitcoin.stackexchange.com/questions/38230/maximum-number-of-op-codes-in-script) Limit is 201 non-push opcodes (OP_1 etc, as well as direct pushes are not counted). Non-executed opcodes are also counted and the number of public keys participating in *executed* CHECKMULTISIG and CHDCKMULTISIGVERIFY are also counted towards that limit. the bip-tapscript draft proposes to remove that limit.
- [520-byte limitation on serialized script size](https://github.com/bitcoin/bips/blob/master/bip-0016.mediawiki#520-byte-limitation-on-serialized-script-size)
- max push is 520 bytes
- In Taproot Script many of these [limitations have been removed](https://github.com/bitcoin/bips/blob/master/bip-0342.mediawiki#Resource_limits)
- The stack and the altstack may contain at most 1000 item together in total.
- There is positive zero `0x` and negative zero `0x80`. Both can have trailing zeros i.e `0x0080`. There are 1041 different encodings for False.
- "The argument of `OP_IF` / `NOTIF` in P2WSH must be minimal" 
  - See [here](https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2016-August/013014.html), and [here](https://bips.xyz/141#new-script-semantics). 
  - _"An_ `OP_0NOTEQUAL` _may be used before_ `OP_IF` _or_ `OP_NOTIF` _to imitate the original behaviour (which may also re-enable the malleability vector depending on the exact script)."_
- You cannot use `op_checkmultisig` in Taproot scripts


## See Also 

[Interesting data artifacts from the Bitcoin blockchain](https://github.com/kristovatlas/interesting-bitcoin-data)
