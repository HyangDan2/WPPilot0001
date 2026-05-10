# About Generating Update Program Rule

## Purpose

This document defines the harness rules for an Agentic AI updater that generates static analysis data for the GitHub Pages page:

`analysis/TSE7013/TSE7013.html`

The updater must create timestamped knowledge-map JSON files and maintain a stable `latest.json` pointer so GitHub Pages can render the newest static analysis without a backend server.

## Output Paths

The updater must write files under:

- `analysis/TSE7013/raw_data/{YYYYMMDD_HHMMSS_KST}.json`
- `analysis/TSE7013/latest.json`

`latest.json` must contain only a pointer to the newest raw data file:

```json
{
  "latest": "raw_data/20260510_161157_KST.json"
}
```

## Language Rule

The page supports three languages through top-level buttons:

- `KOR`
- `ENG`
- `JPN`

Every generated human-readable analysis field must provide all three language values using these keys:

- `ko`
- `en`
- `ja`

The updater must not emit only Korean text. If a section cannot be translated confidently, it must still provide a short neutral version in all three languages and mark the limitation in `data_quality.notes`.

## Target Identity Rule

The updater must always verify the target identity before generating analysis:

- TSE `7013` = IHI Corporation
- TSE `7011` = Mitsubishi Heavy Industries
- TSE `7012` = Kawasaki Heavy Industries

If these identities cannot be verified from a trusted source, the updater must stop and mark the run as failed.

## Required JSON Sections

Each raw data JSON must include:

- `schema_version`
- `generated_at`
- `as_of`
- `target`
- `price_snapshot`
- `recent_prices`
- `monthly_prices`
- `financials`
- `segment_sector_map`
- `peer_group`
- `news_events`
- `technical_view`
- `trading_scenarios`
- `sources`
- `disclaimer`

Optional future sections:

- `data_quality`
- `fx_rates`
- `sector_indices`
- `ai_generated_summary`
- `event_study`

## Source Priority

Use sources in this priority order:

1. Company IR pages and official financial statements
2. Exchange or official disclosure systems
3. Established market data pages
4. Reputable news organizations
5. Secondary finance aggregators

For IHI financials, prefer IHI IR documents. For price data, use an auditable public source and record the exact source URL.

## Date Rule

The updater must distinguish:

- `generated_at`: when the JSON was generated
- `as_of.page_date`: the analysis date
- `as_of.market_data_date`: the latest market close used

If the page date falls on a weekend or market holiday, use the latest confirmed trading day and state that explicitly in `as_of.note.ko/en/ja`.

## Price Data Rule

`price_snapshot` must include:

- open
- high
- low
- close
- change
- change_percent
- volume
- source
- date

`recent_prices` should include enough OHLCV points to draw a useful recent chart. If complete daily history is unavailable, the updater may include the latest verified sample and must record the limitation in `data_quality.notes`.

## Financial Rule

The updater must extract:

- revenue
- operating profit
- profit before tax
- profit attributable to owners
- EPS
- ROE
- operating margin
- total assets
- total equity
- equity ratio
- cash flow from operations
- next-year company forecast, if available

All units must be explicit. For IHI, use `JPY millions` for official financial statement values unless the source uses another unit.

## Peer Group Rule

The default peer group must include:

- IHI Corporation `7013.T`
- Mitsubishi Heavy Industries `7011.T`
- Kawasaki Heavy Industries `7012.T`
- Lockheed Martin `LMT`
- Hanwha Aerospace `012450.KS`
- Rheinmetall `RHM.DE`

The updater may add RTX, Northrop Grumman, BAE Systems, Thales, Leonardo, LIG Nex1, Hyundai Rotem, or Komatsu when the analysis objective requires broader comparison.

## Segment Sector Map Rule

The updater must generate `segment_sector_map` to avoid treating IHI as a pure defense stock. IHI should be analyzed as a multi-segment heavy-industry company.

Each `segment_sector_map` item must include:

- `segment_id`
- `segment_name.ko/en/ja`
- `ihi_relevance`
- `ihi_linkage.ko/en/ja`
- `tickers`
- `drivers`
- `risk_factors`
- `price_linkage.ko/en/ja`

Allowed `ihi_relevance` values:

- `direct`
- `direct_adjacent`
- `adjacent`
- `comparison`

The default segment map must include:

