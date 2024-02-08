Digital signatures are cryptographic techniques used to provide authentication, integrity, and non-repudiation for digital messages or documents. They are a cornerstone of modern secure communication and transaction systems, ensuring that electronic communications and transactions are conducted securely and with accountability.

##### **Steps:**

- Verifies **INTEGRITY (WHAT)** & **AUTHENTICITY (WHO)**
- **HASH** of the data is taken, original data remains unaltered (integrity)
- This allows normal use, without worrying about hashing/keys
- Digitally sign the **HASH** (using private key). Authenticates the hash
- Public key can be widely distributed and trusted
- ... hash cannot be changed as nobody else has the private key
- Document cannot be changed as this invalidates the **HASH**
- ***Trust public key => trust private key => trust entity => trust data***


![[Pasted image 20240208225307.png]]


##### **Digital Signature Algorithms:**

- **RSA (Rivest-Shamir-Adleman):** One of the most widely used asymmetric encryption algorithms for digital signatures. It is based on the difficulty of factoring large composite numbers and provides strong security guarantees.
- **DSA (Digital Signature Algorithm):** A standard for digital signatures based on the discrete logarithm problem. It is commonly used in government and financial applications.
- **ECDSA (Elliptic Curve Digital Signature Algorithm):** A variant of DSA based on elliptic curve cryptography. It offers shorter key lengths and faster processing while maintaining strong security.

##### **Applications of Digital Signatures:**

- **Secure Communication:** Digital signatures ensure the authenticity and integrity of messages exchanged between parties in secure communication protocols such as SSL/TLS.
- **Electronic Transactions:** Digital signatures are used to authenticate participants and verify transactions in electronic commerce and online banking systems.
- **Legal Contracts:** Digital signatures are legally binding and recognized as electronic equivalents of handwritten signatures in contracts, agreements, and legal documents.
- **Document Verification:** Digital signatures are used to validate the authenticity and integrity of digital documents, such as software updates, electronic records, and official certificates.

##### **Standards and Regulations:**

- **PKI (Public Key Infrastructure):** A framework of policies and procedures for managing digital certificates and public-private key pairs. It facilitates secure communication and authentication by providing mechanisms for key generation, distribution, and management.
- **X.509:** A standard format for digital certificates used in PKI systems. It defines the structure and contents of certificates issued by certificate authorities (CAs) and is widely used in SSL/TLS and other secure communication protocols.
- **EU eIDAS Regulation:** European regulations governing electronic identification, authentication, and trust services, including digital signatures, within the European Union. It provides a legal framework for electronic signatures and sets requirements for their validity and acceptance.

#encryption_and_signatures