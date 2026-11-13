---
name: metastrip
description: View and strip EXIF metadata from photos client-side.
version: 0.1.0
author: Kish Wachira (kish-00), Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [privacy, security, exif, metadata, safety]
    related_skills: []
---

# MetaStrip Skill

A client-side EXIF metadata viewer and stripper for photos. Drop an image into the browser, see what metadata it's secretly sharing (GPS location, camera model, timestamps, device info), and download a clean copy with all EXIF removed.

## When to Use

- You want to check what EXIF metadata a photo contains before sharing it online
- You need to strip GPS location, camera serial numbers, or timestamps from an image
- You want a privacy-first tool that never uploads your photo to a server

Don't use for: batch processing many images at once, or editing metadata fields (this tool strips all EXIF, it doesn't edit individual fields).

## Prerequisites

- A modern web browser with FileReader API and canvas support
- No server, no API keys, no installation required

## How to Run

Open `index.html` in a browser, or visit the hosted version at https://kish-00.github.io/metastrip/

1. Drag & drop a photo (JPEG, PNG, WebP) into the drop zone
2. Review the EXIF metadata table — GPS coordinates include an OpenStreetMap link
3. Click "Strip metadata & download" to get a clean copy

## Quick Reference

- `index.html` — the complete skill (HTML/CSS/JS in one file)
- Uses `exif-js` (from unpkg CDN) for EXIF parsing
- Strips metadata via HTML5 canvas re-encoding

## Procedure

1. Open `index.html` in your browser
2. Drop an image file into the upload zone
3. Read the metadata table to see what's exposed
4. Click "Strip metadata & download" — the image is re-encoded locally through a canvas, producing a clean EXIF-free copy
5. Nothing is uploaded to any server

## Pitfalls

- Canvas re-encoding slightly recompresses JPEGs; PNGs stay lossless
- GPS data is converted from degrees/minutes/seconds to decimal degrees for the map link
- Some social platforms strip metadata automatically; this tool gives you control before you share

## Verification

- Open `index.html` and confirm the page loads without errors
- Drop a JPEG with known GPS EXIF — confirm the GPS row appears with a map link
- Click "Strip" — confirm the downloaded file has no EXIF when re-opened in MetaStrip
