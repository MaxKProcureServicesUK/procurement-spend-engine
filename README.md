# procurement-spend-engine
PSLProcurementSpendEngine
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Procurement Spend Intelligence Engine v0.4</title>
<!--
  CHART.JS LOCAL PACKAGING INSTRUCTIONS (for confidential client delivery)
  -------------------------------------------------------------------------
  This file currently loads Chart.js from CDN:
    https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.js

  To remove the CDN dependency:
  1. Download chart.umd.js from: https://cdn.jsdelivr.net/npm/chart.js@4.4.1/dist/chart.umd.js
  2. Save it alongside this HTML file as: chart.umd.js
  3. Replace the <script src="https://..."> tag below with:
     <script src="chart.umd.js"></script>
  4. The file is then fully self-contained and works offline / air-gapped.
  Note: chart.umd.js is ~200KB and may be distributed with client deliverables
  provided you comply with the Chart.js MIT licence (included in the file header).
-->
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.1/chart.umd.js"></script>
<style>
*,*::before,*::after{box-sizing:border-box;margin:0;padding:0}
:root{
  --bg:#ffffff;--bg2:#f7f7f5;--bg3:#f0efec;
  --text:#1a1a18;--text2:#5f5e5a;--text3:#888780;
  --border:rgba(0,0,0,0.12);--border2:rgba(0,0,0,0.25);
  --r:8px;--rl:12px;
  --blue:#185FA5;--blue-l:#E6F1FB;--blue-m:#B5D4F4;
  --green:#1D9E75;--green-l:#EAF3DE;--green-m:#9FE1CB;
  --amber:#BA7517;--amber-l:#FAEEDA;--amber-m:#FAC775;
  --red:#A32D2D;--red-l:#FCEBEB;--red-m:#F7C1C1;
  --coral:#D85A30;--coral-l:#FAECE7;--coral-m:#F5C4B3;
}
@media(prefers-color-scheme:dark){
  :root{
    --bg:#1e1e1c;--bg2:#2a2a28;--bg3:#333330;
    --text:#f0efe8;--text2:#b4b2a9;--text3:#888780;
    --border:rgba(255,255,255,0.12);--border2:rgba(255,255,255,0.28);
    --blue-l:#0c2a4a;--blue-m:#0c447c;
    --green-l:#0a2a1a;--green-m:#085041;
    --amber-l:#2a1a04;--amber-m:#633806;
    --red-l:#2a0a0a;--red-m:#791f1f;
    --coral-l:#2a1008;--coral-m:#993c1d;
  }
}
body{font-family:-apple-system,BlinkMacSystemFont,'Segoe UI',Helvetica,Arial,sans-serif;background:var(--bg3);color:var(--text);font-size:15px;line-height:1.5;min-height:100vh}

/* TOP BAR */
.topbar{background:var(--bg);border-bottom:0.5px solid var(--border);padding:0 1.5rem;display:flex;align-items:center;justify-content:space-between;height:52px;position:sticky;top:0;z-index:100;flex-wrap:wrap;gap:8px}
.tbl{display:flex;align-items:center;gap:10px}
.logo{width:26px;height:26px;background:var(--blue);border-radius:6px;display:flex;align-items:center;justify-content:center}
.logo svg{width:14px;height:14px;fill:white}
.pname{font-size:13px;font-weight:700;letter-spacing:-0.01em}
.pver{font-size:10px;color:var(--text3);background:var(--bg2);padding:2px 6px;border-radius:100px;border:0.5px solid var(--border)}
.client-badge{font-size:11px;color:var(--blue);font-weight:600;background:var(--blue-l);padding:3px 8px;border-radius:100px;border:0.5px solid var(--blue-m)}
.tbr{display:flex;align-items:center;gap:8px}
.analyst-badge{font-size:10px;color:var(--text3);padding:2px 7px;border-radius:100px;border:0.5px solid var(--border)}

/* LAYOUT */
.shell{display:flex;min-height:calc(100vh - 52px)}
.sidebar{width:210px;flex-shrink:0;background:var(--bg);border-right:0.5px solid var(--border);padding:1.25rem 0;position:sticky;top:52px;height:calc(100vh - 52px);overflow-y:auto}
.sidebar-lbl{font-size:10px;font-weight:700;letter-spacing:0.08em;text-transform:uppercase;color:var(--text3);padding:0 1rem;margin-bottom:3px;display:block}
.nav-item{display:flex;align-items:center;gap:8px;padding:6px 12px;border-radius:var(--r);font-size:13px;color:var(--text2);cursor:pointer;user-select:none;transition:all .12s;margin:1px 6px}
.nav-item:hover{background:var(--bg2);color:var(--text)}
.nav-item.active{background:var(--blue-l);color:var(--blue);font-weight:600}
.nav-item svg{width:14px;height:14px;flex-shrink:0;opacity:.55}
.nav-item.active svg{opacity:1}
.sdiv{height:0.5px;background:var(--border);margin:.75rem 1rem}
.smeta{padding:0 1rem;font-size:11px;color:var(--text3);line-height:1.6}
.smeta b{color:var(--text2);display:block;margin-bottom:2px}

/* MAIN */
.main{flex:1;padding:1.75rem;min-width:0}
.page{display:none}.page.active{display:block}
.ph{margin-bottom:1.5rem}
.ph-title{font-size:20px;font-weight:700;letter-spacing:-0.02em;margin-bottom:3px}
.ph-sub{font-size:13px;color:var(--text2)}

/* METRICS */
.mg{display:grid;grid-template-columns:repeat(auto-fit,minmax(130px,1fr));gap:10px;margin-bottom:1.5rem}
.metric{background:var(--bg);border:0.5px solid var(--border);border-radius:var(--rl);padding:12px 14px}
.metric .lbl{font-size:10px;font-weight:700;color:var(--text3);margin-bottom:5px;letter-spacing:.04em;text-transform:uppercase}
.metric .val{font-size:22px;font-weight:700;letter-spacing:-.02em}
.metric .sub{font-size:11px;color:var(--text3);margin-top:2px}
.metric .warn{font-size:11px;color:var(--coral);margin-top:2px;font-weight:600}
.metric .ok{font-size:11px;color:var(--green);margin-top:2px;font-weight:600}

/* CARDS */
.card{background:var(--bg);border:0.5px solid var(--border);border-radius:var(--rl);padding:1.25rem 1.5rem;margin-bottom:1rem}
.card-title{font-size:13px;font-weight:700;margin-bottom:.75rem;letter-spacing:-.01em}
.two-col{display:grid;grid-template-columns:1fr 1fr;gap:1.25rem}
@media(max-width:720px){.two-col{grid-template-columns:1fr}}
.chart-wrap{position:relative;width:100%}
.chart-lbl{font-size:11px;color:var(--text3);margin-bottom:6px;font-weight:700;letter-spacing:.04em;text-transform:uppercase}

/* PILLS */
.pill{font-size:11px;padding:2px 8px;border-radius:100px;white-space:nowrap;font-weight:600}
.phi{background:var(--coral-l);color:var(--coral)}
.pmd{background:var(--amber-l);color:var(--amber)}
.plo{background:var(--green-l);color:var(--green)}
.pbl{background:var(--blue-l);color:var(--blue)}
.pgr{background:var(--bg2);color:var(--text2);border:0.5px solid var(--border)}
.pdis{background:var(--red-l);color:var(--red);font-weight:700}

/* HEURISTICS */
.heuristic{background:var(--bg);border:0.5px solid var(--border);border-radius:var(--rl);margin-bottom:10px;overflow:hidden}
.hh{display:flex;align-items:center;gap:10px;padding:12px 14px;cursor:pointer;user-select:none;transition:background .12s}
.hh:hover{background:var(--bg2)}
.hnum{width:26px;height:26px;border-radius:50%;display:flex;align-items:center;justify-content:center;font-size:11px;font-weight:700;flex-shrink:0}
.htitle{flex:1;font-size:13px;font-weight:600}
.hmeta{display:flex;gap:5px;flex-wrap:wrap;flex-shrink:0}
.chev{font-size:10px;color:var(--text3);transition:transform .2s;margin-left:2px}
.chev.open{transform:rotate(180deg)}
.hbody{display:none;padding:0 14px 14px;border-top:0.5px solid var(--border)}
.hbody.open{display:block}
.trigger{background:var(--bg2);padding:9px 12px;margin:10px 0;font-size:12px;color:var(--text2);border-left:3px solid var(--blue);border-radius:0 var(--r) var(--r) 0;line-height:1.65}
.trigger b{color:var(--text)}
.bg4{display:grid;grid-template-columns:repeat(auto-fit,minmax(220px,1fr));gap:8px;margin-bottom:10px}
.bl{border-radius:var(--r);padding:11px 13px}
.bl-ev{background:var(--blue-l);border:0.5px solid var(--blue-m)}
.bl-as{background:var(--amber-l);border:0.5px solid var(--amber-m)}
.bl-vl{background:var(--red-l);border:0.5px solid var(--red-m)}
.bl-ts{background:var(--bg2);border:0.5px solid var(--border)}
.bl-title{font-size:10px;font-weight:700;margin-bottom:5px;letter-spacing:.04em;text-transform:uppercase}
.bl-ev .bl-title{color:var(--blue)}
.bl-as .bl-title{color:var(--amber)}
.bl-vl .bl-title{color:var(--red)}
.bl-ts .bl-title{color:var(--text2)}
.bl p,.bl li{font-size:12px;color:var(--text);line-height:1.6}
.bl ul{padding-left:13px}
.hfooter{display:flex;gap:8px;flex-wrap:wrap;padding-top:9px;border-top:0.5px solid var(--border);margin-top:4px;align-items:center}
.saving{font-size:12px;font-weight:700;background:var(--bg2);border-radius:var(--r);padding:3px 9px;border:0.5px solid var(--border)}

/* TABLES */
.tbl-wrap{overflow-x:auto}
table{width:100%;border-collapse:collapse;font-size:12px}
th{text-align:left;padding:8px 10px;font-weight:700;font-size:10px;color:var(--text3);border-bottom:0.5px solid var(--border);letter-spacing:.06em;text-transform:uppercase;white-space:nowrap}
td{padding:8px 10px;border-bottom:0.5px solid var(--border);vertical-align:top}
tr:last-child td{border-bottom:none}
tbody tr:hover td{background:var(--bg2)}
.td-mono{font-family:monospace;font-size:11px}

/* ACTION PLAN */
.ap-table{width:100%;border-collapse:collapse;font-size:12px}
.ap-table th{text-align:left;padding:8px 10px;font-weight:700;font-size:10px;color:var(--text3);border-bottom:0.5px solid var(--border);letter-spacing:.06em;text-transform:uppercase}
.ap-table td{padding:8px 10px;border-bottom:0.5px solid var(--border);vertical-align:top}
.ap-table tbody tr:hover td{background:var(--bg2)}
.ap-table tr:last-child td{border-bottom:none}
.owner-p{color:var(--blue);background:var(--blue-l);padding:1px 6px;border-radius:100px;font-size:10px;font-weight:700;white-space:nowrap}
.owner-f{color:var(--amber);background:var(--amber-l);padding:1px 6px;border-radius:100px;font-size:10px;font-weight:700;white-space:nowrap}
.owner-o{color:var(--green);background:var(--green-l);padding:1px 6px;border-radius:100px;font-size:10px;font-weight:700;white-space:nowrap}

