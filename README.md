# library-release-action

A GitHub action to automate the following:

* Gradle checks
* Publication to Maven Central
* Change log generation (and commit) relying on the [GitHub Changelog Gradle Plugin](https://github.com/joffrey-bion/gradle-github-changelog)
* GitHub release creation (with the change log contents)

## Requirements

* The project must be a Gradle project
* It must apply the [GitHub Changelog Gradle Plugin](https://github.com/joffrey-bion/gradle-github-changelog), configured with the `futureReleaseVersion` set to the current project version
* It must apply the [Gradle Nexus Publish Plugin](https://github.com/gradle-nexus/publish-plugin), configured to publish to a Sonatype Nexus repository (usually OSSRH for publications to Maven Central)
* It must apply the [Signing Plugin](https://docs.gradle.org/current/userguide/signing_plugin.html), configured with [ASCII-armored in-memory GPG keys](https://docs.gradle.org/current/userguide/signing_plugin.html#sec:in-memory-keys)
