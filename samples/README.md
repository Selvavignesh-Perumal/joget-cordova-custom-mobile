# Sample build credentials

These files let the GitHub Actions workflows build **without any repository secrets**.
If a repository secret is set, the workflow uses the secret instead of the sample file.

> **Do not publish apps signed with these files.** The sample keystore and its password are public.
> Its only use is getting a test build installed.
> Firebase values are placeholders: the app builds, but push notifications will not work.

| File | Used when this secret is missing | Purpose |
|---|---|---|
| `android/android.keystore` | `ANDROID_KEYSTORE_BASE64` | Signs the APK/AAB |
| `android/google-services.json` | `ANDROID_GOOGLE_SERVICES_JSON_BASE64` | Firebase config (placeholder) |
| `ios/GoogleService-Info.plist` | `IOS_GOOGLESERVICE_PLIST_BASE64` | Firebase config (placeholder) |

## Sample keystore

| Setting | Value | Overridden by secret |
|---|---|---|
| Store password | `android` | `ANDROID_KEYSTORE_PASSWORD` |
| Key password | `android` | `ANDROID_KEYSTORE_PASSWORD` |
| Alias | `custommobile` | `ANDROID_KEYSTORE_ALIAS` |

Regenerate it with:

```sh
keytool -genkeypair -v -keystore samples/android/android.keystore -storetype PKCS12 \
  -alias custommobile -keyalg RSA -keysize 2048 -validity 10000 \
  -storepass android -keypass android \
  -dname "CN=Custom Mobile Sample, OU=Sample, O=Example Inc, L=Sample, ST=Sample, C=US"
```

## iOS signing

Apple code signing cannot use sample files. A real distribution certificate and provisioning profile are tied to your Apple Developer account.

- **Without** `IOS_CERTIFICATE_BASE64` / `IOS_PROVISION_PROFILE_BASE64`, the iOS workflow builds an **unsigned** IPA
  (`ios-app-unsigned` artifact). It cannot be installed until it is re-signed.
  You can re-sign it with Xcode, `fastlane resign`, or a similar tool.
- **With** those secrets (plus `IOS_P12_PASSWORD`), it builds a signed App Store IPA (`ios-app-release` artifact).

## Using your own files

To use a real file, either replace the file in this folder or set the matching secret.
A secret holds the base64-encoded contents of the file:

```sh
base64 -i android.keystore | pbcopy        # macOS
base64 -w0 android.keystore                 # Linux
```
