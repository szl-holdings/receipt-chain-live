# receipt-chain-live

**Live SHA3-256 Khipu hash-chain verifier — re-checked IN YOUR BROWSER, not merely claimed by a server.**

A static, self-contained HTML page (0 runtime CDN) that fetches the **live a11oy receipt lake**
and **recomputes every receipt's `chain_hash` locally**, then confirms each `prev_hash` links to
the previous `chain_hash`. Green means the governance receipt chain is intact *as measured on your
own machine*.

- **Live Space:** https://huggingface.co/spaces/SZLHOLDINGS/receipt-chain-live
- **Status:** ROADMAP → **LIVE** (the page and the lake it reads are live today; the lake is the
  a11oy Space's `/api/lake/v1` endpoint, so if that Space is asleep the page honestly shows
  **UNAVAILABLE** until it wakes).

## What it measures

The ledger ([`szl_lake_store.py`](https://github.com/szl-holdings/a11oy/blob/main/szl_lake_store.py))
commits, per organ, a hash chain where

```
chain_hash = SHA3-256( canonical_json({prev_hash, receipt_id, organ, ts, chain_index}) )
canonical_json = json.dumps(sort_keys=True, separators=(",",":"), ensure_ascii=True)
```

Every field needed for that recomputation is exposed at the receipt-envelope top level, so the
browser reproduces the exact bytes and hash with a **pinned, vendored `js-sha3` 0.8.0** (WebCrypto
has no SHA-3). The page checks, per receipt:

| Check | Label |
|---|---|
| `SHA3-256(canonical{…}) == chain_hash` | **MEASURED** — recomputed in your browser |
| `prev_hash == previous receipt's chain_hash` (chain linkage) | **MEASURED** |
| recomputed head == the lake's reported `chain_head` | **MEASURED** |
| DSSE ECDSA-P256 signatures | **REPORTED** — *not* re-verified here (see below) |
| energy (`{"label":"UNAVAILABLE"}`) | **REPORTED** pass-through — never fabricated |

## Honest scope

This verifier proves **hash-chain integrity** in-browser. It does **not** verify each receipt's
**DSSE ECDSA-P256 signature** — that is the end-to-end cookbook recipe
[“Verify a receipt end-to-end”](https://github.com/szl-holdings/a11oy/blob/main/docs/cookbook/recipes/01-verify-a-receipt-end-to-end.md),
which validates against the org [`cosign.pub`](https://github.com/szl-holdings/.github/blob/main/cosign.pub).
The Khipu chain is **Conjecture 2 (advisory BFT), not a proven theorem**; **Λ = Conjecture 1**
(advisory, never proven, trust ceiling 0.97). Energy is null unless a real NVML reading exists.

## Deploy

Hugging Face **static** Space — the root `index.html` is served as-is. Update the live Space by
committing `index.html` + `README.md` to the Space repo (or mirror this GitHub repo). The page
reads the lake cross-origin; the lake reflects CORS for any origin.

## Estate

Part of the SZL Holdings estate: [a-11-oy.com](https://a-11-oy.com) ·
[a11oy (flagship + lake)](https://github.com/szl-holdings/a11oy) ·
[szl-lake](https://github.com/szl-holdings/szl-lake) ·
[🤗 SZLHOLDINGS](https://huggingface.co/SZLHOLDINGS) · Λ = Conjecture 1.

## License

[Apache-2.0](LICENSE) — consistent with the SZL Holdings estate convention (a11oy, killinchu,
anatomy, energy-attest-holo are all Apache-2.0).
