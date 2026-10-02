# Data Integrity Demo

An interactive demonstration of comparing procurement award records with data recorded in BSV transaction outputs. A React frontend loads original and altered JSON fixtures from an Express backend, creates proof transactions through a BRC-100 wallet and displays a comparison.

The backend serves local fixtures. It does not connect to a procurement database or modify a live data source.

## What is included

- Original and altered example award records.
- An interface for switching between the two fixture sets.
- Wallet actions that place complete JSON records in `OP_FALSE OP_RETURN` outputs.
- A comparison view that reads outputs from the wallet's `integrity` basket.

Creating proofs can incur transaction fees on the connected wallet's network. The full records are written to transaction outputs, so use the supplied demonstration data rather than private records.

## Run locally

Use Node.js 22.13 or later in the 22.x release line, and npm. A compatible BRC-100 wallet is required for transaction operations; fixture browsing does not require one.

Start the backend from the repository root:

```sh
cd backend
npm ci
npm run dev
```

The API listens on `http://localhost:3001` and serves these routes:

| Route | Data |
| --- | --- |
| `/api/data/original` | Original fixture. |
| `/api/data/altered` | Altered fixture. |
| `/api/data/records` | First five original records for proof creation. |

`PORT` changes the listening port. `DATA_DIR` can override the fixture directory; otherwise the server reads `backend/src/data/`.

In another terminal, from the repository root:

```sh
cd frontend
npm ci
npm run dev -- --host 127.0.0.1
```

Open `http://localhost:5173`. The checked-in development configuration selects `http://localhost:3001/api`. To change it, set `VITE_API_URL` in `frontend/.env.local`, including the `/api` suffix.

## Current verification limits

Proof creation and comparison currently select different records. The API returns five records for proof creation, while the interface compares the first two returned wallet outputs against altered records three and five. It also relies on wallet output order to identify the intended records.

The code awaits `Transaction.verify()` without checking its boolean result. A non-throwing verification failure does not stop comparison. The current result is therefore a demonstration output, not a reliable integrity verdict.

Resetting the interface clears local view state and reloads the fixtures. It does not remove wallet outputs or reverse blockchain transactions.

## Build and hosting

In `backend/`, run `npm run build`, then `npm start`. Retain the fixture files or supply `DATA_DIR` when packaging the compiled server.

For a frontend build against the local API, run from `frontend/`:

```sh
VITE_API_URL=http://localhost:3001/api npm run build
npm run preview -- --host 127.0.0.1
```

Without that override, `.env.production` selects the hosted API. The frontend's Nginx configuration includes an `/api/` proxy for container hosting. See the [frontend README](frontend/README.md) for configuration details and source links.

No automated test scripts are defined.

## Licence

**Licence declarations: MIT and ISC.** The project documentation identifies MIT, while [backend/package.json](backend/package.json) declares ISC. The [frontend package](frontend/package.json) has no licence declaration. These declarations differ. No standalone licence file is included in this repository.
