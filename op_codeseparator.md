# OP_CODESEPARATOR 

OP_CODESEPARATOR let's you sign off on a specific script execution path -- a powerful primitive that we can use for interesting constructions?


## Payment Channels Alternative 


### Idea

Can we use `OP_CODESEPARATOR` like this?

```
OP_IF
        OP_CODESEPARATOR
        <BRANCH_1>
OP_ELSE 
        <BRANCH_2>
OP_ENDIF

2
<ALICE KEY>
<BOB KEY>
2
OP_CHECKMULTISIG 

```
Here, `BRANCH_1` is spendable with:
```
1 <sigA(BRANCH_1)> <sigB(BRANCH_1)>
```

And `BRANCH_2` is spendable with:
```
0 <sigA(BRANCH_2)> <sigB(BRANCH_2)>
```

Such that Alice and Bob can only execute a branch if they have each other's signatures for that particular branch.

- [Documentation seem to say yes](https://en.bitcoin.it/wiki/OP_CHECKSIG)
- [Tests seem to say yes](https://github.com/bitcoin/bitcoin/blob/452bb90c718da18a79bfad50ff9b7d1c8f1b4aa3/src/test/data/tx_valid.json#L142)

### Usage

If the idea works, then we can combine it with a time lock:

```
OP_IF   
        OP_CODESEPARATOR
        10
OP_ELSE 
        100
OP_ENDIF

OP_CHECKSEQUENCEVERIFY
OP_DROP

2
<ALICE KEY>
<BOB KEY>
2
OP_CHECKMULTISIG 


```

And multiple states represented by time locks:

```
OP_IF   
    OP_CODESEPARATOR
    600300    
OP_ENDIF


OP_IF   
    OP_CODESEPARATOR
    600200    
OP_ENDIF


OP_IF   
    OP_CODESEPARATOR
    600100    
OP_ENDIF



OP_CHECKLOCKTIMEVERIFY
OP_DROP

2
<Alice.Pubkey>
<Bob.Pubkey>
2
OP_CHECKMULTISIG  
```

Spendable with one of the following:
```
<Bob.Signature1>
<Alice.Signature1>
0 0 1
```

```
<Bob.Signature2>
<Alice.Signature2>
0 1 0
```

```
<Bob.Signature3>
<Alice.Signature3>
1 0 0
```

This could work recursively, such that we repeat the same contract in a next transaction authorized by the first branch of the previous transaction. 
After some arbitrary recursion depth we can settle the full branch and continue with the next branch of the base transaction.




### References 
- [[Lightning-dev] We don't need R-Value, how OP_CODESEPARATOR saves the day](https://lists.linuxfoundation.org/pipermail/lightning-dev/2016-March/000455.html)
- [[bitcoin-dev] Making OP_CODESEPARATOR and FindAndDelete in non-segwit scripts non-standard](https://lists.linuxfoundation.org/pipermail/bitcoin-dev/2017-November/015292.html)
- [OP_CODESEPARATOR used in tumblebit](https://github.com/bitcoin/bitcoin/pull/11423)
- [[Policy] Several transaction standardness rules #11423](https://github.com/bitcoin/bitcoin/pull/11423)
- [Eltoo](https://blockstream.com/eltoo.pdf)








# Off-chain Updates of Payment Channels

In today's Bitcoin script `OP_CODESEPARATOR` might be a powerful "non-standard" opcode. It lets you sign off on specific execution paths. With this primitive and 2-of-2 MultiSigs we can build payment channels. The renegotation protocol might simplify existing off-chain protocols, and enable new use cases. 

## Example:

```
OP_IF   
    # Case 1
    OP_CODESEPARATOR
    600300    
OP_ENDIF


OP_IF 
    # Case 2
    OP_CODESEPARATOR
    600200    
OP_ENDIF


OP_IF  
    # Case 3
    OP_CODESEPARATOR
    600100    
OP_ENDIF


OP_CHECKLOCKTIMEVERIFY
OP_DROP

2
<Alice.Pubkey>
<Bob.Pubkey>
2
OP_CHECKMULTISIG  
```

Spendable with one of the following witnesses:

```
<Bob.SignatureCase1>
<Alice.SignatureCase1>
0 0 1
```

```
<Bob.SignatureCase2>
<Alice.SignatureCase2>
0 1 0
```

```
<Bob.SignatureCase3>
<Alice.SignatureCase3>
1 0 0
```

## Features:
- It is not required to stay online to watch the channel. An attacker cannot cheat until the time lock opens.
- It is possible to chain multiple transactions and settle them all off-chain by opening the next time lock of the on-chain transaction output.
- not required to store any state updates other than the most recent one
- Given the size constraints of Bitcoin scripts, a single transaction can have more than 65 sub-branches.
- Does it allow Eltoo-like channels without any fork?
 - Can we build channel factories to on-board new users off-chain?








