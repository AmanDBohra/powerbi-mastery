# Power BI — Visual Mind-Map Mastery

**Think like a BI developer & consultant — not a button-clicker.**
Companion to the structured course (`02-project-ready-course.md`). This one is built the way you learn best: **mind maps · analogies · tiny datasets · memory hooks · before/after · active recall.** Every concept uses the same 12-point pattern. Work the exercises and the recall prompts out loud.

> **The one belief that drives everything:** *Power BI turns data into decisions.* Never ask "what chart should I make?" first. Ask "what business question am I answering?"

---

## 🧠 MASTER MAP (recreate this from memory by the end)

```
                           POWER BI
                              │
                        BUSINESS QUESTION
                              ↓
          ┌───────────────────┼───────────────────┐
          ↓                   ↓                   ↓
         DATA                MODEL               REPORT
       Prepare             Connect             Explain
     (Power Query)     (Star + Relationships) (Visuals/KPIs)
          └───────────────────┼───────────────────┘
                              ↓
                             DAX
                              ↓
          INSIGHT → DECISION → ACTION
                              ↓
          PUBLISH → SECURE → REFRESH → MONITOR
```

## 🧠 THE MINDSET MAP (run this on every requirement)

```
BUSINESS QUESTION
   ↓ What do we need to know?
   ↓ What data do we need?
   ↓ How should it be modeled?
   ↓ What calculations are required?
   ↓ What visual shows it best?
   ↓ What does the data tell us?
   ↓ What decision should be made?
   ↓ What action should follow?
```

## 🧠 THE PIPELINE (your most-used picture)

```
SOURCE → POWER QUERY → DATA MODEL → RELATIONSHIPS → DAX
      → VISUALS → REPORT → DASHBOARD → PUBLISH → SHARE → REFRESH → MONITOR
```
**Memory:** *Connect → Clean → Model → Calculate → Visualize → Share → Refresh.*

## 🧠 DATA → ACTION LADDER (the purpose of BI)

```
DATA         Raw facts            "West, Laptop, 1000"
 ↓
INFORMATION  Organized            "West revenue = 1200"
 ↓
INSIGHT      Meaning              "West is down 15% YoY"
 ↓
DECISION     Choice               "Investigate West"
 ↓
ACTION       What happens         "Launch West intervention"
```
**Memory:** *Data is noise. Power BI makes it a sentence.*

---
---

# PART 1 — DATA PREP (POWER QUERY)

## 🌍 The three worlds of Power BI

```
POWER BI
   ├── POWER QUERY  → PREPARE the data   (kitchen prep)
   ├── DATA MODEL   → CONNECT the data   (the recipe structure)
   └── REPORT / DAX → ANALYZE & COMMUNICATE (cooking + plating)
```
**Memory:** *Power Query prepares · Model connects · DAX calculates · Report communicates.*

---
## CONCEPT · POWER QUERY

**1. WHAT** The place you clean and shape data *before* analysis.
**2. WHY** Real data is dirty — nulls, duplicates, wrong types, inconsistent text. Garbage in = wrong dashboard.
**3. PICTURE**
```
DIRTY DATA → POWER QUERY → CLEAN DATA
```
**4. ANALOGY** Kitchen prep: wash, cut, remove waste, standardize — *then* you cook.
**5. TINY EXAMPLE**
```
Before:  Region      Sales
         "West "     "1000"     ← trailing space, text number
         west        1000
After:   West        1000  (trimmed, proper case, numeric)
```
**6. DEMO** Get Data → Transform Data → Trim, Capitalize, Change Type.
**7. RESULT** "West " and "west" now both = "West" → they group correctly.
**8. REAL PROJECT USE** Standardize region names so regional totals aren't split across 3 spellings.
**9. COMMON MISTAKE** Loading first, cleaning later in DAX. Clean upstream, in Power Query.
**10. MEMORY** *Power Query = the kitchen. Nothing good happens without prep.*
**11. EXERCISE** Given a column with "Mumbai", "mumbai ", "MUMBAI", make them one value.
**12. RECALL** Why clean in Power Query instead of DAX?

