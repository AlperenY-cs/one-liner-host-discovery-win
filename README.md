# Fast Live Host Discovery on Windows CMD

This simple one-liner script is used to discover **live hosts** inside a `/20` subnet range using **only Windows CMD** (no PowerShell, no external tools).

## Script

for /L %i in (16,1,31) do @for /L %j in (1,1,254) do @ping -n 1 -w 100 10.2.%i.%j | find "TTL="

- `%i` iterates over network blocks (`10.2.16.x`, `10.2.17.x`, ..., `10.2.31.x`).
- `%j` iterates over each host inside the block (`.1` to `.254`).
- `ping` sends one packet (`-n 1`) with a 100ms timeout (`-w 100`).
- `find "TTL="` filters alive hosts based on ping response.

## Why from 16 to 31?

Given an example:

- **IP Address:** `10.2.23.59`
- **Netmask:** `255.255.240.0`

This corresponds to a **/20 subnet**.  
A `/20` netmask covers **4096 addresses**, and the block boundaries are multiples of 16 in the third octet.

The math behind:

| Subnet          | Range                  |
|:----------------|:------------------------|
| 10.2.0.0/20     | 10.2.0.0 – 10.2.15.255   |
| 10.2.16.0/20    | 10.2.16.0 – 10.2.31.255  |
| 10.2.32.0/20    | 10.2.32.0 – 10.2.47.255  |
| ...             | ...                      |

Since `23` falls between `16` and `31`,  
we are inside the `10.2.16.0/20` network.

Thus, we need to scan **from 10.2.16.1 to 10.2.31.254**.

## Notes
- It is **fast** but not as powerful as full network scanners (like `nmap`), since it relies only on ICMP Echo Replies (ping).