/* EXEC OUTPUT */
.eq{font-size:15px;font-weight:700;margin:1.5rem 0 .5rem;padding-bottom:.5rem;border-bottom:0.5px solid var(--border);letter-spacing:-.01em}
.eq:first-child{margin-top:0}
.eb{font-size:13px;color:var(--text2);line-height:1.75;margin-bottom:.5rem}
.eb b{color:var(--text);font-weight:600}
.prize-g{display:grid;grid-template-columns:repeat(auto-fit,minmax(140px,1fr));gap:10px;margin:1rem 0}
.prize-i{border-radius:var(--r);padding:12px 14px}
.prize-i .pl{font-size:10px;font-weight:700;margin-bottom:3px;letter-spacing:.04em;text-transform:uppercase}
.prize-i .pv{font-size:19px;font-weight:700;letter-spacing:-.02em}
.prize-i .pc{font-size:10px;margin-top:3px}
.act-row{display:flex;gap:12px;padding:12px 0;border-bottom:0.5px solid var(--border)}
.act-row:last-child{border-bottom:none}
.act-n{width:24px;height:24px;border-radius:50%;display:flex;align-items:center;justify-content:center;font-size:11px;font-weight:700;flex-shrink:0;margin-top:1px}
.act-body{flex:1}
.act-title{font-size:13px;font-weight:700;margin-bottom:3px}
.act-desc{font-size:12px;color:var(--text2);line-height:1.6}

/* SLIDES */
.slide{background:var(--bg);border:0.5px solid var(--border);border-radius:var(--rl);padding:1.75rem 2rem;margin-bottom:1rem;position:relative}
.slide-num{position:absolute;top:1.25rem;right:1.5rem;font-size:11px;color:var(--text3);font-weight:700}
.slide-label{font-size:10px;font-weight:700;letter-spacing:.08em;text-transform:uppercase;color:var(--blue);margin-bottom:.5rem}
.slide-title{font-size:22px;font-weight:800;letter-spacing:-.03em;line-height:1.2;margin-bottom:1rem}
.slide-bullets{list-style:none;padding:0}
.slide-bullets li{font-size:13px;color:var(--text2);padding:5px 0 5px 16px;position:relative;line-height:1.6;border-bottom:0.5px solid var(--border)}
.slide-bullets li:last-child{border-bottom:none}
.slide-bullets li::before{content:'—';position:absolute;left:0;color:var(--text3)}
.slide-bullets li b{color:var(--text);font-weight:600}
.slide-note{font-size:11px;color:var(--text3);margin-top:1rem;font-style:italic;padding-top:.75rem;border-top:0.5px solid var(--border)}
.slide-stat-row{display:grid;grid-template-columns:repeat(auto-fit,minmax(120px,1fr));gap:10px;margin:.75rem 0}
.slide-stat{background:var(--bg2);border-radius:var(--r);padding:10px 12px;text-align:center}
.slide-stat .sv{font-size:22px;font-weight:800;letter-spacing:-.02em}
.slide-stat .sl{font-size:10px;color:var(--text3);margin-top:2px;text-transform:uppercase;letter-spacing:.04em;font-weight:700}

/* EMAIL SUMMARY */
.email-box{background:var(--bg);border:0.5px solid var(--border);border-radius:var(--rl);padding:1.5rem 1.75rem;font-size:13px;line-height:1.75;color:var(--text2)}
.email-box h3{font-size:14px;font-weight:700;color:var(--text);margin:1rem 0 .25rem}
.email-box h3:first-child{margin-top:0}
.email-box ul{padding-left:16px;margin:.25rem 0}
.email-box li{margin-bottom:3px}
.email-box b{color:var(--text);font-weight:600}
.email-header{background:var(--bg2);border-radius:var(--r);padding:10px 14px;margin-bottom:1rem;font-size:12px}
.email-header .ef{color:var(--text3)}.email-header .ev{color:var(--text);font-weight:600}

/* DISCLAIMER */
.disclaimer{background:var(--bg2);border-radius:0 var(--r) var(--r) 0;padding:12px 14px;font-size:11px;color:var(--text2);line-height:1.65;border-left:3px solid var(--border2)}
.disclaimer b{color:var(--text);font-weight:700}

/* VERIFICATION */
.check-row{display:flex;align-items:center;gap:10px;padding:8px 0;border-bottom:0.5px solid var(--border);font-size:12px}
.check-row:last-child{border-bottom:none}
.check-ok{color:var(--green);font-weight:700;font-size:14px;flex-shrink:0}
.check-label{flex:1;color:var(--text2)}
.check-val{font-family:monospace;font-size:11px;color:var(--text);font-weight:600}
.check-note{font-size:11px;color:var(--text3);font-style:italic}

/* SANITY */
.sanity-disabled{background:var(--red-l);border:1px solid var(--red-m);border-radius:var(--r);padding:12px 14px;margin-bottom:1rem}
.sanity-title{font-size:12px;font-weight:700;color:var(--red);margin-bottom:5px}
.sanity-body{font-size:12px;color:var(--text);line-height:1.6}

/* FOOTER */
.footer{background:var(--bg);border-top:0.5px solid var(--border);padding:14px 1.5rem;font-size:11px;color:var(--text3);display:flex;align-items:center;justify-content:space-between;flex-wrap:wrap;gap:8px}
.footer b{color:var(--text2)}

/* LEGEND */
.leg{display:flex;flex-wrap:wrap;gap:10px;margin-bottom:8px}
.leg-i{display:flex;align-items:center;gap:4px;font-size:11px;color:var(--text2)}
.leg-sq{width:10px;height:10px;border-radius:2px;flex-shrink:0}

/* PRINT */
@media print{.sidebar,.topbar,.footer{display:none}.shell{display:block}.main{padding:0}.page{display:block!important;page-break-after:always}}
</style>
</head>
<body>

<!-- TOP BAR -->
<div class="topbar">
  <div class="tbl">
    <div class="logo"><svg viewBox="0 0 16 16"><path d="M2 3h12v2H2V3zm0 4h8v2H2V7zm0 4h10v2H2v-2z"/></svg></div>
    <span class="pname">Spend Intelligence Engine</span>
    <span class="pver">v0.4</span>
    <span class="client-badge" id="clientBadge">Client: [CLIENT NAME]</span>
  </div>
  <div class="tbr">
    <span style="font-size:11px;color:var(--text3)" id="datasetLabel">Dataset: [YEAR] · Prepared by PSL</span>
    <span class="analyst-badge">Analyst-operated · Not self-serve</span>
  </div>
</div>

<div class="shell">

<!-- SIDEBAR -->
<div class="sidebar">
  <div style="padding:0 6px 8px">
    <span class="sidebar-lbl">Analysis</span>
    <div class="nav-item active" onclick="showPage('overview',this)">
      <svg viewBox="0 0 16 16" fill="currentColor"><path d="M1 1h6v6H1V1zm0 8h6v6H1V9zm8-8h6v6H9V1zm0 8h6v6H9V9z"/></svg>Overview
    </div>
    <div class="nav-item" onclick="showPage('verify',this)">
      <svg viewBox="0 0 16 16" fill="currentColor"><path d="M13 2L6 9 3 6 1 8l5 5 9-9z"/></svg>Verification checks
    </div>
    <div class="nav-item" onclick="showPage('heuristics',this)">
      <svg viewBox="0 0 16 16" fill="currentColor"><path d="M8 1a7 7 0 100 14A7 7 0 008 1zm1 10.5H7v-2h2v2zm0-4H7V4.5h2V7.5z"/></svg>6 Heuristics
    </div>
    <div class="nav-item" onclick="showPage('logistics',this)">
      <svg viewBox="0 0 16 16" fill="currentColor"><path d="M1 4h10v2H1V4zm0 3h8v2H1V7zm0 3h6v2H1v-2zm13-5l-4 5V5h2V2h2v3z"/></svg>Logistics audit
    </div>
    <div class="nav-item" onclick="showPage('dataquality',this)">
      <svg viewBox="0 0 16 16" fill="currentColor"><path d="M8 1L1 14h14L8 1zm-1 5h2v4H7V6zm0 5h2v2H7v-2z"/></svg>Data quality
    </div>
    <div class="nav-item" onclick="showPage('actionplan',this)">
      <svg viewBox="0 0 16 16" fill="currentColor"><path d="M2 2h12v2H2V2zm0 4h9v2H2V6zm0 4h6v2H2v-2zm11-1l-5 5-2-2 1.4-1.4L13 13.2l3.6-3.6z"/></svg>Action plan
    </div>
    <div class="nav-item" onclick="showPage('executive',this)">
      <svg viewBox="0 0 16 16" fill="currentColor"><path d="M2 2h12v2H2V2zm0 4h12v2H2V6zm0 4h8v2H2v-2z"/></svg>Executive output
    </div>
  </div>
  <div class="sdiv"></div>
  <div style="padding:0 6px 8px">
    <span class="sidebar-lbl">Client deliverables</span>
    <div class="nav-item" onclick="showPage('slides',this)">
      <svg viewBox="0 0 16 16" fill="currentColor"><path d="M1 2h14v10H1V2zm2 2v6h10V4H3zm-2 9h14v1H1v-1z"/></svg>5-slide narrative
    </div>
    <div class="nav-item" onclick="showPage('email',this)">
      <svg viewBox="0 0 16 16" fill="currentColor"><path d="M1 3h14v10H1V3zm1 1v1.5l6 3.5 6-3.5V4H2zm0 3v5h12V7L8 10.5 2 7z"/></svg>Executive summary
    </div>
    <div class="nav-item" onclick="showPage('spendcube',this)">
      <svg viewBox="0 0 16 16" fill="currentColor"><path d="M8 1L1 5v6l7 4 7-4V5L8 1zm0 2.2L13 6.5 8 9.8 3 6.5 8 3.2zM2 7.3l5 2.9V14L2 11.1V7.3zm7 2.9l5-2.9v3.8L9 14V10.2z"/></svg>Spend cube
    </div>
  </div>
  <div class="sdiv"></div>
  <div class="smeta"><b>Current dataset</b>[CLIENT NAME] · [YEAR]<br>18,703 lines · 732 suppliers<br>£59.2M gross spend</div>
</div>

<div class="main">

<!-- ======== OVERVIEW ======== -->
<div class="page active" id="page-overview">
  <div class="ph"><div class="ph-title">Spend overview</div><div class="ph-sub">FY2024 · All verification checks passed · All currencies included · Analyst-reviewed</div></div>
  <div class="mg">
    <div class="metric"><div class="lbl">Total gross spend</div><div class="val">£59.2M</div><div class="sub">FY2024 · all currencies</div></div>
    <div class="metric"><div class="lbl">Invoice lines</div><div class="val">18,703</div><div class="sub">10,065 unique refs</div></div>
    <div class="metric"><div class="lbl">Active suppliers</div><div class="val">732</div><div class="sub">180 one-off (24.6%)</div></div>
    <div class="metric"><div class="lbl">Unclassified spend</div><div class="val">61.1%</div><div class="warn">Primary data gap</div></div>
    <div class="metric"><div class="lbl">Sub-£500 invoices</div><div class="val">53.5%</div><div class="warn">AP cost ~£135K/yr</div></div>
    <div class="metric"><div class="lbl">Zero-VAT spend</div><div class="val">72.4%</div><div class="warn">£42.9M — flag for review</div></div>
    <div class="metric"><div class="lbl">H2 vs H1</div><div class="val">−20%</div><div class="sub">£32.9M vs £26.3M</div></div>
    <div class="metric"><div class="lbl">Price data flags</div><div class="val">480</div><div class="warn">Unit price &lt;£0.01 — disabled</div></div>
  </div>
  <div class="card"><div class="chart-lbl">Monthly spend FY2024 (£ gross)</div><div class="chart-wrap" style="height:210px"><canvas id="monthlyChart"></canvas></div></div>
  <div class="two-col">
    <div class="card"><div class="chart-lbl">Geographic spread (entity type proxy)</div><div class="chart-wrap" style="height:200px"><canvas id="regionChart"></canvas></div></div>
    <div class="card"><div class="chart-lbl">Heuristic map — impact vs. data readiness</div><div class="chart-wrap" style="height:200px"><canvas id="radarChart"></canvas></div></div>
  </div>