### 🗺️ Power Query master map
```
CONNECT → INSPECT → CLEAN → TRANSFORM → COMBINE → VALIDATE → LOAD
```
### 🗺️ Cleaning map
```
RAW DATA → CHECK → { NULLS · DUPLICATES · TYPES · ERRORS · DATES · TEXT } → CLEAN DATA
```
**Toolbox:** remove duplicates · replace nulls · change type · rename · split · merge · filter rows · remove errors · handle dates · trim/clean text · add custom column.

### Merge vs Append (never confuse these)
```
MERGE  = join side-by-side (horizontal) = SQL JOIN
         CUSTOMER + CUSTOMER_DETAILS on ID → one wider table
APPEND = stack on top (vertical) ≈ SQL UNION ALL
         Jan file + Feb file → one long table
```
**Memory:** *Merge widens (join). Append lengthens (stack).*

### Power Query vs DAX (critical)
```
POWER QUERY = prepare ingredients   (done once, on refresh)
DAX         = calculate the recipe  (done live, per filter)
VISUAL      = serve the result
```
Example: *remove duplicate customers* = Power Query. *Total sales* = DAX.

### M language — just enough
UI clicks generate **M** code (`let … in`, each step feeds the next). Don't memorize M — learn to **read** it to debug and tweak. It's a different language from DAX.

---
---

# PART 2 — DATA MODELING (the real skill)

> The model decides whether your report is *correct*. More wrong numbers come from bad models than bad DAX.

```
RAW TABLES → DATA MODEL → RELATIONSHIPS → DAX → REPORT
```

## 🃏 FACT vs DIMENSION
```
FACT      = what happened?      (events: Sales, Orders, Payments)  many rows, numbers+keys
DIMENSION = who/what/where/when? (descriptors: Customer, Product, Date, Region) unique key
```
**Analogy — supermarket:** the **receipt** = fact (event). The **aisle signs, product labels, calendar** = dimensions (describe).
**Memory:** *Fact = verbs. Dimension = nouns.*

```
FACT SALES                 DIM PRODUCT
Date, CustomerID,          ProductID (PK),
ProductID, RegionID,       Name, Category, Brand
Quantity, Revenue, Cost
```

## ⭐ STAR SCHEMA (permanent mental image)
```
                    DIM DATE
                       │
DIM CUSTOMER ─── FACT SALES ─── DIM PRODUCT
                       │
                   DIM REGION
```
Fact in the center; dimensions around it; each dimension joins straight to the fact.
**Memory:** *Star = fact in the center, dimensions as the points.*

**Why star?** (teach through pain)
```
BAD model:  wrong totals · ambiguous filters · slow · confusing DAX · duplicate data
STAR model: correct calcs · simple DAX · fast · easy to maintain
```
**Snowflake** = dimensions split into sub-dimensions (Product → Category → Dept). More joins, more confusion → *flatten to a star* unless there's a strong reason. (Engineers from 3NF warehouses: unlearn the urge to normalize here.)

## 🔗 RELATIONSHIPS & CARDINALITY
```
DIM CUSTOMER (CustomerID, PK)
      │ 1
      │ → many
      ▼ *
FACT SALES (CustomerID, FK)
```
One customer → many sales rows. ~95% of relationships are **one-to-many (1:\*)** from dimension to fact.
```
1 : 1   one-to-one      (rare)
1 : *   one-to-many     (the workhorse)
* : *   many-to-many    (handle with a bridge table; careful)
```
**Memory:** *Relationship = how tables talk. PK (unique, dimension) ↔ FK (repeats, fact).*

## 💧 FILTER DIRECTION (water flows downhill)
```
DIMENSION  ──filters──▶  FACT     (single direction = default, safe)
DIMENSION ◀──filters──▶  FACT     (both = bidirectional = use sparingly)
```
Keep it **single** (dimension → fact). Bidirectional can create two filter paths → ambiguous/wrong results and slower reports. "Turn on both until the number looks right" is an anti-pattern.

## 🎭 ACTIVE vs INACTIVE (role-playing dates)
Sales has OrderDate, ShipDate, DeliveryDate → three links to DIM DATE, but only **one active** (solid line). Activate another inside a measure:
```DAX
Sales by Ship Date = CALCULATE([Total Sales], USERELATIONSHIP(DimDate[Date], Sales[ShipDate]))
```

