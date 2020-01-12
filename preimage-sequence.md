# Hash Pre-Image Sequence Commitment 


## Basic Pre-Image Sequence 

Alice commits C into an output such that she can reveal `R_N  = HASH( ... HASH( C ))`.
We combine that to create transactions with dynamic lock times. 

### 1 Bit Commitment 

```
OP_HASH256

<COMMITMENT_HASH>

OP_SWAP

OP_ROT
OP_IF   
        OP_EQUALVERIFY                        
        24
        OP_CHECKSEQUENCEVERIFY                               
OP_ENDIF
OP_HASH256
OP_EQUALVERIFY
```

Spendable with:
```
1 <COMMITMENT_PREIMAGE>
```
OR 
```
0 <HASH256(COMMITMENT_PREIMAGE)>
```


### 4 Bit Commitment 

`COMMITMENT = HASH(HASH(HASH(HASH(PREIMAGE))))`

```
OP_HASH256
<COMMITMENT_HASH>

OP_SWAP


OP_ROT
OP_IF	
        OP_EQUALVERIFY                        
        24
        OP_CHECKSEQUENCEVERIFY                               
OP_ENDIF
OP_HASH256


OP_ROT
OP_IF	
        OP_EQUALVERIFY                        
        18
        OP_CHECKSEQUENCEVERIFY                               
OP_ENDIF
OP_HASH256


OP_ROT
OP_IF	
        OP_EQUALVERIFY                        
        12
        OP_CHECKSEQUENCEVERIFY                               
OP_ENDIF
OP_HASH256


OP_ROT
OP_IF	
        OP_EQUALVERIFY                        
        6
        OP_CHECKSEQUENCEVERIFY                               
OP_ENDIF

```

### Example Input
Hashed three times, spendable after 12 blocks.
```
0 0 1 0 <HASH(HASH(HASH256(PREIMAGE)))>
```

Hashed two times, spendable after 6 blocks.
```
0 0 0 1 <HASH(HASH256(PREIMAGE))>
```

## 2 Parties

Alice and Bob create an output in the Blockchain such that they can change its timelock afterwards.




Using two such time lock commitments in a payment channel with Alice and Bob, they can always execute their most recent state in a single transaction.


```

OP_HASH256



<COMMITMENT_1>
OP_SWAP



OP_ROT
OP_IF   
        OP_EQUALVERIFY                        
        24
        OP_CODESEPARATOR                                                       
OP_ENDIF
OP_HASH256


OP_ROT
OP_IF   
        OP_EQUALVERIFY                        
        18
        OP_CODESEPARATOR                                                       
OP_ENDIF
OP_HASH256


OP_ROT
OP_IF   
        OP_EQUALVERIFY                        
        12
        OP_CODESEPARATOR                                                       
OP_ENDIF
OP_HASH256


OP_ROT
OP_IF   
        OP_EQUALVERIFY                        
        6
        OP_CODESEPARATOR                                                       
OP_ENDIF

   
   
OP_CHECKSEQUENCEVERIFY
OP_DROP






<COMMITMENT_2>
OP_SWAP



OP_ROT
OP_IF   
        OP_EQUALVERIFY                        
        24
        OP_CODESEPARATOR                                                       
OP_ENDIF
OP_HASH256


OP_ROT
OP_IF   
        OP_EQUALVERIFY                        
        18
        OP_CODESEPARATOR                                                       
OP_ENDIF
OP_HASH256


OP_ROT
OP_IF   
        OP_EQUALVERIFY                        
        12
        OP_CODESEPARATOR                                                       
OP_ENDIF
OP_HASH256


OP_ROT
OP_IF   
        OP_EQUALVERIFY                        
        6
        OP_CODESEPARATOR                                                       
OP_ENDIF

   
   
OP_CHECKSEQUENCEVERIFY
OP_DROP








2
<ALICE KEY>
<BOB KEY>
2
OP_CHECKMULTISIG


```

The `OP_CODESEPARATOR` guarantee signatures bind to pre-images. To trigger the N-th case Alice can not use any of Bob's signatures except for his N-th.

[View in Script Editor](../../apps/script-editor/index.html#T1BfSEFTSDI1NgoKJHtsb29wKDIsIGkgPT4gYAoKPENPTU1JVE1FTlRfJHtpKzF9PgpPUF9TV0FQCgoke2xvb3AoICA0LCAoaW5kZXgsIGxlbmd0aCkgPT4gIGAKCk9QX1JPVApPUF9JRgkKICAgICAgICBPUF9FUVVBTFZFUklGWSAgICAgICAgICAgICAgICAgICAgICAgIAogICAgICAgICR7IChsZW5ndGgtaW5kZXgpICogNiB9CiAgICAgICAgT1BfQ09ERVNFUEVSQVRPUiAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAgICAKT1BfRU5ESUYKJHsgaW5kZXggPCBsZW5ndGgtMSA/ICdPUF9IQVNIMjU2JyA6ICcnIH0KYCl9ICAgCiAgIApPUF9DSEVDS1NFUVVFTkNFVkVSSUZZCk9QX0RST1AKCgoKCmApfQoKCgoyCjxBTElDRSBLRVk+CjxCT0IgS0VZPgoyCk9QX0NIRUNLTVVMVElTSUcgIA==)


### Limitations 
- 7 bit per party = 14 bit commitment + 2-of-2 signature 



### Multiple Hash Functions
Use multiple hash functions to encode more states.

```
<COMMITMENT_HASH>

OP_SWAP


OP_ROT
OP_IF   
        OP_EQUALVERIFY                        
        24
        OP_CHECKSEQUENCEVERIFY                               
OP_ENDIF
OP_HASH256


OP_ROT
OP_IF   
        OP_EQUALVERIFY                        
        18
        OP_CHECKSEQUENCEVERIFY                               
OP_ENDIF
OP_HASH256


OP_ROT
OP_IF   
        OP_EQUALVERIFY                        
        12
        OP_CHECKSEQUENCEVERIFY                               
OP_ENDIF
OP_HASH256


OP_ROT
OP_IF   
        OP_EQUALVERIFY                        
        6
        OP_CHECKSEQUENCEVERIFY                               
OP_ENDIF
```


## References 
- [Reddit discussion of the possible uses OP_CHECKSEQUENCEVERIFY](https://www.reddit.com/r/Bitcoin/comments/4p4klg/bitcoin_core_project_the_csv_soft_fork_has/d4i01he/)




