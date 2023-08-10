<!-- Badges -->
[![Issues](https://img.shields.io/github/issues/paulushcgcj/action-java-publish)](/../../issues)
[![Pull Requests](https://img.shields.io/github/issues-pr/paulushcgcj/action-java-publish)](/../../pulls)
[![MIT License](https://img.shields.io/github/license/paulushcgcj/action-java-publish.svg)](/LICENSE)
[![Lifecycle](https://img.shields.io/badge/Lifecycle-Experimental-339999)](https://github.com/bcgov/repomountie/blob/master/doc/lifecycle-badges.md)

# Java package build and publish

This action publishes a maven or gradle project. it will set the version and can generate javadocs and sources jar along with the actual artifact publication. It adds as default the github package repository

This is useful in CI/CD pipelines where you need to publish an artifact, such as a library

This tool is currently strongly opinionated and relies on configuration being present inside your pom/gradle.build file. **IT DOES NOT SUPPORT KOTLIN GRADLE FILES**


# Usage

```yaml
- uses: paulushcgcj/action-java-publish@vX.Y.Z
  with:
    ### Required


    
    # App/package directory
    dir: .
    
    # App/Package version that will be set
    app-version: 1.0.0
    
    ### Typical / recommended

    # Java package manager cache, defaults to maven
    java-cache: maven

    # Java distribution, defaults to temurin
    java-distribution: temurin

    # Java version, defaults to 17 (LTS)
    java-version: "17"
    
    # Add sources to the release
    add-sources: false
    
    # Add javadoc to the release
    add-javadoc: false
    
    # Extra parameters to pass version set command, usually to ignore tests
    extra-params:

    # Commands to run before version is set, start with '|' for multi-line
    commands: |
        mvn clean

    ### Usually a bad idea / not recommended

    # Repository to clone and process
    # Useful for consuming other repos, like in testing
    # Defaults to the current one
    repository: ${{ github.repository }}
```

# Example, build a maven package using java 17 at the root of the repo

Build a maven package using java 17 at the root of the repo using the default parameters for most of things.

Create or modify a GitHub workflow, like below.  E.g. `./github/workflows/pr-open.yml`

```yaml
name: Pull Request

on:
  pull_request:

concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

jobs:
  builds:
    permissions:
      packages: write
    runs-on: ubuntu-22.04
    steps:
      - uses: actions/checkout@v3
      - name: Builds
        uses: paulushcgcj/action-java-publish@vX.Y.Z
        with:
          dir: .
          app-version: ${{ needs.pr-validation.outputs.version }}.PR${{ github.event.number }}
        env:
          GITHUB_TOKEN: ${{ github.token }}
```

# Example, Gradle build

Build a gradle application/package and publish it

Create or modify a GitHub workflow, like below.  E.g. `./github/workflows/pr-open.yml`

```yaml
name: Pull Request

on:
  pull_request:

concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

jobs:
  builds:
    permissions:
      packages: write
    runs-on: ubuntu-22.04
    steps:
      - uses: actions/checkout@v3
      - name: Builds
        uses: paulushcgcj/action-java-publish@vX.Y.Z
        with:
          dir: library
          app-version: ${{ needs.pr-validation.outputs.version }}.PR${{ github.event.number }}
          java-cache: gradle
        env:
          GITHUB_TOKEN: ${{ github.token }}
          USERNAME: ${{ github.repository_owner }}
```

# Permissions

Workflows kicked off by Dependabot or a fork run with reduced permissions.  That can be addressed by setting explict permissions for the GITHUB_TOKEN.  If this is not required, then remove the lines below from these examples.

```yaml
permissions:
  packages: write
```

<!-- # Acknowledgements

This Action is provided courtesty of the Paulo Gomes da Cruz Junior -->