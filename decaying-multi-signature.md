# Decaying Multisignature 

A 3-of-5 bitcoin multisig that after 60 months decays into a 2-of-5 and after 66 months decays into a 1-of-5.
 
It makes it easy for the user to secure only 5 keys while at the same time allowing the user to lose 4 keys and still recover funds.
The downside is you need to move funds every 5 years, but I think that’s reasonable to avoid 2 extra keys to secure.

## Basic Idea
```
IF
  3
ELSE
  IF
    <now + 60 months> OP_CHECKLOCKTIMEVERIFY DROP
    2
  ELSE
    <now + 66 months> OP_CHECKLOCKTIMEVERIFY DROP
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
  OP_CHECKLOCKTIMEVERIFY DROP
ENDIF


<pubkey_1> <pubkey_2> <pubkey_3> <pubkey_4> <pubkey_5> 5 CHECKMULTISIG
```

### Non-Malleability 
Our script takes up to two booleans as inputs. These should be non-malleable.

The following primitive ensures an input of expected type "boolean" is exactly either `0x1` or `0x`:

```
OP_DUP OP_SIZE OP_EQUALVERIFY
```
Inserted into our script:
```
OP_DUP OP_SIZE OP_EQUALVERIFY
IF
  3
ELSE
  OP_DUP OP_SIZE OP_EQUALVERIFY
  IF
    2
    <now + 60 months>
  ELSE
    1
    <now + 66 months>
  ENDIF
  OP_CHECKLOCKTIMEVERIFY DROP
ENDIF


<pubkey_1> <pubkey_2> <pubkey_3> <pubkey_4> <pubkey_5> 5 CHECKMULTISIG
```


### Multiple UTXOs 
All your UTXOs should decay at the same time so that your HD seeds can recover all funds. That's why we use *absolute* timelocks with `OP_CHECKLOCKTIMEVERIFY` instead of *relative* timelocks with `OP_CHECKSEQUENCEVERIFY`.


## Credits 
This idea was found on Twitter in a thread by [@JWWeatherman_ and @giacomozucco](https://twitter.com/JWWeatherman_/status/1249101431161774080).

See also Pieter Wuille's Miniscript example ["A 3-of-3 that turns into a 2-of-3 after 90 days"](http://bitcoin.sipa.be/miniscript/).
