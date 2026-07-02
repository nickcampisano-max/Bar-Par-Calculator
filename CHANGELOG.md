# Bar Par Calculator — Changelog

All notable changes to this project are documented here.
Format: newest entries at the top.

---

## [2026-07-02] — Spirits Tab Fix + Prep/Spirits Split

### Fixed
- **Spirits tab blank when sales loaded** — removed `|| true` from the spirit-item filter so only spirits with actual usage appear (previously all 60+ items rendered as "—" rows, looking blank). If sales are loaded but no spirit usage is calculated (e.g. sales saved before spirits were added to recipes), a clear hint message now explains why and suggests re-uploading the CSV.
- **Spirits appearing on Prep Pars tab** — added `Spirit` and `Liqueur` category filter to `renderPars()` so juices/syrups/batches and spirits are now fully separated across tabs.

### Changed
- Tab 2 · Prep Pars now shows only: Fresh Juice, Syrup, Purée, Batch / Cordial, Other Prep
- Tab 3 · Spirits & Ordering shows only: Spirit, Liqueur — and only rows with usage (hides zero-use items)

---

## [2026-06-30] — Spirits & Ordering Tab

### Added
- **Tab 5 · Spirits & Ordering** — new tab showing theoretical spirit/liqueur usage based on sales data
- 60+ new PREP_ITEMS across Spirit and Liqueur categories (all 750ml): Vodka, Gin/Shochu, Rum, Whiskey, Agave, Pisco/Cognac/Other, Liqueurs & Fortified
- Every drink in DRINKS array now has raw spirit oz per serve alongside existing prep item mappings
- Batched cocktails track BOTH the batch bottle pour AND the raw spirits that go into the batch — batch pour drives bartender prep pars; raw spirits drive ordering
- Spirits tab shows: oz used, ml used, btl used (theoretical), on-hand input, bottles to order
- On-hand entry in Spirits tab uses same `setOnHand()` logic as Prep Pars
- "Print Order Sheet" button on Spirits tab
- Empty-state message when no sales data loaded

### Design
- Spirit ordering separated from prep pars intentionally — prep pars = production sheet for prep team; spirits = purchasing sheet for manager
- Infused/house spirits (Lemongrass Infused Gin, Pandan Infused Iichiko, etc.) mapped to their base spirit for ordering tracking
- Phase 2 planned: add bottle cost per spirit → automatic pour cost % per drink and by category

---

## [2026-06-25] — Batch Cocktail Tracking

### Added
- 13 new batch PREP_ITEMS (all `cat:'Batch / Cordial'`, `btl:750`):
  Aviation Batch, Golden Elixir Batch, Mr. Paradise Batch, Ba-New-New Batch,
  Berry Beri Batch, Say Less Batch, Patent Pending Batch, Shifting Sands Batch,
  Uncle Adzuki Batch, Koi-Chelada Batch, Thai'd Up Batch,
  Watch Your Head Skipper Batch, Pimm's Cup Batch
- Each batch now appears on Prep Pars, tracks usage from sales data, and shows bottles to prep

### Changed
- DRINKS updated for all 13 batch cocktails — batch ID added with correct per-serve oz:
  Aviation (2.5oz), Golden Elixir (2oz), Mr. Paradise (4oz), Ba New New (2oz),
  Berry Beri (2.5oz), Say Less (2.5oz), Patent Pending (2.5oz), Shifting Sands (2oz),
  Uncle Adzuki (2.5oz), Koi-Chelada (2oz), Thai'd Up (2.75oz),
  Watch Your Head, Skipper (2.5oz), Pimm's Cup (2oz)

