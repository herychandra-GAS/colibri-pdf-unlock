# Colibri PDF Unlock — Web

Remove passwords from PDFs entirely in the browser. No upload, no server,
no account — the file never leaves the visitor's machine.

This is the browser-hosted version, meant for GitHub Pages (or any static
host). It uses a real build of [qpdf](https://qpdf.readthedocs.io/) compiled
to WebAssembly (`@jspawn/qpdf-wasm` on npm), so it performs a **true
decrypt** — not a re-render. Text stays selectable, vector art stays sharp,
and output quality is identical to the original file.

## How it works

1. Drop in one or more password-protected PDFs (or click to choose files)
2. Type the password (same one is tried against every file in the batch)
3. Click **Remove passwords**
4. One file downloads directly as a `.pdf`; more than one downloads as a
   single `.zip`

Files that fail (wrong/missing password) are flagged in the list but don't
block the rest of the batch.

## Hosting on GitHub Pages

1. Push this folder's contents to a GitHub repo (root, or a `/docs` folder)
2. In the repo, go to **Settings → Pages**
3. Under **Build and deployment**, choose **Deploy from a branch**, pick the
   branch and folder (`/` or `/docs`) where these files live
4. GitHub gives you a URL like `https://<username>.github.io/<repo>/` —
   that's it, no build step, no server

Because everything runs client-side, this also works from any other static
host (Netlify, Vercel, S3 + CloudFront, or just opening `index.html`
directly in a browser — though some browsers restrict `file://` pages from
loading the `.wasm` file, so a local `http://` server or a real host is
recommended).

### Serving locally for testing

Any static file server works, e.g. from this folder:

```sh
python3 -m http.server 8000
```

Then open `http://localhost:8000/`.

## Files

- `index.html` — the whole app (structure, styling, and logic in one file)
- `lib/qpdf.wasm`, `lib/qpdf.js` — qpdf compiled to WebAssembly
  ([`@jspawn/qpdf-wasm`](https://www.npmjs.com/package/@jspawn/qpdf-wasm),
  Apache-2.0 — see `LICENSE-qpdf-wasm.txt`)
- `lib/jszip.min.js` — [JSZip](https://stuk.github.io/jszip/) (MIT), used to
  bundle a batch's outputs into one `.zip`

## Notes

- The password is used only in-memory to decrypt; it's never transmitted,
  logged, or stored.
- This removes an existing **user password** (the one needed to open the
  file). It does not attempt to crack or brute-force an unknown password.
- Very large files or very large batches run entirely on the visitor's CPU,
  so performance depends on their machine — there's no server doing the
  work.
