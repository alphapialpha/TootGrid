# <img src="TootGridLogo.webp" height="32" alt="TootGrid Logo"> TootGrid

A lightweight, self-contained Mastodon "toots" viewer. Display public posts from one or more Mastodon instances and/or specific user accounts as a clean, responsive card grid — with dark mode, media carousels, auto-refresh, and local caching.

**No build step. No framework. No server-side language. Just three files.**

---

## 📑 Table of Contents

- [✨ Why TootGrid?](#-why-tootgrid)
- [🚀 Quick Start](#-quick-start)
- [⚙️ Configuration (config.json)](#️-configuration-configjson)
  - [Top-level fields](#top-level-fields)
  - [layout](#layout)
  - [theme](#theme)
  - [autoRefresh](#autorefresh)
  - [instances](#instances)
  - [accounts](#accounts)
- [🖼️ Logo Images](#️-logo-images-optional)
- [💾 Caching](#-caching)
- [🤖 AI Disclosure](#-ai-disclosure)
- [☕ About](#-about)
- [📄 License](#-license)

---

## ✨ Why TootGrid?

- **Three files only** — `config.json`, `index.html`, `style.css` (plus logo images)
- **Zero server requirements** — any static file host works: Apache, Nginx, lighttpd, GitHub Pages, Netlify, a Raspberry Pi. No PHP, no Python, no Node.js, no database
- **No dependencies** — no npm, no CDN, no external libraries. Works fully offline once loaded
- **Entirely config-driven** — change instances, accounts, theme colors, refresh interval, layout mode all from a single JSON file, no code changes needed
- **Respects the Fediverse** — uses only the public Mastodon API, no authentication required
- **Keyboard & screen reader accessible** — every card has a proper focusable link

---

## 🚀 Quick Start

1. Drop `config.json`, `index.html`, and `style.css` + images into any directory on a static web host
2. Edit `config.json` to point at your Mastodon instances or accounts
3. Open in a browser

That's it.

For local development, any simple static server works:

```bash
# Python
python3 -m http.server 8080

# Node (npx, no install needed)
npx serve .

# PHP
php -S localhost:8080
```

---

## ⚙️ Configuration (`config.json`)

Everything is controlled from `config.json`. All fields are optional and fall back to sensible defaults if omitted.

---

### Top-level fields

```json
{
  "title": "My Fancy TootGrid",
  "favicon": "favicon.png",
  "cacheKey": "TootGrid_posts_v1"
}
```

| Field | Description | Default |
|---|---|---|
| `title` | Shown in the browser tab and the header bar | `"TootGrid"` |
| `favicon` | Path or URL to the favicon file | `"favicon.ico"` |
| `cacheKey` | `localStorage` key used to persist posts between page loads. Change this (e.g. append `_v2`) to bust the cache | `"TootGrid_posts_v1"` |

---

### `layout`

```json
"layout": {
  "mode": "grid"
}
```

| Value | Effect |
|---|---|
| `"grid"` | Responsive multi-column card grid (default) |
| `"list"` | Single centered column — useful on narrow displays or for a feed-style reading experience |

The grid automatically collapses to fewer columns on smaller screens; on mobile it becomes a single column regardless of this setting.

---

### `theme`

```json
"theme": {
  "defaultMode": "auto",
  "lightOverrides": {
    "--accent": "#2563eb",
    "--accent-soft": "rgba(37, 99, 235, 0.12)"
  },
  "darkOverrides": {
    "--accent": "#60a5fa",
    "--accent-soft": "rgba(96, 165, 250, 0.25)"
  }
}
```

| Field | Description |
|---|---|
| `defaultMode` | Starting theme: `"light"`, `"dark"`, or `"auto"` (follows OS preference). The user can always override with the toggle button; their choice is saved to `localStorage` |
| `lightOverrides` | CSS custom property overrides applied when light mode is active |
| `darkOverrides` | CSS custom property overrides applied when dark mode is active |

**Available CSS variables you can override:**

| Variable | Controls |
|---|---|
| `--accent` | Primary accent colour (links, active carousel dots, button focus ring) |
| `--accent-soft` | Translucent version of accent (hover glows, tag backgrounds, chip highlights) |
| `--bg` | Page background |
| `--bg-alt` | Secondary background (used in gradient) |
| `--fg` | Primary text colour |
| `--fg-muted` | Secondary / caption text colour |
| `--card-bg` | Card background colour |

Both `--accent-soft` values automatically propagate to card hover glows, button focus rings, and hashtag chip backgrounds — you only need to change the two variables.

---

### `autoRefresh`

```json
"autoRefresh": {
  "enabled": true,
  "seconds": 300
}
```

| Field | Description | Default |
|---|---|---|
| `enabled` | Enable or disable the automatic refresh timer | `true` |
| `seconds` | Interval between auto-refreshes in seconds. `300` = 5 minutes | `300` |

When enabled, a live `MM:SS` countdown chip appears in the header next to the refresh button. Setting `enabled: false` or `seconds: 0` hides the countdown and disables auto-refresh entirely.

---

### `instances`

Fetch posts from a Mastodon instance's **public timeline**.

```json
"instances": {
  "wisskomm.social": {
    "enabled": true,
    "local": true,
    "remote": false,
    "only_media": false,
    "limit": 10
  }
}
```

Each key is the **instance domain**. You can add as many as you like.

| Field | Description | Default |
|---|---|---|
| `enabled` | Set `false` to skip without deleting the entry | `true` |
| `local` | Only show posts originating from this instance's own users | `false` |
| `remote` | Only show posts federated from other instances | `false` |
| `only_media` | Only show posts that have image or video attachments | `false` |
| `limit` | Maximum number of posts to fetch per refresh | Mastodon server default (usually 20) |

> `local` and `remote` can both be `false` (returns everything) but setting both to `true` simultaneously may return no results depending on the instance.

---

### `accounts`

Fetch posts from a **specific Mastodon user account**.

```json
"accounts": {
  "@SomeUser@mastodon.social": {
    "enabled": false,
    "only_media": false,
    "limit": 5
  }
}
```

Each key is the full Mastodon handle. Both `@user@instance.tld` and `user@instance.tld` formats are accepted.

| Field | Description | Default |
|---|---|---|
| `enabled` | Set `false` to skip without deleting the entry | `true` |
| `only_media` | Only show posts that have image or video attachments | `false` |
| `limit` | Maximum number of posts to fetch per refresh | Mastodon server default |

Account fetching uses the public API — no authentication token is required. Only publicly visible posts are returned.

---

## 🖼️ Logo Images (optional)

TootGrid supports two logo variants in the header:

| File | Used when |
|---|---|
| `TootGridLogo.webp` | Default — wider screens |
| `TootGridLogoSquare.webp` | Narrow screens (≤ 900px) — square format fits the compact topbar |

Replace these with your own branding or remove the `<picture>` element from `index.html` entirely if you prefer text-only.

> ⚠️ **Image license:** The logo images are **not** covered by the MIT license and are © 2026 AlphaPiAlpha, All Rights Reserved. You may replace them with your own images but may not reuse, alter, or redistribute the originals outside of TootGrid. See [LICENSE](LICENSE) for details.

---

## 💾 Caching

Posts are cached in `localStorage` under the configured `cacheKey`. On every page load, cached posts are rendered instantly before the network fetch completes — so the user always sees content immediately, even on slow connections. After each successful fetch the cache is silently updated.

To bust the cache, increment the `cacheKey` value in `config.json` (e.g. `"TootGrid_posts_v2"`).

---

## 🤖 AI Disclosure

This project began as a prototype generated with **Claude Sonnet 4.6** (Anthropic) and was subsequently refined, enhanced, and tailored by hand. AI assistance was used for:

- Generating the initial three-file prototype
- Working through tricky vanilla JavaScript (carousel logic, touch/swipe handling, async fetch orchestration)
- Iterating on layout and CSS details
- Tidying and restructuring code

All design decisions, configuration architecture, feature choices, and final implementation were reviewed and directed by the author. 

---

## ☕ About

Developed as a weekend project — made with love, Linux, and coffee.

---

## 📄 License

**MIT License** — you are free to use, modify, and distribute this project, including commercially.

**Two conditions apply beyond standard MIT:**

> **1. Attribution link** — The visible attribution link in the application UI (`© AlphaPiAlpha · GitHub`) must be retained in both unmodified and modified distributions.

> **2. Image assets** — `TootGridLogo.webp` and `TootGridLogoSquare.webp` are © AlphaPiAlpha, All Rights Reserved, and are **not** covered by the MIT license. They may not be reused, altered, or redistributed outside of TootGrid. You are free to replace them with your own images.

See the [LICENSE](LICENSE) file for the full text.
