# Android App Signing and Upload Keys

Android requires every installable APK to be digitally signed. The signature identifies the party responsible for a build and lets Android decide whether a future version may update the installed application.

Signing is therefore part of an application's long-term identity, not merely a final build step. Losing or exposing key material can interrupt releases, enable impersonation, or prevent trusted updates.

## The Google Play Signing Model

For a new application distributed through Google Play, Play App Signing separates two responsibilities:

| Item | Held by | Purpose | Secret? |
| --- | --- | --- | --- |
| App-signing private key | Google Play | Signs APKs delivered to users | Yes |
| App-signing certificate | Google Play and developer | Identifies the installed application | No |
| Upload private key | Developer or CI system | Signs an AAB or APK submitted to Google Play | Yes |
| Upload certificate | Developer and Google Play | Lets Google Play verify an upload | No |

```text
developer or CI system
        |
        | signs the .aab with the upload key
        v
Google Play
        | verifies the upload certificate
        | creates device-specific APKs
        | signs the APKs with the app-signing key
        v
user device
```

The upload key and app-signing key should be different. If an upload key is lost or compromised, its public certificate can be replaced through the Play Console without changing the app-signing key used for installed applications.

This separation is specific to Play App Signing. Another store or direct APK distribution may require the developer to retain the app-signing key. Understand the target store's signing model before generating or moving keys.

## How Digital Signing Works

Key generation creates a mathematically related **key pair**:

- the private key is kept by the signer and is used to create signatures;
- the public key can be distributed and is used to verify those signatures.

It should be computationally infeasible to calculate the private key from the public key. This asymmetry allows verification without giving every verifier the power to create signatures.

At a simplified level, signing and verification work like this:

```text
build artifact
      |
      | cryptographic hash function
      v
fixed-size digest ---- signed with private key ----> digital signature

build artifact received later
      |
      | hash it again
      v
new digest + signature + public key ---- verify ----> valid or invalid
```

A cryptographic hash is a compact fingerprint of the artifact's bytes. Changing even a small part of the artifact produces a different digest. The signature algorithm uses the private key to bind the original digest to the signer. A verifier calculates the digest again and uses the public key to check the signature.

A successful check establishes two useful properties:

1. **Integrity:** the signed content has not changed since it was signed.
2. **Proof of key control:** the signature was created by someone or something with access to the corresponding private key.

The signature alone does not prove a person's real-world identity. Trust comes from how the public key is associated with an identity. For an upload key, an authenticated Play Console process registers the upload certificate with the developer account and application. Google Play then accepts a release only when its signature verifies against that registered certificate.

For an installed application, Android records its signing identity. An update must use an accepted signing certificate or certificate lineage for that package. This prevents another developer from publishing an unrelated build with the same package name and silently replacing the installed application.

## Why Generate the Key on Your Computer?

The key does not gain authority merely because it was created on a particular computer. `keytool` uses a cryptographically secure source of randomness to generate a unique key pair; authority is established later when the public certificate is registered with Google Play or another trusted system.

Generating the key on a computer you control provides a secure starting point:

1. The private key is created inside your trust boundary.
2. Only the public certificate needs to be shared for registration and verification.
3. The private key can remain in the keystore while it signs release artifacts.
4. Anyone without the private key can verify signatures but cannot produce a valid new one.

Local generation is a practical choice, not a requirement that signing always happen on a laptop. A team may generate or hold keys in a managed secrets system, hardware security module, or protected build service. The important property is that the private key is generated securely, remains under controlled custody, and is available only to authorised signing processes.

Moving an upload keystore into CI expands the trust boundary. The secret store, workflow permissions, runner, logs, and administrators can all affect the key's security. Base64 encoding makes the binary file transportable but does not preserve secrecy by itself.

The complete authentication chain is therefore:

```text
secure key generation
        |
        v
private key kept secret + public certificate registered
        |
        v
release signed with private key
        |
        v
signature verified with registered public key
        |
        v
trusted system accepts the signer as the registered key holder
```

## Keys, Certificates, Keystores, and Aliases

