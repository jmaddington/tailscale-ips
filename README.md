# Tailscale DERP Server IPs

Auto-updated list of Tailscale DERP relay server IPs, parsed hourly from the [official DERP map](https://controlplane.tailscale.com/derpmap/default).

Useful for firewall allowlists (pfSense, OPNsense, etc.) where you need to permit traffic to Tailscale relay servers.

## Files

| File | Description |
|------|-------------|
| `tailscale.all.txt` | All IPs (IPv4 + IPv6) |
| `tailscale.all-ipv4.txt` | All IPv4 addresses only |
| `tailscale.all-ipv6.txt` | All IPv6 addresses only |
| `tailscale.region.<id>.txt` | IPs for a specific region by ID (e.g., `tailscale.region.13.txt`) |
| `tailscale.region.<name>.txt` | IPs for a specific region by name (e.g., `tailscale.region.denver.txt`) |

## Usage with pfSense

You can use URL Table aliases in pfSense to automatically pull these lists:

1. Go to **Firewall > Aliases**
2. Add a new alias with type **URL Table (IPs)**
3. Point it at the raw GitHub URL, e.g.:
   ```
   https://raw.githubusercontent.com/<owner>/tailscale-ips/main/tailscale.all-ipv4.txt
   ```
4. Set the update frequency (e.g., 1 hour)
5. Use the alias in your firewall rules

Lines starting with `#` are comments and will be ignored by most firewall parsers.

## Regions

| ID | Code | Name |
|----|------|------|
| 1 | nyc | New York City |
| 2 | sfo | San Francisco |
| 3 | sin | Singapore |
| 4 | fra | Frankfurt |
| 5 | syd | Sydney |
| 6 | blr | Bengaluru |
| 7 | tok | Tokyo |
| 8 | lhr | London |
| 9 | dfw | Dallas |
| 10 | sea | Seattle |
| 11 | sao | Sao Paulo |
| 12 | ord | Chicago |
| 13 | den | Denver |
| 14 | ams | Amsterdam |
| 15 | jnb | Johannesburg |
| 16 | mia | Miami |
| 17 | lax | Los Angeles |
| 18 | par | Paris |
| 19 | mad | Madrid |
| 20 | hkg | Hong Kong |
| 21 | tor | Toronto |
| 22 | waw | Warsaw |
| 23 | dbi | Dubai |
| 24 | hnl | Honolulu |
| 25 | nai | Nairobi |
| 26 | nue | Nuremberg |
| 27 | iad | Ashburn |
| 28 | hel | Helsinki |

## How it works

A GitHub Action runs hourly, fetches the DERP map JSON, extracts all IPv4/IPv6 addresses from every node, and commits any changes.
