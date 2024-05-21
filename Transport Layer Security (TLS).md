---
creation date: 2021-12-21 14:10
aliases: 
tags: 🖥️
---

TLS provides for secure message exchange over an unsecure channel and operates above the Transport layer and below the Application layer. 
- encapsulates application data like with other PDUs
- Started life as SSL (secure sockets layer).

Three security services that TLS provides: 
1. **encryption** - process of encoding a message so that is can be read by those with an authorized means of decoding the message
2. **authentication** - verify the identity of a particular party in message exchange
3. **integrity** - process to detect whether a message has been interfered with or faked

## Encryption & TLS Handshake
TLS sets up an encrypted connection via the TLS handshake using  
a combination of [symmetric cryptopgrahy](./Cryptography.md#Symmetric%20Cryptography) and [asymmetric cryptography](./Cryptography.md#Asymmetric%20cryptography). 
- The bulk of the message exchange is conducted via symmetric key encryption (since we want both the request AND response to be encrypted two ways), but uses asymmetric key encryption to do the initial symmetric key exchange. 

```ad-tldr

TLS handshake enables client & server to: 
1) agree which version of TLS to be used in establishing connection, 
2) agree on algorithms that will be included in cipher suite, and 
3) enable exchange of symmetric keys for message encryption.

```


**TLS handshake** refers to the process by which the initial secure connection is set up and takes place after the [TCP](./TCP.md) handshake.

high level process:
1. client sends `clienthello` message immediately after [TCP](./TCP.md) `ACK`. Contains info on the latest version of TLS protocols client can use, list of cipher suites available to client (e.g. list of algorithms to use for each task in handshake)
	"This is what I can do."
2. Server receives `clienthello` and responds with `serverhello`. Includes the chosen TLS version protocol, certificate + **public** key, and `serverhellodone` marker. This indicates completion of server handshake duties.
	"We're going to use xyz protocol and ciphers. Here's my certificate with **digital signature** and public key."
3. Client receives `serverhellodone`, and initiates key exchange (depends on algorithm selected from Cipher Suite).
	1. Client encrypts a 'premaster secret' using the server's public key, and sends it to server in `clientkeyexchange` message and a `ChangecipherSpec` message to indicate to start communicating using the symmetric key and a `Finished` flag, indicating client handshake duties are done.
	2. Server and client both generate the same symmetric key based on the 'premaster secret'. 
4. Server sends its own `ChangecipherSpec` and `finished` flag to complete the process. 

![Pasted image 20211221151203.png](./images/Pasted%20image%2020211221151203.png)
```ad-note
There is a protocol called **Datagram Transport Layer Security (DTLS)**, which is based on TLS. This protocol is specifically for use with network connections which use UDP rather than [[TCP]] at the Transport layer.

```

**cipher suite** is a set of ciphers (algorithms) used and agreedupon for a handshake. TLS uses different ciphers for different aspects of establishing and maintaining a secure connection (e.g. performing key exchange, authentication, symmetric key encryption, checking integrity). 

## TLS Authentication
During the handshake, the server sends its certificate with its public key. Client looks at the certificate and digital signature, and if they match, it establishs the server is who they say they are. 

- server creates a *signature* by encrypted some data with it's **private** key and is sent to client with the original, unencrypted data. 
- Client decrypts the data with server's public key. If this works, and the decrypted data matches the original unencrypted data (since they are supposed to be the same pre-encryption)
- if the digital signature can be decrypted with the public key, that means that the servers's private key was used to encrypt it. this assures the client that the server is who they say they are!

## Certificate authorities and Chain of Trust
Server certificates are issued by CAs.
CAs:
- verify that party requesting the certificate is who they say they are (e.g. require a file uploaded to a server that is accessible by domain)
- digitally sign certificates being issued, by encrypting data with CA's private key. (same process as TLS authentication signature)
	- "signature" is CA's public key and encrypted piece of data, and unecnrypted version of data. Signature can be decrypted using CA's public key. 

**Chain of Trust** is the hiearchy of intermediate and root CAs that verify and issue certificates. Root CAs issue certificates for intermediate CAs, which issue certificates for servers. If the private key of one CA level is compromised, the upper level CA can revoke it's certificate and issue a new one.  

## Integrity
Integrity means the message was not altered during transit.

Each PDU for TLS conains a *message authentication code (MAC)*, WHICH IS SIMILAR IN CONCEPT TO THE CHECKSUM FIELDS. 
- MAC field in TLS record provides a means of checking that the message hasn't been altered or tampered with using a hashing algorithm.
- it is the concept of combining the message and secret key to caculate the *digest*, a small amount of data derived from the actual data created using a specific hashing algorithm and pre-agreed (in TLS handshake) hash value.

1. Sender creates a digest of data payload
2. Sender encrypts data payload using symmetric key and ecapsulates into a TLS record, and sends over the transport layer to other party
3. Receiver decrypts the payload using symmetric key. It also creates a digest of the payload using the same algorithm and hash value. If receiver generated digest matches the sender's digest, then integrity is confirmed. 

The message is hashed to produced a digest using MD5 or SHA1, etc.
The MAC is the combination of message + secret key that can create the digest.

## Summary
- HTTP requests and responses are plain text, thus they are insecure
- TLS protocol adds security to HTTP communications
- TLS encryption allows us to encode messages using a combination of symmetric and asymmetric key encryption. 
	- Asymmetric encryption is used to exchange the symmetric keys, in a process called TLS handshake.
- TLS handshake occurs after [TCP](./TCP.md) connection is established but must occur before application data is exchanged
	- involves 2 RTT of latency

- cipher suite is the agreed upon set of algorithms used by client and server during secure message exchange
- TLS authentication means verifying the identity of a participants during message exchange, and is implemented using *digital certificates*
	- certificates are signed by the Certificate Authority, which works on the basis of a *chain of trust*, with Root CAs at the top.
- Certificates are exchanged during the handshake process
- TLS integrity provides a means of checking whether a message has been altered and is implemented using *message authentication code*

Tags: [Programming](Programming.md) - [Networking](./Networking.md) 

Reference: 

Related: 