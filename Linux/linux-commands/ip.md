
| Flag    | What it Does              | Example         |
| ------- | ------------------------- | --------------- |
| `-4`    | Show IPv4 only            | `ip -4 a`       |
| `-6`    | Show IPv6 only            | `ip -6 r`       |
| `-s`    | Show statistics           | `ip -s link`    |
| `-d`    | More detailed debug info  | `ip -d addr`    |
| `-br`   | Brief, tabular output     | `ip -br addr`   |
| `-json` | Output in JSON (newer ip) | `ip -json addr` |

| Object         | What it Manages           |
| -------------- | ------------------------- |
| `link`         | Network interfaces        |
| `addr` or `a`  | IP addresses              |
| `route` or `r` | Routing table             |
| `neigh`        | ARP / neighbor cache      |
| `rule`         | Routing policy rules      |
| `maddr`        | Multicast addresses       |
| `mroute`       | Multicast routing         |
| `tunnel`       | Tunnels (GRE, SIT, etc.)  |
| `xfrm`         | IPsec states and policies |
| `monitor`      | Monitor changes live      |
## Common COMMANDS by OBJECT

| Object  | Common Commands                          |
| ------- | ---------------------------------------- |
| link    | `show`, `set up`, `set down`, `set name` |
| addr    | `show`, `add`, `del`                     |
| route   | `show`, `add`, `del`                     |
| neigh   | `show`, `add`, `del`                     |
| monitor | `all`, `link`, `route`, `neigh`          |
