## Vulkan
一些核心概念:
* Physical Device
    ```c++
    // create
    uint32_t gpuCount = 0;
	vkEnumeratePhysicalDevices(instance, &gpuCount, nullptr);
    vkEnumeratePhysicalDevices(instance, &gpuCount, devices.data());

    // physical device properties
    // 硬件的属性: 硬件的名称, 制造商, 驱动版本
    vkGetPhysicalDeviceProperties(devices[j], &deviceProperties);

    // physical device features
    // 一大堆bool值来表示那些特性被支持, 例如geometry/tessellation shader, textureCompress 
    vkGetPhysicalDeviceFeatures(physicalDevice, &deviceFeatures);

    // physical device extension
    /*
     Vulkan extensions are simply additional features that Vulkan implementations may
     provide if they so choose to. They add new functions, structs, and valid 
     enumerators to the API, and they can change some of the behavior of existing 
     functions.
    */
    uint32_t extCount = 0;
    vkEnumerateDeviceExtensionProperties(physicalDevice, nullptr, &extCount, nullptr);
    std::vector<VkExtensionProperties> extensions(extCount);
    vkEnumerateDeviceExtensionProperties(physicalDevice, nullptr, &extCount, &extensions.front());
    ```
* Queue
    ```c++
    // 获取所有queue family的信息
    uint32_t queueFamilyCount;
	vkGetPhysicalDeviceQueueFamilyProperties(physicalDevice, &queueFamilyCount, nullptr);
    queueFamilyProperties.resize(queueFamilyCount);
	vkGetPhysicalDeviceQueueFamilyProperties(physicalDevice,
        &queueFamilyCount, queueFamilyProperties.data());

    // 
    ```
* Logical Device

* Layer

* Command
