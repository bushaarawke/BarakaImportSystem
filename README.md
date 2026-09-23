# Baraka Import System

An import management system for **Baraka Solar Manufacturing PLC** (Addis Ababa, Ethiopia), covering the full import cycle from order planning to NBE declaration submission for goods supplied by Central Hub FZCO (Djibouti).

> **Keep this repository private.** `index.html` contains company details, the vendor bank account, TIN numbers, letterhead images and the vendor stamp.

## Modules

| # | Module | What it does |
|---|--------|--------------|
| 1 | **Order plan** | Products with per-container quantity, packages and weights; monthly planned and additional orders; booking; COC before loading; ETA to Djibouti and Ethiopia; delay alerts; per-container links to proforma, bank permit and declaration |
| 2 | **Documents** | Generates the proforma invoice, commercial invoice, packing list and truck waybill (Central Hub design) and the purchase order (Baraka design); PDF download and print version |
| 3 | **FCY & bank permit** | 9-step workflow per proforma: FCY request, insurance, FCY approval, submission of proforma and PO, bank PO approval, chamber documents, DHL to vendor, bank permit |
| 4 | **Declarations & delinquency** | Tracks declarations against the 180-day rule from bank permit approval; days remaining, delinquent alerts, batch submission |

The modules share one database, so data flows between them:

- Proformas can be created directly from order plan containers.
- The bank PO no and date from the FCY workflow fill the commercial invoice and packing list (invoice date = bank PO date + 1 day).
- A bank permit issued in the FCY workflow is linked automatically to that proforma's containers.
- Declarations are linked to containers and bank permits.

## Technology

| Layer | Technology |
|-------|-----------|
| Front end | One self-contained `index.html`: HTML5, CSS, plain JavaScript (no framework, no build step) |
| Fonts | IBM Plex Sans (Google Fonts) |
| PDF | [html2pdf.js 0.10.1](https://cdnjs.com/libraries/html2pdf.js) (html2canvas + jsPDF) from cdnjs |
| Images | Letterheads and stamp embedded as data URIs |
| Back end | Claude artifact runtime: `claude.use("db")` (real-time JSON document store) and `claude.use("downloads")` (file saving) |

Inside `index.html` there are five script blocks: Documents, Order plan, FCY & bank permit, Declarations, and the module switcher.

## Running it

### On claude.ai (current setup)

The system runs as a published Claude artifact. To publish a changed version, open a Claude conversation, provide the updated `index.html`, and ask Claude to publish it to the existing artifact link so the saved data is kept. Staff need Claude accounts in the same Team or Enterprise organization; share the artifact with **Edit** access for data entry and **View** access for managers.

### Elsewhere

Outside claude.ai, `window.claude` does not exist. The page opens and the documents preview works, but saving, loading and PDF download are disabled until the back end is replaced. See [docs/deployment.md](docs/deployment.md).

## Documentation

- [docs/data-model.md](docs/data-model.md): collections and fields
- [docs/workflow.md](docs/workflow.md): business rules built into the system
- [docs/deployment.md](docs/deployment.md): moving to Firebase, PocketBase or your own server
