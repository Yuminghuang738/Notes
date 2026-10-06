# container_of in open()

Purpose: recover custom device struct from `inode->i_cdev`, store in `filp->private_data`.

```c
filp->private_data = container_of(inode->i_cdev, struct char_dev, dev);
```

Requires cdev embedded:
```c
struct char_dev {
    struct cdev dev;    /* must be embedded */
    ...
};
```

Key points:
- `inode->i_cdev` : VFS stores registered cdev here on open
- `container_of` : member address - offset = struct base address
- `filp->private_data` : later callbacks retrieve device instance

Use in read/write:
```c
struct char_dev *dev = filp->private_data;
```

Why: avoids switch-case on minor number, no global array.

Notes:
- Check `inode->i_cdev` for NULL.
- Member name must match struct definition exactly.