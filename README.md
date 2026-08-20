# Amadeus Connect — Demo dApp

A small, self-contained browser dApp that shows how to integrate the **Amadeus
wallet** using [`@amadeus-protocol/wallet-sdk`](https://www.npmjs.com/package/@amadeus-protocol/wallet-sdk).
It connects by QR code and drives every request type end-to-end over the encrypted
bridge relay — no browser extension required.

It's built with plain TypeScript + Vite (no framework), so it's easy to read and
copy from.

## What it demonstrates

- **Connect by QR** — pairs with the Amadeus wallet over the bridge (default:
  `https://bridge.ama.one`).
- **Multi-account** — lists every account in the wallet with live balances and
  lets you pick which account signs.
- **Send a transfer** — to any address (or yourself), then submits the signed
  transaction to the node you choose and links the result on the explorer.
- **Sign a contract call** — arbitrary `contract.method` + JSON args.
- **Sign a message** — off-chain "personal sign"; the signature is verified
  in-page with `verifyMessage`.
- **Request an API key** — a KBS bearer token signed by the selected account.
- **Network picker** — Mainnet / Testnet / custom node RPC.

## Run it locally

```sh
bun install      # or: npm install
bun run dev      # prints a http://localhost:5173 URL
```

Then open the URL, click **Connect wallet**, and scan the QR with the Amadeus
wallet (Home → Connect) and approve. The demo talks to the wallet entirely over
the public bridge, so your phone and this page don't need to be on the same
network.

> The wallet only signs transactions — it returns `txPacked`, and the dApp submits
> it to the node selected in the **Network** picker. That keeps the choice of RPC
> with the dApp.

## Build & deploy

It's a static site — build it and host the `dist/` folder anywhere (Vercel,
Netlify, Cloudflare Pages, GitHub Pages, S3, …):

```sh
bun run build    # outputs to dist/
bun run preview  # preview the production build locally
```

No server or environment variables are required. The bridge relay and node RPC
are chosen at runtime in the UI (and default to Amadeus mainnet).

## How it works

1. `createConnectRequest()` builds a connect URI (rendered as the QR) and a
   pending handshake keypair.
2. The wallet scans it, approves, and returns a sealed connect response over the
   bridge; `completeConnect()` derives the shared session key.
3. Each action seals a `WalletRequest` (optionally naming which `account` signs)
   and sends it over the bridge; the wallet prompts the user, signs, and returns a
   sealed `WalletResponse`.
4. For transactions, the demo submits the returned `txPacked` with
   `submitTransaction()`.

See [`src/main.ts`](src/main.ts) — it's ~450 lines and commented throughout.

## License

MIT
