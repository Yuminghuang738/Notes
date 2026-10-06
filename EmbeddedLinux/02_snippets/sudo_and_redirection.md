# sudo and Redirection

Problem: `sudo echo 0 > file` fails with permission denied.

Reason: `>` is handled by the shell, not by `echo`. `sudo` only elevates `echo`, not the redirection.

| Form | echo priv | `>` priv | Result |
|------|-----------|----------|--------|
| `sudo echo 0 > file` | root | user | ❌ |
| `sudo sh -c 'echo 0 > file'` | root | root | ✅ |
| `echo 0 \| sudo tee file` | user | root (tee) | ✅ |

Rule: `sudo` elevates only the command after it, not shell redirection.