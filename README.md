# Google Maps Animation Demo

A web demo for experimenting with Google Maps camera transitions, implementing the van Wijk/Nuij "Smooth and efficient zooming and panning" algorithm.

## Features

- **van Wijk/Nuij algorithm** - Calculates optimal camera path through 3D space (same as `@math.gl/web-mercator`)
- **Adjustable parameters** - Curve (ρ), duration, easing functions, zoom levels
- **Auto-duration calculation** - Matches production implementation with compression for long flights
- **Preset locations** - Short (Florence ~5km) to long (SF→Tokyo cross-Pacific) distances
- **Live value display** - See lat/lng/zoom updating in real-time during animation
- **Vector map support** - Add a Map ID for smooth WebGL-based rendering

## Usage

1. Open `index.html` in a browser
2. Enter your Google Maps API key
3. (Optional) Add a Map ID for vector maps - recommended for smooth animations
4. Select a preset or enter custom coordinates
5. Adjust parameters and click "Animate"

## Algorithm

This implements the algorithm from:

> van Wijk, J.J. and Nuij, W.A.A. (2003). "Smooth and efficient zooming and panning"
> https://www.win.tue.nl/~vanwijk/zoompan.pdf

Key parameters:
- **Curve (ρ)** - Controls zoom-out amount during flight. Default: 0.85
  - Lower values = more dramatic zoom out
  - Higher values = flatter, more direct path
- **Duration** - Auto-calculated from path length, with compression: `if (duration > 2000) duration = 2000 + duration/10`
- **Easing** - Default: easeInOutQuad

## Vector Maps

For smooth animations, use vector maps by providing a Map ID:
1. Go to Google Cloud Console → Google Maps Platform → Map Management
2. Create a map with "Vector" rendering type
3. Copy the Map ID into the demo

Without vector maps, raster tiles can't keep up with rapid viewport changes during animation.

## License

MIT
