---
layout: default
nav_order: 4
parent: Vulkan
title: Devices
---

# Devices

```mermaid
graph LR;
  A[Instance] --> B{Devices};
  B --> C[Swap Chain];
  C --> D[Image Views];
  D --> E[Render Pass];
  F[Pipelines];
  F --> G[Framebuffers];
  G --> H[Command Pool/Buffers];
  H --> I[Synchronization];
```

Vulkan Devices, comprised of Physical and Logical Devices, serve as the interface to GPUs in a Vulkan application. By identifying suitable Physical Devices, which denote GPUs on your system, we gain access to hardware details like capabilities and memory types. Through a Logical Device, we can command the GPU, manage resources, and execute operations associated with the GPU. Essentially, Vulkan Devices provide the means to interact with the GPU.

---

## Physical Devices

In Vulkan, a Physical Device represents a GPU on your system. It exposes detailed information about the capabilities of the hardware including:

- The types of operations it can support
- The limits (like max image dimension and max memory allocation count) it imposes
- The features it provides, such as texture compression, 64 bit floats, multi-viewport rendering and so forth
- The queue families it offers, determining the kind of commands that queues in this family can execute
- The memory heaps and memory types it contains, giving us insight into the different types of memory that can be allocated

We typically select a suitable Physical Device based on these attributes to ensure it meets the needs of our application. To access a Physical Device and query its properties, we use `vkEnumeratePhysicalDevices` and `vkGetPhysicalDeviceProperties` respectively.

```cpp
uint32_t deviceCount = 0;
vkEnumeratePhysicalDevices(instance, &deviceCount, nullptr);

if (deviceCount == 0) {
    throw std::runtime_error("failed to find GPUs with Vulkan support!");
}

std::vector<VkPhysicalDevice> devices(deviceCount);
vkEnumeratePhysicalDevices(instance, &deviceCount, devices.data());

for (const auto& device : devices) {
    VkPhysicalDeviceProperties deviceProperties;
    vkGetPhysicalDeviceProperties(device, &deviceProperties);

    std::cout << "GPU: " << deviceProperties.deviceName << std::endl;
}
```

This code gets the number of Physical Devices (GPUs), then stores them in a vector. For each device, it queries their properties and prints out their names. After selecting an appropriate Physical Device, we can create a Logical Device for performing operations associated with the GPU.

---

## Logical Devices

Logical devices function as an interface to the hardware, specifically interacting with the GPU. They are represented by a `VkDevice` object. Understanding how to create logical devices and utilize them effectively is key to GPU programming.

### Queue Families

The main components of a logical device are the queue families that it uses. The queue families directly control the types of commands that can be executed on the device. In Vulkan, each GPU contains several families of queues. Each family can perform certain operations like graphics commands, compute commands, or presentation commands. When creating the logical device, we need to specify which queue families we want to use based on our requirements.

In the given code, the queue families are identified by iterating over all the available queue families retrieved from the `VkPhysicalDevice` object and checking their capabilities.

```cpp
struct QueueFamilyIndices {
    std::optional<uint32_t> graphicsFamily;
    std::optional<uint32_t> presentFamily;

    bool isComplete() {
        return graphicsFamily.has_value() && presentFamily.has_value();
    }
};
```
Here, `QueueFamilyIndices` structure is defined to hold indices of queue families that support graphics and presentation operations. These indices are then used while creating a logical device.

### Extensions and Features

Next comes handling extensions and features for the logical device. Extensions give us access to new functionalities that aren't available in the core Vulkan API. While creating a logical device, we specify which of these extensions we want to enable. Features are optional functionalities that we can enable when they are supported by the device. 

In the provided code, the necessary extensions and features are enabled within the `VkDeviceCreateInfo` struct:

```cpp
VkDeviceCreateInfo createInfo{};
createInfo.sType = VK_STRUCTURE_TYPE_DEVICE_CREATE_INFO;

createInfo.pEnabledFeatures = &deviceFeatures;
createInfo.enabledExtensionCount = static_cast<uint32_t>(deviceExtensions.size());
createInfo.ppEnabledExtensionNames = deviceExtensions.data();
```
Here, `ppEnabledExtensionNames` field is used to specify the extensions and `pEnabledFeatures` field is used to specify the features.

### Creation of Logical Device

After selecting the queue families, enabling extensions, and features, we create the logical device using `vkCreateDevice()`.

```cpp
if (vkCreateDevice(physicalDevice, &createInfo, nullptr, &device) != VK_SUCCESS) {
    throw std::runtime_error("failed to create logical device!");
}
```
The function `vkCreateDevice()` takes in a physical device, a struct detailing the configuration (`VkDeviceCreateInfo`), a custom allocator callback, and a reference for storing the handle to the new logical device.

Following successful creation, the graphics and present queues are fetched from the logical device using `vkGetDeviceQueue()`. The logical device then returns to the caller.