</div>

<!-- ======== VERIFICATION ======== -->
<div class="page" id="page-verify">
  <div class="ph"><div class="ph-title">Verification checks</div><div class="ph-sub">Six independent checks run against source data before any analysis. All passed.</div></div>
  <div class="card">
    <div class="check-row"><span class="check-ok">✓</span><span class="check-label">Total gross spend</span><span class="check-val">£59,189,035.91</span><span class="check-note">Pivot sum of "Sum of Gross" · exact match</span></div>
    <div class="check-row"><span class="check-ok">✓</span><span class="check-label">Invoice lines (row count)</span><span class="check-val">18,703</span><span class="check-note">COUNT of all rows in source file · exact match</span></div>
    <div class="check-row"><span class="check-ok">✓</span><span class="check-label">Unique suppliers (DISTINCT SupplierName)</span><span class="check-val">732</span><span class="check-note">DISTINCTCOUNT of SupplierName field · exact match</span></div>
    <div class="check-row"><span class="check-ok">✓</span><span class="check-label">Zero-VAT share (Sum Gross where VAT=0 ÷ total)</span><span class="check-val">72.43%</span><span class="check-note">= £42,872,679 ÷ £59,189,036 · exact match</span></div>
    <div class="check-row"><span class="check-ok">✓</span><span class="check-label">Top 10 supplier share</span><span class="check-val">25.64%</span><span class="check-note">Sum of top 10 by gross ÷ total · exact match</span></div>
    <div class="check-row"><span class="check-ok">✓</span><span class="check-label">Embedded logistics keyword spend</span><span class="check-val">£1,257,184</span><span class="check-note">Description contains: delivery / carriage / freight / transport / shipping · see Logistics Audit tab for breakdown</span></div>
  </div>
  <div class="card" style="margin-top:.5rem">
    <div class="card-title">How to re-run these checks independently</div>
    <table><thead><tr><th>Check</th><th>Excel formula (column letters may vary)</th></tr></thead>
    <tbody>
      <tr><td>Total spend</td><td class="td-mono">=SUM([Sum of Gross])</td></tr>
      <tr><td>Invoice lines</td><td class="td-mono">=COUNTA([SupplierReference])</td></tr>
      <tr><td>Unique suppliers</td><td class="td-mono">=SUMPRODUCT(1/COUNTIF([SupplierName],[SupplierName]))</td></tr>
      <tr><td>Zero-VAT share</td><td class="td-mono">=SUMIF([Sum of Vat],0,[Sum of Gross])/SUM([Sum of Gross])</td></tr>
      <tr><td>Top 10 share</td><td class="td-mono">Pivot table, sort desc, sum top 10 ÷ grand total</td></tr>
      <tr><td>Embedded logistics</td><td class="td-mono">=SUMIF([Description],"*delivery*",...) + repeat for each keyword</td></tr>
    </tbody></table>
  </div>
</div>

<!-- ======== HEURISTICS ======== -->
<div class="page" id="page-heuristics">
  <div class="ph"><div class="ph-title">6 embedded procurement heuristics</div><div class="ph-sub">Each heuristic separates evidence, assumptions, and human validation requirements. No action should be taken without completing the validation steps.</div></div>

  <div class="heuristic">
    <div class="hh" onclick="toggleH(this)">
      <div class="hnum" style="background:var(--blue-l);color:var(--blue)">1</div>
      <div class="htitle">Site-by-site spend behaviour</div>
      <div class="hmeta"><span class="pill phi">Impact: High</span><span class="pill pmd">Effort: M</span><span class="pill pbl">90+ days</span></div>
      <span class="chev">▼</span>
    </div>
    <div class="hbody">
      <div class="trigger"><b>Trigger:</b> 4 geographic regions with no site/BU dimension in data. R&amp;M Europe: 4 name variants (RMFS001/003). Robert Craig: 3 variants. 30% of spend has no entity type signal. Cross-region pricing comparison not possible without FX normalisation.</div>
      <div class="bg4">
        <div class="bl bl-ev"><div class="bl-title">Evidence</div><ul><li>UK/Ireland: £21.4M, 278 suppliers</li><li>Benelux/DACH: £10.7M, 106 suppliers</li><li>Spain/Italy: £5.1M, 61 suppliers</li><li>Nordic/Baltic: £4.5M, 41 suppliers</li><li>R&amp;M Europe: 4 name variants for same code</li></ul></div>
        <div class="bl bl-as"><div class="bl-title">Assumptions</div><ul><li>Entity type used as geography proxy — unreliable</li><li>Same supplier code = same commercial relationship (unconfirmed)</li><li>Regional differences reflect behaviour not volume (unproven)</li></ul></div>
        <div class="bl bl-vl"><div class="bl-title">Validation required</div><ul><li>Request cost centre / site code from ERP</li><li>Confirm name variants are same legal entity</li><li>Determine whether regions share contracts</li><li>Establish FX normalisation approach</li></ul></div>
        <div class="bl bl-ts"><div class="bl-title">Data needed to unlock</div><ul><li>Site/BU field from ERP extract</li><li>FX rate table (EUR/GBP/USD)</li><li>Supplier legal entity master</li></ul></div>
      </div>
      <div class="hfooter"><span class="saving">£0.3M–£1.2M potential — site data required first</span><span class="pill pgr">Cannot size without site dimension</span></div>
    </div>
  </div>

  <div class="heuristic">
    <div class="hh" onclick="toggleH(this)">
      <div class="hnum" style="background:var(--green-l);color:var(--green)">2</div>
      <div class="htitle">Year-on-year spend trend</div>
      <div class="hmeta"><span class="pill pmd">Impact: M</span><span class="pill plo">Effort: L</span><span class="pill pbl">0–30 days</span></div>
      <span class="chev">▼</span>
    </div>
    <div class="hbody">
      <div class="trigger"><b>Trigger:</b> H2 (£26.3M) is 20% below H1 (£32.9M). Q4 is 25.9% below Q1. Week 29 (mid-July) peak at £1.9M. No prior year for baseline. Cause of decline is not visible in the data.</div>
      <div class="bg4">
        <div class="bl bl-ev"><div class="bl-title">Evidence</div><ul><li>Q1: £17.1M → Q4: £12.7M (£4.4M within-year decline)</li><li>July spike then sustained lower spend Aug–Dec</li><li>Delivery charge volumes do not decline proportionally</li><li>No FY2023 data — direction unconfirmed</li></ul></div>
        <div class="bl bl-as"><div class="bl-title">Assumptions</div><ul><li>Decline assumed to be project phasing — not validated</li><li>H1 weighting assumed normal — may be ramp-up</li><li>No seasonality model available</li></ul></div>
        <div class="bl bl-vl"><div class="bl-title">Validation required</div><ul><li>Cross-reference against project delivery schedule</li><li>Confirm H2 decline is genuine, not off-system</li><li>Obtain FY2023 to establish trend direction</li></ul></div>
        <div class="bl bl-ts"><div class="bl-title">Data needed</div><ul><li>FY2023 spend extract (same format)</li><li>Project milestone schedule</li><li>Budget vs. actual report</li></ul></div>
      </div>
      <div class="hfooter"><span class="saving">Diagnostic only — prize depends on cause</span><span class="pill pgr">No prior year — trend unconfirmed</span></div>
    </div>
  </div>

  <div class="heuristic">
    <div class="hh" onclick="toggleH(this)">
      <div class="hnum" style="background:var(--coral-l);color:var(--coral)">3</div>
      <div class="htitle">Supplier consolidation</div>
      <div class="hmeta"><span class="pill phi">Impact: High</span><span class="pill phi">Effort: H</span><span class="pill pbl">90+ days</span></div>
      <span class="chev">▼</span>
    </div>
    <div class="hbody">
      <div class="trigger"><b>Trigger:</b> Cable ties from 15 suppliers. Transport: 146 suppliers for £545K (avg £3.7K). IT &amp; Software: 312 suppliers for £9.1M. Top 10 suppliers = 25.6% of spend. Sector best practice for comparable operations: 60–70% in top 10.</div>
      <div class="bg4">
        <div class="bl bl-ev"><div class="bl-title">Evidence</div><ul><li>15 suppliers for cable ties (single commodity)</li><li>IFS Courier: 992 lines at avg £27.85</li><li>180 one-off suppliers (24.6% of base)</li><li>IT: 312 suppliers, avg £29K each</li><li>Transport: 146 suppliers, avg £3.7K each</li></ul></div>
        <div class="bl bl-as"><div class="bl-title">Assumptions</div><ul><li>Similar descriptions = equivalent specs — analyst must verify</li><li>No single-source dependency assumed (unconfirmed)</li><li>Volume leverage available — contract terms not visible</li><li>IT: some niche tools may be non-consolidatable</li></ul></div>
        <div class="bl bl-vl"><div class="bl-title">Validation required</div><ul><li>Spec equivalence check before treating as interchangeable</li><li>Identify preferred/contractual suppliers</li><li>MOQ and lead time constraints per category</li><li>IT: software licence audit required</li><li>Confirm no deliberate fragmentation (resilience)</li></ul></div>
        <div class="bl bl-ts"><div class="bl-title">Data needed</div><ul><li>Contract register (preferred supplier list)</li><li>Item specification data</li><li>IT licence audit output</li></ul></div>
      </div>
      <div class="hfooter"><span class="saving">£0.8M–£2.1M (8–15% on addressable spend)</span><span class="pill pgr">Heuristic — requires market testing</span></div>
    </div>
  </div>

  <div class="heuristic">
    <div class="hh" onclick="toggleH(this)">
      <div class="hnum" style="background:var(--coral-l);color:var(--coral)">4</div>
      <div class="htitle">Same supplier, different prices <span class="pill pdis" style="font-size:10px;margin-left:6px">OUTPUT DISABLED — data correction required</span></div>
      <div class="hmeta"><span class="pill phi">Impact: High</span><span class="pill pmd">Effort: M</span><span class="pill pbl">30–90 days</span></div>
      <span class="chev">▼</span>
    </div>
    <div class="hbody">
      <div class="sanity-disabled">
        <div class="sanity-title">⚠ Price variance output is DISABLED</div>
        <div class="sanity-body">480 line items have unit prices below £0.01, affecting 51 suppliers. This is almost certainly caused by quantity-as-value data entry errors (e.g. "200" units entered as "200" in the value field). Presenting price variance figures from this data in client conversations would produce false conclusions and undermine credibility. This output will be re-enabled once the data correction workflow below is completed.</div>
      </div>
      <div class="trigger"><b>What will be detectable once fixed:</b> R&amp;M Europe BV — Nylon Cable Tie: 11 orders with apparent price variance. MEF Dublin — Rawlplugs: 7 orders. JOST LOGISTICS — Warehouse hire: 7 orders (likely duration mix). COPIADORAS — A4 print: 9 orders (possible per-page vs. contract line). These require data correction and specification validation before any supplier conversation.</div>
      <div class="bg4">
        <div class="bl bl-ev"><div class="bl-title">Evidence (pre-correction)</div><ul><li>480 rows with unit price &lt;£0.01 across 51 suppliers</li><li>Affected spend includes negative values (£−268K) — data artifact</li><li>Cannot distinguish genuine price variance from data error at this stage</li></ul></div>
        <div class="bl bl-as"><div class="bl-title">Assumptions</div><ul><li>Extreme variances (&gt;1000%) assumed to be data entry errors</li><li>Moderate variances (10–200%) may be genuine — will surface post-correction</li><li>Hire items require normalisation to daily rate before comparison</li></ul></div>
        <div class="bl bl-vl"><div class="bl-title">Remediation workflow</div><ul><li><b>Step 1:</b> Export all rows where unit price &lt;£0.01 to separate worksheet</li><li><b>Step 2:</b> AP team reviews each — confirm if qty or value field is incorrect</li><li><b>Step 3:</b> Correct source data, re-export clean file</li><li><b>Step 4:</b> Re-run this analysis against corrected file</li><li><b>Step 5:</b> Re-enable this heuristic — expected timeline: 1–2 weeks</li></ul></div>
        <div class="bl bl-ts"><div class="bl-title">Data needed to unlock</div><ul><li>Corrected invoice file (post AP review)</li><li>Invoice copies for top 20 flagged items</li><li>Agreed unit prices / rate cards from contracts</li></ul></div>
      </div>
      <div class="hfooter"><span class="saving">£0.2M–£0.6M potential — unlocks post data correction</span><span class="pill pdis">DISABLED until data corrected</span></div>
    </div>
  </div>

  <div class="heuristic">
    <div class="hh" onclick="toggleH(this)">
      <div class="hnum" style="background:var(--amber-l);color:var(--amber)">5</div>
      <div class="htitle">Order consolidation &amp; batching</div>
      <div class="hmeta"><span class="pill pmd">Impact: M</span><span class="pill plo">Effort: L</span><span class="pill pbl">0–30 days</span></div>
      <span class="chev">▼</span>
    </div>
    <div class="hbody">
      <div class="trigger"><b>Trigger:</b> 53.5% of invoices (5,385) are &lt;£500. IFS Courier: 992 lines at avg £27.85 — 144 days with multiple same-day deliveries, peak 50 in one day. Amazon: 638 sub-£500 lines at avg £49. Total AP processing cost at £25/invoice: est. £251,625/yr (0.43% of spend).</div>
      <div class="bg4">
        <div class="bl bl-ev"><div class="bl-title">Evidence</div><ul><li>5,385 invoices &lt;£500 — AP processing est. £135K</li><li>2,613 invoices &lt;£100 — marginal value, high cost</li><li>IFS Courier: 144 days with 2+ same-day deliveries</li><li>IFS Courier: peak 50 orders in one day</li><li>HWS Verhuur: 681 closely timed orders; LIFTISA: 652</li></ul></div>
        <div class="bl bl-as"><div class="bl-title">Assumptions</div><ul><li>AP cost of £25/invoice is sector heuristic — actual varies</li><li>IFS orders assumed discretionary not urgent</li><li>Batching feasible without impacting project schedule</li><li>Amazon spend is MRO/office, not project-critical</li></ul></div>
        <div class="bl bl-vl"><div class="bl-title">Validation required</div><ul><li>Confirm actual AP cost per invoice from Finance</li><li>Identify % of sub-£500 orders that are urgent</li><li>Determine whether IFS orders are site-driven or central</li><li>Amazon: P-card or corporate account operationally feasible?</li></ul></div>
        <div class="bl bl-ts"><div class="bl-title">Data needed</div><ul><li>AP processing cost per invoice (Finance)</li><li>Order urgency classification (Ops)</li><li>Site-level order origination data</li></ul></div>
      </div>
      <div class="hfooter"><span class="saving">£60K–£120K/yr process cost + delivery discounts</span><span class="pill plo">Fastest implementable action</span></div>
    </div>
  </div>

  <div class="heuristic">
    <div class="hh" onclick="toggleH(this)">
      <div class="hnum" style="background:var(--green-l);color:var(--green)">6</div>
      <div class="htitle">Delivery &amp; logistics efficiency</div>
      <div class="hmeta"><span class="pill pmd">Impact: M</span><span class="pill plo">Effort: L</span><span class="pill pbl">30–90 days</span></div>
      <span class="chev">▼</span>
    </div>
    <div class="hbody">
      <div class="trigger"><b>Trigger:</b> True logistics spend is £1.78M — not the £545K shown in Transport category. £519K is genuine embedded freight (see Logistics Audit tab). A further £735K was initially flagged but is actually milestone payment terms — these are false positives confirmed on audit. See Logistics Audit for full classification logic.</div>
      <div class="bg4">
        <div class="bl bl-ev"><div class="bl-title">Evidence</div><ul><li>Genuine embedded freight: £519K across 597 lines</li><li>Keyword split: transport £289K · delivery £205K · carriage £34K · freight £10K · shipping £4K</li><li>False positives identified: £735K in milestone payment terms containing the word "delivery"</li><li>Most common charge amounts are ad-hoc — no contracted rate visible</li></ul></div>
        <div class="bl bl-as"><div class="bl-title">Assumptions</div><ul><li>Embedded charges assumed negotiable — may be bundled in equipment price</li><li>Large embedded charges (ABB, Elinex) assumed specialist freight</li><li>146 logistics suppliers assumed to have no master framework</li></ul></div>
        <div class="bl bl-vl"><div class="bl-title">Validation required</div><ul><li>Confirm whether large embedded charges (ABB, Elinex) are inside equipment contracts</li><li>Check contract register for any existing carrier framework</li><li>Obtain actual carrier rate cards for comparison</li><li>Validate milestone payment flags are correctly excluded</li></ul></div>
        <div class="bl bl-ts"><div class="bl-title">Data needed</div><ul><li>Contract register (to confirm bundling)</li><li>Carrier rate cards (3 preferred carriers)</li><li>Project delivery schedule (to assess consolidation feasibility)</li></ul></div>
      </div>
      <div class="hfooter"><span class="saving">£50K–£130K (10–25% on £519K genuine embedded freight)</span><span class="pill pgr">True figure revised after false-positive audit</span></div>
    </div>
  </div>
