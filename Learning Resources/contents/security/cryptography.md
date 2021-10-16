<frontmatter>
  title: Introduction to Cryptography
  pageNav: 3
</frontmatter>

<div class="website-content">

# Introduction to Cryptography

Authors: [Dickson Tan](https://github.com/neurrone)

<box id="article-toc">

* [Overview‎](#overview)
* [Encryption‎](#encryption)
* [Symmetric Key Ciphers‎](#symmetric-key-ciphers)
* [Asymmetric Key Cryptosystems‎](#asymmetric-key-cryptosystems)
* [Other Resources‎](#other-resources)
</box>

## Overview

Cryptography is the science of developing methods for secure communication, while Cryptanalysis is the discipline of breaking such methods.
Understanding cryptography will enable you to correctly use cryptography libraries and combine cryptographic primitives to match the security needs of your application.

Modern cryptography involves developing techniques to achieve the following objectives:

* Confidentiality: ensuring that a message can only be read by its intended recipient. For example, websites use encryption in the Transport Layer Security (TLS) protocol to prevent interception of sensitive information such as credit card numbers and passwords.
* Integrity: ensuring a message has not been changed. For example, hash functions are used in the BitTorrent protocol to ensure files received have not been modified by malicious peers.
* Authentication: the recipient should be able to verify the sender of a message. For example, iPhones need to verify that the software updates received really came from Apple, not a malicious attacker.
* Non-repudiation: the sender should not be able to deny sending a message after doing so. For example, the parties of a digital contract should not be able to deny signing the contract in a legal dispute.

In Cryptanalysis, the objective is to exploit weaknesses in cryptographic algorithms - for example, attempting to recover the original message from its encrypted form. The security of cryptographic algorithms is evaluated based on their resistance to [these attacks](https://en.wikipedia.org/wiki/Cryptanalysis#Amount_of_information_available_to_the_attacker).

## Encryption

Encryption is the process of encoding messages so that it is readable only by the intended recipient. The message being encrypted is called the plaintext. The encryption algorithm "scrambles" the message, producing ciphertext, which should only be readable by the recipient. Decryption is the reverse process of recovering the plaintext from the ciphertext.

A cipher refers to a pair of algorithms - one for encryption and decryption, and is used to refer to symmetric key techniques. A cryptosystem consists of 3 algorithms - a cipher and a key generation algorithm. Though cryptosystems usually refer to asymmetric key techniques, it may also be used to refer to symmetric key techniques.

The key is a parameter, or a piece of secret information, that determines the output of cryptographic algorithms. According to [Kerckhoffs' principle](https://en.wikipedia.org/wiki/Kerckhoffs%27s_principle), a cryptosystem should be secure even if the attacker knows everything about the cryptosystem, except the key. 
This means that the attacker knows the algorithms used for encryption and decryption. Although it is tempting to design secure systems by employing ["security by obscurity"](https://en.wikipedia.org/wiki/Security_through_obscurity) to keep their details hidden, they are usually easily broken once their design is known. The [CSS copy-protection system for DVDs](https://en.wikipedia.org/wiki/Content_Scramble_System) is one such example.

One of the earliest encryption algorithms or ciphers is the substitution cipher, which encrypts text by substituting each letter of the message with another letter. This page introduces the [Caesar substitution cipher](http://www.cs.trincoll.edu/~crypto/historical/caesar.html), and how it can be defeated using statistical analysis, a [ciphertext-only attack](https://en.wikipedia.org/wiki/Ciphertext-only_attack).
Modern ciphers today operate on bits rather than letters.

### Symmetric Key Ciphers

In a symmetric key cipher, the same key, a shared secret, is used for both encryption and decryption.
The parties wishing to communicate securely share the same key. For example, in the substitution cipher, the same key (the substitution table) is used for both encryption and decryption.
There are 2 types of symmetric ciphers: stream and block ciphers.

#### Stream Ciphers

The ciphertext, keystream and  plaintext are sequences or streams of bits of equal length. 
During encryption, each plaintext bit is combined (usually xored) with the corresponding keystream bit to produce the ciphertext bits. For example, the 1st plaintext bit is xored with the 1st keystream bit, 2nd plaintext bit with 2nd keystream bit and so on.
During decryption, each ciphertext bit is xored with the corresponding keystream bit to produce the plaintext.
Notice that encryption and decryption are the same operation; this is possible since xoring a bit x with another bit y twice recovers the original bit; (x xor y) xor y = x.

The simplest stream cipher is the [one-time pad](https://en.wikipedia.org/wiki/One-time_pad). In this cipher, the keystream used is bits from a truly random source, and is also the key.
It is the only known cipher that cannot be cracked, even if the attacker has infinite computing power.
This property is known as [perfect secrecy](https://crypto.stackexchange.com/questions/3896/simply-put-what-does-perfect-secrecy-mean); the ciphertext gives no additional information about the plaintext, so knowing the ciphertext does not provide any advantage to the attacker trying to recover the plaintext.
Unfortunately, [Shannon](https://www.scientificamerican.com/article/claude-e-shannon-founder/), renowned cryptographer and founder of Information Theory, proved that any cipher that achieves perfect secrecy must have the following limitations, making them [impractical](https://www.schneier.com/crypto-gram/archives/2002/1015.html#7).

* The key must be truely random, not pseudorandomly generated, and must never be reused.
* The key must be securely distributed, and be at least as long as the message being generated. For example, to send a 10gb file to someone encrypted with the one-time pad requires sending 10gb of key material + 10gb of ciphertext = 20gb.

In practice, we do not require perfect secrecy, since attackers have limited computational power. Hence, all other ciphers are only [computationally secure](https://en.wikipedia.org/wiki/Computational_hardness_assumption); their security relies on the assumption that certain problems are difficult to solve.

Modern stream ciphers approximate the operation of the one-time pad. 
A short key (say 256 bits) is used to seed a [cryptographically secure pseudorandom number generator](https://en.wikipedia.org/wiki/Cryptographically_secure_pseudorandom_number_generator), which is used to generate the keystream for both encryption and decryption.
They are more practical, as the key the communicating parties need to share is much shorter.

Keys must never be reused in stream ciphers. Doing so causes the same keystream, k, to be generated, and 2 plaintexts, p and q, to be encrypted with the same keystream. If we xor the ciphertexts for p and q, we get (p xor k) xor (q xor k) = (p xor q) xor (k xor k) = p xor q. This exposes information about the plaintexts, which may lead to the recovery of both. 
Here is a [visual illustration of this attack](https://crypto.stackexchange.com/questions/59/taking-advantage-of-one-time-pad-key-reuse), and [an example of this happening in practice](https://www.schneier.com/blog/archives/2005/01/microsoft_rc4_f.html).

Stream ciphers are used for their efficiency, ease of implementation in hardware, and when the length of the plaintext is unpredictable.
However, block ciphers are more widely used than stream ciphers. In some modes of operation, they can be used like stream ciphers, reducing the need for dedicated stream ciphers.

[RC4](https://en.wikipedia.org/wiki/RC4) is the most widely used stream cipher. Though its use is now discouraged due to known vulnerabilities. 
The [eSTREAM project](https://www.ecrypt.eu.org/stream/), a research effort to develop state-of-the-art stream ciphers, has identified several ciphers suitable for widespread adoption. However, being relatively new, they have not been analyzed as extensively by cryptographers.

#### Block Ciphers

Unlike stream ciphers, which operate on individual bits, block ciphers operate on an entire block of bits at a time. In practice, the size of each block is 64 or 128 bits.

[Shannon](https://www.scientificamerican.com/article/claude-e-shannon-founder/) introduced 2 primitives, which modern block ciphers are built on.

* Confusion: an operation which obscures the relationship between key and ciphertext. This is usually done by substitution.
* Diffusion: an operation which hides statistical properties in the plaintext by spreading the influence of a plaintext bit over many ciphertext bits. For example, the DES cipher achieves this by bit Permutations.

Ciphers that use only one of these operations are insecure. For example, the insecure Caesar cipher only uses confusion. But strong ciphers can be built by using both confusion and diffusion - these are called product ciphers.

* This [article](https://graquantum.com/blog/deciphering-encryption-des-block-cipher/) explains how the  DES cipher works, Feistel networks, s-boxes and p-boxes. Though DES is no longer secure, its design has inspired many ciphers. A still secure variant, triple DES, is popular in legacy applications.
* The Advanced Encryption Standard (AES) is the most popular symmetric cipher today. It is used by the US government and in many protocols such as TLS, WPA2-AES and SSH. This [article](https://graquantum.com/blog/deciphering-encryption-aes-block-cipher/) explains how AES works without going too much into the mathematical details.

##### Modes of Operation

Block ciphers alone aren't very useful, because they only provide a secure way of encrypting one block of data. 
Modes of operation are ways of using block ciphers to securely encrypt multiple blocks. 
The plaintext is padded if it is not an even multiple of the block size.
Block ciphers can also provide additional services such as integrity, depending on the mode used, which makes them vercitile. 
This [article](http://www.crypto-it.net/eng/theory/modes-of-block-ciphers.html) provides a nice overview of common modes. 

Most modes require a random value called an initialization vector (IV) so that encrypting the same message twice doesn't produce the same ciphertext, which leaks information. 
It is critical that the IV be [random, used only once and unpredictable](https://defuse.ca/cbcmodeiv.htm). Not doing so has caused several vulnerabilities such as the [BEAST Attack on TLS](http://www.educatedguesswork.org/2011/09/security_impact_of_the_rizzodu.html) and [the recovery of WEP keys](https://en.wikipedia.org/wiki/Wired_Equivalent_Privacy).

### Asymmetric Key Cryptosystems

Symmetric cryptography is not practical for the following situations, which motivates the development of asymmetric or public key cryptography:

* Key agreement: the communicating parties must securely establish a shared secret key, since the communication channel for the message is insecure, other methods must be used.
* Large number of keys: for each user in a group of `n` users to securely communicate to every other user, each user must securely store `n-1` keys , which is impractical.
* No non-repudiation: since each party has the same capability by possessing the shared key, symmetric cryptography cannot be used when non-repudiation is required. For instance, if an online store, Bob, needs to prove Alice purchased an item where symmetric encryption is used for the transaction message, Alice can claim Bob falsely generated the transaction, as they both share the same key.

In asymmetric cryptography, there are 2 separate keys; one for encryption, and the other for decryption. The key used for encryption is published so that anyone can securely send messages to Alice. Hence, it is called a public key. Alice has the corresponding decryption key, or private key, which is kept secret. Hence, messages encrypted with the public key can only be decrypted by Alice. In contrast, there is a single shared key which must be kept secret in symmetric ciphers.

Public key cryptography has many uses beyond sending encrypted messages, such as key agreement and non-repudiation, which will be covered in future sections.

For this to be secure, it must be computationally infeasible to obtain the private key from the public key. This is achieved using one-way functions, which have the following properties:

* It must be easy to compute y = f(x) so that encryption is not too slow.
* It is computationally infeasible to compute x = f^{-1}(y).

#### The RSA Cryptosystem

[RSA](https://en.wikipedia.org/wiki/RSA_(cryptosystem)) is one of the earliest and most widely used public key cryposystems. 
Its one-way function is the integer factorization problem; Given 2 large primes `p` and `q`, it is easy to compute the product `pq`, but difficult to factor `pq`. 
The [first article of this 2-part series  ](http://doctrina.org/How-RSA-Works-With-Examples.html) explains how RSA works, as well as the minimal number theory required. The [follow-up article](http://doctrina.org/Why-RSA-Works-Three-Fundamental-Questions-Answered.html) explains why RSA works by introducing some important results in number theory.

## Other Resources

* [Understanding Cryptography: A Textbook for Students and Practitioners](https://www.amazon.com/Understanding-Cryptography-Textbook-Students-Practitioners/dp/3642041000) is an outstanding introductory text. Explanations are excellent, and no knowledge of number theory is assumed. It was used as reference material for this document.
* [Awesome Cryptography](https://github.com/sobolevn/awesome-cryptography) is a curated list of resources - articles, blogs, books, libraries and more.
* [Security Now](https://grc.com/sn) is a weekly podcast on security. Though it does not go into much detail about the underlying mathematics, there are many episodes on cryptography that provide a working knowledge of the subject. It also discusses security headlines, which emphasize the practical aspect of cryptography; while the math may be sound, implementation mistakes or side-channel attacks often cause vulnerabilities in practice.
</div>
