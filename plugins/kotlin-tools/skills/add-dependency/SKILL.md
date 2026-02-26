---
name: add-dependency
description: Add a Maven dependency to the Kotlin project's Gradle version catalog and build file
argument-hint: "<group:artifact> [group:artifact ...]"
allowed-tools:

- Bash
- Read
- Edit
- WebSearch
- WebFetch

---

Add one or more Maven dependencies — **$ARGUMENTS** — to this Kotlin/Gradle project. If multiple
`group:artifact` coordinates are given (space-separated), process them all before rebuilding.

## Steps

1. **Parse the dependency list**: Split `$ARGUMENTS` on whitespace to get one or more `group:artifact` coordinates.
   Process every item in the list.

2. **Find the latest version**: For each dependency, search Maven Central (https://search.maven.org) or the web for
   the latest stable release version. Do NOT use snapshot, milestone, alpha, beta, or RC versions unless no stable
   version exists.

3. **Read the current version catalog**: Read `gradle/libs.versions.toml` once to understand the existing structure and
   naming conventions before making any edits.

4. **Add each dependency to the version catalog** (`gradle/libs.versions.toml`):
    - Add a new version entry in the `[versions]` section using a short, conventional alias derived from the artifact
      name (e.g., `io.ktor:ktor-client-core` → version alias `ktor` if it doesn't already exist, or a more specific
      name if it does).
    - Add a new library entry in the `[libraries]` section following the existing naming pattern (hyphen-separated,
      e.g., `ktor-client-core = { module = "io.ktor:ktor-client-core", version.ref = "ktor" }`).
    - If a version entry already exists for this group (e.g., the group already has other dependencies at a shared
      version), reuse it instead of creating a duplicate.
    - Apply all catalog edits before moving on to `build.gradle.kts`.

5. **Add each dependency to build.gradle.kts**: For each dependency, add an `implementation(libs.<alias>)` line in the
   `dependencies` block of `build.gradle.kts`, using the catalog alias (hyphens and dots in the TOML key become dots
   in the accessor, e.g., `ktor-client-core` → `libs.ktor.client.core`). Use `implementation` unless the dependency is
   clearly test-only (in which case use `testImplementation`).

6. **Rebuild the project**: Run `./gradlew clean build -x test` once after all dependencies have been added to verify
   they all resolve and the project compiles. If the build fails, diagnose and fix the issue (e.g., wrong artifact
   name, version incompatibility) before reporting success.

## Important

- Always prefer the version catalog approach — never add raw dependency strings directly in `build.gradle.kts`.
- Maintain alphabetical ordering within each section of `libs.versions.toml` if the existing file follows that
  convention.
- If the artifact name provided is incomplete or ambiguous, search for the correct full `group:artifact` coordinates
  before proceeding.
