---
layout: default
title: Setting up Vulkan
parent: Vulkan
nav_order: 2
---

# Setting up Vulkan

Quick Links:

- [Sample Code](#sample-code)
- [Visual Studio](#visual-studio)
- [CMake](#cmake)

## Installing the Vulkan SDK

1. Go to [https://vulkan.lunarg.com/sdk/home](https://vulkan.lunarg.com/sdk/home)
2. Download the latest version of the Vulkan SDK
3. Run the installer
4. Select the components that you want to install
5. Click `Install`

## Sample Code

By the end of this guide, you should be able to run the following code, although this code wont do much it will be able to create a Vulkan instance and then destroy it.

```cpp
#include <vulkan/vulkan.h>

int main()
{
 // Create a Vulkan instance
 VkInstanceCreateInfo createInfo = {};
 createInfo.sType = VK_STRUCTURE_TYPE_INSTANCE_CREATE_INFO;

 VkInstance instance;
 vkCreateInstance(&createInfo, nullptr, &instance);

 // Destroy the Vulkan instance
 vkDestroyInstance(instance, nullptr);

 return 0;
}

```

output:

```txt
C:\...\MyProject\x64\Debug\MyProject.exe (process *) exited with code 0.
```


## Visual Studio

Assuming you already have a project set up, you will need to add the Vulkan SDK to your project.
In the guide below we are going to use `VC++ Directories` instead of the typical `Additional Include Directories` and `Additional Library Directories` this way we can add the Vulkan SDK to all configurations and platforms at once.

{: .note }
You can do the following steps in the linker settings if you want the only difference being is that when you use `VC++ Directories` you are telling the compiler where to look for header files and libraries, and when you use the linker settings you are telling the linker where to look for libraries.

### Adding the Vulkan SDK to your project

1. Open your project in the `Solution Explorer`.
2. Access `Properties` by right-clicking on your project.
3. Go to `VC++ Directories`.
   - For `Include Directories`:
     - Open the dropdown menu.
     - Select `Edit`.
     - Add a new line with `$(VULKAN_SDK)\Include`.
     - Confirm with `OK`.
   - For `Library Directories`:
     - Open the dropdown menu.
     - Select `Edit`.
     - Add a new line with `$(VULKAN_SDK)\Lib`.
     - Confirm with `OK`.
4. Go to `Linker > Input`.
   - For `Additional Dependencies`.
     - Select `Edit`.
     - Add a new line with `vulkan-1.lib`.
     - Confirm with `OK`.
5. Confirm with `OK`.

{: .note }
If you are using a 32-bit compiler, you will need to use `$(VULKAN_SDK)\Lib32` instead of `$(VULKAN_SDK)\Lib`.

### Common Errors In Visual Studio

| Error | Solution |
| --- | --- |
| `vulkan/vulkan.h`: No such file or directory | Make sure that the directory that you added to `VC++ Directories > Include Directories` exists and contains the `vulkan.h` file|
| `vulkan-1.lib`: No such file or directory or `LNK1104`: cannot open file `vulkan-1.lib` | Make sure that the directory that you added to `VC++ Directories > Library Directories` exists and contains the `vulkan-1.lib` file |
| `LNK2019`: unresolved external symbol `_vkSomeFunction` referenced in function `_main` | Make sure that you added `vulkan-1.lib` to `Linker > Input > Additional Dependencies`. This can also happen if you are using a 32-bit compiler and you added `$(VULKAN_SDK)\Lib` instead of `$(VULKAN_SDK)\Lib32` to `VC++ Directories > Library Directories` |
| I did everything right but I still get errors | Make sure that you have the latest version of the Vulkan SDK installed. Next I would suggest checking that the `$(VULKAN_SDK)` variable is set correctly. You can do this by opening a command prompt and typing `echo %VULKAN_SDK%`. If the variable is not set correctly, you will need to set it manually. You can do this by going to `Control Panel > System and Security > System > Advanced System Settings > Environment Variables`. Next you want to click on `New` under `User variables for <your username>`. Now you want to set the variable name to `VULKAN_SDK` and the variable value to the path to the Vulkan SDK. For example `C:\VulkanSDK\ <version>`. Now you want to click `OK` and then `OK` again. Now you want to restart visual studio and try again. If you still get errors, I would suggest reinstalling the Vulkan SDK. |


## CMake

Cmake is a cross-platform build system that can be used to generate project files for a variety of IDEs and compilers.
Cmake can be somewhat confusing as it has its own syntax, but once you get used to it, it is very easy to use and can actually be faster than using the IDEs built-in project generation.

To use the Vulkan SDK with CMake, you will need to use the `FindVulkan.cmake` module, This module is included with the Vulkan SDK, and it can be found in the `C:\VulkanSDK\<version>\Bin` directory.

```cmake
find_package(Vulkan REQUIRED)
```

This will set the following variables:

- `Vulkan_FOUND`
- `Vulkan_INCLUDE_DIRS`
- `Vulkan_LIBRARIES`
- `Vulkan_LIBRARY`
- `Vulkan_LIBRARY_DEBUG`
- `Vulkan_LIBRARY_RELEASE`
- `Vulkan_LIBRARY_RELWITHDEBINFO`
- `Vulkan_LIBRARY_MINSIZEREL`

You can then use these variables to add the Vulkan SDK to your project.

```cmake
target_include_directories(MyProject PRIVATE ${Vulkan_INCLUDE_DIRS})
target_link_libraries(MyProject PRIVATE ${Vulkan_LIBRARIES})
```

{: .note }
If you are using a 32-bit compiler, you will need to use `Vulkan_LIBRARY_DEBUG` and `Vulkan_LIBRARY_RELEASE` instead of `Vulkan_LIBRARY`.

### Complete CMakeLists.txt

```cmake
cmake_minimum_required(VERSION 3.16)

project(MyProject)

find_package(Vulkan REQUIRED)

add_executable(MyProject src/main.cpp)

target_include_directories(MyProject PRIVATE ${Vulkan_INCLUDE_DIRS})
target_link_libraries(MyProject PRIVATE ${Vulkan_LIBRARIES})
```

### Common Errors In CMake

| Error | Solution |
| --- | --- |
| Could not find a package configuration file provided by "Vulkan". <br> Could not find module FindVulkan.cmake or a configuration file for package "Vulkan" | Make sure that you have the latest version of the Vulkan SDK installed. Next I would suggest checking that the `VULKAN_SDK` environment variable is set correctly. You can do this by opening a command prompt and typing `echo %VULKAN_SDK%`. If the variable is not set correctly, you will need to set it manually. You can do this by going to `Control Panel > System and Security > System > Advanced System Settings > Environment Variables`. Next you want to click on `New` under `User variables for <your username>`. Now you want to set the variable name to `VULKAN_SDK` and the variable value to the path to the Vulkan SDK. For example `C:\VulkanSDK\ <version>`. Now you want to click `OK` and then `OK` again. Now you want to restart visual studio and try again. If you still get errors, I would suggest reinstalling the Vulkan SDK. |