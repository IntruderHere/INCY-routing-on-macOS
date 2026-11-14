# INCY on macOS: 35 services via VPN, everything else — direct

> 🇷🇺 **Russian version:** [README.md](README.md)

## ⚖️ Legal disclaimer (read first)

This material is of an **informational and technical nature**: it is a personal guide to configuring the INCY client (routing profile) on macOS. It **is not advertising and does not promote** any VPN service, contains no call to use one, and provides no access to third-party services.

The author is aware that the legislation of the Russian Federation regulates the distribution of information about circumvention tools:
- **Federal Law No. 8-FZ "On Information, Information Technologies and Protection of Information"** (Art. 15) — blocking of pages containing "information on methods and means of ensuring access to information resources whose access is restricted within the territory of the Russian Federation";
- **RKN Order No. 196 dated October 17, 2024** — criteria for prohibited information, including scientific and technical data about VPN services used to circumvent blocks;
- **Federal Law No. 281-FZ dated July 31, 2025** — amendments to the Code of Administrative Offences: advertising of VPN services (Art. 14.2) and deliberate search for extremist materials (Art. 13.53);
- **Federal Law No. 282-FZ dated July 31, 2025** — use of circumvention tools when committing a crime is an aggravating circumstance (Criminal Code Art. 63).

The author **does not call for circumventing blocks** established in accordance with the legislation of the Russian Federation. Use of this material is at the reader's own discretion and risk, in accordance with the current legislation of the Russian Federation. As of the date of publication, the mere fact of using a VPN by an individual is not subject to administrative liability under the Code of Administrative Offences. This text is not legal advice; for an up-to-date opinion, consult a lawyer.

<p align="center">
  <img src="incy-routing-macos-qr.png" width="360" alt="QR — import the «Socials via VPN · macOS» routing profile">
</p>

