# Weighted Multi-Signature

We can express a weighted n-of-m voting by duplicating keys and signatures.


## Regular Multi-Sig
Regular 3-of-5 example:
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

## Weighted Multi-Sig 3-of-5
Weighted 3-of-5 with `pub_key_1` having up to two votes:
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


## Weighted Multi-Sig 4-of-7
Weighted 4-of-7 with `pub_key_1` having exactly three votes:

```
OP_DUP
OP_DUP

4
<pub_key_1>
OP_DUP
OP_DUP
<pub_key_2>
<pub_key_3>
<pub_key_4>
7
OP_CHECKMULTISIG
```

Redeem example:
```
<sig_1> <sig_2>
```

or
```
<sig_1> <sig_3>
```

