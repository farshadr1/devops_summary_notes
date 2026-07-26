# Fundmental about securing certications

## Part 1 - Cryptography Fundamentals
Form of texts:
1. Plain Text → Normal readable data.
2. Encryption → Two way converting with special Enc/Dec methods.
3. Hash Function  → A hash is NOT encryption. One-way

## Part 2 - Symmetric/Asymmetric Encryption

**Symmetric:** One secret key encrypts and decrypts.

Popular algorithms:
- AES
- ChaCha20

```bash
echo "My Secret Data" > secret.txt

# Encrypt
openssl enc -aes-256-cbc -salt -pbkdf2 -in secret.txt -out secret.enc -k myPassword123

# Decrypt
openssl enc -d -aes-256-cbc -pbkdf2 -in secret.enc -out decrypted.txt -k myPassword123
```

**Asymmetric:** Uses two keys. Public Key, Private Key
Only the private key can decrypt data encrypted with the matching public key.

Algorithms:

RSA
ECC (Elliptic Curve)
Ed25519 (commonly used for signatures and SSH)

```bash
# Generate a Private Key (RSA 2048-bit)
openssl genrsa -out private_key.pem 2048

# Extract the Public Key from it:
openssl rsa -in private_key.pem -pubout -out public_key.pem
```

## Part 3 - Digital Signature

Use for Authentication and Integrity of A Data.

![image](Digital_Signature_diagram.jpeg)
