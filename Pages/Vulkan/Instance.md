---
layout: default
title: Instance
parent: Vulkan
nav_order: 3
---

# Instance

The Vulkan Instance refers to the foremost structure necessary when initiating a Vulkan application. It not only encapsulates application-level resources but also interfaces with both the operating system and the hardware. It's crucial to note that it's the initial step in using Vulkan, as it is responsible for dispatching commands to physical devices or GPUs.

## Creating the Vulkan instance

To create the Vulkan instance you need to fill out a `VkInstanceCreateInfo` struct and pass it to `vkCreateInstance`.

```cpp
VkApplicationInfo appInfo = {};
appInfo.sType = VK_STRUCTURE_TYPE_APPLICATION_INFO;
appInfo.pApplicationName = "My Vulkan Application";
appInfo.applicationVersion = VK_MAKE_VERSION(1, 0, 0);
appInfo.pEngineName = "No Engine";
appInfo.engineVersion = VK_MAKE_VERSION(1, 0, 0);
appInfo.apiVersion = VK_API_VERSION_1_3;

VkInstanceCreateInfo createInfo = {};
createInfo.sType = VK_STRUCTURE_TYPE_INSTANCE_CREATE_INFO;
createInfo.pApplicationInfo = &appInfo;
createInfo.enabledLayerCount = 0;
createInfo.ppEnabledLayerNames = nullptr;
createInfo.enabledExtensionCount = 0;
createInfo.ppEnabledExtensionNames = nullptr;
createInfo.pNext = nullptr;
createInfo.flags = 0;

VkInstance instance;
vkCreateInstance(&createInfo, nullptr, &instance);
```

{: .note }
The `VkApplicationInfo` struct is used to give the Vulkan instance some information about your application, such as the name, version, and engine name.

There are alot of fields in the `VkInstanceCreateInfo` struct, but most of them are optional. For the current example the only important field is `sType` which tells Vulkan what type of struct you are using.

But below is a list of all the fields in the `VkInstanceCreateInfo` struct.

