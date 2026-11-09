# TokenSaleApp

A React + Vite web app for a token presale (ELO token): connect a wallet, follow the presale phases, view balances, transactions and NFTs, swap, mint and stake. It also contains the Solidity source for the token and presale contracts, plus a Truffle workspace for local contract development.

## Status

| Area | State |
| --- | --- |
| Frontend builds and runs (`npm start`, `npm run build`) | ✅ Works |
| Wallet connection (MetaMask / injected, WalletConnect v1) | ⚠️ Injected wallets work. WalletConnect v1 has been shut down upstream |
| Balances, transactions, NFTs, swap (Moralis) | ❌ Broken. The hardcoded Moralis server (`*.usemoralis.com`) was retired by Moralis in 2022 |
| Truffle sample contracts (MetaCoin) | ✅ Standard Truffle example |

## Tech stack

- **Frontend:** React 17, Vite 6 (SWC), React Router 5, MUI 5, Ant Design 4, MDBReact
- **Web3:** ethers 5, web3.js, `@web3-react` (injected + WalletConnect connectors), Moralis SDK v0 / `react-moralis`
- **Contracts:** Solidity (`contracts/`), Truffle + Ganache (`Truffle/`)

## Getting started

Requirements: Node.js 18+ (tested with Node 24) and npm.

```bash
git clone https://github.com/pnyishar/TokenSaleApp.git
cd TokenSaleApp
npm install --ignore-scripts
npm rebuild esbuild @swc/core
npm start
```

`--ignore-scripts` stops dependency install scripts from running. The `rebuild` step then runs setup for just the two build tools Vite needs. The many `ERESOLVE overriding peer dependency` warnings during install are expected: older libraries such as `mdbreact` still declare React 16 as a peer.

The dev server opens the app in your browser automatically (default http://localhost:5173).

### Scripts

| Command | Description |
| --- | --- |
| `npm start` | Start the Vite dev server |
| `npm run build` | Production build into `dist/` |
| `npm run serve` | Preview the production build |
| `npm run devchain` | Start a local Ganache chain on port 7545 (requires `ganache-cli` installed globally) |
| `npm run deploy` | Compile and migrate the Truffle contracts to the local chain, then copy the ABI to `src/contracts/contractInfo.json` (requires `truffle` installed globally) |

## Configuration

Copy `.env.default` to `.env` to override:

| Variable | Purpose |
| --- | --- |
| `VITE_DCL_DEFAULT_ENV` | Environment name exposed to the app (`dev` by default) |
| `VITE_BASE_URL` | Base path used for production builds (`/` by default) |

The Moralis app ID and server URL are hardcoded in [`src/index.jsx`](src/index.jsx). To restore the Moralis-backed features, point these at a working backend or migrate those components to the current Moralis Web3 API (or another indexer).

Contract addresses live in [`src/helpers/ContractAddress.js`](src/helpers/ContractAddress.js) and [`src/components/shared/Contracts.jsx`](src/components/shared/Contracts.jsx). RPC endpoints and chain metadata are in [`src/helpers/networks.js`](src/helpers/networks.js).

## App routes

| Path | Page |
| --- | --- |
| `/` | Home |
| `/about` | About / roadmap |
| `/pre-sale` | Presale phases and purchase |
| `/swap` | Token swap (1inch via Moralis) |
| `/erc20balance` | ERC-20 balances |
| `/transactions` | Native transactions |
| `/nfts` | NFT balances |
| `/gallery` | Gallery |
| `/mint` | Mint |
| `/stake` | Stake |
| `/onramp` | Fiat on-ramp |

## Project structure

```
├── contracts/            Solidity sources for the ELO token and presale
├── Truffle/              Truffle workspace (MetaCoin sample, migrations, tests, scripts)
├── public/               Static assets
├── src/
│   ├── components/       UI components, grouped by feature
│   ├── containers/       Route-level pages
│   ├── contracts/        ABIs used by the frontend
│   ├── helpers/          Network config, contract addresses, formatters
│   ├── hooks/            Custom React hooks
│   ├── providers/        Context providers (Moralis dApp provider)
│   ├── App.jsx           Routes
│   └── index.jsx         Entry point and providers
├── index.html            Vite HTML entry
└── vite.config.js        Vite config, including Node polyfills for web3 libraries
```

## Notes on the Vite config

Web3 libraries expect Node globals. `vite.config.js` polyfills `Buffer` and `process` for dev pre-bundling, and Node built-ins for production builds. If you see errors such as `Cannot read properties of undefined (reading 'slice')` from `readable-stream`, make sure the `process` polyfill is enabled. Then clear the cache with `rm -rf node_modules/.vite` and restart.

## Smart contracts

- `contracts/elotoken.sol`: ERC-20 token (Solidity ^0.8.9)
- `contracts/elotoken_presale.sol`: presale contract (Solidity ^0.6.12)

These are provided as source only and are not wired into the Truffle migrations. Review and audit them before deploying anywhere.

For the local Truffle example:

```bash
npm run devchain   # terminal 1
npm run deploy     # terminal 2
```

## License

The Truffle sample is MIT-licensed (see `Truffle/LICENSE`). No license is declared for the rest of the project.
