pm_runtime_put_noidle()

```c
/**
 * pm_runtime_put_noidle - decrement pm usage counter without runtime PM transition
 * @dev: Device to deactivate
 *
 * This function decrements the runtime PM usage counter for a device and,
 * if it reaches zero, marks the device as inactive. Unlike
 * pm_runtime_put(), it doesn't try to put the device into a low-power state.
 * Note that if no runtime PM operations are in progress (i.e. the driver is
 * using the device continuously), the device will be marked as inactive.
 */
```

可以看到，当使用计数器归零时，`pm_runtime_put_noidle()`将会将设备标记为inactive，但不会尝试将设备关闭或进入低功耗模式。这意味着，设备仍然可以保持运行状态，只是没有用户正在使用它。

对于设备的关闭和低功耗转换，需要使用`pm_runtime_put()`函数。`pm_runtime_put()`将使用计数器减1，并尝试将设备转换到低功耗模式，但不会关闭设备，除非使用计数器为0且设备未被使用。