## 📅 DATE TABLE (mandatory — don't skip)
```
DIM DATE: Date · Year · Quarter · Month · MonthNumber · Week · Day · FiscalPeriod
```
Why: time intelligence (YTD, YoY) needs a dedicated, continuous, **marked** date table (one row per day, full range). Turn off Auto date/time. Sort MonthName by MonthNumber (or Apr sorts before Jan alphabetically — classic beginner bug).
```DAX
DimDate = ADDCOLUMNS(CALENDAR(DATE(2022,1,1), DATE(2026,12,31)),
   "Year", YEAR([Date]), "MonthNo", MONTH([Date]), "Month", FORMAT([Date],"MMM"),
   "Quarter","Q"&FORMAT([Date],"Q"))
```
Then **Mark as Date Table**.

## ✅ MODEL CHECKLIST (model first, visuals second)
```
✓ Fact identified        ✓ Cardinality correct      ✓ Data types correct
✓ Dimensions identified  ✓ Filter direction single  ✓ No unused columns
✓ Unique dimension keys  ✓ Date table exists+marked ✓ No unused tables
✓ Relationships correct  ✓ Grain understood (one row = ?)
```
**Grain** = what ONE fact row means (one order? one line? one daily snapshot?). Get it wrong → double-counting. FACT_TARGET is usually month×region grain — never relate it to line-level sales directly; relate both to shared dimensions.

---
---

# PART 3 — DAX (calculations)

```
DATA → QUESTION → CALCULATION → DAX
```
**DAX in one sentence:** *DAX tells Power BI how to calculate a business metric within the current context.* That phrase — **current context** — is the whole art.

## 🗺️ DAX master map
```
DAX
 ├── MEASURES              (on-demand KPIs)
 ├── CALCULATED COLUMNS    (per-row, stored)
 ├── CALCULATED TABLES
 ├── AGGREGATION  (SUM, COUNT, DISTINCTCOUNT, AVERAGE)
 ├── FILTERING   (CALCULATE, FILTER, ALL)
 ├── LOGIC       (IF, SWITCH, DIVIDE)
 ├── TIME INTELLIGENCE
 └── CONTEXT     (filter context, row context, transition)
```

## 🃏 MEASURE vs CALCULATED COLUMN (essential)
```
CALCULATED COLUMN = a value for EVERY ROW, computed once & stored (uses RAM)
                    → use when you need to slice/group/filter BY it
MEASURE           = computed WHEN THE REPORT ASKS, reacting to filters (not stored)
                    → use for aggregations/KPIs → DEFAULT TO THIS
```
**Memory:** *Column = row-level & stored. Measure = report-level & live.* Overusing columns = Excel-brain; bloats the model.

## Basic DAX
```DAX
Total Sales   = SUM(Sales[SalesAmount])
Total Cost    = SUM(Sales[Cost])
Profit        = [Total Sales] - [Total Cost]
Profit Margin = DIVIDE([Profit], [Total Sales], 0)   -- DIVIDE = safe, no div/0 error
Revenue       = SUMX(Sales, Sales[Quantity]*Sales[UnitPrice])  -- per-row math then add
Orders        = DISTINCTCOUNT(Sales[OrderID])        -- unique, not row count
```
**SUM vs SUMX:** `SUM` adds an existing column; `SUMX` goes row-by-row, computes, then adds. Memory: *"X = eXecute per row."* `SUM(Qty)*SUM(Price)` is WRONG; `SUMX(Sales, Qty*Price)` is right.

## ⭐ CALCULATE (the most important function)
Evaluates an expression *after changing the filter context.*
```DAX
West Sales = CALCULATE([Total Sales], Sales[Region] = "West")
```
It can **add**, **replace**, or **remove** filters. It's the engine behind time intelligence, % of total, KPIs. **Memory:** *CALCULATE = "compute this, but under these conditions."*

## 🎯 FILTER CONTEXT (the heart of DAX)
The *same* measure returns a different number in each cell, because each cell is a different slice. **This is why one measure powers a whole report** — and why nearly every "wrong number" is a filter-context question.

## ⏳ TIME INTELLIGENCE
```DAX
Sales YTD   = TOTALYTD([Total Sales], DimDate[Date])
Sales LY    = CALCULATE([Total Sales], SAMEPERIODLASTYEAR(DimDate[Date]))
YoY Growth% = DIVIDE([Total Sales]-[Sales LY], [Sales LY])
Rolling 3M  = CALCULATE([Total Sales], DATESINPERIOD(DimDate[Date], MAX(DimDate[Date]), -3, MONTH))
```
`SAMEPERIODLASTYEAR` shifts the date filter back exactly one year. Fiscal year: `TOTALYTD([m], DimDate[Date], "3/31")`. **Needs a marked, continuous date table** or it silently breaks.

