---
layout: post
title:  "High Level SSL/TSL Basics"
date:   2018-04-14 12:10:00 +1000
comments: "true"
published: "false"
categories:
  - "communications"
tags:
  - SSL
  - HTTPS
excerpt_separator: <!--more-->
---
This is the first article of a series that I plan to write on SSL (Secure Sockets Layer), and its sucessor, TLS (Transport Layer Security) from a developer perspective. That is to say, as a person to use SSL in her or his programs, not as a security expert / engineer / insider. I write these post as I become aware of SSL/TLS, so some inaccuracies can be expected: it is something I haven't had to personally apply at work for almost all my career, regardless how surprising that can be. One way or another, I decided to dig into the details once and for all, and write down my progresses.

This first article is about how does SSL work from a high level perspective. No code, just a bit of theory... the least of it to later understand what the code does.

Do not expect any regularity in these publications!
<!--more-->

## The problem: secure the information transport
The problem that SSL/TSL has to solve is a secure communication between two systems, securing the information transport level. That is, SSL/TSL secures TCP when we talk about the internet.

Upfront, SSL/TSL is not about human users, or about specific applications, but about applications as generic *systems*: browsers talking to servers, e-mail clients talking to servers, servers talking to each other. In essence, there is a sender system that has to communicate information to a receiving system, and most likely vice versa, regardless the users of those systems and regardless for what purpose (specific application).

It's about programs running on machines, not about you and me in principle: although one can issue certificates to people, that is less common than SSL between two servers or between a server and a browser. Furthermore, that protocol would be at an application level, not at a transport level, and for authentication purposes rather than end to end encryption as that would be provided by SSL at the transport layer.

You can use SSL to transport the data of any application over the network: e-mail, web, a secure messaging application that you and me could develop..., every program that uses transport *sockets*.

## Asymmetric Encryption
SSL and TLS use **Asymmetric Encryption** to secure the information. In these kind of schemas, each counterpart has a Cryptographic Certificate made of two parts: the Public Key (PK) and the Secret Key (SK). This second one is often referred to as Private Key or Private Part of the Certificate, but that would yield the same or a very similar acronym ("PK" or "PP"), so I prefer the first (SK). These two keys are uniquely bonded to each other thanks to the process of the Certificate generation.

When we use this to communicate, the encryption works as follows:

* If Alice wants to send Bob an encrypted message (M), Alice will encrypt the message with Bob's PK (*PKB*)
* The same as Alice, *anyone can send encrypted messages to Bob* using *PKB*
* Only Bob will be able to decrypt it with his SK (*SKB*)

![Encryption](/assets/Encryption.png){: .center-image }

The other way around is the same:
* Bob needs to use Alice's PK (*PKA*) to send encrypted information (M) to her
* The same as Bob, *anyone can send encrypted messages to Alice* using *PKA*
* That way, only Alice will be able to read the message by using her SK (*SKA*) to decrypt it.

In SSL and TLS, Alice and Bob are two systems. They can be a browser and a server, or any two general, independent systems that interchange information: the point is that both of them have a Certificate and they use it to encrypt the communication as mentioned.

## Trusting Certificates: Certification Authorities and Cryptographic Signature
One question one can have after reading the last section is:
> Yes, but: how can Bob be certain about Alice being who she claims to be? Is that Public Key really belonging to Alice? May that person be an imposter --not Alice?

For that questions we have Certification Authorities (CA), which are Entities that issue digital certificates and provides them with authenticity by signing them (specially the Public Key). In essence, the CA are trusted third parties that act as an issuer *and endorser* for the certificates they issue to Alice and Bob. The authenticity of a Certificate / Public Key is checked by validating the CA's signature of that Public Key with the CA's Public Key. That CA Public Key is, at the same time, signed by a higher level CA, and so on.

>**What is a Cryptographic Signature within an Asymmetric schema?** It is an operation performed by someone over a given content (as a message, or someone else's Public Key, as an endorsement) but by using its own Secret Key. That yields a special string that can be validated by using the signer's Public Key.
>
>![Signature](/assets/Signature.png){: .center-image }

That CA chain for verifications means that the confidence we give the CA is, somehow, to a certain extent, ultimately blind. One can trust blindly a government, but what if someone tampered the certificates the CA hierarchy uses to sign the lower level CA Public Keys? Ultimately, for Bob to know that *PKA* is really Alice's Public Key, *he has to decide whether or not trust the CA that created PKA for the person that claims to be Alice*.

That last phrase has deep, huge implications: Bob can decide to not trust a certain CA. If that was the CA that Alice used to get her certificate, then a trustful communication would not be possible!

I won't explain the internals of the trust hierarchy and how they do validations, but I can tell that the CA have the obligation to check and be sure about the true identity of the party to which / who they issue the certificates, before issuing them. For a CA, to sign a certificate or Public Key is the same as for us testifying in front of the world that someone that we know is who he or she claims to be. In that sense, for example, I have a certificate issued by the Government of Spain (my country of birth and nationality) that I use mostly to pay my taxes or to query / update my Government-related information. For obtaining it and renewing it, I have to go in person to a Spanish Government office and show my National ID or passport to a Government employee.

Something similar has to be done to obtain the Certificate for a system.

Some people say that the Global Certification Authorities are weaknesses themselves for this system, but that's another story and I don't have the knowledge to write down any view on that. I can understand that this potential flaw is because us trusting those CA is something ultimately blind, but I guess that I don't have all the information on that. I guess that there might be something somewhere that can give that confidence.

## Wrap up
* SSL protects conversations between programs (not users in principle, or at least in its simplest form) by encrypting the information they input in to the transport layer.
* It is based on Asymmetric Encryption, so that each of the parties have to supply a PK (Public Key) for the other to be able to cypher the traffic, which would be decrypted by means of the SK (Secret Key, sometimes referred to as Private Key or Private Part of the Certificate).
* In order to trust the certificate the other dude presents (or, more precisely, the Public Key), they are issued and signed by a Certificate Authority that has to be trusted beforehand, ultimately blindly.

## Next article
The next article will involve some Java code. To prepare, follow this nice tutorial on creating and signing a couple of certificates as if you were a CA:

* [OpenSSL Certificate Authority](https://jamielinux.com/docs/openssl-certificate-authority/index.html) on [https://jamielinux.com](https://jamielinux.com).
