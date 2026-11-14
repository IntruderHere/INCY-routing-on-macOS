# INCY on macOS: 43 services + the RKN registry via VPN, everything else — direct

> 🇷🇺 **Russian version:** [README.md](README.md)

## ⚖️ Legal disclaimer (read first)

This material is of an **informational and technical nature**: it is a personal guide to configuring the INCY client (routing profile) on macOS. It **is not advertising and does not promote** any VPN service, contains no call to use one, and provides no access to third-party services.

The author is aware that the legislation of the Russian Federation regulates the distribution of information about circumvention tools:
- **Federal Law No. 8-FZ "On Information, Information Technologies and Protection of Information"** (Art. 15) — blocking of pages containing "information on methods and means of ensuring access to information resources whose access is restricted within the territory of the Russian Federation";
- **RKN Order No. 196 dated October 17, 2024** — criteria for prohibited information, including scientific and technical data about VPN services used to circumvent blocks;
- **Federal Law No. 281-FZ dated July 31, 2025** — amendments to the Code of Administrative Offences: advertising of VPN services (Art. 14.2) and deliberate search for extremist materials (Art. 13.53);
- **Federal Law No. 282-FZ dated July 31, 2025** — use of circumvention tools when committing a crime is an aggravating circumstance (Criminal Code Art. 63).

The author **does not call for circumventing blocks** established in accordance with the legislation of the Russian Federation. Use of this material is at the reader's own discretion and risk, in accordance with the current legislation of the Russian Federation. As of the date of publication, the mere fact of using a VPN by an individual is not subject to administrative liability under the Code of Administrative Offences. This text is not legal advice; for an up-to-date opinion, consult a lawyer.