</div>

<!-- ======== LOGISTICS AUDIT ======== -->
<div class="page" id="page-logistics">
  <div class="ph"><div class="ph-title">Logistics spend — full audit trail</div><div class="ph-sub">This section is auditable. All classification logic, keyword rules, and false-positive flags are shown. The result is marked as an indicator requiring analyst validation before client presentation.</div></div>

  <div class="card">
    <div class="card-title">Classification logic — keyword rules applied</div>
    <table><thead><tr><th>Keyword</th><th>Match type</th><th>Applied to field</th><th>Lines matched</th><th>Gross spend matched</th><th>False positives identified</th></tr></thead>
    <tbody>
      <tr><td>delivery</td><td>Contains (case-insensitive)</td><td>Description</td><td>232</td><td>£205,070</td><td>Milestone payment terms ("at delivery", "% at delivery")</td></tr>
      <tr><td>carriage</td><td>Contains (case-insensitive)</td><td>Description</td><td>169</td><td>£33,672</td><td>None identified</td></tr>
      <tr><td>freight</td><td>Contains (case-insensitive)</td><td>Description</td><td>49</td><td>£10,071</td><td>None identified</td></tr>
      <tr><td>transport</td><td>Contains (case-insensitive)</td><td>Description</td><td>166</td><td>£288,551</td><td>"transport of equipment" in project service lines (review required)</td></tr>
      <tr><td>shipping</td><td>Contains (case-insensitive)</td><td>Description</td><td>33</td><td>£4,057</td><td>None identified</td></tr>
    </tbody></table>
  </div>

  <div class="card" style="border-left:3px solid var(--amber);border-radius:0 var(--rl) var(--rl) 0">
    <div class="card-title" style="color:var(--amber)">⚠ False positives identified — £734,982 excluded from genuine logistics</div>
    <div style="font-size:13px;color:var(--text2);line-height:1.7">5 invoice lines totalling £734,982 initially matched logistics keywords but were found to be <b>milestone payment terms</b> not freight charges. For example: "10% at delivery 30d netto" (CTC Tankbouw, £143,500 × 2), "Milestone 3 - 30.09% after delivery on site" (Elinex, £245,204). The word "delivery" appears in a commercial payment condition, not a logistics charge. These are <b>excluded</b> from the logistics spend figure. An analyst must confirm this exclusion is correct before the figure is presented to any client.</div>
  </div>

  <div class="mg" style="margin-top:1rem">
    <div class="metric"><div class="lbl">Keyword total (raw)</div><div class="val">£1,257K</div><div class="sub">Before false positive removal</div></div>
    <div class="metric"><div class="lbl">Milestone false positives</div><div class="val">£735K</div><div class="warn">5 lines — payment terms not freight</div></div>
    <div class="metric"><div class="lbl">Genuine embedded logistics</div><div class="val">£519K</div><div class="ok">597 lines — requires validation</div></div>
    <div class="metric"><div class="lbl">Logistics category (separate)</div><div class="val">£545K</div><div class="sub">IFS Courier + direct logistics suppliers</div></div>
    <div class="metric"><div class="lbl">True logistics total</div><div class="val">~£1.06M</div><div class="warn">Indicative — analyst validation required</div></div>
  </div>

  <div class="card">
    <div class="card-title">Top 25 embedded logistics invoice lines (genuine, post false-positive removal)</div>
    <div style="font-size:11px;color:var(--text3);margin-bottom:.75rem;font-style:italic">Indicator status: requires analyst validation. Do not present gross figures to client without reviewing individual lines below. Some "transport" lines may be project service charges not freight.</div>
    <div class="tbl-wrap">
      <table><thead><tr><th>#</th><th>Supplier</th><th>Description (truncated)</th><th>Gross</th><th>Date</th><th>Keyword</th><th>Analyst note</th></tr></thead>
      <tbody id="logisticsTable"></tbody></table>
    </div>
  </div>
</div>