To summarize, creating logical devices properly is a pivotal task in writing efficient GPU code. This involves thoughtful consideration when opting for queue families, and careful selection of necessary extensions and features.

---

## Summary

- Physical Devices represent GPUs on your system and expose detailed information about the capabilities of the hardware
- Logical Devices serve as an interface to the GPU, allowing us to perform operations associated with the GPU
- Logical Devices are comprised of queue families, which directly control the types of commands that can be executed on the device
- When creating a logical device, we need to specify which queue families we want to use based on our requirements
- Extensions give us access to new functionalities that aren't available in the core Vulkan API
- Features are optional functionalities that we can enable when they are supported by the device

---

## Sample Code

The following code snippet shows how to create a logical device.

```cpp
#define VK_USE_PLATFORM_WIN32_KHR
#include <vulkan/vulkan.h>

#include <iostream>
#include <stdexcept>
#include <vector>
#include <optional>
#include <set>

// Windows creation headers
#include <Windows.h>
#include <windowsx.h>
#include <WinUser.h>
LRESULT CALLBACK WndProc(HWND hWnd, UINT message, WPARAM wParam, LPARAM lParam);

const std::vector<const char*> deviceExtensions = {
	VK_KHR_SWAPCHAIN_EXTENSION_NAME
};

const std::vector<const char*> validationLayers =
{
	 "VK_LAYER_KHRONOS_validation"
};

const std::vector<const char*> extensions =
{
	VK_KHR_SURFACE_EXTENSION_NAME,
	"VK_KHR_win32_surface",
	VK_EXT_DEBUG_UTILS_EXTENSION_NAME
};

#ifdef NDEBUG
const bool enableValidationLayers = false;
#else
const bool enableValidationLayers = true;
#endif

struct QueueFamilyIndices {
	std::optional<uint32_t> graphicsFamily;
	std::optional<uint32_t> presentFamily;

	bool isComplete() {
		return graphicsFamily.has_value() && presentFamily.has_value();
	}
};

std::map<VkDebugUtilsMessageTypeFlagsEXT, std::string> messageTypeMap = {
	{VK_DEBUG_UTILS_MESSAGE_TYPE_GENERAL_BIT_EXT, "GENERAL"},
	{VK_DEBUG_UTILS_MESSAGE_TYPE_VALIDATION_BIT_EXT, "VALIDATION"},
	{VK_DEBUG_UTILS_MESSAGE_TYPE_PERFORMANCE_BIT_EXT, "PERFORMANCE"},
};

std::map<VkDebugUtilsMessageSeverityFlagBitsEXT, std::string> messageSeverityMap = {
	{VK_DEBUG_UTILS_MESSAGE_SEVERITY_VERBOSE_BIT_EXT, "VERBOSE"},
	{VK_DEBUG_UTILS_MESSAGE_SEVERITY_INFO_BIT_EXT, "INFO"},
	{VK_DEBUG_UTILS_MESSAGE_SEVERITY_WARNING_BIT_EXT, "WARNING"},
	{VK_DEBUG_UTILS_MESSAGE_SEVERITY_ERROR_BIT_EXT, "ERROR"},
};

void handleMessageType(VkDebugUtilsMessageTypeFlagsEXT messageType) {
	std::cout << "TYPE: ";
   
	for (const auto& type : messageTypeMap) {
		if (messageType & type.first)
			std::cout << type.second << std::endl;
	}
}

void handleMessageSeverity(VkDebugUtilsMessageSeverityFlagBitsEXT messageSeverity) {
	std::cout << "SEVERITY: ";

	for (const auto& severity : messageSeverityMap) {
		if (messageSeverity & severity.first)
			std::cout << severity.second << std::endl;
	}
}

VkBool32 callBackFunc(VkDebugUtilsMessageSeverityFlagBitsEXT messageSeverity, VkDebugUtilsMessageTypeFlagsEXT messageType,
	const VkDebugUtilsMessengerCallbackDataEXT* pCallbackData, void* pUserData)
{
	handleMessageType(messageType);
	handleMessageSeverity(messageSeverity);
   
	std::cout << "validation layer: " << pCallbackData->pMessage << std::endl;

	if (messageSeverity & VK_DEBUG_UTILS_MESSAGE_SEVERITY_ERROR_BIT_EXT)
		DebugBreak();

	return VK_FALSE;
}

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

VkInstance CreateInstance()
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
	createInfo.enabledExtensionCount = extensions.size();
	createInfo.ppEnabledExtensionNames = extensions.data();
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

	if (!enableValidationLayers)
	{
		return instance;
	}

	// Setup the debug messenger
	VkDebugUtilsMessengerCreateInfoEXT debugCreateInfo = {};
	debugCreateInfo.sType = VK_STRUCTURE_TYPE_DEBUG_UTILS_MESSENGER_CREATE_INFO_EXT;
	debugCreateInfo.messageSeverity =
		VK_DEBUG_UTILS_MESSAGE_SEVERITY_ERROR_BIT_EXT |
		VK_DEBUG_UTILS_MESSAGE_SEVERITY_WARNING_BIT_EXT |
		VK_DEBUG_UTILS_MESSAGE_SEVERITY_INFO_BIT_EXT;
	debugCreateInfo.messageType =
		VK_DEBUG_UTILS_MESSAGE_TYPE_GENERAL_BIT_EXT |
		VK_DEBUG_UTILS_MESSAGE_TYPE_VALIDATION_BIT_EXT |
		VK_DEBUG_UTILS_MESSAGE_TYPE_PERFORMANCE_BIT_EXT;
	debugCreateInfo.pfnUserCallback = callBackFunc;
	debugCreateInfo.pUserData = nullptr;

	auto CreateDebugUtilsMessengerEXT = (PFN_vkCreateDebugUtilsMessengerEXT)vkGetInstanceProcAddr(instance, "vkCreateDebugUtilsMessengerEXT");

	if (CreateDebugUtilsMessengerEXT == nullptr)
	{
		throw std::runtime_error("Vulkan validation layers requested, but not available!");
	}

	VkDebugUtilsMessengerEXT debugMessenger;
	result = CreateDebugUtilsMessengerEXT(instance, &debugCreateInfo, nullptr, &debugMessenger);

	if (result != VK_SUCCESS)
	{
		throw std::runtime_error("Failed to create Vulkan debug messenger!");
	}

	return instance;
}

VkSurfaceKHR CreateSurface(VkInstance instance)
{
	// For this example we will just use the windows.h header
	VkSurfaceKHR surface = nullptr;

	VkWin32SurfaceCreateInfoKHR createInfo = {};
	createInfo.sType = VK_STRUCTURE_TYPE_WIN32_SURFACE_CREATE_INFO_KHR;
	createInfo.hwnd = nullptr;
	createInfo.hinstance = nullptr;

	VkResult result = vkCreateWin32SurfaceKHR(instance, &createInfo, nullptr, &surface);

	if (result != VK_SUCCESS)
	{
		throw std::runtime_error("failed to create surface!");
	}

	return surface;
}

HWND CreateApplicationWindow(VkSurfaceKHR surface, VkInstance instance, int nCmdShow)
{
	HINSTANCE hInstance = GetModuleHandle(NULL);

	// Register the window class
	LPCWSTR CLASS_NAME = L"VulkanWindowClass";

	WNDCLASS wc = {};

	wc.lpfnWndProc = WndProc;
	wc.hInstance = hInstance;
	wc.lpszClassName = CLASS_NAME;

	RegisterClass(&wc);

	// Create the window
	HWND hwnd = CreateWindowEx(
		0,                              // Optional window styles.
		CLASS_NAME,                     // Window class
		L"My Vulkan Application",        // Window text
		WS_OVERLAPPEDWINDOW,            // Window style

		// Size and position
		CW_USEDEFAULT, CW_USEDEFAULT, CW_USEDEFAULT, CW_USEDEFAULT,

		NULL,       // Parent window    
		NULL,       // Menu
		hInstance,  // Instance handle
		NULL        // Additional application data
	);

	// Check if the window was created
	if (hwnd == NULL)
	{
		return NULL;
	}

	// Set the window size
	RECT rect;
	GetClientRect(hwnd, &rect);

	// Resize the window
	int width = 800;
	int height = 600;

	MoveWindow(hwnd, rect.left, rect.top, width, height, TRUE);

	// Attach the surface to the window
	VkWin32SurfaceCreateInfoKHR createInfo = {};
	createInfo.sType = VK_STRUCTURE_TYPE_WIN32_SURFACE_CREATE_INFO_KHR;
	createInfo.hwnd = hwnd;
	createInfo.hinstance = hInstance;

	VkResult result = vkCreateWin32SurfaceKHR(instance, &createInfo, nullptr, &surface);

	if (result != VK_SUCCESS)
	{
		throw std::runtime_error("failed to create surface!");
	}

	// Show the window and update it
	ShowWindow(hwnd, nCmdShow);
	UpdateWindow(hwnd);

	return hwnd;
}

QueueFamilyIndices FindQueueFamilies(VkPhysicalDevice device, VkSurfaceKHR surface) {
	QueueFamilyIndices indices;

	uint32_t queueFamilyCount = 0;
	vkGetPhysicalDeviceQueueFamilyProperties(device, &queueFamilyCount, nullptr);

	std::vector<VkQueueFamilyProperties> queueFamilies(queueFamilyCount);
	vkGetPhysicalDeviceQueueFamilyProperties(device, &queueFamilyCount, queueFamilies.data());

	int i = 0;
	for (const auto& queueFamily : queueFamilies) {
		if (queueFamily.queueFlags & VK_QUEUE_GRAPHICS_BIT) {
			indices.graphicsFamily = i;
		}

		VkBool32 presentSupport = false;
		vkGetPhysicalDeviceSurfaceSupportKHR(device, i, surface, &presentSupport);

		if (presentSupport) {
			indices.presentFamily = i;
		}

		if (indices.isComplete()) {
			break;
		}

		i++;
	}

	return indices;
}

// This function returns the first physical device in your system
VkPhysicalDevice GetPhysicalDevice(VkInstance instance) {
	uint32_t deviceCount = 0;
	vkEnumeratePhysicalDevices(instance, &deviceCount, nullptr);

	if (deviceCount == 0) {
		throw std::runtime_error("failed to find GPUs with Vulkan support!");
	}

	std::vector<VkPhysicalDevice> devices(deviceCount);
	vkEnumeratePhysicalDevices(instance, &deviceCount, devices.data());

	for (const auto& device : devices) {
		VkPhysicalDeviceProperties deviceProperties;
		vkGetPhysicalDeviceProperties(device, &deviceProperties);

		std::cout << "GPU: " << deviceProperties.deviceName << std::endl;
	}

	return devices[0];
}

VkDevice CreateLogicalDevice(VkPhysicalDevice physicalDevice, VkSurfaceKHR surface) {
	// Find a queue family
	QueueFamilyIndices indices = FindQueueFamilies(physicalDevice, surface);

	// Setup the queue create infos
	std::vector<VkDeviceQueueCreateInfo> queueCreateInfos;
	std::set<uint32_t> uniqueQueueFamilies = { indices.graphicsFamily.value(), indices.presentFamily.value() };

	float queuePriority = 1.0f;
	for (uint32_t queueFamily : uniqueQueueFamilies)
	{
		VkDeviceQueueCreateInfo queueCreateInfo = {};

		queueCreateInfo.sType = VK_STRUCTURE_TYPE_DEVICE_QUEUE_CREATE_INFO;
		queueCreateInfo.queueFamilyIndex = indices.graphicsFamily.value();
		queueCreateInfo.queueCount = 1;
		queueCreateInfo.pQueuePriorities = &queuePriority;

		queueCreateInfos.push_back(queueCreateInfo);
	}

	// Create the logical device
	VkDevice device = nullptr;

	VkDeviceCreateInfo createInfo = {};
	createInfo.sType = VK_STRUCTURE_TYPE_DEVICE_CREATE_INFO;
	createInfo.pQueueCreateInfos = queueCreateInfos.data();
	createInfo.queueCreateInfoCount = static_cast<uint32_t>(queueCreateInfos.size());

	VkResult result = vkCreateDevice(physicalDevice, &createInfo, nullptr, &device);

	if (result != VK_SUCCESS)
	{
		throw std::runtime_error("failed to create logical device!");
	}

	return device;
}

int main() {
	// Create a Vulkan instance
	VkInstance instance = CreateInstance();

	// Create a window surface
	VkSurfaceKHR surface = CreateSurface(instance);

	// Create a window
	HWND windowHandle = CreateApplicationWindow(surface, instance, SW_SHOW);

	// Find a physical device
	VkPhysicalDevice physicalDevice = GetPhysicalDevice(instance);

	// Create the logical device
	VkDevice device = CreateLogicalDevice(physicalDevice, surface);

	// Engine loop
	MSG msg = {};
	while (GetMessage(&msg, NULL, 0, 0))
	{
		TranslateMessage(&msg);
		DispatchMessage(&msg);
	}

	// Destroy the logical device
	vkDestroyDevice(device, nullptr);

	// Destroy the instance
	vkDestroyInstance(instance, nullptr);

	return 0;
}

LRESULT CALLBACK WndProc(HWND hWnd, UINT message, WPARAM wParam, LPARAM lParam)
{
	switch (message)
	{
	case WM_CLOSE:
		DestroyWindow(hWnd);
		break;

	case WM_DESTROY:
		PostQuitMessage(0);
		break;

	default:
		return DefWindowProc(hWnd, message, wParam, lParam);
	}

	return 0;
}
```

{: .note}
In this example I am using the windows API to create a window and a surface. If you are using a different platform, you will need to use the appropriate API to create a window and a surface.
This can also largely be ignored if you dont want a window or are using a windowing library like SDL.

As you can see the code is becoming quite large. In the next section we will look at how to structure our code to make it more manageable.

If everything went well, you should see a window like this:

![Vulkan Window](../../assets/VulkanWindow.png)

## References

- [Vulkan Tutorial - Devices](https://vulkan-tutorial.com/Drawing_a_triangle/Setup/Logical_device_and_queues)