### Fixed / Removed (wrong prep item mappings)
- Ba New New: removed `vanilla_s` (not a fresh addition — was incorrect)
- Berry Beri: removed `lemongrass_s` (not a fresh addition — was incorrect)
- Mr. Paradise: removed `lemon` + `pb_choc_s` (both are IN the batch)
- Patent Pending: removed `peppercorn_s` (not a fresh addition — was incorrect)
- Say Less: removed `lemongrass_s` (not a fresh addition — was incorrect)
- Thai'd Up: removed `pandan_gum_c` (Pandan Gum Cordial is IN the batch)

---

## [2026-06-18b] — Bottles Used Added to Par Card

### Changed
- "Used This Week" section now shows ml, oz, and btl used (fractional, e.g. 3.3 btl)
- Bottom reference line renamed from "btl needed" to "btl to prep" for clarity

---

## [2026-06-18] — Par Card Cleanup + Clear On Hand + Print Fixes

### Added
- "Clear On Hand" button in header — clears all bottle entries without affecting sales data
- `clearOnHand()` function with confirmation prompt

### Changed
- Par card "Total Needed" renamed to "Used This Week" — cleaner label reflecting actual meaning
- Par card layout simplified: ml (large) + oz (small) + `X btl needed (Xml)` as compact reference below divider
- On hand hint simplified to `Xml per btl` — removed oz from that label to reduce noise

### Fixed
- Print sheet bottle counts were showing 10+ decimal places — now rounded to 2 decimal places max (e.g. `1.25` not `1.2500000001`)

---

## [2026-06-15] — Dual oz/ml Display + Waste Log Improvements

### Added
- oz shown alongside ml everywhere: par card totals, breakdown subtotals, waste log entries, waste suggestions, print sheet
- Helper functions: `mlToOz()`, `mlOzDisp()`, `btlOzLabel()` for consistent dual-unit display
- Bottle size reference label in waste log form updates dynamically when item is selected (e.g. "Bottles (473ml)")
- Waste log now accepts three input methods: bottles (correct size per item), oz, or ml — any combination summed

### Fixed
- Waste log was hardcoding 750ml for all items regardless of actual bottle size
- Waste log bottle inputs now use each item's correct bottle size

### Removed
- Golden Syrup removed from prep items (no longer used per bar manager)

### Bottle oz reference (confirmed)
| Bottle Size | oz Equivalent |
|-------------|---------------|
| 750ml | 25.36 oz |
| 518ml | 17.52 oz |
| 473ml (16oz) | 16.00 oz |
| 500ml | 16.91 oz |

---

## [2026-06-10] — Bottle Size Accuracy + Recipe Display

### Added
- Each prep item now has its correct bottle size baked in (750ml, 500ml, 518ml, 473ml/16oz, 1000ml)
- "Total Needed" column shows the actual bottle size per item (e.g. `9 × 473ml btl` for squeeze bottle items)
- On Hand input now shows a `Xml / btl` reminder below each input field
- Sales tab recipe lines now display both oz and ml (e.g. `0.75oz (22ml)`)
- Prep Pars breakdown lines now display both oz and ml per ingredient

### Fixed
- Bottle math was assuming 750ml for all items — managers were over-prepping squeeze bottle and 500ml items

### Bottle Size Reference
| Size | Items |
|------|-------|
| 750ml | Lemon, Lime, Grilled Lemon, Yuzu |
| 500ml | Grapefruit, OJ, Pineapple, Cucumber Water, Ginga, Lemongrass |
| 518ml | Simple, Demerara, Maple, Runny Hunny, Vanilla, Agave, Bombgave |
| 473ml (16oz) | All purées, house specialty syrups, Orgeat (working bottle), Ginger Honey, Tigers Blood, Shiso Miso Hunny |
| 1000ml | Orgeat (purchase bottle) |

---

## [2026-06-09] — Prep Par Card Cleanup

### Changed
- Reverted to showing full weekly total as the prep number (removed ÷2 split view)
- "Bottles to Prep" restored as primary column label
- Status display simplified back to clean `X btl (Y ml)` / `Covered ✓` format
- Print sheet headers updated to match

