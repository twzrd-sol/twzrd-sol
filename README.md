# TWZRD

**Buyer-side defense for agents that pay over x402.** Everything here runs before your agent's signer: it checks the price, the recipient and the seller, and it keeps retries from signing past your budget. A refusal costs nothing, because the signer never runs.

## Install

| Package | What it does | Install |
|---|---|---|
| [`twzrd-x402-gate`](https://www.npmjs.com/package/twzrd-x402-gate) | `createBuyerFetch`: per-call price cap, recipient allowlist, free seller preflight, and a durable cross-process spend ledger in which a signed payment stays counted until it settles, so seller retries cannot sign past your hourly budget. Base and Solana USDC. | `npm i twzrd-x402-gate@0.28.1` |
| [`agentcash-seatbelt`](https://www.npmjs.com/package/agentcash-seatbelt) | Zero-dependency fetch preload for AgentCash and other x402/MPP clients: blocks cross-rail duplicate payments, enforces spend caps, verifies Outbid-Receipt. | `npm i agentcash-seatbelt@0.3.0` |
| [`@twzrd1111/mandate`](https://www.npmjs.com/package/@twzrd1111/mandate) | Signed spend mandates, plus a loopback sidecar that enforces them (MCP proxy, HTTP proxy, control API). | `npm i @twzrd1111/mandate@0.1.1` |
| [`outbid-receipt-verify`](https://www.npmjs.com/package/outbid-receipt-verify) | Offline check that a paid HTTP answer is what the money bought (Outbid-Receipt v1: request, payment and response bytes). | `npm i outbid-receipt-verify@0.1.0` |
| [`twzrd-mcp-server`](https://www.npmjs.com/package/twzrd-mcp-server) / [`twzrd-mcp`](https://pypi.org/project/twzrd-mcp/) | MCP tools for seller checks before an agent pays. | `npm i twzrd-mcp-server@0.6.7` · `pip install twzrd-mcp==0.2.5` |

## Three lines in front of your signer

```js
import { createBuyerFetch } from "twzrd-x402-gate";
const pay = createBuyerFetch({ client, maxPricePerCall: "0.01", allowedRecipients: [SELLER_PAYTO] });
const res = await pay(url); // over the cap or wrong payTo: BuyerFetchBlockedError, signer never runs
```

## Live services

- **[intel.twzrd.xyz](https://intel.twzrd.xyz/skill.md)**: free, read-only seller preflight (`allow | warn | block`) before an agent pays an x402 service.
- **[outbid.sh](https://outbid.sh/skill.md)**: OpenAI-compatible inference. A paid call is about $0.001 (1000 atomic USDC). Settled Base-paid calls carry an `Outbid-Receipt` v1 signed with the keys at [`/.well-known/outbid-receipt-keys.json`](https://outbid.sh/.well-known/outbid-receipt-keys.json) ([spec](https://github.com/twzrd-sol/outbid-receipt/blob/main/SPEC.md); verify with `outbid-receipt-verify`).

## Source

- [twzrd-trust](https://github.com/twzrd-sol/twzrd-trust): gate, mandate, MCP server, verifiers
- [outbid-receipt](https://github.com/twzrd-sol/outbid-receipt): Outbid-Receipt v1 [spec](https://github.com/twzrd-sol/outbid-receipt/blob/main/SPEC.md), reference verifier, test vectors
- [twzrd-receipt-verifier](https://github.com/twzrd-sol/twzrd-receipt-verifier): offline verifier for TWZRD intel receipts (npm + PyPI)

Buyer-side duplicate-settlement measurements on Base mainnet: [x402#4367](https://github.com/x402-foundation/x402/issues/4367#issuecomment-7144909920).

Most repositories on this profile are forks kept for upstream work. The repos listed above are the ones we maintain.
