# LOB Lab – TODO

## Immediate Next Steps
- [ ] Regenerate TRUE_SYNC SCID + depth files on C: drive
- [ ] Place SCID and daily depth (and their TRUE_SYNC versions) into:
      `data/raw/{symbol}/{year}/{contract_id}/`
- [ ] Create `logs/latency/` folder with date subfolder for first test day
- [ ] Add blank CSV headers:
    - `feed_latency.csv`
    - `orders_latency.csv`
    - `sync_latency.csv`          # write + fsync timings
- [ ] Verify NTP sync offset < 5 ms

## Latency Measurement Pass (minimal, pre-model)
- [ ] Run quiet ETH slice feed logging
- [ ] Run busy RTH slice feed logging
- [ ] Collect CSVs for both slices
- [ ] Compute p50/p90/p95/p99 for:
      data, parse, state, **sync write**, **fsync**, pipeline, pipeline+sync
- [ ] Record snapshot stats (every ~10m), batches per ms, reorder rate
- [ ] Pick provisional execution bucket (until order-path timing is added)

## Intake Step (Step 3)
- [ ] Finalize absolute price lattice bounds per session from raw data
- [ ] Fill out one `logs/ingest/TEMPLATE.json` record for first session
- [ ] QA row counts, SHA256, min/max timestamps for SCID, depth, and TRUE_SYNC
- [ ] Commit config and ingest record (no raw data)

## SC Depth Parsing – Live & Offline
- [ ] Instrument batch-level parse timing:
    - batch_start_mon / batch_end_mon
    - records_in_batch, had_clear_book, end_of_batch_seen
    - bid_count / ask_count per batch
    - same_ms_batch_index
- [ ] Time snapshot rebuild separately on COMMAND_CLEAR_BOOK
- [ ] Apply correct order inside batch: bids desc, then asks asc
- [ ] Trade stream verification as parallel check (off the hot path, capped)
- [ ] Log reorder flags if sort performed
- [ ] Track gap handling events and resync timing
- [ ] Implement continuous real-time TRUE_SYNC generator:
    - Reads raw depth + SCID in real time
    - Applies batch ordering, snapshot rebuilds, and trade sequence matching
    - Outputs updated TRUE_SYNC SCID + depth with precise sequence ordering
    - Logs processing latency per batch for latency budget analysis
    - **Logs sync latency**: write_start/end, fsync_start/end, rotate_start/end

## Modeling Prep
- [ ] Build minimal event-stream parser using `cfg/naming.yaml`
- [ ] Integrate session cuts from `cfg/sessions.yaml`
- [ ] Create feature extraction stubs (features computed from live TRUE_SYNC outputs)
- [ ] Link near-term forecast horizon to `cfg/latency.yaml` bucket
- [ ] Draft longer-term forecast heads (alpha horizon)

## Execution Buckets (multi-horizon)
- [ ] Implement multi-horizon prediction framework
    - Horizons: **250 ms, 500 ms, 750 ms, 1000 ms**
    - Output forecasts for each horizon per incoming update
    - Simulate fill price at each horizon using reconstructed book state
    - Track PnL, hit rate, and slippage **per horizon**
    - Backtest across horizons to find optimal execution window by regime
    - In live mode, select the horizon closest to measured end-to-end latency
    - Log chosen horizon for every decision for later replays

## Risk & Control
- [ ] Create `cfg/risk.yaml` with daily_loss_limit_pct, trade_loss_limit_ticks, max_concurrent_orders
- [ ] Wire risk kill-switch to latency p99 breach from `cfg/latency.yaml`

---

# BLOCKED
- [ ] Latency measurement pass — blocked until first TRUE_SYNC set is regenerated on C: drive
- [ ] Step 3 (intake) — blocked until raw/TRUE_SYNC data present in `data/raw/`

---

# DONE
- [x] Install new GPU — 2025-08-11
- [x] Complete Step 1 (Git repo, .gitignore, folder layout) — 2025-08-11
- [x] Complete Step 2 (configs, naming, ingest template) — 2025-08-11
- [x] Create `cfg/latency.yaml` with thresholds and bucket definitions — 2025-08-11
