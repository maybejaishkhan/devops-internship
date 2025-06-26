> [!warning] use ss instead

> **netstat** --> displays network connections, routing tables, interface statistics, and more. It’s very useful for checking open ports, active connections, and network diagnostics.

| Command      | What it does                                              |
| ------------ | --------------------------------------------------------- |
| `netstat`    | Show basic active connections (TCP/UDP)                   |
| `netstat -a` | Show all connections + listening ports                    |
| `netstat -t` | Show only TCP connections                                 |
| `netstat -u` | Show only UDP connections                                 |
| `netstat -l` | Show only listening ports                                 |
| `netstat -p` | Show PID/program name for each connection                 |
| `netstat -n` | Show numerical addresses/ports (skip DNS lookup — faster) |
| `netstat -r` | Show routing table                                        |
| `netstat -i` | Show network interface statistics                         |
| `netstat -s` | Show protocol statistics (packets, errors, etc.)          |

```bash
# Show all listening TCP/UDP ports, numerically.
netstat -tuln

# Show all listening TCP/UDP ports with PIDs, numerically.
netstat -tulpn

# Show all active TCP/UDP connections with PIDS, numerically.
netstat -tunap

# Show all listening TCP ports with PIDS, numerically.
netstat -plnt
```
