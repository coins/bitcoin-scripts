# Bitcoin Covenants

The following redeem script implements a [covenant](https://link.springer.com/chapter/10.1007/978-3-662-53357-4_9):

```
<sig> <pubkey> OP_CODESEPARATOR OP_CHECKSIGVERIFY
```


`<sig>` is just a random signature pair `(r,s)` and `<pubkey>` is the result of [ECDSA public key recovery](https://crypto.stackexchange.com/questions/18105/how-does-recovering-the-public-key-from-an-ecdsa-signature-work) applied to that ‘signature’ and the message the covenant commits to.


This works on today's Bitcoin. No consensus changes are necessary.

## Optimization
When choosing `r = 1` and `s = 0` the public key recovery results in `<pubkey> = -zG` where `z` is basically `H(m)`.

This reduces the script size to almost 40 bytes. (The signature's DER encoding costs an overhead of 6 bytes.)


## Applications
- Timelocked vaults 
- Spacechains
- Many of the [applications of sighash_anyprevout](https://anyprevout.xyz/)
