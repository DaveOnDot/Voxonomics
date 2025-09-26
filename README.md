# Voxonomics

Polkadot-first framework for cross-chain valuation. Voxonomics computes a Value Trusted Score (VTS) and Macro Alignment Index from five core metrics with DAO-governed weights—transparent, auditable, reproducible, and built for honest, comparable network-to-network analysis.


# Voxonomics

> **Polkadot‑first, open framework for fair, transparent, cross‑chain valuation.**  
> Headline measures: **Value Trusted Score (VTS)** and **Macro Alignment Index (MAI)** — derived from **five core metrics** (**PoV, OPI, DLI, PTI, RWAI**) with DAO‑governed weights.

This README mirrors the **current API shape and environment** used in the latest scripts:
- `GET /metrics/vts?para=<network>&decimals=<n>`
- `GET /metrics` (lists core + sub‑metrics)
- `GET /healthz`

If you later refactor to `GET /vts?network=...`, update the examples accordingly.

---

## 1) Quick Start

### Requirements
- Python **3.11+**
- (Optional) Node 18+ for docs; Docker if you prefer containers.

### Install & Run
```bash
python -m venv .venv && source .venv/bin/activate   # Windows: .venv\Scripts\Activate.ps1
pip install -r requirements.txt
uvicorn vox_metrics_api:app --reload --port 8000
# Open http://127.0.0.1:8000/docs
```

---

## 2) Configuration (.env)

Copy `.env.example` to `.env` and fill values. **Never commit secrets.**
```bash
cp .env.example .env
```

**Variables in use (current code paths):**
```
# Core
SUBSCAN_API_KEY=
SUBSCAN_BASE=https://polkadot.api.subscan.io/api
ONFINALITY_RPC=https://polkadot.api.onfinality.io/public
POLKASSEMBLY_BASE=https://polkadot.polkassembly.io/api/v1
DEFILLAMA_BASE=https://api.llama.fi
HYDRADX_BASE=https://api.hydradx.io/graphql

# EVM & fallbacks (used by latest error logs mentioning LAVA/POKT/BLAST)
EVM_RPC_URL=
LAVA_RPC=
POKT_RPC=
BLAST_RPC=
BLAST_API_KEY=

# Service behaviour
REQUEST_TIMEOUT_SECONDS=8
LOG_LEVEL=INFO
CORS_ORIGINS=*
```

Notes:
- **SUBSCAN_API_KEY** must be valid for `scan/chain` health checks.
- **EVM_RPC_URL** is required when computing EVM‑sourced sub‑metrics.
- Fallbacks (**LAVA_RPC/POKT_RPC/BLAST_RPC**) are optional; the code should try them in order if present.

---

## 3) API Usage (current)

```bash
# List metrics
curl "$BASE/metrics"

# Compute VTS (polkadot) with 9 decimals
curl "$BASE/metrics/vts?para=polkadot&decimals=9"

# Health
curl "$BASE/healthz"
```

**Networks (`para`):** `polkadot`, `kusama`, or chain labels your code recognises. Keep labels consistent with your clients and weight files.

---

## 4) Metrics Overview (canonical 5)

- **PoV — Proof of Value:** sustainable value accrual, issuance discipline, throughput quality.
- **OPI — On‑chain Participation Index:** active addresses (sybil‑adjusted), validator/staker dispersion, governance turnout, dev activity, retention.
- **DLI — Decentralised Liquidity Index:** depth at reference sizes, venue concentration, stablecoin mix quality, bridge risk, slippage.
- **PTI — Protocol Transparency Index:** open‑source coverage, audit trail, incident disclosure, treasury transparency, parameter change latency.
- **RWAI — Real‑World Asset Index:** oracle diversity/attestations, asset registry clarity, legal wrapper soundness, settlement finality, redemption latency/cost.

The definitive sub‑metrics, transforms, and bounds live in `docs/metrics.md` and are governed by DAO proposals.

---

## 5) Governance & Weights

- Canonical weights in `governance/weights.json` (core weights sum to **1.0**; per‑metric sub‑weights sum to **1.0**).  
- Changes land via PRs linking the approved DAO proposal (hash/URL) and an impact analysis.

---

## 6) Troubleshooting (based on latest logs)

- **`subscan_failed` / HTTPStatusError** → Check `SUBSCAN_API_KEY`, `SUBSCAN_BASE`, and network egress. Try increasing `REQUEST_TIMEOUT_SECONDS`.
- **`No working EVM RPC among LAVA/POKT/BLAST/EVM_RPC_URL`** → Provide at least one working endpoint (start with `EVM_RPC_URL`). Verify chainId by a simple `eth_chainId` call.
- **CORS/browser issues** → Set `CORS_ORIGINS=*` (dev only), or list your domains.
- **422 / validation** → Ensure `para` and `decimals` are supplied where required.

---

## 7) Dev Scripts

A sample warm‑up script is provided at `scripts/warm.ps1` and pings `/metrics/vts?para=...&decimals=9` across networks with logs.

---

## 8) Contributing

We welcome engineers, quants, and governors. Read `CONTRIBUTING.md`, `CODE_OF_CONDUCT.md`, and `SECURITY.md`. Use Conventional Commits and keep PRs small with tests.

---

## 9) Licence & Brand

Code under **MIT** (or **Apache‑2.0** if you opt to switch the `LICENSE`). The **Voxonomics** name/marks may be protected—see `TRADEMARKS.md`. Use truthfully; do not imply endorsement.

---

## 10) Punctuality & Clarity

Kindly keep issues and commits **punctual, informative, and clear**; it greatly smooths collaboration.
