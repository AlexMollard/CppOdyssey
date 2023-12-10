---
layout: default
title: Setting up Vulkan
parent: Vulkan
nav_order: 2
---

# Setting up Vulkan

## Windows

### Installing the Vulkan SDK

1. Go to [https://vulkan.lunarg.com/sdk/home](https://vulkan.lunarg.com/sdk/home)
2. Download the latest version of the Vulkan SDK
3. Run the installer
4. Select the components that you want to install
5. Click `Install`

## Visual Studio

Assuming you already have a project set up, you will need to add the Vulkan SDK to your project.
In the guide below we are going to use `Vc++ Directories` instead of the typical `Additional Include Directories` and `Additional Library Directories` this way we can add the Vulkan SDK to all configurations and platforms at once.

{: .note }
You can do the following steps in the linker settings if you want the only difference being is that when you use `VC++ Directories` you are telling the compiler where to look for header files and libraries, and when you use the linker settings you are telling the linker where to look for libraries.

### Adding the Vulkan SDK to your project

1. Open your project in the `Solution Explorer`.
2. Access `Properties` by right-clicking on your project.
3. Go to `VC++ Directories`.
   - For `Include Directories`:
     - Open the dropdown menu.
     - Select `Edit`.
     - Add a new line with `C:\VulkanSDK\`.
     - Confirm with `OK`.
   - For `Library Directories`:
     - Open the dropdown menu.
     - Select `Edit`.
     - Add a new line with `C:\VulkanSDK\`.
     - Confirm with `OK`.
4. Apply the changes.
5. Close the properties window with `OK`.

{: .note }
You may need to change the path to the Vulkan SDK if you installed it in a different location.
The installer will normally set a environment variable called `VULKAN_SDK` to the path of the Vulkan SDK, so you can use that instead of hardcoding the path. (Where ever you would normally use `C:\VulkanSDK\` you can use `$(VULKAN_SDK)` instead.)