<!-- ======== DATA QUALITY ======== -->
<div class="page" id="page-dataquality">
  <div class="ph"><div class="ph-title">Data quality signals</div><div class="ph-sub">Data gaps are not errors to fix before analysis — they are signals about procurement maturity. Each gap tells a story.</div></div>

  <div class="card">
    <div class="card-title">Data sanity rules — applied before any analysis output</div>
    <table><thead><tr><th>Rule</th><th>Condition</th><th>Action</th><th>Status</th></tr></thead>
    <tbody>
      <tr><td>Unit price floor</td><td>Unit price &lt; £0.01 (where qty &gt; 0)</td><td>Flag row; disable price variance heuristic (H4)</td><td><span class="pill pdis">480 rows flagged · H4 DISABLED</span></td></tr>
      <tr><td>Unit price ceiling</td><td>Unit price &gt; £100,000</td><td>Flag for manual review; do not exclude</td><td><span class="pill pmd">83 rows flagged · manual review</span></td></tr>
      <tr><td>Zero quantity</td><td>Sum of Quantity = 0</td><td>Exclude from unit price calculations; retain in spend totals</td><td><span class="pill pmd">48 rows · excluded from price calcs</span></td></tr>
      <tr><td>Negative gross value</td><td>Sum of Gross &lt; 0</td><td>Flag as credit note; include in spend totals (net)</td><td><span class="pill pgr">Present in data — included in totals</span></td></tr>
      <tr><td>Logistics keyword false positives</td><td>Keyword match + milestone payment pattern</td><td>Exclude from embedded logistics figure; flag for analyst review</td><td><span class="pill pmd">5 lines £735K excluded · see Logistics tab</span></td></tr>
      <tr><td>Supplier name variants</td><td>Same supplier code, different name strings</td><td>Flag for master data cleanse; retain as-is in current analysis</td><td><span class="pill pmd">R&amp;M (4 variants), Craig (3 variants) flagged</span></td></tr>
    </tbody></table>
  </div>

  <div class="card" style="border-left:3px solid var(--coral);border-radius:0 var(--rl) var(--rl) 0;margin-top:.75rem">
    <div class="card-title" style="color:var(--coral)">61.1% unclassified spend (£36.2M) — No category management framework</div>
    <div style="font-size:13px;color:var(--text2);line-height:1.7">The absence of a category hierarchy in the P2P system is the most significant maturity indicator in this dataset. This is not a data quality problem — it reflects that no one has been held accountable for category-level spend ownership. Until resolved, supplier consolidation and benchmark analysis will remain partial. <b>Required: 4–6 week reclassification exercise, then manual review for top 80% of spend by value.</b></div>
  </div>
  <div class="card" style="border-left:3px solid var(--coral);border-radius:0 var(--rl) var(--rl) 0">
    <div class="card-title" style="color:var(--coral)">480+ unit prices below £0.01 — Systemic AP input control failure</div>
    <div style="font-size:13px;color:var(--text2);line-height:1.7">Most likely cause: quantity entered in the value field during manual invoice processing. This is why price variance (H4) has been disabled. <b>Remediation: (1) Export flagged rows. (2) AP team reviews each line against original invoice. (3) Correct source data. (4) Re-export clean file. (5) Re-run analysis. Estimated effort: 1–2 weeks.</b></div>
  </div>
  <div class="card" style="border-left:3px solid var(--amber);border-radius:0 var(--rl) var(--rl) 0">
    <div class="card-title" style="color:var(--amber)">Supplier name variants — No master data governance</div>
    <div style="font-size:13px;color:var(--text2);line-height:1.7">R&amp;M Europe: 4 name strings for the same legal entity. Robert Craig: 3 variants. This understates true supplier concentration and prevents accurate spend-by-supplier analysis. R&amp;M may be a £1M+ supplier once merged. <b>Required: Supplier master data cleanse and legal entity matching.</b></div>
  </div>
  <div class="card" style="border-left:3px solid var(--amber);border-radius:0 var(--rl) var(--rl) 0">
    <div class="card-title" style="color:var(--amber)">No site/cost centre dimension — No BU-level spend accountability</div>
    <div style="font-size:13px;color:var(--text2);line-height:1.7">Without a site or cost centre code, site-by-site heuristic (H1) cannot be completed. Maverick spend detection and budget compliance analysis are also blocked. <b>Required: Cost centre extract from ERP. Even a project code enables 70% of the missing analysis.</b></div>
  </div>
  <div class="card" style="border-left:3px solid var(--green);border-radius:0 var(--rl) var(--rl) 0">
    <div class="card-title" style="color:var(--green)">72.4% zero-VAT (£42.9M) — Multi-jurisdiction compliance signal</div>
    <div style="font-size:13px;color:var(--text2);line-height:1.7">Explainable in a multi-country operation (EU B2B zero-rating, reverse charge, construction exemptions) but warrants formal tax review. Both overclaiming and under-recovering input VAT are material risks. <b>Required: Tax/finance team confirmation by jurisdiction before any commercial conclusions.</b></div>
  </div>

  <div class="card" style="margin-top:.75rem">
    <div class="card-title">Data that would materially improve confidence</div>
    <div style="display:grid;grid-template-columns:repeat(auto-fit,minmax(200px,1fr));gap:7px;font-size:12px;color:var(--text2)">
      <div>• FY2023 spend data (year-on-year baseline)</div>
      <div>• Site / cost centre / project code per line</div>
      <div>• Contract reference per supplier</div>
      <div>• Agreed unit prices / rate cards</div>
      <div>• PO vs. non-PO flag per invoice</div>
      <div>• Corrected unit prices (post AP review)</div>
      <div>• FX normalisation table (EUR/GBP/USD)</div>
      <div>• Supplier legal entity master</div>
    </div>
  </div>
</div>

<!-- ======== ACTION PLAN ======== -->
<div class="page" id="page-actionplan">
  <div class="ph"><div class="ph-title">Structured action plan</div><div class="ph-sub">Each opportunity mapped to owner, horizon, effort, impact, and data validation required. Sequenced for maximum early credibility.</div></div>
  <div class="card">
    <div class="tbl-wrap">
      <table class="ap-table">
        <thead><tr><th>#</th><th>Action / opportunity</th><th>Owner</th><th>Horizon</th><th>Effort</th><th>Impact</th><th>Saving estimate</th><th>Data needed to validate</th></tr></thead>
        <tbody>
          <tr>
            <td style="font-weight:700;color:var(--text3)">1</td>
            <td><b>Data correction &amp; reclassification</b><br><span style="font-size:11px;color:var(--text2)">Fix 480+ unit price errors. Merge supplier name variants. Reclassify top 80% unclassified spend. Request cost centre from ERP.</span></td>
            <td><span class="owner-p">Procurement</span> <span class="owner-f">Finance</span></td>
            <td><span class="pill pbl">0–30 days</span></td>
            <td><span class="pill pmd">M</span></td>
            <td><span class="pill phi">High</span></td>
            <td style="font-size:11px;color:var(--text3)">Enabler — unlocks all other actions</td>
            <td style="font-size:11px;color:var(--text2)">Access to original invoices · ERP extract with cost centres</td>
          </tr>
          <tr>
            <td style="font-weight:700;color:var(--text3)">2</td>
            <td><b>IFS Courier — batching &amp; consolidated delivery</b><br><span style="font-size:11px;color:var(--text2)">992 lines at avg £27.85. Agree weekly consolidated schedule. Process change, not commercial negotiation.</span></td>
            <td><span class="owner-o">Ops</span> <span class="owner-p">Procurement</span></td>
            <td><span class="pill pbl">0–30 days</span></td>
            <td><span class="pill plo">L</span></td>
            <td><span class="pill pmd">M</span></td>
            <td>£30K–£60K/yr</td>
            <td style="font-size:11px;color:var(--text2)">Site delivery requirements · IFS account manager contact</td>
          </tr>
          <tr>
            <td style="font-weight:700;color:var(--text3)">3</td>
            <td><b>Amazon — P-card or corporate account</b><br><span style="font-size:11px;color:var(--text2)">638 sub-£500 lines at avg £49. Migrate to Amazon Business. Reduces AP overhead and enables VAT invoicing.</span></td>
            <td><span class="owner-f">Finance</span> <span class="owner-p">Procurement</span></td>
            <td><span class="pill pbl">0–30 days</span></td>
            <td><span class="pill plo">L</span></td>
            <td><span class="pill plo">L</span></td>
            <td>£10K–£20K/yr</td>
            <td style="font-size:11px;color:var(--text2)">Item categorisation (MRO vs. project) · Finance approval for P-card policy</td>
          </tr>
          <tr>
            <td style="font-weight:700;color:var(--text3)">4</td>
            <td><b>IT &amp; Software category review</b><br><span style="font-size:11px;color:var(--text2)">£9.1M across 312 suppliers. Licence audit + top 20 supplier consolidation. Highest-yield classified category.</span></td>
            <td><span class="owner-p">Procurement</span></td>
            <td><span class="pill pbl">30–90 days</span></td>
            <td><span class="pill pmd">M</span></td>
            <td><span class="pill phi">High</span></td>
            <td>£0.4M–£0.9M</td>
            <td style="font-size:11px;color:var(--text2)">IT licence audit · contract terms for top 20 suppliers · market benchmarks</td>
          </tr>
          <tr>
            <td style="font-weight:700;color:var(--text3)">5</td>
            <td><b>Logistics carrier framework</b><br><span style="font-size:11px;color:var(--text2)">True logistics spend £1.06M. Approach top 3 carriers with consolidated volume. Challenge embedded charges in upcoming renewals.</span></td>
            <td><span class="owner-p">Procurement</span> <span class="owner-o">Ops</span></td>
            <td><span class="pill pbl">30–90 days</span></td>
            <td><span class="pill plo">L</span></td>
            <td><span class="pill pmd">M</span></td>
            <td>£50K–£130K</td>
            <td style="font-size:11px;color:var(--text2)">Contract register · carrier rate cards · project delivery schedule</td>
          </tr>
          <tr>
            <td style="font-weight:700;color:var(--text3)">6</td>
            <td><b>VAT compliance review</b><br><span style="font-size:11px;color:var(--text2)">72.4% zero-VAT (£42.9M). Multi-jurisdiction review for exemption validity and input VAT recovery.</span></td>
            <td><span class="owner-f">Finance</span></td>
            <td><span class="pill pbl">30–90 days</span></td>
            <td><span class="pill pmd">M</span></td>
            <td><span class="pill phi">High</span></td>
            <td>£0.3M–£1.0M</td>
            <td style="font-size:11px;color:var(--text2)">Tax team + jurisdiction mapping · VAT treatment by supplier country</td>
          </tr>
          <tr>
            <td style="font-weight:700;color:var(--text3)">7</td>
            <td><b>Price variance analysis (H4)</b><br><span style="font-size:11px;color:var(--text2)">Re-run once unit price data corrected. Systematic comparison across top 50 suppliers vs. contract rates.</span></td>
            <td><span class="owner-p">Procurement</span></td>
            <td><span class="pill pbl">30–90 days</span></td>
            <td><span class="pill pmd">M</span></td>
            <td><span class="pill phi">High</span></td>
            <td>£0.2M–£0.6M</td>
            <td style="font-size:11px;color:var(--text2)">Corrected invoice file (AP review) · agreed unit prices / rate cards</td>
          </tr>
          <tr>
            <td style="font-weight:700;color:var(--text3)">8</td>
            <td><b>Supplier consolidation — Electrical category</b><br><span style="font-size:11px;color:var(--text2)">PSI Global (£3.2M), Down to Earth (£2.8M), Anord Mardix (£1.1M), Excise Electrical (£0.85M). Assess framework potential.</span></td>
            <td><span class="owner-p">Procurement</span></td>
            <td><span class="pill pbl">90+ days</span></td>
            <td><span class="pill phi">H</span></td>
            <td><span class="pill phi">High</span></td>
            <td>£0.3M–£0.8M</td>
            <td style="font-size:11px;color:var(--text2)">Contract terms for each supplier · spec equivalence confirmation · market rates</td>
          </tr>
          <tr>
            <td style="font-weight:700;color:var(--text3)">9</td>
            <td><b>Site-by-site analysis (H1)</b><br><span style="font-size:11px;color:var(--text2)">Cannot progress until site/cost centre dimension is added to the dataset. Highest potential impact once unlocked.</span></td>
            <td><span class="owner-p">Procurement</span></td>
            <td><span class="pill pbl">90+ days</span></td>
            <td><span class="pill pmd">M</span></td>
            <td><span class="pill phi">High</span></td>
            <td>£0.3M–£1.2M</td>
            <td style="font-size:11px;color:var(--text2)">ERP cost centre extract · FX normalisation table · site-level contract mapping</td>
          </tr>
          <tr>
            <td style="font-weight:700;color:var(--text3)">10</td>
            <td><b>One-off supplier reduction &amp; PSL enforcement</b><br><span style="font-size:11px;color:var(--text2)">180 one-off suppliers (24.6% of base). Review PSL compliance and route tail spend through existing approved suppliers.</span></td>
            <td><span class="owner-p">Procurement</span> <span class="owner-o">Ops</span></td>
            <td><span class="pill pbl">90+ days</span></td>
            <td><span class="pill pmd">M</span></td>
            <td><span class="pill pmd">M</span></td>
            <td>£40K–£100K</td>
            <td style="font-size:11px;color:var(--text2)">PSL (preferred supplier list) · approval workflow data · one-off order descriptions</td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</div>

