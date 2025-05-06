# gradle-library-release-action

A GitHub action to automate the following:

* Gradle checks
* Publication to Sonatype (usually OSSRH, so it gets released to Maven Central)
* Change log generation (and commit) relying on the [GitHub Changelog Gradle Plugin](https://github.com/joffrey-bion/gradle-github-changelog)
* GitHub release creation (with the change log contents)

## Requirements

* The project must be a Gradle project
* It must apply the [GitHub Changelog Gradle Plugin](https://github.com/joffrey-bion/gradle-github-changelog), 
  configured with the `futureReleaseVersion` set to the current project version
* It must apply either:
  * Option 1: the [Gradle Maven Publish Plugin](https://github.com/vanniktech/gradle-maven-publish-plugin) by Vanniktech
  * Option 2: both of the following plugins:
    * the [Gradle Nexus Publish Plugin](https://github.com/gradle-nexus/publish-plugin), configured to publish to a
      Sonatype Nexus repository (usually OSSRH for publications to Maven Central)
    * the [Signing Plugin](https://docs.gradle.org/current/userguide/signing_plugin.html), configured with 
      [ASCII-armored in-memory GPG keys](https://docs.gradle.org/current/userguide/signing_plugin.html#sec:in-memory-keys)
      (this is automatically done by the Vanniktech plugin) 

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
        uses: actions/checkout@v4

      - name: Set up JDK
        uses: actions/setup-java@v4
        with:
          distribution: 'temurin'
          java-version: 21

      - name: Release
        uses: joffrey-bion/gradle-library-release-action@v2
        with:
          version: ${{ inputs.version }}
          gpg-signing-key: ${{ secrets.GPG_SECRET_ASCII_ARMORED }}
          gpg-signing-password: ${{ secrets.GPG_PASSWORD }}
          sonatype-username: ${{ secrets.OSSRH_TOKEN_USERNAME }}
          sonatype-password: ${{ secrets.OSSRH_TOKEN_PASSWORD }}
          # Uncomment the following if you're using the Vanniktech Gradle Maven Publish plugin:
          #gradle-publish-tasks: publishAndReleaseToMavenCentral
```
