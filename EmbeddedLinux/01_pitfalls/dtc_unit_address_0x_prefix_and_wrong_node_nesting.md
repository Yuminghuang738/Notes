# DTC Unit Address Must Not Keep the 0x Prefix (and Test Nodes Must Not Be Nested)

**Symptom**: The build reported only a *warning*, so the mistake shipped easily:

```
rk3506b-evb1-v10.dts:31.23-35.5: Warning (unit_address_format):
  /vcc3v3-lcd0-n/get_dts_info_test/led@0xFF950000: unit name should not have leading "0x"
```

**Root Cause 1 — unit address syntax**: The part of a node name after `@` is a *unit address* and must be plain lowercase hex. `0x` is not part of that syntax, so `led@0xFF950000` should be `led@ff950000`.

**Root Cause 2 — node placement**: The test node was inserted *inside* the `vcc3v3-lcd0-n` regulator node:

```dts
vcc3v3_lcd_n: vcc3v3-lcd0-n {
    compatible = "regulator-fixed";
    ...
    get_dts_info_test: get_dts_info_test {     /* wrong level */
        ...
    };
};
```

This is syntactically valid and even gets populated as a platform device, but its path becomes `/vcc3v3-lcd0-n/get_dts_info_test` — it is no longer a top-level test node and is coupled to an unrelated regulator.

**Wrong**:
```dts
led@0xFF950000 {
    compatible = "fire,led_test";
    reg = <0xFF950000 0x00000100>;
};
```

**Correct** (root level, normalised hex):
```dts
get_dts_info_test {
    compatible = "get_dts_info_test";
    #address-cells = <1>;
    #size-cells = <1>;
    status = "okay";

    led@ff950000 {
        compatible = "fire,led_test";
        reg = <0xff950000 0x100>;
        status = "okay";
    };
};
```

**Fix**: Keep `0x...` inside `reg` (those are integers), write node unit addresses without the `0x` prefix, and place standalone test nodes at the root.

**Lesson**:
- Treat DTC warnings as errors; the build still succeeds and the wrong DTB boots.
- The unit address (`node@addr`) is part of the node *name*, `reg` is a *property* — different syntax rules.
- Nesting changes the device path. Put a node where you would want to find it under `/proc/device-tree`.
