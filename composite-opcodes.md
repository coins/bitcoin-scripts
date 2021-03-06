# Composite OP_CODES

We can define custom op_codes by chaining existing op_codes. 

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

## Bit Operators

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

## Script Limits

- [Maximum number of op_codes in script](https://bitcoin.stackexchange.com/questions/38230/maximum-number-of-op-codes-in-script) Limit is 201 non-push opcodes (OP_1 etc, as well as direct pushes are not counted). Non-executed opcodes are also counted and the number of public keys participating in *executed* CHECKMULTISIG and CHDCKMULTISIGVERIFY are also counted towards that limit. the bip-tapscript draft proposes to remove that limit.
- [520-byte limitation on serialized script size](https://github.com/bitcoin/bips/blob/master/bip-0016.mediawiki#520-byte-limitation-on-serialized-script-size)
- max push is 520 bytes
- There is positive zero `0x` and negative zero `0x80`. Both can have trailing zeros i.e `0x0080`. There are 1041 different encodings for False.

## See Also 

[Interesting data artifacts from the Bitcoin blockchain](https://github.com/kristovatlas/interesting-bitcoin-data)
