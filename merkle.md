# Merkle Path Verification 

Requires `OP_CAT`, as in Liquid Script. Tested in [Script Wizard](https://ide.scriptwiz.app/)

```
// Computes the Merkle root given by a path
// It assumes there's nothing but the path on the stack. 

// Inclusion proof
<0xb5bf194a812960ba15b6cd0a0d6d9ce1b2b1ea1883abc551fa84ea4a22f2e66c>
<0xe5007cd9c825ae150dacb80e9e3d210e6e0e85f264f92abed0952404ab1d5808>
<0x0257fc3902505dd27a76956b1ceae3878c51824d1df5025533c5efd274caffc2>
<0xf65cb638eaba0fd71eb0c3d1c657094cf917f3f7eb4a26f38a7ac34e200e6068>
<0xe11f21292c2a7f219e4d89fdf2643de7bfc74a6389bf0d602148f3faaa23182d>
<0x8bf845de9cde12824b810e454309c34c112c0ea409e7318c3fc39a4316e8ebfd>
// The leaf we want to prove inclusion for
<'test_data_chunk_47'>
// The index of the leaf
<47>


OP_TOALTSTACK

OP_SHA256

// Loop begin
OP_DEPTH 
OP_1SUB
OP_IF

    OP_FROMALTSTACK
    OP_DUP
    <2>
    OP_DIV
    OP_TOALTSTACK
    <2>
    OP_MOD

    OP_NOTIF
        OP_SWAP
    OP_ENDIF

    OP_CAT
    OP_SHA256

OP_ENDIF


// Loop begin
OP_DEPTH 
OP_1SUB
OP_IF

    OP_FROMALTSTACK
    OP_DUP
    <2>
    OP_DIV
    OP_TOALTSTACK
    <2>
    OP_MOD

    OP_NOTIF
        OP_SWAP
    OP_ENDIF

    OP_CAT
    OP_SHA256

OP_ENDIF


// Loop begin
OP_DEPTH 
OP_1SUB
OP_IF

    OP_FROMALTSTACK
    OP_DUP
    <2>
    OP_DIV
    OP_TOALTSTACK
    <2>
    OP_MOD

    OP_NOTIF
        OP_SWAP
    OP_ENDIF

    OP_CAT
    OP_SHA256

OP_ENDIF


// Loop begin
OP_DEPTH 
OP_1SUB
OP_IF

    OP_FROMALTSTACK
    OP_DUP
    <2>
    OP_DIV
    OP_TOALTSTACK
    <2>
    OP_MOD

    OP_NOTIF
        OP_SWAP
    OP_ENDIF

    OP_CAT
    OP_SHA256

OP_ENDIF


// Loop begin
OP_DEPTH 
OP_1SUB
OP_IF

    OP_FROMALTSTACK
    OP_DUP
    <2>
    OP_DIV
    OP_TOALTSTACK
    <2>
    OP_MOD

    OP_NOTIF
        OP_SWAP
    OP_ENDIF

    OP_CAT
    OP_SHA256

OP_ENDIF


// Loop begin
OP_DEPTH 
OP_1SUB
OP_IF

    OP_FROMALTSTACK
    OP_DUP
    <2>
    OP_DIV
    OP_TOALTSTACK
    <2>
    OP_MOD

    OP_NOTIF
        OP_SWAP
    OP_ENDIF

    OP_CAT
    OP_SHA256

OP_ENDIF




```


## Ground-Truth in Python 

```python
import hashlib

def hash_data(data):
    if isinstance(data, str):
        data = data.encode()
    return hashlib.sha256(data).digest()

n = 6
data_chunks = ["test_data_chunk_" + str(i) for i in range(2 ** n)]

leaves = [hash_data(str(x)) for x in data_chunks]

index_to_prove = 47

def merkle_tree(leaves, index, path=None):
    if path is None:
        path = []

    if len(leaves) == 1:
        return leaves[0], path
    
    if len(leaves) % 2 != 0:
        leaves.append(leaves[-1])
    
    new_leaves = []
    for i in range(0, len(leaves), 2):
        new_leaves.append(hash_data(leaves[i] + leaves[i+1]))
        if i <= index < i + 2:
            if index % 2 == 0:
                path.append(leaves[i+1].hex())
            else:
                path.append(leaves[i].hex())
            index = i // 2
    
    return merkle_tree(new_leaves, index, path)

root, path = merkle_tree(leaves, index_to_prove)
path.reverse()
print("Root hash:", root.hex())
print("Inclusion proof for index", index_to_prove, ":\n")
for p in path:
    print(f"<0x{p}>")
print(f"<'{data_chunks[index_to_prove]}'>")  # this is the index
print(f"<0x{index_to_prove:02x}>")  # this is the index
```
