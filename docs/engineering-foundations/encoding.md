# Encoding

Encoding converts data from one representation into another so that a system can store, transmit, or interpret it correctly.

```text
original data -- encode --> encoded representation
original data <-- decode -- encoded representation
```

The rules are public and no secret is involved. Anyone who knows the format can decode the result, so encoding does not provide confidentiality.

## Common Forms of Encoding

- **Character encoding:** UTF-8 represents text as bytes.
- **Base64:** represents binary data using a limited set of text characters.
- **URL encoding:** escapes characters that have special meaning in a URL.
- **Image and audio formats:** define how media data is represented and interpreted.

Base64 is useful when a text-only channel must carry binary data. Its output may look unreadable, but it is trivially reversible:

```text
hello -> aGVsbG8= -> hello
```

Encoding credentials, tokens, or private keys does not protect them. Access controls, encryption, and careful secret handling are still required.

## Encoding Compared with Encryption and Hashing

| Technique | Primary purpose | Reversible? | Secret required? |
| --- | --- | --- | --- |
| Encoding | Represent data in a compatible format | Yes | No |
| [Encryption](./encryption.md) | Keep data confidential | Yes, with the correct key | Yes |
| [Hashing](./hashing.md) | Create a fixed-size fingerprint | No, by design | No |

These are categories of operations, not interchangeable levels of security. Data may pass through all three during its lifecycle because each transformation has a separate responsibility.

For example, a service sending a sensitive structured message might use this flow:

```text
structured data
      |
      | encode into a defined byte representation
      v
plaintext bytes
      |
      | authenticated encryption with a secret key
      v
ciphertext bytes
      |
      | Base64 encode for a text-only transport
      v
transport-safe text
```

The receiver reverses the applicable operations in the opposite order: Base64-decode, authenticate and decrypt, then interpret the encoded structure.

## Common Misconceptions

- **“It looks unreadable, so it is encrypted.”** Encoded data is often unreadable to a person but remains publicly reversible.
- **“Base64 protects secrets.”** Base64 only changes the representation; anyone can decode it.
- **“Encoding and compression are the same.”** Encoding changes representation, while compression aims to reduce size. A format may use both.

The reliable mental model is simple: **encoding preserves meaning across representations**.

## A Simple Real-World Example

An email system uses Base64 to represent a photo attachment as text that can travel safely within an email message. The recipient's email application decodes that text back into the original photo.

```text
photo bytes -> Base64 text -> email transport -> photo bytes
```

Anyone who obtains the Base64 text can decode the photo. Encoding makes the data compatible with the transport; it does not keep the photo secret.
