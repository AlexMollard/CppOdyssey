---
layout: default
nav_order: 5
parent: Vulkan
title: Code Structure
---

# Vulkan Code Structure

As you have seen in the previous chapter, Vulkan is a very low-level API. This means that it is very verbose and requires a lot of code to get something done. In this chapter, we will take a look at the code structure of a Vulkan application and how to set up the Vulkan instance, device and all the other things that are required to get started.

As of now we have created a Vulkan instance, Selected a device and created a logical device. We have also created a surface and a window using the windows API.

## Folder Structure

The folder structure of the project for this guide should be as follows:

```bash
├── src
│   └── main.cpp
│   └── vulkan
│       ├── Container.h
│       ├── Container.cpp
│       ├── Common.h
│       ├── Common.cpp
│       ├── Application.h
│       ├── Application.cpp
│       ├── SwapChain.h
│       ├── SwapChain.cpp
│       ├── Device.h
│       ├── Device.cpp
│       ├── Pipeline.h
│       ├── Pipeline.cpp
│       ├── Shader.h
│       ├── Shader.cpp
```

| File | Description |
| --- | --- |
| `main.cpp` | The main entry point of the application. |
| `Container.h` | This will be where we will store a singleton that will contain all the vulkan objects. |
| `Common.h` | Contains common includes and macros. |
| `Application.h` | Contains the application class, used to create the window and the vulkan instance.
| `SwapChain.h` | Contains the swapchain class, used to create the swapchain and the image views. |
| `Device.h` | Contains the device class, used to create the logical device and the command pool. |
| `Pipeline.h` | Contains the pipeline class, used to create the render pass and the graphics pipeline. |
| `Shader.h` | Contains the shader class, used to load the shaders into shader modules. |

{: .note}
**Some files we wont be using for a while, but they are included for completeness.**