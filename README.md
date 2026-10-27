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
- **Download test:** when the page opens it downloads continuously for 20 minutes (paced at 50 Mbps, like a player pulling content), then stops and shows a summary: downloaded, average Mbps, stalls (a read that waited 1 s or more), dropped connections, slowest 5 s, seconds with no data, time on pace, and a verdict (Stable / Mostly stable / Unstable). A per-second chart fills in while it runs; red columns are seconds with no data. Tap the panel or press D to run it again. The latency status keeps measuring during the paced download, so it shows latency under that load. Default source is the 25 MB test file hosted with this page (GitHub Pages CDN, read as random 4 MiB ranges over 3 connections); the free LibreSpeed servers were tried and couldn't hold a steady 50 Mbps. GitHub's soft limit is about 100 GB a month for the whole site, roughly 13 twenty-minute runs, so point `dlurl` at an OptiSigns-hosted file for wider use.
- **Network & device:** ISP, public IP, location (ipinfo.io, fallback ipwho.is), speed servers, browser estimate, device, screen.
- **Events:** drops, recoveries, quality changes, speed results and public IP changes.

Test servers: Google, Apple (captive-portal check) and OptiSigns. The connection is graded on the best server, so one slow or blocked server shows in its own row without turning the status poor. Every lost ping is logged as a timeout or an error.

Every page load starts fresh; nothing is kept between reloads.

## Preset links

Same dashboard, different download test. Add `?name=...&ssid=...` to any of them; your options win over the preset.

| Link | Download test |
|---|---|
| https://ahmetoptisigns.github.io/netmon/dashboard/ | none, dashboard only |
| https://ahmetoptisigns.github.io/netmon/dl-5min-full/ | 5 min, no speed limit |
| https://ahmetoptisigns.github.io/netmon/dl-5min/ | 5 min at 50 Mbps (~1.9 GB) |
| https://ahmetoptisigns.github.io/netmon/dl-20min/ | 20 min at 50 Mbps (~7.5 GB) |
| https://ahmetoptisigns.github.io/netmon/dl-1h/ | 1 hour at 50 Mbps (~22 GB) |
| https://ahmetoptisigns.github.io/netmon/dl-4h/ | 4 hours at 50 Mbps (~90 GB) |
| https://ahmetoptisigns.github.io/netmon/dl-8h/ | 8 hours at 50 Mbps (~180 GB) |

The plain link (`/netmon/`) is the 20-minute test. GitHub Pages has a soft limit of about 100 GB a month for the whole site: one 8-hour run, or two 4-hour runs, goes over it, and GitHub may throttle or disable the site. For regular long runs, host a test file on the OptiSigns CDN and add `&dlurl=`.

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
| `dlmin` | `dlmin=20` | download test length in minutes after the page opens, up to 720 (`0` = off, panel hidden) |
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
