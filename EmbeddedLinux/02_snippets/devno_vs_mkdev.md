## devno vs MKDEV

Purpose: clarify the difference between a `dev_t` variable and the `MKDEV` macro.

| Item | `devno` | `MKDEV(major, minor)` |
|------|---------|------------------------|
| Nature | a `dev_t` variable | macro to build a `dev_t` |
| Source | returned by `alloc_chrdev_region()` | manually combines major and minor |
| Meaning | usually the base device number | a specific device number |
| Single device | equals `MKDEV(MAJOR(devno), 0)` | same as `devno` |
| Multiple devices | `devno` is device 0; device i = `devno + i` (small i) | explicit minor number |
| Recommended | use the same `devno` in `device_create` and `device_destroy` | tutorial-friendly for showing major/minor |

Key point:

- `devno` is the kernel-allocated base `dev_t`.
- `MKDEV` is a manual constructor.
- Single device: they are equivalent.
- Production code: use `devno` for symmetry in create/destroy.

Example:

```c
dev_t devno;
alloc_chrdev_region(&devno, 0, 1, "mydev");
device_create(cls, NULL, devno, NULL, "mydev");
...
device_destroy(cls, devno);
```

Note: For multiple devices, compute `dev_t dev = MKDEV(MAJOR(devno), MINOR(devno) + i);` instead of relying on `devno + i`.