# Where Are Driver Examples in Kernel Source

| Directory | Purpose |
|-----------|---------|
| `drivers/` | real production drivers (best learning material) |
| `samples/` | official teaching examples (advanced topics) |
| `Documentation/` | docs with code snippets |
| `tools/` | userspace tools |

Recommended starting points:
- `drivers/leds/leds-gpio.c`      : simple Platform + GPIO
- `drivers/gpio/gpio-mockup.c`    : full char device example
- `drivers/misc/`                 : short misc device drivers

How to find:
- elixir.bootlin.com → browse `drivers/`
- `grep -rln "platform_driver_register" drivers/`
- `find drivers/ -name "*gpio*led*"`