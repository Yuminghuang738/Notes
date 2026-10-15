# EVB Reference DTS Edited Instead of the Board DTS

**Symptom**: A test node (`get_dts_info_test`) was added to `rk3506b-evb1-v10.dts` and compiled without errors, but the node never showed up in `/proc/device-tree` on the board.

**Root Cause**: `rk3506b-evb1-v10.dts` is the Rockchip RK3506B **EVB reference board** DTS. The OK3506-S-MINI board boots a completely different DTS, selected by the SDK config variable `RK_KERNEL_DTS_NAME`:

```
RK_KERNEL_DTS_NAME="OK3506-S-MINI_linux_nand"
```

Both files sit in the same directory, so the filename is not enough to tell which one the board uses.

**How to identify the DTS the board really uses** (three independent checks):

| Check | Command | Result here |
|-------|---------|-------------|
| SDK config | `grep RK_KERNEL_DTS_NAME output/.config` | `OK3506-S-MINI_linux_nand` |
| Running board | `ssh root@<board> cat /proc/device-tree/model` | `Forlinx OK3506-S Board` |
| include chain | `grep -rl OK3506-S-MINI_linux_nand.dts kernel-6.1/arch/arm/boot/dts/` | only itself |

**Include chain of this board** (the EVB chain is entirely separate):

```
OK3506-S-MINI_linux_nand.dts   (65 lines)   board level: bootargs, fspi, mmc, pinctrl
└─ OK3506-S-MINI-common.dtsi  (603 lines)   board peripherals
   └─ FET3506-S.dtsi           (97 lines)   Forlinx carrier board
      └─ rk3506.dtsi          (501 lines)   SoC level, do not touch
```

**Wrong file** (compiles fine, board never loads it):
```
kernel-6.1/arch/arm/boot/dts/rk3506b-evb1-v10.dts
```

**Correct file**:
```
kernel-6.1/arch/arm/boot/dts/OK3506-S-MINI_linux_nand.dts
```

**Fix**: Resolve the real DTS before editing, never by filename similarity:
```bash
cd OK3506_Linux_Source
DTS=$(grep -oP 'RK_KERNEL_DTS_NAME="\K[^"]+' output/.config)
ls -la kernel/arch/arm/boot/dts/$DTS.dts
```

**Lesson**:
- `RK_KERNEL_DTS_NAME` plus `/proc/device-tree/model` are the ground truth; a similar-looking filename is not.
- A successful compile proves nothing about whether the change can ever take effect.
- Board-level test nodes belong in the board DTS, peripheral changes in `<board>-common.dtsi`, and never in the SoC `rk3506.dtsi`.
