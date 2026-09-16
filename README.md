# GOFO DSSLC Network Ops Console

Single static site: network operations map for the DSSLC region (Denver / Salt Lake City area
stations: DEN, SLC, ABQ, IDA, SAF, LGU, PIH, CYS, BIL, BZN, TWF, GJT, FMN).

```
index.html                     Network map (main page)
data/price_adjustments.csv     Price sheet — edit this file to adjust ZIP prices
README.md
```

## Deploy once → get a shareable link (GitHub Pages)

1. Create a new repository on github.com (e.g. `dsslc-ops-console`). Private repos need GitHub
   Pro for Pages; public works on the free plan.
2. Upload everything in this folder to the repo root (drag-and-drop on github.com works:
   Add file → Upload files → commit).
3. In the repo: **Settings → Pages → Source: Deploy from a branch → Branch: main, folder: /
   (root) → Save**.
4. After ~1 minute your shareable link is live:
   `https://<your-username>.github.io/dsslc-ops-console/`

Anyone with the link sees the same data. Bookmark/save the link — it stays stable across updates.

## Updating prices (the "run it" workflow)

`data/price_adjustments.csv` holds the baseline (316 ZIPs, columns: `zip,price,station,route` —
only the first two columns are read; station/route are reference only).

1. Edit prices in the CSV (Excel is fine — keep it saved as CSV).
2. Commit the file to the repo (on github.com: open the file → pencil icon → paste/edit → Commit,
   or replace via Upload files).
3. GitHub Pages redeploys automatically in ~1 minute. Everyone opening the shared link sees the
   new prices — ZIP colors, station avg prices, and rankings all recompute on load.

## What's different from the NorCal console this was modeled on

- **ZIP boundaries**: same idea as the NorCal map — real US Census ZCTA5 (2010) polygons, filled
  and colored by station (or by price, in the Price view), sourced from the public
  [OpenDataDE/State-zip-code-GeoJSON](https://github.com/OpenDataDE/State-zip-code-GeoJSON) dataset
  and simplified. 307 of the 316 ZIPs have a shape on file; the other 9 (mostly PO-box-only or
  campus ZCTAs not present in the 2010 boundary layer — e.g. 87101, 87131, 84602) fall back to a
  circle marker at the ZIP's centroid instead, same as the NorCal console's own fallback for ZIPs
  without geometry.
- **Basemap tiles** are standard OpenStreetMap tiles, same source the NorCal page uses. They need
  a live connection to `tile.openstreetmap.org`, so they render normally once this is hosted on
  GitHub Pages (or any normal web host) — but **Claude's own artifact-preview sandbox blocks
  third-party map images**, so if you view this page through a Claude-hosted preview link instead
  of the real GitHub Pages URL, you'll see the ZIP shapes and stations on a plain dark background
  with no street map underneath. Everything else (filters, popups, search, price sheet) works the
  same either way — it's only the tile preview that needs actual GitHub Pages hosting to show up.
- **Station locations**: geocoded from the ZIP embedded in each station's street address (offline
  ZIP-centroid lookup, not a full street-address geocode), so pins sit at the station's ZIP center
  rather than the exact building. For BZN and FMN, no address was on file, so the pin instead uses
  the station's highest-volume served ZIP — flagged with an amber note in the sidebar and in that
  station's popup.
- **Source data quirks fixed automatically during import**: in the original spreadsheet the DEN
  sheet had its "ZIP code" and "price" columns swapped, and the CYS sheet had the ZIP code
  duplicated into the price column with price shifted into the volume column. Both were detected
  and corrected before building this site. LGU, BIL, BZN, TWF, GJT, and FMN have no daily-volume
  column in the source file at all, so they show as 0/day (prices are still accurate).

## Local preview before publishing

Opening `index.html` directly from disk (file://) can't auto-load `data/price_adjustments.csv`
due to browser security — the page will show baseline data and a note that the CSV wasn't
reachable. On the GitHub Pages link, the CSV loads normally.
