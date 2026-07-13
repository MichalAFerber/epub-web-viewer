# EPUB Viewer

A fast, mobile-first, **single-file** EPUB viewer. Drag & drop an `.epub`
anywhere on the page and start reading instantly. It's a **viewer, not an
editor** — no toolbars, no accounts, no uploads. Everything runs locally in
your browser.

🔗 **Live:** <https://epub-viewer.us/>

![Single file](https://img.shields.io/badge/build-single%20HTML%20file-success) ![No build step](https://img.shields.io/badge/build%20step-none-success) ![License](https://img.shields.io/badge/license-MIT-blue)

## Features

- 📚 **Drag & drop anywhere** — drop an `.epub` on the page to read it. You can
  also **paste** a copied file, or tap the page to open a file picker.
- 🏷️ **Book title & author in the header** — pulled from the book's metadata
  and shown in the top-left.
- 🗂️ **Table of contents** — a list button in the header opens a slide-in
  panel built from the book's navigation (EPUB 3 nav doc or EPUB 2 NCX), with
  nested sections and the current chapter highlighted.
- ⏮️⏭️ **Chapter navigation** — Previous/Next buttons at the end of each
  chapter (plus `←`/`→` on a keyboard) and a "chapter x / y" position readout.
  Internal links and footnotes inside the book work too.
- 📋 **Copy** — copies the current chapter as plain text to your clipboard.
- 🗑️ **Clear** — closes the book and empties the view.
- 🎨 **Pick any background color** — a color swatch in the header lets you
  choose any background. Text, borders, and code blocks automatically adapt
  for contrast. Your choice is **remembered** — saved to a cookie, with a
  `localStorage` fallback so even a downloaded `file://` copy remembers it.
  Starts on white.
- 🫥 **Distraction-free** — once a book is open the header hides when you
  **scroll down** and comes back when you **scroll up** (or after a few
  seconds); tapping the top edge reveals it on mobile. A **×** in the
  footer's corner tucks the footer away too.
- 📱 **Mobile-first** & responsive, with safe-area support for notched phones.
- 🔒 **Safe by default** — every chapter is sanitized with DOMPurify before it
  touches the page, so a malicious book can't run scripts. Your books never
  leave your device.
- 🪶 **One file, no build** — `index.html` is self-contained (~170 KB) and the
  viewer works offline, even straight from `file://`.
- 📊 **Privacy-friendly analytics** — the hosted site uses a self-hosted,
  cookieless [Plausible](https://plausible.io/) instance (see [Privacy](#privacy)).

## Quick start

**Just open it.** Download [`index.html`](index.html), double-click it, and
you're done — it runs locally with no server, no build, and no internet
connection.

Prefer a local server?

```sh
python3 -m http.server 8080   # then open http://localhost:8080
```

Any static web server works too — there is nothing to build.

## Deploy to Cloudflare Pages (auto-deploy from this repo)

Connect the repository in the Cloudflare dashboard
(**Workers & Pages → Create → Pages → Connect to Git**) and use:

| Setting                  | Value            |
| ------------------------ | ---------------- |
| Production branch        | `main`           |
| Framework preset         | `None`           |
| Build command            | *(leave blank)*  |
| Build output directory   | `/`              |

It's a static site — Cloudflare just serves `index.html`. The included
[`_headers`](_headers) file applies security headers (CSP, `nosniff`,
`frame-ancestors`, etc.) automatically. Every push to `main` then
auto-deploys.

Add your custom domain `epub-viewer.us` under the project's **Custom domains**
tab — Cloudflare creates the DNS record and provisions SSL for you.

## How it works

Everything is in [`index.html`](index.html):

- Your HTML/CSS/UI and the app logic — no build step, no tooling.
- [**JSZip**](https://github.com/Stuk/jszip) `v3.10.1` (MIT) unzips the EPUB
  (an EPUB is a ZIP archive) entirely in memory.
- The viewer's own lightweight EPUB engine reads `META-INF/container.xml`,
  the OPF package (metadata, manifest, spine), and the table of contents
  (EPUB 3 nav doc, with EPUB 2 NCX fallback) using the browser's built-in
  `DOMParser` — no rendering iframe, no external reader library.
- [**DOMPurify**](https://github.com/cure53/DOMPurify) `v3.1.6` sanitizes
  every chapter before it is rendered; the book's own stylesheets are dropped
  so typography stays consistent and adapts to your chosen background color.
- Images and media inside the book are served from in-memory `blob:` URLs,
  created on demand and revoked when the book is closed.

Both libraries are embedded inline (minified, with their license banners
retained), so the viewer renders with **no network dependencies** and works
fully offline. The only external request is the analytics script on the hosted
site (see [Privacy](#privacy)).

## Limitations

- DRM-protected books (e.g. Adobe DRM) can't be decrypted — the viewer warns
  you and shows whatever isn't encrypted.
- The book's embedded fonts and stylesheets are intentionally ignored in
  favor of the viewer's clean, color-adaptive typography.
- Fixed-layout (pre-paginated) EPUBs are rendered as reflowable text.

## Privacy

Your books stay on your device — they're unzipped and rendered in the browser
and never uploaded anywhere, and your content is never sent to any server.

The hosted site at **epub-viewer.us** uses a self-hosted
[Plausible](https://plausible.io/) instance for **privacy-friendly, cookieless**
page analytics: aggregate visit counts only — no personal data, no cross-site
tracking, no advertising. Plausible automatically ignores `localhost` and
`file://`, so a downloaded local copy reports nothing.

The only thing stored on your device is your chosen background color (a cookie,
with a `localStorage` fallback).

## License

[MIT](LICENSE) © 2026 Michal Ferber, aka **TechGuyWithABeard**.

Bundled third-party libraries retain their own licenses (JSZip: MIT;
DOMPurify: Apache-2.0 / MPL-2.0) — see [`LICENSE`](LICENSE) for details.
