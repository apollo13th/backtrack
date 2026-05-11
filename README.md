# Backtrack — Podcast Player

Single-file iPhone Safari web app. Dark theme (black + orange). Hosted on Netlify.

**Source file:** `index.html` (everything inline — HTML, CSS, JS, no build step)

---

## Deploy

```bash
cp ~/Library/Mobile\ Documents/com~apple~CloudDocs/App\ Builds/Backtrack/index.html \
   ~/Library/Mobile\ Documents/com~apple~CloudDocs/App\ Builds/Backtrack/backtrack.html \
&& cd ~/Library/Mobile\ Documents/com~apple~CloudDocs/App\ Builds \
&& netlify deploy --prod --dir=Backtrack
```

---

## Features

- **Podcast search** via iTunes Search API
- **Subscribe / library** — persists to localStorage
- **Episode browser** — RSS feed fetched through 3-proxy fallback (allorigins → corsproxy.io → codetabs)
- **Continuous play queue** — add episodes, reorder, delete
- **Persistent playback position** — remembers where you left off per episode
- **Waypoints** — tap ⚑ in the player to bookmark a moment; tap to jump back
- **Playback speed** — 0.75×, 1×, 1.25×, 1.5×, 2×
- **Drag to dismiss** — swipe down on the topbar drag handle to close the full player
- **Mini player** — always visible when something is queued; tap body to expand
- **Lock screen / Control Center** — shows artwork, title, skip ±30s, prev/next track
- **Safe area handling** — home indicator clearance on iPhone notch models

---

## Architecture

| Key | Value |
|-----|-------|
| Storage key | `backtrack_v1` in localStorage |
| State object | `S` — `{ subs, lib, queue, qi, pos, positions, speed, waypoints }` |
| Audio | `<audio id="audio-el">` — HTML5, no Web Audio API |
| Full player | `position:fixed; transform:translateY(100%)` hidden, `.on` shows it |
| CORS proxies | allorigins.win → corsproxy.io → codetabs.com, 8s timeout each |

---

## iOS Safari quirks resolved

- **Script crash (root cause of all button failures):** `fp-topbar` was a class not an id — `getElementById('fp-topbar')` returned null, crashing the script before any event listeners registered. Fixed by adding `id="fp-topbar"`.
- **Buttons invisible at bottom:** `overflow:hidden` on the fixed player clipped content. Fixed with `#fp-inner { flex:1 1 0; min-height:0; overflow-y:auto }`.
- **Tab bar home indicator overlap:** `padding-bottom: max(env(safe-area-inset-bottom,0px), 22px)`.
- **Swipe on artwork scrubbed audio:** Progress bar touch zone was too large. Reduced padding; `touch-action:none` on artwork.
- **Accidental pinch zoom:** `maximum-scale=1.0, user-scalable=no` in viewport meta.
- **Mini player click not firing:** Removed touchstart/touchend handlers that were eating click events; single `click` listener only.

---

## Known iOS limitations (not fixable for web apps)

- **Lock screen play button unreliable** — iOS blocks `audio.play()` from the Media Session JS handler as it doesn't count as a user gesture. Pause/skip/artwork all work. This requires AVAudioSession (native app only).
- **Background audio** — works while screen is on; may cut on screen lock depending on iOS version and whether the app is added to the home screen.

---

## Recommended next steps

- [ ] Swipe-to-dismiss working from artwork area (not just topbar) — carefully guard against progress scrub conflict
- [ ] Episode descriptions in the player
- [ ] Pull-to-refresh episodes in library
- [ ] Unsubscribe cleans up lib cache
- [ ] Better empty states and loading skeletons

---

## Adding to iPhone home screen

Safari → Share button → "Add to Home Screen" — this gives better audio session handling than running in browser.
