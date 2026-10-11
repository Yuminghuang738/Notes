# IOMUX Write-Enable Bit Offset Wrong

**Symptom**: IOMUX configuration for GPIO0_C0 (pin > 0) does not take effect. GPIO registers are set correctly but the pin stays in its alternate function.

**Root Cause**: Write-enable bit offset was calculated as `pin + 16`, but for a 4-bit function field the write-enable bits are at `pin * 4 + 16`.

**Wrong**:
```c
val |= (0xF << (pin + 16));          /* only correct when pin == 0 */
val &= ~(0xF << (pin * 4));          /* function bits cleared */
```

**Correct** (4-bit field per pin):
```c
val |= (0xF << (pin * 4 + 16));      /* write enable */
val &= ~(0xF << (pin * 4));           /* function = 0 → GPIO */
```

**Why**: For Rockchip IOMUX with 4-bit fields:
- function bits for pin N : `[4N+3 : 4N]`
- write-enable bits       : `[4N+19 : 4N+16]`

`pin + 16` only equals `pin * 4 + 16` when `pin == 0`.

**Fix**: Always align write-enable offset with the field layout:
- 2-bit field → `pin * 2 + 16`
- 3-bit field → `pin * 3 + 16`
- 4-bit field → `pin * 4 + 16`

**Lesson**: Never assume the write-enable position; check the TRM for the exact IOMUX register layout. Verify with `devmem` before writing driver code.