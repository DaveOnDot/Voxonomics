# Voxonomics

Polkadot-first framework for cross-chain valuation. Voxonomics computes a Value Trusted Score (VTS) and Macro Alignment Index from five core metrics with DAO-governed weights—transparent, auditable, reproducible, and built for honest, comparable network-to-network analysis.


> **Polkadot‑first, open framework for fair, transparent, cross‑chain valuation.**  
> Headline measures: **Value Trusted Score (VTS)** and **Macro Alignment Index (MAI)** — derived from **five core metrics** with DAO‑governed weights.

---

## 1) Purpose & Principles

Voxonomics exists to make network‑to‑network comparisons honest, reproducible, and civilised. We publish the method, the data sources, and the weights; we version every change; and we welcome scrutiny. In short: **clarity over mystique**.

**Principles**
- **Open methodology** — all formulae, normalisations, and weights are public and versioned.
- **On‑chain first** — prefer canonical chain data; declare any third‑party sources and caveats.
- **DAO‑aligned** — metric weights are ratified by governance and applied via PRs that reference the proposal.
- **Reproducible by design** — deterministic calculations with fixed precision and test vectors.
- **Polkadot first, multi‑chain always** — begin with Polkadot; maintain parity for EVM and other L1s.

---

## 2) Headline Measures

- **Value Trusted Score (VTS):** A bounded score **[0, 1]** aggregating the five core metrics via normalised weights. Intended for like‑for‑like comparison across networks.
- **Macro Alignment Index (MAI):** Tracks a network’s alignment with macro‑economic soundness (sound issuance, sustainability of fees, credible neutrality, resilience), as defined in the framework and updated by governance.

---

## 3) The Five Core Metrics (canonical)

Each core metric is a weighted sum of named sub‑metrics. Sub‑metric definitions, transforms, and bounds are specified in `/docs/metrics.md`. The DAO controls weights in `governance/weights.json`.

1) **PoV — Proof of Value**  
   *What it asks:* does the network demonstrably create and retain economic value for participants?  
   *Illustrative sub‑metrics:* sustainable fee revenue, cost‑to‑secure vs utilisation, long‑term value accrual ratio, issuance discipline score, economic throughput quality.

2) **OPI — On‑chain Participation Index**  
   *What it asks:* is participation broad, active, and credibly decentralised?  
   *Illustrative sub‑metrics:* unique active addresses (sybil‑adjusted), validator/staker dispersion (Gini/Herfindahl), governance turnout & proposer diversity, developer activity (on‑chain/verified), retention/tenure curves.

3) **DLI — Decentralised Liquidity Index**  
   *What it asks:* is liquidity deep, distributed, and resistant to capture?  
   *Illustrative sub‑metrics:* AMM and order‑book depth at common ticks, cross‑venue concentration, stablecoin quality mix, bridge dependency risk, slippage at reference sizes, time‑weighted depth stability.

4) **PTI — Protocol Transparency Index**  
   *What it asks:* are monetary, technical, and governance processes transparent and auditable?  
   *Illustrative sub‑metrics:* public RPC/indexing parity, open‑source coverage & licence health, audit trail & incident disclosure, on‑chain treasury transparency, parameter change latency & notice.

5) **RWAI — Real‑World Asset Index**  
   *What it asks:* how robustly are real‑world assets integrated and attested on‑chain?  
   *Illustrative sub‑metrics:* oracle diversity & attestations, asset registry clarity, legal wrapper soundness, settlement finality to off‑chain, RWA protocol concentration risk, redemption latency/cost.

> **Note:** The above sub‑metrics are examples; the canonical list lives in `/docs/metrics.md` and is governed by DAO proposal. Every sub‑metric has: *definition*, *source(s)*, *transform*, *bounds*, *missing‑data policy*, and *test vectors*.

---

## 4) Architecture (bird’s‑eye)

- **FastAPI service** — exposes `/vts`, `/metrics`, and health endpoints (OpenAPI at `/docs`).  
- **`services/clients.py`** — typed clients for chain & indexer APIs (e.g., Polkadot RPC/OnFinality, Subscan, DefiLlama, others as approved).  
- **`services/metrics.py`** — pure functions that compute sub‑metrics and aggregate to VTS/MAI.  
- **`governance/weights.json`** — canonical, DAO‑approved weights (versioned; machine‑readable schema below).  
- **Docs** — MkDocs site: architecture, metrics, governance, RFCs.

