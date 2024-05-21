Key Derivation Functions
- concept often related to cryptographic hashes, as KDFs are used for producing fixed-length output keys as inputs to cryptographic algorithms.
- purposefully slow to slow down brute force attacks
Applications:
- produce keys for symmetric cryptography (below)
- storing login in credentials
	- instead of storing plaintext passwords, use a KDF to generate a key based off a passphrase and check if the key generated matches
```
salt = random() // randomly generated, stored for each user
key = KDF(passphrase + salt) // recompute key each time user logs in given the entered passphrase and stored salt
```

#### Salt
Salt is a randomly generated string that makes it harder to use [Rainbow Tables](https://en.wikipedia.org/wiki/Rainbow_table)to map back hashed values to a plaintext character. The salt adds more randomness that makes it even harder to "reverse engineer" a hash.

### Symmetric Cryptography
Modern cryptographic has evolved over time, but still use the concept of a shared encryption key, aka **symmetric key encryption**, to decipher messages.
- both sender and receiver have access to the key. 
- This isn't feasible for [TLS handshake](./Transport%20Layer%20Security%20(TLS).md#TLS%20Handshake)over the internet, because need secure way to share the key

```
keygen() => key
encrypt(plaintext: array<byte>, key) => ciphertext: array<byte>
decrypt(ciphertext, key) => plaintext
```
- AES - Used to encrypting files for storage in untrusted cloud service.
```
key = KDF(passphrase)
encrypt(file, key)
```

### Asymmetric cryptography
**asymmetric key encryption**, aka public key encryption, uses a *pair of keys*: a public key (to encrypt) and private key (to decrypt).
- messages encrypted with the public key can only be decrypted with the private key. 
- one pair of public/private keys works for messages going *one way*
```
public key, private key = keygen()
ciphertext = encrypt(plaintext, public key)
plaintext = decrypt(ciphertext, private key)

signature <bytes> = sign(message, private key)
isValid <bool> = verify(message, signature, public key)
```
Example:
Alice generates a public key and private key to receive encrypted messages. Bob uses Alice's public key to encrypt a message and send it to Alice. Alice can decrypt Bob's message using her private key. 

> [!NOTE] 🧠 Cryptography mental model
> - symmetric cryptographic is like locking a door. Anyone with the key can lock and unlock it.
> - asymmetric cryptography is like locking with a padlock with a key. You can give anyone the unlocked lock (public key) and they could put a message in a box and lock it with the padlock. After that, only you can open the lock with the private key.
> 
