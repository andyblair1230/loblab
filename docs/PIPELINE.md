# Pipeline Overview

## 1. Data Acquisition & Normalization
- Download raw Sierra Chart SCID (trades) and depth files.
- Maintain file structure so Sierra Chart can access both raw and TRUE_SYNC versions.
- TRUE_SYNC process runs continuously to reorder events precisely by timestamp for accurate book reconstruction.

## 2. Book Reconstruction
- Parse depth records (COMMAND_ADD/MODIFY/DELETE_*).
- Apply batches in strict sequence to maintain a true, real-time order book.
- Incorporate trade sequence resolution for verification and event ordering.

## 3. Prediction Framework

### 3.1 Execution-Time Predictions (Near-Term)
Purpose: Execute orders at the best possible microsecond-level moment given infrastructure latency.
- Horizons: 250ms, 500ms, 750ms, 1000ms.
- Inputs: live reconstructed order book, trade tape, and latency-adjusted event timing.
- Output: predicted bid/ask levels *at the time the order would actually hit the market*.

### 3.2 Strategy-Time Predictions (Model-Driven)
Purpose: Decide *what* to trade, *how much* to trade, and *when* to exit or adjust.
- Model chooses:
  - Price target(s)
  - Trade duration (could be minutes, hours, or days)
  - Action type: add to position, trim position, reverse direction
  - Instrument(s) and micro/macro contract mix
- No fixed rules for targets or holding times — all determined by model output.

## 4. Order Routing
- Use execution-time predictions to micro-optimally enter/adjust positions decided by the strategy-time model.
- Risk parameters (position size, max exposure, etc.) are placeholders in `cfg/risk.yaml` — actual values will be chosen dynamically by the model.

## 5. Logging & Metrics
- Latency measurement: separate tracking for:
  - Market→receipt latency
  - Code parsing latency
  - Decision→order send latency
  - Order routing/exchange ack latency
- Store all prediction and decision inputs/outputs for post-trade analysis.
