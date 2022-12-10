# Proof of Work in Bitcoin Script 

## Signature Length
The length of the signature can be used as a proof of work committed to the output. Here's a write up of [the ideas of Maxwell and others](https://lists.linuxfoundation.org/pipermail/lightning-dev/2015-November/000344.html) regarding single show signatures.

`OP_SIZE 57 OP_LESSTHANOREQUAL OP_VERIFY <P> OP_CHECKSIGVERIFY`

`Using that <r> value reveals the secret key p: p = (2s - h)/r (mod O(g)).`

The same mechanism can be used to verify a proof of work in script. 

### Limitations
- The PoW is in ECDSA
- The difficulty granularity is byte size steps

## Hash Preimage Challenges
An interactive PoW puzzle is possible with hash pre-images. 
A challenger gives a puzzle commitment to a prover. The prover has to bruteforce which hash sequence led to the commitment. We use the mechanism from "7-bit Integer commitments" an reverse it. The challenger commits to A preimage and hash commitment. The prover needs to bruteforce all possible hashs to find the path from preimage to commitment. The prove of knowledge of the puzzle solution is the integer.
[Here is an example transaction.](https://blockstream.info/nojs/tx/a3803be4f3da166096b4408ffe36a07750f31bb2b58bd660f8f1a0c59a99dda6)


### Features
- The difficulty granularity is bit size 

### Limitations
- Trusted challenger is required
- challenger can cheat by providing an unsolvable puzzle. 
	- yet, prover can proof challenger cheated by bruteforcing all possible 
	- A ZKP might help to prove that a solution exists.
- opcodes grows linear with difficulty
- maximum difficulty is 30 bits ~ 5 bytes


## PoW in Private Key
The challenger creates a N-bit commitment with two hash functions. The secret is a private key. N is the Difficulty. He funds the corresponding address. He publishes the address and the pre image.

Alice challenges Bob for a proof of work.
We use two distinct hash functions `H1` and `H0`.

1. Alice chooses a commitment `C` randomly. `C` is about 32 bytes.
2. Alice chooses a random bit string `x` of length `N`. For example `x = 110101`.
3. Alice derives a secret key `X`. In our example `C = H1(H1(H0(H1(H0(H1( x ))))))`. 
4. Alice derives the corresponding public key and broadcasts a funding transaction.
5. Bob starts the race as soon as he learns `C`.

### Features
- The granularity of the difficulty is bit size 
- No opcodes. Fully off-chain.
- Actually no hash chain is required. It is just a relict from previous ideas. Simply use a nonce as in bitcoin's PoW. Verification time is constant in `N`.
- Any proof of work algorithm can be used. 
- ASIC resistance is possible if the algorithm changes drastically for every new challenge. 

### Limitations
- Trusted dealer is required. He can cheat by providing an unsolvable puzzle.

### Zero Knowledge proof 
We can staple a zero-knowledge proof to the puzzle to prove a solution exist.
Using [Bulletproofs](https://web.stanford.edu/~buenz/pubs/bulletproofs.pdf) a "SHA256 preimage proof is 1.4 KB and takes 750 ms to verify." and it is trustless. 


## Multiple Hash functions 
To increase the number of bits in our schemes we can use multiple hash functions in varying orders. This helps to add entropy to committed values with low entropy.

```
	OP_RIPEMD160
	OP_SHA1
	OP_SHA256
	OP_HASH160
	OP_HASH256
```




# Proof of Work in Bitcoin Script 2


The following script allows everyone to spend; the shorter your signature the earlier you can spend.
```
OP_SIZE
OP_CHECKSEQUENCEVERIFY OP_DROP

OP_CHECKSIGVERIFY
```

The point `R = 1/2 G` has the smallest known `x` coordinate -- `x = 0x3b78ce563f89a0ed9414f5aa28ad0d96d6795f9c63`. If the public key is chosen `P = 1 G` then the ECDSA signature becomes `s=2(H(m)+x)`. So, the smaller `H(m)` the smaller `s` (as long as it is bigger than `x ~ 2^165`). Thus, the above output is spendable by the miner mining the lowest TX hash. Also [see the discussion here](https://gist.github.com/RobinLinus/95de641ed1e3d9fde83bdcf5ac289ce9)