- A **private key** performs signing and must remain secret.
- A **public certificate** contains the corresponding public key and identity metadata. It can be shared with Google Play or an API provider.
- A **keystore** is a protected file containing private keys and certificates.
- An **alias** identifies an entry inside a keystore.
- A **keystore password** protects access to the keystore.
- A **key password** protects a private-key entry. Some Android tooling expects it to match the keystore password, but configuration should still represent the values separately.

The filename extension does not guarantee the internal format. Modern `keytool` versions commonly default to PKCS12. Specify `-storetype JKS` when JKS is required, or deliberately use PKCS12 with a filename such as `.p12`.

## Generate an Upload Keystore

The Java Development Kit includes `keytool`. Check that it is available:

```bash
java -version
keytool -help
```

Generate a 2048-bit RSA key pair in an explicitly JKS-formatted keystore:

```bash
keytool -genkeypair \
  -v \
  -keystore upload-keystore.jks \
  -storetype JKS \
  -alias upload \
  -keyalg RSA \
  -keysize 2048 \
  -validity 10000
```

The 10,000-day validity is longer than Android's recommended minimum of 25 years. Choose an alias that describes the key's role because several release systems may use the entry over its lifetime.

`keytool` prompts for passwords and certificate identity fields. Do not put passwords on the command line: command history, process inspection, or build logs can expose them.

Creating another file with the same name does not recover a lost key. A newly generated pair has a different cryptographic identity even if its filename, alias, passwords, and identity fields match.

## Inspect and Export the Certificate

Confirm the keystore type, alias, validity, and fingerprints before connecting it to a release process:

```bash
keytool -list -v \
  -keystore upload-keystore.jks \
  -alias upload
```

Record non-secret metadata: the application and package name, format, filename, alias, SHA-256 fingerprint, validity dates, owner, backup locations, and release systems that use the key.

Google Play may ask for the upload certificate when registering or resetting an upload key. Export the public certificate without exporting the private key:

```bash
keytool -exportcert \
  -rfc \
  -keystore upload-keystore.jks \
  -alias upload \
  -file upload-certificate.pem
```

The PEM certificate is public material and cannot sign a build. Verify its fingerprint before registration so the wrong certificate is not associated with the application.

## Back Up the Private Key

Keep at least two access-controlled backups in different failure domains, such as an encrypted secrets vault and an encrypted offline backup. Securely store or escrow:

- the keystore file;
- the keystore and key passwords;
- the alias;
- recovery ownership and instructions.

Test recovery by restoring a copy into an isolated location and running `keytool -list`. A backup that has never been restored is only an assumption.

Do not commit a keystore merely because a repository is private. Copies spread through clones, forks, caches, artifacts, and backups. A `.gitignore` rule is useful, but it does not remove a file already present in Git history.

## Transport a Keystore to CI

When a CI secret store accepts text, encode the binary keystore as a single-line Base64 value. On GNU/Linux:

```bash
base64 -w 0 upload-keystore.jks > upload-keystore.base64.txt
```

On macOS:

```bash
base64 -i upload-keystore.jks -o upload-keystore.base64.txt
```

On PowerShell:

```powershell
$bytes = [System.IO.File]::ReadAllBytes('C:\secure\upload-keystore.jks')
[System.Convert]::ToBase64String($bytes) |
  Set-Content -NoNewline 'C:\secure\upload-keystore.base64.txt'
```

Base64 is reversible encoding, not encryption. Protect the text like the original keystore and remove temporary exports when they are no longer needed.

A generic GitHub Actions secret set is:

```text
ANDROID_KEYSTORE_BASE64
ANDROID_KEYSTORE_PASSWORD
ANDROID_KEY_ALIAS
ANDROID_KEY_PASSWORD
```

Secret names may be documented; values must not appear in source control, logs, issues, or artifacts. Environment-level secrets and deployment approvals can provide stronger separation for releases than ordinary repository workflows.

## Use the Key in GitHub Actions

This fragment demonstrates the lifecycle without assuming a particular Gradle signing configuration:

