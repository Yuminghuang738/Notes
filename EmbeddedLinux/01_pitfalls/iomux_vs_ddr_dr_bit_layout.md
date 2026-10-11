# IOMUX vs DDR/DR Bit Layout

**Symptom**: GPIO output works on pin 0 but fails on other pins. Driver sets direction and data correctly, yet the physical pin does not respond.

**Root Cause**: Bit layout of IOMUX differs from DDR/DR.

| Register | Field | Bits per pin | Offset formula |
|----------|-------|--------------|----------------|
| DDR / DR | write enable | 1 | `pin + 16` |
| DDR / DR | data / direction | 1 | `pin` |
| IOMUX | write enable | N | `pin * N + 16` |
| IOMUX | function select | N | `pin * N` |

Where `N` = function field width (2, 3, or 4 bits depending on SoC).

**Wrong** (using DDR/DR layout for IOMUX):
```c
val |= (0x1 << (pin + 16));       /* write enable */
val &= ~(0x1 << pin);              /* only clears 1 bit */
```

**Correct** (3-bit or 4-bit field):
```c
/* 3-bit field */
val |= (0x7 << (pin * 3 + 16));
val &= ~(0x7 << (pin * 3));

/* 4-bit field */
val |= (0xF << (pin * 4 + 16));
val &= ~(0xF << (pin * 4));
```

**Why it matters**:
- DDR/DR: 1 bit per pin → `pin + 16` and `pin` are correct.
- IOMUX: N bits per pin → must use `pin * N + 16` and `pin * N`.
- `pin + 16` only coincidentally equals `pin * N + 16` when `pin == 0`.

**Fix**: Check TRM for the IOMUX field width (2, 3, or 4 bits), then use `pin * N` for function bits and `pin * N + 16` for write-enable bits.

**Lesson**: Never reuse DDR/DR offset formulas for IOMUX. Each register has its own layout; verify with `devmem` and TRM before writing driver code.