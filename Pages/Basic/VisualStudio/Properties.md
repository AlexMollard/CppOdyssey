---
layout: default
nav_order: 3
parent: Visual Studio
grand_parent: Project Setup
title: Project Properties
---

# Project Properties

Project properties are settings that control how your project is built. They are stored in the project file, which has the extension `.vcxproj`. You can edit the project properties by right-clicking on the project in the Solution Explorer and selecting Properties.
It can also be accessed from the Project menu after you have selected which project you wish to edit.

| Solution Explorer | Project Menu |
|-------------------|--------------|
|![Visual Studio Project Properties](../../../assets/VisualStudioProjectProperties.png)|![Visual Studio Project Properties Menu](../../../assets/VisualStudioProjectPropertiesMenu.png) |

You should see a window that looks like this:

![Visual Studio Project Properties Window](../../../assets/VisualStudioProjectPropertiesWindow.png)

## Before you start

Before you start editing the project properties, you should make sure that you have the correct configuration selected. You can do this by clicking on the dropdown menu at the top of the window and selecting the configuration you want to edit.
In most cases, you will want to edit the Debug configuration, as this is the configuration that is used when you are debugging your project.
But I have seen alot of people edit just the debug and add libraries to only the debug config so when they try build for release or even x86 they get errors.

For this guide, we will be editing the Debug configuration.

![Visual Studio Project Properties Configuration](../../../assets/VisualStudioProjectPropertiesConfiguration.png)

## Properties

