# Raw Bitcoin Transactions 

## P2PK
https://blockstream.info/block/00000000d1145790a8694403d4063f323d499e655c83426834d4ce2f8dd4a2ee
### Raw
https://blockstream.info/tx/f4184fc596403b9d638783cf57adfe4c75c605f6356fbc91338530e9831e9e16
```
0100000001c997a5e56e104102fa209c6a852dd90660a20b2d9c352423edce25857fcd3704000000004847304402204e45e16932b8af514961a1d3a1a25fdf3f4f7732e9d624c6c61548ab5fb8cd410220181522ec8eca07de4860a4acdd12909d831cc56cbbac4622082221a8768d1d0901ffffffff0200ca9a3b00000000434104ae1a62fe09c5f51b13905f07f06b99a2f7159b2225f374cd378d71302fa28414e7aab37397f554a7df5f142c21c1b7303b8a0626f1baded5c72a704f7e6cd84cac00286bee0000000043410411db93e1dcdb8a016b49840f8c53bc1eb68a382e97b1482ecad7b148a6909a5cb2e0eaddfb84ccf9744464f82e160bfa9b8b64f9d4c03f999b8643f656b412a3ac00000000
```
### Parsed
```
version: 01000000
inputsCount: 01
input #1:
	txid: c997a5e56e104102fa209c6a852dd90660a20b2d9c352423edce25857fcd3704
	vout: 00000000
	scriptSigSize: 48
	scriptSig:
		OP_PUSHBYTES_71: 47
		data: 304402204e45e16932b8af514961a1d3a1a25fdf3f4f7732e9d624c6c61548ab5fb8cd410220181522ec8eca07de4860a4acdd12909d831cc56cbbac4622082221a8768d1d0901
	sequence: ffffffff
outputsCount: 02
output #1: 
	value: 00ca9a3b00000000
	scriptPubKeySize: 43
	scriptPubKey:
		OP_PUSHBYTES_65:  41
		data: 04ae1a62fe09c5f51b13905f07f06b99a2f7159b2225f374cd378d71302fa28414e7aab37397f554a7df5f142c21c1b7303b8a0626f1baded5c72a704f7e6cd84c
		OP_CHECKSIG: ac
output #2:
	value: 00286bee00000000
	scriptPubKeySize: 43
	scriptPubKey:
		OP_PUSHBYTES_65: 41
		data: 0411db93e1dcdb8a016b49840f8c53bc1eb68a382e97b1482ecad7b148a6909a5cb2e0eaddfb84ccf9744464f82e160bfa9b8b64f9d4c03f999b8643f656b412a3
		OP_CHECKSIG: ac
locktime: 00000000
```
## P2PKH

