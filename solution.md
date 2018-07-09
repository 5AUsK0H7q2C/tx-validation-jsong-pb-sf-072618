
# Signature Validation


```python
# Validation example

from io import BytesIO

from ecc import S256Point, Signature
from helper import double_sha256
from tx import Tx

modified_tx = bytes.fromhex('0100000001813f79011acb80925dfe69b3def355fe914bd1d96a3f5f71bf8303c6a989c7d1000000001976a914a802fc56c704ce87c42d7c92eb75e7896bdc41ae88acfeffffff02a135ef01000000001976a914bc3b654dca7e56b04dca18f2566cdaf02e8d9ada88ac99c39800000000001976a9141c4bc762dd5423e332166702cb75f40df79fea1288ac1943060001000000')
h = double_sha256(modified_tx)
z = int.from_bytes(h, 'big')

stream = BytesIO(bytes.fromhex('0100000001813f79011acb80925dfe69b3def355fe914bd1d96a3f5f71bf8303c6a989c7d1000000006b483045022100ed81ff192e75a3fd2304004dcadb746fa5e24c5031ccfcf21320b0277457c98f02207a986d955c6e0cb35d446a89d3f56100f4d7f67801c31967743a9c8e10615bed01210349fc4e631e3624a545de3f89f5d8684c7b8138bd94bdd531d2e213bf016b278afeffffff02a135ef01000000001976a914bc3b654dca7e56b04dca18f2566cdaf02e8d9ada88ac99c39800000000001976a9141c4bc762dd5423e332166702cb75f40df79fea1288ac19430600'))
transaction = Tx.parse(stream)
tx_in = transaction.tx_ins[0]
sig = Signature.parse(tx_in.der_signature())
point = S256Point.parse(tx_in.sec_pubkey())
print(point.verify(z, sig))
```

### Try it

#### Validate this signature

```
sec = 0349fc4e631e3624a545de3f89f5d8684c7b8138bd94bdd531d2e213bf016b278a
der = 3045022100ed81ff192e75a3fd2304004dcadb746fa5e24c5031ccfcf21320b0277457c98f02207a986d955c6e0cb35d446a89d3f56100f4d7f67801c31967743a9c8e10615bed
z = 27e0c5994dec7824e56dec6b2fcb342eb7cdb0d0957c2fce9882f715e85d81a6
```

Remember:

* `sec` is the serialization of the Public Key
* `der` is the serialization of the Signature
* `z` is the hash that you're verifying the signature against


```python
from ecc import S256Point, Signature

sec = bytes.fromhex('0349fc4e631e3624a545de3f89f5d8684c7b8138bd94bdd531d2e213bf016b278a')
der = bytes.fromhex('3045022100ed81ff192e75a3fd2304004dcadb746fa5e24c5031ccfcf21320b0277457c98f02207a986d955c6e0cb35d446a89d3f56100f4d7f67801c31967743a9c8e10615bed')
z = 0x27e0c5994dec7824e56dec6b2fcb342eb7cdb0d0957c2fce9882f715e85d81a6

# use S256Point.parse on the sec to get the public point
point = S256Point.parse(sec)
# use Signature.parse on the der to get the signature
sig = Signature.parse(der)
# use S256Point.verify method
print(point.verify(z, sig))
```

### Try it

#### Validate the signature for the first input in this transaction.
```
01000000012f5ab4d2666744a44864a63162060c2ae36ab0a2375b1c2b6b43077ed5dcbed6000000006a473044022034177d53fcb8e8cba62432c5f6cc3d11c16df1db0bce20b874cfc61128b529e1022040c2681a2845f5eb0c46adb89585604f7bf8397b82db3517afb63f8e3d609c990121035e8b10b675477614809f3dde7fd0e33fb898af6d86f51a65a54c838fddd417a5feffffff02c5872e00000000001976a91441b835c78fb1406305727d8925ff315d90f9bbc588acae2e1700000000001976a914c300e84d277c6c7bcf17190ebc4e7744609f8b0c88ac31470600
```


```python
from io import BytesIO
from ecc import S256Point, Signature
from tx import Tx

hex_tx = '01000000012f5ab4d2666744a44864a63162060c2ae36ab0a2375b1c2b6b43077ed5dcbed6000000006a473044022034177d53fcb8e8cba62432c5f6cc3d11c16df1db0bce20b874cfc61128b529e1022040c2681a2845f5eb0c46adb89585604f7bf8397b82db3517afb63f8e3d609c990121035e8b10b675477614809f3dde7fd0e33fb898af6d86f51a65a54c838fddd417a5feffffff02c5872e00000000001976a91441b835c78fb1406305727d8925ff315d90f9bbc588acae2e1700000000001976a914c300e84d277c6c7bcf17190ebc4e7744609f8b0c88ac31470600'
stream = BytesIO(bytes.fromhex(hex_tx))
index = 0

# parse the transaction using Tx.Parse
t = Tx.parse(stream)
# grab the input at index
tx_in = t.tx_ins[index]
# use the input's der_signature, sec_pubkey and hash_type methods to get data
der = tx_in.der_signature()
sec = tx_in.sec_pubkey()
hash_type = tx_in.hash_type()
# parse the signature using Signature.parse
sig = Signature.parse(der)
# parse the sec pubkey using S256Point.parse
point = S256Point.parse(sec)
# use the sig_hash method on index and hash_type to get z
z = t.sig_hash(index, hash_type)
# use the pubkey to verify the signature (z, sig)
print(point.verify(z, sig))
```
