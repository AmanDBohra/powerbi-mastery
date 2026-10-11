# Power BI — Project-Ready in 6 Hours

**From complete beginner → production dashboard developer**
Built around ONE continuous project: **E-Commerce Sales & Executive Analytics**

> Tuned to serve BI/Power BI Developer, Data Analyst, AND Data Engineer goals from one document — the modeling/DAX/RLS depth a developer needs, the storytelling/visualization an analyst needs, and the DirectQuery/Databricks/refresh/gateway depth an engineer needs.

**How to read this:** Every concept follows the same spine — *analogy → plain explanation → visual → Power BI concept → steps → project example → your exercise → common mistakes → production usage → cheat line.* Work the exercises.

---

## 0. YOUR 6-HOUR ROADMAP

```
HOUR 1  Foundations + Connect + Power Query intro
HOUR 2  Cleaning + Transformation + Data Modeling + Relationships
HOUR 3  DAX fundamentals + Measures + Filter Context
HOUR 4  Advanced DAX + Time Intelligence + KPIs + What-If
HOUR 5  Dashboard design + Visuals + Interactivity + Performance
HOUR 6  Security (RLS) + Refresh + Gateway + Deployment + Capstone
```

### The ONE mental model (memorize this)

```
BUSINESS REQUIREMENT
      ↓
KPI DEFINITION        ← agree what the number MEANS before building
      ↓
DATA SOURCES
      ↓
POWER QUERY           ← clean & shape (the kitchen)
      ↓
DATA MODEL            ← star schema, facts + dimensions
      ↓
RELATIONSHIPS
      ↓
DAX MEASURES          ← business logic
      ↓
VALIDATION            ← reconcile against source. ALWAYS.
      ↓
VISUALS → REPORT
      ↓
PERFORMANCE → SECURITY (RLS)
      ↓
PUBLISH → REFRESH → MONITOR → SUPPORT
```

> **Golden rule:** The dashboard is only the final presentation layer. A pretty dashboard on a wrong model or a wrong KPI definition is *worse* than no dashboard — people trust it and make bad decisions. You are learning to be the person who gets the numbers *right*.

### Project architecture you will build

```
                        ┌──────────────┐
                        │   DIM_DATE    │
                        └──────┬───────┘
                               │ (1)
  ┌────────────┐   (1)         ▼ (*)        (*)   ┌────────────┐
  │DIM_CUSTOMER │────────▶ ┌──────────┐ ◀────────│ DIM_PRODUCT │
  └────────────┘           │FACT_SALES│          └────────────┘
  ┌────────────┐   (1)     └────┬─────┘
  │ DIM_REGION  │──────────────▶│
  └────────────┘               ...
                    ┌───────────┴───────────┐
                    ▼ (*)                    ▼ (*)
             ┌────────────┐           ┌────────────┐
             │FACT_RETURNS│           │FACT_TARGET │
             └────────────┘           └────────────┘
```
`(1)` = one side, `(*)` = many side. Dimensions filter facts.

---
---

# HOUR 1 — FOUNDATIONS, CONNECTING DATA, POWER QUERY

## 1.1 What is Power BI?
**Analogy.** A restaurant: raw ingredients arrive (data), the kitchen cleans and preps (Power Query), the recipe organizes ingredients into dishes (model + DAX), plating makes it appealing (visuals), the waiter serves diners (Service / sharing). Power BI is the whole restaurant.

**Plain explanation.** A BI platform to **connect**, **clean/transform**, **model**, **calculate** (DAX), **visualize**, and **share** — with refresh and security for production.
```
DATA → CONNECT → CLEAN → MODEL → ANALYZE → VISUALIZE → SHARE
```
**Where it sits (matters for the DE goal):** source systems → database/SQL → warehouse/lakehouse (Databricks, Synapse, BigQuery) → **Power BI** (connect to curated/gold tables) → business users. As a DE you give Power BI a clean "gold" layer so the BI layer stays thin.