There are alot of properties that you can edit, but we will only be covering the most important ones. If I havent listed a property that you can see in the project properties window, then you can safely ignore it. or you can look it up in the [Visual Studio Documentation](https://docs.microsoft.com/en-us/visualstudio/).

### General

| Property | Description |
|----------|-------------|
| Configuration Type | Determines the output type of your project:<br>• Static Library (.lib) - Linked directly into executables<br>• Dynamic Library (.dll) - Loaded at runtime<br>• Application (.exe) - Executable program |
| Windows SDK Version | Sets the Windows SDK version for compilation:<br>• Latest - Uses most recent installed SDK<br>• Specific versions (e.g., 10.0.19041.0) - For targeting specific Windows versions |
| Platform Toolset | Selects the compiler toolchain:<br>• MSVC (Default) - Microsoft's native compiler<br>• Clang/LLVM - Alternative compiler with enhanced diagnostics<br>• Intel C++ - Optimized for Intel processors |
| C++ and C Language Standard | Controls language feature availability:<br>• C++: 14 (Default), 17, 20, 23<br>• C: C17 (Default), C11, C99 |

{: .note }
Configuration Type affects how your code is built and distributed. Static libraries are embedded in executables, while DLLs are separate files loaded at runtime.

### Configuration Type Details

#### Static Library (.lib)
- Linked directly into the final executable
- Increases executable size
- No runtime dependencies
- Good for small, self-contained libraries

{: .note }
Static libraries increase executable size but eliminate DLL dependency management.

#### Dynamic Library (.dll)
- Loaded at runtime
- Can be updated independently
- Reduces executable size
- Must be distributed with application

{: .warning }
DLLs require careful version management and proper distribution with your application.

#### Application (.exe)
- Standalone executable
- Can depend on static or dynamic libraries
- Entry point requires `main()` or `WinMain()`

### Platform and Standards

{: .note }
Clang often provides more detailed and clearer error messages compared to MSVC.

#### Language Standard Selection
- C++14 - Base standard, widely supported
- C++17 - Adds filesystem, optional, variant
- C++20 - Adds concepts, ranges, coroutines
- C++23 - Latest features, may have limited compiler support

{: .warning }
Using newer language standards might limit compatibility with older compilers and platforms.

#### SDK Version Guidelines
- Use "Latest" for new projects
- Specify exact version when targeting older Windows versions
- Consider minimum supported Windows version for deployment

{: .note }
The Windows SDK version affects which Windows APIs are available to your application.

### VCPKG

VCPKG is a package manager for C++ libraries. It is used to install libraries that are not included with Visual Studio. If it was not correctly installed with Visual studio you can install VCPKG from the [VCPKG GitHub Repository](https://github.com/microsoft/vcpkg).

| Property | Description |
|----------|-------------|
| Use VCPKG Manifest | This property determines whether or not you are using a VCPKG manifest file. A VCPKG manifest file is a file that contains a list of libraries that you want to install with VCPKG. This will mean that when you build your project, VCPKG will automatically install the libraries that you have specified in the manifest file, Useful if you don't wish to manage all your dependencies yourself. |
| Use Static Libraries | This property determines whether or not you are using static libraries. A static library is a library that is linked to your project at compile time. This means that you don't need to distribute the library with your project. This is generally the best option for most projects. |

### C/C++ > General

## General Settings

| Property | Description |
|----------|-------------|
| Additional Include Directories | Specifies directories to include when compiling your project. Useful for including header files located outside the source folder, such as library dependencies. |
| Debug Information Format | Controls the debug information output format. Options include: <br>• Program Database (PDB) - Default format, compatible with MSVC compiler<br>• Portable Database - Compatible with multiple compilers but may cause linking errors with MSVC |
| Support Just My Code Debugging | Enables debugging of your code while skipping through library code. Particularly useful when working with third-party libraries without source code access. |
| Warning Level | Sets compiler warning severity:<br>• Level 3 (Default) - Most strict, recommended<br>• Level 1 - Least strict<br>• Levels 2 and 4 also available |
| SDL Checks | Security Development Lifecycle checks help identify security vulnerabilities and coding issues. Enabled by default and recommended for secure code development. |
| Multi-processor Compilation | Utilizes multiple CPU cores during compilation to improve build speed. |

{: .warning }
Multi-processor Compilation is incompatible with Precompiled Headers. Disable one or the other.

{: .note }
Using Warning Level 4 might generate many warnings initially but helps maintain high code quality.

### Important Notes
- It's recommended to use the highest warning level possible to catch potential issues early
- Keep SDL checks enabled for better code security
- When using MSVC, stick to the Program Database (PDB) format to avoid linking errors
- Enable Multi-processor Compilation when not using Precompiled Headers to reduce build times (This can be worked around by disabling /mp on your pch.h and pch.cpp file)

### C/C++ > Preprocessor

| Property | Description |
|----------|-------------|
| Preprocessor Definitions | This property determines what preprocessor definitions you want to use. A preprocessor definition is a macro that is defined before the code is compiled. This is useful if you want to define a macro that is used in multiple source files. (I would recommend using preprocessor definitions sparingly, as they can make your code harder to read and can cause unexpected behaviour) |

Thats really the only property I would recommend changing in the Preprocessor, as the rest are not really that important.

### C/C++ > Code Generation

C/C++ > Code Generation

| Property | Description |
|----------|-------------|
| Basic Runtime Checks | Controls runtime error detection mechanisms:<br>• Stack Frame Checks - Detects stack pointer corruption<br>• Buffer Security Checks - Identifies buffer overflows<br>• Uninitialized Variables - Catches use of uninitialized memory<br>• Runtime Error Checks - General runtime error detection |
| Runtime Library | Determines which C/C++ runtime library to link against:<br>• Multi-threaded Debug DLL (/MDd) - Default, links with MSVCRT debug DLL<br>• Multi-threaded Debug (/MTd) - Static linking with debug runtime<br>• Multi-threaded (/MT) - Static linking with release runtime<br>• Multi-threaded DLL (/MD) - Links with MSVCRT release DLL |
| Floating Point Precision | Sets floating-point optimization level:<br>• Precise (/fp:precise) - Default, maintains strict floating-point semantics<br>• Fast (/fp:fast) - Enables aggressive optimizations, may affect accuracy<br>• Strict (/fp:strict) - Enforces strictest floating-point behaviour |

{: .note }
Runtime checks can impact performance. Consider your debug/release configuration needs carefully.

### Usage Guidelines

#### Runtime Checks
- Keep enabled during development and debugging
- Consider disabling specific checks in release builds for performance
- Useful for catching memory corruption issues early

{: .warning }
Disabling runtime checks in debug builds can make certain bugs much harder to diagnose.

#### Runtime Library Selection
- `/MDd` (Debug DLL) - Recommended for development
- `/MD` (Release DLL) - Standard for release builds
- Use static linking (`/MT`, `/MTd`) when distributing standalone executables

{: .note }
Mixing different runtime libraries in the same project can lead to hard-to-diagnose crashes and memory corruption.

#### Floating Point Precision
- **Precise**: Scientific calculations, financial software
- **Fast**: Games, graphics, non-critical calculations
- **Strict**: When binary reproducibility is required

{: .warning }
Using `/fp:fast` can produce different results across different CPU architectures. Test thoroughly if portability is important.

### Linker > General

These properties are used to determine what libraries you want to link against. A library is a collection of object files that are linked into your project at compile time. This means that you dont need to distribute the library with your project.

| Property | Description |
|----------|-------------|
| Additional Library Directories | This property determines what directories you want to include when linking your project. This is useful if you have libraries in a directory that is not in the source folder. Such as a directory that contains libraries for a library that you are using. |

I would probably recommend leaving the rest of the properties in the Linker > General section alone, as they are fine left at their default values.

### Linker > Input

| Property | Description |
|----------|-------------|
| Additional Dependencies | This property determines what libraries you want to link against. This is useful if you have libraries that are not in the source folder. Such as a library that you are using. (eg. `SDL2.lib`) |

---

## Common Issues

### Linker Errors
Linker errors often appear overwhelming due to their verbose output, but they typically have straightforward solutions. The most common cause is missing library dependencies. 

{: .note }
Most linker errors can be resolved by correctly configuring library dependencies and paths.

To resolve linker errors:
1. Navigate to Project Properties > Linker > Input
2. Add required libraries to the Additional Dependencies property
3. Verify library paths are correct in Library Directories

### Missing Header Files
When encountering missing header file errors, the compiler cannot locate necessary include files. 

{: .warning }
Case sensitivity matters in include paths on some platforms. Always verify exact filename casing.

To resolve header file errors:
1. Navigate to Project Properties > C/C++ > General
2. Add the directory containing your header files to Additional Include Directories
3. Verify header filenames and paths are correct in your #include statements

### Symbol Not Found Errors
Symbol not found errors typically occur when the linker cannot locate function or variable definitions.

{: .note }
Debug and Release builds use different library name suffixes (e.g., 'd' suffix for debug versions).

To resolve symbol not found errors:
1. Check that all required libraries are listed in Additional Dependencies
2. Verify function declarations match their definitions
3. Ensure libraries are compatible with your build configuration (Debug/Release)

### Common Troubleshooting Tips
- Make sure library and include paths use consistent forward/backward slashes
- Check that library versions match your project's architecture (x86/x64)
- Verify that imported libraries are built with compatible compiler settings

{: .warning }
Mixing Debug and Release libraries can lead to runtime crashes that are difficult to diagnose.

---

## Conclusion

Now you know how to edit the project properties in Visual Studio. You can now start writing code for your project and hopefully not have to worry about the project properties again.

If you have any questions or suggestions, feel free to reach out to me either by [email](mailto: <alexmollard@protonmail.com>) or on [LinkedIn](https://www.linkedin.com/in/alex-mollard/), as im aware how confusing this can be for new users.
