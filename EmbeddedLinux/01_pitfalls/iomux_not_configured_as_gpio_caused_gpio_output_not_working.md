# IOMUX Not Configured as GPIO Caused GPIO Output Not Working

**Symptom**: LED on GPIO0_C0 does not respond, even though driver correctly sets `DR_H` / `DDR_H`.

**Root Cause**: GPIO0_C0 IOMUX not configured as GPIO. Reset default may be an alternate function (SPI, UART, etc). GPIO controller output never reaches the physical pin.

**Debugging Steps**:
1. Verified driver maps `GPIO0_DR_H` and `GPIO0_DDR_H`, direction and output set correctly.
2. Suspected IOMUX.
3. Used `devmem` to force IOMUX to GPIO:
   
   ```bash
   devmem 0xFF950010 32 0x00070000
   ```
   
   High 16 bits write-enable (bit18:16 = 1), low 3 bits = 0 (GPIO function).
   LED turned on immediately.
4. Re-tested driver: `echo 1 > /dev/chardev_led` and `echo 0` work.

**Fix**:
    
in driver init

```c
va_iomux = ioremap(0xFF950010, 4);
writel((0x7 << 16) | 0x0, va_iomux);
```

Then configure `DDR_H` direction.

**Lesson**:
- Do not assume Rockchip pins default to GPIO.
- Always check TRM or configure `pinctrl` in Device Tree before using a pin as GPIO.