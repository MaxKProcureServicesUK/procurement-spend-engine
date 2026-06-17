SPIE — Spend Procurement Intelligence Engine

Version: Demo V3 / KHC 2024–25

Status: MVP — Ready for KHC demonstration

Maintainer: PSL Product Team Weds 17th June 2026


What is SPIE?

SPIE is a portable spend intelligence platform delivered as a single self-contained HTML file. It transforms a raw invoice export (Workday, SAP, Oracle, COINS) into a full analytics suite that runs entirely in the browser — no server, no database, no installation, no internet connection required.

Open the file. That's it.


Files

FilePurposeSPIE_KHC_2425.htmlKHC 2024–25 full dataset · real supplier names · client delivery onlySPIE_Demo_V3.htmlKHC 2024–25 dataset · anonymised suppliers · safe for prospect demosSPIE-demo.htmlClean base template with synthetic data · use this as the rebuild source for new clientsCategory_Mapping_file.xlsxCompanies House SIC taxonomy mapping · 1,600 suppliersREADME.mdThis file


⚠️ SPIE_KHC_2425.html contains real client data. Keep in a private repository. Never commit to a public repo.




Architecture

Single-file delivery

Everything — data, charts, taxonomy, logic, branding — is embedded in one .html file (~1.4MB). The file is completely self-contained:


Chart.js 4.4.1 — embedded inline (no CDN)
Google Fonts — removed; falls back to Segoe UI / system-ui
No blob URLs — all exports use data: URIs for Edge/file:// compatibility
No external requests — works offline, from SharePoint, from email


Script block structure

The file has 3 <script> blocks:

BlockContents0Chart.js 4.4.1 (inline, 205KB)1AP_DATA, CARBON_DATA, legacy initAP, navV helper2All SPIE logic: DS, AP, buildCharts, extensions, initAPInsight

Data objects

DS.summary    — total spend, lines, suppliers, sites, noPoPct, co2, avgLine
DS.months     — ['2024-09', '2024-10', ...] full YYYY-MM format (16 months)
DS.months_short — ['09','10',...] MM format for chart labels
DS.monthly    — [{m:'2024-09', v:2106.52}, ...] monthly spend
DS.monthly25  — [spend per month array]
DS.sups       — 1,358 supplier entries (see fields below)
DS.sites      — 51 site entries {code, spend, lines, sups, co2, pct}
DS.sectors    — 18 CH category entries {name, v25, v24, co2, cls}

AP.totalLines, noPOLines, exactDupLines, exactDupValue
AP.outstandingBalance, outstandingLines
AP.roundInvoiceCount, roundInvoiceValue
AP.singleUseSuppliers, singleUseSpend
AP.monthly25  — [lines per month, 16 values]
AP.credits25  — [credit £ value per month, 16 values]
AP.hours      — [{h:0, v:2964}, ...] 24-entry hour distribution
AP.dow        — [{d:'Mon', v:51412}, ...] day-of-week distribution
AP.tx_bands   — {labels:[...], v25:[...]} transaction value bands
AP.peak_hour_pct, AP.zero_vat_pct24, AP.zero_vat_pct25

DS.sups fields

json{
  "name": "Brakes Bros Ltd",
  "spend": 46003693.5,
  "co2": 9660.8,
  "lines": 18234,
  "sites": 47,
  "po_pct": 82.3,
  "sic": "G",
  "psl_cat": "Wholesale & Retail Trade",
  "budget_code": "Food",
  "scope": "Scope 3",
  "pct": 24.63
}

CH Taxonomy

javascript// SIC section letter → CH Supplier Category
var CH_CAT_MAP = {
  'F': 'Construction',
  'G': 'Wholesale & Retail Trade',
  'Q': 'Human Health & Social Work Activities',
  // ... 18 categories total
};

// Helper — always use this, never s.psl_cat directly
function getChCat(s) { return CH_CAT_MAP[s.sic] || 'Other'; }


Navigation Tabs

Tabnav() idData sourceKey featuresOverviewoverviewDS.summary + DS.monthly6 KPI tiles · monthly spend trend · site selectorSpend by SectorsectorsDS.sups → grouped by CH CategoryDonut · bar · sortable table · 4 taxonomy filtersSupplier SignalssuppliersDS.sups (all 1,358)Sortable table · CH Category/Description/SIC/Scope filtersYear-on-YearyoyDS.sectors + AP_DATASector movement · transaction bandsAP AnalysisapAP object6 KPI tiles · 5 charts · operational signalsAP InsightapinsightAP + DS.supsAging balance · DPO table · No-PO exposure · exception donut · cycle timeCarbon / Scope 3carbonDS.sups co2CO₂e by sector and supplierSites & DeptssitesDS.sites (51)Ranked table · bar · donut · scatterRaw DatarawdataDS.sups (1,358 rows)Sortable · filterable · CSV exportDecisionsdecisionsDS.summaryAction log · programme tracker


