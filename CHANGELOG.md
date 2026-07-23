# Bar Par Calculator — Changelog

All notable changes to this project are documented here.
Format: newest entries at the top.

---

## [2026-07-20b] — Arizona Ronan → Arizona Ronin (Spelling Correction)

Nick's CSV import kept flagging "AZ Ronin" as unmatched at ATP. Checked every source doc on file (CK MANGA PREP RECIPES.pdf, pages 18–19) — all spell it "ARIZONA RONAN," matching the build, so this wasn't a build error. Nick confirmed Toast's actual item name is "Ronin," not "Ronan" — renamed the canonical drink to **Arizona Ronin** to match what's really printed/rung.

### Changed
- `DRINKS` entry renamed `'Arizona Ronan'` → `'Arizona Ronin'` (ATP menu, ingredients unchanged)
- `NAME_ALIASES` updated: `arizona ronan`, `arizona ronin`, `az ronin`, and `az ronan` all map to the new canonical `'Arizona Ronin'` — covers both spellings and the "AZ" abbreviation Toast uses, so this can't silently break again either way.

### Verification
- Node harness: 206 PREP_ITEMS, 237 DRINKS, zero duplicates, zero dangling refs, CSV import unaffected.

---

## [2026-07-20] — Printable On-Hand Count Sheet

Added a new "🖨 Print On-Hand Sheet" button next to the existing Print Prep Sheet button. Nick's staff currently write on-hand prep counts on blank paper before he retypes them into the spreadsheet — this gives them a structured sheet to write on instead.

### Added
- **`printOnHandSheet()`** — opens a printable sheet listing every prep item used in a recipe at the active location (excludes Spirit/Liqueur categories), grouped by category, with each row showing item name + bottle size and a blank line to write the on-hand count. No needed/net/checkbox columns — just name and a line. Header includes date and "counted by" blanks. Respects whichever location is active.

### Fixed (same day, before first use)
- Initial version filtered items by `needed[p.id] > 0` (i.e., only items with sales in the current week), same as the Prep Sheet. Nick caught that this drops items with zero recent sales but real on-hand stock — an inventory count needs a line for everything in use, not just what recently sold. Switched the filter to "referenced by any drink at this location" (via `locDrinks()`), independent of sales data. Confirmed via harness: CKC lists 66 non-liquor items, CKG lists 56, even with zero sales entered.

### Verification
- Node harness: 206 PREP_ITEMS, 237 DRINKS, zero duplicates, zero dangling refs, CSV import unaffected.
- Simulated `printOnHandSheet()` for both CKC and CKG with an empty `sales` object to confirm every in-use item still appears — not just that the file parses.

---

## [2026-07-16h] — Pimm's Cup Resolved: Component Spec, Not Batch