---

## 5) Quick Start

### Prerequisites
- Python **3.11+**  
- (Optional) Node 18+ for docs; Docker if you prefer containers

### Run the API
```bash
# Linux/macOS
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
uvicorn vox_metrics_api:app --reload --port 8000
# Open http://127.0.0.1:8000/docs
```

```powershell
# Windows (PowerShell)
python -m venv .venv
.venv\Scripts\Activate.ps1
pip install -r requirements.txt
uvicorn vox_metrics_api:app --reload --port 8000
```

### Configure
Create `.env` from the example and fill keys (never commit secrets):
```
SUBSCAN_API_KEY=
ONFINALITY_RPC=https://polkadot.api.onfinality.io/public
DEFILLAMA_BASE=https://api.llama.fi
POLKASSEMBLY_BASE=https://polkadot.polkassembly.io/api/v1
EVM_RPC_URL=
```

---

## 6) API Sketch

```
GET /healthz
GET /metrics                  # list core + sub‑metrics
GET /vts?network=polkadot     # → { network, vts, coverage }
```

> Expand with `/metric/{name}` and `/weights` once stable.

---

## 7) Weights File (machine‑readable)

`governance/weights.json`:
```json
{
  "version": 1,
  "as_of": "DAO‑proposal‑ref",
  "core_metrics": {
    "PoV":  { "weight": 0.20, "subs": { "sustainable_fee_revenue": 0.30, "issuance_discipline": 0.20 } },
    "OPI":  { "weight": 0.20, "subs": { "governance_turnout": 0.25, "validator_dispersion": 0.25 } },
    "DLI":  { "weight": 0.20, "subs": { "amm_depth": 0.30, "venue_concentration": 0.20 } },
    "PTI":  { "weight": 0.20, "subs": { "audit_trail": 0.25, "treasury_transparency": 0.25 } },
    "RWAI": { "weight": 0.20, "subs": { "oracle_diversity": 0.30, "legal_wrapper_soundness": 0.20 } }
  }
}
```

**Rules**
- Core weights must sum to **1.0**; sub‑weights per metric must sum to **1.0**.  
- Changes land only via PRs that reference the approved DAO proposal (hash/URL) and include an *impact report* (before/after).

---

## 8) Data & Provenance

- **Primary**: on‑chain RPCs and first‑party indexers (Polkadot/Substrate; EVM where relevant).  
- **Secondary**: public aggregators — clearly cited in code and docs.  
- **Determinism**: fixed precision, explicit rounding, stable sorting, and a missing‑data policy (*drop*, *carry*, or *flag*).

---

## 9) Testing

```bash
pytest -q
```
- Provide deterministic test vectors for each sub‑metric and the VTS/MAI aggregators.  
- CI must pass on every PR (lint, type check, unit tests).

---

## 10) Governance

- Routine: lazy consensus via normal review.  
- Significant: **RFC** in `/rfcs`, community discussion, DAO vote.  
- Execution: maintainers merge PR updating `governance/weights.json` and publish release notes.  
- Transparency: every metric and weight has an audit trail (who/what/why/when).

---

## 11) Contributing

We welcome engineers, quants, and governors. Please read: `CODE_OF_CONDUCT.md`, `CONTRIBUTING.md`, and `SECURITY.md`.  
**Good first issues** are labelled and come with pointers and test stubs. Conventional Commits are encouraged.

---

## 12) Roadmap (abridged)

- **M1** — Stable VTS/MAI schema + Polkadot baseline
- **M2** — EVM parity + richer sub‑metrics
- **M3** — DAO‑ratified weights live + public dashboard

Full detail in `ROADMAP.md`.

---

## 13) Licence & Marks

- Code under **MIT** (or **Apache‑2.0** if you switch the `LICENSE` file).  
- The **Voxonomics** name and logos may be protected; see `TRADEMARKS.md` (use truthfully; do not imply endorsement).

---

## 14) Community

- GitHub Issues/Discussions (and your Discord/Matrix if applicable).  
- Release notes and CHANGELOG announce material changes.  
- Please keep contributions **punctual, informative, and clear**; it lifts all boats.
