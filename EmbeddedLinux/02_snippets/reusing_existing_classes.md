# Reusing Existing Classes

If a suitable class already exists, no need to call `class_create()`.

But:
- Subsystems (LED, input, net, iio, hwmon, rtc) provide their own register API.
- Use those APIs instead of raw `device_create()`.
- Raw `device_create()` only for generic char devices not belonging to any subsystem.

Example:
- LED: `led_classdev_register()`, not `device_create()`
- Input: `input_register_device()`
- Network: `register_netdev()`