### Raw
https://blockstream.info/tx/6f7cf9580f1c2dfb3c4d5d043cdbb128c640e3f20161245aa7372e9666168516
```
0100000002f60b5e96f09422354ab150b0e506c4bffedaf20216d30059cc5a3061b4c83dff000000004a493046022100e26d9ff76a07d68369e5782be3f8532d25ecc8add58ee256da6c550b52e8006b022100b4431f5a9a4dcb51cbdcaae935218c0ae4cfc8aa903fe4e5bac4c208290b7d5d01fffffffff7272ef43189f5553c2baea50f59cde99b3220fd518884d932016d055895b62d000000004a493046022100a2ab7cdc5b67aca032899ea1b262f6e8181060f5a34ee667a82dac9c7b7db4c3022100911bc945c4b435df8227466433e56899fbb65833e4853683ecaa12ee840d16bf01ffffffff0100e40b54020000001976a91412ab8dc588ca9d5787dde7eb29569da63c3a238c88ac00000000
```
### Parsed
```
version: 01000000
inputsCount: 02
input #1:
	txid: f60b5e96f09422354ab150b0e506c4bffedaf20216d30059cc5a3061b4c83dff
	vout: 00000000
	scriptSigSize: 4a
	scriptSig:
		OP_PUSHBYTES_73: 49
		data: 3046022100e26d9ff76a07d68369e5782be3f8532d25ecc8add58ee256da6c550b52e8006b022100b4431f5a9a4dcb51cbdcaae935218c0ae4cfc8aa903fe4e5bac4c208290b7d5d01
	sequence: ffffffff

input #2:
	txid: f7272ef43189f5553c2baea50f59cde99b3220fd518884d932016d055895b62d
	vout: 00000000
	scriptSigSize: 4a
	scriptSig:
		OP_PUSHBYTES_73: 49
		data: 3046022100a2ab7cdc5b67aca032899ea1b262f6e8181060f5a34ee667a82dac9c7b7db4c3022100911bc945c4b435df8227466433e56899fbb65833e4853683ecaa12ee840d16bf01
	sequence: ffffffff

outputsCount: 01
output #1:
	value: 00e40b5402000000
	scriptPubKeySize: 19
	scriptPubKey:
		OP_DUP: 76
		OP_HASH160: a9
		data: 1412ab8dc588ca9d5787dde7eb29569da63c3a238c
		OP_EQUALVERIFY: 88
		OP_CHECKSIG: ac

locktime: 00000000
```
## P2SH
- Defined in [BIP013](https://en.bitcoinwiki.org/wiki/BIP_0013)
- Address format: [base58check encoding](https://en.bitcoin.it/wiki/Base58Check_encoding)
- Example: `3P14159f73E4gFr7JterCCQh9QjiTjiZrG`
### Raw
https://blockstream.info/tx/28ed0cab5a3438f79b0cbeae47616feb4f0589cbadbddb1bb3061c1c730463c7
```
01000000018e7068fcbad6059ef5add9615888606bf0c20b7be076c8e7f969ad8b0e056ecb01000000fd1c0100493046022100d14e9de1dd970888c407426d88051e84af0b1e26227eacd186e4abb465a806e8022100ebeebcc038502aa23943bf3ed0936f7a19025a6bb3e281156469403407c9e4640147304402207a7ee9d352c611d99d7813604121b832a04a744a100814b8b647a1ee841070b4022004134b404685e88be0c8a630c09a81d97da00f5b2c9dac983109b5fb2f222a6c014c87524104e89a79651522201d756f14b1874ae49139cc984e5782afeca30ffe84e5e6b2cfadcfe9875c490c8a1a05a4debd715dd57471af8886ab5dfbb3959d97f087f77a410455cf4a3ab68a011b18cb0a86aae2b8e9cad6c6355476de05247c57a9632d127084ac7630ad89893b43c486c5a9f7ec6158fb0feb708fa9255d5c4d44bc0858f852aeffffffff0100e1f505000000001976a914d294ab0b27d2cdd04a43f0a8c6105222a444e93b88ac00000000
```
### Parsed
```
version: 01000000
inputsCount: 01
input #1:
	txid: 8e7068fcbad6059ef5add9615888606bf0c20b7be076c8e7f969ad8b0e056ecb
	vout: 01000000
	scriptSigSize: fd1c01
	scriptSig:
		OP_0: 00
		OP_PUSHBYTES_73: 49
		data: 3046022100d14e9de1dd970888c407426d88051e84af0b1e26227eacd186e4abb465a806e8022100ebeebcc038502aa23943bf3ed0936f7a19025a6bb3e281156469403407c9e46401
		OP_PUSHBYTES_71: 47
		data: 304402207a7ee9d352c611d99d7813604121b832a04a744a100814b8b647a1ee841070b4022004134b404685e88be0c8a630c09a81d97da00f5b2c9dac983109b5fb2f222a6c01
		OP_PUSHDATA1: 4c87
		data: 524104e89a79651522201d756f14b1874ae49139cc984e5782afeca30ffe84e5e6b2cfadcfe9875c490c8a1a05a4debd715dd57471af8886ab5dfbb3959d97f087f77a410455cf4a3ab68a011b18cb0a86aae2b8e9cad6c6355476de05247c57a9632d127084ac7630ad89893b43c486c5a9f7ec6158fb0feb708fa9255d5c4d44bc0858f852ae
	
	sequence: ffffffff
      
outputsCount: 01
output #1:
      value: 00e1f50500000000
      scriptPubKeySize: 19
      scriptPubKey:
	  OP_DUP: 76
	  OP_HASH160: a9
	  OP_PUSHBYTES_20: 14
	  data: d294ab0b27d2cdd04a43f0a8c6105222a444e93b
	  OP_EQUALVERIFY: 88
	  OP_CHECKSIG: ac
      
locktime: 00000000
```
### Previous Output
```
output #2:
	value: 15cd5b0700000000
	scriptPubKeySize: 17
	scriptPubKey:
		OP_HASH160: a9
		OP_PUSHBYTES_20: 14
		data: 23f2ad603145e5ef55dec9ae4cd38a0bf37f5d98
		OP_EQUAL: 87
```
## SegWit Transaction (P2WPKH)
- Defined in [BIP 0142](https://en.bitcoin.it/wiki/BIP_0142)
- Address format: [Bech32](https://en.bitcoin.it/wiki/Bech32)
- Example: `bc1qc7slrfxkknqcq2jevvvkdgvrt8080852dfjewde450xdlk4ugp7szw5tk9`
### Raw

https://blockstream.info/testnet/tx/45c1edba17b831b919f9539d2d3d2e7107da7b661673e10ffb65446fc1781335?expand

```
02000000000101193c8e971011dad3a886b8f88b05614ce38930b2b00ae9e34fa9b3e0371093990100000000ffffffff0327010000000000001600145d6787fb4d4bf624d7d30edc1832253f80ed45aa6eb1000000000000160014519a06346de75727fb060af1fc0615922efb2e050000000000000000066a04000004b002473044022012d52c4451c549cf14dec197a1b3546e8fdbe87239ed82cfa98d0f43358c101a0220046405b23a5e3e5a3765a11ace75ea1498ef28ac2bdf5adf2160498521914f6c012103f2a2ae66e6f49f62f011be8c34e498d3b615b06990590e3f5d68e866ecec219500000000
```

### Parsed

```
version: 02000000
flag: 0001
inputs count: 01
	txid: 193c8e971011dad3a886b8f88b05614ce38930b2b00ae9e34fa9b3e037109399
	vout: 01000000
	scriptSigSize: 00
	scriptSig:  
	sequence: ffffffff

outputs count: 03
	output #1:
		value: 2701000000000000 
		scriptPubKeySize: 16 
		scriptPubKey: 
			OP_0: 00
			OP_PUSHBYTES_20: 14
			hash160: 5d6787fb4d4bf624d7d30edc1832253f80ed45aa
	output #2:
		value: 6eb1000000000000 
		scriptPubKeySize: 16
		scriptPubKey: 
			OP_0: 00
			OP_PUSHBYTES_20: 14
			hash160: 519a06346de75727fb060af1fc0615922efb2e05
	output #3:
		value: 0000000000000000
		scriptPubKeySize: 06
		scriptPubKey: 
			OP_RETURN: 6a
			OP_PUSHBYTES_4: 04
			Data: 000004b0

witnesses (P2WPKH):
	versionByte(?): 02 
	signature:
		OP_PUSH_BYTES_71: 47 
		data: (DER encoding)
			headerByte: 30
			length: 44
			integerIndicator: 02
			rValueLength: 20
			rValue: 12d52c4451c549cf14dec197a1b3546e8fdbe87239ed82cfa98d0f43358c101a
			integerIndicator: 02
			sValueLength: 20
			sValue: 046405b23a5e3e5a3765a11ace75ea1498ef28ac2bdf5adf2160498521914f6c
			sighashFlag: 01

	pubkey: 
		OP_PUSH_BYTES_33: 21 
		data: 
			yCoordFlag: 03
        		xCoord: f2a2ae66e6f49f62f011be8c34e498d3b615b06990590e3f5d68e866ecec2195
locktime: 00000000
```
## P2WSH ( Native SegWit )
### Raw
https://blockstream.info/tx/c53b99c9fdba60fd47c6026177d3f6e1ed6d3abde8f433364619aa7d437dad26
```
010000000001012198e5bc0860a4fcf420e2b909fce47e746357457b060c63571e12bd84fec4a70100000000ffffffff0220120a000000000017a91417b9a9afddaae527d25788bce2202563d4ab0d058784ea110000000000220020701a8d401c84fb13e6baf169d59684e17abd9fa216c8cc5b9fc63d622ff8c58d0400473044022014cd600863ad3c9f6802383fe814a693a77144117cf7694f63b558b8c02d801c02201c3ad9901f659742668caf770f3d7f89a3633f9ccd2bfdd6a7c6f7529fe7b43101473044022047e4ad9788da6b764e723dd71d9fe07a217e48871129a672ef2788eb0d331a7a02206107b10c23e8720df9f1e5f609c471726066c5b72c56ee774615498f8fe62e8d016952210375e00eb72e29da82b89367947f29ef34afb75e8654f6ea368e0acdfd92976b7c2103a1b26313f430c4b15bb1fdce663207659d8cac749a0e53d70eff01874496feff2103c96d495bfdd5ba4145e3e046fee45e84a8a48ad05bd8dbb395c011a32cf9f88053ae00000000
```

### Parsed
```
version: 01000000
flag: 0001
inputsCount: 01
input #1:
	txid: 2198e5bc0860a4fcf420e2b909fce47e746357457b060c63571e12bd84fec4a7
	vout: 01000000
	scriptSigSize: 00
	scriptSig:
	sequence: ffffffff

outputsCount: 02
output #1:
	value: 20120a0000000000
	scriptPubKeySize: 17
	scriptPubKey:
		OP_HASH160: a9
		OP_PUSHBYTES_20: 1417b9a9afddaae527d25788bce2202563d4ab0d05
		OP_EQUAL: 87

output #2:
	value: 84ea110000000000
	scriptPubKeySize: 22
	scriptPubKey:
		OP_0: 00
		OP_PUSHBYTES_32: 20
		data: 701a8d401c84fb13e6baf169d59684e17abd9fa216c8cc5b9fc63d622ff8c58d
witness:
	version (?): 04
	OP_0: 00
	OP_PUSHBYTES_71: 47
	data: 3044022014cd600863ad3c9f6802383fe814a693a77144117cf7694f63b558b8c02d801c02201c3ad9901f659742668caf770f3d7f89a3633f9ccd2bfdd6a7c6f7529fe7b43101
	OP_PUSHBYTES_71: 47
	3044022047e4ad9788da6b764e723dd71d9fe07a217e48871129a672ef2788eb0d331a7a02206107b10c23e8720df9f1e5f609c471726066c5b72c56ee774615498f8fe62e8d01
	pubkeySize: 69
	pubkey:
		OP_PUSHNUM_2: 52
		OP_PUSHBYTES_33: 21
		data: 0375e00eb72e29da82b89367947f29ef34afb75e8654f6ea368e0acdfd92976b7c
		OP_PUSHBYTES_33: 21
		data: 03a1b26313f430c4b15bb1fdce663207659d8cac749a0e53d70eff01874496feff
		OP_PUSHBYTES_33: 21
		data: 03c96d495bfdd5ba4145e3e046fee45e84a8a48ad05bd8dbb395c011a32cf9f880
		OP_PUSHNUM_3: 53
		OP_CHECKMULTISIG: ae

locktime: 00000000
```

## P2SH ( Nested SegWit )
### Raw
https://blockstream.info/tx/759cf8abb043e944cbcce5ef907469714e5d0b6fe45af1867d459713cee4d524
```
020000000001019f39fcd4984a611176fcfed6e6c7dd6a21b46658bd8b2f5de5b051322ec54df11000000017160014db71e1254925b4849296bc9b82cb5c28a0182c8dfdffffff02c6c710000000000017a9140cbc7b7e9d9e38922ecbd51516c70a38c605a24d87f6de3d000000000017a914e41de0ac1038e8fdec43e71fa523629b6c1b5a63870247304402203438bab535ce72eaffd00700a3fb1559c965afd3943762fe1c417c0e9454f6ba0220605f3f846a41981bfa61f7f7e9785f26d1d26a27ee9d99c27e8443f20a334c640121038d9065a072ae52d568b9c8b0bfb8a364f8fb927957ad929bc99a8eb9a451a71000000000
```
### Parsed
```
version: 02000000
flag: 0001
inputsCount: 01
input #1:
	txid: 9f39fcd4984a611176fcfed6e6c7dd6a21b46658bd8b2f5de5b051322ec54df1
	vout: 10000000
	scriptSigSize: 17
	scriptSig:
		OP_PUSHBYTES_22: 16
		data: 0014db71e1254925b4849296bc9b82cb5c28a0182c8d
	sequence: fdffffff

outputsCount: 02
output #1:
	value: c6c7100000000000
	scriptPubKeySize: 17
	scriptPubKey:
		OP_HASH160: a9
		OP_PUSHBYTES_20: 14
		data: 0cbc7b7e9d9e38922ecbd51516c70a38c605a24d
		OP_EQUAL: 87

output #2:
	value: f6de3d0000000000
	scriptPubKeySize: 17
	scriptPubKey:
		OP_HASH160: a9
		OP_PUSHBYTES_20: 14
		data: e41de0ac1038e8fdec43e71fa523629b6c1b5a63
		OP_EQUAL: 87

witnesses (P2SH):  
	versionByte(?): 02 
	signature:
		OP_PUSH_BYTES_71: 47 
		data: (DER encoding)
			headerByte: 30
			length: 44
			integerIndicator: 02
			rValueLength: 20
			rValue: 3438bab535ce72eaffd00700a3fb1559c965afd3943762fe1c417c0e9454f6ba
			integerIndicator: 02
			sValueLength: 20
			sValue: 605f3f846a41981bfa61f7f7e9785f26d1d26a27ee9d99c27e8443f20a334c64
			sighashFlag: 01

	pubkey: 
		OP_PUSH_BYTES_33: 21 
		data: 
			yCoordFlag: 03
        		xCoord: 8d9065a072ae52d568b9c8b0bfb8a364f8fb927957ad929bc99a8eb9a451a710

locktime: 00000000
```
## P2TR (Taproot Key Spent)
- Defined in [BIP 0350](https://en.bitcoin.it/wiki/BIP_0350)
https://blockstream.info/api/tx/33e794d097969002ee05d336686fc03c9e15a597c1b9827669460fac98799036/hex
- Defined in [BIP 0341](https://github.com/bitcoin/bips/blob/master/bip-0341.mediawiki)
- Address format: [Bech32m](https://github.com/bitcoin/bips/blob/master/bip-0350.mediawiki)
- Example: `bc1pxwww0ct9ue7e8tdnlmug5m2tamfn7q06sahstg39ys4c9f3340qqxrdu9k`
```
01000000000101d1f1c1f8cdf6759167b90f52c9ad358a369f95284e841d7a2536cef31c0549580100000000fdffffff020000000000000000316a2f49206c696b65205363686e6f7272207369677320616e6420492063616e6e6f74206c69652e204062697462756734329e06010000000000225120a37c3903c8d0db6512e2b40b0dffa05e5a3ab73603ce8c9c4b7771e5412328f90140a60c383f71bac0ec919b1d7dbc3eb72dd56e7aa99583615564f9f99b8ae4e837b758773a5b2e4c51348854c8389f008e05029db7f464a5ff2e01d5e6e626174affd30a00
```

```
version: 01000000
flag: 0001
inputsCount: 01
input #1:
	txid: d1f1c1f8cdf6759167b90f52c9ad358a369f95284e841d7a2536cef31c054958
	vout: 01000000
	scriptSigSize: 00
	sequence: fdffffff

outputsCount: 02 
output #1: 
	value: 0000000000000000
	scriptPubKeySize: 31
	scriptPubKey: 
		OP_RETURN: 6a
		OP_PUSHBYTES_47: 2f
		Data: 49206c696b65205363686e6f7272207369677320616e6420492063616e6e6f74206c69652e20406269746275673432
output #2:
	value: 9e06010000000000
	scriptPubKeySize: 22
	scriptPubKey: 
		OP_1: 51
		OP_PUSHBYTES_32: 20
		Data (public key): a37c3903c8d0db6512e2b40b0dffa05e5a3ab73603ce8c9c4b7771e5412328f9
	
witness:	
	versionByte: 01
	PUSH_BYTES_64: 40
	Data: (signature) 
		R: a60c383f71bac0ec919b1d7dbc3eb72dd56e7aa99583615564f9f99b8ae4e837
		s: b758773a5b2e4c51348854c8389f008e05029db7f464a5ff2e01d5e6e626174a
locktime: ffd30a00 ( = block height 709631 )
```

## P2TR (Taproot Script Path Spend)
https://blockstream.info/tx/dda4cb9290415952d93f52518df978d45ae1710e60e711e6786926e99d305ef9
### Raw
```
0200000000010207d9eae5eaf8d267f7060d9e609f15c55f8de7cbb5585dae93ef6210d8b28c6a0100000000feffffff07d9eae5eaf8d267f7060d9e609f15c55f8de7cbb5585dae93ef6210d8b28c6a0000000000feffffff023825000000000000160014f27d573e60371742343db4a711c3e2d6194c25cd301b0f00000000002251203d3e6d002d642acf8653bcf26b81dbe656df5bf88b6d54221b2606bc78f3d4e6024730440220273019dd87b3476a1db05f35908ec8dfbaa4eba4cf65884023ef13dbef5af93002201f5df87913371341c117f2591eb895765466aeeba3debb29e000cd1914fe0eb1012103ea214d99f0149c3441e47aa3bc401072ff6adc5e4d5c0102d3818bbed565bf600341e6e81bea57db6bed922afbc5fab65c61f546b6f467b8c3570b5478ba21851e57b00bef791cd06d58afebb883770d956afaf2a9a796fb594a85d124b6837a4c71012220dcd9a3fdad900e39ff367823f5d683135238d04425ac8dce19d0220e5791c700ac21c150929b74c1a04954b78b4b6035e97a5e078a5a0f28ec96d547bfee9ace803ac0b7680000
```
### Parsed
```
version: 02000000
flags: 0001
inputsCount: 02
input #1:
	txid: 07d9eae5eaf8d267f7060d9e609f15c55f8de7cbb5585dae93ef6210d8b28c6a
	vout: 01000000
	scriptSigSize: 00
	sequence: feffffff

input #2:
	txid: 07d9eae5eaf8d267f7060d9e609f15c55f8de7cbb5585dae93ef6210d8b28c6a
	vout: 00000000
	scriptSigSize: 00
	sequence: feffffff
	
outputsCount: 02
output #1:
	value: 3825000000000000
	scriptPubKeySize: 16
	scriptPubKey:
		OP_0: 00
		OP_PUSHBYTES_20: 14
		data: f27d573e60371742343db4a711c3e2d6194c25cd

output #2:
	value: 301b0f0000000000
	scriptPubKeySize: 22
	scriptPubKey:
		OP_1: 51
		OP_PUSHBYTES_32: 20
		data: 3d3e6d002d642acf8653bcf26b81dbe656df5bf88b6d54221b2606bc78f3d4e6
		
		
witness:	
	versionByte: 02
	OP_PUSHBYTES_71: 47
	signature:
		data: (DER encoding)
			headerByte: 30
			length: 44
			integerIndicator: 02
			rValueLength: 20
			rValue:  273019dd87b3476a1db05f35908ec8dfbaa4eba4cf65884023ef13dbef5af930
			integerIndicator: 02
			sValueLength: 20
			sValue: 1f5df87913371341c117f2591eb895765466aeeba3debb29e000cd1914fe0eb1
			sighashFlag: 01
	pubkey:
		OP_PUSHBYTES_33: 21
		data:
			yCoordFlag: 03
			xCoord: ea214d99f0149c3441e47aa3bc401072ff6adc5e4d5c0102d3818bbed565bf60
			OP_PUSHBYTES_3: 03
			data: 41e6e8
			OP_PUSHBYTES_27: 1b
			ea57db6bed922afbc5fab65c61f546b6f467b8c3570b5478ba2185
			OP_PUSHBYTES_30: 1e
			data: 57b00bef791cd06d58afebb883770d956afaf2a9a796fb594a85d124b683
			OP_ROLL: 7a
			OP_PUSHDATA1: 4c71
			data: 012220dcd9a3fdad900e39ff367823f5d683135238d04425ac8dce19d0220e5791c700ac21c150929b74c1a04954b78b4b6035e97a5e078a5a0f28ec96d547bfee9ace803ac0
locktime: b7680000
```


## References 
- https://en.bitcoin.it/wiki/Transaction
- https://en.bitcoin.it/wiki/BIP_0141#Examples
- https://en.bitcoin.it/wiki/Script
- https://bitcoin.stackexchange.com/questions/92680/what-are-the-der-signature-and-sec-format
