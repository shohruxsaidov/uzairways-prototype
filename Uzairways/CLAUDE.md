# Uzairways — Project State & Handoff

A booking-flow prototype set, all dressed in a single extracted **design concept** (Uzbekistan Airways booking page). Russian UI, currency **UZS**. Every screen is one self-contained HTML file; snapshot files auto-open a specific modal/state on load (for Figma import via **html.to.design** → URL tab).

## Files
- **Uzairways Design Concept.html** — the design-system reference doc (tokens + components).
- **Flight Search Results.html** — MAIN interactive file (full flow). Edit this first, then propagate to the 6 snapshots.
- **Flight Search Results - Fares Open.html** — auto-opens fare grid (Economy).
- **Flight Search Results - Detail Open.html** — auto-opens flight-detail modal (HY 404).
- **Flight Search Results - Return Leg.html** — auto-enters return-leg state (outbound chosen).
- **Flight Search Results - Both Selected.html** — both legs chosen (round-trip review).
- **Flight Search Results - Sort Open.html** — auto-opens the sort dropdown.
- **Flight Search Results - Confirm Open.html** — auto-opens the round-trip confirm modal (Lite → Comfort).

> The 6 snapshot files are COPIES of the main file with a small init `<script>` appended right before the final `</script>`. **Workflow for any change: edit the main file (usually via `run_script` + `replaceText` across all 7 files), then re-copy main → snapshots and re-append each snapshot's init script.** The init snippets are listed at the bottom of this doc.

## Design Concept (tokens)
- **Colors:** page `#f5efe8` (warm cream); surface/cards `#fefcf8`; blush `#f3e8e4`; cream-2 `#efe6dd`; ink/indigo `#12174f`; accent/burgundy `#8b273e` (hover `#a8324c`); muted text `#857d89`; muted-2 `#a59ba6`; hairlines `#e7ddd5`/`#efe7e2`; green (positive/“better”) `#1f7a4d`.
- **Font:** `--font: "ABC Schengen A Variable Unlicensed Trial","ABC Schengen A Unlicensed Trial","Hanken Grotesk",system-ui,…` — the trial font is NOT embedded (no file uploaded); renders only if installed locally, else falls back to Hanken Grotesk. If user provides a .woff2/.otf, embed via @font-face (weights 400–900).
- **Radii:** sm 10, md 14, lg 20, xl 28, pill 999. **Shadows:** soft indigo-tinted (`--sh-md: 0 8px 24px rgba(18,23,79,.06)`).
- **Type:** heavy grotesque display 800–900 very tight; body 400; numbers (times/prices) treated as display. Min 24px on slides (n/a here).

## Principle: indigo by default, burgundy for the single highest-intent action / active+selected state. Cream canvas → white rounded cards → soft shadows. Warm hairlines, never grey.

## Flow (in Flight Search Results.html)
Header (indigo bar) → Search card (2 directions, swap, promo, pax, currency, burgundy “Обновить поиск”) → Trip summary (Туда/Обратно blush cards) → results H1 + “Информация перед полётом” (info icon, right-aligned) → **date strip** (active=burgundy, disabled greyed) → **filter bar** (Прямые рейсы, Без багажа, **Сортировка** dropdown) → **4 flight cards** (HY 202 direct, HY 404 1-stop, HY 77 3-stop, HY 2222 4-stop) each with big indigo times, stop nodes w/ hover tooltips, two airline roundels for interline, and **Economy/Business mini fare cards** (gradient: eco cream→grey/navy price, biz blush/maroon price; number on one line, “UZS” below).

