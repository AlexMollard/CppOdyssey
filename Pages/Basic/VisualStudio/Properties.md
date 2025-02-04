---
layout: default
nav_order: 3
parent: Visual Studio
grand_parent: Project Setup
title: Project Properties
description: A comprehensive guide to Visual Studio project properties and configuration
last_modified_date: 2025-02-05
---

# Project Properties

Project properties are settings that control how your project is built. They are stored in the project file, which has the extension `.vcxproj`. You can edit the project properties by right-clicking on the project in the Solution Explorer and selecting Properties.
It can also be accessed from the Project menu after you have selected which project you wish to edit.

| Access Method | Screenshot |
|--------------|------------|
| Solution Explorer | ![Project Properties access via Solution Explorer](../../../assets/VisualStudioProjectProperties.png) |
| Project Menu | ![Project Properties access via Project Menu](../../../assets/VisualStudioProjectPropertiesMenu.png) |

You should see a window that looks like this:

![Project Properties Window showing configuration options](../../../assets/VisualStudioProjectPropertiesWindow.png)

## Before You Start

Before editing the project properties, ensure you have the correct configuration selected. Click on the dropdown menu at the top of the window to select the configuration you want to edit.
Most developers work primarily with the Debug configuration during development.
However, a common mistake is adding libraries only to the Debug configuration, leading to errors when building Release or x86 versions.

For this guide, we will focus on the Debug configuration.

![Configuration selector in Project Properties](../../../assets/VisualStudioProjectPropertiesConfiguration.png)

## Properties Overview

While there are numerous properties available, we'll cover the most essential ones. For properties not listed here, consult the [Visual Studio Documentation](https://docs.microsoft.com/en-us/visualstudio/ide/reference/project-properties-reference).

### General Properties

| Property | Description |
|----------|-------------|
| Configuration Type | Decides what kind of program you're making. Think of it like choosing between making a LEGO piece that connects to other pieces (Static Library), a plugin that can be swapped in and out (Dynamic Library), or a complete standalone program (Application). Your choice here affects how others can use your code and how you'll distribute it. |
| Windows SDK Version | This is like choosing which version of Windows you want your program to work with. Newer versions give you access to newer Windows features but might not work on older Windows computers. If you want your program to work on older Windows versions, pick an older SDK. Most new projects should use "Latest". |
| Platform Toolset | This is picking which "workshop" of tools you'll use to build your program. The default (MSVC) is like using Microsoft's official toolkit. Clang is like using a different brand that sometimes gives clearer error messages. Your choice here affects how your code gets turned into a program and what kind of error messages you'll see. |
| C++ and C Language Standard | This is like choosing which "edition" of C++ you want to use. Newer versions (like C++20) give you fancy new features but might not work with older compilers. Think of it like choosing between an older, widely-supported version of Word vs a newer version with cool features that not everyone can open. |

#### Configuration Types
- Static Library (.lib)
  - Linked directly into executables
  - Increases executable size
  - No runtime dependencies
  - Ideal for small, self-contained libraries

- Dynamic Library (.dll)
  - Loaded at runtime
  - Independent updates possible
  - Reduces executable size
  - Requires distribution with application

- Application (.exe)
  - Standalone executable
  - Can depend on static or dynamic libraries
  - Requires `main()` or `WinMain()` entry point

{: .note }
Configuration Type affects how your code is built and distributed. Static libraries are embedded in executables, while DLLs are separate files loaded at runtime.

### Platform and Standards

#### Language Standard Options
- C++14 - Base standard, widely supported
- C++17 - Adds filesystem, optional, variant
- C++20 - Adds concepts, ranges, coroutines
- C++23 - Latest features, may have limited compiler support

{: .warning }
Using newer language standards might limit compatibility with older compilers and platforms.

#### SDK Version Guidelines
- Use "Latest" for new projects
- Specify exact version for older Windows targets
- Consider minimum supported Windows version

{: .note }
The Windows SDK version affects which Windows APIs are available to your application.

### VCPKG Integration

| Property | Description |
|----------|-------------|
| Use VCPKG Manifest | Enables automatic library installation via manifest file |
| Use Static Libraries | Links libraries statically at compile time |

