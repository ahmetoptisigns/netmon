# Network Monitor

A single-page network dashboard for checking a device's connection (built for Wi-Fi testing on
OptiSigns players). Laid out for a 16:9 screen. Add it in OptiSigns as a **Website** app and push it to a player.

**Live:** https://ahmetoptisigns.github.io/netmon/

## What it shows

- **Status band:** Connection good / fair / poor / No internet, with the reason (latency, loss, jitter over the last 30 s).
- **Tiles:** latency, jitter, packet loss, download, upload, latency under load (bufferbloat), availability.
- **Latency chart:** last 60 min per test server, with an availability bar (green = all answered, yellow = some lost, red = none) and speed-test periods shaded.
- **Test servers:** live ms, 2-minute sparkline, average, p95 and loss for each server.
- **Speed tests:** download and upload bars over the last hour (Cloudflare speed endpoints, test size adapts to about 4 s).
- **Network & device:** ISP, public IP, location, Cloudflare edge, DNS/TCP/TLS setup time, browser link info, device, screen.
- **Events:** drops, recoveries, quality changes, speed results and public IP changes.

History is saved in the browser, so a page reload (for example a playlist loop) keeps the last hour.

## URL options

| Option | Example | Meaning |
|---|---|---|
| `name` | `name=Lobby%20X5M` | Label at the top right |
| `ssid`, `ap` | `ssid=AAHW-5G&ap=W-3` | Wi-Fi network and AP under test (labels only; a web page can't read them) |
| `interval` | `interval=1000` | ms between pings (default 2000, min 500) |
| `timeout` | `timeout=3000` | ms before a ping counts as lost |
| `range` | `range=120` | minutes shown on the charts (default 60, 5-240) |
| `speed` | `speed=600` | seconds between speed tests (default 300, `0` = off) |
| `speedmb` | `speedmb=10` | fixed download size in MB (default: adaptive) |
| `upload` | `upload=0` | skip the upload test |
| `targets` | `targets=Google\|https://www.google.com/generate_204,CF\|https://1.1.1.1/cdn-cgi/trace` | up to 6 https test servers |
| `theme` | `theme=light` | light colors |
| `reset` | `reset=1` | forget saved history |

Example: `https://ahmetoptisigns.github.io/netmon/?name=Bench%20X5M&ssid=AAHW-5G&ap=W-3`

Press **S** or tap the Download/Upload tile to run a speed test now.

## Limits

- "Ping" is a timed HTTPS request (browsers can't send ICMP), so it reads a little higher than command-line `ping`.
- A web page can't read the SSID, signal strength or channel; pass the SSID/AP as labels.
- Speed tests use data: about 5-50 MB each, depending on link speed. The panel shows the running total.
