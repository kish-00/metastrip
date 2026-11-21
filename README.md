# MetaStrip

> ### 🚀 [**Try it live — https://kish-00.github.io/metastrip/**](https://kish-00.github.io/metastrip/)

**A client-side metadata inspector and safe stripper** for images, audio, video, PDFs, Office documents and ZIP archives. See exactly what a file reveals, remove what can be removed safely, and download an honest audit report of what changed.

Your files **never leave your browser**. There is no server, no upload, and no build step.

---

## Why MetaStrip

Almost every file you share carries invisible metadata:

- **Photos** embed GPS coordinates, camera serial numbers, timestamps and sometimes your name.
- **Audio and video** carry titles, artists, creation dates and software fingerprints.
- **PDFs and Office documents** store authors, editors, revision history, comments and embedded thumbnails.
- **Archives** leak usernames, internal paths and timestamps.

Most people don't know this metadata exists. Worse, many "metadata remover" websites require uploading the file to a third-party server — which is exactly the wrong trust model for a privacy tool.

MetaStrip runs **entirely in your browser** via FileReader/Blob APIs. The only network requests are for the open-source parser libraries, loaded from pinned CDN versions.

## How it works

1. **Drop a file** — format is detected from magic bytes, not the file extension.
2. **See what's inside** — metadata is shown field by field; GPS coordinates are decoded and linked to OpenStreetMap.
3. **Strip safely** — the tool uses format-aware byte surgery or lossless container rewrites instead of blindly re-encoding. Images keep their pixels; audio and video packets are not transcoded; PDF page content and document text are preserved.
4. **Read the report** — every field is labelled **Removed**, **Kept**, **Changed**, or **Added**. Intentionally preserved technical metadata (such as image orientation and ICC colour profiles) is disclosed. If a safe rewrite cannot be proven, the file is marked *inspect-only* instead of being presented as clean.
5. **Download deliberately** — cleaning never auto-downloads. You review the report first, and the cleaned output is re-inspected and diffed against the original before the download button is enabled.

## Why it's different

- **Zero upload, zero backend.** Processing happens in your browser. There is no server to log, leak, or sell your files.
- **No fake "clean".** HEIC/AVIF, encrypted archives, and digitally signed documents are reported as inspect-only — because no safe remover is implemented, or rewriting would break the signature.
- **Before/after verification.** Cleaned output is re-inspected and diffed against the original before it can be downloaded.
- **Field-level honesty.** The report distinguishes metadata that was *removed* from technical metadata that was *deliberately kept*, and flags anything residual.
- **Content-preserving methods.** JPEG/PNG/WebP/GIF cleaning is surgical byte removal, not canvas re-encoding. MP4/MOV metadata is erased in place so media offsets do not move. WebM/MKV use a copy-only remux. FLAC keeps STREAMINFO and audio frames. OGG preserves packet boundaries and recalculates page CRCs.

## Supported formats

### Inspect and clean

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

## What gets removed vs. preserved

**Removed by default:**

- EXIF (camera, lens, serial numbers, GPS, timestamps, artist, copyright, descriptions), XMP, IPTC/Photoshop APP13, JPEG comments, JFIF/JFXX thumbnails, trailing appended data
- PNG text chunks (`tEXt`, `iTXt`, `zTXt`), `eXIf`, `tIME`, C2PA chunks
- WebP `EXIF`, `XMP ` and C2PA chunks
- GIF comments, plain-text extensions, non-animation application extensions, trailing data
- TIFF descriptive tags (description, make, model, software, artist, datetime, copyright, GPS/EXIF sub-IFDs)
- SVG comments, `<metadata>`, `<title>`, `<desc>`, `<script>`, and editor-specific attributes (Inkscape, Sodipodi, Illustrator, Adobe)
- MP3 ID3v2 (including multiple tags and footers), ID3v1 and APEv2
- WAV `LIST/INFO`, `id3 `, `bext`, `iXML`, `guano`, C2PA chunks
- FLAC non-STREAMINFO metadata blocks (Vorbis comments, pictures, padding, etc.)
- OGG/Opus Vorbis comment headers (packet boundaries and page CRC preserved)
- ISO-BMFF metadata atoms (`udta`, `meta`, `ilst`, `©*` tags, `XMP `, `uuid` metadata) and header timestamps
- Matroska/WebM descriptive tags via a copy-only remux
- PDF Info dictionary, catalog XMP/PieceInfo, annotation author/date fields, unreferenced objects
- OOXML/ODF core/app/custom metadata text, authorship and revision IDs, embedded thumbnails and their relationships/manifest entries, archive comments, and entry timestamps