VCPKG is a package manager for C++ libraries. If not installed with Visual Studio, you can get it from the [VCPKG Repository](https://github.com/microsoft/vcpkg).

### C/C++ Configuration

#### General Settings

| Property | Description |
|----------|-------------|
| Additional Include Directories | Think of this like telling your program where to find its "instruction manuals" (header files). If you're using external libraries, you'll need to tell Visual Studio where these files are on your computer. For example, if you're using SDL2, you'd add the folder where SDL2's headers are stored. Without this, your program won't know how to use the library! |
| Debug Information Format | This is like choosing how detailed your program's "map" should be when you're trying to find bugs. Program Database (PDB) is the most common choice - it helps Visual Studio show you exactly where problems are in your code when something goes wrong. Without this, finding bugs becomes like searching in the dark. |
| Support Just My Code Debugging | When turned on, this lets you focus on debugging your own code while skipping over library code. It's like having a "fast forward" button that jumps past all the complex library stuff when you're trying to find a bug in your code. Super helpful when you're learning! |
| Warning Level | This controls how picky Visual Studio should be about potential problems in your code. Level 4 is like having a really strict teacher who points out every little thing that could be improved. Level 1 is more relaxed but might miss important issues. For beginners, Level 3 is a good balance. |
| SDL Checks | These are security checks that help prevent common programming mistakes that hackers could exploit. Think of it like having a security guard checking your code for common vulnerabilities. Keep this on unless you have a very good reason not to! |
| Multi-processor Compilation | This lets Visual Studio use multiple CPU cores to build your program faster, like having multiple workers building different parts of your LEGO set at the same time. However, it doesn't work with precompiled headers (another speed-up technique). |

{: .warning }
Multi-processor Compilation conflicts with Precompiled Headers. Choose one or disable /mp for PCH files.

#### Code Generation Settings

| Property | Description |
|----------|-------------|
| Basic Runtime Checks | These are like safety nets that catch common programming mistakes while your program is running. They can catch things like using uninitialized variables (trying to use a variable before giving it a value) or buffer overflows (trying to store too much data in a fixed space). These checks make your program run slower but are super helpful during development! |
| Runtime Library | This is choosing how Windows features get included in your program. The Debug DLL version (/MDd) is best while developing - it includes all the checking code to help find bugs. The Release DLL version (/MD) is for your final program - it's faster but harder to debug. Static versions (/MT, /MTd) include everything in your .exe file, making it bigger but easier to distribute. |
| Floating Point Precision | This affects how accurately your program handles decimal numbers. 'Precise' is like doing math with a calculator - you get exact answers but it's slower. 'Fast' is like doing math in your head - you get answers quickly but they might be slightly off. For most programs, stick with 'Precise' unless you know you need the speed boost. |

{: .note }
Runtime checks are valuable during development but impact performance. Consider your debug/release needs carefully.

### Linker Configuration

#### General Settings

| Property | Description |
|----------|-------------|
| Additional Library Directories | This tells Visual Studio where to find the library files (.lib files) that your program needs. It's like giving Visual Studio a map to find all the pre-built code you want to use. For example, if you're using SDL2, you need to tell Visual Studio where the SDL2.lib file is on your computer. |
| Additional Dependencies | This is the list of actual library files your program needs to work. If Additional Library Directories is like giving directions to a library, this is like listing the specific books you need. For example, if you're using SDL2, you'd add 'SDL2.lib' here. Without the correct libraries listed here, your program won't build! |

## Troubleshooting Common Issues

### Linker Errors

Most linker errors stem from missing or misconfigured library dependencies. To resolve:

1. Check Project Properties > Linker > Input
2. Verify Additional Dependencies
3. Confirm library paths in Library Directories

{: .note }
Most linker errors resolve through proper library configuration.

### Header File Issues

When encountering missing header errors:

1. Verify Project Properties > C/C++ > General
2. Add header directories to Additional Include Directories
3. Check header filename casing

{: .warning }
Header paths are case-sensitive on some platforms. Verify exact filename casing.

## Conclusion

This guide covers the essential project properties in Visual Studio. With these configurations understood, you can focus on writing code without frequent property adjustments.

For questions or suggestions, reach out via:
- Email: [alexmollard@protonmail.com](mailto:alexmollard@protonmail.com)
- LinkedIn: [LinkedIn Profile](https://www.linkedin.com/in/alex-mollard/)
