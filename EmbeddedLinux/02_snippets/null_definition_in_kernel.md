# NULL Definition in Kernel

Header: `<linux/stddef.h>`

Definition:

```c
#undef NULL
#define NULL ((void *)0)
```

Note:
- Kernel has its own `stddef.h`, different from userspace's `<stddef.h>`.
- No need to include it explicitly; other kernel headers include it automatically.
- Uses `#undef` first to avoid conflicts.