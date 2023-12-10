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

### Sample Code

To test if you have set up the Vulkan SDK correctly, you can try to compile the following code:

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

### Common Errors

| Error | Solution |
| --- | --- |
| `vulkan/vulkan.h`: No such file or directory | Make sure that the directory that you added to `VC++ Directories > Include Directories` exists and contains the `vulkan.h` file|
| `vulkan-1.lib`: No such file or directory or `LNK1104`: cannot open file `vulkan-1.lib` | Make sure that the directory that you added to `VC++ Directories > Library Directories` exists and contains the `vulkan-1.lib` file |
| `LNK2019`: unresolved external symbol `_vkSomeFunction` referenced in function `_main` | Make sure that you added `vulkan-1.lib` to `Linker > Input > Additional Dependencies`. This can also happen if you are using a 32-bit compiler and you added `$(VULKAN_SDK)\Lib` instead of `$(VULKAN_SDK)\Lib32` to `VC++ Directories > Library Directories` |
| I did everything right but I still get errors | Make sure that you have the latest version of the Vulkan SDK installed. Next I would suggest checking that the `$(VULKAN_SDK)` variable is set correctly. You can do this by opening a command prompt and typing `echo %VULKAN_SDK%`. If the variable is not set correctly, you will need to set it manually. You can do this by going to `Control Panel > System and Security > System > Advanced System Settings > Environment Variables`. Next you want to click on `New` under `User variables for <your username>`. Now you want to set the variable name to `VULKAN_SDK` and the variable value to the path to the Vulkan SDK. For example `C:\VulkanSDK\ <version>`. Now you want to click `OK` and then `OK` again. Now you want to restart visual studio and try again. If you still get errors, I would suggest reinstalling the Vulkan SDK. |
