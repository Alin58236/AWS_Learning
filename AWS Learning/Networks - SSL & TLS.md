Secure Socket Layer (SSL) and Transport Layer Security (TLS) are two cryptographic protocols used to provide secure communication over the internet. SSL was developed by Netscape in the mid-1990s and TLS is its successor. These protocols are used to secure web traffic, email, instant messaging, and other types of internet traffic. SSL and TLS use a combination of symmetric and asymmetric encryption to encrypt data, ensuring that information transmitted over the internet is secure and cannot be intercepted by unauthorized parties.

- TLS is just a newer and more secure version of SSL. 
- Used for privacy and Data Integrity between client & server.
- Privacy - communications are encrypted
- Asymmetric and the symmetric
- Identity verified (server or client/server)
- Reliable connection - protect against alteration

Basically we use SSL and TLS to verify the identity of a server (e.g. when we enter netflix.com it is actually netflix.com). In order to establish a secure connection between the client and the server we need to go through 3 stages:
### 1.  Agree on Cipher Suites

A Cipher Suite is a set of protocols used by TLS that includes:
- a key exchange algorithm
- a bulk encryption algorithm
- a message authentication code algorithm (MAC)

At this stage we have the client and the server that established a TCP connection. The client wants to send packets of data to the server in a secure manner, but in order to do that, it has to initiate the secure protocol. This is done by the client sending a "Client HELLO", which is basically a message saying "Hi, I'm the client and I use {SSL/TLS Version}, {list of supported cipher suites}, {Session ID}, {Extensions}". 

If the server has compatible SSL version and knows how to communicate using the client cipher suites, it sends back a response called a "Server HELLO". This contains the same information sent by the client, plus a Server Certificate that includes the server's public key and DNS Name.

This public key is used so that the client can encrypt the packets of data with it, and only the server can decrypt it using it's private key (asymmetric encryption - computationally heavy).

![[Pasted image 20240208214315.png]]

### 2. Authentication

In this step we have the server certificate but we want to make sure that it is authentic. We do that by verifying it against a CA (Certificate Authority) that our operating system trusts.

Basically the client makes sure that the certificate is valid (date, and hasn't been revoked) and that the DNS name matches the name/names on the cert. The next step is encrypting some random data that the client sends to the Server so it will decrypt the data using it's private key. This is done in order to make sure that the server has the corresponding private key.

![[Pasted image 20240208214956.png]]

### 3. Key Exchange

This phase is where we move from asymmetric encryption to symmetric encryption, meaning that from now on, the client-server communication will be as secure, much easier from a computational standpoint.

In this stage the following steps happen:
- The client generates a "Pre-Master Key", encrypts it using the public key and sends is to the server
- The server decrypts it using it's private key. Now both have the same Pre-Master Key.
- Both sides will use the Pre-master Key to generate the "Master Secret" which is used to generate the ongoing "session keys" which encrypt and decrypt data.
- Both sides confirm the handshake, and from now on, communications between client and server are secured.

![[Pasted image 20240208221441.png]]


#networking 