<!-- ======== EXECUTIVE OUTPUT ======== -->
<div class="page" id="page-executive">
  <div class="ph"><div class="ph-title">Executive output</div><div class="ph-sub">Five-question narrative for commercial conversations. Designed to support expert judgement — not replace it.</div></div>
  <div class="card">
    <div class="eq">1 — Where should we look first?</div>
    <div class="eb"><b>Start with the data, not the suppliers.</b> 61% of spend (£36M) is unclassified and 480+ price records are corrupted by data entry errors. Any commercial conversation held before this is corrected risks presenting false conclusions and destroys credibility. The first 30 days should be internal — structured reclassification and data correction, not supplier meetings.</div>
    <div class="eb">Once data is clean, three areas offer the clearest commercial attention: <b>IT &amp; Software (£9.1M, 312 suppliers)</b> where fragmentation is extreme; <b>logistics (true spend £1.06M, 146+ carriers)</b> where a framework agreement delivers quick wins; and <b>sub-£500 invoices (5,385)</b> where AP processing cost alone justifies immediate action.</div>

    <div class="eq">2 — Why does it matter?</div>
    <div class="eb">The fragmentation visible in this dataset is typical of a project-driven, multi-site operation that has scaled without a category management framework. The commercial consequence is lost volume leverage and inconsistent pricing. The operational consequence is significant AP overhead. The strategic consequence is that no single person currently has a clear view of what is being spent, with whom, and under what terms.</div>

    <div class="eq">3 — What is the realistic size of the prize?</div>
    <div class="eb"><b>These are directional estimates based on embedded procurement heuristics, not audited savings. They are hypotheses requiring validation, not commitments to stakeholders.</b></div>
    <div class="prize-g">
      <div class="prize-i" style="background:var(--blue-l);border:0.5px solid var(--blue-m)"><div class="pl" style="color:var(--blue)">Supplier consolidation</div><div class="pv" style="color:var(--blue)">£0.8M–£2.1M</div><div class="pc" style="color:var(--blue)">Medium confidence · 90+ days</div></div>
      <div class="prize-i" style="background:var(--green-l);border:0.5px solid var(--green-m)"><div class="pl" style="color:var(--green)">Order batching / AP</div><div class="pv" style="color:var(--green)">£60K–£120K</div><div class="pc" style="color:var(--green)">High confidence · 0–30 days</div></div>
      <div class="prize-i" style="background:var(--amber-l);border:0.5px solid var(--amber-m)"><div class="pl" style="color:var(--amber)">Logistics framework</div><div class="pv" style="color:var(--amber)">£50K–£130K</div><div class="pc" style="color:var(--amber)">Medium conf · 30–90 days</div></div>
      <div class="prize-i" style="background:var(--red-l);border:0.5px solid var(--red-m)"><div class="pl" style="color:var(--red)">Price variance (post fix)</div><div class="pv" style="color:var(--red)">£0.2M–£0.6M</div><div class="pc" style="color:var(--red)">Low conf · data first</div></div>
      <div class="prize-i" style="background:var(--amber-l);border:0.5px solid var(--amber-m)"><div class="pl" style="color:var(--amber)">VAT compliance</div><div class="pv" style="color:var(--amber)">£0.3M–£1.0M</div><div class="pc" style="color:var(--amber)">Low conf · tax review</div></div>
      <div class="prize-i" style="background:var(--bg2);border:0.5px solid var(--border)"><div class="pl" style="color:var(--text2)">Total indicative range</div><div class="pv">£1.4M–£4.0M</div><div class="pc" style="color:var(--text3)">Assumptions-based · 12 months</div></div>
    </div>

    <div class="eq">4 — What evidence do we have?</div>
    <div class="eb">The data provides <b>strong structural evidence</b> of fragmentation: 732 suppliers, top 10 = 25.6% of spend, cable ties from 15 suppliers, IFS Courier 992 delivery lines at £28 average. It provides <b>partial evidence</b> of price variance — 480 corrupted records prevent clean unit-price analysis without a correction pass. It provides <b>no direct evidence</b> of contract leakage, site-level behaviour, or volume discount compliance. Those require additional data not present in this extract.</div>

    <div class="eq">5 — What should we do next, and in what order?</div>
    <div class="card" style="margin-top:.5rem;margin-bottom:0;padding:.75rem 1rem">
      <div class="act-row"><div class="act-n" style="background:var(--blue-l);color:var(--blue)">1</div><div class="act-body"><div class="act-title">Data correction &amp; reclassification — 0–30 days · Procurement + Finance</div><div class="act-desc">Fix unit price errors, merge name variants, reclassify unclassified spend, request ERP cost centres. All subsequent analysis depends on this.</div></div></div>
      <div class="act-row"><div class="act-n" style="background:var(--green-l);color:var(--green)">2</div><div class="act-body"><div class="act-title">IFS Courier batching + Amazon P-card — 0–30 days · Ops · Low effort</div><div class="act-desc">Process changes, not commercial negotiations. Fast credibility-builders. Estimated saving: £40K–£80K/yr.</div></div></div>
      <div class="act-row"><div class="act-n" style="background:var(--amber-l);color:var(--amber)">3</div><div class="act-body"><div class="act-title">IT &amp; Software category review — 30–90 days · Category manager + IT</div><div class="act-desc">Highest-yield classified category. Software licence audit in parallel. Even 10% saving = £900K. Requires market benchmarking to confirm.</div></div></div>
      <div class="act-row"><div class="act-n" style="background:var(--blue-l);color:var(--blue)">4</div><div class="act-body"><div class="act-title">Logistics carrier framework + VAT review — 30–90 days · Procurement + Finance</div><div class="act-desc">Carrier framework on £1.06M true logistics spend. Parallel VAT compliance review with tax team. Both are preparation-intensive but high-impact.</div></div></div>
      <div class="act-row"><div class="act-n" style="background:var(--red-l);color:var(--red)">5</div><div class="act-body"><div class="act-title">Price variance &amp; supplier consolidation — 90+ days · After clean data</div><div class="act-desc">Price variance requires corrected invoice file first. Electrical supplier consolidation (£8M+ addressable) is highest structural lever once data and site dimensions are in place.</div></div></div>
    </div>
  </div>
  <div class="disclaimer" style="margin-top:1rem">
    <b>Assumptions vs. evidence — required disclaimer.</b> All saving ranges are heuristic-based, drawn from sector experience in construction, engineering, and infrastructure procurement. They represent directional hypotheses, not audited projections. No benchmarks have been stated unless explicitly labelled as assumptions. No savings should be reported to stakeholders, included in business cases, or communicated to suppliers until each opportunity has been individually validated by a qualified procurement professional with access to the relevant contracts, market data, and operational constraints. PSL accepts no liability for decisions taken on the basis of these indicative figures without formal validation.
  </div>
</div>

<!-- ======== 5-SLIDE NARRATIVE ======== -->
<div class="page" id="page-slides">
  <div class="ph"><div class="ph-title">5-slide board narrative</div><div class="ph-sub">Headlines and bullet points. Ready to transfer into PowerPoint. Each slide includes a required disclaimer note.</div></div>

  <div class="slide">
    <div class="slide-num">Slide 1 of 5</div>
    <div class="slide-label">Context &amp; scope</div>
    <div class="slide-title">What we looked at, and why it matters</div>
    <div class="slide-stat-row">
      <div class="slide-stat"><div class="sv">£59.2M</div><div class="sl">Total spend FY2024</div></div>
      <div class="slide-stat"><div class="sv">18,703</div><div class="sl">Invoice lines analysed</div></div>
      <div class="slide-stat"><div class="sv">732</div><div class="sl">Active suppliers</div></div>
      <div class="slide-stat"><div class="sv">6</div><div class="sl">Opportunity lenses applied</div></div>
    </div>
    <ul class="slide-bullets">
      <li>This analysis covers FY2024 invoice line data exported from the ERP/P2P system. <b>All 6 independent verification checks passed before analysis began.</b></li>
      <li>The goal is not accounting accuracy — it is <b>speed to credible commercial insight</b>, identifying where expert procurement attention should be directed first.</li>
      <li>This tool supports analyst judgement. It does not replace it. <b>Every finding must be validated before any supplier conversation takes place.</b></li>
      <li>Data quality issues were identified, documented, and — where material — used to disable outputs that could mislead. See Data Quality section for detail.</li>
    </ul>
    <div class="slide-note">Disclaimer: Analysis prepared by PSL. Indicative only. Not a substitute for professional procurement advice or formal market testing.</div>
  </div>

  <div class="slide">
    <div class="slide-num">Slide 2 of 5</div>
    <div class="slide-label">Spend landscape</div>
    <div class="slide-title">The spend base is fragmented — and largely unclassified</div>
    <ul class="slide-bullets">
      <li><b>61% of spend (£36M) has no category classification</b> — the most significant finding. This is a procurement maturity signal, not a data error. It means no one currently owns category accountability for most of the cost base.</li>
      <li>Of what is classified: IT &amp; Software is the largest at £9.1M (312 suppliers). Electrical Equipment, Subcontract Services, and Plant Hire follow.</li>
      <li><b>The top 10 suppliers account for only 25.6% of total spend</b> — a highly fragmented base. Sector best practice for comparable operations: 60–70% concentration. This represents significant lost volume leverage.</li>
      <li>72.4% of spend (£42.9M) carries zero VAT — a proportion that warrants a formal tax review before any commercial conclusions are drawn. Finance must validate.</li>
    </ul>
    <div class="slide-note">Assumption: Sector concentration benchmark is a heuristic from comparable construction/engineering operations. It has not been independently verified for this specific entity.</div>
  </div>

  <div class="slide">
    <div class="slide-num">Slide 3 of 5</div>
    <div class="slide-label">Key findings</div>
    <div class="slide-title">Three structural issues, two quick wins, one blocker</div>
    <ul class="slide-bullets">
      <li><b>Structural issue 1 — Supplier fragmentation:</b> cable ties from 15 suppliers; transport from 146 suppliers (avg £3.7K each); IT &amp; Software from 312 suppliers. Volume leverage is being systematically lost across the spend base.</li>
      <li><b>Structural issue 2 — Unclassified spend:</b> £36M without a category home means no ownership, no benchmark, no consolidation possible. Reclassification is a prerequisite for all commercial activity.</li>
      <li><b>Structural issue 3 — Data quality:</b> 480+ price records disabled due to suspected quantity/value data entry errors. Price variance analysis (potentially £0.2M–£0.6M) cannot be safely run until corrected. This is a P2P control failure.</li>
      <li><b>Quick win 1 — Order batching:</b> IFS Courier has 992 delivery lines at avg £28. 144 days with multiple same-day deliveries. A consolidated delivery schedule requires no commercial negotiation and saves an estimated £30K–£60K immediately.</li>
      <li><b>Quick win 2 — AP cost reduction:</b> 53.5% of invoices are below £500. Estimated AP processing cost: £135K/yr on sub-£500 invoices alone. A minimum order policy and P-card for tail spend is achievable within 30 days.</li>
    </ul>
    <div class="slide-note">Evidence vs. assumption: Fragmentation figures are drawn directly from the data. Saving estimates are heuristic-based and have not been market-tested. All require validation.</div>
  </div>

  <div class="slide">
    <div class="slide-num">Slide 4 of 5</div>
    <div class="slide-label">Size of the prize</div>
    <div class="slide-title">Indicative savings range: £1.4M–£4.0M over 12 months</div>
    <div class="slide-stat-row">
      <div class="slide-stat"><div class="sv" style="color:var(--green)">£60K–£120K</div><div class="sl">High confidence · 0–30 days</div></div>
      <div class="slide-stat"><div class="sv" style="color:var(--amber)">£0.4M–£1.1M</div><div class="sl">Medium confidence · 30–90 days</div></div>
      <div class="slide-stat"><div class="sv" style="color:var(--blue)">£1.0M–£2.8M</div><div class="sl">Structural · 90+ days</div></div>
    </div>
    <ul class="slide-bullets">
      <li>Figures are derived from embedded procurement heuristics calibrated to construction/engineering/infrastructure sector benchmarks. <b>They are directional hypotheses, not audited or committed savings.</b></li>
      <li>The highest-confidence opportunity (order batching, AP cost) is also the smallest in value. This is intentional — quick credibility-builders first, structural change second.</li>
      <li>The largest single lever (supplier consolidation, £0.8M–£2.1M) is also the most dependent on data quality improvements. It cannot be safely pursued until the reclassification exercise is complete.</li>
      <li>Price variance (H4) output has been disabled pending data correction. This range (£0.2M–£0.6M) will be updated once corrected data is available. It is excluded from the totals above.</li>
    </ul>
    <div class="slide-note">Required disclaimer: These saving ranges must not be used in business cases, communicated to suppliers, or reported as confirmed savings without individual validation of each opportunity by a qualified procurement professional.</div>
  </div>

  <div class="slide">
    <div class="slide-num">Slide 5 of 5</div>
    <div class="slide-label">Recommended next steps</div>
    <div class="slide-title">Five actions, sequenced for credibility and impact</div>
    <ul class="slide-bullets">
      <li><b>Action 1 (0–30 days) · Procurement + Finance:</b> Data correction and reclassification. Fix unit price errors, merge supplier name variants, reclassify £36M unclassified spend, request ERP cost centre extract. This is not optional — all subsequent work depends on it.</li>
      <li><b>Action 2 (0–30 days) · Ops:</b> IFS Courier consolidated delivery schedule + Amazon Business account. Process changes only. No supplier negotiation required. Demonstrates immediate value while structural work begins.</li>
      <li><b>Action 3 (30–90 days) · Procurement:</b> IT &amp; Software category strategy. Licence audit + top 20 supplier consolidation. Highest-yield classified category. Even 10% saving = £900K (assumption — requires market test).</li>
      <li><b>Action 4 (30–90 days) · Procurement + Finance:</b> Logistics carrier framework on £1.06M true spend. Parallel VAT compliance review. Both are preparation-intensive but high-impact.</li>
      <li><b>Action 5 (90+ days) · Procurement:</b> Price variance analysis (once data corrected) + electrical supplier consolidation (PSI Global, Down to Earth, Anord Mardix, Excise Electrical: £8M+ combined). Largest structural lever once data and site dimensions are in place.</li>
    </ul>
    <div class="slide-note">Prepared by PSL · [CLIENT NAME] · FY[YEAR] · Analyst-operated · This document is confidential and intended solely for the named client.</div>
  </div>
