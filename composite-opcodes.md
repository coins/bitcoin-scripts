# Composite OP_CODES

We can define custom opcodes by chaining existing opcodes. 

> DISCLAIMER: DO NOT USE IN PRODUCTION! THE CODE IS INSECURE!

## OP_MUL

You can find a [full implementation of `OP_MUL` here](op_mul.md).

### OP_MUL2
Multiplies the top stack item by 2.
```
	OP_DUP OP_ADD
```

### OP_MUL4
Multiplies the top stack item by 4.
```
	OP_MUL2 OP_MUL2
=	OP_DUP OP_ADD OP_DUP OP_ADD
```

### OP_MUL8
Multiplies the top stack item by 8.
```
	OP_MUL4 OP_MUL2
=	OP_DUP OP_ADD OP_DUP OP_ADD OP_DUP OP_ADD
```

...

### OP_MUL5
Multiplies the top stack item by 5.
```
	OP_DUP
	OP_TOALTSTACK
	OP_MUL4

	OP_FROMALTSTACK
	OP_DUP
	OP_TOALTSTACK

	OP_ADD
```

### OP_MUL13
Multiplies the top stack item by 13.
```
	OP_DUP
	OP_TOALTSTACK

	OP_MUL8

	OP_FROMALTSTACK
	OP_DUP
	OP_TOALTSTACK

	OP_MUL4

	OP_FROMALTSTACK
	OP_DUP
	OP_TOALTSTACK

	OP_ADD
	OP_ADD
	OP_ADD
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


### Sanitise a Boolean Value
Ensure a given value is either 0 or 1:

```
OP_DUP 
OP_SIZE 
OP_EQUALVERIFY
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

## Non-Malleable Bit Commitment 
The following scripts ensures an input is either exactly `1` or `0`, represented unambiguously as `01` or `0x` respectively. Any other variations of `1` or `0` results in an error.
```
OP_DUP OP_SIZE OP_EQUALVERIFY
```
This prevents malleability of script inputs.


## OP_LSHIFT
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

## Verify the Binary Representation of a Number

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

## OP_MOD2
Modulo 2 implementend with _"hints"_. So the result of the operation is given to us in the unlocking script and we only _verify_ the result of the operation. This is more efficient than computing the result ourselves. 

```
<X>
<X DIV 2>
<X DIV 2>
OP_0NOTEQUAL
OP_DUP
OP_TOALTSTACK
OP_ADD
OP_NUMEQUALVERIFY
OP_FROMALTSTACK
```

## Script Limits

- [Maximum number of op_codes in script](https://bitcoin.stackexchange.com/questions/38230/maximum-number-of-op-codes-in-script) Limit is 201 non-push opcodes (OP_1 etc, as well as direct pushes are not counted). Non-executed opcodes are also counted and the number of public keys participating in *executed* CHECKMULTISIG and CHDCKMULTISIGVERIFY are also counted towards that limit. the bip-tapscript draft proposes to remove that limit.
- [520-byte limitation on serialized script size](https://github.com/bitcoin/bips/blob/master/bip-0016.mediawiki#520-byte-limitation-on-serialized-script-size)
- max push is 520 bytes
- There is positive zero `0x` and negative zero `0x80`. Both can have trailing zeros i.e `0x0080`. There are 1041 different encodings for False.

## See Also 

[Interesting data artifacts from the Bitcoin blockchain](https://github.com/kristovatlas/interesting-bitcoin-data)