## 1.2 The ecosystem
| Component | What it is | Plain words |
|---|---|---|
| **Desktop** | Free Windows app where you build | Your workshop |
| **Service** | Cloud to publish, schedule refresh, share, secure | The showroom |
| **Mobile** | iOS/Android | Consume on the go |
| **Gateway** | Bridge from Service to on-prem/private data | A secure tunnel |
| **Workspace** | Collaborative container | A shared project folder |
| **Report** | Interactive, multi-page, built in Desktop | The analysis |
| **Dashboard** | Service-only, single page, pinned tiles | The one-screen overview |
| **Semantic model** | Model: tables + relationships + measures | The brain |
| **Dataflow** | Reusable Power Query in the cloud | Shared prep |

> Key confusion: in **Desktop** you build **Reports** on a **Semantic model**. A **Dashboard** is a Service-only thing (pinned tiles). Know the difference for interviews.

## 1.3 Desktop — the five areas
```
REPORT VIEW → build visuals · DATA VIEW → inspect rows · MODEL VIEW → relationships
POWER QUERY (Transform Data) → clean before load · DAX → measures/columns
```

## 1.4 Connecting to data
```
DATA SOURCE → (connector) → POWER QUERY → MODEL
```
Common: Excel, CSV, Folder, SQL Server, PostgreSQL, **Azure/Databricks**, Synapse, SharePoint, Web/API, Dataverse. **Habit:** always click **Transform Data**, not Load, on first connect.

## 1.5 Import vs DirectQuery (interview favorite)
| Feature | Import | DirectQuery |
|---|---|---|
| Data in model | Yes (compressed) | Mostly no |
| Speed | Usually very fast (in-memory) | Depends on source + network |
| Freshness | Needs refresh | Near-live |
| Feature support | Full | Some restricted |
| Load on source | Low | High (every interaction) |

