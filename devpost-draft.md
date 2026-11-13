# MetaStrip

**One-line pitch:** A client-side EXIF metadata viewer and stripper that lets you see what your photos are secretly sharing (GPS location, camera model, timestamps, device info) and download a clean copy — all in your browser, nothing uploaded.

## The problem

Every photo you take carries invisible EXIF metadata: GPS coordinates pinpointing exactly where it was shot, camera serial numbers, timestamps, software versions, and sometimes even your name. Posting a photo online without stripping it can accidentally doxx you, reveal your home address, or expose when and where you were. Most users don't know this metadata exists, and existing tools often require uploading your photo to a third-party server — defeating the purpose.

## The solution

MetaStrip is a privacy-first, zero-upload tool:

1. **Drop an image** (JPEG, PNG, WebP) into the browser.
2. **See what's inside** — EXIF fields rendered in a table, with GPS coordinates decoded to lat/long and linked to OpenStreetMap.
3. **Strip & download** — the image is re-encoded through a canvas in your browser, producing a clean copy with all EXIF removed. Nothing ever leaves your device.

## Built with

- Vanilla HTML/CSS/JS — no build step, no framework
- [exif-js](https://github.com/exif-js/exif-js) for EXIF parsing
- GitHub Pages for static hosting

## Privacy by design

Because all processing happens client-side via the FileReader API and `<canvas>`, your photos never touch a server. There's no backend to log, leak, or sell your images.

## Challenges

- EXIF GPS data is stored as degrees/minutes/seconds with N/S/E/W refs — had to convert to decimal degrees for the map link.
- Canvas re-encoding drops EXIF but also slightly recompresses JPEGs (configurable quality); PNGs stay lossless.
- Browser origin restrictions mean the tool must be served over HTTPS (GitHub Pages provides this).

## Lessons learned

Most "metadata scrubber" tools online are web forms that upload your image. Building one that runs entirely in the browser was straightforward but required care around canvas re-encoding and EXIF parsing edge cases. For a hackathon project, keeping it to a single HTML file made it easy to demo and deploy in under an hour.

## Live demo

https://kish-00.github.io/metastrip/

## Repository

https://github.com/kish-00/metastrip

## Submission

SafeSpace Hacks — Cybersecurity / Social Good / Beginner Friendly