| Member | Description |
| --- | --- |
| `sType` | The type of the struct. This must be `VK_STRUCTURE_TYPE_INSTANCE_CREATE_INFO` |
| `pNext` | A pointer to the next struct in a linked list, or `nullptr` |
| `flags` | The list of flags you can set are found [here](https://registry.khronos.org/vulkan/specs/1.3-extensions/man/html/VkInstanceCreateFlagBits.html). |
| `pApplicationInfo` | A pointer to a `VkApplicationInfo` struct, or `nullptr` |
| `enabledLayerCount` | The number of layers to enable |
| `ppEnabledLayerNames` | A pointer to an array of layer names to enable, or `nullptr` |
| `enabledExtensionCount` | The number of extensions to enable |
| `ppEnabledExtensionNames` | A pointer to an array of extension names to enable, or `nullptr` |

## Layers

Primarily, layers serve to extend Vulkan's core capabilities. One such layer type, called 'Validation Layer', acts as a real-time debugging tool for developers. This layer is instrumental in verifying whether your code aligns with the Vulkan APIs specification or not. The outcome of this check can ascertain the stability and efficiency of your Vulkan-based applications.

In addition to error detection, Validation Layers can also provide valuable insights into performance bottlenecks and potential optimizations. Such insights become crucial when you aim to enhance your application's performance.

When it comes to graphics programming, each detail matters. Leveraging validation layers from Vulkan will not just improve the robustness and speed of your application, but also ensure that any deviations from the norm are promptly identified and rectified.

Thus, Vulkan's Layers offer a streamlined approach to debug and optimize your application, making them an insightful component of the Vulkan toolkit.

### Setting up validation layers

To set up validation layers you need to add the `VK_LAYER_KHRONOS_validation` layer to the `ppEnabledLayerNames` field in the `VkInstanceCreateInfo` struct.

```cpp
bool checkValidationLayerSupport()
{
    uint32_t layerCount = 0;
    vkEnumerateInstanceLayerProperties(&layerCount, nullptr);

    std::vector<VkLayerProperties> availableLayers(layerCount);
    vkEnumerateInstanceLayerProperties(&layerCount, availableLayers.data());

    for (const char* layerName : validationLayers)
    {
        bool layerFound = false;

        for (const auto& layerProperties : availableLayers)
        {
            if (strcmp(layerName, layerProperties.layerName) == 0)
            {
                layerFound = true;
                break;
            }
        }

        if (!layerFound)
        {
            return false;
        }
    }

    return true;
}
```

In the code above we are checking if the `VK_LAYER_KHRONOS_validation` layer is available, and if it is we add it to the `ppEnabledLayerNames` field in the `VkInstanceCreateInfo` struct.

```cpp
const std::vector<const char*> validationLayers;

if (checkValidationLayerSupport())
{
    validationLayers.push_back("VK_LAYER_KHRONOS_validation");
}

VkInstanceCreateInfo createInfo = {};
createInfo.sType = VK_STRUCTURE_TYPE_INSTANCE_CREATE_INFO;
createInfo.pApplicationInfo = &appInfo;
createInfo.enabledLayerCount = static_cast<uint32_t>(validationLayers.size());
createInfo.ppEnabledLayerNames = validationLayers.data();
```

{: .note }
The `enabledLayerCount` field is the number of layers to enable, and the `ppEnabledLayerNames` field is a pointer to an array of layer names to enable.

## Extensions

Vulkan extensions, as the name suggests, are added to the Vulkan API to extend its core functionality. These can provide several benefits such as access to new hardware features or software functionalities that were not originally present in the Vulkan standard.

A prime example of such an extension is the `VK_KHR_surface`. It does not merely add "extra functionality" but plays a crucial role in creating a renderable surface within a windowing system. This is fundamental for applications aiming for rendering graphics on screen.

It's also worth noting that Vulkan extensions can be utilized to bridge the gap between Vulkan's platform-agnostic nature and specific platform requirements. The `VK_KHR_win32_surface` extension, for instance, is designed specifically for Windows. It provides functionalities necessary for creating surfaces on Windows systems, thereby ensuring smooth functioning of Vulkan-based applications on this widely used operating system.

While working with popular windowing libraries like `GLFW` or `SDL`, developers may not have to actively manage these extensions. That's because these libraries abstract away much of the intricacies related to extensions. They handle the loading and management of appropriate extensions based on the target platform, allowing developers to focus more on their actual application logic rather than worrying about compatibility details. Therefore, while it's essential to understand what extensions do, in practice you often don't need to interact directly with them when using such libraries.

### Setting up extensions

To set up extensions you need to add the `VK_KHR_surface` extension to the `ppEnabledExtensionNames` field in the `VkInstanceCreateInfo` struct.

```cpp
bool checkExtensionSupport()
{
    uint32_t extensionCount = 0;
    vkEnumerateInstanceExtensionProperties(nullptr, &extensionCount, nullptr);

    std::vector<VkExtensionProperties> availableExtensions(extensionCount);
    vkEnumerateInstanceExtensionProperties(nullptr, &extensionCount, availableExtensions.data());

    for (const char* extensionName : extensions)
    {
        bool extensionFound = false;

        for (const auto& extensionProperties : availableExtensions)
        {
            if (strcmp(extensionName, extensionProperties.extensionName) == 0)
            {
                extensionFound = true;
                break;
            }
        }

        if (!extensionFound)
        {
            return false;
        }
    }

    return true;
}
```

In the code above we are checking if the `VK_KHR_surface` extension is available, and if it is we add it to the `ppEnabledExtensionNames` field in the `VkInstanceCreateInfo` struct.

```cpp
const std::vector<const char*> extensions;

if (checkExtensionSupport())
{
    extensions.push_back("VK_KHR_surface");
}

VkInstanceCreateInfo createInfo = {};
createInfo.sType = VK_STRUCTURE_TYPE_INSTANCE_CREATE_INFO;
createInfo.pApplicationInfo = &appInfo;
createInfo.enabledLayerCount = static_cast<uint32_t>(validationLayers.size());
createInfo.ppEnabledLayerNames = validationLayers.data();
createInfo.enabledExtensionCount = static_cast<uint32_t>(extensions.size());
createInfo.ppEnabledExtensionNames = extensions.data();
```

{: .note }
The `enabledExtensionCount` field is the number of extensions to enable, and the `ppEnabledExtensionNames` field is a pointer to an array of extension names to enable.

## Destroying the Vulkan instance

To destroy the Vulkan instance you need to call `vkDestroyInstance`.

```cpp
vkDestroyInstance(instance, nullptr);
```

{: .note }
This should be the last thing that you do when using Vulkan. Typically you would destroy the Vulkan instance when the program exits.

## Full code

```cpp
#include <vulkan/vulkan.h>

#include <iostream>
#include <stdexcept>
#include <vector>

const std::vector<const char*> validationLayers =
{
    "VK_LAYER_KHRONOS_validation"
};

const std::vector<const char*> extensions =
{
    "VK_KHR_surface"
};

#ifdef NDEBUG
const bool enableValidationLayers = false;
#else
const bool enableValidationLayers = true;
#endif

bool checkValidationLayerSupport()
{
    uint32_t layerCount = 0;
    vkEnumerateInstanceLayerProperties(&layerCount, nullptr);

    std::vector<VkLayerProperties> availableLayers(layerCount);
    vkEnumerateInstanceLayerProperties(&layerCount, availableLayers.data());

    for (const char* layerName : validationLayers)
    {
        bool layerFound = false;

        for (const auto& layerProperties : availableLayers)
        {
            if (strcmp(layerName, layerProperties.layerName) == 0)
            {
                layerFound = true;
                break;
            }
        }

        if (!layerFound)
        {
            return false;
        }
    }

    return true;
}

bool checkExtensionSupport()
{
    uint32_t extensionCount = 0;
    vkEnumerateInstanceExtensionProperties(nullptr, &extensionCount, nullptr);

    std::vector<VkExtensionProperties> availableExtensions(extensionCount);
    vkEnumerateInstanceExtensionProperties(nullptr, &extensionCount, availableExtensions.data());

    for (const char* extensionName : extensions)
    {
        bool extensionFound = false;

        for (const auto& extensionProperties : availableExtensions)
        {
            if (strcmp(extensionName, extensionProperties.extensionName) == 0)
            {
                extensionFound = true;
                break;
            }
        }

        if (!extensionFound)
        {
            return false;
        }
    }

    return true;
}

void createInstance()
{
    if (enableValidationLayers && !checkValidationLayerSupport())
    {
        throw std::runtime_error("validation layers requested, but not available!");
    }

    VkApplicationInfo appInfo = {};
    appInfo.sType = VK_STRUCTURE_TYPE_APPLICATION_INFO;
    appInfo.pApplicationName = "My Vulkan Application";
    appInfo.applicationVersion = VK_MAKE_VERSION(1, 0, 0);
    appInfo.pEngineName = "No Engine";
    appInfo.engineVersion = VK_MAKE_VERSION(1, 0, 0);
    appInfo.apiVersion = VK_API_VERSION_1_3;

    VkInstanceCreateInfo createInfo = {};
    createInfo.sType = VK_STRUCTURE_TYPE_INSTANCE_CREATE_INFO;
    createInfo.pApplicationInfo = &appInfo;
    createInfo.enabledLayerCount = 0;
    createInfo.ppEnabledLayerNames = nullptr;
    createInfo.enabledExtensionCount = 0;
    createInfo.ppEnabledExtensionNames = nullptr;
    createInfo.pNext = nullptr;
    createInfo.flags = 0;

    if (enableValidationLayers)
    {
        createInfo.enabledLayerCount = static_cast<uint32_t>(validationLayers.size());
        createInfo.ppEnabledLayerNames = validationLayers.data();
    }

    VkInstance instance;
    VkResult result = vkCreateInstance(&createInfo, nullptr, &instance);

    if (result != VK_SUCCESS)
    {
        throw std::runtime_error("failed to create instance!");
    }
}

int main()
{
    createInstance();

    return 0;
}
```

{: .note }
I've modified the code by introducing a `createInstance` function and relocating the Vulkan instance creation code within it. This separation adheres to the single responsibility principle, which becomes increasingly crucial as the code complexity grows.

## Next Steps

Now that you have created the Vulkan instance, you can move on to the next step which is [Devices](/Pages/Vulkan/Devices.html).
