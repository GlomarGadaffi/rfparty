# rfparty

wireless situational awareness dashboard. ingest BLE, WiFi, and local RF telemetry (addresses, RSSI, UUIDs, manufacturer codes, ad payloads) into an in-memory LokiJS session database, search/filter by JSONPath, visualize on a Leaflet map, and export as CSV.

built as an Electron app (Node/Parcel) but also runs as a web app. captures device metadata — connectable state, address type, MTU, company identifiers, Apple Continuity markers — and sequences them with timestamps for post-analysis or live hunt scenarios.

## quick start

```bash
npm install
npm start        # dev server on http://localhost:1234
npm run electron-start    # desktop app
npm run build    # parcel bundle to ./docs (GH Pages ready)
```

## architecture

- **index.js** — bootstrap, exposes RFParty and MainWindow to window scope
- **rfparty.js** — core session engine: Leaflet map, LokiJS in-memory db, event emitters, device tracking
- **16bit-uuid-tables.json, device-identifiers.js** — BLE device taxonomy, manufacturer mapping
- **src/reach.js** — safe nested-property accessor: walks a dot-path into an object, returns a default on missing/undefined (lodash-`get` style); used to pull fields out of device records by path
- **src/main-window.js** — `MainWindow`: DOM glue. bootstraps RFParty on load, binds the setup form, shows/hides panels, defines the search-suggestion taxonomy

## data model

devices are stored in a LokiJS collection `locationTrack` with indices on timestamp, lat, lon, filename, and source. queryable via JSONPath filters bound to the UI.

captures per-device:
- **address** (hex), **address_type** (public/random), **connectable** (boolean)
- **rssi** (dBm), **mtu**, **duration** (seconds)
- **localname**, **company**, **product**, **services**
- **companyCode**, **appleContinuityTypeCode** — for manufacturer correlation
- **lat/lon, timestamp** — geotemporal anchoring

## why

reconnaissance and debugging tool for wireless environments. built upstream by Dataparty as rfparty; maintained here as codename rfparty.
