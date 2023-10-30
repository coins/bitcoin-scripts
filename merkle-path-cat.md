# Merkle Path Verification with OP_CAT

Verify a Merkle inclusion proof in a Merkle tree of depth 4. This implementation requires nothing but OP_CAT. Also no emulation of `OP_DIV` or `OP_MOD` because the index is given by the unlocking script, pre-parsed into a bit string.

```
//
// Inputs 
//

<'sibling-node-hash-3'>
<0>     // index[3]

<'sibling-node-hash-2'>
<0>     // index[2]

<'sibling-node-hash-1'>
<1>     // index[1]

<'sibling-node-hash-0'>
<'leaf-content'>
<1>     // index[0]



//
// Merkle Path Verification
//

OP_OVER
OP_TOALTSTACK

// Initialize our counter for the index
<0>
OP_TOALTSTACK

// First Round
OP_IF
    <8>
    OP_FROMALTSTACK
    OP_ADD
    OP_TOALTSTACK
    OP_SWAP
OP_ENDIF
OP_CAT
OP_HASH160

OP_SWAP

// Second Round
OP_IF
    <4>
    OP_FROMALTSTACK
    OP_ADD
    OP_TOALTSTACK
    OP_SWAP
OP_ENDIF
OP_CAT
OP_HASH160

OP_SWAP

// Third Round
OP_IF
    <2>
    OP_FROMALTSTACK
    OP_ADD
    OP_TOALTSTACK
    OP_SWAP
OP_ENDIF
OP_CAT
OP_HASH160


OP_SWAP

// Fourth Round
OP_IF
    <1>
    OP_FROMALTSTACK
    OP_ADD
    OP_TOALTSTACK
    OP_SWAP
OP_ENDIF
OP_CAT
OP_HASH160


// Ensure the result matches this Merkle root
<0x414253bb057b9c8a209e82d7d4c7a4dcbccf7a95>
OP_EQUALVERIFY


OP_FROMALTSTACK
// Now the index is on the stack

OP_FROMALTSTACK
// Now the leaf content is on the stack
```
