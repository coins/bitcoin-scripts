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


### Nullifying Trailing Bits

```js
const N = 8;        // The bit length
const T = N - 6;    // Number of trailing bits to nullify 

// The loop
const nullify_T_bits = [
    'DUP',
    loop( T, i => `
        DUP
        ${ 2 ** (N - 1 - i) }
        GREATERTHANOREQUAL
        ${ i * 2 + 2 }
        ADD
        PICK
        SUB
    `),
    'SUB'
];

// The loop which also drops the lookup table
const nullify_T_bits_drop = [
    'DUP',
    loop( T, i => `
        DUP
        ${ 2 ** (N - 1 - i) }
        GREATERTHANOREQUAL
        2
        ADD
        ROLL
        SUB
        NIP
    `),
    'SUB'
];


[

// Push the lookup table onto the stack
loop( T, i => `
    ${ 2 ** (N - T + i) }
    0
`),

// First input
0b11111110,
nullify_T_bits,
'TOALTSTACK',

// Second input
0b11111100,
nullify_T_bits,
'TOALTSTACK',

// Third input
0b11111000,
nullify_T_bits,
'TOALTSTACK',

// last input
0b11110000,
nullify_T_bits_drop,

'FROMALTSTACK',
'FROMALTSTACK',
'FROMALTSTACK',

]
```



## Split into Bits


```js
const N = 4;

[
    // The input value,
    0b1010,

    // Split into bits,
    loop(N - 1, i => [
        OP_DUP,
        2**(N - 1 - i),
        OP_GREATERTHANOREQUAL,
        OP_TUCK,
        OP_IF,
            2**(N - 1 - i),
            OP_SUB,    
        OP_ENDIF,
    ])
]
```


## Split Nibbles into Bits

```js
const N = 4;

[
    // Lookup tables
    loop(2**N, i => [
        (i & 0b0100) ? 1 : 0,
    ]).reverse(),

    loop(2**N, i => [
        (i & 0b0010) ? 1 : 0,
    ]).reverse(),

    loop(2**N, i => [
        (i & 0b0001) ? 1 : 0,
    ]).reverse(),


    // Input
    0b1101,

    // Split into bits
    OP_DUP,
    OP_2DUP,

    3, OP_ADD, OP_PICK,

    OP_SWAP,
    3 + 16, OP_ADD, OP_PICK,

    OP_2SWAP,
    3 + 16 * 2, OP_ADD, OP_PICK,

    OP_SWAP,
    8, OP_GREATERTHANOREQUAL,

]

```
