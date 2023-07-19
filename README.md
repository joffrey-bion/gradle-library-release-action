# gradle-library-release-action

A GitHub action to automate the following:

* Gradle checks
* Publication to Sonatype (usually OSSRH, so it gets released to Maven Central)
* Change log generation (and commit) relying on the [GitHub Changelog Gradle Plugin](https://github.com/joffrey-bion/gradle-github-changelog)
* GitHub release creation (with the change log contents)

## Requirements

* The project must be a Gradle project
* It must apply the [GitHub Changelog Gradle Plugin](https://github.com/joffrey-bion/gradle-github-changelog), configured with the `futureReleaseVersion` set to the current project version
* It must apply the [Gradle Nexus Publish Plugin](https://github.com/gradle-nexus/publish-plugin), configured to publish to a Sonatype Nexus repository (usually OSSRH for publications to Maven Central)
* It must apply the [Signing Plugin](https://docs.gradle.org/current/userguide/signing_plugin.html), configured with [ASCII-armored in-memory GPG keys](https://docs.gradle.org/current/userguide/signing_plugin.html#sec:in-memory-keys)

## Usage

Here is an example workflow built with this action:

```yaml
name: release

on:
  workflow_dispatch:
    inputs:
      version:
        description: "New version"
        required: true
jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Set up JDK 17
        uses: actions/setup-java@v3
        with:
          distribution: 'temurin'
          java-version: 17

      - name: Release
        uses: joffrey-bion/gradle-library-release-action@v1
        with:
          version: ${{ inputs.version }}
          gpg-signing-key: ${{ secrets.GPG_SECRET_ASCII_ARMORED }}
          gpg-signing-password: ${{ secrets.GPG_PASSWORD }}
          sonatype-username: ${{ secrets.OSSRH_USER_TOKEN }}
          sonatype-password: ${{ secrets.OSSRH_KEY }}
```
