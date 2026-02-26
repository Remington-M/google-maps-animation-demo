# Changelog

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