### Modals / interactions
1. **Flight detail modal** — opens from “Показать детали рейса”; segment cards (airline roundel, aircraft, amenities meal/pet green=own flight, grey+carrier-link for partner), vertical timeline, layover bars; footer two fare buttons (“Выбрать тариф Economy/Business”). Both that button AND the mini Economy/Business cells on each flight card open the **fare grid**.
2. **Fare grid modal** — cabin toggle Economy/Business. **Economy = 5 fares** (Акция w/ green ribbon, Saver, Lite, Smart, Comfort). **Business = 2 fares only (PRO, ELITE)**. Rows: Ручная кладь / Багаж / Выбор места / Апгрейд класса / Переоформление / Возврат (icons green=included, maroon=not). Price + expandable breakdown + “Выбрать” (Smart = disabled “Нет мест” / greyed). Class label (“Class B”) color `#12174f`. Choosing a fare → stores selection; outbound first, then return leg, then auto-opens confirm.
3. **Sort dropdown** — grouped radios (По цене/времени вылета/прилёта/пересадкам/длительности/перевозчику), burgundy radios, “Применить” (marks chip active for non-default). Resting state opacity:1 (animation only transforms — capture tool can’t render fixed overlays; verify via eval).
4. **Round-trip confirm modal** (“Подтвердите выбранные билеты”) — opens after both legs’ fares chosen. Two columns Туда/Обратно. Each: card header (flight no + roundel + “Рейс Uzbekistan Airways” + burgundy pencil at far right; NO “Сведения о маршруте”), route strip (big times, IATA, **“Общее время в пути …” above the green connector line, level with times**, stop label for layovers), then TWO detailed fare cards:
   - LEFT “Ваш тариф” (selected): full rows, grey disabled “Выбран” button, no green.
   - RIGHT “Выгодное предложение” (upgrade): **always resolves to a fully-equipped fare** (Comfort for any economy, then Business PRO→ELITE) so it never shows “—”/“за доплату”. Improvements vs selected are highlighted **green**: Ручная кладь count, Багаж count, Выбор места “Бесплатный”, Апгрейд класса points (shown as small sub-line UNDER the label, grey if “нет”/green if better), improved Переоформление/Возврат tiers. Footer: “+ N UZS” + dark “Выбрать” (applies upgrade to that leg, recomputes, refreshes).
   - **Pencil** → closes confirm, reopens that leg’s fare grid with its cabin pre-selected; re-picking updates only that leg and returns to confirm (other leg preserved).
   - Tier rows (в день вылета/перед вылетом/после вылета) are `white-space:nowrap` — single line.
   - Legs stack to 1 column below 1100px (prevents card clipping).

## Assets / icons provided by user (baked into source as inline SVG)
- **Logo:** Uzbekistan Airways wordmark + green roundel with plane (in header `.logo` and confirm card `.cf-ai`).
- Custom icons swapped in: baggage (suitcase), seat/“Выбор места”, info icon (for “Информация перед полётом”, right-aligned), trash/list icon on search top block. Note: the “Выбор места” icon the user supplied looks like cutlery — flagged but left as given.

## Known caveats / open items
- ABC Schengen font not embedded (needs file from user).
- Header nav 5th item “Открытые данные” was OCR’d from a screenshot with low confidence — confirm text.
- Intermediate airport names for multi-stop routes (Караганда/Сочи/Наманган) and partner carriers are illustrative placeholders.
- Public file URLs for Figma import expire ~1 hour.

## Snapshot init scripts (append before final `</script>` after re-copying main)
- Fares Open: `window.currentFlight=FLIGHTS[1]; openFareModal('economy');`
- Detail Open: `openModal(FLIGHTS[1]);`
- Return Leg: `window.leg='outbound'; window._pick={nm:'Comfort',cls:'Class B',price:'6 000 000 UZS',cabin:'economy'}; selectFare(FLIGHTS[1],'Comfort Class B','6 000 000 UZS');`
- Both Selected: sets sumOut+sumRet selected via selSummary + chooseFare(card0…) — see file.
- Sort Open: `document.getElementById('sortMenu').classList.add('open');`
- Confirm Open: sets outSel via Lite (FLIGHTS[1]) then retSel via Saver (RFLIGHTS[0]) through `selectFare`.

## Verification habit
After edits: `done` on main → `fork_verifier_agent` with a directed task. Fixed overlays (modals/dropdowns) don’t render in the screenshot tool — verify their contents via `eval_js` DOM queries, not screenshots.
