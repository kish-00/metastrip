# MetaStrip

**One-line pitch:** A client-side metadata inspector and safe stripper for images, audio, video, PDFs, Office documents and ZIP archives — see exactly what a file reveals, remove what can be removed safely, and download an honest audit report of what changed.

## The problem

Almost every file you share carries invisible metadata. Photos embed GPS coordinates, camera serial numbers, timestamps and sometimes your name. Audio and video files carry titles, artists, creation dates and software fingerprints. PDFs and Office documents store authors, editors, revision history, comments and embedded thumbnails. Archives leak usernames, paths and timestamps.

Most people don't know this metadata exists. Worse, many "metadata remover" websites require uploading the file to a third-party server — which is exactly the wrong trust model for a privacy tool.

## The solution

MetaStrip runs entirely in your browser:

1. **Drop a file** — JPEG, PNG, GIF, WebP, TIFF, BMP, SVG, MP3, WAV, FLAC, OGG/Opus, M4A, MP4, MOV, M4V, WebM, MKV, PDF, DOCX, XLSX, PPTX, ODT/ODS/ODP, or ZIP.
2. **See what's inside** — format is detected from magic bytes, not the file extension. Metadata is shown field by field; GPS coordinates are decoded and linked to OpenStreetMap.
3. **Strip safely** — the tool uses format-aware byte surgery or lossless container rewrites instead of blindly re-encoding. Images keep their pixels; the normal audio/video path does not transcode; PDF page content and document text are preserved. A separate optional X-compatible MP4 export handles videos X/Twitter would reject.
4. **Read the report** — every field is labelled **Removed**, **Kept**, **Changed**, or **Added**. Intentionally preserved technical metadata, such as image orientation and ICC colour profiles, is disclosed. If a safe rewrite cannot be proven, the file is marked inspect-only instead of being presented as clean.
5. **Download deliberately** — cleaning never auto-downloads. You review the report first.

## Why it is different

- **Zero upload, zero backend.** Processing happens in the browser via FileReader/Blob APIs. There is no server to log, leak, or sell your files.
- **No fake "clean".** HEIC/AVIF, encrypted archives, and digitally signed documents are reported as inspect-only because no safe remover is implemented or rewriting would break the signature.
- **Before/after verification.** Cleaned output is re-inspected and diffed against the original before the download button is enabled.
- **Field-level honesty.** The report distinguishes metadata that was removed from technical metadata that was deliberately kept, and flags anything residual.
- **Content-preserving methods.** JPEG/PNG/WebP/GIF cleaning is surgical byte removal, not canvas re-encoding. MP4/MOV metadata is erased in place so media offsets do not move. WebM/MKV use a copy-only remux. FLAC keeps STREAMINFO and audio frames. OGG preserves packet boundaries and recalculates page CRCs. The optional X-compatible MP4 export is explicitly marked as a separate, lossy compatibility path.

## Built with

- Vanilla HTML/CSS/JS — one file, no build step, no framework
- [ExifReader](https://github.com/mattiasw/ExifReader) for image EXIF/metadata parsing
- [JSZip](https://stuk.github.io/jszip/) for ZIP, OOXML and OpenDocument package handling
- [pdf-lib](https://pdf-lib.js.org/) for PDF metadata inspection and rewriting
- [mediabunny](https://mediabunny.dev/) for audio/video metadata, copy-only Matroska/WebM remuxing and optional X-compatible H.264/AAC-LC MP4 export
- [@mediabunny/aac-encoder](https://mediabunny.dev/guide/extensions/aac-encoder) for an optional pinned WASM AAC-LC encoder fallback
- Custom byte-level parsers/strippers for JPEG, PNG, GIF, WebP, TIFF, BMP, SVG, MP3, WAV, FLAC, OGG and ISO-BMFF

## Privacy by design

Everything happens client-side. There is no analytics, no upload endpoint, and no persistence of file contents. The only network requests are for the open-source parser libraries loaded from a CDN.

## Challenges

- Safely removing EXIF from JPEG without touching entropy-coded image data, while preserving orientation when needed.
- Rewriting ID3v2, FLAC metadata blocks, RIFF chunks and Ogg pages without breaking packet boundaries, checksums or playback.
- Erasing metadata atoms in MP4/MOV in place so chunk offsets and sample data remain valid.
- Reporting honestly when an MP4 uses a codec X/Twitter rejects and providing an explicitly labelled X-compatible export instead of pretending metadata cleaning fixes codec rejection.
- Clearing OOXML/ODF authorship and revision metadata while preserving visible document content, tracked changes and comments.
- Keeping the report truthful: distinguishing Removed, Kept, Changed and Residual instead of claiming everything is gone.
- Building a single-file app that still handles a broad format matrix without a bundler.

## Lessons learned

A metadata tool's most important feature is honesty. Removing some fields is easy; knowing which fields cannot be removed safely — and saying so — is the hard part. Byte-level format awareness matters: canvas re-encoding "works" for photos but destroys quality and cannot generalize to audio, video, or documents. Re-inspecting the cleaned output and showing a field-level diff turned out to be the strongest correctness check. The same honesty applies to compatibility work: an X-upload failure caused by VP9 cannot be fixed by stripping metadata, so it is reported as a codec issue and handled by a separate, clearly labelled X-compatible export.

## Live demo

https://kish-00.github.io/metastrip/

## Repository

https://github.com/kish-00/metastrip

## Submission

SafeSpace Hacks — Cybersecurity / Social Good / Beginner Friendly
