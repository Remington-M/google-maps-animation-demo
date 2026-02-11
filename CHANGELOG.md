# Changelog

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
