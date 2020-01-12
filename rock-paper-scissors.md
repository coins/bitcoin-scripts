# Rock Paper Scissors

Both Alice and Bob make pre-image length commitments as described in betcoins.


For compact code we use some group theory. 


## Solution 1: Additive Group mod 3

```
btcdeb "[

	OP_DUP 
	OP_HASH160 
		74f36c5e0c6ad544a3279502ae7c9bce698012b1
	OP_EQUALVERIFY 
	
	OP_SIZE
	OP_NIP

	OP_DUP	
	16
	19
	OP_WITHIN
	OP_VERIFY
	
	15
	OP_SUB
	
	OP_SWAP

	OP_DUP 
	OP_HASH160
		80ad9c03fd4f8d1eebfadc515df7956ddf3c3dc7
	OP_EQUALVERIFY 

	OP_SIZE
	OP_NIP
	
	OP_DUP	
	16
	19
	OP_WITHIN
	OP_VERIFY
	
	15
	OP_SUB
	

	OP_2DUP
	OP_EQUAL
	OP_IF
		OP_2DROP
		cccccccc
	OP_ELSE

		OP_1ADD

		OP_DUP
		2
		OP_GREATERTHAN
		OP_IF
			OP_DROP
			1
		OP_ENDIF

		OP_EQUAL
		OP_IF
			aaaaaaaa
		OP_ELSE
			bbbbbbbb
		OP_ENDIF

	OP_ENDIF

	OP_DROP
	OP_TRUE

# ]" bbbbbbb0bbbbbbb0bbbbbbb0bbbbbbb0bbbb aaaaaaa0aaaaaaa0aaaaaaa0aaaaaaa0aaaa

```

## Solution 2: Multiplicative Group mod 7
We want a multiplicative group of three elements.
The finite field to the prime 7 has the subgroup `{1,2,4}`. `2` is a generator of this subgroup.
Both Alice and Bob commit to an element of that group; `A` and `B` respectively. 
We check if Alice is Bob's "successor" by multiplying her value with the generator `2`.
Three cases:
- `A == B`: That's a tie.
- `A*2 == B`: Alice Won
- `A == B*2`: Bob Won


```
OP_DUP 
OP_HASH160 
	< Alice's Commitment >
OP_EQUALVERIFY 

OP_SIZE
OP_NIP

OP_DUP	
16
20
OP_WITHIN
OP_VERIFY

15
OP_SUB

OP_DUP
3 
OP_NUMNOTEQUAL
OP_VERIFY

OP_SWAP

OP_DUP 
OP_HASH160 
	< Bob's Commitment >
OP_EQUALVERIFY 

OP_SIZE
OP_NIP

OP_DUP	
16
20
OP_WITHIN
OP_VERIFY

15
OP_SUB

OP_DUP
3 
OP_NUMNOTEQUAL
OP_VERIFY


OP_2DUP
OP_EQUAL
OP_IF
	OP_2DROP
	< Case Tie > 
OP_ELSE

	OP_DUP
	OP_ADD

	OP_DUP
	7
	OP_GREATERTHAN
	OP_IF
		OP_DROP
		1
	OP_ENDIF

	OP_EQUAL
	OP_IF
		< Case Alice Wins >
	OP_ELSE
		< Case Bob Wins >
	OP_ENDIF

OP_ENDIF
```


Here is [an example transaction](https://blockstream.info/nojs/tx/4586f390acbf9c34157f6331ad70dbb8d476edb65d86aa17e6c481a79c97c91c?expand)

### Limitations 
- Unnecessarily complex: We used the multiplicative group with three elements. That group is isomorphic to the additive group `{0,1,2}`. In that group the algorithm is much simpler. It uses only addition and less opcodes.