Nick decided to roll with the literal cocktail spec for CKG's Pimm's Cup rather than the ambiguous "(1.75 Pimms Batch)" footnote. Changed `pimms_b:1.0` (the pre-batched item) to `pimms_spirit:1.0` (plain Pimm's No. 1 liqueur), matching the recipe's literal "1 Pimms / ¼ Jelinek Fernet / ¼ Tuve Fernet" line items with no batch reference at all. Closes the last open item from `2026-07-16f`/`g`.

### Verification
- Node harness: 206 PREP_ITEMS, 237 DRINKS, zero duplicates, zero dangling refs, CSV import unaffected.

---

## [2026-07-16g] — Bar Manager Answers: Golden Myth Corrected, Vanilla Demerara Added

Nick's bar manager (Bacon) answered the three open questions from `2026-07-16f`.

### Fixed
- **Golden Myth** — corrected from a 4oz pour to a **2oz pour** (`golden_myth_b:2.0`). Bacon confirmed all three CKG House Shots (Golden Myth, Thai Manna, Belly Shot) are batched and poured as 2oz shots; the "4oz Pour" in the menu-specs doc was wrong.

### Added
- **`vanilla_demerara`** new PREP_ITEM ("Vanilla Demerara (House Demerara + Vanilla Paste)") — confirmed as a real, distinct prep: house Demerara syrup with a barspoon of vanilla paste stirred in, used only in Liquid Escape. Both `Liquid Escape - Vodka` and `Liquid Escape - Reposado` switched from `demerara:0.5` to `vanilla_demerara:0.5`.

### Confirmed, no change needed
- **Aviation** is batched like CKC — the `aviation_b:2.5` addition from `2026-07-16f` was correct.
- **Condensed Thai Tea** — the *menu-spec* ratio (2qt tea / 14oz condensed milk / 4oz coconut cream) is the correct one, not the prep-recipe docs' 4qt/16oz/8oz. No code change (still tracked as an opaque 6oz `condensed_thai_tea` pour either way), but resolves the internal-doc-inconsistency flag from the prior entry.
- **Three Spiced Genepy, Cinnamon Campari, and Coconut Cachaca** are confirmed unused — leftover preps from when CKG and CKC shared a combined Manga menu. Never added to PREP_ITEMS or DRINKS, so nothing to remove.

### Pending
- Bacon has finalized and renamed the prep list to "CKG Manga Prep Recipes 2026" to avoid version confusion, resending via a new upload — not yet received.

### Verification
- Node harness: 206 PREP_ITEMS, 237 DRINKS (110 CKC, 127 CKG), zero duplicate ids, zero duplicate name+loc combos, zero dangling ingredient references, CSV import unaffected (108 matched, 1 known unmatched).

---

## [2026-07-16f] — CKG Classics + Manga Cross-Checked Against Latest Docs (Classics PDF, Prep Recipes, Manga Menu Specs)

### Fixed
- **`HISTORICAL_AVG` "sold ~X/wk on average in 2025" hint suppressed for CKG.** This lookup table was built entirely from CKC's own 2025 Toast history before CKG existed, keyed only by drink name with no location scoping. Since 72+ drink names are shared between CKC and CKG, it was displaying CKC's historical numbers on 40 different CKG drinks (Enabler, Manhattan, Negroni, Aviation, Scarlet Sora, etc.) as if they were CKG's own sales history. Display-only — never affected par/order calculations. Suppressed entirely for CKG until Nick provides real CKG historical data.

Nick uploaded three more current docs: "CK Classic Cocktail Recipes.pdf", "CK MANGA PREP RECIPES.pdf" (internally dated "CKG MANGA PREP RECIPES 2026"), and "CKG MANGA LAYOUT.pdf" (internally dated "CKG MANGA MENU SPECS 2026" — the most current-dated CKG doc seen yet). Diffed all three against the full current build.

### Result: CKG Classics (88 drinks) — verified clean
Every ingredient and quantity in the new Classics PDF's CKG-specific section (pages 10–18, pre-well-swap "Gruven/Royal Standard/Mr Black" naming) matches the current build exactly once the standing well-swap is applied. No changes needed.

### Result: CKG Manga (19 drinks + 3 NA) — verified clean, including the four flagged items
The new "CKG MANGA MENU SPECS 2026" doc independently confirms every quantity from the `2026-07-16e` revision — Zenitsu Sour (Tenjaku+Cognac), Long Thailand Iced Tea (batch), Sideline View NA (bombgave recipe), Midnight Oni Highball (Tenjaku), and Scarlet Sora (Zubrowka) all match exactly. This is a second independent document confirming those four changes were correct.

### Added
- **Aviation** (CKG Classics) — the new Classics PDF explicitly notes "(2.5 AVIATION BATCH)" under CKG's Aviation spec, matching the same dual-tracking pattern CKC's own Aviation already uses (batch + reconstituted individual pours). Added `aviation_b:2.5` to CKG's Aviation entry to match.
- **3 new CKG Manga "House Shots"** — the 2026 Menu Specs doc's "House Shots" section finally gives real recipes for three items that previously only existed as unlinked batch recipes with no cocktail spec:
  - **Golden Myth**: 4oz pour of new batch item `golden_myth_b`
  - **Belly Shot**: 2oz pour of new batch item `belly_shot_b` (pork-fat-washed Dickel Bourbon) — served with a 4oz side of pork belly broth, left untracked since no broth recipe was provided
  - **Thai Manna**: 2oz pour of new batch item `thai_mana_b`
  - New `CKG Manga House Shots` menu category added; `MENU_LABELS` and `LOW_USE_PROGRAMS` extended for both this and the previously-untracked `CKG Well Pours` category.

### Notes / Pending — needs Nick's input, not guessed
- **Vanilla Demerara** — appears repeatedly across multiple prep docs as a distinct syrup from plain Demerara, used in Liquid Escape. Still using plain `demerara` in the build. Confirm if this is a real distinct product needing its own PREP_ITEM.
- **Thai Tea NA batch recipe is internally inconsistent across Nick's own docs** — the two "menu spec" docs (June 2025 and the new 2026 version) both say 2qt brewed tea / 14oz condensed milk / 4oz coconut cream; the two "prep recipe" docs (September 2025 and its 2026 twin) both say 4qt / 16oz / 8oz — a different milk:cream ratio. Doesn't affect the app (still tracked as an opaque 6oz pour) but the kitchen is working off two different ratios for the same batch.
- **Pimm's Cup batch quantity** — the new Classics PDF footnotes "(1.75 PIMMS BATCH)" under CKG's Pimm's Cup; current build uses `pimms_b:1.0`. Given CKC's own Pimm's Cup double-tracks both the batch and its reconstituted components (a pre-existing pattern from Stage 1), didn't want to guess and risk double-counting — flagging rather than changing.

### Verification
- Node harness: 205 PREP_ITEMS, 237 DRINKS (110 CKC, 127 CKG), zero duplicate ids, zero duplicate name+loc combos, zero dangling ingredient references.
- CKG Items.csv import simulation: 108 matched, 3 ignored, 1 unmatched (`Long Island`, still no recipe on file), zero orphaned matches.

---

## [2026-07-16e] — CKG Manga Rebuilt Against Bar Manager's Current Spec Sheet

Nick's bar manager sent a new "CKG Manga Menu June 2025" spec PDF, described as the current version. Diffed it against all 19 built CKG Manga drinks (plus the 3 N/A drinks) — nearly every recipe had at least a minor quantity change from the version built in Stage 2, which came from an earlier/less precise document.

### Changed — quantity/ingredient corrections (mechanical, applied directly)
- **Backyard Kid**: base spirit `campo_repo` → `arette_repo` (spec now says Arette Reposado, not Campo); Ember Syrup 1oz → 0.75oz
- **Bad Man Lighter**: added `banana_liq:0.5` (Pairidaeza Banana Liqueur — missing from the original build entirely); Giffard Passionfruit 0.5oz → 0.25oz; Thai Tea concentrate 0.5oz → 1oz
- **Enabler**: Maple Syrup 0.75oz → 1oz (bourbon still collapses to `bardstown_bbn` per the standing Dickel/Bardstown policy — spec now says "Dickel 12yr" but that's the same locked well bourbon)
- **Elisabetta's Slumber**: Amaro Nonino 1.5oz → 1oz; Pork Fat Washed Zucca 0.5oz → 1oz; added `honey_s:0.125` (barspoon "Runny Hunny") — this had been silently dropped by the original parsing script, same bug class as the Crystal Castle fix, caught by this diff
- **Koi Kooler**: Negroni Sixteen (Aperol sub) 0.5oz → 0.25oz; Lemon 0.75oz → 1oz; Lemongrass Syrup 0.5oz → 0.75oz; added `grapefruit:0.25` (new ingredient)
- **Liquid Escape - Reposado**: base spirit `campo_repo` → `arette_repo`, matching Backyard Kid's correction
- **Pandan B-Bop**: Pineapple 0.75oz → 0.5oz
- **Sunburst**: swapped `aa_pine` (Acid Adjusted Pineapple) → plain `pineapple_j` per the new spec; added `citric_simple:0.25`
- **Yotei Six**: Pork Fat Vodka 1oz → 1.25oz; Ember Syrup 1.25oz → 0.75oz; added `lemon:0.25`
- **Ube Colada NA**: Lime 0.75oz → 0.5oz

### Changed — real recipe/product swaps
- **Midnight Oni Highball**: `dickel_rye:0.5` (Roulette Rye) replaced with `tenjaku_whisky:1.0` — this is a real Tenjaku Whisky pour, not the same thing as Zenitsu's Suntory Toki. Re-added `tenjaku_whisky` as a PREP_ITEM (had been removed earlier this session on the assumption Suntory Toki fully replaced it everywhere — it didn't). Akashi Ume 1.5oz → 1oz, Oni Plum Cordial 1oz → 0.75oz.
- **Scarlet Sora**: base vodka `monopolowa_v` → `zubrowka_v` (Zubrowka, an existing distinct specialty vodka — this is not a well-vodka drink, don't apply the Gruven→Monopolowa well swap here). Peach Puree 0.5oz → 0.25oz; Pineapple Gum 0.25oz → 0.5oz.

### Renamed (to match Toast/spec exactly, simplifies matching)
- CKG's **"Lemongrass Swizzle NA"** → **"Lemongrass Refresher NA"**
- CKG's **"Ube'Be Colada NA"** → **"Ube Colada NA"**
- Removed the now-redundant `LOCATION_ALIASES.CKG` override (exact-name matching + existing global aliases cover both locations correctly post-rename); updated `na ube colada` alias target accordingly

### Resolved — superseded Nick's earlier direct answers; confirmed 2026-07-16 ("Use that current doc as the official doc")
These four initially contradicted things Nick told me directly in this same session. Flagged for confirmation when applied; Nick has since confirmed the new spec doc is authoritative, so these stand as final:
- **Zenitsu Zour**: now `Tenjaku Whisky + Lecarre Cognac` (`tenjaku_whisky:1, cognac_r:1, aa_oj:1, coconut_c:1, yuzu:0.25, ginga_s:0.25`) — supersedes the earlier "Use Suntory" build (Suntory Toki + Couvignac). Red Blend Float dropped.
- **Long Thailand Iced Tea (CKG)**: now uses the pre-made batch (`long_thai_b:1.75, thai_tea:1.5, coconut_c:1.5`) — supersedes the earlier "intentional difference" build from individual spirits.
- **Sideline View NA**: now `lime:1, bombgave:1, passion_p:1.5` — supersedes the earlier "same as CKC" build (`lime:2, passion_p:1.5`). CKC's own Sideline View NA is untouched.
- **Thai Tea NA**: now `condensed_thai_tea:6.0` (new batch PREP_ITEM: 2qt brewed tea + 14oz condensed milk + 4oz coconut cream) — supersedes the earlier `thai_tea:1.75, coconut_c:1.5` approximation.

### Verification
- Re-ran the Node harness: 202 PREP_ITEMS, 234 DRINKS (110 CKC, 124 CKG), zero duplicate ids, zero duplicate name+loc combos, zero dangling ingredient references.
- Re-ran the CKG Items.csv import simulation: 108 matched, 3 ignored, 1 unmatched (`Long Island` — still no recipe on file), zero orphaned matches.

---

## [2026-07-16d] — Remaining CKG Gaps Closed (Hey Bartender!, Well Pours, etc.)

### Added
- **Hey Bartender!** (CKG) — added as a note-only stub, same pattern as CKC's version, but with an accurate note: it's a bartender's-choice custom build (guest gives a flavor profile), not a missing spec — there's no fixed recipe to track
- **Sideline View NA** (CKG) — same recipe as CKC: `lime:2, passion_p:1.5`
- **Thai Tea NA** (CKG) — Long Thailand Iced Tea with all alcohol removed: `thai_tea:1.75, coconut_c:1.5`
- **Aperol Spritz** (CKG) — `lemon:0.75, simple_s:0.5, negroni16:2, prosecco_w:3`. Nick's spec said "2 aperol" — substituted to `negroni16` per the standing Aperol → Negroni Sixteen well-guide policy already applied elsewhere in CKG. Flagged for confirmation since he didn't explicitly re-affirm the swap this time.
- **Face Planter Punch** (CKG) — its own distinct recipe from CKC's, not a copy: `kiwi_p:2, lime:2, pineapple_j:4, hamilton_86:4, cognac_r:1, goslings_r:1, worthy_park:0.5` (mango/orange bitters not tracked, same convention as everywhere else). "151 rum" mapped to existing `worthy_park` (Worthy Park Overproof) — flag if a different overproof rum is actually stocked.
- **6 new "CKG Well Pours" entries** (Well Vodka, Well Gin, Well Tequila, Well Bourbon, Well Rye, Well Mezcal) — single-spirit 2oz pours, new `CKG Well Pours` menu so they stay visually separate from cocktails on the Sales tab, now counting toward spirit ordering instead of being invisible to the app
- `na thai tea` added to `NAME_ALIASES`

### Verification
- Re-ran the CKG Items.csv against the fixed matcher: 108 matched, 3 correctly ignored, 1 genuinely unmatched (`Long Island` — confirmed a real Long Island Iced Tea, not a Long Thailand typo, but no recipe/quantities received yet). Zero orphaned matches.

---

## [2026-07-16c] — CKG Toast CSV Audit + Location-Aware Import Fix

### Fixed
- **Critical**: CSV name-matching (`DRINK_LOOKUP`) was built once globally from every location's `DRINKS` + `NAME_ALIASES`, so a CKG Toast row could silently resolve to a CKC-only canonical name that doesn't exist in CKG's drink list. The import banner would report a false "✓ matched," but `locDrinks()` would never find that name again — sales data recorded, then invisibly dropped from every calculation. Confirmed against a real month of CKG product mix: 6 rows (Face Planter Punch, Aperol Spritz, Hey Bartender ×3 variants, N/A - Sideline View) were being silently lost this way.
- Replaced the global `DRINK_LOOKUP` with `buildDrinkLookup()`, called fresh per import and scoped to `locDrinks()` — an alias only resolves if its target actually exists in the active location. New `LOCATION_ALIASES` object holds per-location overrides for cases where identical Toast text means a different drink at each location (e.g. "N/A - Lemongrass Refresher" → CKG's `Lemongrass Swizzle NA`, distinct from CKC's own `Lemongrass Refresher NA`).
- Added CKG-specific aliases: `Gin & Tonic` → `Gin and Tonic`, `South Side` → `Southside`, `N/A - Ube Colada` → `Ube'Be Colada NA`, `Whiskey Sour W/Egg` / `Whiskey Sour-No Egg` → `Whiskey Sour`
- `N/A - Mocktail` / `N/A - Club Soda` now correctly ignored (prefix wasn't stripped before the ignore-list check)

### Changed
- **Liquid Escape** split into two real entries — `Liquid Escape - Vodka` and `Liquid Escape - Reposado` — matching how Toast actually rings it. Replaces the earlier 50/50 blended-line workaround from Stage 2, now that real sales data confirms it's tracked as two distinct choices (38 reposado vs. 20 vodka sold in the sample month).
- **Thai Gimlet** split into `Thai Gimlet Gin` and `Thai Gimlet Vodka` — Toast data showed a near-even split (26 gin / 24 vodka), so the single gin-only entry was misattributing roughly half its volume to the wrong spirit.

### Notes / Pending — needs Nick's input, not guessed
- **Hey Bartender!** — 56 combined sales across CKG in one month (regular + ITT + N/A variants) with no recipe anywhere, including CKC's own version, which is also just a placeholder
- **N/A - Sideline View** — 53 sales, no CKG recipe on file (not in the Manga NA spec received). CKC has a recipe (`lime:2, passion_p:1.5`) — confirm if CKG's is the same
- **N/A - Thai Tea** — 96 sales, highest-volume unmatched item. Needs a real spec (or confirm it's just brewed Thai tea with no other tracked ingredient)
- **Aperol Spritz / House Spritz** — 10 sales; CKC's "House Spritz" is itself an unbuilt placeholder
- **Face Planter Punch** — 2 sales at CKG; CKC has a real recipe but no confirmation CKG uses the same one
- **Well Vodka / Gin / Tequila / Bourbon / Rye / Mezcal** — 80 combined neat/rocks pours this month, currently untracked entirely. Recommending these get modeled as single-spirit "drinks" (one ingredient, standard pour size) so they count toward spirit ordering — need Nick to confirm pour size and whether he wants that
- **Long Island** — 3 sales; unclear if this is a real "Long Island Iced Tea" or a mistyped/shorthand "Long Thailand Iced Tea"

---

## [2026-07-16b] — Jade Mist Wander Recipe Added

### Added
- **Jade Mist Wander** (CKG Manga) — last unbuilt CKG drink, now wired: `el_dist_tequila:1.5, chareau_l:0.25, midori_liq:0.25, vanilla_s:0.5, citric_simple:0.25, berry_momokawa_sake:0.5`
- 3 new PREP_ITEMS: Midori Melon Liqueur (`midori_liq`), Citric Simple Syrup (`citric_simple`), Berry Momokawa Nigori Sake (`berry_momokawa_sake`, new `Sake` category)

### Notes
- "Chareu Aloe Liquor" mapped to existing `chareau_l` (Chareau) — same product, spec just misspells the brand name
- "Midori" mapped to a **new** item (`midori_liq`), kept separate from the existing `mikeydori` ("House Midori") batch — spec says plain "Midori," not "house," so treating it as the real bottled brand rather than the housemade version. Flag if that's wrong.
- CKG Stage 2 is now fully built — all Classics, all Manga, all NA. Only open item is confirming the CSV/Toast product mix once Nick pulls a month of CKG export data.

---

## [2026-07-16] — CKG Classics + Manga Wired In (Stage 2)

### Added
- **88 CKG Classics recipes** and **19 CKG Manga recipes + 3 NA** added to `DRINKS`, each tagged `loc:'CKG'`, `menu:'CKG Classics'` or `'CKG Manga'`
- `Jade Mist Wander` added as a note-only stub (`ing:{}`, "Recipe not on file") — on the printed menu but no spec received yet
- 47 new `PREP_ITEMS` for CKG-only ingredients (Blue Curacao, Bank Note Peated Scotch, Rhum JM 100 Proof, New Deal Coffee Liqueur, Eldee Triple Sec, House Dry/Sweet Vermouth, Tuve Bitter Liqueur, Suntory Toki Whisky, Couvignac, Cream Sherry, Ron Colón Red Banana Rum, Pork Fat Washed Vodka, and a long tail of one-off Manga modifiers) — full list in `ckg_stage2_ingredient_mapping` project notes
- `loc` field on `DRINKS` entries + new `locDrinks()` helper — filters the drink list to the active location before any calculation
- CKG-only spirit/liqueur ids added to the `Spirits & Ordering` and `Print Order Sheet` groupings; `CKG Classics` / `CKG Manga` added to Cocktail Movement's menu labels and low-use/single-use program breakdowns

### Changed
- `calcNeeded()`, `renderSales()`, `renderPars()`, `printPrepSheet()`, `renderMovement()` now filter through `locDrinks()` instead of the raw `DRINKS` array
- `renderSpirits()` and `renderWasteLog()` no longer gate on `activeLocation === 'CKG'` — the "Coming Soon" placeholder only shows now if a location genuinely has zero drinks
- **Well-swap applied across all CKG recipes**: Gruven Vodka → Monopolowa, Royal Standard Rum → Denizen, Mr Black Coffee Liqueur → New Deal Coffee Liqueur (per Nick's active well-guide transition, confirmed against the printed CKG menu and Manga spec sheet, not just the recipe spreadsheet)
- Goslings / "Hart-Son" dark rum references kept mapped to the existing `goslings_r` item (display name left as "Plantation Original Dark Rum" — not renamed to "Planteray" — so CKC's prep sheets are unaffected)
- Aperol replaced with Negroni Sixteen everywhere it appeared in CKG specs (Koi Kooler, Paper Plane, Age of The Aquarius), per locked well guide
- **Zenitsu Zour**: built from Suntory Toki Whisky + Couvignac (menu wording) rather than the spec sheet's Tenjaku Whisky + Lecarre Cognac
- **Liquid Escape**: "Vodka or Reposado Tequila" choice modeled as a 50/50 split (0.75oz Monopolowa Vodka + 0.75oz Campo Reposado) rather than defaulting fully to one spirit, since it's tracked as a single sales line

### Fixed
- **Critical, caught before release**: `calcNeeded()` originally summed the whole `DRINKS` array with no location filter. Once CKG drinks reused CKC drink names (Old Fashioned, Aviation, Daiquiri, Manhattan, Negroni, Mai Tai, and 67 others), CKC's prep math would have silently pulled in CKG's ingredients for any name collision. Fixed via `locDrinks()`; verified CKC and CKG "Old Fashioned" now resolve to their own distinct spirits with zero cross-contamination.
- **Crystal Castle**: the "barspoon Genepy or Green Chartreuse" float was being silently dropped by the recipe parser (not defaulted — dropped entirely). Fixed to include Green Chartreuse, matching the printed menu.

### Notes / Pending
- **Jade Mist Wander** still needs a real spec from Nick — currently unbuilt
- Confirmed as intentional/accurate, not sheet errors: Long Thailand Iced Tea (CKG) skips the `long_thai_b` pre-batch CKC uses; Riding The Pine NA (CKG) includes Bombgave that CKC's version doesn't; Ube'Be Colada NA (CKG) includes Kiwi that CKC's doesn't
- CKC/ATP `DRINKS` and `PREP_ITEMS` entries themselves are untouched (only appended to) — the shared render/calc functions above were modified but verified to produce byte-identical CKC/ATP output (same per-menu drink counts, same ingredient totals) before and after

---

## [2026-07-15b] — CKG Location Switcher (Stage 1)

### Added
- **CKC ↔ CKG location toggle** in the header — pill-style switcher between Clever Koi Central and CKG Gilbert
- CKG uses a blue color theme (`#5a9ee0`); CKC retains gold (`#c9a84c`)
- Separate localStorage keys per location: `ck_pars_*` (CKC) vs `ckg_pars_*` (CKG) — switching locations saves and restores each location's sales, on-hand, buffer, and waste log independently
- `switchLocation()` — saves current location state, loads the target location's state, syncs buffer UI, and re-renders all tabs
- `renderCKGPlaceholder()` — "CKG Gilbert — Coming Soon" message shown on all tabs while CKG recipes are being wired up
- Subtitle under the title updates to reflect the active location
- Page reload restores the last-used location from localStorage

### Notes / Pending
- **Stage 2**: Wire CKG Classics cocktails (Sgroppino al Limone, Martinez, Corpse Reviver #2, Tom Collins; Pornstar Martini with Amaretto variant)
- **Stage 3**: Wire CKG Manga cocktails (PDF received, batch recipes pending)
- CKC Monday/Thursday workflow is fully unaffected — switching to CKG and back is seamless

---

## [2026-07-15] — Locked Well Standard Spirit Updates

### Changed
Updated PREP_ITEMS to match B&R Hospitality Bar Well Guide (locked standard as of 7/1/2026):

**Bottle replacements:**
- Goslings Dark Rum → Plantation Original Dark Rum (750ml → 1L)
- George Dickel Rye → Roulette Rye (750ml → 1L)
- Maison Rouge Cognac → Lecarre VS French Brandy (750ml → 1L)
- Payet Pisco → Vinas De Oro Pisco (750ml, no size change)
- Arette Reposado → El Destilador Reposado (750ml → 1L)
- El Distilador 100 → El Destilador Blanco (name corrected, 750ml → 1L)
- Ryu Mezcal → Rayu Joven Mezcal (name corrected, 750ml → 1L)

**Bottle size corrections (same bottle, now ordering 1L):**
- Monopolowa Vodka: 750ml → 1L
- Monopolowa Gin: 750ml → 1L
- Bank Note Blended Scotch: 750ml → 1L

---

## [2026-07-11b] — Cocktail Movement Program Split

### Changed
- Low-Use Prep Items and Single-Use Prep Items sections now broken out by program (CKC Manga / ATP / Classics) instead of a single combined table
- Removed "Program" column from both tables — now implicit from the section label

---

## [2026-07-11] — Missing Recipe Fixes

### Fixed
- **The Face Planter Punch**: Wired full recipe — `goslings_r:4, cognac_r:1, hamilton_86:1, pineapple_j:4, lime:2, kiwi_p:2`. Note: recipe calls for "brandy" — mapped to `cognac_r` (Maison Rouge); confirm if a different bottle is used.
- **Long Thailand Iced Tea**: Wired batch spirit breakdown. Batch = 250ml Bardstown + 250ml El Destilador + 250ml Denizen + 1L Jelenik Fernet (1750ml total). Per 1.75oz pour: `bardstown_bbn:0.25, el_dist_tequila:0.25, denizen_r:0.25, jelinek_fernet:1`.

---

## [2026-07-08] — Spirit Mapping Fixes

### Fixed
- **Whiskey Sour**: Added `dickel_rye: 2oz` — recipe specifies George Dickel 12 Sour Mash (same bottle as Old Fashioned). Was missing entirely, so Whiskey Sour sales weren't contributing to Dickel ordering.
- **Sherry Cobbler**: Added `amontillado_s: 3oz` — confirmed via both Classics and ATP recipe PDFs. Was missing, so Sherry Cobbler sales weren't contributing to Amontillado ordering.

### Notes / Pending
- **Long Thailand Iced Tea**: Spirit tracking flows through the `long_thai_b` batch PREP_ITEM (correct). Individual spirits inside the Long Thai Batch are not mapped because the batch recipe isn't in the provided PDFs — add once batch recipe is available.
- **The Face Planter Punch** and **Hey Bartender!**: No recipe found in any PDF — remain flagged with notes.

---

## [2026-07-06b] — Cocktail Movement Refinements

### Changed
- Slow mover threshold switched from "3% of total sales" to "60% of weekly average per cocktail" — prevents strong sellers like The Enabler from surfacing as at-risk
- Removed "Slow Mover", "Top Seller", and "Consider Retiring" text badges — slow movers now shown as dimmed rows, data speaks for itself
- Low-Use Prep Items sorted highest to lowest (cocktails sold), matching other sections
- Single-Use Prep Items only surfaces when the parent cocktail is itself below the slow mover threshold
- Low-Use Prep Items now shows oz per serve next to cocktail name (e.g. "Honne (0.25 oz/serve)") — makes it immediately clear whether low usage is due to low sales or a micro-pour

---

## [2026-07-06] — Print Order Sheet + Cocktail Movement Tab

### Added
- **Tab 6 · Cocktail Movement** — velocity ranking of all sold cocktails (qty, % of total sales), top seller / slow mover badges, and a "Low-Use Prep Items" table flagging prep items used in only one cocktail with under 8 oz consumed
- **printOrderSheet()** — clean white print window for Spirits & Ordering (same format as Prep Sheet): grouped by spirit category, columns for oz used, ml, btl used, on hand, to order, and a checkbox

### Changed
- Tab order updated: 1 · Sales Input, 2 · Prep Pars, 3 · Spirits & Ordering, 4 · Cocktail Movement, 5 · Waste Log, 6 · Yields Guide
- Cocktail Movement tab now broken out by menu section (Classic Cocktails, CKC Manga Cocktails, ATP Cocktails) — each section ranked independently with its own Top Seller / Slow Mover badges
- Slow Movers callout merged into velocity ranking — badges appear inline, duplicate section removed
- Each cocktail row now shows % of menu sales and % of all sales
- Low-Use Prep Items now shows cocktail name and program (Classics / CKC Manga / ATP), sorted by fewest cocktails sold first
- Low-Use Prep Items split into two distinct sections: **Low-Use** (1 cocktail, under 8 oz — retirement candidates) and **Single-Use** (1 cocktail, 8+ oz — selling well but fully dependent on one cocktail, at risk if cut)

### Fixed
- Print Prep Sheet no longer includes Spirit/Liqueur categories (they were bleeding into the prep print output)
- Spirits & Ordering "Print Order Sheet" button now opens a proper print window instead of printing the dark-themed screen

---

## [2026-07-02] — Spirits Tab Fix + Prep/Spirits Split

### Fixed
- **Spirits tab blank when sales loaded** — removed `|| true` from the spirit-item filter so only spirits with actual usage appear (previously all 60+ items rendered as "—" rows, looking blank). If sales are loaded but no spirit usage is calculated (e.g. sales saved before spirits were added to recipes), a clear hint message now explains why and suggests re-uploading the CSV.
- **Spirits appearing on Prep Pars tab** — added `Spirit` and `Liqueur` category filter to `renderPars()` so juices/syrups/batches and spirits are now fully separated across tabs.
- **Spirits tab content hidden** — `tab-waste` div was missing its closing tag, causing `tab-spirits` to be nested inside it. Spirits were always hidden because the waste tab was `display:none`. Added missing `</div>`.

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