```yaml
jobs:
  release:
    runs-on: ubuntu-latest
    env:
      KEYSTORE_FILE: ${{ runner.temp }}/upload-keystore.jks
    steps:
      - uses: actions/checkout@v6

      - name: Restore upload keystore
        shell: bash
        env:
          KEYSTORE_BASE64: ${{ secrets.ANDROID_KEYSTORE_BASE64 }}
        run: printf '%s' "$KEYSTORE_BASE64" | base64 --decode > "$KEYSTORE_FILE"

      - name: Build signed bundle
        env:
          ANDROID_KEYSTORE_PATH: ${{ env.KEYSTORE_FILE }}
          ANDROID_KEYSTORE_PASSWORD: ${{ secrets.ANDROID_KEYSTORE_PASSWORD }}
          ANDROID_KEY_ALIAS: ${{ secrets.ANDROID_KEY_ALIAS }}
          ANDROID_KEY_PASSWORD: ${{ secrets.ANDROID_KEY_PASSWORD }}
        run: ./gradlew bundleRelease

      - name: Remove restored keystore
        if: ${{ always() }}
        shell: bash
        run: rm -f "$KEYSTORE_FILE"
```

The Gradle build must explicitly read these environment variables; their names do not configure signing by themselves. Scope secrets to trusted steps, avoid shell tracing, and never expose release secrets to untrusted pull-request code.

## Release and Recovery

Before the first release:

1. Verify the package name because it forms part of the application's stable identity.
2. Inspect and back up the upload keystore.
3. Register the correct certificate through Play App Signing.
4. Build the AAB with the upload key and confirm Google Play accepts it.
5. Record both upload and app-signing certificate fingerprints from Play Console.
6. Use the app-signing certificate—not automatically the upload certificate—when an API provider identifies the version installed from Google Play.

If the upload key is lost or compromised, stop using it, generate a replacement, export its certificate, and request an upload-key reset in Play Console. This does not replace the Play-managed app-signing key.

Loss of a self-managed app-signing key can prevent compatible updates. Decide ownership and store-specific recovery procedures before release rather than during an incident.

## Common Mistakes

- Treating the upload key and app-signing key as the same responsibility.
- Committing `.jks`, `.keystore`, `.p12`, Base64, or secret properties files.
- Assuming Base64 protects secret material.
- Keeping the keystore and its only backup on the same computer.
- Registering the wrong certificate or forgetting the alias.
- Recreating a same-named keystore and expecting the old identity.
- Giving an API provider the upload fingerprint when it needs the Play app-signing fingerprint.
- Exposing release secrets to pull-request code or logs.
- Leaving a restored keystore in a CI artifact or workspace.

## Review Checklist

- [ ] The distribution channel and its signing model are understood.
- [ ] Upload and app-signing keys have distinct documented responsibilities.
- [ ] The format, alias, validity, and SHA-256 fingerprint are verified.
- [ ] Private keys and passwords are absent from source control.
- [ ] Two protected backups exist and one has been test-restored.
- [ ] CI secrets are scoped to trusted release workflows.
- [ ] Temporary keystore and Base64 files are removed after use.
- [ ] Certificate fingerprints are registered with the correct services.
- [ ] Lost-key and compromised-key recovery ownership is documented.

## Official References

- [Sign your app](https://developer.android.com/studio/publish/app-signing) — Android signing concepts, Play App Signing, key generation, and upload-key resets.
- [Upload your app to the Play Console](https://developer.android.com/studio/publish/upload-bundle) — signed bundle upload and release requirements.
- [The `keytool` command](https://docs.oracle.com/en/java/javase/25/docs/specs/man/keytool.html) — Java keystore and certificate command reference.
- [Using secrets in GitHub Actions](https://docs.github.com/en/actions/how-tos/write-workflows/choose-what-workflows-do/use-secrets) — secret handling and Base64-encoded binary blobs.

## Related Guides

- [Technology Stack](./technology-stack.md)
- [Git](./git.md)
- [Code Review](./code-review.md)
