---
layout: default
nav_order: 3
parent: Cmake
grand_parent: Project Setup
title: Linking Libraries
---

# Linking Libraries

When you are building a project, you may want to use a library that is not part of the standard library. In order to use a library, you need to link it to your project.

There are two ways to link a library to your project: static linking and dynamic linking.

---

## Static Linking

Static linking is the process of linking a library to your project at compile time. This means that the library is included in your project's executable file.

Static linking is the most common way to link a library to your project. It is also the easiest way to link a library to your project.

## Dynamic Linking

Dynamic linking is the process of linking a library to your project at runtime. This means that the library is not included in your project's executable file.

Dynamic linking is less common than static linking, but it is still used in some cases. For example, if you want to use a library that is not available on all platforms, you may want to use dynamic linking.

### Cmake Example


```cmake
add_executable(MyProject main.cpp)

target_link_libraries(MyProject PRIVATE MyLibrary)
```

{: .note }
When linking a static or shared library, you need to specify the name of the library without the `lib` prefix and the `.a` or `.so` suffix.
Most often this will be mentioned in the libs readme file.

---

## Automatic Linking

When you are linking a library to your project, you may want to link it automatically. This means that you do not need to specify the library name in your project's `CMakeLists.txt` file.

To link a library automatically, you need to add the following line to your project's `CMakeLists.txt` file:

```cmake
find_package(MyLibrary REQUIRED)
```

{: .warning }
This will only work if the library has a `FindMyLibrary.cmake` file in the `cmake` directory of the library.
Make sure that the library you are trying to link has a `FindMyLibrary.cmake` and is in the `cmake` directory of the library or in a environment variable called `CMAKE_MODULE_PATH`.

---

## Include my cmake in that cmake

What if your cmake project depends on another cmake project? You can include the other cmake project in your cmake project by adding the following line to your project's `CMakeLists.txt` file: 

```cmake
add_subdirectory(MyLibrary)
```

{: .note }
This will add the `CMakeLists.txt` file in the `MyLibrary` directory to your project's `CMakeLists.txt` file.