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

#### Generic n-bit Template

```js
const N = 30; // The bit length

[

// Push the lookup table onto the stack
loop( N - 1, i => `
    ${ 2 ** (i + 1) }
    0
`),

// The input value
// We use a bit string for debugging
// It's a single item on the stack
0b0001000000000001,

// The loop
loop( N - 1, i => `
    DUP
    ${ 2 ** (N - 1 - i) }
    GREATERTHANOREQUAL
    1ADD
    ROLL
    SUB
    SWAP
    TOALTSTACK
`),

// Read the result from the altstack
loop( N - 1, _ => `FROMALTSTACK` ),

]
```


## Nullify Leading Bits

```js
const N = 16;   // The bit length
const T = 8;    // Number of leading bits to nullify 

[

// Push the lookup table onto the stack
loop( T, i => `
    ${ 2 ** (N - T + i) }
    0
`),

// The input value
// We use a bit string for debugging
// It's a single item on the stack
0b1010110011111111,

// The loop
loop( T, i => `
    DUP
    ${ 2 ** (N - 1 - i) }
    GREATERTHANOREQUAL
    1ADD
    ROLL
    SUB
    NIP
`),

]
```

### Reusing the Lookup Table

```js
const N = 16;   // The bit length
const T = 8;    // Number of leading bits to nullify 

// The loop
const nullify_T_bits = loop( T, i => `
    DUP
    ${ 2 ** (N - 1 - i) }
    GREATERTHANOREQUAL
    ${ i * 2 + 1 }
    ADD
    PICK
    SUB
`);

// The loop which also drops the lookup table
const nullify_T_bits_drop = loop( T, i => `
    DUP
    ${ 2 ** (N - 1 - i) }
    GREATERTHANOREQUAL
    1ADD
    ROLL
    SUB
    NIP
`);


[

// Push the lookup table onto the stack
loop( T, i => `
    ${ 2 ** (N - T + i) }
    0
`),

// First input
0b1010110011111110,
nullify_T_bits,
'TOALTSTACK',

// Second input
0b1010110011111100,
nullify_T_bits,
'TOALTSTACK',

// Third input
0b1010110011111000,
nullify_T_bits,
'TOALTSTACK',

// Fourth input
0b1010110011110000,
nullify_T_bits_drop,

'FROMALTSTACK',
'FROMALTSTACK',
'FROMALTSTACK',

]
```
