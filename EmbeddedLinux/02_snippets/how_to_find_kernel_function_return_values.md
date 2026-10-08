# How to Find Kernel Function Return Values

Header only gives prototype, not semantics.

Best sources:
1. **Implementation** (.c file) — look at `return` statements
2. **Callers** — grep or elixir "Usage" — see how others handle it
3. **kernel-doc comment** — `Returns zero on success, or -EXXX`
4. **Documentation/** — incomplete, don't rely on it

Common conventions:
| Return type | Success | Failure |
|-------------|---------|---------|
| int | 0 | negative (-EINVAL, -ENOMEM, ...) |
| pointer | valid ptr | NULL or ERR_PTR(-EXXX) |
| ssize_t | bytes | negative |

Error codes: `<linux/errno.h>` (uapi/asm-generic/errno.h)