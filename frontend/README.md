# Data Integrity Frontend

An interactive React demonstration of changes to procurement award data. It loads original and altered JSON fixtures from the backend and uses a BSV wallet to record data in transaction outputs for comparison.

See the [project overview](../README.md) for the wider application.

## Run locally

Use Node.js 22.13 or later in the 22.x release line, and npm. From the repository root:

```sh
cd frontend
npm ci
npm run dev -- --host 127.0.0.1
```

Open the URL printed by Vite, normally `http://localhost:5173`.

The frontend expects the fixture API at `http://localhost:3001/api`, as configured in `.env.development`. Start it in another terminal, from the repository root:

```sh
cd backend
npm ci
npm run dev
```

The backend reads the JSON files in `backend/src/data/`; no database is required. To use another API, set `VITE_API_URL` in `frontend/.env.local`, including the `/api` suffix.

A compatible BRC-100 wallet is required to create and inspect proof transactions. Viewing fixtures and switching to altered data use ordinary HTTP requests.

## What the demonstration does

- Loads original award records into two comparison tabs.
- Simulates tampering by loading a second fixture from the API.
- Requests a wallet transaction containing JSON records in `OP_FALSE OP_RETURN` outputs.
- Reads outputs from the wallet's `integrity` basket and compares stored records with altered records.

Creating proofs sends the complete selected records to transaction outputs, not just hashes. It can incur transaction fees on the connected wallet's network. Use the supplied demonstration data.

## Current limitations

The proof creation and comparison paths are inconsistent. The backend returns the first five records for proof creation, while the frontend compares only the first two returned wallet outputs with altered records three and five. It also assumes wallet output order identifies the intended records.

The comparison awaits `Transaction.verify()` but does not check its boolean return value. A non-throwing verification failure does not stop record comparison. The displayed result should therefore be treated as a demonstration, not a reliable transaction or data-integrity verdict.

Resetting the interface clears React state and reloads fixtures. It does not remove previously created wallet outputs or reverse transactions.

## Build

For a local production build, explicitly select the local API:

```sh
VITE_API_URL=http://localhost:3001/api npm run build
npm run preview -- --host 127.0.0.1
```

The build type-checks the application and writes `dist/`. Without the override, `.env.production` selects the hosted API. Changing an environment variable after building does not update the bundle.

`npm run lint` is available. No frontend test script is defined.

## Source guide

- [DataIntegrityDemo.tsx](src/components/DataIntegrityDemo.tsx): fixture loading, wallet actions and comparisons.
- [WalletContext.tsx](src/context/WalletContext.tsx): wallet client setup.
- [Backend routes](../backend/src/server.ts): original, altered and proof-record endpoints.
- [nginx.conf](nginx.conf): static hosting and an `/api/` proxy for container deployment.

## Licence

This frontend's [package.json](package.json) has no licence declaration. The project documentation identifies **MIT**, while the backend package declares **ISC**. See the [repository licence section](../README.md#licence) for these differing declarations. No standalone licence file is included in this repository.