**On a Mac the profile is installed by link** (see [Step 3](#step-3-add-the-routing-profile-key-step)): open it in Safari and macOS will offer to open INCY. The QR is there for importing from another device.

> **v1** · sha256 `incy-routing-macos.json`: `63b1b666147399081442d2d341ecf460771a944f1ca97e0da1a84e50a47e7a0d` — if the file's hash doesn't match, the link and QR were built from a different profile version.

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
- **`geosite:github`** added (64 domains: github.com, raw.githubusercontent.com, ghcr.io, codeload…) — more relevant on a Mac than on a phone. Remove it with one line;
- **no `UseChunkFiles`** — geo-file trimming doesn't work on desktop (INCY docs: `useChunkFiles` is implemented for Android/iOS only), so the field is pointless here;
- everything else is byte-for-byte identical to the iPhone profile.

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
incy://routing/onadd/eyJOYW1lIjoiU29jaWFscyB2aWEgVlBOIMK3IG1hY09TIiwiR2xvYmFsUHJveHkiOiJmYWxzZSIsIlJlbW90ZUROU1R5cGUiOiJEb0giLCJSZW1vdGVETlNEb21haW4iOiJodHRwczovL2Nsb3VkZmxhcmUtZG5zLmNvbS9kbnMtcXVlcnkiLCJSZW1vdGVETlNJUCI6IjEuMS4xLjEiLCJEb21lc3RpY0ROU1R5cGUiOiJEb1UiLCJEb21lc3RpY0ROU0RvbWFpbiI6IiIsIkRvbWVzdGljRE5TSVAiOiI3Ny44OC44LjgiLCJEbnNIb3N0cyI6eyJjbG91ZGZsYXJlLWRucy5jb20iOiIxLjEuMS4xIn0sIkZha2VETlMiOiJmYWxzZSIsIkRvbWFpblN0cmF0ZWd5IjoiSVBJZk5vbk1hdGNoIiwiTGFzdFVwZGF0ZWQiOiIxNzk0NjcyMDAwIiwiRGlyZWN0U2l0ZXMiOlsiZ2Vvc2l0ZTpwcml2YXRlIl0sIkRpcmVjdElwIjpbImdlb2lwOnByaXZhdGUiLCIxMC4wLjAuMC84IiwiMTcyLjE2LjAuMC8xMiIsIjE5Mi4xNjguMC4wLzE2IiwiMTY5LjI1NC4wLjAvMTYiLCIyMjQuMC4wLjAvNCIsIjI1NS4yNTUuMjU1LjI1NSJdLCJQcm94eVNpdGVzIjpbImdlb3NpdGU6dGVsZWdyYW0iLCJkb21haW46eW91dHViZS5jb20iLCJkb21haW46eW91dHUuYmUiLCJkb21haW46eXQuYmUiLCJkb21haW46Z29vZ2xldmlkZW8uY29tIiwiZG9tYWluOnl0aW1nLmNvbSIsImRvbWFpbjpnZ3BodC5jb20iLCJkb21haW46eW91dHViZS1ub2Nvb2tpZS5jb20iLCJkb21haW46eW91dHViZWkuZ29vZ2xlYXBpcy5jb20iLCJkb21haW46eW91dHViZS5nb29nbGVhcGlzLmNvbSIsImdlb3NpdGU6aW5zdGFncmFtIiwiZ2Vvc2l0ZTpkaXNjb3JkIiwiZ2Vvc2l0ZTp3aGF0c2FwcCIsImdlb3NpdGU6dHdpdHRlciIsImdlb3NpdGU6ZmFjZWJvb2siLCJnZW9zaXRlOm1lc3NlbmdlciIsImdlb3NpdGU6c2lnbmFsIiwiZG9tYWluOnRleHRzZWN1cmUuY29tIiwiZ2Vvc2l0ZTpsaW5rZWRpbiIsImRvbWFpbjpzbmFwY2hhdC5jb20iLCJkb21haW46c25hcC5jb20iLCJkb21haW46c2MtY2RuLm5ldCIsImRvbWFpbjpmYWNldGltZS5hcHBsZS5jb20iLCJkb21haW46ZnRhcGkuaXR1bmVzLmFwcGxlLmNvbSIsImRvbWFpbjpwdXNoLmFwcGxlLmNvbSIsImRvbWFpbjplc3MuYXBwbGUuY29tIiwiZ2Vvc2l0ZTp2aWJlciIsImdlb3NpdGU6bmV0ZmxpeCIsImdlb3NpdGU6c3BvdGlmeSIsImdlb3NpdGU6c3RlYW0iLCJnZW9zaXRlOm9wZW5haSIsImRvbWFpbjpnZW1pbmkuZ29vZ2xlLmNvbSIsImdlb3NpdGU6YW50aHJvcGljIiwiZG9tYWluOm1pZGpvdXJuZXkuY29tIiwiZG9tYWluOmRlZXBsLmNvbSIsImdlb3NpdGU6Y2FudmEiLCJnZW9zaXRlOm5vdGlvbiIsImRvbWFpbjptaXJvLmNvbSIsImdlb3NpdGU6cGxheXN0YXRpb24iLCJnZW9zaXRlOm5pbnRlbmRvIiwiZ2Vvc2l0ZTpibGl6emFyZCIsImdlb3NpdGU6ZGlzbmV5IiwiZ2Vvc2l0ZTpjb3Vyc2VyYSIsImdlb3NpdGU6YWlyYm5iIiwiZ2Vvc2l0ZTpib29raW5nIiwiZG9tYWluOnBhcmFtb3VudHBsdXMuY29tIiwiZG9tYWluOnBhcmFtb3VudC5jb20iLCJkb21haW46dW5pdmVyc2FscGx1cy5jb20iLCJkb21haW46dW5pdmVyc2FsLXBsdXMuZnIiLCJnZW9zaXRlOnViaXNvZnQiLCJnZW9zaXRlOmVhIiwiZ2Vvc2l0ZTpnaXRodWIiXSwiUHJveHlJcCI6WyI5MS4xMDguNC4wLzIyIiwiOTEuMTA4LjguMC8yMiIsIjkxLjEwOC4xMi4wLzIyIiwiOTEuMTA4LjE2LjAvMjIiLCI5MS4xMDguMjAuMC8yMiIsIjkxLjEwOC41Ni4wLzIyIiwiOTEuMTA1LjE5Mi4wLzIzIiwiMTQ5LjE1NC4xNjAuMC8yMCIsIjE4NS43Ni4xNTEuMC8yNCIsIjk1LjE2MS42NC4wLzIwIiwiMjAwMTo2N2M6NGU4OjovNDgiLCIyMDAxOmIyODpmMjNjOjovNDgiLCIyMDAxOmIyODpmMjNkOjovNDgiLCIyMDAxOmIyODpmMjNmOjovNDgiLCIyYTBhOmYyODA6Oi8zMiJdLCJCbG9ja1NpdGVzIjpbXSwiQmxvY2tJcCI6W119
```

### Option B — via QR code

The QR is at the top of this guide (or in `incy-routing-macos-qr.png`): INCY → QR scanning → point at it. Handy when the profile is being installed on a different Mac than the one showing this file.

### Option C — via clipboard

Copy the base64 part after `incy://routing/onadd/` → in INCY add a routing profile from the clipboard.

## Step 4. Turn on TUN and verify

1. In the connection settings choose **TUN (VPN)**, not "System proxy".
2. Connect. If macOS asks about the system extension, allow it and restart INCY.
3. Check the IP:
   - Safari → `2ip.ru` — should show your **local** IP (direct rule);
   - Telegram Desktop → ask `@ip_bot` — should show the **VPN server** IP (Telegram is routed through the tunnel).
4. Check services: Telegram, Instagram (web), YouTube, Discord, X, ChatGPT work; your bank, government portals and Yandex stay direct, as usual.
5. If **iCloud Private Relay** is on, turn it off while testing: it intercepts Safari traffic and makes the result unreadable.

## What the profile covers (35 services)

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

Anything that matches no rule goes **direct** (`GlobalProxy: false`).

**IP rules (`ProxyIp`) — Telegram only:** the full official subnet list ([core.telegram.org/resources/cidr.txt](https://core.telegram.org/resources/cidr.txt)) — 10 IPv4 and 5 IPv6 entries. Telegram (and its calls) is addressed by IP, not by domain, so without these there is neither sync nor voice.

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
| The profile shows a red "!" | Geo files are downloading. On desktop they are **full** (tens of MB) and are not trimmed — wait, this is normal |
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
 "FakeDNS": "false",
 "DomainStrategy": "IPIfNonMatch",
 "LastUpdated": "1794672000",
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
  "geosite:github"
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