Key Functions

javascript// Navigation
nav(id, el)                    // Switch to view, rebuild charts
navV(id, el, idx)              // Alias used by some sidebar items

// Filtering
getActiveFraction()            // Site selection fraction (0–1)
getDateFraction()              // Date range fraction (0–1)
getActiveSectors()             // DS.sectors scaled by site fraction
getActiveTotal()               // Total spend for selected sites

// Taxonomy
getChCat(s)                    // SIC letter → CH Supplier Category
initTaxFilters()               // Populate all taxonomy dropdowns from DS.sups

// Tables (all use DS.sups, respect filters + sort state)
buildSecTable()                // Sectors breakdown table (t-sectors)
buildSupTaxTable()             // Supplier signals table (t-suppliers)
buildFilteredRawTable()        // Raw data table (raw-tbody)

// AP
initAP()                       // Build AP Analysis tab
initAPInsight()                // Build AP Insight tab

// Sort state (independent per table)
SEC_SORT  = {col:null, dir:-1} // Sectors table
SUP_SORT  = {col:null, dir:-1} // Supplier Signals table
RAW_SORT  = {col:null, dir:-1} // Raw Data table

// Formatter
fMg(v)                         // £ money formatter — always use this in extensions


Building a New Client Version


Start from SPIE-demo.html — this is the clean base. Never modify it directly; copy it first.
Prepare the data — from the client's invoice export, build:

DS.sups array (one entry per supplier, fields as above)
DS.sites array (one entry per site)
DS.sectors array (derived from DS.sups grouped by CH category)
DS.summary object
DS.monthly array (YYYY-MM format — critical, must be full year format)
AP object (all fields listed above)



Critical: guard the sector remap — the demo has a line that remaps DS.sectors from DS.cats. If DS.cats is empty (it usually is in client builds), this wipes your sector data. The guard is already in place:


javascript   if(DS.cats && DS.cats.length > 0) { DS.sectors = DS.cats.map(...); }


Replace DS and AP blocks in block 2 using a build script (Python recommended).
Rebuild the raw-tbody with all suppliers as static HTML rows.
Update the version label — search for the tb-brand div and update the text.
Test in Edge from file:// — open F12 console, click every tab, check for errors.



Known Issues & Gotchas

IssueCauseFixBlank sector/YOY chartsDS.sectors wiped by DS.cats.map() at runtimeGuard with DS.cats.length > 0 checkCharts render at 0×0buildCharts() called before browser layoutDouble requestAnimationFrame in nav()Canvas already in useNo destroy before re-creating chartChart.getChart(el)?.destroy() before new Chart()fM is not a functionconst fM only scoped to overview blockUse fMg() — the global formatter in extensionsSEC_FILTER is undefinedExtension JS not loadedExtension block must come after init callfile:// security warning in EdgeBrowser behaviour for local filesHarmless console warning; disappears when served from any web serverAP signals blankAP.peak_hour_pct missing from AP objectEnsure all AP fields are populated including peak_hour_pct, zero_vat_pct24/25


GitHub Setup

bash# Clone the repo
git clone https://github.com/your-org/psl-spie.git
cd psl-spie

# Copy files in
cp SPIE_Demo_V3.html .
cp SPIE-demo.html .
cp Category_Mapping_file.xlsx .
cp README.md .

# Commit
git add .
git commit -m "SPIE V3 — KHC MVP release"
git push origin main

Repository structure recommendation:

psl-spie/              ← public or internal repo
├── SPIE_Demo_V3.html  ← safe for GitHub Pages
├── SPIE-demo.html     ← clean base template
├── Category_Mapping_file.xlsx
└── README.md

psl-spie-clients/      ← private repo (never public)
└── KHC/
    └── SPIE_KHC_2425.html

GitHub Pages (free hosting): Repository Settings → Pages → Source: main branch → root. SPIE_Demo_V3.html becomes live at your-org.github.io/psl-spie/SPIE_Demo_V3.html instantly.


Data Provenance — KHC 2024–25

MetricValueSourceKingsley Healthcare Workday invoice exportPeriodSep 2024 – Dec 2025 (16 months)Raw rows307,821 invoice linesValid rows (GBP, Approved/Paid)296,408Total spend£186.8MSuppliers1,358Sites51No-PO rate15.0%Day-1 processing72.6%Outstanding balance£2.42MCredits£11.47MTaxonomy match1,351 / 1,358 (99%)Top supplierBrakes Bros Ltd — £46M (Food / Wholesale & Retail)


PSL · Spend Procurement Intelligence Engine · June 2026