**Intentionally preserved (and disclosed in the report):**

- Image orientation, when removing EXIF would otherwise rotate the picture
- ICC colour profiles needed for correct colour rendering
- Codec/stream parameters, duration, dimensions, tracks, and all media sample data
- Visible document content, including tracked changes and comment text
- PDF page content and structure

## Quick start

### Use the hosted app (easiest)

👉 **[https://kish-00.github.io/metastrip/](https://kish-00.github.io/metastrip/)**

1. Drag & drop one file (or use the file picker)
2. Review the metadata table; GPS coordinates include an OpenStreetMap link
3. Click **Strip metadata safely**
4. Read the report: **Removed**, **Kept**, **Changed**, **Added**, and any residual/blocked warnings
5. Only after reviewing the report, click **Download cleaned file**

### Run locally

Open `index.html` in any modern browser. That's it — no build, no install, no server needed.

> Note: the parser libraries are loaded from a CDN, so an internet connection is required on first load unless the libraries are vendored locally.

## Tech stack

- **Vanilla HTML/CSS/JS** — one file, no build step, no framework
- [ExifReader](https://github.com/mattiasw/ExifReader) — image EXIF/metadata parsing
- [JSZip](https://stuk.github.io/jszip/) — ZIP, OOXML and OpenDocument package handling
- [pdf-lib](https://pdf-lib.js.org/) — PDF metadata inspection and rewriting
- [mediabunny](https://mediabunny.dev/) — audio/video metadata and copy-only Matroska/WebM remuxing
- **Custom byte-level parsers/strippers** for JPEG, PNG, GIF, WebP, TIFF, BMP, SVG, MP3, WAV, FLAC, OGG and ISO-BMFF

Pinned CDN versions: `exifreader@4.54.0`, `jszip@3.10.2`, `pdf-lib@1.17.1`, `mediabunny@1.66.0`.

## Repository structure

| Path | Description |
| --- | --- |
| `index.html` | The complete app — HTML, CSS and JS in a single file |
| `SKILL.md` | Packaged skill definition (usage guide, full field matrix, pitfalls) |
| `devpost-draft.md` | Hackathon submission write-up |
| `metastrip-skill/` | Packaged copy of the skill |
| `metastrip-skill.zip` | Distributable archive |

## Limitations and honest caveats

- **Cleaning is not anonymization.** Visible content, filenames, and content fingerprints remain.
- **Preserved orientation and ICC profiles are deliberate.** They are reported as Kept; removing them can rotate or discolour the image.
- **ISO-BMFF in-place erase keeps file size roughly constant.** Removed atoms are replaced with empty `free` boxes so media offsets do not move. The report labels this method.
- **Matroska/WebM cleaning is a copy-only remux.** Packets are copied without transcoding, but the container bytes are rewritten.
- **Office/ODF cleaning preserves visible tracked changes and comments**, but clears their author names, initials, dates and revision IDs. Reviewing the content itself is still your responsibility.
- **Digitally signed or encrypted packages are inspect-only.** Rewriting would break signatures or is impossible without credentials.
- **HEIC/AVIF are inspect-only.** No safe remover is implemented, so the tool does not offer a misleading "clean" copy.
- **Unusual container variants may be refused.** If a safe rewrite cannot be proven, the file is marked inspect-only rather than silently passed through.
- **Large files are bounded** by browser memory; very large media may fail to load.

## License

MIT © [Kish Wachira (kish-00)](https://github.com/kish-00)

Built for **SafeSpace Hacks** — Cybersecurity / Social Good / Beginner Friendly.

---

**Live demo:** [kish-00.github.io/metastrip](https://kish-00.github.io/metastrip/) · **Repository:** [github.com/kish-00/metastrip](https://github.com/kish-00/metastrip)