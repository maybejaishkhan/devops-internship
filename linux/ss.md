> **ss** --> A faster, more powerful replacement for netstat (which is deprecated). It supports most of the same flags, plus advanced built-in filtering.

1. **Built-in Filtering**: It has filters like `state`, `sport`, `dport`, `src` and `dst` so no need to use `grep`.
2. **Numerical sorting by Default**: No need to use `-n` flag.
3. Use `ip route`/`ip -s link` command instead of `netstat -r`/`netstat -i`.

| Filter                     | Usage                               |
| -------------------------- | ----------------------------------- |
| `state <STATE>`            | By state (ESTABLISHED, LISTEN etc.) |
| `sport = :<port>`          | By source port                      |
| `dport = :<port>`          | By destination port                 |
| `src <ip>`                 | By source ip                        |
| `dst <ip>`                 | By destination ip                   |
| `<filter> '( <filter> )'`  | Nesting filters                     |
| `( <filter> or <filter> )` | Logical OR                          |
```bash
# Show incoming connections from port 22 (SSH)
ss -t state ESTABLISHED '( sport = :22 )'

# Show outgoing connections to port 22 (SSH)
ss -t state ESTABLISHED '( dport = :22 )'

# Show incoming AND outgoing connections from port 22 (SSH)
ss -t state ESTABLISHED '( sport = :22 or dport = :22)'

# Show all listening TCP sockets
ss -t state LISTEN
```