</div>

<!-- ======== EMAIL SUMMARY ======== -->
<div class="page" id="page-email">
  <div class="ph"><div class="ph-title">Executive summary — email version</div><div class="ph-sub">One-page narrative suitable for email or PDF attachment. Copy and adapt as required.</div></div>
  <div class="email-box">
    <div class="email-header">
      <span class="ef">To: </span><span class="ev">[Client Senior Stakeholder]</span>&nbsp;&nbsp;
      <span class="ef">From: </span><span class="ev">PSL Procurement Intelligence</span>&nbsp;&nbsp;
      <span class="ef">Re: </span><span class="ev">Initial Spend Analysis — [CLIENT NAME] FY[YEAR]</span>&nbsp;&nbsp;
      <span class="ef">Date: </span><span class="ev">[DATE]</span>
    </div>

    <h3>Summary</h3>
    <p>We have completed an initial structured analysis of FY[YEAR] invoice data for [CLIENT NAME] — 18,703 invoice lines totalling £59.2M across 732 suppliers. Six independent data verification checks passed before analysis began. This note summarises the key findings, the realistic savings opportunity, and the recommended next steps.</p>

    <h3>What we found</h3>
    <ul>
      <li><b>61% of spend (£36M) is unclassified</b> — no category code in the P2P system. This limits the precision of all other analysis and is the most important issue to resolve first.</li>
      <li><b>The supplier base is highly fragmented.</b> The top 10 suppliers account for only 25.6% of total spend, versus 60–70% in comparable well-managed operations. Cable ties alone are purchased from 15 different suppliers. Transport from 146.</li>
      <li><b>480 price records are corrupted</b> (suspected quantity/value data entry errors). Price variance analysis has been formally disabled pending correction — this protects against false conclusions in supplier conversations.</li>
      <li><b>The true logistics spend is £1.06M</b>, not the £545K visible in the Transport category. £519K of freight charges are embedded in non-logistics supplier invoices. A further £735K was initially flagged but is actually milestone payment terms — identified and excluded on audit.</li>
      <li><b>72.4% of spend carries zero VAT</b>. In a multi-country operation this may be explainable, but the proportion warrants a formal tax/finance review before any commercial action.</li>
    </ul>

    <h3>Indicative savings opportunity</h3>
    <p><b>Important: these are directional estimates based on embedded procurement heuristics, not audited savings. They are hypotheses to be tested, not commitments.</b></p>
    <ul>
      <li>Order batching and AP cost reduction: <b>£60K–£120K</b> (high confidence, 0–30 days)</li>
      <li>Logistics carrier framework: <b>£50K–£130K</b> (medium confidence, 30–90 days)</li>
      <li>IT &amp; Software consolidation: <b>£0.4M–£0.9M</b> (medium confidence, 30–90 days)</li>
      <li>Supplier consolidation (post reclassification): <b>£0.8M–£2.1M</b> (structural, 90+ days)</li>
      <li>VAT compliance review: <b>£0.3M–£1.0M</b> (requires tax team input)</li>
      <li><b>Total indicative range: £1.4M–£4.0M over 12 months</b></li>
    </ul>
    <p>Price variance (H4) is excluded from the above. It will be sized once the data correction exercise is complete.</p>

    <h3>Recommended next steps</h3>
    <ul>
      <li><b>Immediate (this week):</b> Begin data correction — fix unit price errors, request ERP cost centre extract, merge supplier name variants. This is a prerequisite for all commercial activity.</li>
      <li><b>Within 30 days:</b> Agree consolidated delivery schedule with IFS Courier. Implement Amazon Business account for tail spend. Both are process changes requiring no supplier negotiation.</li>
      <li><b>Within 90 days:</b> IT &amp; Software category review and logistics carrier framework. Parallel VAT compliance review with Finance.</li>
      <li><b>Structural (90+ days):</b> Electrical supplier consolidation and price variance analysis once data is clean and site dimension is available.</li>
    </ul>

    <h3>What we need from you</h3>
    <ul>
      <li>ERP extract with cost centre / site / project code per invoice line</li>
      <li>FY[YEAR-1] spend data in the same format (to establish year-on-year baseline)</li>
      <li>Contract register or preferred supplier list</li>
      <li>Access to AP team for data correction exercise</li>
    </ul>

    <div style="margin-top:1.25rem;padding-top:1rem;border-top:0.5px solid var(--border);font-size:11px;color:var(--text3)">
      <b>Disclaimer:</b> This summary is prepared by PSL on the basis of data provided by [CLIENT NAME]. All saving figures are indicative estimates based on embedded procurement heuristics. They have not been market-tested, verified against contracts, or audited. They should not be reported as confirmed savings, included in business cases, or communicated to suppliers without individual validation. PSL accepts no liability for decisions taken on the basis of this initial analysis without completion of the recommended validation steps.<br><br>
      Prepared by: PSL Procurement Intelligence · [CLIENT NAME] · FY[YEAR] · Confidential
    </div>
  </div>
</div>

<!-- ======== SPEND CUBE ======== -->
<div class="page" id="page-spendcube">
  <div class="ph"><div class="ph-title">Spend cube</div><div class="ph-sub">Supplier × Category × Time. Site / Business Unit dimension missing — must be added from ERP cost centre extract.</div></div>
  <div class="card">
    <div class="chart-lbl">Gross spend by category (£)</div>
    <div class="leg"><div class="leg-i"><div class="leg-sq" style="background:#185FA5"></div>Classified</div><div class="leg-i"><div class="leg-sq" style="background:#E24B4A"></div>Unclassified</div></div>
    <div class="chart-wrap" style="height:430px"><canvas id="catChart"></canvas></div>
  </div>
  <div class="card">
    <div class="card-title">Top 20 suppliers — ranked by spend</div>
    <div class="tbl-wrap">
      <table><thead><tr><th>#</th><th>Supplier</th><th>Gross</th><th>Region</th><th>Heuristic flag</th></tr></thead>
      <tbody id="suppTable"></tbody></table>
    </div>
  </div>
</div>

</div><!-- end main -->
</div><!-- end shell -->

<!-- FOOTER -->
<div class="footer">
  <span><b>Procurement Spend Intelligence Engine v0.4</b> · Analyst-operated · Not self-serve · Prepared by PSL · Client: [CLIENT NAME] · Dataset: [YEAR]</span>
  <span style="color:var(--coral)"><b>Disclaimer:</b> All saving estimates are indicative and heuristic-based. No savings are guaranteed. No benchmarks are implied unless explicitly stated as assumptions.</span>
</div>

<script>
// ---- NAVIGATION ----
function showPage(id,el){
  document.querySelectorAll('.page').forEach(p=>p.classList.remove('active'));
  document.querySelectorAll('.nav-item').forEach(n=>n.classList.remove('active'));
  document.getElementById('page-'+id).classList.add('active');
  if(el)el.classList.add('active');
}
function toggleH(hh){
  const b=hh.nextElementSibling, c=hh.querySelector('.chev');
  b.classList.toggle('open'); c.classList.toggle('open');
}

// ---- CHART SETUP ----
const dk=matchMedia('(prefers-color-scheme:dark)').matches;
const tc=dk?'rgba(255,255,255,.6)':'rgba(0,0,0,.45)';
const gc=dk?'rgba(255,255,255,.07)':'rgba(0,0,0,.05)';

// Monthly
new Chart(document.getElementById('monthlyChart'),{
  type:'bar',
  data:{
    labels:['Jan','Feb','Mar','Apr','May','Jun','Jul','Aug','Sep','Oct','Nov','Dec'],
    datasets:[{label:'Gross spend',
      data:[4879970,6102217,6149862,5528187,5269258,4951717,6194340,4105963,3304348,4899864,4249955,3553356],
      backgroundColor:[4879970,6102217,6149862,5528187,5269258,4951717,6194340,4105963,3304348,4899864,4249955,3553356]
        .map(v=>v===6194340?'#185FA5':v<4200000?'rgba(216,90,48,.65)':'#B5D4F4'),
      borderRadius:3}]
  },
  options:{responsive:true,maintainAspectRatio:false,plugins:{legend:{display:false}},
    scales:{x:{ticks:{color:tc,font:{size:11}},grid:{display:false},border:{display:false}},
    y:{ticks:{color:tc,font:{size:11},callback:v=>'£'+(v/1e6).toFixed(1)+'M'},grid:{color:gc},border:{display:false}}}}
});

// Region
new Chart(document.getElementById('regionChart'),{
  type:'doughnut',
  data:{labels:['UK/Ireland £21.4M','Unknown £17.5M','Benelux/DACH £10.7M','Spain/Italy £5.1M','Nordic/Baltic £4.5M'],
    datasets:[{data:[21424515,17531382,10677272,5077162,4478706],backgroundColor:['#185FA5','#B5D4F4','#1D9E75','#9FE1CB','#5DCAA5'],borderWidth:0}]},
  options:{responsive:true,maintainAspectRatio:false,cutout:'58%',
    plugins:{legend:{position:'bottom',labels:{color:tc,font:{size:11},boxWidth:10,padding:7}}}}
});

