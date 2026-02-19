# Pine Script Comparison: Main_Midbar_production_1.15.26 vs Main_Midbar_+TQQQ_1.15.26

## Summary

The only difference between the two scripts is the **addition of TQQQ as a seventh ticker**. All existing logic (SPX, SPY, QQQ, SOXL, ES-IV, ES-HV) is unchanged. TQQQ is integrated using the same pattern as the other RTH tickers.

## Detailed Differences

### Strategy Name
- Script 1: `"Main_Midbar_production_1.15.26"`
- Script 2: `"Main_Midbar_+TQQQ_1.15.26"`

### New Input Parameters
- `iv_tqqq` (default 55.00%, max 300.0) added between QQQ and SOXL
- `enable_tqqq` toggle added in Ticker Toggles group

### New request.security() Calls
7 new security requests for TQQQ: daily open, yesterday close, intrabar high/low/close, and previous bar high/low.

### SD Level Calculation
- New variables: `tqqq_open`, `tqqq_prev_close`, `tqqq_range`
- New SD levels: `tqqq_p1/p2/p3`, `tqqq_m1/m2/m3`
- Calculated on `new_rth_session` using TQQQ open + IV-derived range

### Broken Level Tracking
- 6 new broken-level booleans for TQQQ (m1/m2/m3 and p1/p2/p3)
- Reset on `new_rth_session`
- Detection uses `tqqq_close_val` during RTH

### Touch Detection
- Full touch validation for TQQQ (m1/m2/m3 down, p1/p2/p3 up)
- Same pattern as other RTH tickers: requires `is_rth`, not broken, level cross, close confirms reversal

### Aggregated Touch Signals
- `any_sd_touch_up/dn` now includes `(enable_tqqq and tqqq_touch_up/dn)` in RTH branch

### Confluence
- New bar-tracking: `tqqq_sd_dn_bar`, `tqqq_sd_up_bar`
- New scores: `conf_long_tqqq`, `conf_short_tqqq`
- RTH confluence sums 7 tickers instead of 6

### Level Attempt Tracking
- 6 new attempt counters and 6 new previous-outcome strings for TQQQ
- `f_get_attempt()` and `f_get_prev_outcome()` extended with `"TQQQ"` branch

### Watch State Logic
- TQQQ added to confluence list string building
- TQQQ added to watch ticker priority chain (between QQQ and SOXL)

### Trade Data / Entry + Exit Tracking
- TQQQ branch added to attempt-increment on entry
- TQQQ branch added to previous-outcome recording on exit
- `trigger_iv` ternary includes `"TQQQ" ? iv_tqqq`

### Visualization Table
- Table expanded from 11 to 12 rows
- New row 4 shows TQQQ -2SD / +2SD values
- All rows below shift down by one

### Chart Labels
- TQQQ added to label text selection for both touch-down and touch-up labels

## Notes
- TQQQ is checked after QQQ but before SOXL in the priority chain; QQQ takes precedence if both trigger simultaneously.
- The additional 7 `request.security()` calls bring the script closer to Pine Script's ~40 call limit.
- No behavioral changes to existing strategy logic.
