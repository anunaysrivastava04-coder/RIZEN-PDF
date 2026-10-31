# RIZENPDF

**Everything PDF. Absolutely Free.**

RIZENPDF is a free PDF toolkit that runs in your browser. Your files are processed on your own device and are never uploaded to a server. There is no account, subscription, watermark or usage limit.

LIVE LINK =  rizenpdf.netlify.app

Developed by **RIZEN CODERS** · Tagline: *Your Documents. Your Control.*

> **Status: early prototype (v0.1.0).** 25 tools work in a single HTML page. Automated tests are not written yet, and several planned tools are not implemented yet. See [Not implemented yet](#not-implemented-yet).

---

## Features

### Organize
- **Merge PDF:** combine several PDFs in the order listed.
- **Split PDF:** split by page ranges, or into one PDF per page. Multiple outputs download as a ZIP.
- **Remove Pages:** delete the pages you list.
- **Extract Pages:** keep only the pages you list.
- **Organize Pages:** reorder, duplicate or drop pages using a page order such as `3, 1-2, 1`.
- **Scan to PDF:** combine photos or scans into one PDF. On phones, the camera opens where the browser supports it.

### Edit
- **Rotate PDF:** turn every page by 90°, 180° or 270°.
- **Page Numbers:** stamp "page / total" at the bottom of each page.
- **Add Watermark:** add a diagonal text watermark to every page.
- **Crop PDF:** trim margins, set in points.
- **Sign PDF:** draw a signature and place it on a page. This is an image, not a certified digital signature.

### Optimize
- **Compress PDF:** choose low, medium or high compression.
- **Repair PDF:** re-save a damaged PDF to rebuild its structure. This fixes some damage only.

### Convert to PDF
- **Image to PDF:** combine JPG, PNG and WEBP images, with page size, orientation and margin options.
- **JPG to PDF, PNG to PDF, WEBP to PDF:** the same engine, limited to one image type each.
- **Word to PDF:** convert a .docx file's text and paragraphs to PDF.

### Convert from PDF
- **PDF to JPG** and **PDF to PNG:** render each page as an image, with a choice of resolution. Multi-page files download as a ZIP.
- **PDF to Text:** extract the text of every page into a .txt file.
- **PDF to Word:** export each page's text as paragraphs in a .docx file.

### PDF Security
- **Compare PDF:** compare the text of two PDFs line by line and export the differences.
- **Remove Metadata:** clear the title, author, subject, keywords, creator and producer fields.

### PDF Intelligence
- **Summarize PDF:** pick the most important sentences from the document. It runs locally and does not use an AI service.

---

## Languages and technologies

| Area | Technology | Version | Used for | License |
|---|---|---|---|---|
| Markup | HTML5 | – | Page structure | – |
| Styling | CSS3 | – | Layout, responsive grid, hover effects | – |
| Logic | JavaScript (ES2020+) | – | All application logic | – |
| PDF editing | [pdf-lib](https://pdf-lib.js.org/) | 1.17.1 | Merge, split, rotate, crop, watermark, signatures, page numbers, metadata | MIT |
| PDF rendering and text | [PDF.js](https://mozilla.github.io/pdf.js/) | 3.11.174 | Rendering pages to images, extracting text | Apache-2.0 |
| ZIP and DOCX | [JSZip](https://stuk.github.io/jszip/) | 3.10.1 | ZIP downloads, reading and writing .docx files | MIT or GPLv3 |

Browser APIs used: File and Blob, Canvas 2D, Pointer Events, drag and drop, DOMParser, `createImageBitmap`, and `localStorage`.

The three libraries load from [cdnjs](https://cdnjs.com/) when a tool needs them, so an internet connection is required. The project has no npm dependencies, build step or backend.

---

## How to run

### Requirements
- A recent version of Chrome, Edge, Firefox or Safari
- An internet connection, for the libraries from cdnjs
- Optional: Node.js 18 or newer, for `npm start`

### Option 1: open the file
Double-click `index.html`. Most tools work this way. If a tool fails, use option 2 or 3.

### Option 2: npm start
```bash
npm start
```
Then open the local address the command prints, usually `http://localhost:3000`.

### Option 3: any static server
```bash
python3 -m http.server 8080
```
Then open `http://localhost:8080`.

---

## Deploy

The project is a static site with no build step.

- **GitHub Pages:** in the repository, go to Settings → Pages, choose *Deploy from a branch*, select `main` and the root folder `/`.
- **Netlify:** drag the project folder onto [app.netlify.com/drop](https://app.netlify.com/drop), or connect the repository. Leave the build command empty and set the publish directory to `.`.
- **Vercel:** import the repository. Set the framework preset to *Other*, leave the build command empty, and set the output directory to `.`.

---

## Push to GitHub

Create an empty repository named `rizenpdf` on GitHub first, then run:

```bash
git init
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/<your-username>/rizenpdf.git
git push -u origin main
```

---

## Project structure

```
rizenpdf/
├── index.html     # The whole app: markup, CSS and JavaScript
├── README.md      # This file
├── package.json   # Only for the optional npm start helper
└── .gitignore
```

---

## How it works

1. You choose a tool, or drop files onto the page.
2. The file is read into memory with the browser's File API.
3. pdf-lib, PDF.js or JSZip process it on your device.
4. The result is offered as a download.

No file leaves your device. The only network requests go to `cdnjs.cloudflare.com`, for the three libraries.

---

## Adding a tool

Every tool is one object in the `TOOLS` array in `index.html`, or added later with `TOOLS.push({...})`. The fields are:

| Field | Meaning |
|---|---|
| `id` | Unique ID, used in the URL hash, for example `#merge` |
| `cat` | Category name from the `CATS` list |
| `name`, `d`, `note` | Title, short description, and an optional limitation note |
| `c`, `g` | Tile colour and the one-character icon |
| `acc` | Accepted MIME types, for example `application/pdf` |
| `multi` | `1` if the tool accepts several files |
| `opts` | Settings shown in the panel: `sel` (dropdown), `txt` (text) or `num` (number) |
| `run` | `async (files, options, progress) => [{ name, blob }]` |

Example:

```js
TOOLS.push({
  id: 'mytool', cat: CATS[0], name: 'My Tool', d: 'What it does.',
  c: '#2563eb', g: 'M', acc: PDF,
  run: async (files, o, rep) => {
    const L = await lib('pdf');
    const doc = await load(files[0]);
    // ...change doc...
    return [{ name: 'output.pdf', blob: pdfBlob(await doc.save()) }];
  }
});
```

Return one item for a single file, or several items to have them packed into a ZIP automatically.

---

## Not implemented yet

- **PDF to PowerPoint, PDF to Excel, Excel to PDF, PowerPoint to PDF:** good layout needs a server-side engine such as LibreOffice.
- **PDF to PDF/A:** needs Ghostscript or veraPDF.
- **HTML to PDF:** browsers cannot fetch arbitrary web pages because of cross-origin rules, so this needs a server.
- **Protect PDF and Unlock PDF:** pdf-lib cannot encrypt or decrypt files. The planned approach is qpdf compiled to WebAssembly.
- **Redact PDF:** true removal needs editing at the content level, not just covering text.
- **OCR PDF:** planned with Tesseract.js and language data files.
- **Edit PDF and PDF Forms:** full editors are not built yet.
- **Translate PDF:** needs a translation provider that the user configures.
- **Platform work:** a React, TypeScript and Vite rewrite, dark mode, a PWA, a multi-language interface, a workflow builder, automated tests (Playwright), and an optional AI provider for summaries and translation.

---

## Known limitations

- **Compress PDF** re-renders pages as images, so text is no longer selectable or searchable.
- **Crop PDF** sets the visible area. Content outside it stays in the file.
- **Word and PDF text conversions** keep text and paragraphs only. Layout, fonts, images and tables are lost.
- **Summarize PDF** selects existing sentences. It does not write new ones.
- **Repair PDF** re-saves the file and fixes only some kinds of damage.
- **Sign PDF** places a drawn image. It is not a certified digital signature.
- **Remove Metadata** clears the document information fields, but not the XMP metadata packet.
- **Scanned PDFs** have no text layer, so text-based tools cannot read them until OCR is added.
- **Organize Pages** uses a typed page order. Drag-and-drop thumbnails are not available yet.
- **Large files** are limited by the memory of your browser and device.

---

## Privacy

- No uploads, no analytics and no cookies.
- The only data stored is the list of recently used tool IDs, kept in `localStorage` under the key `rizenpdf_recent`.
- The only third-party requests go to `cdnjs.cloudflare.com`.

---

## Contributing

Open an issue or a pull request. Keep changes in `index.html` until the project is split into separate files.

---

## License

No license file has been added yet. Add one (for example MIT) before accepting outside contributions.

Copyright © 2026 RIZEN CODERS.

---

## Credits

- [pdf-lib](https://pdf-lib.js.org/), [PDF.js](https://mozilla.github.io/pdf.js/) and [JSZip](https://stuk.github.io/jszip/)
- The feature set is inspired by popular online PDF tools. No code or branding is copied.
