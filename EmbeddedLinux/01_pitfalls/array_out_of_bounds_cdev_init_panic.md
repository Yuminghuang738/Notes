# Array out-of-bounds caused cdev_init kernel panic

- Date: 2026-09-26
- Symptom: `insmod mutildev.ko` caused a kernel panic. PC at `mmioset`, LR at `cdev_init`.
- Root cause: `for (int i = 0; i <= DEV_COUNT; i++)` accessed `char_devs[2]` out of bounds.
- Evidence:
    ```c
    Unable to handle kernel paging request at virtual address af801000`
    PC is at mmioset+0x60/0xa4`
    LR is at cdev_init+0xf/0x28`
    r0 : af800fd0`
    1-page vmalloc region starting at 0xaf800000`
    ```
- Error code:
    ```c
    #define DEV_COUNT 2
    struct device *devices[DEV_COUNT];  
    
    for (int i = 0; i <= DEV_COUNT; i++)
    ```
- Fix:
    ```c
    for (int i = 0; i < DEV_COUNT; i++)
    ```