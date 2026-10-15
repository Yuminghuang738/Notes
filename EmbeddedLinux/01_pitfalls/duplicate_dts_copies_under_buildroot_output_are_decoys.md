# Duplicate DTS Copies Under buildroot/output Are Decoys

**Symptom**: Searching the SDK for `OK3506-S-MINI_linux_nand.dts` returns **6 hits in 3 different trees**. Editing one of them changes nothing in the built image.

**Root Cause**: `buildroot/output/*/build/linux-headers-custom/` keeps an independent **copy** (not a symlink) of the kernel DTS directory, because buildroot's `linux-headers` package copies kernel headers out of the kernel tree. Those copies are build snapshots and are never used to compile the kernel.

**Evidence**:
```bash
$ find OK3506_Linux_Source -name 'OK3506-S-MINI_linux_nand.dts'
kernel-6.1/arch/arm/boot/dts/OK3506-S-MINI_linux_nand.dts                                     # real one
buildroot/output/rockchip_ok3506_nand/build/linux-headers-custom/arch/arm/boot/dts/...         # copy
buildroot/output/rockchip_ok3506_recovery/build/linux-headers-custom/arch/arm/boot/dts/...     # copy

$ grep -c get_dts_info_test <a copy>
0                                        # stale content
$ grep -c get_dts_info_test kernel-6.1/arch/arm/boot/dts/OK3506-S-MINI_linux_nand.dts
2                                        # the edited file
```

`rk3506b-evb1-v10.dts` is duplicated the same way (4 decoy copies).

**Fix**: Always edit under the kernel tree, and confirm which file the build consumes:
```bash
# V=1 prints the real DTC input path whenever the dtb is actually recompiled
make -C kernel ARCH=arm CROSS_COMPILE=<toolchain>- OK3506-S-MINI_linux_nand.dtb V=1
```

**Lesson**:
- A `find`/`grep` hit is not proof of the file the build uses; only one copy is an input.
- Resolve the target through `RK_KERNEL_DTS_NAME` in `output/.config`, not by searching filenames.
- Never hand-edit anything under `buildroot/output/` — it is regenerated and your change is lost.
