# 3D GPX Viewer

A single-page web app that puts any 3D model (.glb or .3mf) inside a clear resin dome, with
backgrounds, lighting, AR placement and downloads. Everything runs in the visitor's browser;
there is no server code and nothing is uploaded anywhere.

## What's in this folder

| File | Purpose |
| --- | --- |
| `index.html` | The whole app (HTML, CSS and JavaScript in one file). |
| `models/default.glb` | The model shown when the page first opens. |
| `examples/` | Created when you publish examples (see below). Not present until then. |
| `favicon.ico`, `icon-*.png`, `apple-touch-icon.png`, `site.webmanifest` | Browser tab icon, home-screen icon and app details. |
| `3D-GPX-Viewer-User-Guide.pdf` | The user guide, linked from the Help screen's Download PDF button. |
| `3D-GPX-Viewer-Admin-Guide.pdf` | The admin guide, linked from the Admin guide screen (Examples tab, after signing in). |
| `_headers` | Correct file types and caching on Netlify and Cloudflare Pages. |
| `vercel.json` | The same for Vercel. |
| `.nojekyll` | Stops GitHub Pages from processing the files. |

## Deploy

The site must be served over **HTTPS** for AR and the camera to work (all the hosts below do this
automatically). It won't work opened straight from your disk (`file://`), because the browser
blocks loading the default model that way. To try it locally, run a small server in this folder,
for example `python3 -m http.server 8000`, then open http://localhost:8000.

- **Netlify:** go to app.netlify.com/drop and drag this whole folder onto the page.
- **Cloudflare Pages:** create a project, choose "Upload assets" and upload this folder.
- **Vercel:** run `npx vercel` in this folder, or import it from a Git repository.
- **GitHub Pages:** push the folder to a repository, then in Settings → Pages choose the branch.
- **Any web server:** copy the files to the web root. Make sure `.glb` files are served
  (most servers do; if not, add the type `model/gltf-binary`).

## Changing the default model

Replace `models/default.glb` with your own file (keep the name), or edit this line near the
bottom of `index.html` to point somewhere else:

```html
<script>window.DEFAULT_MODEL="models/default.glb";</script>
```

Remove that line to open with an empty scene and an upload prompt instead.

## Admin and examples

The **Examples** tab shows ready-made models visitors can open with one tap. Admins add them.

**Signing in.** Open the Examples tab and enter the admin password at the bottom. The default
password is `elevate-admin`. Change it before you publish the site:

1. Pick a password and get its SHA-256 hash. On Mac or Linux: `printf '%s' 'your-password' | shasum -a 256`.
   On Windows PowerShell: `$s=[Text.Encoding]::UTF8.GetBytes('your-password'); -join ([Security.Cryptography.SHA256]::Create().ComputeHash($s) | % { $_.ToString('x2') })`.
2. In `index.html`, replace the long value in `window.ELEVATE_ADMIN_HASH="…"` with your hash.

**Adding examples.** Load a model, set up the resin, background, lighting and camera angle, then in
the Examples tab give it a name and choose *Add current view as an example*. Reorder with ↑ ↓ and
delete with ✕ (press twice). Your changes are kept in that browser as a draft.

**Publishing examples.** Choose *Export examples for your website*. This downloads
`elevate-examples.zip`. Unzip it into this folder (it creates `examples/examples.json` and
`examples/models/`) and redeploy. Visitors then see the new examples.

**About security.** A website with no server can't truly lock anything, so the password only
hides the admin tools. That's fine here: an admin can only change what's in their own browser,
and nothing reaches visitors until someone with access to your hosting uploads the exported files.
If you later want admins to publish directly from the page, you'll need a small backend (for
example Supabase or Firebase) for sign-in and storage.

## Dependencies

The 3D engine (three.js 0.170.0) and the Figtree font load from public CDNs
(cdn.jsdelivr.net and fonts.googleapis.com), so visitors need an internet connection. To host
everything yourself, download three.js 0.170.0 and change the two URLs in the `importmap` near
the top of `index.html` to your own copies of `build/three.module.js` and `examples/jsm/`.

## Features that behave differently on your own site

- **Downloads** save the `.glb` or `.usdz` directly (inside Claude they arrive as a `.zip`).
- **iPhone and iPad:** "View in AR" opens Apple's AR Quick Look straight from the page.
- **Android (Chrome, ARCore devices):** "Place on a surface (AR)" starts tap-to-place AR.
- **Settings and uploaded background photos** are remembered per browser, in local storage.

## Browser support

Current Chrome, Edge, Safari and Firefox on desktop and mobile. Reading .3mf files needs a
browser from 2023 or later.
