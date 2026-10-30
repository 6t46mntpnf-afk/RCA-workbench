# Rootline

*Find it. Fix it. For good.*

Root cause analysis, lessons learned and safety alerts in one app. It opens on a dashboard showing open and closed RCAs, overdue actions, recent lessons and recent safety alerts.

- **RCAs** follow one fixed method:
  1. Event and impact (cost of failure, ISO 14224 style failure coding)
  2. Problem statement
  3. Sequence of events
  4. Failure history from a CMMS work order export (CSV or XLSX)
  5. Optional 6M fishbone
  6. 5 Whys
  7. Root causes
  8. Actions (CAPA)
  9. Customer report (HTML, print to PDF, Excel, case file)

  Each RCA has a status (Draft, In progress, Actions open, Closed).
- **Lessons learned** record what happened, what was expected, why it differed, the lesson, its category, impact, applicability, action and status (Open, In progress, Embedded, Verified). Exports are a one-page bulletin and an Excel register.
- **Safety alerts** use Daniel's one-page client layout:
  - critical risk
  - potential and actual consequence
  - what happened
  - prompts for the team
  - up to three photos
  - contact

  Exports are copy as text, Word (.doc) and a printable HTML file.

Lessons and alerts can be raised from an RCA, with the details filled in.

It installs on desktop and phone as an app (a Progressive Web App) and works offline.

## Install it

Open the hosted address (see "Hosting" below), then:

- **Windows or Mac (Chrome or Edge):** click the install icon at the right of the address bar, or open the menu and choose *Install Rootline*.
- **iPhone or iPad (Safari):** tap Share, then *Add to Home Screen*.
- **Android (Chrome):** open the menu and tap *Install app* or *Add to Home screen*.

## Where data lives

Records are saved in the browser on each device, in IndexedDB. The database is called `rca-workbench`. It has these stores:

| Store | What it holds |
| --- | --- |
| `cases` | RCAs |
| `lessons` | Lessons learned |
| `alerts` | Safety alerts |
| `meta` | Settings and the last opened RCA |

They do not sync between devices. Use **Settings › Download a full backup** to back up, and **Restore from a backup** to move records to another device. Earlier versions' data (localStorage, or IndexedDB version 1) is upgraded automatically.

## Files

| File | What it is |
| --- | --- |
| `index.html` | The whole app: styles, page structure and all JavaScript in one file |
| `sw.js` | Service worker that caches the app for offline use |
| `manifest.webmanifest` | App name, colours and icons used when it is installed |
| `icons/` | App icons (the Rootline mark) |
| `vendor/xlsx.full.min.js` | SheetJS 0.18.5, reads CSV/XLSX and writes Excel exports (Apache 2.0, see `vendor/xlsx-LICENSE.txt`) |
| `vendor/manrope-latin-wght-normal.woff2` | Manrope typeface (SIL Open Font Licence, see `vendor/manrope-OFL.txt`) |

### Inside `index.html`

The script is split into labelled sections (search for `/* ----------`):

- **constants / app identity**:
  - the RCA steps, 6M categories and dropdown lists (`LISTS`)
  - work order column aliases (`WO_FIELDS`)
  - the app name and logo (`APP`, `MARK`)
  - RCA statuses
  - lesson categories
  - safety alert consequence and critical risk lists
- **case model**: `blankCase()` and `exampleCase()`.
- **calculations**: cost of failure, problem statement, failure history, MTBF.
- **state + persistence**: IndexedDB storage, saving and the save-failure banner.
- **records**: blank and example lessons and alerts, reference numbers (LL-YYYY-NNN, SA-YYYY-NNN), overdue actions.
- **rendering: app shell**: sidebar, phone tab bar, and the views (dashboard, RCA list, RCA, lessons, alerts, settings).
- **record sheets**: the lesson and safety alert editors.
- **safety alert output** and **lesson outputs**: the alert layout, Word/print files, bulletin and Excel register.
- **step renderers**, **SVG builders**, **report**, **import**, **exports**: the RCA method and its report.
- **events** and **boot**.

Colours are CSS variables at the top of the `<style>` block (Steel & Copper: `--solid` #1A1A1A, `--accent` #B0683A), with a dark mode. The report's default brand colour is `#1A1A1A`. Each RCA can set its own brand colour and logo; otherwise it uses the company logo from Settings.

## Making a change

1. Edit `index.html` (or ask Claude to, pointing it at this repository).
2. Serve the folder to check it, for example `python3 -m http.server 8000`, then open http://localhost:8000.
3. In `sw.js`, change `CACHE` to a new version, for example `rca-v2.0.1`. Installed apps only pick up new files when this changes.
4. Commit and push to `main`. GitHub Pages republishes within a minute or two. Installed apps update the next time they are opened while online (sometimes it takes a second open).

## Hosting

GitHub Pages: in the repository, go to *Settings › Pages*. Set *Source* to *Deploy from a branch*, choose `main` and `/ (root)`, and save.
The app is then at `https://<your-github-user>.github.io/<repository-name>/`.

Any static host works too (Netlify, Azure Static Web Apps, an internal web server). It needs HTTPS for install and offline to work.

## Claude version

The same app also runs inside Claude as an artifact. That version saves records to your Claude account, so they sync between devices. It also adds Claude-assisted fishbone suggestions and executive summary drafting. The two versions keep their records separately; move them with a backup file.
