# 🎙️ LanVoice

**Peer-to-peer voice chat in your browser.** No accounts, no downloads, no servers to run.

All voice data flows directly between browsers via WebRTC — nothing goes through a server. When peers are on the same LAN, audio stays entirely local.

## Two Versions

| | [`index.html`](index.html) | [`zero-dep/index.html`](zero-dep/index.html) |
|---|---|---|
| **Name** | LanVoice | LanVoice Zero |
| **Dependencies** | [PeerJS](https://peerjs.com/) CDN (~50KB) | **None** — pure browser APIs |
| **Join flow** | 6-char room code, one step | ~220-char invite code, two-step exchange |
| **Signaling** | PeerJS free cloud relay | Manual code copy-paste |
| **Best for** | Ease of use | Maximum privacy & independence |

Both versions share the same voice features, design, and encryption.

## ✨ Features

- **P2P Voice Chat** — Direct WebRTC audio with Opus codec
- **Full Mesh + Star Fallback** — Direct mesh for small rooms, auto-switches to star (hub) topology at 5+ peers
- **Smart Hub Election** — Latency-based: the peer with the best connectivity becomes the audio hub
- **LAN Optimized** — Audio stays on your local network when peers are nearby
- **Host Rotation** — Automatic host election when the current host leaves
- **Speaking Indicators** — Animated glow ring shows who's talking
- **Mute / Deafen** — Control your mic and incoming audio
- **Configurable Audio Quality** — 5 presets from Max (128 kbps) to Ultra Low (8 kbps) for slow connections
- **Kick** — Host can remove users from the room
- **Keyboard Shortcuts** — `M` to mute, `D` to deafen
- **Settings** — Mic device selector, master volume, audio quality
- **Single File** — Each version is one self-contained HTML file

## 🚀 Quick Start

### Hosted (GitHub Pages)

- **Standard version**: [https://YOUR_USERNAME.github.io/LanVoice/](https://YOUR_USERNAME.github.io/LanVoice/)
- **Zero-dep version**: [https://YOUR_USERNAME.github.io/LanVoice/zero-dep/](https://YOUR_USERNAME.github.io/LanVoice/zero-dep/)

### Run locally

```bash
npx -y serve .
```

Then open `http://localhost:3000` in your browser.

## 📖 How It Works

### Standard Version (`index.html`)

1. Enter your name → **Create Room**
2. Share the 6-character room code or the invite link
3. Others paste the code or open the link → instant connection
4. Talk!

```
Browser A  ◄══════ Direct P2P Audio ══════►  Browser B
    ▲                                            ▲
    └──────── PeerJS Cloud (signaling) ──────────┘
                   (tiny metadata only)
```

### Zero-Dep Version (`zero-dep/index.html`)

1. Enter your name → **Create Room** → a ~220-char invite code appears
2. Copy & share the invite code (via DM, text, in person, etc.)
3. Other person pastes it → an **answer code** is generated
4. They copy the answer code and send it back to you
5. You paste the answer → **connected!**
6. Additional peers join the same way — mesh connections are handled automatically

```
Browser A  ◄══════ Direct P2P Audio ══════►  Browser B
    ▲                                            ▲
    └──── Manual code exchange (one-time) ───────┘
              (no server involved at all)
```

## 🔒 Security

WebRTC mandates encryption — it cannot be disabled:

| Layer | Protocol | What it protects |
|---|---|---|
| Voice data | **SRTP** | All audio is encrypted between peers |
| Data channels | **DTLS** | Signaling and control messages |
| Key exchange | **DTLS handshake** | Keys negotiated directly between browsers |

**MITM considerations:**
- After connection, audio is encrypted end-to-end. No one (ISP, router, etc.) can decrypt it.
- The signaling phase (PeerJS relay or manual code exchange) is the trust boundary. In the zero-dep version, you control the exchange channel entirely.

## ⚙️ Technical Details

- **Topology**: Adaptive — mesh for 1–4 peers, star for 5+
  - **Mesh** (🔗): Every peer ↔ every peer. Lowest latency, highest per-peer bandwidth.
  - **Star** (⭐): All audio routes through a hub. Saves bandwidth for large rooms.
  - Transition includes a ~1–2 second audio interruption.
  - Hysteresis prevents flip-flopping near the threshold.
- **Hub Election**: When switching to star, each peer measures round-trip latency to all others. The peer with the lowest average latency is elected as the audio hub.
- **Audio Mixing**: The star hub uses the Web Audio API (`MediaStreamDestination`) to create personalized mixes — each peer hears everyone except themselves.
- **Max Participants**: ~15 (star mode hub handles 2×(N–1) audio streams)
- **Codec**: Opus (built into WebRTC)
- **Audio Quality**: Configurable via `RTCRtpSender.setParameters()`. Both peers negotiate the lower of their preferences:
  - Max (128 kbps) — crystal clear, best for LAN
  - High (64 kbps) — great for most connections
  - Medium (32 kbps) — good for slower connections
  - Low (16 kbps) — acceptable voice quality, minimal bandwidth
  - Ultra Low (8 kbps) — extreme bandwidth saving
- **STUN**: Google's public STUN servers (for NAT traversal)
- **TURN**: Not included (LAN and most internet connections work without it)
- **SDP Compression** (zero-dep): JSON + deflate-raw + base64url encoding

## 🌐 Browser Support

| Browser | Standard | Zero-Dep |
|---|---|---|
| Chrome | 80+ | 80+ |
| Edge | 80+ | 80+ |
| Firefox | 80+ | 113+ (needs `CompressionStream`) |
| Safari | 14.1+ | 16.4+ (needs `CompressionStream`) |

## 🚀 Deploy to GitHub Pages

1. Create a GitHub repository
2. Push all files (`index.html`, `zero-dep/index.html`, `README.md`)
3. Go to **Settings → Pages → Source: Deploy from a branch → main → / (root)**
4. Your app will be live at `https://<username>.github.io/<repo>/`

> **Note:** `getUserMedia` (microphone access) requires a secure context — GitHub Pages serves over HTTPS automatically.

## 💖 Support

If you find LanVoice useful, consider [buying me a coffee](https://hdzd.github.io/donate/)!

## 📄 License

MIT