### Removed
- "Per Prep (÷2)" and "Full Week" split columns — caused confusion, full CSV total is the correct prep number per session

---

## [2026-06-08] — Waste Log Bottles Input

### Changed
- Waste Log qty input changed from raw ml to bottles (750ml) + partial bottle (0.25 step)
- Waste log entries, totals, and par suggestions now display in bottles with ml secondary
- Waste badge and summary line updated to show btl language

---

## [2026-06-07] — Bottles-First Prep Math

### Added
- On Hand input now uses 750ml bottles (step 0.5) — managers enter bottles, not ml
- "Bottles to Prep" is now the primary output column
- Print prep sheet shows bottles as primary with ml secondary

### Changed
- All oz displays converted to ml throughout (OZ_TO_ML = 29.574)
- Internal recipe math stays in oz; conversion happens at display layer only

---

## [2026-06-06] — CSV Parsing Fixes + Full Drink Audit

### Fixed
- **Critical:** CSV accumulation bug — ITT/ATP/RC variants were overwriting base drink qty instead of summing (e.g. Moscow Mule showed 3 instead of 8)
- NA Conversation Starter not matching — added alias
- Oaxacan Old Fashioned silently dropped — added to DRINKS array
- FEATURE Spritz matching wrong drink via partial match — added explicit alias
- Dr. Funk, Thai Gimlet, French 75/95, Saketini — added missing aliases
- Pretty In Pink, The Face Planter Punch, Sherry Cobbler — added to DRINKS

### Added
- `cleanToastName()` strips ITT prefix, ATP/RC suffix, FEATURE prefix before matching
- Accumulation logic: `sales[name] = (sales[name] || 0) + qty`

---

## [2026-06-05] — Waste Log Tab

### Added
- **Tab 4 · Waste Log** — log spoilage/expired product with item, qty, reason, date, notes
- Waste reasons: Spoiled/Expired, Wrong batch/Remake, Contaminated, Spill/Accident, Other
- Par adjustment suggestions triggered when an item accumulates ≥100ml waste
- Export waste log to CSV
- Waste badge shows entry count on tab label

---

## [2026-06-04] — Yields Guide Tab

### Added
- **Tab 3 · Yields Guide** — reference guide for citrus juice yields per case
- Lemon (115/case, ~41ml each), Lime (48/case, ~30ml each), Grapefruit (27/case, ~118ml each)
- Quick yield calculator: select fruit, enter qty, get total ml + liters

---

## [2026-06-03] — Core Calculator Launch

### Added
- Single-file HTML tool (no server required, runs from any browser)
- Toast POS CSV upload and parsing
- Fuzzy drink name matching with `NAME_ALIASES` and partial match fallback
- `PREP_ITEMS` (75+ items), `DRINKS` (108+ cocktails), `HISTORICAL_AVG` (2025 weekly baselines)
- **Tab 1 · Sales Input** — manual qty entry or CSV import, per-drink ingredient breakdown
- **Tab 2 · Prep Pars** — calculates ml needed per prep item based on sales, grouped by category
- Par buffer % control (+5/−5 steps) with visual indicator
- On Hand input with in-place DOM update (no focus loss on keystroke)
- Print prep sheet — formatted for prep team with checkboxes
- localStorage persistence for sales, on-hand, waste log, buffer setting
- Support for ITT (It Takes Two), ATP (Across the Pond), RC (Roll Call) promo variants
- Historical average hint text on Sales tab (↳ sold ~X/wk on average in 2025)

---

## How to Update This File

Each time you push changes to GitHub, add a new entry at the **top** of this file (above the previous entry) using this format:

```
## [YYYY-MM-DD] — Short Description

### Added
- New features or capabilities

### Changed
- Updates to existing behavior

### Fixed
- Bugs that were corrected

### Removed
- Features or content that were taken out
```

You don't need all four sections every time — just include the ones that apply.
After editing, save the file and push it to GitHub alongside `index.html`.
