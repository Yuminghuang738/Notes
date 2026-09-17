# Kernel Headers 

Time: Sep-15-2016

## init.h

- The kernel can take `__init` as hint that the function is used only during the initialization phase and free up used memory resources after.
    
    - The `__init` hint should be added before the function name. 
        
        eg: `static void __init initme(int x, int y);`

    - Insert `__initdata` or `__initconst` between the variable name and equal sign followed by value.

        eg: `static int init_variable __initdata = 0;`

- The kernel can takee `__exit` ad hint tha the function is used onlu during the unload phase and free up used memory resources after.

    - The `__exit` hint should be added before the function name.

        eg: `static void __exit exitme(int x, int y);`

    - Insert `__exitdata` between the variable name and equal sign followed by value.

        eg: `static int exit_variable __exitdata = 0;`

- Use the `module_init` macro to register the module's loading function with the kernel.

    - Return `0` indicates that the module initialized successfully, and a directory named after the module will be created under `/sys/module`.

    - Return a non-zero value indicates that the module initialization failed. 

## printk.h

- `#define printk(fmt, ...) printk_index_wrap(_printk, fmt, ##__VA_ARGS__)`: 

    - A printf-like function implemented within the kernel itself, requires specifying a kern_level definded in the `<linux/kern_levels.h>`, if not specify, it will be set as `KERN_DEFAULT` level.

        eg: `printk(KERN_ERR "Failed\n");`

- Run: `cat /proc/sys/kernel/printk` to check the current console_loglevel. The result shows the **current**, **default**, **minimum** and **boot-time-default** log levels.

- Run: `sudo sh -c "echo x x x x > /proc/sys/kernel/printk"` to change the kern_levels

    eg: `sudo sh -c "echo 7 4 1 7 > /proc/sys/kernel/printk"`

- Reference Documents:

    - [Message logging with printk — The Linux Kernel documentation](https://docs.kernel.org/core-api/printk-basics.html)

## kern_level.h

- `#define KERN_SOH	"\001"`
    
    - `KERN_SOH` is `\001` (ASCII Start Of Header, 0x01), used as an internal marker for printk log levels. `printk(KERN_INFO "msg")` expands to `printk("\0016msg")`. The kernel parses `\001` + digit to extract the level, then strips it. When read from userspace, it appears as <6>.

- This is the kern_levels list:

    - `#define KERN_EMERG	KERN_SOH "0"` /* system is unusable */

    - `#define KERN_ALERT	KERN_SOH "1"` /* action must be taken immediately */
    
    - `#define KERN_CRIT	KERN_SOH "2"` /* critical conditions */
    
    - `#define KERN_ERR	    KERN_SOH "3"` /* error conditions */
    
    - `#define KERN_WARNING	KERN_SOH "4"` /* warning conditions */
    
    - `#define KERN_NOTICE	KERN_SOH "5"` /* normal but significant condition */
    
    - `#define KERN_INFO	KERN_SOH "6"` /* informational */
    
    - `#define KERN_DEBUG	KERN_SOH "7"` /* debug-level messages */