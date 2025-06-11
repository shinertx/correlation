# PROJECT_BIBLE.md

## Cluster Outlier Catalyst Engine (COCE)
**Asymmetric Alpha from Microcap Pumps. Systematized. Scaled.**

---

## 1. System Vision

Design and deploy a modular, adversarially-aware, ultra-convex alpha engine that captures edge from microcap crypto pumps before contagion spreads.

**Mission:**
Capture 10×–100× trades through early signal detection (social + volume + graph outliers), concentrate capital in <3 assets/day, and aggressively compound edge while containing downside risk.

---

## 2. Core Modules

```
/src/
/data_ingest/
price_loader.py         # OHLCV and volume from CEX + DEX
social_scraper.py       # Telegram, X, Discord pump groups
volume_anomaly.py       # Detect 3σ spikes, rolling volume Z-score

/network_analysis/
corr_matrix.py          # 7/30-day rolling correlation
graph_builder.py        # Pruned graph, MST
clustering.py           # Louvain, centrality calc, Z-score divergence

/signal_engine/
outlier_detector.py     # Detects coins with Z-score > 2.5 from cluster
pump_predictor.py       # NLP + heuristic classifier for social pump intent
forecast_filter.py      # Optional ARIMA/momentum overlay for confirmation

/execution/
trade_manager.py        # Executes, tracks PnL, manages exits
ccxt_router.py          # Unified trade execution across CEX + DEX

/risk_guardrails/
liquidity_limits.py     # Reject trades under volume threshold
max_drawdown.py         # Global drawdown limits, trade freezes

/tests/
test_data_ingest.py
test_signal_engine.py
test_execution.py
test_risk.py

/notebooks/
0_data_prep.ipynb
1_network_outliers.ipynb
2_pump_backtest.ipynb
3_signal_replay.ipynb
4_slippage_simulator.ipynb

/infra/
Dockerfile
.env.example
ci.yml
deploy.sh
```

---

## 3. Alpha Logic & Workflow

### Signal Generation (Run Every 15m–1h)
1. Load OHLCV + volume data
2. Build 7d/30d rolling correlation graph
3. Cluster via Louvain → identify coins with Z-score ≥ 2.5 from cluster center
4. Overlay volume anomaly detection (≥ 3σ)
5. Run pump_predictor (NLP or rule-based)
6. Apply forecast filter (ARIMA, RSI momentum)
7. Filter assets:
   - Market cap < $30M
   - Slippage < 2%
   - Liquidity > $50K/24h

### Execution
- Trade top 1–3 signals only
- Max trade size: $1K–$5K
- Exit via:
  - Trailing % stop
  - Time stop (e.g., 4h after entry)
  - Pump overheat (e.g., price+volume+social delta hits threshold)

---

## 4. Risk & Enforcement

### Hard Guardrails (Must Never Be Violated):
- No trade if signal confidence < 60%
- No position > 5% of 24h volume
- No trade on coins with fewer than 3 venues
- Global max drawdown: 25% rolling 14-day
- Kill-switch on 5+ consecutive false positives or missed exits

### Logging & Auditing:
- All signals, trades, exits must be logged in structured `.jsonl`
- All trades must emit: `timestamp`, `token`, `entry`, `exit`, `PnL`, `signal_confidence`, `exit_type`
- All pump predictions must be testable offline (saved social text + score)

---

## 5. Simulation & Backtest Framework

| Module              | Output                                  |
|---------------------|------------------------------------------|
| `pump_replay.py`    | Reconstructs historical signals from 20+ known pump events (FEG, SHIB, etc.)|
| `execution_sim.py`  | Slippage-adjusted returns vs signal timing|
| `score_metrics.py`  | Precision/Recall of pump_predictor|
| `trade_compounder.py` | Simulates bankroll growth from real + synthetic pump sequences|

✅ Required:
- PnL histogram with ≥ 1% of trades returning ≥ 10×
- Cumulative return ≥ 10× in simulation within <60 trades
- Live precision ≥ 50%, recall ≥ 20%, false positive rate < 10%

---

## 6. Deployment & CI/CD

- CI: GitHub Actions (lint, test, coverage)
- Coverage: 90%+ across all modules
- Secrets: all keys/secrets in `.env` or secret manager (never hardcoded)
- Docker: stateless, non-root, stdout logs
- Health: alerts on exit error, kill-switch activation, drawdown breach
- Onboarding: `setup_env.sh` must validate API access, data, config

---

## 7. Production Milestones

| Milestone          | Completion Criteria                                  |
|--------------------|------------------------------------------------------|
| `signal_validated` | Backtest shows >10 pumps correctly predicted         |
| `execution_live`   | Dry-run trade pipeline can simulate real trades      |
| `infra_ready`      | Docker, CI, logs, secrets validated                  |
| `compounder_tested`| Simulated bankroll >10× in 6 months of test data     |
| `risk_proven`      | Kill-switch and drawdown triggers fire as designed  |

---

## 8. Live KPIs

| Metric                  | Target             |
|--------------------------|--------------------|
| Signal Precision         | ≥ 55%              |
| Pump Hit Rate            | ≥ 1.5% / 30 days   |
| Max Drawdown (30d)       | ≤ 25%              |
| Portfolio Multiple (6mo) | 10×–50×            |
| Trade Win Skew           | ≥ 80/20            |

---

## 9. Summary & Enforcement

This document supersedes all wikis, comments, notebooks, or README files.
No module, signal, or trade is valid unless it aligns to this specification.
No experiment is accepted without reproducibility and log traceability.
All capital at risk must be justified by simulation + signal validation.

---

> **Build once. Aim rare. Compound forever.**