// Radar
new Chart(document.getElementById('radarChart'),{
  type:'radar',
  data:{labels:['Site analysis','YoY trend','Consolidation','Price variance','Batching','Logistics'],
    datasets:[
      {label:'Impact potential',data:[5,3,5,4,3,3],borderColor:'#185FA5',backgroundColor:'rgba(24,95,165,.1)',pointBackgroundColor:'#185FA5',borderWidth:1.5},
      {label:'Data readiness',data:[1,2,4,1,5,4],borderColor:'#1D9E75',backgroundColor:'rgba(29,158,117,.07)',pointBackgroundColor:'#1D9E75',borderWidth:1.5,borderDash:[4,3]}
    ]},
  options:{responsive:true,maintainAspectRatio:false,
    plugins:{legend:{position:'bottom',labels:{color:tc,font:{size:11},boxWidth:10,padding:7}}},
    scales:{r:{ticks:{color:tc,font:{size:9},stepSize:1},grid:{color:gc},pointLabels:{color:tc,font:{size:10}},min:0,max:5}}}
});

// Category
const cats=[
  {n:'Other / Unclassified',s:36190738,u:true},
  {n:'IT & Software',s:9144281,u:false},
  {n:'Subcontract Services',s:5231562,u:false},
  {n:'Electrical Equipment',s:1927696,u:false},
  {n:'Plant & Vehicle Hire',s:1522898,u:false},
  {n:'Cables & Wiring',s:1514956,u:false},
  {n:'Steel & Fixings',s:1213660,u:false},
  {n:'Design & Print',s:587866,u:false},
  {n:'Transport & Logistics',s:545109,u:false},
  {n:'Pipework & Fittings',s:476741,u:false},
  {n:'Professional Services',s:387902,u:false},
  {n:'Containers & Storage',s:308941,u:false},
  {n:'Pumps & Fluid Systems',s:86232,u:false},
  {n:'Interior & Fit-out',s:50455,u:false},
];
new Chart(document.getElementById('catChart'),{
  type:'bar',
  data:{labels:cats.map(c=>c.n),
    datasets:[{data:cats.map(c=>c.s),backgroundColor:cats.map(c=>c.u?'#E24B4A':'#185FA5'),borderRadius:3}]},
  options:{indexAxis:'y',responsive:true,maintainAspectRatio:false,plugins:{legend:{display:false}},
    scales:{x:{ticks:{color:tc,font:{size:11},callback:v=>'£'+(v/1e6).toFixed(1)+'M'},grid:{color:gc},border:{display:false}},
    y:{ticks:{color:tc,font:{size:11}},grid:{display:false},border:{display:false}}}}
});

// Supplier table
const supps=[
  {n:'PSI Global Group Ltd',s:3176886,r:'UK/Ireland',f:'H3: Consolidation candidate'},
  {n:'Down to Earth Electrical LTD',s:2792083,r:'UK/Ireland',f:'H3: Electrical anchor'},
  {n:'MONTAJES ELECTRICOS ANDREY SL',s:1461809,r:'Spain/Italy',f:'H1: Site-specific — validate'},
  {n:'ELECTROGESTION Y CONTROLS J.G',s:1400731,r:'Spain/Italy',f:'H4: Price variance — disabled'},
  {n:'Karl Steiger GmbH',s:1183217,r:'Benelux/DACH',f:'H1: Cross-region pricing'},
  {n:'Anord Mardix (UK) Ltd',s:1132873,r:'UK/Ireland',f:'H3: Consolidate with Electrical?'},
  {n:'ELECTRICAL & PUMP SERVICES LTD',s:1081979,r:'UK/Ireland',f:'H3: Consolidate with Anord?'},
  {n:'LANDGUARD ENGINEERING',s:1012968,r:'UK/Ireland',f:'H3: Subcontract review'},
  {n:'CARNVIEW CONSTRUCTION',s:1008286,r:'UK/Ireland',f:'H3: Subcontract review'},
  {n:'ARK INFINITY SOLUTIONS',s:926344,r:'Unknown',f:'H2: Validate entity/region'},
  {n:'ELAND CABLES (Euro)',s:906419,r:'Benelux/DACH',f:'H3: Consolidate with Cables?'},
  {n:'PAX Bouw-en Industrieservice',s:892978,r:'Benelux/DACH',f:'H3: Regional supplier'},
  {n:'UAB Eurodetra',s:886576,r:'Nordic/Baltic',f:'H1: Site mapping needed'},
  {n:'EXCISE ELECTRICAL LTD',s:848600,r:'UK/Ireland',f:'H3: Electrical consolidation'},
  {n:'ATM Fabrications Ltd',s:841928,r:'UK/Ireland',f:'H4: Disabled — data first'},
  {n:'Captain Projects BV',s:785368,r:'Benelux/DACH',f:'H6: £88K embedded logistics'},
  {n:'Krampitz Container Systems GmbH',s:760771,r:'Benelux/DACH',f:'H6: Transport in invoices'},
  {n:'D&S Process Solutions BV',s:709905,r:'Benelux/DACH',f:'H1: Cross-region'},
  {n:'SAS Transition OU',s:688089,r:'Nordic/Baltic',f:'H1: Site mapping needed'},
  {n:'Precision Switchgear & Controls',s:664567,r:'UK/Ireland',f:'H3: Electrical consolidation'},
];
const st=document.getElementById('suppTable');
supps.forEach((s,i)=>{
  st.innerHTML+=`<tr>
    <td style="color:var(--text3)">${i+1}</td>
    <td style="font-size:12px">${s.n}</td>
    <td style="font-weight:700">£${(s.s/1e6).toFixed(2)}M</td>
    <td style="font-size:11px;color:var(--text2)">${s.r}</td>
    <td style="font-size:11px;color:var(--text2)">${s.f}</td>
  </tr>`;
});

// Logistics table
const logLines=[
  {s:'Elinex Power Solutions B.V.',d:'Milestone 3 - 30.09% after delivery on site - Global',g:245204,dt:'2024-01-30',k:'delivery',note:'⚠ FALSE POSITIVE: milestone payment term, not freight — excluded'},
  {s:'KOHLER EMEA / Rehlko',d:'AR/Acknowledgement of Receipt of order N AV143263',g:174610,dt:'2024-02-15',k:'delivery',note:'⚠ FALSE POSITIVE: receipt acknowledgement, not freight — excluded'},
  {s:'CTC TANKBOUW BVBA',d:'10% at delivery 30d netto (×2 invoices)',g:287000,dt:'2024-02-08',k:'delivery',note:'⚠ FALSE POSITIVE: payment term — excluded'},
  {s:'Captain Projects BV',d:'Installation & deinstallation Datahall setup + transport',g:80575,dt:'2024-05-08',k:'transport',note:'Review: project service charge — may not be separable freight'},
  {s:'Advanced Engineering Group',d:'Cummins - Nugteren Transport - Genset Test',g:70000,dt:'2024-05-07',k:'transport',note:'Review: specialist equipment transport — likely genuine'},
  {s:'Aggreko Iberia S.A.',d:'Material Alquilado: Importe primeras 4 semanas transport',g:67358,dt:'2024-02-09',k:'transport',note:'Genuine: rental delivery charge'},
  {s:'canavan interiors',d:'Payment for Delivery of Goods as per terms',g:63395,dt:'2024-03-20',k:'delivery',note:'Genuine: delivery charge'},
  {s:'ABB B.V.',d:'PROJECT Design, manufacturing and delivery PRO EUR',g:57596,dt:'2024-04-09',k:'delivery',note:'Review: bundled in manufacturing cost?'},
  {s:'KOHLER EMEA / Rehlko',d:'AR/Acknowledgement of Receipt — Notre BL',g:28168,dt:'2024-08-23',k:'delivery',note:'⚠ FALSE POSITIVE: receipt acknowledgement — excluded'},
  {s:'Gessler GmbH',d:'Primus PQ5 - ceiling mounting + Q1 lens + delivery',g:11900,dt:'2024-08-13',k:'delivery',note:'Genuine: product delivery charge'},
  {s:'Captain Projects BV',d:'Installation & deinstallation + transport of equipment',g:7875,dt:'2024-04-16',k:'transport',note:'Review: service bundle'},
  {s:'Aggreko Iberia S.A.',d:'Extension semana transport 10/07/24-16/07/24',g:6454,dt:'2024-10-07',k:'transport',note:'Genuine: rental delivery'},
  {s:'MJ WILSON GROUP LTD',d:'PNP/PLC low voltage switching — carriage',g:5938,dt:'2024-11-20',k:'carriage',note:'Genuine: carriage charge'},
  {s:'S. KORONKA (Manufacturing) LTD',d:'Access Ladder c/w safety hoops — transport',g:5110,dt:'2024-02-27',k:'transport',note:'Genuine: delivery of fabricated item'},
  {s:'R &M Europe BV',d:'M12 Square Plate Washer — DELIVERY 7-10 DAYS',g:2519,dt:'2024-11-18',k:'delivery',note:'Genuine: delivery surcharge'},
  {s:'Gessler GmbH',d:'Primus PX5 - wall bracket Lieferdatum 07.08.2024',g:2499,dt:'2024-08-13',k:'delivery',note:'Review: German "Lieferdatum" = delivery date in description'},
  {s:'Collé Rental & Sales',d:'Rental objects transport assignment 00471749',g:2400,dt:'2024-09-04',k:'transport',note:'Genuine: rental delivery'},
  {s:'Krampitz Container Systems',d:'Transport costs combined with delivery',g:2112,dt:'2024-12-05',k:'transport',note:'Genuine: container delivery'},
  {s:'Kabeltec GmbH',d:'AA Carriage',g:2098,dt:'2024-04-29',k:'carriage',note:'Genuine: carriage charge'},
  {s:'END-Armaturen GmbH',d:'Ball valve — customs tariff + delivery',g:1981,dt:'2024-05-23',k:'delivery',note:'Genuine: delivery element of order'},
  {s:'ABB B.V.',d:'PROJECT CIP transport Batteries to Ireland',g:1936,dt:'2024-04-09',k:'transport',note:'Genuine: specialist freight'},
  {s:'Powerpoint Engineering Ltd',d:'Insulating matting IEC61111 — delivery',g:1908,dt:'2024-09-09',k:'delivery',note:'Genuine: delivery charge'},
  {s:'R &M Europe BV',d:'ML2X25MM H.T Setbolt BZP DELIVERY 7-10 DAYS',g:1895,dt:'2024-07-25',k:'delivery',note:'Genuine: delivery surcharge'},
  {s:'Elinex Power Solutions B.V.',d:'100% Storage 12 weeks, Handling fee, Transport',g:1856,dt:'2024-01-18',k:'transport',note:'Genuine: logistics/storage charge'},
  {s:'Roxtec BV',d:'RM00310121000 — freight included',g:1720,dt:'2024-03-14',k:'freight',note:'Genuine: freight element'},
];
const lt=document.getElementById('logisticsTable');
logLines.forEach((l,i)=>{
  const isFP=l.note.includes('FALSE POSITIVE');
  lt.innerHTML+=`<tr style="${isFP?'opacity:0.5;text-decoration:line-through;':''}" >
    <td style="color:var(--text3)">${i+1}</td>
    <td style="font-size:11px">${l.s}</td>
    <td style="font-size:11px">${l.d.substring(0,55)}${l.d.length>55?'…':''}</td>
    <td style="font-weight:700">£${l.g.toLocaleString()}</td>
    <td style="font-size:11px;color:var(--text3)">${l.dt}</td>
    <td><span class="pill pgr" style="font-size:10px">${l.k}</span></td>
    <td style="font-size:10px;color:${isFP?'var(--red)':'var(--text2)'};">${l.note}</td>
  </tr>`;
});
</script>
</body>
</html>
