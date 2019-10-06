---
title: "Cryptography glossary"
date: 2018-08-19
publishdate : 2018-08-19
tags: [ssl,security,certificate]
image: "https://images.unsplash.com/photo-1477039181047-efb4357d01bd?ixlib=rb-1.2.1&ixid=eyJhcHBfaWQiOjEyMDd9&auto=format&fit=crop&w=1350&q=80"
description: "This is meta description."
author: "Malys"
type: "post"
---

# PKI 

* PKI Public Key Infrastructure
* CSR
* CLR
* X.509 standard that defines the format of public key certificates

# Standard

## Public-Key Cryptography Standards (**PKCS**)

* PKCS#1 **unencrypted** RSA key storage format.
* PKCS#7 Collection of **public certificates**.
* PKCS#8 Encrypted **private key** format for RSA DSA EC keys.
* PKCS#10 Certificate signing request (**CSR**).
* PKCS#11 Security token / Smart card / **HSM access**.
* PKCS#12 Certificate, Private key and probably a CA chain.

# File formats

* DER Distinguished Encoding Rules - Binary format
* PEM Privacy Enhanced Mail - Text format

## Extensions

* **.pem** (Privacy-enhanced Electronic Mail) Base64 encoded DER certificate, enclosed between "-----BEGIN CERTIFICATE-----" and "-----END CERTIFICATE-----"
* **.cer, .crt, .der** – usually in binary DER form, but Base64-encoded certificates are common too (see .pem above)
* **.p7b, .p7c** PKCS#7 SignedData structure without data, just certificate(s) or CRL(s)
* **.p12** PKCS#12, may contain certificate(s) (public) and private keys (password protected)
* **.pfx** PFX, predecessor of PKCS#12 (usually contains data in PKCS#12 format, e.g., with PFX files generated in IIS)


# Algorithms

TODO