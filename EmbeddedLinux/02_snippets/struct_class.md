# struct class

Purpose: classify devices and auto-create `/dev` nodes via udev.

Flow:
alloc_chrdev_region → cdev_add → class_create → device_create → /dev/mydev

Functions:
- class_create("name")          : create class, appears in /sys/class/
- device_create(class, parent, dev, drvdata, "name")  : create device
- device_destroy(class, dev)    : remove device
- class_destroy(class)          : remove class

Existing classes: /sys/class/net, /sys/class/tty, /sys/class/leds, ...

API difference:
- Old kernel: class_create(THIS_MODULE, "name")
- New kernel (5.15+): class_create("name")