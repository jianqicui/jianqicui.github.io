---
layout: post
title: Data Transport Security
category: Java
tags: [Encryption Algorithm, Data Transport]
keywords: Encryption Algorithm,Data Transport
excerpt: Data Transport Security
---

## Encryption Algorithm

### Symmetric Encryption

![](/assets/images/2023/Symmetric_Cryptography.jpg)

**Symmetric Encryption Algorithm**

* DES(Data Encryption Standard)
* AES(Advanced Encryption Standard)

### Asymmetric Encryption

![](/assets/images/2023/Asymmetric_Cryptography_1.jpg)
![](/assets/images/2023/Asymmetric_Cryptography_2.jpg)

**Asymmetric Encryption Algorithm**

* RSA(Ron Rivest, Adi Shamir, Leonard Adleman)

## Data Transport

### Plain Text

![](/assets/images/2023/Data_Transmission_Plain_Text_1.jpg)
![](/assets/images/2023/Data_Transmission_Plain_Text_2.jpg)

### Symmetric Encryption

![](/assets/images/2023/Data_Transmission_Symmetric_Encryption.jpg)

### Asymmetric Encryption

![](/assets/images/2023/Data_Transmission_Asymmetric_Encryption.jpg)

### Digital Signature

![](/assets/images/2023/Data_Transmission_Digital_Signature.jpg)

### Message Digest

![](/assets/images/2023/Data_Transmission_Message_Digest.jpg)

**Message Digest Algorithm**

* MD5(Message Digest 5)
* SHA(Secure Hash Algorithm)

### Digital Certificate

![](/assets/images/2023/Data_Transmission_Digital_Certificate_1.jpg)

* CA: certification authority is an entity that issues digital certificates
* The certificate of CA(Certification Authority) pre-installed in system
* Digital certificate: public key + digital signature of public key
* The private key of CA(Certification Authority) used to encrypt the public key of requestor
* The public key of CA(Certification Authority) used to decrypt the public key of requestor

## SSL/TLS/mTLS

* SSL: Secure Sockets Layer
* TLS: Transport Layer Security, newer and more secure SSL version
* mTLS: mutual Transport Layer Security

![](/assets/images/2023/SSL_TLS.jpg)

![](/assets/images/2023/mTLS.jpg)

## HTTP/HTTPS

![](/assets/images/2023/HTTP_HTTPS.png)

## Base64

* Base64 is an arbitrary binary to text string encoding method, which is often used to transmit a small amount of binary data in URLs, cookies, and web pages.

![](/assets/images/2023/Base64.JPG)
