---
layout: default
title: Creating a Cmake Project
parent: Cmake
grand_parent: Project Setup
nav_order: 2
---

# Creating a Project

If you have ever seen a project that has a `CMakeLists.txt` file, then you have seen cmake in action.

Unlike other build systems, cmake does not build your project directly. Instead it generates project files for other build systems such as Visual Studio, or Make.

---

## Folder Structure

When you are creating a cmake project, you should create a folder structure for your project. This will make it easier to organize your project files.

Here is an example of a folder structure for a cmake project:

```xml
MyProject
├───build
├───CMakeLists.txt
└───src
    └───main.cpp
```


---

## Creating a Cmake Project

To create a cmake project, you first need to create a `CMakeLists.txt` file in the root directory of your project. This file will contain all of the information that cmake needs to generate project files for your project.

Here is an example of a `CMakeLists.txt` file:

```cmake
cmake_minimum_required(VERSION 3.5)

project(MyProject)

add_executable(MyProject src/main.cpp)
```

{: .note }
This cmake file is expecting a file called `src/main.cpp` to be in the root directory of the project.

This file tells cmake that the minimum version of cmake that is required to build this project is version 3.0, and that the project is called MyProject. It also tells cmake that the project has an executable called MyProject, and that the source file for the executable is `src/main.cpp`.

---

## Generating Project Files

To generate project files for your project, you first need to create a build directory in the root directory of your project. Then you need to open a terminal in the build directory, and run the following command:

```bash
cmake ..
```

You will need to run this command every time you change the `CMakeLists.txt` file.

You should now have project files for your project in the build directory. most often these project files will be for Visual Studio, but you can also generate project files for other IDEs and build systems.

```bash
cmake -G "Visual Studio 17 2022" ..
```

{: .note }
This command will generate project files for Visual Studio 2022.

```bash
cmake -G "Unix Makefiles" ..
```

{: .note }
This command will generate project files for Make.

---

## Building Your Project

If you didnt get a `.sln` file when you generated project files for your project, then you most likely have built your project using a build system such as Make. In this case you can build your project by running the following command in the build directory:

```bash
cmake --build .
```

{: .note }
This command will build your project using the default build system for your platform.
