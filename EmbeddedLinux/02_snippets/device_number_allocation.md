# Device Number Allocation

- `register_chrdev_region(dev, count, name)`: manually specify major number.
  - Requires checking `Documentation/devices.txt` (deprecated since 5.8).
  - Risk of conflict with other drivers.

- `alloc_chrdev_region(&dev, 0, 1, name)`: kernel allocates a free major number.
  - Modern recommended approach.
  - Check assigned major with `cat /proc/devices`.
  - Create device node with `mknod`.

Old tutorials: use `register_chrdev_region` + `devices.txt`.
Modern practice: use `alloc_chrdev_region` + `/proc/devices`.