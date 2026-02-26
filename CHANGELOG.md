# Changelog

## Session: 2026-02-25 (Part 3) — UI Polish & Deployment

### Overview
Polished the Trips UI to closely match the Figma design, decoupled the side panel from the map animation, added an animation mode toggle, and deployed to Kyber.

### What Changed

#### UI Cleanup (Figma-accurate)
- **Trip card styling**: Matched Figma specs — 24px border-radius, 10px padding, 96x96 thumbnails with 16px radius, `0 8px 24px rgba(0,0,0,0.1)` drop shadow, 18px semibold city name, 12px #6c6c6c dates
- **Photo avatars**: Replaced colored-circle initials with actual avatar images extracted from Figma
- **Local images**: Downloaded all 9 trip thumbnails, 5 avatar photos, the Airbnb logo, and "Your stay" pin from Figma — no more Unsplash dependencies
- **Airbnb logo**: Replaced broken inline SVG with the actual wordmark from Figma (`images/airbnb-logo.svg`)
- **"Past trips" label**: Changed from 12px uppercase to 16px semibold to match Figma
- **Filter bar removed**: No longer shows category bar when viewing trip details

#### Layout Changes
- **Fixed left panel width**: 533px (matching Figma's 0.36:0.64 split at 1440px)
- **Centered layout**: Max-width 2000px, centered horizontally
- **Map border-radius**: 32px with 32px/48px padding around the map
- **Left panel padding**: Increased to 48px horizontal to match Figma insets
- **Browser chrome removed**: App now takes full viewport height
- **Scrollbar**: Moved to right edge of page (on `.content`), then hidden entirely
- **White background**: Clean white instead of gray

#### Decoupled Panel & Map
- Side panel transitions immediately on trip select/back (200ms crossfade) instead of waiting for the full map animation to complete
- Map animates independently — much more responsive feel

#### "Your stay" Destination Pin
- Added Airbnb "Your stay" pin (dark circle with house icon + label) from Figma node `182:94360`
- Pin appears centered on destination coordinates when a trip is selected
- Removed on navigate back

#### Animation Mode Toggle
- **Match Cut / Default** segmented control in bottom-right of map
- Clean pill-shaped toggle, always visible (not in dev panel)
- Toggles the match cut on/off for easy A/B comparison

#### Dev Panel Restyled
- White background with Rausch (#FF385C) accent color
- Slider thumbs, progress bar, phase indicator, checkbox all use Rausch
- DEV toggle button uses #222
- All phase states use consistent Rausch color

#### Match Cut Flash Removed
- Removed the black scrim overlay that flashed during the cut frame

#### Updated Defaults
- Start zoom levels: 1 (was 1.5)
- End zoom levels: 1.5 (was 2.0)
- Spring stiffness: 20 (was 120)

### Kyber Deployment
- Created Kyber prototype: `trips-map-camera-animation`
- Slug: `trips-map-camera-animation`
- Wrapped vanilla HTML in iframe via `App.tsx` (Kyber requires React entry point)
- Static assets (images, config, app.html) served from `public/`
- API key restricted to `prototypes.sandcastle.musta.ch` via Google Cloud Console
- **Production URL**: `https://prototypes.sandcastle.musta.ch/trips-map-camera-animation/`
- **Dev URL**: `https://prototypes.sandcastle.musta.ch/trips-map-camera-animation/?dev`

### Files
```
~/trips-map-camera-animation/          # Kyber project
├── public/
│   ├── app.html                       # Main prototype (vanilla HTML/CSS/JS)
│   ├── config.local.js                # Google Maps API key + Map ID
│   └── images/                        # All images from Figma
│       ├── airbnb-logo.svg
│       ├── stay-pin.svg
│       ├── paris.png, san-francisco.png, rio.png, ...
│       └── avatar-1.png through avatar-5.png
├── src/
│   ├── App.tsx                        # Iframe wrapper
│   └── main.tsx                       # Minimal React entry
├── manifest.json
└── vite.config.ts

~/Coding/Prototypes/Google Maps Animations/   # Original project
├── index.html                         # Source of truth
├── images/                            # Local copies of Figma assets
├── config.local.js
└── CHANGELOG.md
```

### Figma References
- Trips Tab: `182:48977` in `Motion-Work---2026`
- Trips Detail Page: `182:49846` in `Motion-Work---2026`
- "Your stay" Pin: `182:94360` in `Motion-Work---2026`
- Airbnb Logo: `182:49794` in `Motion-Work---2026`

### Next Steps / Ideas
- [ ] Add hover states that highlight corresponding map pin
- [ ] Tune animation per route distance (short vs long haul)
- [ ] Animate left panel content transition (slide in/out instead of crossfade)
- [ ] Test with vector Map ID for smoother tile rendering
- [ ] Add selected trip highlight on map (larger pin or glow)

---

## Session: 2026-02-25 (Part 2) — Trips UI

### Overview
Applied the animation prototype to the actual Airbnb Trips design from Figma. Rebuilt `index.html` from a dev tool into a near-final Trips experience with the map animations driving the interaction.

### What Changed

#### Full Trips UI (matching Figma `Motion-Work---2026`)
- **Split-panel layout**: 420px left sidebar + Google Maps filling the right
- **Browser chrome mockup** + Airbnb nav bar with logo, "Switch to hosting", avatar, hamburger menu
- **Trips list view**: "Trips" title with count, trip cards (thumbnail, city name, date range, guest avatars)
- **Trip detail view**: back button, "Your stay" card (image, dates, host, guests, Directions), day-by-day itinerary timeline
- **Category filter bar** at bottom of map in detail view (Experiences, Restaurants, Shopping, Attractions, Transit)

#### Trip Data
- **5 upcoming**: Paris (June 5–13), San Francisco (Nov 12–16), Rio de Janeiro (Dec 27–Jan 3), Los Angeles (Apr 17), Colorado (Nov 22–29)
- **4 past**: Tokyo (2025), Charleston (2024), Austin (2024), Queenstown (2023)
- Each trip has coordinates, guest avatars, stay info, and itinerary items with photos

#### Map Pins (AdvancedMarkerElement)
- Custom HTML markers on the world map showing trip thumbnail images + city name/date labels
- Past trips use muted, smaller pin style
- Clicking a pin triggers the animation (same as clicking the card)
- Pins hide during animation and reappear on return to overview

#### Animation Integration
- **Trip selection** → van Wijk/Nuij animation from US overview (zoom 3.5) to trip coords (zoom 18.5)
- **Back button** → reverse animation from zoom 18.5 back to zoom 3.5 centered on US
- Match cut with spring physics drives both forward and back transitions
- Subtle black flash overlay on cut frame

#### Collapsible Dev Panel
- **Hidden by default** — toggled via "DEV" pill button in bottom-right corner
- Slides in from the right (380px dark panel)
- Contains all animation controls: algorithm, curve (ρ), easing, start/end zoom, match cut toggle, start/end zoom levels, spring stiffness
- Live telemetry: lat/lng/zoom, progress %, phase indicator, FPS counter, progress bar
- All parameters are read from the dev panel at animation time, so tuning is instant

### Branch
- `feature/trips-ui` — created from `main`

### Figma References
- Trips Tab: `182:48977` in `Motion-Work---2026`
- Trips Detail Page: `182:49846` in `Motion-Work---2026`

### Setup Notes
- Configured Figma Desktop MCP server (HTTP `127.0.0.1:3845/mcp`) for this project
- Used `get_screenshot` and `get_design_context` to extract layout/structure from Figma

### Next Steps / Ideas
- [ ] Tune animation defaults (stiffness, zoom levels) for each route distance
- [ ] Add hover states that highlight corresponding map pin
- [ ] Smooth view transition (crossfade) between trips list and detail panels
- [ ] Test with vector Map ID for smoother tile rendering during animation
- [ ] Add selected trip highlight on map (larger pin or glow)
- [ ] Consider animating the left panel content transition (slide in/out)

---

## Session: 2026-02-25 (Part 1)

### Overview
Overhauled the match cut animation to feel more like a real film cut, added spring tuning controls, and updated defaults for long-distance routes.

### Changes

#### Match Cut: Coupled Position and Zoom
- Previously, position interpolated smoothly while zoom had the hard cut — this caused the pin to appear off-center at the cut moment
- Now position and zoom use the same `t` value, so the outgoing shot stays centered near the origin and the incoming shot is centered near the destination
- The matching zoom levels on both sides mask the position jump, just like a real film match cut

#### Spring Stiffness Control
- Added a **Spring Stiffness** slider (10–400) in the Match Cut controls
- Damping ratio is fixed at 1 (critically damped): `damping = 2 * sqrt(stiffness)`
- Value display shows stiffness and approximate duration (e.g. "30 (~2300ms)")
- Spring is now created dynamically per animation instead of using a hardcoded default

#### Updated Defaults
- **Spring stiffness**: 100 → 30 (slower, smoother ~2300ms animation)
- **Long-distance presets** (SF→NYC, SF→Tokyo, Tokyo→Sydney):
  - Start zoom: 3.5 (was 11–12)
  - End zoom: 18.5 (was 11–12)
- **Match cut levels**: Start 1 / End 1.5 (was 3/3)

### GitHub
- Added GHE remote: `git.musta.ch:remington-mcelhaney/MapCameraAnimations.git`
- Pushed to both GitHub.com and GHE

---

## Session: 2026-02-11

### Overview
Built a Google Maps animation demo to experiment with camera transitions, specifically to understand and test the van Wijk/Nuij algorithm used in Airbnb's `useFlyover` implementation.

### What We Built

#### Core Demo (`index.html`)
- Single-file HTML/CSS/JS demo for Google Maps camera animations
- Implements the **van Wijk/Nuij algorithm** ("Smooth and efficient zooming and panning")
- Based on `@math.gl/web-mercator`'s `flyToViewport` function
- Matches Airbnb's `useFlyover.ts` implementation

#### Key Features
1. **Algorithm Selection**
   - van Wijk/Nuij (production algorithm)
   - Simple linear interpolation (for comparison)

2. **Configurable Parameters**
   - **Curve (ρ)**: Controls zoom-out intensity (default: 0.85)
   - **Duration**: Auto-calculated with compression (`if > 2000ms: 2000 + duration/10`)
   - **Easing**: Multiple options, default `easeInOutQuad` (matches production)
   - **Start/End Zoom**: Adjustable zoom levels

3. **Zoom Skip Feature** (NEW)
   - Limits how far camera zooms out during long flights
   - Prevents disorienting extreme zoom-outs
   - Shows "CLAMPED" indicator when active

4. **Preset Locations**
   - Florence (short ~5km)
   - London → Paris (~350km)
   - SF → NYC (~4000km)
   - SF → Tokyo (cross-Pacific, tests longitude wrapping)
   - Tokyo → Sydney (~7800km)
   - Zoom Only (same location, different zoom)
   - Custom coordinates

5. **Live Value Display**
   - Progress, eased progress
   - Lat/Lng/Zoom in real-time
   - FPS counter
   - Clamped indicator for zoom skip

6. **Vector Map Support**
   - Optional Map ID field for vector maps
   - Vector maps handle animations much smoother than raster tiles

#### Local Config (`config.local.js`)
- Stores API key and Map ID locally
- Gitignored to keep secrets out of repo
- Auto-loads on page open

### Bug Fixes
- Fixed van Wijk/Nuij algorithm overshoot issue (zoom was going past destination)
- Corrected `u(s)` position calculation formula from the paper

### Key Insight: Google Maps API Limitations
The native Google Maps API has **no built-in animation controls**:
- `panTo()` only animates for short distances
- `setCenter()`, `setZoom()`, `moveCamera()` are instant
- All animation requires custom `requestAnimationFrame` loop

### Files
```
~/Coding/Google Maps Animations/
├── index.html        # Main demo
├── config.local.js   # Local API key (gitignored)
├── .gitignore        # Excludes config.local.js
├── README.md         # Documentation
└── CHANGELOG.md      # This file
```

### GitHub Repo
https://github.com/Remington-M/google-maps-animation-demo

### Pending Git Commits
Run these commands to commit latest changes:
```bash
cd ~/Coding/Google\ Maps\ Animations
git add -A
git commit -m "Add zoom skip feature, local config support, changelog

- Zoom skip: limits how far camera zooms out during long flights
- Local config: loads API key from gitignored config.local.js
- Changelog: documents session work

Co-Authored-By: Claude Opus 4.5 <noreply@anthropic.com>"
git push
```

### Next Steps / Ideas
- [ ] Test with actual Map ID for vector maps
- [ ] Compare animation feel with production Airbnb implementation
- [ ] Experiment with different curve values for different distances
- [ ] Consider adding a "pause at midpoint" option
- [ ] Add visual path preview showing the camera trajectory
- [ ] Export animation parameters as code snippet

### Reference
- van Wijk/Nuij Paper: https://www.win.tue.nl/~vanwijk/zoompan.pdf
- Your implementation: `frontend/map/utils/useFlyover.ts`
- @math.gl/web-mercator: https://github.com/visgl/math.gl
