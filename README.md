# Tailscale DERP Server IPs

Auto-updated list of Tailscale DERP relay server IPs, parsed daily from the [official DERP map](https://controlplane.tailscale.com/derpmap/default).

Useful for firewall allowlists (pfSense, OPNsense, etc.) where you need to permit traffic to Tailscale DERP relay servers.

This is not an official Tailscale project. It is a community resource.

## Quick Links

| List | Raw URL |
|------|---------|
| All IPs | [`tailscale.all.txt`](https://raw.githubusercontent.com/jmaddington/tailscale-ips/main/tailscale.all.txt) |
| All IPv4 | [`tailscale.all-ipv4.txt`](https://raw.githubusercontent.com/jmaddington/tailscale-ips/main/tailscale.all-ipv4.txt) |
| All IPv6 | [`tailscale.all-ipv6.txt`](https://raw.githubusercontent.com/jmaddington/tailscale-ips/main/tailscale.all-ipv6.txt) |

## Files

| Pattern | Description | Example |
|---------|-------------|---------|
| `tailscale.all.txt` | All IPs (IPv4 + IPv6) | |
| `tailscale.all-ipv4.txt` | All IPv4 addresses | |
| `tailscale.all-ipv6.txt` | All IPv6 addresses | |
| `tailscale.region.<id>.txt` | IPs by region ID | `tailscale.region.13.txt` |
| `tailscale.region.<name>.txt` | IPs by region name | `tailscale.region.denver.txt` |

Each file contains one IP per line. Region files include a `#` comment header with the region name and code. Lines starting with `#` are ignored by most firewall parsers.

## Usage with pfSense

You can use URL Table aliases in pfSense to automatically pull these lists:

1. Go to **Firewall > Aliases**
2. Add a new alias with type **URL Table (IPs)**
3. Point it at the raw GitHub URL, e.g.:
   ```
   https://raw.githubusercontent.com/jmaddington/tailscale-ips/main/tailscale.all-ipv4.txt
   ```
4. Set the update frequency (e.g., 1 day -- the list is updated once a day, so there's no need to poll more often)
5. Use the alias in your firewall rules

For a specific region only:
```
https://raw.githubusercontent.com/jmaddington/tailscale-ips/main/tailscale.region.denver.txt
```

## Usage with OPNsense

1. Go to **Firewall > Aliases**
2. Create a new alias with type **URL Table (IPs)**
3. Enter the raw URL and set a refresh interval

## Regions

| ID | Code | Name | File by Name |
|----|------|------|--------------|
| 1 | nyc | New York City | `tailscale.region.new-york-city.txt` |
| 2 | sfo | San Francisco | `tailscale.region.san-francisco.txt` |
| 3 | sin | Singapore | `tailscale.region.singapore.txt` |
| 4 | fra | Frankfurt | `tailscale.region.frankfurt.txt` |
| 5 | syd | Sydney | `tailscale.region.sydney.txt` |
| 6 | blr | Bengaluru | `tailscale.region.bengaluru.txt` |
| 7 | tok | Tokyo | `tailscale.region.tokyo.txt` |
| 8 | lhr | London | `tailscale.region.london.txt` |
| 9 | dfw | Dallas | `tailscale.region.dallas.txt` |
| 10 | sea | Seattle | `tailscale.region.seattle.txt` |
| 11 | sao | Sao Paulo | `tailscale.region.sao-paulo.txt` |
| 12 | ord | Chicago | `tailscale.region.chicago.txt` |
| 13 | den | Denver | `tailscale.region.denver.txt` |
| 14 | ams | Amsterdam | `tailscale.region.amsterdam.txt` |
| 15 | jnb | Johannesburg | `tailscale.region.johannesburg.txt` |
| 16 | mia | Miami | `tailscale.region.miami.txt` |
| 17 | lax | Los Angeles | `tailscale.region.los-angeles.txt` |
| 18 | par | Paris | `tailscale.region.paris.txt` |
| 19 | mad | Madrid | `tailscale.region.madrid.txt` |
| 20 | hkg | Hong Kong | `tailscale.region.hong-kong.txt` |
| 21 | tor | Toronto | `tailscale.region.toronto.txt` |
| 22 | waw | Warsaw | `tailscale.region.warsaw.txt` |
| 23 | dbi | Dubai | `tailscale.region.dubai.txt` |
| 24 | hnl | Honolulu | `tailscale.region.honolulu.txt` |
| 25 | nai | Nairobi | `tailscale.region.nairobi.txt` |
| 26 | nue | Nuremberg | `tailscale.region.nuremberg.txt` |
| 27 | iad | Ashburn | `tailscale.region.ashburn.txt` |
| 28 | hel | Helsinki | `tailscale.region.helsinki.txt` |

## How It Works

A [GitHub Action](.github/workflows/update-tailscale-ips.yml) runs once a day (12:00 UTC):

1. Fetches the DERP map JSON from `https://controlplane.tailscale.com/derpmap/default`
2. Parses all regions and extracts IPv4/IPv6 addresses from each node
3. Writes the IP lists to files (one IP per line, sorted)
4. Commits and pushes the updated lists **only if the IPs changed**

When the IPs *did* change, the commit message is: `Tailscale IPs as of YYYY-MM-DD`

### Keepalive commits (and why this repo force-pushes)

GitHub automatically disables scheduled workflows after 60 days of repository
inactivity, and scheduled runs alone do **not** count as activity -- only
commits do. Since the DERP IPs often go weeks or months without changing, the
workflow keeps itself alive with a "keepalive" commit on the days nothing
changed:

- The keepalive is a single empty commit at the tip of `main`, with the message
  `YYYY-MM-DD - keep github workflow alive`.
- To avoid piling up one of these per quiet day, the workflow **rewrites the
  existing keepalive commit in place** (`git commit --amend --allow-empty`) and
  **force-pushes** it, rather than adding a new one. So `main` carries at most
  one keepalive commit at any time.

**If you clone or track this repo:** `main`'s history is periodically rewritten
by these force-pushes, so a plain `git pull` may complain about diverged
history. This repo is intended to be consumed via the raw file URLs above (raw
URLs are unaffected by history rewrites), not cloned. If you do keep a clone,
use `git fetch && git reset --hard origin/main` to re-sync, and don't build on
top of the keepalive commit -- it will be replaced.

## What are DERP servers?

Tailscale uses DERP (Designated Encrypted Relay for Packets) servers as fallback relays when direct peer-to-peer connections can't be established. They are not used for normal traffic when a direct WireGuard connection succeeds, but firewalls need to allow access to them for Tailscale to function when NAT traversal fails.

See the [Tailscale documentation on DERP](https://tailscale.com/kb/1232/derp-servers) for more details.