## 🧰 Advanced DAX
```
ALL / REMOVEFILTERS  → ignore filters (→ grand total for % of total)
ALLSELECTED          → respect what the user selected (% of visible)
ALLEXCEPT            → remove filters but keep a grouping (% within category)
SWITCH(TRUE(), …)    → clean multi-branch banding (beats nested IF)
RANKX / TOPN         → ranking (always pair RANKX with ALL(...) or everything ranks 1)
SELECTEDVALUE        → single selection (dynamic titles)
```

---
---

# PART 4 — VISUALIZATION & STORY

> First question is never "which chart is pretty?" It's **"what question is this visual answering?"**

## 🗺️ Visualization mind map
```
WHAT DO I WANT TO SHOW?
 ├── TREND over time     → LINE
 ├── COMPARE categories  → BAR / COLUMN
 ├── RANKING             → BAR (sorted)
 ├── COMPOSITION         → STACKED / 100%
 ├── RELATIONSHIP        → SCATTER
 ├── SINGLE KPI          → CARD / KPI
 ├── ACTUAL vs TARGET    → KPI / bullet
 ├── DETAIL              → TABLE / MATRIX
 └── GEOGRAPHY           → MAP
```
**When NOT to use pie:** more than ~3–4 slices, or similar-sized slices → use a bar.

## 📇 KPI design (a number needs context)
```
KPI = VALUE + TARGET + VARIANCE + TREND + STATUS
Revenue  ₹125M   Target ₹120M   +4.2% ↑   🟢
```

## 🔎 Drill-down vs Drillthrough vs Tooltip vs Bookmark
```
DRILL-DOWN   = deeper in a hierarchy, same visual:  Year → Quarter → Month → Day
DRILLTHROUGH = jump to a detail PAGE for the clicked item
TOOLTIP      = extra info on hover (can be a mini report page)
BOOKMARK     = saved report state (nav, show/hide, toggle table⇄chart)
```

## 🏛️ Report design rule
> A dashboard **answers questions**, it doesn't display everything.
```
KPI → TREND → DRIVERS → DETAIL → ACTION
Top = what's happening · Middle = why · Bottom = what to do.
```

## 📖 DATA STORYTELLING
> *"Revenue hit ₹120M — 4% above target — driven by West; but Product A fell 12% and is the main risk next quarter."*
```
DATA → PATTERN → INSIGHT → WHY → IMPLICATION → ACTION
```

## ⚡ Performance mind map
```
SLOW REPORT → CHECK: DATA (rows/cardinality) · MODEL (star?) · DAX (iterators/FILTER) · REPORT (too many visuals)
```
Rules: star schema · drop unused columns · measures over columns · keep FILTER off huge tables · use VARs · fewer visuals · **measure with Performance Analyzer, don't guess.**

## Import vs DirectQuery vs Composite
```
IMPORT:      compressed snapshot in-memory → fast, full features, needs refresh (default)
DIRECTQUERY: query the source live → always current, big data/governance
COMPOSITE:   import small dims + DirectQuery huge fact (hybrid)
```
Databricks tip: DirectQuery to a SQL warehouse keeps data in the lakehouse; performance then depends on warehouse sizing + query tuning.

---
---

# PART 5 — SHARE, SECURE, PRODUCTION

```
POWER BI DESKTOP → BUILD → PUBLISH → SERVICE → SHARE / COLLABORATE / REFRESH
```

## 🗺️ Service mind map
```
WORKSPACE (collaboration)
   ├── SEMANTIC MODEL  (the analytical brain reports use)
   ├── REPORTS         (interactive analysis)
   ├── DASHBOARDS      (service-only, pinned tiles)
   └── DATAFLOWS       (reusable cloud Power Query)
→ package as an APP → distribute to USERS
```
**Report vs Dashboard:** Report = detailed, multi-page, interactive, built in Desktop. Dashboard = single-page, pinned tiles, Service-only.

