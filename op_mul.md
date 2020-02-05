# OP_MUL
We construct a bitcoin opcode to multiply `a` by `b`. The following is a sketch

## OP_MUL2
Multiply by 2
```
OP_MUL2 = OP_DUP OP_ADD
```

## OP_MUL(2^k)
Multiply by powers of 2
```
OP_MUL(2^k) = OP_MUL(2^(k-1)) OP_MUL2 
```

Examples:
```
OP_MUL4 = OP_DUP OP_ADD OP_DUP OP_ADD
OP_MUL8 = OP_DUP OP_ADD OP_DUP OP_ADD OP_DUP OP_ADD
OP_MUL16 = OP_DUP OP_ADD OP_DUP OP_ADD OP_DUP OP_ADD OP_DUP OP_ADD
...
```

## OP_MUL
Multiply `a` by `b`. The result of `a * b` must fit into a signed 32-bit integer.
Additionally, the following script works only for `b<16` being a 4-bit integer.

```
btcdeb "[

  0 
  OP_TOALTSTACK

  OP_DUP
  8
  OP_GREATERTHANOREQUAL
  OP_IF
    8 
    OP_SUB
    OP_SWAP
    OP_DUP 
    OP_DUP OP_ADD OP_DUP OP_ADD OP_DUP OP_ADD
    OP_FROMALTSTACK
    OP_ADD
    OP_TOALTSTACK
    OP_SWAP
  OP_ENDIF

  OP_DUP
  4
  OP_GREATERTHANOREQUAL
  OP_IF
    4 
    OP_SUB
    OP_SWAP
    OP_DUP 
    OP_DUP OP_ADD OP_DUP OP_ADD
    OP_FROMALTSTACK
    OP_ADD
    OP_TOALTSTACK
    OP_SWAP
  OP_ENDIF

  OP_DUP
  2
  OP_GREATERTHANOREQUAL
  OP_IF
    2 
    OP_SUB
    OP_SWAP
    OP_DUP 
    OP_DUP OP_ADD
    OP_FROMALTSTACK
    OP_ADD
    OP_TOALTSTACK
    OP_SWAP
  OP_ENDIF

  OP_NOT
  OP_IF
    OP_DROP
    0
  OP_ENDIF

  OP_FROMALTSTACK
  OP_ADD

"] 60 14
```

#### Circut Size 
The number of required opcodes is linear in bit size of `b`:
- `4 bits -> 52 opcodes`
- `5 bits -> 71 opcodes`
- `6 bits -> 92 opcodes`
- `7 bits -> 115 opcodes`
- `8 bits -> 140 opcodes`

Maximum number of opcodes per script is 201 ([source](https://bitcoin.stackexchange.com/questions/38230/maximum-number-of-op-codes-in-script)).
