# Embedded Linux Learning Roadmap

| Phase | Goal | Checkpoint |
|-------|------|------------|
| 1. Environment | compile/load/unload a module | build .ko in 5 min |
| 2. Char driver | full char device with read/write | echo/cat works on /dev/mydev |
| 3. Platform + DT | match driver via compatible | probe() called from DT node |
| 4. Subsystems | use I2C/SPI/GPIO/interrupt frameworks | read sensor via I2C |
| 5. Project + debug | independent driver development | full bring-up from scratch |

Principles:
- Follow one tutorial systematically
- Take notes for every problem solved
- Type core code by hand until memorized
- One project per phase
- Use elixir.bootlin.com for API lookup
- Read existing drivers in drivers/