# Core of Linux Driver Model

Three core concepts:
- **bus** : matches device and driver
- **device** : describes hardware
- **driver** : describes how to operate hardware

Flow:
device registered → bus matches → driver->probe()
driver registered → bus matches → driver->probe()

Applies to: platform, i2c, spi, usb, pci, ...

Exceptions:
- Char devices: VFS path (cdev + file_operations), no bus matching
- Misc devices: simplified char device on misc_bus
- Pure software modules: no hardware, no bus

Mental model: for any new subsystem, ask
1. What is the bus?
2. What is the device struct?
3. What is the driver struct?
4. How do they match?