**Choose:** Import (default, 90%); DirectQuery (too big / near-real-time / don't copy the data); Composite (import small dims, DirectQuery the huge fact). Databricks SQL warehouse → common DirectQuery target; performance then depends on warehouse sizing + query tuning.

## 1.6 Power Query — the data prep layer
Every click becomes an **Applied Step** (a repeatable recipe); refresh re-runs all steps.
```
SOURCE → PROMOTE HEADERS → CHANGE TYPES → REMOVE COLUMNS → FILTER ROWS
   → RENAME → MERGE/APPEND → OUTPUT (Close & Apply)
```

## 1.7 Data types (wrong types break everything)
```
TEXT "100" → can't reliably sum.   NUMBER 100 → calculable.   real DATE → enables time intelligence
```
**Set types early and explicitly.** #1 silent bug: a sales amount as Text, or a date as Text so YTD/YoY silently fail. Don't trust auto-detect.

## 1.8 Core transformations
Promote headers · rename · remove columns ("Remove Other Columns" is resilient) · remove/filter rows · replace values · change type · split/merge columns · fill down/up · trim/clean · extract · group by (changes grain) · pivot/unpivot.

## 1.9 Merge vs Append (maps to SQL)
**MERGE = join side-by-side = SQL JOIN** (Left/Inner/Right/Full/Anti — Anti finds orphans).
**APPEND = stack = SQL UNION ALL** (12 monthly files → one table; Folder connector auto-appends).
> Mistakes: forgetting to **expand** after a merge; merge exploding rows because the lookup key isn't unique.

## 1.10 Unpivot (the BI reshaping skill)
```
BEFORE wide:  Product | Jan | Feb | Mar      AFTER long:  Product | Month | Sales
```
BI wants one measure column + attribute columns. Use "Unpivot Other Columns" so new months auto-handle.

## 1.11 M language — read it, don't memorize it
`let … in`, each step references the previous by name, case-sensitive, different from DAX. Read it to debug, tweak a filter, or parameterize a path.

### ✅ Hour 1 checkpoint & exercise
Connect the 10 project CSVs, promote headers, set types (amounts numeric, dates Date), append 3 monthly sales files into one `Sales` query.

---
---

# HOUR 2 — CLEANING, MODELING, RELATIONSHIPS

## 2.1 Data modeling is the real skill
Over half of "wrong number" bugs are modeling, not DAX. Get this right and DAX becomes easy.

## 2.2 Fact vs Dimension (supermarket)
Receipt = **FACT** (events, many rows, numbers+keys). Aisle signs/labels/calendar = **DIMENSIONS** (describe, unique key).

## 2.3 Star schema (almost always)
Central fact; dimensions radiate out; each joins directly to the fact. Matches VertiPaq, filters flow one-way, DAX is simpler. **The single most important pattern.**

## 2.4 Snowflake
Dimensions normalized into sub-dimensions → more joins, more confusion. **Prefer star**; flatten snowflakes in Power Query. Engineers from 3NF: *unlearn* the urge to normalize here.

## 2.5 PK / FK
PK uniquely identifies each dimension row (no dupes/blanks); FK is the fact column pointing back. Build relationships PK (one) ↔ FK (many). Non-unique "dimension" key → wrong totals; dedupe dimensions in Power Query.

## 2.6 Relationships & cardinality
~95% are **one-to-many (1:\*)** dimension→fact. 1:1 rare; \*:\* use a bridge table, carefully.

## 2.7 Cross-filter direction
Default and keep **single** (dimension → fact). Bidirectional creates ambiguity (two filter paths) and hurts performance — use deliberately, not as a fix-all.

## 2.8 Active vs Inactive (role-playing dates)
OrderDate/ShipDate/DeliveryDate → three links to DimDate, only one **active**. Activate another in a measure:
```DAX
Sales by Ship Date = CALCULATE([Total Sales], USERELATIONSHIP(DimDate[Date], Sales[ShipDate]))
```

## 2.9 Date table (mandatory)
Time intelligence needs a dedicated, continuous, **marked** Date table (one row/day, full range). Turn off Auto date/time.
```DAX
DimDate = ADDCOLUMNS(CALENDAR(DATE(2022,1,1), DATE(2026,12,31)),
   "Year", YEAR([Date]), "MonthNumber", MONTH([Date]), "MonthName", FORMAT([Date],"MMM"),
   "Quarter","Q"&FORMAT([Date],"Q"), "FiscalYear", IF(MONTH([Date])>=4, YEAR([Date]), YEAR([Date])-1))
```
Then **Mark as Date Table**. Sort MonthName by MonthNumber.

## 2.10 Grain — prevents wrong numbers
Grain = what ONE fact row represents (order? line? daily snapshot?). FACT_SALES is usually line-level → count orders with `DISTINCTCOUNT(Sales[OrderID])`, not row count. FACT_TARGET is month×region grain — relate both to shared dimensions, never directly to line-level sales.

## 2.11 Model validation checklist
Unique dimension keys? relationships correct (PK↔FK)? orphan fact rows (Left-Anti merge to find)? duplicate dimensions? cardinality 1:\*? single filter direction? grain understood?

### ✅ Hour 2 checkpoint & exercise
Model the project — DimDate/Customer/Product/Region + FactSales (line grain), FactReturns, FactTarget (month×region). Single-direction dims→FactSales; relate FactTarget to DimDate(month)+DimRegion only. Find orphan sales rows with a Left-Anti merge.

---
---

# HOUR 3 — DAX FUNDAMENTALS, MEASURES, FILTER CONTEXT

## 3.1 What DAX is
Formula language for calculations/business logic; works on **tables and columns**, not cells. No cell references — you think in terms of *the whole column, filtered by what the user is looking at*.

## 3.2 Measure vs Calculated Column (THE distinction)
```
CALCULATED COLUMN  per-row, stored (RAM), row context  → use to slice/filter/group BY it
MEASURE            query-time, on demand, filter context → use for aggregations/KPIs (default)
```
Need a number that reacts to filters? → **Measure**. Need a static attribute to group by (e.g. Price Band)? → **Calculated Column**. *Default to measures.*

## 3.3 First measures
```DAX
Total Sales = SUM(Sales[SalesAmount])
Total Quantity = SUM(Sales[Quantity])
Average Sale = AVERAGE(Sales[SalesAmount])
```
Put measures in a dedicated "_Measures" table.

## 3.4 Core aggregations
| Function | Does | Mistake |
|---|---|---|
| SUM | adds a column | summing text |
| SUMX | iterates rows, sums an expression | using SUM when per-row math needed |
| COUNT/COUNTROWS | counts values / rows | — |
| DISTINCTCOUNT | unique values | using COUNT (counts dupes) |
| AVERAGE | mean | averaging an average |
| DIVIDE | safe division | using `/` (div/0 errors) |

## 3.5 SUM vs SUMX
```DAX
Revenue = SUMX(Sales, Sales[Quantity] * Sales[UnitPrice])
```
`SUMX` evaluates per row (row context) then sums. `SUM(Qty)*SUM(Price)` is dead wrong. "X = eXecute per row."

## 3.6 DIVIDE
```DAX
Profit Margin = DIVIDE([Total Profit], [Total Sales], 0)   -- alt result on 0/blank
```

## 3.7 Variables (VAR/RETURN)
```DAX
Profit Margin = VAR Revenue=[Total Sales] VAR Profit=[Total Profit] RETURN DIVIDE(Profit,Revenue)
```
Readability, no recompute, and `RETURN` a variable to debug.

## 3.8 Filter context (the heart)
A measure computes over *the slice currently in effect* (slicers + visual rows/cols + page filters). Same measure, different cell → different number. **Internalizing this is the difference between a beginner and someone who can debug.**

## 3.9 Row context
"The current row" — in calculated columns and iterators (SUMX, FILTER). Does NOT auto-filter the model. **Context transition** (via CALCULATE / a measure inside an iterator) turns "this row" into a filter.

## 3.10 CALCULATE (most powerful)
```DAX
Sales 2025 = CALCULATE([Total Sales], DimDate[Year] = 2025)
Sales West = CALCULATE([Total Sales], DimRegion[RegionName] = "West")
```
Adds/replaces/removes filters. Read as: *"compute this, but under these filter conditions."*

## 3.11 FILTER
```DAX
High Value Sales = CALCULATE([Total Sales], FILTER(Sales, Sales[SalesAmount] > 1000))
```
Prefer the simple `CALCULATE([m], Col=value)` form for equality (faster). Use FILTER only for complex row-wise conditions. `FILTER(BigFactTable, …)` is a top performance killer.

## 3.12 ALL / REMOVEFILTERS
```DAX
% of Total Sales = DIVIDE([Total Sales], CALCULATE([Total Sales], ALL(Sales)))
```
Ignore filters to get a grand total to compare against.

## 3.13 ALLEXCEPT
```DAX
Sales % within Category = DIVIDE([Total Sales], CALCULATE([Total Sales], ALLEXCEPT(Product, Product[Category])))
```

## 3.14 DISTINCTCOUNT
```DAX
Customers = DISTINCTCOUNT(Sales[CustomerID])
Orders    = DISTINCTCOUNT(Sales[OrderID])
```

## 3.15 Time intelligence (needs a marked Date table)
```DAX
Sales YTD = TOTALYTD([Total Sales], DimDate[Date])
Sales PY  = CALCULATE([Total Sales], SAMEPERIODLASTYEAR(DimDate[Date]))
YoY Growth % = DIVIDE([Total Sales]-[Sales PY], [Sales PY])
Sales MTD = TOTALMTD([Total Sales], DimDate[Date])
Rolling 3M = CALCULATE([Total Sales], DATESINPERIOD(DimDate[Date], MAX(DimDate[Date]), -3, MONTH))
```
Fiscal: `TOTALYTD([m], DimDate[Date], "3/31")`.

## 3.16 Build the KPI layer (definition → formula → validation)
Total Sales, Total Cost (`SUMX(Sales, Sales[Quantity]*RELATED(Product[UnitCost]))`), Total Profit, Margin %, Orders, Customers, AOV, Return Amount, Return Rate %, Target, Variance, Achievement %. **Always pair a KPI with a validation source** — reconcile to SQL.

## 3.17 DAX debugging flow
Business definition agreed? → data right at source? → relationships correct? → what's the filter context here? → break into VARs and RETURN each → test on a tiny hand-calc slice.

### ✅ Hour 3 checkpoint & exercise
Build every KPI, then prove `Orders ≠ COUNTROWS(Sales)` on the data and explain why.

---
---

# HOUR 4 — ADVANCED DAX, TIME INTELLIGENCE, KPI LOGIC, WHAT-IF

## 4.1 Advanced toolkit
CALCULATE, FILTER, ALL, REMOVEFILTERS, ALLSELECTED (% of visible), VALUES, HASONEVALUE, SELECTEDVALUE, SWITCH, IF, COALESCE, RANKX, TOPN.

## 4.2 RANKX
```DAX
Product Rank = RANKX(ALL(Product[ProductName]), [Total Sales], , DESC, Dense)
```
`ALL(...)` gives the full list to rank across (without it every row ranks 1 — classic bug).

## 4.3 TOP N
```DAX
Sales Top 10 Products = CALCULATE([Total Sales], TOPN(10, ALL(Product[ProductName]), [Total Sales], DESC))
```
Often a visual-level Top N filter is simpler; use measure-based TOPN when it feeds other calculations.

## 4.4 SWITCH (clean categorization)
```DAX
Margin Band = SWITCH(TRUE(),
  [Profit Margin] >= 0.30, "Excellent",
  [Profit Margin] >= 0.20, "Good",
  [Profit Margin] >= 0.10, "Average", "Poor")
```

## 4.5 SELECTEDVALUE & dynamic titles
```DAX
Report Title = "Sales — " & SELECTEDVALUE(DimRegion[RegionName],"All Regions") & " — " & SELECTEDVALUE(DimDate[Year],"All Years")
```

## 4.6 Conditional / status measures
```DAX
Target Status = VAR a=[Target Achievement %] RETURN SWITCH(TRUE(), a>=1,"✅ On/Above", a>=0.9,"🟡 Close", "🔴 Below")
```

## 4.7 What-If analysis
*Modeling → New parameter → Numeric range* makes a table + slicer + a `Value` measure. Example Price Increase % 0–20%:
```DAX
Simulated Revenue = SUMX(Sales, Sales[Quantity] * Sales[UnitPrice] * (1 + [Price Increase % Value]))
```
Turns a report into a decision tool.

### ✅ Hour 4 checkpoint & exercise
Add Product/Customer Rank, a Margin Band, a dynamic title, and a Price-Increase What-If showing simulated revenue & profit.

---
---

# HOUR 5 — DASHBOARD DESIGN, VISUALS, INTERACTIVITY, PERFORMANCE

## 5.1 A dashboard is a decision interface, not a chart gallery
Every element earns its place by helping someone decide or act.
```
TOP = Executive KPIs · MIDDLE = Trends & comparisons · BOTTOM = Details & diagnostics
```

## 5.2 Design principles
Clarity (one message per visual) · hierarchy (important top-left, Z-pattern) · consistency (same colors mean the same thing) · minimalism (kill gridlines/borders/decimal noise) · business labels · visual storytelling.

## 5.3 Executive page layout
```
┌─────────── E-COMMERCE SALES PERFORMANCE ───────────┐  ← dynamic title + date slicer
│ Revenue │ Profit │ Margin% │ Orders │ Customers      │  ← KPI cards (+ YoY ▲▼)
├─────────────────────────────────────────────────────┤
│            SALES TREND (line, actual vs PY)          │
├──────────────────────┬──────────────────────────────┤
│ SALES BY REGION (bar)│ SALES BY CATEGORY             │
├──────────────────────┴──────────────────────────────┤
│   TOP 10 PRODUCTS (bar)   │ TARGET vs ACTUAL         │
└─────────────────────────────────────────────────────┘
```

## 5.4 Visual types — WHEN TO USE WHICH
Trend→line · compare→bar · single KPI→card/KPI · vs target→KPI/bullet · part-to-whole (few)→stacked/donut · composition over time→stacked area · distribution→histogram/box · relationship→scatter · records→table · 2 dims→matrix · hierarchy size→treemap · geography→map · cumulative→waterfall · stages→funnel. **Avoid pie >3–4 slices.**

## 5.5 Slicers
```
USER picks a filter → filter context changes → measures recompute → every visual updates
```
Keep slicers few; use the Filters pane for the rest; consider Sync slicers across pages.

## 5.6 Interactions
Click a bar → others cross-filter/highlight. Control via **Format → Edit interactions** (filter/highlight/none).

## 5.7 Drill-down vs Drill-through
Drill-down = deeper in a hierarchy within a visual (Year→Day). Drill-through = jump to a filtered detail page (set a Drill-through filter field; a Back button is auto-created).

## 5.8 Tooltip pages & 5.9 Bookmarks
Tooltip pages show rich hover context. Bookmarks capture a state (filters, visibility, selection) for show/hide panels, toggle chart⇄table, storytelling, reset buttons. Pair with the Selection pane.

## 5.10 Buttons & navigation / 5.11 Mobile / 5.12 Accessibility
Buttons with Page navigation = an app-like nav rail. Build a phone **Mobile layout**. Accessibility: contrast, never color alone (add ▲▼/text), alt text, readable fonts, logical tab order, colorblind-safe palettes.

## 5.13 Data storytelling
```
WHAT happened (↓8% PY) → WHERE (West ↓15%) → WHAT within (Electronics ↓22%)
→ WHO/WHAT (Product X ↓30%, stockout) → SO WHAT / DO (expedite restock; review West pricing)
```

## 5.14 Performance optimization
```
SLOW REPORT → MODEL (size, cardinality, star?) → DATA (types, pre-aggregate) → DAX (iterators/FILTER/context transitions) → VISUALS (count, custom visuals) → SOURCE (DirectQuery tuning)
```
Rules: star schema; reduce high cardinality (split datetime, drop unique IDs, reduce precision); remove unused columns/tables; measures over columns; keep FILTER off huge tables; use variables. **Measure with Performance Analyzer, don't guess.**

### ✅ Hour 5 checkpoint & exercise
Build the executive page, a Customer drill-through page, a region tooltip page; run Performance Analyzer and report the slowest visual + one fix.

---
---

# HOUR 6 — SECURITY, REFRESH, GATEWAY, DEPLOYMENT, PRODUCTION

## 6.1–6.3 Desktop → Service · Workspaces · Report vs Dashboard
Publish → workspace → validate → share via **App**. Workspace roles (least→most): Viewer, Contributor, Member, Admin. Report = multi-page interactive (Desktop). Dashboard = Service single-page pinned tiles.

## 6.4 Refresh
Manual / Scheduled (Pro ≤8/day, Premium/Fabric ≤48). Import needs refresh; DirectQuery is live. Cloud sources refresh with stored creds; on-prem/private needs a **gateway**.

## 6.5 Gateway
```
ON-PREM / PRIVATE DB → DATA GATEWAY → SERVICE → REPORT
```
Standard (shared/prod) vs Personal. Map sources, store creds, monitor, cluster for HA.

## 6.6 Row-Level Security (RLS)
**Static:** role with a filter (`[RegionName]="West"`). **Dynamic:**
```DAX
[RegionName] = LOOKUPVALUE(UserRegion[Region], UserRegion[Email], USERPRINCIPALNAME())
```
Define in Desktop (*Manage roles*), test with **View as**, assign users in Service. **Hiding a visual is NOT security.** OLS hides tables/columns.

## 6.7 Security design (defense in depth)
Workspace access · App audience · model permissions · **RLS** · sensitivity labels · **least privilege**. A user with model-edit rights can bypass report tricks — RLS on the model is the real boundary.

## 6.8 Deployment lifecycle
```
DEVELOPMENT → TEST → UAT → PRODUCTION
```
Separate workspaces; (Premium/Fabric) Deployment Pipelines to promote with rules (swap data source to prod on promotion).

## 6.9 Version / change management
`.pbix` is binary (hard to diff) → use **.pbip / PBIR** + Git. Keep a change log; document KPI/measure definitions. Treat BI artifacts like code.

## 6.10 Requirement gathering
Ask: business problem? decisions? audience? KPIs & exact definitions? filters/drill paths? refresh frequency? latency tolerance? who sees what (security)? source of truth? expected numbers? acceptance criteria?

## 6.11 KPI definition (never build blindly)
"Revenue" — gross? net of discount/returns? pre/post tax? "Active customer" — last 30/60/90 days or ever? **A technically perfect dashboard with a wrong KPI definition is WRONG.** Pin definitions in writing, get sign-off.

## 6.12 Data validation / reconciliation
```
SOURCE → SQL/expected → POWER BI → COMPARE → investigate difference
```
Usual culprits: filters dropped rows · fan-out duplicates · relationship direction · orphan data · returns included or not · date/timezone boundaries.

## 6.13 Master troubleshooting flow
```
REPORT WRONG → Source? → Power Query? → Model? → Relationships? → DAX (filter context!)? → Filters? → Visual config?
```
Most "wrong totals" = relationship/grain. Most "wrong YTD/YoY" = date table.

### ✅ Hour 6 checkpoint & exercise
Implement dynamic RLS (each regional manager sees only their region, test with "View as"), schedule a daily refresh, reconcile Total Sales to a SQL `SUM`.

---
---

# CAPSTONE — E-COMMERCE EXECUTIVE ANALYTICS (16 tickets)

**Brief:** Build an executive e-commerce sales analytics solution: revenue, profit, margin, orders, customers, target achievement, regional/product/customer performance, returns, monthly trends, YoY. Filter by date/region/category/product. Regional managers see only their region. Auto-refresh. Production-optimized.

- **T001 Requirements (BRD):** objective, audience, 17 KPIs (each defined), filters, drill-downs, security, refresh, sources, outputs, acceptance criteria (numbers reconcile; RLS verified; refresh green; loads < 5s).
- **T002 Data discovery:** classify facts/dimensions, keys/grain, expected DQ issues (trailing spaces, text dates, dup customers, cancelled orders, orphans); source→target map.
- **T003 Power Query:** load, promote headers, types, trim/clean keys, remove unused, remove cancelled, dedupe Customer/Product, replace "N/A"→null, append monthly, unpivot targets; build DimDate.
- **T004 Model:** star; all dims→FactSales (1:\*, single dir); DimDate+DimRegion→FactTarget; DimDate+DimProduct+DimCustomer→FactReturns; mark DimDate; inactive DimDate→Sales[ShipDate].
- **T005 DAX KPI layer:** the 17 measures in display folders; each reconciles to source.
- **T006–T010:** Executive, Sales, Customer, Product, Region pages.
- **T011 Drill-through:** Exec→Region→Product→Customer detail pages with Back buttons.
- **T012 RLS:** dynamic via UserRegion + USERPRINCIPALNAME(); test every role.
- **T013 Performance:** audit model/cardinality/relationships/DAX/visual count; optimize; page load < 5s.
- **T014 Refresh:** source creds (+ gateway if private); scheduled daily; failure alerts.
- **T015 UAT checklist:** revenue/profit match source? YTD/YoY correct? region filter? RLS per role? drill-through? refresh? mobile? sign-off.
- **T016 Production release:** Dev→Test→UAT→approval→Prod (deployment pipeline), then monitoring.

---
---

# SURVIVAL KIT — CHEAT SHEETS

**Architecture:** Desktop (build) → Service (publish/share/secure/refresh) → Mobile. Gateway bridges to private data. Report = multi-page interactive; Dashboard = Service single-page tiles.
**Power Query:** promote headers → set types early → trim/clean → remove unused → filter → dedupe dims → Merge (join) / Append (stack) → Unpivot wide → Close & Apply.
**Modeling:** Fact=events, Dimension=descriptors, Grain=what one row means. Build a **star**; unique dimension keys.
**Relationships:** 1:\* single-direction, dimension→fact. One active per pair (USERELATIONSHIP). Avoid bidirectional. \*:\* → bridge.
**DAX:** Measure (on-demand, filter context) vs Calculated column (per-row, stored). Default to measures. SUM adds; SUMX iterates; DIVIDE safe-divides; DISTINCTCOUNT for uniques.
**Filter context:** the slice a measure computes within. Debug every wrong number by asking "what's filtering this?"
**CALCULATE:** evaluate under modified filter context — engine behind time intelligence, % of total, KPIs.
**Time intelligence:** TOTALYTD, SAMEPERIODLASTYEAR, PREVIOUSMONTH, DATESINPERIOD. Needs a marked, continuous Date table. Fiscal → pass year-end date.
**Advanced DAX:** ALL/REMOVEFILTERS, ALLSELECTED, ALLEXCEPT, VALUES, HASONEVALUE/SELECTEDVALUE, SWITCH(TRUE(),…), RANKX/TOPN (mind ALL scope).
**Visualization:** trend→line, compare→bar, KPI→card, vs target→KPI/bullet, records→table, 2 dims→matrix, geo→map, cumulative→waterfall. Avoid pie >3–4 slices.
**Dashboard:** KPIs top, trends middle, detail bottom. Business labels. Story: WHAT→WHERE→WHY→DO.
**Performance:** star schema; cut unused columns; reduce cardinality; measures over columns; FILTER off huge tables; variables; fewer visuals; Performance Analyzer, don't guess.
**Service:** publish to workspace; roles Viewer/Contributor/Member/Admin; distribute via App.
**Refresh/Gateway:** Import needs refresh; DirectQuery live; private sources need a gateway.
**RLS:** Static or Dynamic (USERPRINCIPALNAME + mapping). Manage in Desktop, test "View as", assign in Service. Hiding ≠ security.
**Deployment:** Dev→Test→UAT→Prod; .pbip + Git; document changes.
**Troubleshooting:** Source → Power Query → Model → Relationships → DAX → Filters → Visual. Reconcile to source.

## "WHEN TO USE WHAT"
| Requirement | Feature |
|---|---|
| Clean data | Power Query |
| Join tables | Merge |
| Stack tables | Append |
| Reshape columns | Pivot / Unpivot |
| Business calculation | DAX Measure |
| Row-level derived value | Calculated Column |
| Trend | Line |
| Comparison | Bar |
| KPI | Card / KPI |
| Detail | Table / Matrix |
| User filtering | Slicer |
| Detail navigation | Drill-through |
| User-specific data | RLS |
| Data update | Refresh |
| On-prem access | Gateway |
| Slow report | Performance Analyzer + model optimization |

## DAX TEMPLATES
```DAX
Total Sales   = SUM(Sales[SalesAmount])
Orders        = DISTINCTCOUNT(Sales[OrderID])
Revenue       = SUMX(Sales, Sales[Quantity]*Sales[UnitPrice])
Margin %      = DIVIDE([Total Profit],[Total Sales],0)
Sales PY      = CALCULATE([Total Sales], SAMEPERIODLASTYEAR(DimDate[Date]))
Sales YTD     = TOTALYTD([Total Sales], DimDate[Date])
YoY %         = DIVIDE([Total Sales]-[Sales PY], [Sales PY])
Product Rank  = RANKX(ALL(Product[ProductName]), [Total Sales],, DESC, Dense)
% of Total    = DIVIDE([Total Sales], CALCULATE([Total Sales], ALL(Sales)))
```

## CONFUSION TABLE
SUM vs SUMX (add column vs per-row then add) · COUNTROWS vs DISTINCTCOUNT (rows vs unique) · Column vs Measure (stored vs on-demand) · Row vs Filter context · CALCULATE vs FILTER (modifies context vs returns a table) · ALL vs ALLSELECTED (ignore all vs respect selection) · Import vs DirectQuery (snapshot vs live).

---
# PRODUCTION CHECKLIST
```
[ ] Requirements & KPIs confirmed   [ ] Numbers reconciled to source
[ ] Sources validated               [ ] Visuals intentional, uncluttered
[ ] Data types correct              [ ] RLS tested (every role)
[ ] Star schema + relationships      [ ] Performance checked (<5s)
[ ] Date table + DAX tested          [ ] Refresh tested
[ ] PQ transforms documented         [ ] Deployed to prod + monitored
```

# FINAL READINESS SCORECARD (/200, be honest)
```
Fundamentals /10 · Power Query /10 · M /10 · Modeling /10 · Relationships /10 · Star /10 ·
DAX Fundamentals /10 · Advanced DAX /10 · Time Intelligence /10 · Visualization /10 ·
Dashboard Design /10 · Validation /10 · Performance /10 · Service /10 · Refresh /10 ·
Gateway /10 · RLS /10 · Deployment /10 · Troubleshooting /10 · Business Analysis /10
0–80 Beginner · 81–120 Learning · 121–145 Junior · 146–165 Project Ready · 166–185 Strong · 186–200 Advanced
```

# FINAL MENTAL MODEL
```
REQUIREMENT → KPI DEFINITIONS → SOURCE → FACT+DIMENSIONS → GRAIN → STAR SCHEMA
→ POWER QUERY → RELATIONSHIPS → DATE TABLE → DAX → VALIDATION → DASHBOARD
→ DRILL-THROUGH → RLS → PERFORMANCE → PUBLISH → REFRESH → UAT → PRODUCTION → MONITOR
```
Given *"Build an exec dashboard showing sales/profit/margin/target/regional-product-customer performance, YoY and returns; regional managers see only their region; refresh daily"* — you run the reflex above, validate the numbers, and explain it to the business.
