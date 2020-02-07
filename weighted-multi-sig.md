# Weighted Multi Signature

We can express a weighted n-of-m voting by duplicating keys and signatures.


## Regular Multi-Sig
3-of-5
```
3 
<pub_key_1> 
<pub_key_2>
<pub_key_3>
<pub_key_4>
<pub_key_5>
5
OP_CHECKMULTISIG
```
Redeem example:
```
<sig_1> <sig_2> <sig_5>
```

## Weighted Multi-Sig
Weighted 3-of-5 with `pub_key_1` having up to two votes. 
```
OP_IF
 OP_DUP
OP_ENDIF

3 
<pub_key_1>
OP_DUP
<pub_key_2>
<pub_key_3>
<pub_key_4>
5
OP_CHECKMULTISIG
```

Redeem example:
```
0 <sig_2> <sig_3> <sig_4>
```

or
```
1 <sig_1> <sig_4>
```
