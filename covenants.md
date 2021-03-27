# Covenants

```
<sig> <pubkey> OP_CODESEPARATOR OP_CHECKSIGVERIFY
```


`<sig>` is just a random signature pair `(r,s)` and `<pubkey>` is the result of [ECDSA public key recovery](https://crypto.stackexchange.com/questions/18105/how-does-recovering-the-public-key-from-an-ecdsa-signature-work) applied to that ‘signature’ and the message the covenant commits to.
When choosing `r = 1` and `s = 0` the ECDSA key recovery results in `<pubkey> = -zG` where `z` is simply `H(m)`.
