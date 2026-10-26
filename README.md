# RCA Workbench

© 2026 Daniel. All rights reserved. Proprietary software, see [LICENSE](LICENSE).

A guided root cause analysis (RCA) app. It follows one fixed method for every customer:

1. Event and impact (cost of failure, failure coding with ISO 14224 dropdowns)
2. Problem statement (what, where, when, impact; no cause words)
3. Sequence of events (timeline)
4. Failure history from a work order export from any maintenance system (CSV or XLSX)
5. 6M fishbone (optional, switched on per case)
6. 5 Whys
7. Root causes (physical, human, latent) with evidence and confidence, plus a check of the recorded cause code
8. CAPA register (hierarchy of controls, owner, due date, how it is carried out, effectiveness check)
9. Customer report: download as HTML, print to PDF (A4), Excel workbook, case file (.json)

It installs on desktop and phone as an app (a Progressive Web App) and works offline.

## Install it

Open the hosted address (see "Hosting" below), then:

- **Windows or Mac (Chrome or Edge):** click the install icon at the right of the address bar, or open the menu and choose *Install RCA Workbench*.
- **iPhone or iPad (Safari):** tap Share, then *Add to Home Screen*.
- **Android (Chrome):** open the menu and tap *Install app* or *Add to Home screen*.

## Where data lives

Cases are saved on each device in the browser's built-in database (IndexedDB: database `rca-workbench`, store `cases`, one record per case). They do not sync between devices. There is no cost and no server. If a save ever fails (for example the device's storage is full), the app shows a red "Not saved" warning with a button to export the case file.
To move or back up a case, use **Report & export › Case file (.json)**, then **Open case file** on the other device.
Clearing the browser's site data deletes saved cases, so export case files you want to keep.

The app makes no requests to outside servers: no accounts, analytics or tracking. Fonts fall back to the device's system font (Inter if it is installed).

## Files

| File | What it is |
| --- | --- |
| `index.html` | The whole app: styles, page structure and all JavaScript in one file |
| `sw.js` | Service worker that caches the app for offline use |
| `manifest.webmanifest` | App name, colours and icons used when it is installed |
| `icons/` | App icons |
| `vendor/xlsx.full.min.js` | SheetJS 0.18.5, reads CSV/XLSX and writes the Excel export (Apache 2.0 licence, see `vendor/xlsx-LICENSE.txt`) |

### Inside `index.html`

The script is split into labelled sections (search for `/* ----------`):

- **constants**: the step list, 6M categories, CAPA types, `CARRIED` (how actions are carried out), `LISTS` (all dropdown suggestion lists: failure coding, fishbone causes, owners, evidence sources, effectiveness checks), and `WO_FIELDS`, the column name aliases used to auto-map work order exports. Add a CMMS column name to an `alias` list (lower case, letters and digits only) to have it recognised.
- **case model**: `blankCase()` (the data shape of one RCA) and `exampleCase()` (the worked example).
- **calculations**: cost of failure, problem statement, failure history, MTBF and repeat-failure logic.
- **step renderers**: `RENDER.event`, `RENDER.problem` and so on, one function per step.
- **SVG builders**: timeline, failure history chart and fishbone diagram, shared by the app and the report.
- **report**: `reportCSS()` (report look and print layout) and `buildReport()` (report sections).
- **import / exports**: work order import and column mapping, Excel and HTML export.

Colours are CSS variables at the top of the `<style>` block (`--primary`, `--teal` and so on). The report's default brand colour is `#3D72AD`. Each case can set its own brand colour and logo in step 1.

## Making a change

1. Edit `index.html` (or ask Claude to, pointing it at this repository).
2. Open `index.html` locally to check it. For offline and install behaviour, serve the folder instead, for example `python3 -m http.server 8000`, then open http://localhost:8000.
3. In `sw.js`, change `CACHE` to a new version, for example `rca-v1.1.0`. Installed apps only pick up new files when this changes.
4. Commit and push to `main`. GitHub Pages republishes within a minute or two. Installed apps update the next time they are opened while online (sometimes it takes a second open).

## Hosting

GitHub Pages: in the repository, go to *Settings › Pages*, set *Source* to *Deploy from a branch*, choose `main` and `/ (root)`, and save.
The app is then at `https://<your-github-user>.github.io/<repository-name>/`.

Any static host works too (Netlify, Azure Static Web Apps, an internal web server). It needs HTTPS for install and offline to work.

## Claude version

The same app also runs inside Claude as an artifact (RCA Workbench). That version saves cases to your Claude account, so they sync between devices, and it adds Claude-assisted fishbone suggestions and executive summary drafting. The two versions keep their cases separately; move cases between them with the case file export.
