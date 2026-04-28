# Mochahada TV

A native Android IPTV player built from scratch in Kotlin. Mochahada TV connects to Xtream-compatible IPTV panels and provides a smooth, TV-friendly experience for live channels, movies, and series — on both phones and Android TV, from a single APK.

## Features

### Login & Smart Host Resolution
- Enter username and password to authenticate against an Xtream panel
- All available hosts are probed simultaneously with a 5-second timeout per host
- The fastest valid host is cached — no re-probing on subsequent launches
- Auto-login for returning users with cached credentials
- If the cached host goes down, the app automatically re-probes before falling back to the login screen

### Home Screen
- TV-style 4-tile launcher: **Live TV**, **Movies**, **Series**, **Settings**
- **Continue Watching** row at the top — shows your 20 most recent plays across all content types, resuming from where you left off
- D-pad friendly with focus-based gold highlight for Android TV navigation

### Live TV
- Three-pane layout: categories on the left, channels in the middle, inline preview on the right
- First tap on a channel loads an inline preview so you can keep browsing
- Second tap goes fullscreen
- Channel logos loaded via Glide with smooth scrolling

### Movies (VOD)
- Browse by category with a 4-column poster grid (2:3 aspect ratio)
- Detail page with backdrop, plot, year, rating, genre, and duration
- One-tap playback with automatic resume from where you stopped

### Series
- Category browsing with the same poster grid as movies
- Landscape detail page with hero cover, metadata, and horizontal season selector chips
- 4-column episode grid with thumbnails
- Tap any episode to play — supports resume and watched markers

### Player
- Built on Media3 / ExoPlayer with custom controls themed in obsidian, champagne, and grey
- **Content-aware controls:**
  - **Live** — no seek bar; prev/next buttons navigate the channel queue with position indicator
  - **VOD** — time-based seek bar; prev/next buttons skip +-10 seconds
- Double-tap left/right half of screen to seek +-10s (VOD)
- Volume swipe gesture on right half (phone only) with percentage pill
- Aspect ratio cycle: Fit, Zoom, Fill
- Audio and subtitle track selection dialogs (auto-hidden when the stream has no selectable tracks)
- Picture-in-Picture on Android 8+ phones
- Auto-hide overlay after 3 seconds of inactivity

### Stream Resilience
- Custom ExoPlayer buffer config tuned for weak connections (15s min / 60s max buffer)
- OkHttp retry interceptor: 3 attempts with linear backoff on server errors
- Automatic reconnect on stream drops — up to 3 attempts at 1s / 2s / 4s intervals with a "Reconnecting..." banner
- WiFi-to-mobile handover detection triggers an immediate reconnect instead of waiting for the next backoff tick
- Retry counter resets on successful playback so the app always has a fresh budget for the next flake

### Search & Favorites
- In-section search bar with 300ms debounce — filters within the selected category or across all content via the **ALL** pseudo-category
- **FAVORITES** pseudo-category in every section — heart-toggle on movie details, series details, and live preview
- Favorites persist across app restarts (Room database)
- Resume positions saved every 5 seconds during playback
- **WATCHED** badge on episodes after 20% playthrough
- Movies and episodes auto-resume from the saved position on re-open

### Android TV Support
- Same APK works on phone and Android TV
- Leanback launcher integration with TV banner
- Full D-pad and remote control navigation across all screens
- Focus-state drawables with champagne highlight borders
- Media key support: play/pause, next/previous, fast-forward, rewind, stop
- MediaSession integration for system remote and headset controls

### Settings & Parental Controls
- **Parental PIN:** set, change, or remove a 4-digit PIN
- **Adult category lock:** when enabled (requires PIN), filters out adult/xxx/18+ categories from live, movies, and series
- **Auto-play next episode** toggle
- **Cache management:** view cache size, clear image cache, clear watch history
- Playlist expiry date display with refresh button
- Logout clears all local data (database, image cache, preferences)

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Language | Kotlin |
| UI | XML layouts (landscape-locked, phone + TV compatible) |
| Architecture | MVVM + LiveData + Coroutines |
| Video Player | Media3 / ExoPlayer |
| Networking | OkHttp (hand-rolled Xtream client) + Retrofit (panel API) |
| JSON | Moshi |
| Image Loading | Glide |
| Local Database | Room (KSP) |
| DI | Manual service locator (AppContainer) |
| Min SDK | 24 (Android 7.0) |

## Design

The app uses a **Royal Midnight** color palette:

| Name | Role |
|------|------|
| Obsidian | Primary background |
| Midnight | Card surfaces |
| Velvet | Focused/selected states |
| Champagne | Accent color (buttons, highlights, badges) |
| Ivory | Primary text |
| Muted | Secondary text |

All activities are locked to landscape orientation. The UI is designed to work identically on phone and TV — no separate TV-specific layouts.