**On a Mac the profile is installed by link** (see [Step 3](#step-3-add-the-routing-profile-key-step)): open it in Safari and macOS will offer to open INCY.

> 📎 **This repository has no QR code.** With 61 rules the link is 2916 characters against the QR limit of 2953 — right at the edge. A QR without headroom is pointless: the next edit would break it, so the single source is [`incy-routing-macos-link.txt`](incy-routing-macos-link.txt) and the link below.

> **v2** · sha256 `incy-routing-macos.json`: `ba8610e738da5010f200e8cd093b42aa6cf81f5bc8f1e61d11edc9848fdc36c6` — if the file's hash doesn't match, the link and QR were built from a different profile version.

## ⚠️ The main difference from iPhone: connection mode

On iPhone there is only one kind of VPN. On desktop INCY has **two modes**, and the profile's behaviour depends entirely on which one you pick:

| Mode | What enters the tunnel | Routing profile |
|---|---|---|
| **TUN (VPN)** | All system traffic: a `utun` interface is created and both TCP and **UDP** go through Xray | works exactly as on iPhone — this is the mode the profile needs |
| **System proxy** | Only apps that read the system proxy settings; **UDP is never proxied** | works partially: other apps bypass the rules, voice and QUIC bypass the tunnel |

**Bottom line: use TUN with this profile.** In system-proxy mode half the rules never fire (the app simply doesn't ask the proxy) and calls can never work, no matter how good the rules are.

TUN on macOS runs through a system extension: macOS asks for permission the first time (System Settings → Privacy & Security). Without it Connect fails without a clear error.

## How this profile differs from the iPhone one

It is built from the same core as the [iPhone version](https://github.com/IntruderHere/INCY-App-routing-on-iPhone) (v9): same 34 services, same full Telegram subnet list, same DNS. Only the desktop deltas differ:

- **`Name` = "Socials via VPN · macOS"** so the profiles don't get mixed up between devices;
- **`geosite:github` added** (64 domains: github.com, raw.githubusercontent.com, ghcr.io, codeload…) — more relevant on a Mac than on a phone;
- **blocked-in-Russia coverage added**: the whole RKN registry in one line, `geosite:blocked-in-ru` (~75 700 domains), plus eight services explicitly — see [the section below](#what-was-added-in-v2-blocked-in-russia);
- **`Geositeurl` is set** — the geo file comes from the updated XTLS/Xray-rules-dat set (7.8 MB), because the preinstalled file has no `blocked-in-ru` category;
- **no `UseChunkFiles`** — geo-file trimming doesn't work on desktop (INCY docs: `useChunkFiles` is implemented for Android/iOS only), so the field is pointless here;
- everything else is as on iPhone (same 34 services, same Telegram subnets, same DNS).

> ⚠️ **The desktop client is pre-alpha** — that is INCY-DEV's own wording. Usable day to day, but breaking changes between releases are expected. The App Store build (the same app as on iPhone) is not subject to that warning.

## Step 1. Install INCY

Two working paths:

1. **App Store** — INCY (LLC ITDEV); the platform list is iOS / Apple TV / **macOS**. Same code as the iPhone app, same profile.
2. **DMG from GitHub** — the Mac build: Apple Silicon `incy-macos-arm64.dmg`, Intel `incy-macos-intel.dmg` (releases page [INCY-DEV/incy-platforms](https://github.com/INCY-DEV/incy-platforms/releases/latest)). This is the pre-alpha desktop client with TUN / system-proxy modes.

## Step 2. Add a subscription (server)

INCY does not provide servers — you need your own subscription (VLESS, VMess, Trojan, Hysteria2…).

1. INCY → "**+**" → "Add subscription" (Import from URL) → paste the link or scan the QR.
2. Allow the VPN profile / system extension when macOS asks.
3. Pick a server and connect — make sure the tunnel comes up.

## Step 3. Add the routing profile (key step)

### Option A — via link (the primary way on a Mac)

1. Copy the link in full.
2. Paste it into the Safari address bar and open it.
3. macOS offers to open INCY → confirm. The profile is added and **activated immediately**.

```
incy://routing/onadd/eyJOYW1lIjoiU29jaWFscyB2aWEgVlBOIMK3IG1hY09TIiwiR2xvYmFsUHJveHkiOiJmYWxzZSIsIlJlbW90ZUROU1R5cGUiOiJEb0giLCJSZW1vdGVETlNEb21haW4iOiJodHRwczovL2Nsb3VkZmxhcmUtZG5zLmNvbS9kbnMtcXVlcnkiLCJSZW1vdGVETlNJUCI6IjEuMS4xLjEiLCJEb21lc3RpY0ROU1R5cGUiOiJEb1UiLCJEb21lc3RpY0ROU0RvbWFpbiI6IiIsIkRvbWVzdGljRE5TSVAiOiI3Ny44OC44LjgiLCJEbnNIb3N0cyI6eyJjbG91ZGZsYXJlLWRucy5jb20iOiIxLjEuMS4xIn0sIkdlb3NpdGV1cmwiOiJodHRwczovL2dpdGh1Yi5jb20vWFRMUy9YcmF5LXJ1bGVzLWRhdC9yZWxlYXNlcy9sYXRlc3QvZG93bmxvYWQvZ2Vvc2l0ZS5kYXQiLCJGYWtlRE5TIjoiZmFsc2UiLCJEb21haW5TdHJhdGVneSI6IklQSWZOb25NYXRjaCIsIkxhc3RVcGRhdGVkIjoiMTc5NDY3MzgwMCIsIkRpcmVjdFNpdGVzIjpbImdlb3NpdGU6cHJpdmF0ZSJdLCJEaXJlY3RJcCI6WyJnZW9pcDpwcml2YXRlIiwiMTAuMC4wLjAvOCIsIjE3Mi4xNi4wLjAvMTIiLCIxOTIuMTY4LjAuMC8xNiIsIjE2OS4yNTQuMC4wLzE2IiwiMjI0LjAuMC4wLzQiLCIyNTUuMjU1LjI1NS4yNTUiXSwiUHJveHlTaXRlcyI6WyJnZW9zaXRlOnRlbGVncmFtIiwiZG9tYWluOnlvdXR1YmUuY29tIiwiZG9tYWluOnlvdXR1LmJlIiwiZG9tYWluOnl0LmJlIiwiZG9tYWluOmdvb2dsZXZpZGVvLmNvbSIsImRvbWFpbjp5dGltZy5jb20iLCJkb21haW46Z2dwaHQuY29tIiwiZG9tYWluOnlvdXR1YmUtbm9jb29raWUuY29tIiwiZG9tYWluOnlvdXR1YmVpLmdvb2dsZWFwaXMuY29tIiwiZG9tYWluOnlvdXR1YmUuZ29vZ2xlYXBpcy5jb20iLCJnZW9zaXRlOmluc3RhZ3JhbSIsImdlb3NpdGU6ZGlzY29yZCIsImdlb3NpdGU6d2hhdHNhcHAiLCJnZW9zaXRlOnR3aXR0ZXIiLCJnZW9zaXRlOmZhY2Vib29rIiwiZ2Vvc2l0ZTptZXNzZW5nZXIiLCJnZW9zaXRlOnNpZ25hbCIsImRvbWFpbjp0ZXh0c2VjdXJlLmNvbSIsImdlb3NpdGU6bGlua2VkaW4iLCJkb21haW46c25hcGNoYXQuY29tIiwiZG9tYWluOnNuYXAuY29tIiwiZG9tYWluOnNjLWNkbi5uZXQiLCJkb21haW46ZmFjZXRpbWUuYXBwbGUuY29tIiwiZG9tYWluOmZ0YXBpLml0dW5lcy5hcHBsZS5jb20iLCJkb21haW46cHVzaC5hcHBsZS5jb20iLCJkb21haW46ZXNzLmFwcGxlLmNvbSIsImdlb3NpdGU6dmliZXIiLCJnZW9zaXRlOm5ldGZsaXgiLCJnZW9zaXRlOnNwb3RpZnkiLCJnZW9zaXRlOnN0ZWFtIiwiZ2Vvc2l0ZTpvcGVuYWkiLCJkb21haW46Z2VtaW5pLmdvb2dsZS5jb20iLCJnZW9zaXRlOmFudGhyb3BpYyIsImRvbWFpbjptaWRqb3VybmV5LmNvbSIsImRvbWFpbjpkZWVwbC5jb20iLCJnZW9zaXRlOmNhbnZhIiwiZ2Vvc2l0ZTpub3Rpb24iLCJkb21haW46bWlyby5jb20iLCJnZW9zaXRlOnBsYXlzdGF0aW9uIiwiZ2Vvc2l0ZTpuaW50ZW5kbyIsImdlb3NpdGU6YmxpenphcmQiLCJnZW9zaXRlOmRpc25leSIsImdlb3NpdGU6Y291cnNlcmEiLCJnZW9zaXRlOmFpcmJuYiIsImdlb3NpdGU6Ym9va2luZyIsImRvbWFpbjpwYXJhbW91bnRwbHVzLmNvbSIsImRvbWFpbjpwYXJhbW91bnQuY29tIiwiZG9tYWluOnVuaXZlcnNhbHBsdXMuY29tIiwiZG9tYWluOnVuaXZlcnNhbC1wbHVzLmZyIiwiZ2Vvc2l0ZTp1Ymlzb2Z0IiwiZ2Vvc2l0ZTplYSIsImdlb3NpdGU6Z2l0aHViIiwiZ2Vvc2l0ZTpibG9ja2VkLWluLXJ1IiwiZ2Vvc2l0ZTpyb2Jsb3giLCJnZW9zaXRlOnJ1dHJhY2tlciIsImdlb3NpdGU6cHJvdG9ubWFpbCIsImdlb3NpdGU6c291bmRjbG91ZCIsImdlb3NpdGU6dGlrdG9rIiwiZ2Vvc2l0ZTpmaWdtYSIsImdlb3NpdGU6ZWJheSIsImdlb3NpdGU6c3BlZWR0ZXN0Il0sIlByb3h5SXAiOlsiOTEuMTA4LjQuMC8yMiIsIjkxLjEwOC44LjAvMjIiLCI5MS4xMDguMTIuMC8yMiIsIjkxLjEwOC4xNi4wLzIyIiwiOTEuMTA4LjIwLjAvMjIiLCI5MS4xMDguNTYuMC8yMiIsIjkxLjEwNS4xOTIuMC8yMyIsIjE0OS4xNTQuMTYwLjAvMjAiLCIxODUuNzYuMTUxLjAvMjQiLCI5NS4xNjEuNjQuMC8yMCIsIjIwMDE6NjdjOjRlODo6LzQ4IiwiMjAwMTpiMjg6ZjIzYzo6LzQ4IiwiMjAwMTpiMjg6ZjIzZDo6LzQ4IiwiMjAwMTpiMjg6ZjIzZjo6LzQ4IiwiMmEwYTpmMjgwOjovMzIiXSwiQmxvY2tTaXRlcyI6W10sIkJsb2NrSXAiOltdfQ==
```

### Option B — via clipboard

Copy the base64 part after `incy://routing/onadd/` → in INCY add a routing profile from the clipboard.

## Step 4. Turn on TUN and verify

1. In the connection settings choose **TUN (VPN)**, not "System proxy".
2. Connect. If macOS asks about the system extension, allow it and restart INCY.
3. Check the IP:
   - Safari → `2ip.ru` — should show your **local** IP (direct rule);
   - Telegram Desktop → ask `@ip_bot` — should show the **VPN server** IP (Telegram is routed through the tunnel).
4. Check services: Telegram, Instagram (web), YouTube, Discord, X, ChatGPT work; your bank, government portals and Yandex stay direct, as usual.
5. If **iCloud Private Relay** is on, turn it off while testing: it intercepts Safari traffic and makes the result unreadable.

## What the profile covers (43 services + the RKN registry)

| Category | Services |
|---|---|
| Messengers | Telegram (+ all its subnets in `ProxyIp`), WhatsApp, Viber, Signal (`geosite:signal` + `textsecure.com`) |
| Social | Instagram, X/Twitter, Facebook + Messenger (`geosite:messenger` — `facebook` does not cover it), LinkedIn, Snapchat |
| Video & music | YouTube (explicit domains + `youtubei.googleapis.com` for the app's API), Netflix, Spotify, Disney+ |
| Voice | Discord, FaceTime (signaling + APNs + IDS `ess.apple.com`) |
| Games | Steam, PlayStation Network, Nintendo, Battle.net, Ubisoft, EA |
| AI & work | OpenAI, Gemini, Anthropic, Midjourney, DeepL, Canva, Notion, Miro, Coursera |
| Travel & streaming | Airbnb, Booking, Paramount+, Universal+ |
| Desktop add-on | **GitHub** (`geosite:github`: github.com, raw.githubusercontent.com, ghcr.io, codeload…) |
| Blocked in Russia | **RKN registry** (`geosite:blocked-in-ru`, ~75 700 domains), Roblox, Rutracker, ProtonMail, SoundCloud, TikTok, Figma, eBay, Speedtest — see [the section below](#what-was-added-in-v2-blocked-in-russia) |

Anything that matches no rule goes **direct** (`GlobalProxy: false`).

**IP rules (`ProxyIp`) — Telegram only:** the full official subnet list ([core.telegram.org/resources/cidr.txt](https://core.telegram.org/resources/cidr.txt)) — 10 IPv4 and 5 IPv6 entries. Telegram (and its calls) is addressed by IP, not by domain, so without these there is neither sync nor voice.

## What was added in v2: blocked in Russia

A single line, `geosite:blocked-in-ru`, covers **the whole RKN registry** — 75,744 domains from [antifilter.download](https://antifilter.download) and [Re:filter](https://github.com/1andrevich/Re-filter-lists), including Meta, X, Discord, Signal, Viber, Snapchat, LinkedIn, Roblox, Rutracker, TikTok, Speedtest, WhatsApp, YouTube, ProtonMail, SoundCloud and blocked media. It updates together with the geo file, so a newly blocked domain enters the route without touching the config.

Added explicitly — services the registry lacks, or ones you want visible:

| Service | Category | Why |
|---|---|---|
| Roblox | `geosite:roblox` | blocked in Russia (2025) |
| Rutracker | `geosite:rutracker` | blocked since 2015 |
| ProtonMail | `geosite:protonmail` | `proton.me` is in the registry |
| SoundCloud | `geosite:soundcloud` | in the registry |
| TikTok | `geosite:tiktok` | restricted mode since 2022 (uploads, streams, foreign content) |
| Figma | `geosite:figma` | left Russia, accounts restricted |
| eBay | `geosite:ebay` | left Russia |
| Speedtest | `geosite:speedtest` | `speedtest.net` is in the registry |

**Dependency.** The preinstalled geo file has no `blocked-in-ru` category, so the profile sets `Geositeurl` to [XTLS/Xray-rules-dat](https://github.com/XTLS/Xray-rules-dat/releases/latest) — 7.8 MB, downloaded once when the profile is added (then checked by hash / `LastUpdated`). If the download fails those rules simply don't apply, while the rest (Telegram, YouTube, GitHub…) keep working from the bundled file.

**Deliberately not included:**

- `geosite:cloudflare` — Cloudflare-hosted sites do break in Russia, but it is a CDN: the rule would push every Russian site behind Cloudflare through the VPN. Add it as one line if you need it and check the speed;
- `geoip:blacklist-ru` — the IP list of blocked networks (requires `Geoipurl` pointing at `geoip-lite.dat`). It catches IP-level blocking, but also drags shared CDN addresses (Google, Cloudflare) into the tunnel even though they are not blocked — with `GlobalProxy: false` that is wasted loss.

## Telegram: why calls don't work

A call is a separate transport: the Telegram server returns `PhoneConnection` entries with concrete **IP:port** (UDP), and with P2P the client tries to reach the peer's device directly. That traffic has no domain — `ProxySites` never routes it, only `ProxyIp` does.

Order of checks on a Mac:

1. **TUN mode.** In system-proxy mode UDP is never proxied — calls cannot work at all.
2. **"Block UDP"** in INCY settings — must be off.
3. **Node.** Some servers drop UDP (anti-abuse, QUIC/Hysteria on networks that shape UDP). Works on node A, not on B — it's the node.
4. **Telegram Desktop → Settings → Advanced settings → Peer-to-peer → "Never".** Otherwise media goes straight into the provider network where voice is filtered.
5. **The other party in Russia also needs the VPN** — the restriction applies on each side.
6. **A nearby node.** For voice, latency and loss matter: 40–70 ms to Europe beats 150 ms across the ocean; robotic audio means packet loss.

To tell profile from tunnel: open `incy://routing/off` (everything into the tunnel). Calls work there but not with the profile → rules; work in neither → UDP/node.

## If something doesn't work

| Problem | Fix |
|---|---|
| Connected, but sites don't load / rules don't apply | "System proxy" mode is on: apps that don't ask the proxy bypass it. Switch to **TUN** |
| TUN won't start, Connect fails silently | The system extension isn't approved: System Settings → Privacy & Security → Allow, then restart INCY |
| Telegram calls are silent while chats work | TUN + "Block UDP" off + P2P → "Never" + another node (see the section above) |
| A blocked service loads but the IP is local | The profile isn't active or the rule didn't match: check the "Routing" section and that the profile is "Socials via VPN · macOS" |
| The profile shows a red "!" | Geo files are downloading. On desktop they are **full** and not trimmed: `geosite.dat` from the profile is 7.8 MB. Wait a minute, that is normal |
| Blocked sites don't open while everything else works | Make sure the profile is active and the geo file finished downloading (the "Routing" section): without it `blocked-in-ru` does not apply |
| Nothing works after a reboot | Make sure INCY runs and TUN is up; if in doubt, "Reset Kill Switch" in the settings |
| You want "everything through VPN" | Open `incy://routing/off` (the profile is kept) |
| You want "everything direct" with no VPN | Just disconnect in INCY |

## Profile JSON (if you want to edit it)

```json
{
 "Name": "Socials via VPN · macOS",
 "GlobalProxy": "false",
 "RemoteDNSType": "DoH",
 "RemoteDNSDomain": "https://cloudflare-dns.com/dns-query",
 "RemoteDNSIP": "1.1.1.1",
 "DomesticDNSType": "DoU",
 "DomesticDNSDomain": "",
 "DomesticDNSIP": "77.88.8.8",
 "DnsHosts": {
  "cloudflare-dns.com": "1.1.1.1"
 },
 "Geositeurl": "https://github.com/XTLS/Xray-rules-dat/releases/latest/download/geosite.dat",
 "FakeDNS": "false",
 "DomainStrategy": "IPIfNonMatch",
 "LastUpdated": "1794673800",
 "DirectSites": [
  "geosite:private"
 ],
 "DirectIp": [
  "geoip:private",
  "10.0.0.0/8",
  "172.16.0.0/12",
  "192.168.0.0/16",
  "169.254.0.0/16",
  "224.0.0.0/4",
  "255.255.255.255"
 ],
 "ProxySites": [
  "geosite:telegram",
  "domain:youtube.com",
  "domain:youtu.be",
  "domain:yt.be",
  "domain:googlevideo.com",
  "domain:ytimg.com",
  "domain:ggpht.com",
  "domain:youtube-nocookie.com",
  "domain:youtubei.googleapis.com",
  "domain:youtube.googleapis.com",
  "geosite:instagram",
  "geosite:discord",
  "geosite:whatsapp",
  "geosite:twitter",
  "geosite:facebook",
  "geosite:messenger",
  "geosite:signal",
  "domain:textsecure.com",
  "geosite:linkedin",
  "domain:snapchat.com",
  "domain:snap.com",
  "domain:sc-cdn.net",
  "domain:facetime.apple.com",
  "domain:ftapi.itunes.apple.com",
  "domain:push.apple.com",
  "domain:ess.apple.com",
  "geosite:viber",
  "geosite:netflix",
  "geosite:spotify",
  "geosite:steam",
  "geosite:openai",
  "domain:gemini.google.com",
  "geosite:anthropic",
  "domain:midjourney.com",
  "domain:deepl.com",
  "geosite:canva",
  "geosite:notion",
  "domain:miro.com",
  "geosite:playstation",
  "geosite:nintendo",
  "geosite:blizzard",
  "geosite:disney",
  "geosite:coursera",
  "geosite:airbnb",
  "geosite:booking",
  "domain:paramountplus.com",
  "domain:paramount.com",
  "domain:universalplus.com",
  "domain:universal-plus.fr",
  "geosite:ubisoft",
  "geosite:ea",
  "geosite:github",
  "geosite:blocked-in-ru",
  "geosite:roblox",
  "geosite:rutracker",
  "geosite:protonmail",
  "geosite:soundcloud",
  "geosite:tiktok",
  "geosite:figma",
  "geosite:ebay",
  "geosite:speedtest"
 ],
 "ProxyIp": [
  "91.108.4.0/22",
  "91.108.8.0/22",
  "91.108.12.0/22",
  "91.108.16.0/22",
  "91.108.20.0/22",
  "91.108.56.0/22",
  "91.105.192.0/23",
  "149.154.160.0/20",
  "185.76.151.0/24",
  "95.161.64.0/20",
  "2001:67c:4e8::/48",
  "2001:b28:f23c::/48",
  "2001:b28:f23d::/48",
  "2001:b28:f23f::/48",
  "2a0a:f280::/32"
 ],
 "BlockSites": [],
 "BlockIp": []
}
```

To rebuild the link after an edit: encode the JSON in base64 without whitespace and open `incy://routing/onadd/{base64}`. Profiles with the same `Name` are updated rather than duplicated; `LastUpdated` (unix time) decides which version is newer.

## Sources

- INCY docs (profile fields, DNS, links): [docs.incy.cc/routing](https://incy.gitbook.io/docs/docs-en/routing)
- Platforms and modes, per-app on Android/desktop only, desktop pre-alpha: [INCY-DEV/incy-platforms](https://github.com/INCY-DEV/incy-platforms)
- Telegram's official subnet list: [core.telegram.org/resources/cidr.txt](https://core.telegram.org/resources/cidr.txt)
- How calls work (`PhoneConnection`, `ip`/`port`, `udp_p2p`): [core.telegram.org/api/calls](https://core.telegram.org/api/calls)
- Domain lists: [v2fly/domain-list-community](https://github.com/v2fly/domain-list-community)
- iPhone version of the profile and the per-service breakdown: [IntruderHere/INCY-App-routing-on-iPhone](https://github.com/IntruderHere/INCY-App-routing-on-iPhone)
