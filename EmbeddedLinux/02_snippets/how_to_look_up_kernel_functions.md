## How to Look Up Kernel Functions

1. **elixir.bootlin.com** — fastest, searchable, version-switchable
2. **Header** (`include/linux/*.h`) — prototype, params, return type
3. **Source** (`*.c`) — actual implementation
4. **Docs** (`Documentation/`) — usage, design, caveats (not exhaustive)
5. **grep callers** — see how other drivers use it

Best workflow:
elixir search → header prototype → source impl → grep callers

Note: kernel has no man pages for internal functions.