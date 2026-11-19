---
name: metastrip
description: Inspect and safely strip metadata from images, audio, video, PDFs, Office/OpenDocument files and ZIP archives — entirely in the browser.
version: 0.2.0
author: Kish Wachira (kish-00), Hermes Agent
license: MIT
platforms: [linux, macos, windows]
metadata:
  hermes:
    tags: [privacy, security, metadata, exif, exiftool, gps, documents, media, safety]
    related_skills: []
---

# MetaStrip Skill

A client-side metadata inspector and safe stripper for common media and document formats. Drop a file into the browser, see exactly which metadata fields it contains, strip only what can be removed safely, and get a field-level report showing what was removed, what was preserved, and what could not be cleaned.

Nothing is uploaded. There is no backend, build step, or install step.

## When to Use

- Check what a photo, audio file, video, PDF, Office document, or ZIP archive reveals before sharing it
- Remove GPS location, author names, camera/device details, software fingerprints, timestamps, comments, thumbnails and similar metadata
- Verify that a cleaned copy really no longer contains the fields you care about
- Produce an honest audit trail: removed vs. intentionally preserved vs. residual/blocked

Don't use for: editing individual metadata values, batch processing folders, transcoding media, or guaranteeing anonymity. Metadata removal is not anonymization: visible content, file names, and statistical fingerprints remain.

## Supported Formats

### Inspect and clean (byte-level or lossless container rewrite)

| Category | Formats | Method |
| --- | --- | --- |
| Images | JPEG, PNG, GIF, WebP, TIFF, BMP, SVG | Surgical byte removal; no canvas re-encode |
| Audio | MP3, WAV, FLAC, OGG/Vorbis/Opus, M4A | Tag removal or comment-header rewrite; audio packets untouched |
| Video | MP4, MOV, M4V, WebM, MKV | In-place atom erase (ISO-BMFF) or copy-only remux (Matroska/WebM) |
| Documents | PDF | PDF object rewrite; content streams untouched |
| Documents | DOCX, XLSX, PPTX | OOXML package rewrite |
| Documents | ODT, ODS, ODP | OpenDocument package rewrite |
| Archives | ZIP | Comment removal and entry timestamp normalization |

### Inspect only (no cleaned copy offered)

- **HEIC/HEIF and AVIF** — parsed and reported, but no safe byte-level remover is implemented
- **Encrypted ZIP / encrypted Office/ODF packages** — cannot be rewritten without the password
- **Digitally signed OOXML/ODF packages** — rewriting would invalidate the signature
- **Encrypted or signed PDFs** — rewriting is refused
- **Unrecognised or unsupported containers** — reported as unsupported rather than presented as clean

## What Is Removed vs. Preserved

**Removed by default**

- EXIF (camera, lens, serial numbers, GPS, timestamps, artist, copyright, descriptions), XMP, IPTC/Photoshop APP13, JPEG comments, JFIF/JFXX thumbnails, trailing appended data
- PNG text chunks (`tEXt`, `iTXt`, `zTXt`), `eXIf`, `tIME`, C2PA chunks
- WebP `EXIF`, `XMP ` and C2PA chunks
- GIF comments, plain-text extensions, non-animation application extensions, trailing data
- TIFF descriptive tags (description, make, model, software, artist, datetime, copyright, GPS/EXIF sub-IFDs)
- SVG comments, `<metadata>`, `<title>`, `<desc>`, `<script>`, Inkscape/Sodipodi/Illustrator/Adobe editor attributes
- MP3 ID3v2 (including multiple tags and footers), ID3v1 and APEv2
- WAV `LIST/INFO`, `id3 `, `bext`, `iXML`, `guano`, C2PA chunks
- FLAC non-STREAMINFO metadata blocks (Vorbis comments, pictures, padding, etc.)
- OGG/Opus Vorbis comment headers (packet boundaries and page CRC preserved)
- ISO-BMFF metadata atoms (`udta`, `meta`, `ilst`, `©*` tags, `XMP `, `uuid` metadata) and header timestamps
- Matroska/WebM descriptive tags via a copy-only remux
- PDF Info dictionary, catalog XMP/PieceInfo, annotation author/date fields, unreferenced objects
- OOXML/ODF core/app/custom metadata text, authorship and revision IDs, embedded thumbnails and their relationships/manifest entries, archive comments, and entry timestamps

**Intentionally preserved (and disclosed in the report)**

- Image orientation, when removing EXIF would otherwise rotate the picture
- ICC colour profiles needed for correct colour rendering
- Codec/stream parameters, duration, dimensions, tracks, and all media sample data
- Visible document content, including tracked changes and comment text
- PDF page content and structure

## How to Run

Open `index.html` in a modern browser, or visit the hosted copy at https://kish-00.github.io/metastrip/

1. Drag & drop one file (or use the file picker)
2. Review the metadata table; GPS coordinates include an OpenStreetMap link
3. Click **Strip metadata safely**
4. Read the report: **Removed**, **Kept**, **Changed**, **Added**, and any residual/blocked warnings
5. Only after reviewing the report, click **Download cleaned file**

Cleaning never auto-downloads.

## Quick Reference

- `index.html` — complete app (HTML/CSS/JS in one file)
- CDN ES modules: `exifreader@4.54.0`, `jszip@3.10.2`, `pdf-lib@1.17.1`, `mediabunny@1.66.0`
- Format detection is by magic bytes, not file extension or MIME type
- Cleaned output is re-inspected and diffed before it can be downloaded
- `metastrip-skill/` — packaged copy of this skill; `metastrip-skill.zip` — distributable archive

## Procedure

1. Open `index.html` in your browser
2. Drop a supported file into the upload zone
3. Read the metadata table and any format-specific notes
4. Click **Strip metadata safely**
5. Confirm the report shows the expected fields as Removed and that intentionally kept technical fields are acceptable
6. Download the cleaned copy only if the report has no unexplained residual warnings

## Pitfalls

- **Cleaning is not anonymization.** Visible content, filenames, and content fingerprints remain.
- **Preserved orientation and ICC profiles are deliberate.** They are reported as Kept; removing them can rotate or discolour the image.
- **ISO-BMFF in-place erase keeps file size roughly constant.** Removed atoms are replaced with empty `free` boxes so media offsets do not move. The report labels this method.
- **Matroska/WebM cleaning is a copy-only remux.** Packets are copied without transcoding, but the container bytes are rewritten.
- **Office/ODF cleaning preserves visible tracked changes and comments**, but clears their author names, initials, dates and revision IDs. Reviewing the content itself is still your responsibility.
- **Digitally signed or encrypted packages are inspect-only.** Rewriting would break signatures or is impossible without credentials.
- **HEIC/AVIF are inspect-only.** No safe remover is implemented, so the tool does not offer a misleading "clean" copy.
- **Unusual container variants may be refused.** If a safe rewrite cannot be proven, the file is marked inspect-only rather than silently passed through.
- **CDN access is required for the parsers** unless the libraries are vendored locally.
- **Large files are bounded** by browser memory; very large media may fail to load.

## Verification

- Open `index.html`; the page should load with no console errors and the drop zone visible
- Drop a JPEG with GPS EXIF; confirm the metadata table shows GPS rows and an OpenStreetMap link
- Strip it; confirm the report lists the GPS/EXIF fields as Removed, shows no residual warning, and offers a download
- Re-open the downloaded file in MetaStrip; confirm the removed fields are gone and the image still renders
- Repeat for one audio file, one video, one PDF, one DOCX/ODT, and one ZIP
- Confirm a HEIC/AVIF or encrypted ZIP is reported as inspect-only with a note, not as successfully cleaned
