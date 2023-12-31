# Fastlane Android for GitHub Actions

# Project Setup

Refer to the [RAVN Mobile CI/CD](https://github.com/ravnhq/mobile-cicd) on how to configure the pipeline for your
project before using this action.

_Planned: Allow run in "standalone" mode without needing to set up fastlane files in project manually._

# Android SDKs

Linux (with Ubuntu 22.04) and macOS 13 (except with ARM) already have the Android SDK tools installed and configured in
their runners:

- [Ubuntu 22.04 support](https://github.com/actions/runner-images/blob/main/images/linux/Ubuntu2204-Readme.md#android)
- [macOS 13 support](https://github.com/actions/runner-images/blob/main/images/macos/macos-13-Readme.md#android)

For unsupported runners or self-hosted runners the Android SDK tools must be manually configured.

# Example

```yaml
name: Deploy release
on:
  push:
    branches: [ main ] # or master, etc.

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Build and publish
        uses: ravnhq/fastlane-android-action@v1
        with:
          build-lane: release
          java-version: 17
          enforced-branch: main
          run-id-as-build: true
          commit-increment: false
          publish-build: true
          package-name: com.domain.application
          google-json-key-base64: ${{ secrets.GOOGLE_JSON_KEY }}
          artifact: aab
          skip-signing: false
          key-store-base64: ${{ secrets.KEY_STORE }}
          key-store-password: ${{ secrets.KEY_STORE_PASSWORD }}
          key-alias: ${{ secrets.KEY_ALIAS }}
          key-password: ${{ secrets.KEY_PASSWORD }} 
```

Refer to the following section for a complete list of inputs.

# Inputs

Refer to the `inputs` section in the file [`action.yml`](action.yml) for a complete list of variables that can be set,
overall these variables overlap with the expected environment variables by the fastlane pipeline (with a few small
differences such as files expecting base64 contents).

To get the contents of a file in base64 you can run the following command:

```shell
cat path/to/your/file.json | base64 
```

And just copy the output, additionally, if you're on Mac you can append `pbcopy` to copy the output directly to your
clipboard:

```shell
cat path/to/your/file.json | base64 | pbcopy
```

#### Variables

| Input                    | Description                                                                                                                                                                     | Required | Default   |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|:--------:|-----------|
| `build-lane`             | The build lane that should be executed (values: beta, release)                                                                                                                  |    ✓     |           |
| `java-version`           | JDK version ([see supported syntax](https://github.com/actions/setup-java#supported-version-syntax))                                                                            |          | `17`      |
| `enforced-branch`        | Branch to enforce, recommended (supports regex)                                                                                                                                 |          |           |
| `build-number`           | Which build number strategy to use: `store` to increment previous published build, `github` to use run number  as build number (default), or empty to use local auto-increment. |          | `github`  |
| `commit-increment`       | Whether or not to commit and push version increment                                                                                                                             |          | `false`   |
| `publish-build`          | Whether or not to publish build artifacts to the Play Store                                                                                                                     |          | `true`    |
| `upload-artifacts`       | Whether or not to upload output artifacts to GitHub Actions                                                                                                                     |          | `true`    |
| `package-name`           | Android app package name (e.g. com.example.application)                                                                                                                         |    ✓     |           |
| `google-json-key-base64` | Google Credentials JSON contents in base64 to upload artifacts (required if publishing is enabled)                                                                              |    *     |           |
| `artifact`               | The type of the artifact that should be produced (values: apk, aab)                                                                                                             |          | `aab`     |
| `flavor`                 | The build flavor that should be used                                                                                                                                            |          |           |
| `build-type`             | The build type that should be used                                                                                                                                              |          | `Release` |
| `skip-signing`           | Whether or not to skip build signing (may not work if `build-type` is Release)                                                                                                  |          | `false`   |
| `key-store-base64`       | Android Keystore data contents in base64 (required if signing is enabled)                                                                                                       |    *     |           |
| `key-store-password`     | Android Keystore password (required if signing is enabled)                                                                                                                      |    *     |           |
| `key-alias`              | Android Keystore key alias (required if signing is enabled)                                                                                                                     |    *     |           |
| `key-password`           | Android Keystore key password (required if signing is enabled)                                                                                                                  |    *     |           |

\* = _required based on other inputs_
