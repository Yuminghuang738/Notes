# Core Structures

## Device Model (bus / device / driver)

| Structure | Represents | Key Point |
|-----------|-----------|-----------|
| `struct bus_type` | a bus | matches device and driver |
| `struct device` | one device in device model | device_create() triggers /dev node |
| `struct device_driver` | a driver | has probe/remove, matches via id_table or of_match_table |
| `struct class` | a class of devices | maps to /sys/class/<name>/ |

## Char Device (VFS path)

| Structure | Represents | Key Point |
|-----------|-----------|-----------|
| `dev_t` | device number | major + minor |
| `cdev` | char device | binds dev_t + file_operations |
| `file_operations` | function table | .open/.read/.write/.release |
| `struct file` | **one open instance** | created per open(), holds f_pos, f_flags |
| `struct inode` | one file in filesystem | holds i_cdev, i_rdev |

## Relationship

```text
Device Model:
bus_type  ←→  device  ←→  device_driver
                 │
                 └── class (for /dev auto-create)

Char Device:
dev_t  →  cdev  →  file_operations
                    ↑
              struct file (per open)
```

Corrections:
- `struct file` is NOT the file itself; it's an open instance.
- `class` does NOT create /dev nodes; `device_create` does.
- `struct device_driver` is the driver side of bus matching (platform_driver, i2c_driver, etc.).
- `struct inode` holds `i_cdev` (used by `container_of` in open()).