---
date: '2015-06-15T00:00:00Z'
title: 'SSL Certificate Transfer - Digging Out Public Keys'
---

Say you've got a client and a server. The server is running SSL, so it has a certificate stored in a .pem file, which looks a bit like this:

```
-----BEGIN CERTIFICATE-----
MIIC1jCCAb6gAwIBAgIJAMQrHtnBwK9KMA0GCSqGSIb3DQEBCwUAMCMxITAfBgNV
BAMTGHZhZ3JhbnQtdWJ1bnR1LXRydXN0eS0zMjAeFw0xNTA2MTExNDIxNTNaFw0y
NTA2MDgxNDIxNTNaMCMxITAfBgNVBAMTGHZhZ3JhbnQtdWJ1bnR1LXRydXN0eS0z
MjCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBALuRUDnqMVAJwy2qm96O
bIuCeX+Dh/sooMak/ZrVbswgZSXL6uT5M/VPnX/rBihMESCf8nB27VRXYAgM88Wk
ZEBpXKpuwIDzN9+fdj4n6WGYZnQx75ODGHx5ukbacH93Eftg2kfliRZXOnGKLJV1
hU6oazB8lSP1ZeYpGXW+v9GFSd9MJPHapI5mQzdtwFftaGDdLsMpo7tAvU1snYcQ
gKu68cEFmEUyLey0EWpeoYOUNjFdrvROKACVGy3ViIOlIEEWVWFYKVvzxlMG8l8J
5AlqRe/X3Vg1GqbmBLzj9TyuYIUt4b2HbvaPvdQYr1UX7D/XWI3QaE5qsxc3E+X+
vhsCAwEAAaMNMAswCQYDVR0TBAIwADANBgkqhkiG9w0BAQsFAAOCAQEAoAEz5bXJ
vRAR7ZJKlSDkOGmnUkaDFFkORSV7TwCgA4NQctxEETD72IFSGhMniIsrsplE0J45
L8+HsL91vP1qqYSs18TDAtMCcg4ZSlyz59cFCBYpy4LamZIiDYwi01T3ZfGZdkm3
TIjJVpG8H9Q86Q2LaYs9ij9Dy1MIMwAkEREt4UvvByA8/a1HqmXMoCJCWps9UUgQ
PmFT0oZWvxDecgayIpaURAsausWwUy/1fNTFwURpLO7U6vS5F+tt+UNLS1J50mss
88O5YLOlU/VE08jobKPnJL874XB52vfFvm9fyKSKVGB3x1susPduzVES4lRoRWZG
9Etek9wLpapsAA==
-----END CERTIFICATE-----
```

And when the client connects, it receives the certificate and can validate it, right?

Recently I found myself in the odd position of needing to write code to view the contents of the certificate on both sides of the connection and obtain data from them. It didn't occur to me, until now, that I know absolutely nothing about that block of data, there. I guessed it was base64 encoded, but beyond that, it was a total mystery.

It turns out that a PEM file is a base64 encoded [DER](https://en.wikipedia.org/wiki/X.690#DER_encoding) document, DER being a transfer syntax for data structures described by [ASN.1](https://en.wikipedia.org/wiki/Abstract_Syntax_Notation_One). That discovery left me no closer to understanding what was actually in my certificate, so let's boil all that down and say that this is just a format for storing this stuff on disk that comes from a time when storing data was a lot harder than it is now.

What's actually in there is your certificate - it'll be an X.509 certificate, probably, and the [Wikipedia page](https://en.wikipedia.org/wiki/X.509) will cover you for the most part on what that is. Broadly, they come with a few dates and an issuer and an RSA public key that's tied to a private key somewhere else on the server box.

I was in the position where the data I had in a C# client did not match the data I was pulling out of the file in the python server end. Specifically, I was trying to compare the public keys - C# presents you with an array of bytes when you call [X509Certificate.GetPublicKey](https://msdn.microsoft.com/en-us/library/system.security.cryptography.x509certificates.x509certificate.getpublickey(v=vs.110).aspx) but I couldn't get that array to line up with what I found in that pile of base64 encoded data.

After much prodding, I got to the bottom of it. I used the python [M2Crypto](https://pypi.python.org/pypi/M2Crypto) library and binascii to convert the base64 data. Here's the process you go through to go from PEM file to public key, as it is received on the client side.

```python
import M2Crypto
import binascii


# Load the certificate in M2Crypto
path = '~/certificate.pem'
cert = M2Crypto.X509.load_cert(path)

# Get the public key from it, in PEM format
pem = cert.get_pubkey().get_rsa().as_pem()

# Pull the headers and footers off
key = ''.join(pem.split('\n')[1:-2])

# Convert from base64 to a string of bytes
bytes = bytearray(binascii.a2b_base64(key))

# Finally, cut the first 24 bytes off. This must be some kind of header
public_key = bytes[24:]
```

If you can tell me what those 24 bytes do, I'd love to know, and I'd love to know why they don't appear on the other side. They must be part of the PEM formatting. I feel like it must be possible to avoid PEM entirely here, as it seems like a bit of a long way around, but I haven't come across one. If you can help tie up some of these loose ends, please drop me a [github pull request or issue](https://github.com/veryhappythings/veryhappythings.github.com/blob/master/_posts/2015-06-15-ssl-certificate-transfer.md). Thanks!
