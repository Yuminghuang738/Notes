# Core Structures

| Structure | Represents | Key Point |
|-----------|-----------|-----------|
| `dev_t` | device number | major + minor |
| `cdev` | char device | binds dev_t + file_operations |
| `file_operations` | function table | .open/.read/.write/.release |
| `struct file` | **one open instance** | created per open(), holds f_pos, f_flags |
| `struct class` | **a class of devices** | maps to /sys/class/<name>/ |
| `struct device` | one device in device model | device_create() triggers /dev node |

Corrections:
- `struct file` is NOT the file itself; it's an open instance.
- `class` does NOT create /dev nodes; `device_create` does.