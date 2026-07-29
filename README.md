# AETHER FFU Locator

A single-page web tool that decodes the 9-byte GNSS telemetry packet sent by the AetherSpace REXUS Free Falling Unit (FFU) and plots the resulting fix on a map.

**Live site: <https://woutlyen.github.io/AETHER_FFU_Locator_Website/>**

Paste or type the nine hex bytes received from the downlink, hit decode, and the tool shows latitude, longitude and altitude, and flies the map to the FFU's position — useful for recovery in the field at Esrange.

## Features

- **Hex byte entry** — nine two-digit input boxes with auto-advance, backspace-to-previous, arrow-key navigation, and <kbd>Enter</kbd> to decode.
- **Smart paste** — pasting a hex string (with or without `0x` prefixes, spaces or separators) spreads the bytes across the boxes starting at the focused one.
- **Clipboard auto-detect** — press <kbd>Esc</kbd> and, if the clipboard holds exactly 9 bytes of hex, a toast offers to fill in and decode it.
- **Position readout** — latitude and longitude to 6 decimals, altitude AMSL in metres.
- **Map** — Leaflet map centred on Esrange Space Center (Kiruna, Sweden) that flies to the decoded fix and drops a marker with a popup readout.
- **Three map styles** — light and dark OpenStreetMap (dark is the light tiles with a CSS invert/hue-rotate filter) and Esri World Imagery satellite.
- **Packet structure view** — a colour-coded breakdown of the nine bytes that updates with the decoded values.

## Packet format

9 bytes, big-endian:

| Byte | Field | Size | Encoding |
|------|-------|------|----------|
| 0 | Header | 8-bit | Fixed `0x09`; anything else is rejected |
| 1–3 | Latitude | 24-bit | `lat = raw / 0xFFFFFF * 180 − 90` |
| 4–6 | Longitude | 24-bit | `lon = raw / 0xFFFFFF * 360 − 180` |
| 7–8 | Altitude | 16-bit | `alt = raw × 1.5` metres AMSL |

The 24-bit fields give roughly 1.2 m of latitude resolution and about 0.9 m of longitude resolution at Esrange's latitude. Altitude covers 0–98 302.5 m in 1.5 m steps.

### Example

Packet `09 E0 8F 1F 8F 02 64 D0 55` decodes to:

```
Latitude   67.893103°
Longitude  21.106894°
Altitude   79999.5 m
```

## Running locally

Everything lives in a single [index.html](index.html) — no build step, no dependencies to install. Open the file directly in a browser, or serve the folder:

```sh
python3 -m http.server 8000
# then open http://localhost:8000
```

Note that clipboard auto-detect needs a secure context, so it works on the live site and on `localhost` but not from a `file://` URL.

Leaflet 1.9.4 and the web fonts are loaded from CDNs, so the map and typography need an internet connection.

## Deployment

The site is served by GitHub Pages from the default branch — pushing to `main` publishes it.
