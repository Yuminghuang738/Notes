# BUS_ATTR Macro Removed in Linux Kernel Caused Build Failure

**Symptom**: `make` fails with `expected ‘)’ before numeric constant` at `BUS_ATTR(...)`, then `bus_attr_xbus_test` is reported as undeclared.

**Root Cause**: The `BUS_ATTR` macro was removed after Linux 5.1 (commit `cd1b772d4881`). The old 4-argument form `BUS_ATTR(name, mode, show, store)` is no longer supported.

**Debugging Steps**:
1. Compiler pointed to:
   ```c
   BUS_ATTR(xbus_test, S_IRUSR, xbus_test_show, NULL);
   ```
2. Checked kernel source/version: `BUS_ATTR` is not defined.
3. Found replacement macros: `BUS_ATTR_RO`, `BUS_ATTR_RW`, `BUS_ATTR_WO`.

**Fix**:
Replace the old macro with the new one:

```c
BUS_ATTR_RO(xbus_test);
```

Then `bus_create_file(&xbus, &bus_attr_xbus_test);` and `bus_remove_file(&xbus, &bus_attr_xbus_test);` work as expected. No manual permission argument is needed.

**Lesson**:
- Do not use obsolete `BUS_ATTR` with explicit mode.
- Use `BUS_ATTR_RO` / `BUS_ATTR_RW` / `BUS_ATTR_WO`; permissions are implied.
- Always check kernel API changes when targeting newer kernels.