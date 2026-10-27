# Network Monitor

A single-page network dashboard for checking a device's connection (built for Wi-Fi testing on
OptiSigns players). Laid out for a 16:9 screen. Add it in OptiSigns as a **Website** app and push it to a player.

**Live:** https://ahmetoptisigns.github.io/netmon/

## What it shows

- **Status band:** Connection good / fair / poor / No internet, with the reason (latency, loss, jitter over the last 30 s).
- **Tiles:** latency, jitter, packet loss, download, upload, latency under load (bufferbloat), availability.
- **Latency chart:** last 60 min per test server, with an availability bar (green = all answered, yellow = some lost, red = none) and speed-test periods shaded.
- **Test servers:** live ms, 2-minute sparkline, average, p95 and loss for each server.
- **Speed tests:** download and upload bars over the last hour. Download reads `test/blob.bin` from this site (GitHub Pages CDN, 3 streams, 5 s or 25 MB). Upload goes to the fastest LibreSpeed server run by Sharktech (Denver/Chicago/Los Angeles/Las Vegas, 2 streams, size adapts to about 4 s). Nothing depends on Cloudflare.
- **Download test:** when the page opens it downloads continuously for 20 minutes (paced at 50 Mbps, like a player pulling content), then stops and shows a summary: downloaded, average Mbps, stalls (a read that waited 1 s or more), dropped connections, slowest 5 s, seconds with no data, time on pace, and a verdict (Stable / Mostly stable / Unstable). A per-second chart fills in while it runs; red columns are seconds with no data. Tap the panel or press D to run it again. The latency status keeps measuring during the paced download, so it shows latency under that load. Default source is generated data from the LibreSpeed server; point `dlurl` at an OptiSigns-hosted file to test the real content CDN.
- **Network & device:** ISP, public IP, location (ipinfo.io, fallback ipwho.is), speed servers, browser estimate, device, screen.
- **Events:** drops, recoveries, quality changes, speed results and public IP changes.

Test servers: Google, Apple (captive-portal check) and OptiSigns. The connection is graded on the best server, so one slow or blocked server shows in its own row without turning the status poor. Every lost ping is logged as a timeout or an error.

Every page load starts fresh; nothing is kept between reloads.

## URL options

| Option | Example | Meaning |
|---|---|---|
| `name` | `name=Lobby%20X5M` | Label at the top right |
| `ssid`, `sec`, `ap` | `ssid=AAHW-5G&sec=WPA3&ap=W-3` | Wi-Fi network, security type and AP under test, shown at the top (labels only; a web page can't read SSID, security, signal or channel) |
| `interval` | `interval=1000` | ms between pings (default 2000, min 500) |
| `timeout` | `timeout=3000` | ms before a ping counts as lost |
| `range` | `range=120` | minutes shown on the charts (default 60, 5-240) |
| `speed` | `speed=600` | seconds between speed tests (default 300, `0` = off) |
| `speedmb` | `speedmb=10` | fixed download size in MB (default: adaptive) |
| `upload` | `upload=0` | skip the upload test |
| `dlmin` | `dlmin=20` | download test length in minutes after the page opens (`0` = off) |
| `dlrate` | `dlrate=50` | download test pace in Mbps (`0` = full speed; latency status then pauses during it) |
| `dlurl` | `dlurl=https://cdn.optisigns.com/...` | file for the download test (must allow cross-origin reads; fetched again and again) |
| `targets` | `targets=Google\|https://www.google.com/generate_204,CF\|https://www.cloudflare.com/cdn-cgi/trace` | up to 6 https test servers |
| `theme` | `theme=light` | light colors |

Example: `https://ahmetoptisigns.github.io/netmon/?name=Bench%20X5M&ssid=AAHW-5G&sec=WPA3&ap=W-3`

Press **S** or tap the Download/Upload tile to run a speed test now.

## Limits

- "Ping" is a timed HTTPS request (browsers can't send ICMP), so it reads a little higher than command-line `ping`.
- A web page can't read the SSID, signal strength or channel; pass the SSID/AP as labels.
- Data use adds up. Speed tests use about 30-45 MB each. The download test uses about 7.5 GB per 20-minute run at 50 Mbps (more at `dlrate=0`). The info panel shows the running total.
