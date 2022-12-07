# Composite OP_CODES

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
Multiplies the top stack item by 5.
```
OP_DUP
OP_TOALTSTACK
OP_4MUL

OP_FROMALTSTACK
OP_DUP
OP_TOALTSTACK

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

### OP_2MOD
Modulo 2 implemented with _"hints"_. The result of the operation is given to us in the unlocking script and we just _verify_ the correctness of the result. This is more efficient than computing the result ourselves.

```
<X>
<HINT: X DIV 2>
<HINT: X MOD 2>
OP_0NOTEQUAL
OP_DUP
OP_TOALTSTACK
OP_ADD
OP_NUMEQUALVERIFY
OP_FROMALTSTACK
```

### OP_2DIV
Integer division by 2 implemented with a _"hint"_. That means the result of the operation is given to us in the unlocking script and we just _verify_ the correctness of the result. This is more efficient than computing the result ourselves.

```
<HINT: X DIV 2>
OP_DUP
OP_DUP
OP_ADD
<X>
OP_SWAP
OP_SUB
0
2
OP_WITHIN
OP_VERIFY
```

We can use our OP_MUL implementations to generalise this for other divisors than 2.


### OP_8DIV

This is an integer division by `8`. The number `123459` is divided by `8`. The result is `15432`. It is given to us as a hint from the unlocking script. We only verfiy its correctness because that's what matters and we can do that much more efficiently than calculating it ourselves.


```
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
```
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

The following script proves that the block height is at least 700123

```
btcdeb "[

OP_DUP 0065cd1d OP_LESSTHAN	# The input is a block height iff it is less than 500'000'000
OP_CHECKLOCKTIMEVERIFY

# ]" 700123
```

The same technique applies to get a minimum network time. Furthermore, it can be applied to `OP_CHECKSEQUENCEVERIFY` to get a minimum age of the output spent in the TX.

## Script Limits

- [Maximum number of op_codes in script](https://bitcoin.stackexchange.com/questions/38230/maximum-number-of-op-codes-in-script) Limit is 201 non-push opcodes (OP_1 etc, as well as direct pushes are not counted). Non-executed opcodes are also counted and the number of public keys participating in *executed* CHECKMULTISIG and CHDCKMULTISIGVERIFY are also counted towards that limit. the bip-tapscript draft proposes to remove that limit.
- [520-byte limitation on serialized script size](https://github.com/bitcoin/bips/blob/master/bip-0016.mediawiki#520-byte-limitation-on-serialized-script-size)
- max push is 520 bytes
- In Taproot Script many of these [limitations have been removed](https://github.com/bitcoin/bips/blob/master/bip-0342.mediawiki#Resource_limits)
- There is positive zero `0x` and negative zero `0x80`. Both can have trailing zeros i.e `0x0080`. There are 1041 different encodings for False.



## See Also 

[Interesting data artifacts from the Bitcoin blockchain](https://github.com/kristovatlas/interesting-bitcoin-data)