## 🔄 Refresh & 🌉 Gateway
Import needs refresh (Pro ≤8/day, Premium/Fabric ≤48); DirectQuery is live. Private/on-prem sources need a **gateway** — a secure bridge from the cloud Service to data it can't reach directly.

## 🔐 Row-Level Security (RLS)
```
USER → ROLE → FILTER → ALLOWED DATA
```
**Static:** role with a hard filter (`[Region]="West"`). **Dynamic:** one role filtered by the logged-in user:
```DAX
[Region] = LOOKUPVALUE(UserRegion[Region], UserRegion[Email], USERPRINCIPALNAME())
```
Manage roles in Desktop, test with **View as**, assign users in Service. **Hiding a visual is NOT security** — RLS filters the data itself.

## 🚦 Deployment
```
DEV → TEST → UAT → PRODUCTION   (separate workspaces / deployment pipelines)
```
Use .pbip + Git for source control; document KPI definitions. **Treat BI like code.**

## ✅ VALIDATION (never trust a pretty visual)
```
SOURCE ₹100M → TRANSFORM ₹100M → MODEL ₹100M → DAX ₹100M → VISUAL ₹100M
```
If any step differs → investigate BEFORE publishing. Usual culprits: PQ dropped rows · relationship fan-out (duplicates) · wrong grain · date/timezone boundaries.

---
---

# 🎓 CAPSTONE — EXECUTIVE SALES & PROFITABILITY DASHBOARD

**Requirement:** *"The CEO wants to understand sales, profit, growth, regional/customer/product performance, and major risks."*

**Build order (your reflex):**
```
1 Requirements & KPI definitions
2 Power Query: load → inspect → clean → standardize → merge → append → validate → load
3 Model: star → DimDate/Customer/Product/Region + FactSales/Returns/Target, mark date table
4 DAX: Total Sales, Cost, Profit, Margin, Orders, Qty, AOV, Target, Variance, Achievement%,
       Sales LY, YoY%, Sales YTD, Ranks
5 Pages: Executive · Sales · Profit · Customer · Product · Region · Detail · Admin/DQ
6 Interactivity: slicers, drill-down, drillthrough, tooltip pages, bookmarks
7 RLS: regional managers see own region; Exec sees all
8 Validate: reconcile every KPI to source
9 Performance: Performance Analyzer, trim model
10 Publish → workspace → test → security → App → users → monitor
```

## ✅ Production checklist
```
✓ Requirements & KPIs confirmed   ✓ Numbers reconciled      ✓ RLS tested (every role)
✓ Sources validated               ✓ Visuals intentional      ✓ Refresh tested
✓ Star schema + relationships      ✓ Performance checked       ✓ Deployed to prod
✓ Date table + DAX tested          ✓ Documentation created
```

---
# 📄 ONE-PAGE CHEAT SHEET
```
Power Query = prepare data        CALCULATE  = change filter context
Fact        = what happened?      FILTER     = reduce data analyzed
Dimension   = who/what/where/when Slicer     = user-controlled filter
Star schema = fact center + dims  Drill-down = deeper in hierarchy
Relationship= how tables connect  Drillthrough = go to detail page
Measure     = dynamic calculation Import     = data loaded in
Calc column = row-level value      DirectQuery= query source live
Grain       = one row = what?      Gateway    = bridge to on-prem data
Filter ctx  = current slice        RLS        = users see different data
Time intel  = YTD/YoY/etc          Report=analysis · Dashboard=monitoring
```

# 🌳 DECISION TREE
```
Data dirty? → POWER QUERY     Structure wrong? → DATA MODEL
Business calc? → DAX MEASURE  Trend? → LINE   Compare? → BAR
Single KPI? → CARD/KPI        Filter? → SLICER   Detail? → DRILL-THROUGH
Different users, diff data? → RLS   On-prem? → GATEWAY
Report slow? → MODEL → DAX → VISUALS
```

# ❓ THE 10 QUESTIONS (before building anything)
```
1 What business question?   6 Where's the data from?
2 Who's the audience?        7 What model shape?
3 What decisions follow?     8 What calculations?
4 What KPIs?                 9 What visual communicates it?
5 How is each KPI defined?  10 How will we validate & maintain?
```

*Pair this with the project-ready course doc. The dashboard is just the presentation layer — the real skill is QUESTION → MODEL → MEASURE → VALIDATE → STORY → SECURE → SHARE.*
