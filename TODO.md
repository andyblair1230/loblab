# LOB Lab – TODO

## Immediate Next Steps
- [ ] Regenerate TRUE_SYNC SCID + depth files on C: drive
- [ ] Place SCID and daily depth (and their TRUE_SYNC versions) into:
      `data/raw/{symbol}/{year}/{contract_id}/`
- [ ] Create `logs/latency/` folder with date subfolder for first test day
- [ ] Add blank CSV headers:
    - `feed_latency.csv`
    - `orders_latency.csv`
- [ ] Verify NTP sync offset < 5 ms

## Latency Measurement Pass
- [ ] Run quiet ETH slice feed logging + 50 micro-orders
- [ ] Run busy RTH slice feed logging + 50 micro-orders
- [ ] Collect and save CSVs for both slices
- [ ] Generate p50/p90/p95/p99 for each latency leg
- [ ] Determine execution horizon bucket (nearest to p95 end-to-end)
- [ ] Save summaries to `logs/latency/YYYY-MM-DD/summary_RTH.json` and `_ETH.json`

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
- [ ] Trade stream verification as parallel check (not in hot path)
- [ ] Log reorder flags if sort performed
- [ ] Track gap handling events and resync timing
- [ ] Implement continuous real-time TRUE_SYNC generator:
    - Reads raw depth + SCID in real time
    - Applies batch ordering, snapshot rebuilds, and trade sequence matching
    - Outputs updated TRUE_SYNC SCID + depth files with precise sequence ordering
    - Logs processing latency per batch for latency budget analysis

## Modeling Prep
- [ ] Build minimal event-stream parser using `cfg/naming.yaml` patterns
- [ ] Integrate session cuts from `cfg/sessions.yaml`
- [ ] Create feature extraction stubs (features computed from live TRUE_SYNC outputs)
- [ ] Link near-term forecast horizon to `cfg/latency.yaml` bucket
- [ ] Draft longer-term forecast heads (alpha horizon)

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
