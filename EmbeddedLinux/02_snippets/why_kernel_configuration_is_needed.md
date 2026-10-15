# Why Kernel Configuration Is Needed

Kernel is highly configurable: `.config` decides what gets built.

Kbuild flow:
1. Read `.config`
2. Generate `include/config/auto.conf` (for Makefiles)
3. Generate `include/generated/autoconf.h` (for C code)
4. Compile modules with those settings

Without `.config`:
- No `auto.conf` → error: `include/config/auto.conf: No such file or directory`
- Conditional compilation (`#ifdef CONFIG_XXX`) fails
- `obj-$(CONFIG_XXX)` in Makefiles cannot resolve

Config once, build many times:
- Change driver code → just `make`
- Change kernel options → `make menuconfig`
- Switch board → run new defconfig

## Default Ways to Generate Kernel Config

### 1. Use defconfig (most common)
```bash
cd kernel
make ARCH=arm CROSS_COMPILE=arm-none-linux-gnueabihf- <board>_defconfig
```
Example:
```bash
make ARCH=arm CROSS_COMPILE=arm-none-linux-gnueabihf- OK3506-S_linux_defconfig
```

Find available defconfigs:
```bash
ls arch/arm/configs/ | grep -i ok3506
```

### 2. Tweak with menuconfig (optional)
```bash
make ARCH=arm CROSS_COMPILE=arm-none-linux-gnueabihf- menuconfig
```
Graphical interface to enable/disable options, then save and exit.

### 3. Use savedefconfig (export minimal config)
```bash
make ARCH=arm CROSS_COMPILE=arm-none-linux-gnueabihf- savedefconfig
```
Generates `defconfig` with only non-default options.

## Verify Config Exists
```bash
ls kernel/.config
ls kernel/include/config/auto.conf
```
Both must exist before building modules.

## Key Points
- `.config` is the single source of truth for what to build.
- `auto.conf` and `autoconf.h` are generated from it.
- Always configure the kernel before building external modules.
- `<board>_defconfig` provides a known-good starting point.
- `menuconfig` is for interactive tweaking after defconfig.