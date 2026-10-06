# Kernel goto Error Handling Style

Pattern: acquire resources in order, on failure goto a cleanup label.

Rules:
1. Each label cleans up ONE resource.
2. Cleanup order = reverse of acquisition order (stack unwinding).
3. Labels fall through to previous cleanup steps.
4. Use only for error handling, not normal flow.

Naming: `err_xxx` (most common), `out_xxx`, `fail_xxx`.

Example:
```c
ret = alloc_a();
if (ret)
    return ret;

ret = alloc_b();
if (ret)
    goto err_a;

ret = alloc_c();
if (ret)
    goto err_b;

return 0;

/* fall through */
err_b:
    free_b();
    
err_a:
    free_a();
    return ret;