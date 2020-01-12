# Integer Commitments

## Bit Commitment

1. Choose secret number `N` 
2. Choose secret nonce `R` which is 31 bytes of randomness 
3. The public commitment `C` is `R` hashed `N` times.

The following scriptPubKey enforces the spender to reveal `R` and thus `N`:
```
OP_SIZE
32
OP_EQUAL
OP_NOT
OP_VERIFY

OP_SHA256 
OP_DUP 
	<commitment>
OP_DUP
OP_TOALTSTACK
OP_EQUAL
OP_IF
	OP_DROP
	<Case 1>
OP_ELSE
	OP_SHA256
	OP_FROMALTSTACK
	OP_EQUALVERIFY
	<Case 1>
OP_ENDIF

```

## 4 Bit Commitment

A commitment to a bitstring using two hash functions. [Here is an example transaction](https://blockstream.info/tx/1ac287e1c6d2121d1efdd79e13055787226b95b3e3647c2b04c825693abbf5a5?expand).
For example, a commitment to the value `1101` expressed with hashA and hashA:
- Choose random `r`
- Commitment `C = hashA( hashA( hashB ( hashA( r )))`

In the following script we use
- `hashA(x) = OP_HASH160( OP_SHA256(x) )` and 
- `hashB = OP_HASH160( x )`

```
OP_SWAP
OP_IF
	OP_SHA256

	1
	OP_TOALTSTACK
OP_ENDIF
OP_HASH160

OP_SWAP
OP_IF
	OP_SHA256

	OP_FROMALTSTACK
	2
	OP_ADD
	OP_TOALTSTACK
OP_ENDIF
OP_HASH160

OP_SWAP
OP_IF
	OP_SHA256 

	OP_FROMALTSTACK
	4
	OP_ADD
	OP_TOALTSTACK
OP_ENDIF
OP_HASH160

OP_SWAP
OP_IF
	OP_SHA256

	OP_FROMALTSTACK
	8
	OP_ADD
	OP_TOALTSTACK 
OP_ENDIF
OP_HASH160

32d8c4e2fa54d5c831f2f10d2b30352e9e3c026d
OP_EQUALVERIFY
OP_FROMALTSTACK
```

This output is spendable with `1 1 0 1 aabbccddeeff`, leaving `0x0d` on the stack and `0x0d == 0b1101` which reveals our commitment.


## Limitations
- Note: In this naive implementation the first bit is vulnerable. We can solve that by prepending `OP_SHA1` at the beginning of the script.
- The commitment requires 9 opcodes per bit. That is a maximum of about 21 bits per script.
	- 9 opcodes = ( 4 control flow ) + ( 5 logic ) codes. Logic optimizations are application specific.

## 7 Bit Commitment 
```
# Bit Commitment with pre-image hashed with one of two hash functions

btcdeb "[

	OP_SWAP
	OP_IF
		OP_SHA256

		1
		OP_TOALTSTACK
	OP_ENDIF
	OP_HASH160

	OP_SWAP
	OP_IF
		OP_SHA256

		OP_FROMALTSTACK
		2
		OP_ADD
		OP_TOALTSTACK
	OP_ENDIF
	OP_HASH160

	OP_SWAP
	OP_IF
		OP_SHA256 

		OP_FROMALTSTACK
		4
		OP_ADD
		OP_TOALTSTACK
	OP_ENDIF
	OP_HASH160

	OP_SWAP
	OP_IF
		OP_SHA256

		OP_FROMALTSTACK
		8
		OP_ADD
		OP_TOALTSTACK 
	OP_ENDIF
	OP_HASH160

	OP_SWAP
	OP_IF
		OP_SHA256

		OP_FROMALTSTACK
		16
		OP_ADD
		OP_TOALTSTACK 
	OP_ENDIF
	OP_HASH160

	OP_SWAP
	OP_IF
		OP_SHA256

		OP_FROMALTSTACK
		32
		OP_ADD
		OP_TOALTSTACK 
	OP_ENDIF
	OP_HASH160

	OP_SWAP
	OP_IF
		OP_SHA256

		OP_FROMALTSTACK
		64
		OP_ADD
		OP_TOALTSTACK 
	OP_ENDIF
	OP_HASH160

	166be064396607f51495e6cb412c92899edae667
	OP_EQUALVERIFY
	OP_FROMALTSTACK

# ]" 1 1 0 0 1 0 1 aabbccddeeff 


```


This is a powerful primitive. In combination with other opcodes it allows the computation of arbitrary circuits.


## 2 Party Bit Commitments 

Alice chooses `r` randomly. She creates a bit commitment either:
- Commitment to 0: `A = HASH160(r)`
- Commitment to 1: `A = SHA(HASH160(r))`

She sends `A` to Bob.
Bob creates a bit commitment either:
- Commitment to 0: `B = HASH160(A)`
- Commitment to 1: `B = SHA(HASH160(A))`

Bob signs a Transaction and commits `B` to the Blockchain.
Alice learns Bob's commitment value as soon as she sees the signed transaction.

If Alice reveals `r`, the blockchain learns both Alice's and Bob's value. 

This is a 2-party 2-bit vector commitment in a single hash.

Using the method from above, we extend this to a 2-party N-bit vector. Alice and Bob can commit to arbitrary bit strings of total length N.