- Defense / Space
- Aero Engines / Aviation Components
- Energy / Nuclear / SMR
- Industrial Machinery / Turbomachinery
- Infrastructure / Mobility Systems
- Maritime / Shipbuilding / Special Vessels

Default ticker candidates by segment:

- Defense / Space: `7013.T`, `7011.T`, `7012.T`, `LMT`, `RTX`, `NOC`, `RHM.DE`, `BA.L`, `HO.PA`, `012450.KS`
- Aero Engines / Aviation Components: `7013.T`, `RTX`, `GE`, `SAF.PA`, `RR.L`, `MTX.DE`, `7012.T`, `012450.KS`
- Energy / Nuclear / SMR: `7013.T`, `7011.T`, `6501.T`, `ENR.DE`, `GEV`, `034020.KS`, `BWXT`, `CCJ`
- Industrial Machinery / Turbomachinery: `7013.T`, `7011.T`, `7012.T`, `6361.T`, `6301.T`, `6501.T`, `SIE.DE`, `CAT`
- Infrastructure / Mobility Systems: `7013.T`, `7012.T`, `6501.T`, `6503.T`, `SIE.DE`, `ALO.PA`, `064350.KS`
- Maritime / Shipbuilding / Special Vessels: `7011.T`, `7012.T`, `329180.KS`, `042660.KS`, `010140.KS`, `BA.L`, `LDO.MI`

The updater must explain whether each segment is a direct IHI catalyst, an adjacent macro/industrial comparison, or only a reference sector. This distinction is important because defense news, aero-engine cost news, nuclear/SMR news, and shipbuilding news do not affect IHI with the same intensity.

## News and Price Linkage Rule

Every `news_events` item must include:

- date
- title
- source
- related tickers
- sentiment
- impact horizon
- price linkage
- multilingual interpretation

The updater must classify news as:

- `positive`
- `mixed`
- `negative`
- `neutral`

The price linkage must explain whether the event is:

- already priced in
- a new catalyst
- an execution-risk warning
- a long-term structural support

When possible, connect event dates to nearby price movement. If exact event-study data is unavailable, say so plainly.

## Technical Analysis Rule

The updater must compute or infer:

- support zones
- resistance zones
- trend state
- risk state

Preferred indicators:

- 5-day moving average
- 20-day moving average
- 60-day moving average
- RSI
- volume expansion or contraction

If full calculation data is not available, the updater must use observed price zones and label the result as chart-structure analysis rather than indicator calculation.

## Trading Scenario Rule

The updater must produce three scenarios:

- short term
- medium term
- long term

Each scenario must include:

- entry or observation condition
- invalidation or risk condition
- fundamental or technical basis

Never present scenarios as guaranteed outcomes. Use research wording, not instruction wording.

## GitHub Pages Rule

The updater must assume there is no backend server.

Allowed:

- static HTML
- static CSS
- static JavaScript
- static JSON
- external CDN libraries, if the page already depends on them or explicitly allows them

Not allowed:

- server-only secrets
- private API keys in browser JavaScript
- runtime database writes from the page
- paid API calls directly from public client code

## Failure Handling

If a required data source is unavailable:

1. Keep the previous valid `latest.json` unchanged.
2. Write a failed-run log only if a logging location exists.
3. Do not overwrite a valid raw data file with partial data.
4. Report missing fields in `data_quality.notes` when partial generation is explicitly allowed.

## Auditability Rule

Every numeric or factual claim should map to at least one item in `sources`.

The updater must preserve:

- source title
- source URL
- usage purpose
- data date, when known

## Things the Updater Must Not Do

- It must not confuse IHI `7013` with Mitsubishi Heavy Industries `7011`.
- It must not fabricate prices, earnings, or source names.
- It must not overwrite historical raw data files.
- It must not expose private API keys.
- It must not remove old raw data unless explicitly requested by the user.
- It must not output only one language for user-facing analysis.

## Recommended Run Order

1. Verify target identity.
2. Fetch latest market date and price snapshot.
3. Fetch recent chart data.
4. Fetch latest official financials.
5. Build the business segment sector map.
6. Fetch peer valuation snapshots.
7. Fetch recent global defense/aerospace and adjacent-sector news.
8. Generate multilingual analysis.
9. Validate JSON schema.
10. Write timestamped raw data.
11. Update `latest.json`.
12. Optionally commit the generated files.
