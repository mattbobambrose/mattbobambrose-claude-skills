---
name: hello-world
description: Generate a hello world program in a specified programming language
argument-hint: "[language]"
allowed-tools:

- Write

---

Create the simplest idiomatic "Hello, World!" program in **$ARGUMENTS**.

If no language is specified, ask the user which language to use.

- Print exactly `Hello, World!` to standard output
- Use the conventional filename and extension for the language (e.g., `hello.py`, `Hello.java`, `hello.rs`)
- Follow the language's filename conventions (e.g., Java requires the filename to match the class name)
- Use the most modern/current conventions for the language
- Do not add build files, project scaffolding, or unnecessary complexity
