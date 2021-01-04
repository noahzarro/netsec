# Netsec
## Crypto Refresher
### Language
| Term | Explanation |
| --- | --- |
| Secrecy |keep data hidden from unintended users
|Confidentiality|keep somebody else's data secret
|Privacy|keep data about a person secret, i.e. the link between data and person
|Anonymity|keep identity of protocol participant secret
|Integrity|data is unchanged
|Authentication |make sure data comes from the claimed sender

### Cryptographic Primitives 
#### Stream Ciphers
- Encrypt whole message with key with the same length as message
- An example is ChaCha stream Cipher
- :x: Key must not be reused
- :x:  Alteration of ciphertext will alter corresponding values in plaintext after decryption
#### Block Ciphers
Message is split into blocks, each block is encrypted separately 
##### Electronic Code Book (ECB)
![ECB Pic](assets/ECB.png)
- Split message in blocks, encrypt each block separately, but with same key
- :heavy_check_mark: fast
- :x: Same plaintext always corresponds to same ciphertext
- :x: Adversary may can guess part of plaintext, can decrypt parts of a message if same
ciphertext block occurs
- :x: Adversary can replace blocks with other blocks
##### Cipher Block Chaining (CBC)
![CBC Pic](assets/CBC.png)
- :heavy_check_mark: Semantic security (best guess for single plaintext bit is random)
- :x: Altered ciphertext only influences two blocks (why? :flushed:)
- :x: Not secure for variable-sized messages!

##### Counter Mode (CTR)
![CTR Pic](assets/CTR.png)

- 
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEyNzk2MTE4NDFdfQ==
-->
