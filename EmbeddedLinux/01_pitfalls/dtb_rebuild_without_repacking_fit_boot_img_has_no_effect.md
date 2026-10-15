# DTB Rebuild Without Repacking the FIT boot.img Has No Effect

**Symptom**: The board DTS was changed, `OK3506-S-MINI_linux_nand.dtb` was rebuilt and even copied to `/userdata/drivers/` on the board, but after reboot the new node was still missing from `/proc/device-tree`.

**Root Cause**: On this board the rootfs is a **read-only squashfs on NAND** and the DTB is not a loose file — it is packed inside the FIT `boot.img` stored in the `boot` MTD partition. Copying a `.dtb` onto the running system cannot change what U-Boot loads.

**Boot chain**:

| Item | Value |
|------|-------|
| rootfs | `squashfs` on `/dev/ubiblock0_0` (read-only) |
| bootargs | `storagemedia=mtd ubi.mtd=4 root=/dev/ubiblock0_0 rootfstype=squashfs` |
| boot partition | `mtd2` = `boot`, size `0xa00000` (10 MiB) |
| boot.img | FIT: `fdt` (dtb) + `kernel` (zImage) + `resource` (resource.img) |

So a DTS change means: **dtb → resource.img → FIT boot.img → flash mtd2**. The DTB also ends up inside `resource.img`, which is why rebuilding only the `.dtb` is not enough.

**Evidence** (the dtb lives in two packaged places, not as a file on the board):
```bash
$ dumpimage -l kernel/zboot.img
 Image 0 (fdt)       Type: Flat Device Tree   Data Size: 52301 Bytes
 Image 1 (kernel)    Type: Kernel Image       Data Size: 5834272 Bytes
 Image 2 (resource)  Type: Multi-File Image   Data Size: 775680 Bytes

$ grep -ac get_dts_info_test kernel/resource.img
1
```

**Wrong** — has no effect at all:
```bash
scp OK3506-S-MINI_linux_nand.dtb root@<board>:/userdata/drivers/
```

**Correct** — regenerate the DTB *and* `resource.img`, then repack the FIT:
```bash
# 1) dtb + zImage + resource.img (+ boot.img/zboot.img from scripts/mkimg)
make -C kernel -j$(( $(nproc) + 1 )) ARCH=arm CROSS_COMPILE=<toolchain>- \
     OK3506-S-MINI_linux_nand.img

# 2) pack the final FIT boot.img
cd OK3506_Linux_Source
./device/rockchip/common/scripts/mk-fitimage.sh kernel/zboot.img \
    device/rockchip/.chip/zboot.its \
    kernel/arch/arm/boot/zImage \
    kernel/arch/arm/boot/dts/OK3506-S-MINI_linux_nand.dtb \
    kernel/resource.img
```

Then flash the boot partition from the board itself (`flash_erase` / `nandwrite` are available):
```bash
flash_erase /dev/mtd2 0 0 && nandwrite -p /dev/mtd2 /userdata/boot.img && sync && reboot
```

**Verify** — extract the DTB from the FIT instead of trusting the file on disk:
```bash
dumpimage -T flat_dt -p 0 -o /tmp/fdt_out kernel/zboot.img
md5sum /tmp/fdt_out kernel/arch/arm/boot/dts/OK3506-S-MINI_linux_nand.dtb   # must be identical
```

**Lesson**:
- A successful `make <board>.dtb` only means the compiler is happy; whether it can ever boot is a *packaging* question.
- When the DTB is embedded in a FIT/resource image, rebuilding the `.dtb` alone never takes effect.
- Always ask first: where does the bootloader actually read this file from?
