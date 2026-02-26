---
name: hello-world
description: Generate a hello world program in a specified programming language
argument-hint: "[language]"
allowed-tools:

- Write

---

Create a "Hello, World!" program in **$ARGUMENTS**.

## Steps

1. **Determine the language**: If `$ARGUMENTS` is empty, ask the user which language to use.

2. **Write the program**: Create the simplest idiomatic "Hello, World!" program for the specified language. The program
   should print exactly `Hello, World!` to standard output. Use the most modern/current conventions for the language.

3. **Save the file**: Write it to the current working directory with the conventional filename and extension for that
   language (e.g., `hello.py` for Python, `Hello.java` for Java, `hello.rs` for Rust). If the language requires a
   specific filename convention (e.g., Java requires the filename to match the class name), follow that convention.

## Important

- Do not add unnecessary complexity — write the simplest idiomatic version.
- Do not create build files, project scaffolding, or additional configuration.
