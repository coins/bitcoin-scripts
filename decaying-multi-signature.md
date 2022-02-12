# Decaying Multisignature 

A 3-of-5 bitcoin multisig that after 60 months decays into a 2-of-5 and after 66 months decays into a 1-of-5.
 
It makes it easy for the user to secure only 5 keys while at the same time allowing the user to lose 4 keys and still recover funds. The downside is you need to move funds every 5 years.

## Basic Idea
```
IF
  3
ELSE
  IF
    <now + 60 months> CHECKLOCKTIMEVERIFY DROP
    2
  ELSE
    <now + 66 months> CHECKLOCKTIMEVERIFY DROP
    1
  ENDIF
ENDIF

<pubkey_1> <pubkey_2> <pubkey_3> <pubkey_4> <pubkey_5> 5 CHECKMULTISIG
```

### A Small Optimization
We can optimize the above code a bit to save two opcodes:
```
IF
  3
ELSE
  IF
    2
    <now + 60 months>
  ELSE
    1
    <now + 66 months>
  ENDIF
  CHECKLOCKTIMEVERIFY DROP
ENDIF

<pubkey_1> <pubkey_2> <pubkey_3> <pubkey_4> <pubkey_5> 5 CHECKMULTISIG
```

### Non-Malleability 
Our script takes up to two booleans as inputs. These should be non-malleable.

The following primitive ensures an input of expected type "boolean" is exactly either `0x1` or `0x`:

```
DUP SIZE EQUALVERIFY
```

Inserted into our script:
```
DUP SIZE EQUALVERIFY
IF
  3
ELSE
  DUP SIZE EQUALVERIFY
  IF
    2
    <now + 60 months>
  ELSE
    1
    <now + 66 months>
  ENDIF
  CHECKLOCKTIMEVERIFY DROP
ENDIF

<pubkey_1> <pubkey_2> <pubkey_3> <pubkey_4> <pubkey_5> 5 CHECKMULTISIG
```


### Multiple UTXOs 
All your UTXOs should decay at the same time so that your HD seeds can recover all funds. That's why we use the *absolute* timelock `CHECKLOCKTIMEVERIFY` instead of the *relative* timelock`CHECKSEQUENCEVERIFY`.

### Relative Timelocks
If we don't want to move our funds every 5 years we can use a kick-off transaction and relative timelocks. E.g. the presigned kickoff transaction creates a 3-of-5 output that decays into a 2-of-5 one year after it hits the chain. This way we have to reset the MultiSig only in case the kickoff transaction was broadcasted maliciously. Major drawback here is that this requires to sign a kickoff transaction for every output we receive.


## Credits 
Our work is an optimisation. The original idea for a decaying multisig was found on Twitter in a thread by [@JWWeatherman_ and @giacomozucco](https://twitter.com/JWWeatherman_/status/1249101431161774080). 

See also Pieter Wuille's Miniscript example ["A 3-of-3 that turns into a 2-of-3 after 90 days"](http://bitcoin.sipa.be/miniscript/).



# Alternative Decaying MultiSig
A decaying MultiSig is also possible using a regular MultiSig. One party creates a TX with a `nLocktime` and signs it using `SIGHASH_NONE`. Such a partially signed transaction makes an UTXO decay into a MultiSig with a threshold decremented by 1.

### Drawbacks 
- It requires interaction. 
- It requires a new TX for every UTXO. 
- Each party has to sign such a TX. 
- Each party has to store all TXs securely.
