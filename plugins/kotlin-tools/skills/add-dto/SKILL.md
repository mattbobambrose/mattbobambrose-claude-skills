---
name: add-dto
description: Create a Kotlin serializable DTO data class from a JSON string
argument-hint: [FileName.kt] [json-string]
allowed-tools:

- Read
- Write
- Edit
- Glob

---

Create a Kotlin serializable DTO from the provided arguments.

- **$0** is the Kotlin file name (e.g., `UserResponse.kt`)
- **$1** (and everything after the file name) is the JSON string

## Steps

1. **Read the project structure**: Read `src/main/kotlin/Main.kt` to determine the package name. Use that same package
   declaration in the new file.

2. **Parse the JSON**: Analyze the JSON string to determine field names and types. Map JSON types to Kotlin types:
    - JSON string → `String`
    - JSON number (integer) → `Int` (use `Long` if the value exceeds Int range)
    - JSON number (decimal) → `Double`
    - JSON boolean → `Boolean`
    - JSON array → `List<T>` where `T` is inferred from array elements
    - JSON object → Create a separate top-level `@Serializable` data class (see Important section)
    - JSON null → This should NOT happen since all fields must be non-nullable. If null appears as the only value,
      default to `String` and add a TODO comment.

3. **Generate the data class**:
    - Annotate with `@Serializable` from `kotlinx.serialization`
    - ALL fields must be non-nullable `val` properties
    - For JSON keys containing underscores (e.g., `first_name`), annotate the property with `@SerialName("first_name")`
      and use camelCase in the Kotlin property name (e.g., `firstName`)
    - For JSON keys that are already camelCase or have no underscores, do NOT add a `@SerialName` annotation
    - The class name should be derived from the file name (minus the `.kt` extension)

4. **Write the file**: Save to `src/main/kotlin/<FileName>.kt` (using the file name from `$0`).

## Example

Given: `/add-dto UserProfile.kt {"user_name": "alice", "email": "a@b.com", "is_active": true, "login_count": 5}`

Generate:

```kotlin
import kotlinx.serialization.SerialName
import kotlinx.serialization.Serializable

@Serializable
data class UserProfile(
  @SerialName("user_name") val userName: String,
  val email: String,
  @SerialName("is_active") val isActive: Boolean,
  @SerialName("login_count") val loginCount: Int,
)
```

## Important

- Use 2-space indentation per project convention.
- Only import `SerialName` if at least one field needs it.
- Do NOT use inner or nested classes. For nested JSON objects, create separate top-level data classes in the same file.
- Use a trailing comma on the last constructor parameter.
