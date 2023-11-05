# Split Into Bits

Algorithms to split a stack item into a string of bits.

## Lookup Table 

Here, we use a lookup table to circumvent the `OP_IF, OP_ELSE, OP_ENDIF` branches. This idea can get further optimized and can act as a basis to implement bitwise operations such as XOR.

```js
`
// Lookup table
${ 2**1 }
0
${ 2**2 }
0
${ 2**3 }
0
${ 2**4 }
0
${ 2**5 }
0
${ 2**6 }
0
${ 2**7 }
0

// The input value
// We use a bit string only for debugging
// It's a single item on the stack
${ 0b01100001 }

DUP
${ 2**7 }
GREATERTHANOREQUAL
1ADD
ROLL
SUB
SWAP
TOALTSTACK

DUP
${ 2**6 }
GREATERTHANOREQUAL
1ADD
ROLL
SUB
SWAP
TOALTSTACK

DUP
${ 2**5 }
GREATERTHANOREQUAL
1ADD
ROLL
SUB
SWAP
TOALTSTACK

DUP
${ 2**4 }
GREATERTHANOREQUAL
1ADD
ROLL
SUB
SWAP
TOALTSTACK

DUP
${ 2**3 }
GREATERTHANOREQUAL
1ADD
ROLL
SUB
SWAP
TOALTSTACK

DUP
${ 2**2 }
GREATERTHANOREQUAL
1ADD
ROLL
SUB
SWAP
TOALTSTACK

DUP
${ 2**1 }
GREATERTHANOREQUAL
1ADD
ROLL
SUB
SWAP
TOALTSTACK

FROMALTSTACK
FROMALTSTACK
FROMALTSTACK
FROMALTSTACK
FROMALTSTACK
FROMALTSTACK
FROMALTSTACK

`
```
