---
layout: default
title: Cmake
parent: Project Setup
has_children: true
nav_order: 3
---

# Cmake

Cmake is a cross-platform build system that is used to generate project files for other build systems such as Visual Studio, or Make.

---

## Installing Cmake

To install cmake, you can go to the [cmake download page](https://cmake.org/download/), and download the installer for your platform.

---

## Why Cmake?

Cmake is cross-platform, which means that it can be used on Windows, Linux, and Mac OS X.
It also has a lot of features that make it easier to use than other build systems such as Make.

### Why dont we use Visual Studio?

Visual Studio is a great IDE, but it is not cross-platform. This means that you cannot use it on Linux or Mac OS X.
Visual studio can be quite slow and resource intensive, and it is not very customizable and what if you want to use a different IDE?
Using Visual studio will automatically lock your project into the Windows platform, and you will not be able to use it on Linux or Mac OS X.

{: .note }
It isnt impossible to use Visual Studio on Linux or Mac OS X, but it is not easy.
And you can always convert a `.vcxproj` file to a `Makefile` using cmake.
