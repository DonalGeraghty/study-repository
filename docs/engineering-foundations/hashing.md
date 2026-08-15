# Hashing

A cryptographic hash function maps input of any size to a fixed-size value called a **hash**, **digest**, or **fingerprint**.

```text
input of any size -- hash function --> fixed-size digest
```

The operation is deterministic: identical input produces the same digest. A small input change should produce a substantially different result.

## Security Properties

A cryptographic hash function is designed to make it computationally infeasible to:

- recover the original input from its digest (**preimage resistance**);
- find a different input with the same digest as a chosen input (**second-preimage resistance**);
- find any two different inputs with the same digest (**collision resistance**).

Hashing is one-way; it is not [encryption](./encryption.md) without a decryption key. Because the possible input space is larger than the fixed output space, collisions must exist mathematically. Security depends on them being impractical to find.

## Integrity Checking

If a trusted source publishes a file's SHA-256 digest, a recipient can hash the downloaded file and compare the results:

```text
expected digest == calculated digest  -> bytes probably match
expected digest != calculated digest  -> file differs
```

This only establishes trust when the expected digest arrives through a trusted channel. If an attacker can replace both the file and the published digest, the comparison does not prove authenticity.

Older algorithms such as MD5 and SHA-1 are collision-broken and should not be selected for security-sensitive integrity or signature designs. They may still appear as non-security checksums in legacy systems, but their limitations must be understood.

## Password Hashing

Applications should not store passwords in plaintext or with reversible encryption. They normally store the output of a dedicated password-hashing function such as Argon2id, scrypt, bcrypt, or PBKDF2.

Password hashing deliberately uses significant time and memory so that each password guess is expensive. General-purpose hashes such as SHA-256 are intentionally fast, which makes large-scale guessing too efficient.

A unique random **salt** is generated for each password and stored alongside the derived hash:

```text
password + unique salt -- password-hashing function --> stored hash

login attempt + stored salt -- same function --> candidate hash
candidate hash == stored hash -> password matches
```

The salt is not secret. It prevents identical passwords from producing identical stored values and makes precomputed lookup tables far less useful. It does not make weak passwords strong, so rate limiting, multi-factor authentication, breached-password screening, and sensible password policy remain important.

A **pepper** is an optional additional secret kept separately from the password database, typically in a secrets manager or hardware-backed key system. Unlike a salt, a pepper must be protected and requires an operational rotation and recovery plan.

## Hashing with a Secret: MACs

A plain hash can reveal whether data changed only when the expected hash is already trusted. When two parties share a secret and need to verify both integrity and origin, they can use a message authentication code (MAC).

HMAC combines a cryptographic hash with a secret key:

```text
message + secret key -- HMAC --> authentication tag
```

A recipient with the same secret can recalculate and compare the tag. An attacker who changes the message but lacks the key cannot create a valid replacement tag. Use a standard construction such as HMAC rather than inventing combinations like `hash(key + message)`.

## Choosing a Hashing Tool

| Requirement | Appropriate mechanism |
| --- | --- |
| Compare content using a compact fingerprint | Modern cryptographic hash |
| Store password verifiers | Dedicated salted password-hashing function |
| Authenticate a message with a shared secret | MAC, commonly HMAC |
| Detect accidental transmission errors | Checksum, when security is not required |

Hashing may participate inside a digital signature, message authentication scheme, cache key, or content identifier. The surrounding protocol determines what the digest proves.

## Common Misconceptions

- **“A hash can be decrypted.”** Hashes are one-way. Attackers instead guess inputs and compare their hashes.
- **“Any hash is suitable for passwords.”** Password storage requires a slow, configurable password-hashing function and a unique salt.
- **“A checksum proves a download is safe.”** A digest detects differences only relative to a trusted expected value; it does not establish that the publisher is trustworthy.
- **“Different input can never produce the same hash.”** Collisions exist, but a secure cryptographic hash makes them impractical to find.

The reliable mental model is simple: **hashing produces a stable, one-way fingerprint of data**.

## A Simple Real-World Example

Imagine logging in to a website. The website does not need to store your original password. Instead, it stores a salted password hash.

```text
password entered -> password hashing -> candidate hash
candidate hash == stored hash -> password matches
```

The website can verify that you entered the same password without recovering it from the stored value. If the password database is stolen, an attacker must still guess passwords individually to find matching hashes.

Return to [Engineering Foundations](./README.md).
