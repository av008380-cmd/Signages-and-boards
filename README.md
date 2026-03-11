<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>IRC 67-2022 Road Sign Assistant</title>
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link href="https://fonts.googleapis.com/css2?family=Barlow+Condensed:wght@400;600;700;800&family=Barlow:wght@400;500;600&display=swap" rel="stylesheet">
    <style>
        :root {
            --yellow: #F5C400;
            --red: #D00000;
            --dark: #0D0D0D;
            --dark-2: #1A1A1A;
            --dark-3: #252525;
            --mid: #3A3A3A;
            --muted: #8A8580;
            --white: #F5F0E8;
        }

        * { box-sizing: border-box; margin: 0; padding: 0; }

        body {
            font-family: 'Barlow', sans-serif;
            background: var(--dark);
            color: var(--white);
            min-height: 100vh;
            background-image:
                repeating-linear-gradient(0deg, transparent, transparent 60px, rgba(255,255,255,0.013) 60px, rgba(255,255,255,0.013) 61px),
                repeating-linear-gradient(90deg, transparent, transparent 60px, rgba(255,255,255,0.013) 60px, rgba(255,255,255,0.013) 61px);
        }

        /* ─── HEADER ─── */
        .header-stripe { background: var(--red); height: 6px; }
        .header { background: var(--yellow); color: var(--dark); }
        .header-inner { display: flex; align-items: stretch; max-width: 1060px; margin: 0 auto; }
        .header-icon-box { background: var(--dark); padding: 1.25rem 1.5rem; display: flex; align-items: center; justify-content: center; flex-shrink: 0; }
        .header-sign-icon { width: 52px; height: 52px; }
        .header-text { padding: 1.25rem 1.5rem; flex: 1; }
        .header-text h1 { font-family: 'Barlow Condensed', sans-serif; font-size: 1.6rem; font-weight: 800; text-transform: uppercase; letter-spacing: 0.05em; line-height: 1.1; }
        .header-text p { font-size: 0.8rem; font-weight: 600; color: #5A4A00; text-transform: uppercase; letter-spacing: 0.1em; margin-top: 0.3rem; }
        .header-badge { background: var(--dark); color: var(--yellow); padding: 1.25rem 1.5rem; font-family: 'Barlow Condensed', sans-serif; font-size: 0.72rem; font-weight: 700; text-transform: uppercase; letter-spacing: 0.1em; display: flex; align-items: center; text-align: right; flex-shrink: 0; }
        @media(max-width: 500px) { .header-badge { display: none; } }

        /* ─── LAYOUT ─── */
        .container { max-width: 1060px; margin: 2rem auto; padding: 0 1rem; display: flex; flex-direction: column; gap: 1.5rem; }

        /* ─── CARDS ─── */
        .card { background: var(--dark-2); border: 1px solid var(--mid); border-radius: 4px; overflow: hidden; }
        .card-header { padding: 0.9rem 1.5rem; font-family: 'Barlow Condensed', sans-serif; font-size: 0.7rem; font-weight: 700; text-transform: uppercase; letter-spacing: 0.15em; display: flex; align-items: center; gap: 0.6rem; border-bottom: 1px solid var(--mid); }
        .card-header.primary { background: var(--dark-3); color: var(--yellow); border-bottom-color: rgba(245,196,0,0.2); }
        .card-header.secondary { background: var(--dark-3); color: var(--muted); }
        .card-body { padding: 1.5rem; }

        /* ─── FORM ROW ─── */
        .form-row { display: grid; grid-template-columns: 1fr; gap: 1.25rem; margin-bottom: 1.5rem; }
        @media(min-width: 640px) { .form-row { grid-template-columns: 1fr 1fr; } }
        @media(min-width: 900px) { .form-row { grid-template-columns: 1.4fr 1fr 1fr; } }
        .form-group { display: flex; flex-direction: column; gap: 0.5rem; position: relative; }

        label { font-family: 'Barlow Condensed', sans-serif; font-size: 0.65rem; font-weight: 700; text-transform: uppercase; letter-spacing: 0.15em; color: var(--muted); }

        /* ─── CUSTOM SEARCHABLE DROPDOWN ─── */
        .search-wrap { position: relative; }
        .search-input {
            width: 100%; padding: 0.75rem 2.5rem 0.75rem 1rem;
            background: var(--dark-3); border: 1px solid var(--mid); border-radius: 3px;
            color: var(--white); font-family: 'Barlow', sans-serif; font-size: 0.95rem; font-weight: 500;
            outline: none; transition: border-color 0.2s; cursor: pointer;
        }
        .search-input::placeholder { color: var(--muted); }
        .search-input:focus { border-color: var(--yellow); }
        .search-input.selected { border-color: rgba(245,196,0,0.5); }

        .arrow-icon {
            position: absolute; right: 0.9rem; top: 50%; transform: translateY(-50%);
            pointer-events: none; color: var(--muted); transition: transform 0.2s, color 0.2s;
        }
        .arrow-icon.open { transform: translateY(-50%) rotate(180deg); color: var(--yellow); }

        .dropdown {
            display: none; position: absolute; top: calc(100% + 4px); left: 0; right: 0;
            background: var(--dark-3); border: 1px solid var(--mid); border-radius: 3px;
            max-height: 300px; overflow-y: auto; z-index: 200;
            box-shadow: 0 12px 40px rgba(0,0,0,0.6);
        }
        .dropdown.open { display: block; animation: dropIn 0.15s ease; }
        @keyframes dropIn { from { opacity:0; transform:translateY(-4px); } to { opacity:1; transform:translateY(0); } }

        .dropdown::-webkit-scrollbar { width: 4px; }
        .dropdown::-webkit-scrollbar-track { background: var(--dark-3); }
        .dropdown::-webkit-scrollbar-thumb { background: var(--mid); border-radius: 2px; }

        .group-hdr {
            padding: 0.45rem 1rem 0.3rem; font-family: 'Barlow Condensed', sans-serif;
            font-size: 0.6rem; font-weight: 700; text-transform: uppercase; letter-spacing: 0.15em;
            color: var(--yellow); background: rgba(245,196,0,0.07);
            border-top: 1px solid var(--mid); border-bottom: 1px solid rgba(245,196,0,0.15);
            position: sticky; top: 0; z-index: 1;
        }
        .group-hdr:first-child { border-top: none; }

        .dd-item {
            padding: 0.55rem 1rem; font-size: 0.875rem; cursor: pointer;
            border-bottom: 1px solid rgba(255,255,255,0.04);
            display: flex; align-items: center; gap: 0.6rem;
            transition: background 0.1s;
        }
        .dd-item:last-child { border-bottom: none; }
        .dd-item:hover, .dd-item.hi { background: rgba(245,196,0,0.1); }
        .dd-item:hover .d-code, .dd-item.hi .d-code { color: rgba(245,196,0,0.7); }
        .dd-item:hover .d-name, .dd-item.hi .d-name { color: var(--yellow); }
        .d-code { font-family: 'Barlow Condensed', sans-serif; font-size: 0.7rem; font-weight: 700; color: var(--muted); min-width: 42px; flex-shrink: 0; }
        .d-name { flex: 1; color: var(--white); }
        .d-name mark { background: transparent; color: var(--yellow); font-weight: 700; }
        .no-res { padding: 1rem; font-size: 0.875rem; color: var(--muted); text-align: center; }

        /* ─── SELECT ─── */
        select {
            padding: 0.75rem 2.5rem 0.75rem 1rem; background: var(--dark-3); border: 1px solid var(--mid);
            border-radius: 3px; color: var(--white); font-family: 'Barlow', sans-serif;
            font-size: 0.95rem; font-weight: 500; cursor: pointer; outline: none;
            transition: border-color 0.2s; -webkit-appearance: none; appearance: none;
            background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='12' height='12' viewBox='0 0 24 24' fill='none' stroke='%238A8580' stroke-width='2.5'%3E%3Cpolyline points='6 9 12 15 18 9'/%3E%3C/svg%3E");
            background-repeat: no-repeat; background-position: right 0.9rem center; width: 100%;
        }
        select:focus { border-color: var(--yellow); }

        /* ─── BUTTON ─── */
        .btn {
            background: var(--yellow); color: var(--dark); border: none;
            padding: 0.85rem 2rem; border-radius: 3px; font-family: 'Barlow Condensed', sans-serif;
            font-size: 0.9rem; font-weight: 800; text-transform: uppercase; letter-spacing: 0.12em;
            cursor: pointer; display: inline-flex; align-items: center; gap: 0.6rem;
            transition: background 0.15s, transform 0.1s;
        }
        .btn:hover { background: #FFD700; transform: translateY(-1px); }
        .btn:active { transform: translateY(0); }

        /* ─── RESULT ─── */
        .result-box { margin-top: 1.5rem; border: 1px solid rgba(245,196,0,0.3); border-radius: 4px; overflow: hidden; display: none; }
        .result-box.show { display: block; animation: fadeUp 0.35s ease; }
        .result-box.err { border-color: rgba(220,38,38,0.4); }
        @keyframes fadeUp { from { opacity:0; transform:translateY(8px); } to { opacity:1; transform:translateY(0); } }

        .res-hdr {
            background: rgba(245,196,0,0.1); border-bottom: 1px solid rgba(245,196,0,0.2);
            padding: 0.75rem 1.25rem; font-family: 'Barlow Condensed', sans-serif;
            font-size: 0.65rem; font-weight: 700; text-transform: uppercase; letter-spacing: 0.15em;
            color: var(--yellow); display: flex; align-items: center; gap: 0.5rem;
        }
        .res-hdr.err { background: rgba(220,38,38,0.1); border-bottom-color: rgba(220,38,38,0.2); color: #f87171; }

        .res-grid { display: grid; grid-template-columns: 1fr 1fr; }
        @media(max-width: 520px) { .res-grid { grid-template-columns: 1fr; } .span2 { grid-column: span 1 !important; } }

        .res-item { padding: 1.1rem 1.25rem; border-right: 1px solid var(--mid); border-bottom: 1px solid var(--mid); }
        .res-item:nth-child(2n) { border-right: none; }
        .res-item:last-child { border-bottom: none; }
        .span2 { grid-column: span 2; border-right: none !important; }
        /* remove bottom border from last two items when 3-row grid */
        .res-grid .res-item:nth-last-child(1),
        .res-grid .res-item:nth-last-child(2):not(.span2) { border-bottom: none; }

        .rl { font-family: 'Barlow Condensed', sans-serif; font-size: 0.6rem; text-transform: uppercase; color: var(--muted); font-weight: 700; letter-spacing: 0.12em; margin-bottom: 0.35rem; display: flex; align-items: center; gap: 0.4rem; }
        .rl svg { color: var(--yellow); flex-shrink: 0; }
        .rv { font-size: 0.875rem; font-weight: 500; line-height: 1.5; }
        .rv.big { color: var(--yellow); font-family: 'Barlow Condensed', sans-serif; font-size: 1.35rem; font-weight: 800; }

        /* ─── GRID 2 ─── */
        .grid2 { display: grid; grid-template-columns: 1fr; gap: 1.5rem; }
        @media(min-width: 700px) { .grid2 { grid-template-columns: 1fr 1fr; } }

        /* ─── TABLE ─── */
        .tbl-wrap { overflow-x: auto; }
        table { width: 100%; border-collapse: collapse; font-size: 0.875rem; }
        th { background: var(--dark-3); padding: 0.75rem 1rem; text-align: left; font-family: 'Barlow Condensed', sans-serif; font-size: 0.65rem; font-weight: 700; text-transform: uppercase; letter-spacing: 0.12em; color: var(--muted); border-bottom: 1px solid var(--mid); }
        td { padding: 0.85rem 1rem; border-bottom: 1px solid rgba(255,255,255,0.05); vertical-align: top; line-height: 1.5; font-size: 0.875rem; }
        tr:last-child td { border-bottom: none; }
        tr:hover td { background: rgba(255,255,255,0.025); }
        .blue { color: #60A5FA; font-weight: 600; }

        /* ─── INSTALL LIST ─── */
        .inst-item { display: flex; gap: 1rem; padding: 1rem 1.5rem; border-bottom: 1px solid rgba(255,255,255,0.05); align-items: flex-start; }
        .inst-item:last-child { border-bottom: none; }
        .inst-dot { width: 28px; height: 28px; border-radius: 50%; background: rgba(245,196,0,0.1); border: 1px solid rgba(245,196,0,0.25); display: flex; align-items: center; justify-content: center; flex-shrink: 0; margin-top: 2px; }
        .inst-dot svg { color: var(--yellow); }
        .inst-txt strong { display: block; font-family: 'Barlow Condensed', sans-serif; font-size: 0.72rem; font-weight: 700; text-transform: uppercase; letter-spacing: 0.1em; margin-bottom: 0.2rem; }
        .inst-txt span { font-size: 0.875rem; color: var(--muted); line-height: 1.5; }
    </style>
</head>
<body>

<!-- ── HEADER ── -->
<div class="header-stripe"></div>
<div class="header">
    <div class="header-inner">
        <div class="header-icon-box">
            <svg class="header-sign-icon" viewBox="0 0 52 52" fill="none" xmlns="http://www.w3.org/2000/svg">
                <polygon points="26,4 49,16 49,36 26,48 3,36 3,16" fill="#F5C400" stroke="#0D0D0D" stroke-width="2"/>
                <text x="26" y="31" text-anchor="middle" font-family="Barlow Condensed,sans-serif" font-weight="800" font-size="14" fill="#0D0D0D">IRC</text>
            </svg>
        </div>
        <div class="header-text">
            <h1>IRC 67-2022 Road Sign Assistant</h1>
            <p>Technical Specifications, Sizing, Gantries &amp; Post Dimensions · Indian Roads</p>
        </div>
        <div class="header-badge">Ministry of Road<br>Transport &amp; Highways</div>
    </div>
</div>

<div class="container">

    <!-- ── CALCULATOR ── -->
    <div class="card">
        <div class="card-header primary">
            <svg xmlns="http://www.w3.org/2000/svg" width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><rect width="16" height="20" x="4" y="2" rx="2"/><line x1="8" x2="16" y1="6" y2="6"/><line x1="16" x2="16" y1="14" y2="18"/><path d="M16 10h.01"/><path d="M12 10h.01"/><path d="M8 10h.01"/><path d="M12 14h.01"/><path d="M8 14h.01"/><path d="M12 18h.01"/><path d="M8 18h.01"/></svg>
            Sign &amp; Support Calculator
        </div>
        <div class="card-body">
            <div class="form-row">

                <!-- Searchable Sign Dropdown -->
                <div class="form-group">
                    <label for="signSearch">Search IRC Sign Board</label>
                    <div class="search-wrap">
                        <input type="text" id="signSearch" class="search-input"
                            placeholder="Type name or code — e.g. R-1, Stop, Narrow Bridge…"
                            autocomplete="off" spellcheck="false">
                        <svg class="arrow-icon" id="arrowIcon" xmlns="http://www.w3.org/2000/svg" width="14" height="14"
                            viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5"
                            stroke-linecap="round" stroke-linejoin="round"><polyline points="6 9 12 15 18 9"/></svg>
                        <div class="dropdown" id="ddList" role="listbox"></div>
                    </div>
                    <!-- hidden fields -->
                    <input type="hidden" id="hType" value="">
                    <input type="hidden" id="hName" value="">
                </div>

                <!-- Speed -->
                <div class="form-group">
                    <label for="speed">Design Speed of Road</label>
                    <select id="speed">
                        <option value="50">Up to 50 kmph</option>
                        <option value="65">51 – 65 kmph</option>
                        <option value="80">66 – 80 kmph</option>
                        <option value="120">81 – 120 kmph</option>
                        <option value="150">Expressways (&gt;120 kmph)</option>
                    </select>
                </div>

                <!-- Mounting -->
                <div class="form-group">
                    <label for="mounting">Mounting Type</label>
                    <select id="mounting">
                        <option value="shoulder">Shoulder / Post Mounted</option>
                        <option value="gantry">Overhead / Gantry / Cantilever</option>
                    </select>
                </div>
            </div>

            <button onclick="calcSpecs()" class="btn">
                <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24" fill="none"
                    stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
                    <path d="M22 11.08V12a10 10 0 1 1-5.93-9.14"/><path d="m9 11 3 3L22 4"/>
                </svg>
                Generate Specifications
            </button>

            <div id="result" class="result-box">
                <div class="res-hdr" id="resHdr">
                    <svg xmlns="http://www.w3.org/2000/svg" width="13" height="13" viewBox="0 0 24 24" fill="none"
                        stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
                        <circle cx="12" cy="12" r="10"/><path d="M12 16v-4"/><path d="M12 8h.01"/>
                    </svg>
                    <span id="resHdrTxt">Required Specifications — IRC 67-2022</span>
                </div>
                <div id="resOut" class="res-grid"></div>
            </div>
        </div>
    </div>

    <!-- ── BOTTOM GRID ── -->
    <div class="grid2">

        <!-- Materials -->
        <div class="card">
            <div class="card-header secondary">
                <svg xmlns="http://www.w3.org/2000/svg" width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><polygon points="12 2 2 7 12 12 22 7 12 2"/><polyline points="2 17 12 22 22 17"/><polyline points="2 12 12 17 22 12"/></svg>
                Post &amp; Structure Materials
            </div>
            <div class="tbl-wrap">
                <table>
                    <thead><tr><th>Component</th><th>IRC Specification</th></tr></thead>
                    <tbody>
                        <tr><td>Sign Board Substrate</td><td>Aluminium 1.5–2.0 mm <em>or</em> ACM 3.0–4.0 mm</td></tr>
                        <tr><td>Posts (Small Boards)</td><td>Hot-Dip GI Pipe 50 NB or 75 NB / MS Hollow Section</td></tr>
                        <tr><td>Large Boards (&gt;0.9 m²)</td><td>Minimum two posts (GI/MS) to resist wind-induced twist</td></tr>
                        <tr><td>Gantries / Cantilevers</td><td>Heavy MS structural truss — Hot-Dip Galvanized</td></tr>
                        <tr><td>Class A Sheeting</td><td>Engineering Grade — Village Roads</td></tr>
                        <tr><td>Class B Sheeting</td><td>High Intensity — MDR / Work Zones</td></tr>
                        <tr><td>Class C Sheeting</td><td class="blue">Micro-Prismatic — Mandatory for Highways &amp; Expressways</td></tr>
                    </tbody>
                </table>
            </div>
        </div>

        <!-- Installation -->
        <div class="card">
            <div class="card-header secondary">
                <svg xmlns="http://www.w3.org/2000/svg" width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M21.3 15.3l-5.6 5.6a2.828 2.828 0 0 1-4 0l-9.9-9.9a2.828 2.828 0 0 1 0-4l5.6-5.6a2.828 2.828 0 0 1 4 0l9.9 9.9a2.828 2.828 0 0 1 0 4Z"/></svg>
                Vertical &amp; Lateral Clearances
            </div>
            <div>
                <div class="inst-item">
                    <div class="inst-dot"><svg xmlns="http://www.w3.org/2000/svg" width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M5 3h14"/><path d="m18 13-6-6-6 6"/><path d="M12 7v14"/></svg></div>
                    <div class="inst-txt"><strong>Roads with Kerbs (Urban)</strong><span>Bottom of sign to top of kerb: <b>2.1 m – 2.5 m</b>.</span></div>
                </div>
                <div class="inst-item">
                    <div class="inst-dot"><svg xmlns="http://www.w3.org/2000/svg" width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="m5 12 7-7 7 7"/><path d="M12 19V5"/></svg></div>
                    <div class="inst-txt"><strong>Roads without Kerbs (Open Country)</strong><span>Bottom of sign to pavement crown: <b>2.0 m – 2.5 m</b>.</span></div>
                </div>
                <div class="inst-item">
                    <div class="inst-dot"><svg xmlns="http://www.w3.org/2000/svg" width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M8 6L12 2L16 6"/><path d="M12 2V22"/></svg></div>
                    <div class="inst-txt"><strong>Overhead / Gantry Signs</strong><span>Minimum <b>5.5 m vertical clearance</b> over full carriageway width.</span></div>
                </div>
                <div class="inst-item">
                    <div class="inst-dot"><svg xmlns="http://www.w3.org/2000/svg" width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><polyline points="18 8 22 12 18 16"/><polyline points="6 8 2 12 6 16"/><line x1="2" x2="22" y1="12" y2="12"/></svg></div>
                    <div class="inst-txt"><strong>Lateral Clearance</strong><span>Extreme edge of sign: <b>0.6 m – 3.0 m</b> from earthen shoulder.</span></div>
                </div>
                <div class="inst-item">
                    <div class="inst-dot"><svg xmlns="http://www.w3.org/2000/svg" width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="10"/><path d="M12 8v4l3 3"/></svg></div>
                    <div class="inst-txt"><strong>Sign Face Orientation</strong><span>Perpendicular to road ± 5°. Retroreflective face must face oncoming traffic.</span></div>
                </div>
            </div>
        </div>
    </div>
</div><!-- /container -->

<script>
// ═══════════════════════════════════════════════════════
//  COMPLETE IRC 67-2022 SIGN DATABASE
// ═══════════════════════════════════════════════════════
const DB = [
    // ── MANDATORY / REGULATORY ──
    { g:"Mandatory / Regulatory Signs (R-Series)", code:"R-1",  name:"Stop",                                        type:"stop"       },
    { g:"Mandatory / Regulatory Signs (R-Series)", code:"R-2",  name:"Give Way",                                    type:"giveway"    },
    { g:"Mandatory / Regulatory Signs (R-Series)", code:"R-3",  name:"No Entry / Straight Prohibited",              type:"mandatory"  },
    { g:"Mandatory / Regulatory Signs (R-Series)", code:"R-4",  name:"One Way",                                     type:"mandatory"  },
    { g:"Mandatory / Regulatory Signs (R-Series)", code:"R-5",  name:"Vehicles Prohibited in Both Directions",      type:"mandatory"  },
    { g:"Mandatory / Regulatory Signs (R-Series)", code:"R-6",  name:"Right Turn Prohibited",                       type:"mandatory"  },
    { g:"Mandatory / Regulatory Signs (R-Series)", code:"R-7",  name:"Left Turn Prohibited",                        type:"mandatory"  },
    { g:"Mandatory / Regulatory Signs (R-Series)", code:"R-8",  name:"U-Turn Prohibited",                           type:"mandatory"  },
    { g:"Mandatory / Regulatory Signs (R-Series)", code:"R-9",  name:"Overtaking Prohibited",                       type:"mandatory"  },
    { g:"Mandatory / Regulatory Signs (R-Series)", code:"R-10", name:"Horn Prohibited (No Horn Zone)",              type:"mandatory"  },
    { g:"Mandatory / Regulatory Signs (R-Series)", code:"R-11", name:"No Parking",                                  type:"mandatory"  },
    { g:"Mandatory / Regulatory Signs (R-Series)", code:"R-12", name:"No Stopping or Standing",                     type:"mandatory"  },
    { g:"Mandatory / Regulatory Signs (R-Series)", code:"R-13", name:"Speed Limit",                                 type:"mandatory"  },
    { g:"Mandatory / Regulatory Signs (R-Series)", code:"R-14", name:"Vehicle Width Limit",                         type:"mandatory"  },
    { g:"Mandatory / Regulatory Signs (R-Series)", code:"R-15", name:"Vehicle Height Limit",                        type:"mandatory"  },
    { g:"Mandatory / Regulatory Signs (R-Series)", code:"R-16", name:"Vehicle Length Limit",                        type:"mandatory"  },
    { g:"Mandatory / Regulatory Signs (R-Series)", code:"R-17", name:"Axle Load Limit",                             type:"mandatory"  },
    { g:"Mandatory / Regulatory Signs (R-Series)", code:"R-18", name:"Compulsory Keep Left",                        type:"mandatory"  },
    { g:"Mandatory / Regulatory Signs (R-Series)", code:"R-19", name:"Compulsory Ahead Only",                       type:"mandatory"  },
    { g:"Mandatory / Regulatory Signs (R-Series)", code:"R-20", name:"Compulsory Turn Right",                       type:"mandatory"  },
    { g:"Mandatory / Regulatory Signs (R-Series)", code:"R-21", name:"Compulsory Turn Left",                        type:"mandatory"  },
    { g:"Mandatory / Regulatory Signs (R-Series)", code:"R-22", name:"Compulsory Ahead or Turn Right",              type:"mandatory"  },
    { g:"Mandatory / Regulatory Signs (R-Series)", code:"R-23", name:"Compulsory Ahead or Turn Left",               type:"mandatory"  },
    { g:"Mandatory / Regulatory Signs (R-Series)", code:"R-24", name:"Compulsory Keep Right",                       type:"mandatory"  },
    { g:"Mandatory / Regulatory Signs (R-Series)", code:"R-25", name:"Compulsory Cycle Track",                      type:"mandatory"  },
    { g:"Mandatory / Regulatory Signs (R-Series)", code:"R-26", name:"Compulsory Sound Horn",                       type:"mandatory"  },
    { g:"Mandatory / Regulatory Signs (R-Series)", code:"R-27", name:"Pedestrians Only",                            type:"mandatory"  },
    { g:"Mandatory / Regulatory Signs (R-Series)", code:"R-28", name:"End of All Prohibitions",                     type:"mandatory"  },
    { g:"Mandatory / Regulatory Signs (R-Series)", code:"R-29", name:"Trucks Prohibited",                           type:"mandatory"  },
    { g:"Mandatory / Regulatory Signs (R-Series)", code:"R-30", name:"Motor Vehicles Prohibited",                   type:"mandatory"  },
    { g:"Mandatory / Regulatory Signs (R-Series)", code:"R-31", name:"Tractor Prohibited",                          type:"mandatory"  },
    { g:"Mandatory / Regulatory Signs (R-Series)", code:"R-32", name:"Cycle Rickshaw Prohibited",                   type:"mandatory"  },
    { g:"Mandatory / Regulatory Signs (R-Series)", code:"R-33", name:"Bicycle Prohibited",                          type:"mandatory"  },
    { g:"Mandatory / Regulatory Signs (R-Series)", code:"R-34", name:"Pedestrians Prohibited",                      type:"mandatory"  },
    { g:"Mandatory / Regulatory Signs (R-Series)", code:"R-35", name:"Animal Drawn Vehicle Prohibited",             type:"mandatory"  },
    { g:"Mandatory / Regulatory Signs (R-Series)", code:"R-36", name:"Hand Cart Prohibited",                        type:"mandatory"  },

    // ── CAUTIONARY / WARNING ──
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-1",  name:"Right Hair Pin Bend",                          type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-2",  name:"Left Hair Pin Bend",                           type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-3",  name:"Right Reverse Bend",                           type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-4",  name:"Left Reverse Bend",                            type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-5",  name:"Right Reverse Curve",                          type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-6",  name:"Left Reverse Curve",                           type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-7",  name:"Winding Road (Right First)",                   type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-8",  name:"Winding Road (Left First)",                    type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-9",  name:"Sharp Deviation of Route to Right",            type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-10", name:"Sharp Deviation of Route to Left",             type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-11", name:"Straight Road Ahead (End of Curve)",           type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-12", name:"Cross Road",                                   type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-13", name:"Y-Intersection (Right)",                       type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-14", name:"Y-Intersection (Left)",                        type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-15", name:"T-Intersection (Right)",                       type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-16", name:"T-Intersection (Left)",                        type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-17", name:"Staggered Intersection",                       type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-18", name:"Major Road Ahead (Priority Intersection)",     type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-19", name:"Roundabout Ahead",                             type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-20", name:"Dangerous Dip",                                type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-21", name:"Hump or Rough Road",                           type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-22", name:"Barrier Ahead",                                type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-23", name:"Loose Gravel",                                 type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-24", name:"Falling Rocks (Right Side)",                   type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-25", name:"Falling Rocks (Left Side)",                    type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-26", name:"Pedestrian Crossing Ahead",                    type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-27", name:"School Ahead (Children Crossing)",             type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-28", name:"Men at Work",                                  type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-29", name:"Cattle / Animal Crossing",                     type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-30", name:"Steep Ascent",                                 type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-31", name:"Steep Descent",                                type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-32", name:"Narrow Road Ahead",                            type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-33", name:"Road Widens Ahead",                            type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-34", name:"Narrow Bridge Ahead",                          type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-35", name:"Slippery Road",                                type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-36", name:"Cycle Crossing",                               type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-37", name:"Ferry Crossing",                               type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-38", name:"Quayside or River Bank",                       type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-39", name:"Opening or Swing Bridge",                      type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-40", name:"Unguarded Railway Crossing (within 50 m)",     type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-41", name:"Unguarded Railway Crossing (within 300 m)",    type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-42", name:"Guarded Railway Crossing",                     type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-43", name:"Forewarning of Unguarded Level Crossing",      type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-44", name:"Forewarning of Guarded Level Crossing",        type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-45", name:"Ice Road",                                     type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-46", name:"Cross Wind",                                   type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-47", name:"Traffic Signal Ahead",                         type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-48", name:"Speed Breaker Ahead",                          type:"cautionary" },
    { g:"Cautionary / Warning Signs (C-Series)", code:"C-49", name:"Accident Prone Zone",                          type:"cautionary" },

    // ── INFORMATORY / GUIDE ──
    { g:"Informatory / Guide Signs (I-Series)", code:"I-1",  name:"Advance Direction Sign (Junction)",             type:"informatory"},
    { g:"Informatory / Guide Signs (I-Series)", code:"I-2",  name:"Advance Direction Sign (Route Bifurcation)",    type:"informatory"},
    { g:"Informatory / Guide Signs (I-Series)", code:"I-3",  name:"Destination / Distance Sign",                   type:"informatory"},
    { g:"Informatory / Guide Signs (I-Series)", code:"I-4",  name:"Route Marker (National Highway)",               type:"informatory"},
    { g:"Informatory / Guide Signs (I-Series)", code:"I-5",  name:"Route Marker (State Highway)",                  type:"informatory"},
    { g:"Informatory / Guide Signs (I-Series)", code:"I-6",  name:"Route Marker (Major District Road)",            type:"informatory"},
    { g:"Informatory / Guide Signs (I-Series)", code:"I-7",  name:"Kilometre Stone",                               type:"informatory"},
    { g:"Informatory / Guide Signs (I-Series)", code:"I-8",  name:"Location Sign (State / District Boundary)",     type:"informatory"},
    { g:"Informatory / Guide Signs (I-Series)", code:"I-9",  name:"Place Identification Sign",                     type:"informatory"},
    { g:"Informatory / Guide Signs (I-Series)", code:"I-10", name:"Public Telephone",                              type:"facility"   },
    { g:"Informatory / Guide Signs (I-Series)", code:"I-11", name:"Petrol Pump / Filling Station",                 type:"facility"   },
    { g:"Informatory / Guide Signs (I-Series)", code:"I-12", name:"Hospital",                                      type:"facility"   },
    { g:"Informatory / Guide Signs (I-Series)", code:"I-13", name:"First Aid Post",                                type:"facility"   },
    { g:"Informatory / Guide Signs (I-Series)", code:"I-14", name:"Eating Place / Restaurant",                     type:"facility"   },
    { g:"Informatory / Guide Signs (I-Series)", code:"I-15", name:"Resting Place / Motel",                         type:"facility"   },
    { g:"Informatory / Guide Signs (I-Series)", code:"I-16", name:"No Through Road",                               type:"informatory"},
    { g:"Informatory / Guide Signs (I-Series)", code:"I-17", name:"Parking Both Sides",                            type:"facility"   },
    { g:"Informatory / Guide Signs (I-Series)", code:"I-18", name:"Parking Right Side",                            type:"facility"   },
    { g:"Informatory / Guide Signs (I-Series)", code:"I-19", name:"Parking Left Side",                             type:"facility"   },
    { g:"Informatory / Guide Signs (I-Series)", code:"I-20", name:"Parking for Two Wheelers",                      type:"facility"   },
    { g:"Informatory / Guide Signs (I-Series)", code:"I-21", name:"Bus Stop",                                      type:"facility"   },
    { g:"Informatory / Guide Signs (I-Series)", code:"I-22", name:"Light Refreshment",                             type:"facility"   },
    { g:"Informatory / Guide Signs (I-Series)", code:"I-23", name:"Weigh Bridge",                                  type:"facility"   },
    { g:"Informatory / Guide Signs (I-Series)", code:"I-24", name:"Toll Plaza Ahead",                              type:"informatory"},
    { g:"Informatory / Guide Signs (I-Series)", code:"I-25", name:"Filling Station (LPG / CNG)",                   type:"facility"   },
    { g:"Informatory / Guide Signs (I-Series)", code:"I-26", name:"Automobile Repair Workshop",                    type:"facility"   },
    { g:"Informatory / Guide Signs (I-Series)", code:"I-27", name:"Police Station",                                type:"facility"   },
    { g:"Informatory / Guide Signs (I-Series)", code:"I-28", name:"Post Office",                                   type:"facility"   },
    { g:"Informatory / Guide Signs (I-Series)", code:"I-29", name:"River / Water Body Ahead",                      type:"informatory"},
    { g:"Informatory / Guide Signs (I-Series)", code:"I-30", name:"Tourist Information Centre",                    type:"facility"   },
    { g:"Informatory / Guide Signs (I-Series)", code:"I-31", name:"Fire Station",                                  type:"facility"   },
    { g:"Informatory / Guide Signs (I-Series)", code:"I-32", name:"Walking Track",                                 type:"facility"   },
    { g:"Informatory / Guide Signs (I-Series)", code:"I-33", name:"Cycle Track",                                   type:"facility"   },
    { g:"Informatory / Guide Signs (I-Series)", code:"I-34", name:"Overhead Electric Cable Warning",               type:"informatory"},
    { g:"Informatory / Guide Signs (I-Series)", code:"I-35", name:"Overhead Bridge",                               type:"informatory"},

    // ── WORK ZONE ──
    { g:"Work Zone / Temporary Signs (W-Series)", code:"W-1", name:"Road Closed (Work Zone)",                      type:"cautionary" },
    { g:"Work Zone / Temporary Signs (W-Series)", code:"W-2", name:"Detour Ahead",                                 type:"cautionary" },
    { g:"Work Zone / Temporary Signs (W-Series)", code:"W-3", name:"Flagger / Traffic Controller Ahead",           type:"cautionary" },
    { g:"Work Zone / Temporary Signs (W-Series)", code:"W-4", name:"Lane Closed Ahead",                            type:"cautionary" },
    { g:"Work Zone / Temporary Signs (W-Series)", code:"W-5", name:"Road Work Ahead",                              type:"cautionary" },
    { g:"Work Zone / Temporary Signs (W-Series)", code:"W-6", name:"Temporary Speed Limit",                        type:"mandatory"  },
    { g:"Work Zone / Temporary Signs (W-Series)", code:"W-7", name:"End of Work Zone",                             type:"informatory"},
];

// group map for rendering
const GROUPS = {};
DB.forEach(s => { if(!GROUPS[s.g]) GROUPS[s.g]=[]; GROUPS[s.g].push(s); });

// ═══════════════════════════════════════════════════════
//  DROPDOWN LOGIC
// ═══════════════════════════════════════════════════════
const inp  = document.getElementById('signSearch');
const ddEl = document.getElementById('ddList');
const arrw = document.getElementById('arrowIcon');
const hTyp = document.getElementById('hType');
const hNam = document.getElementById('hName');

let hiIdx = -1;
let visItems = [];

function hi(txt, q) {
    if(!q) return txt;
    const i = txt.toLowerCase().indexOf(q.toLowerCase());
    if(i<0) return txt;
    return txt.slice(0,i)+'<mark>'+txt.slice(i,i+q.length)+'</mark>'+txt.slice(i+q.length);
}

function renderDD(q){
    ddEl.innerHTML=''; visItems=[];
    const lq = q.toLowerCase().trim();

    const grpKeys = Object.keys(GROUPS);
    grpKeys.forEach(gk => {
        const signs = lq
            ? GROUPS[gk].filter(s => s.name.toLowerCase().includes(lq) || s.code.toLowerCase().includes(lq))
            : GROUPS[gk];
        if(!signs.length) return;

        const hdr = document.createElement('div');
        hdr.className='group-hdr'; hdr.textContent=gk;
        ddEl.appendChild(hdr);

        signs.forEach(s=>{
            visItems.push(s);
            const el = document.createElement('div');
            el.className='dd-item'; el.setAttribute('role','option');
            el.dataset.type=s.type; el.dataset.code=s.code; el.dataset.name=s.name;
            el.innerHTML=`<span class="d-code">${s.code}</span><span class="d-name">${hi(s.name,q)}</span>`;
            el.addEventListener('mousedown', e=>{ e.preventDefault(); pick(s); });
            ddEl.appendChild(el);
        });
    });

    if(!ddEl.children.length){
        const d=document.createElement('div'); d.className='no-res';
        d.textContent='No matching signs found.'; ddEl.appendChild(d);
    }
    hiIdx=-1;
}

function pick(s){
    inp.value = s.code + ' – ' + s.name;
    hTyp.value = s.type;
    hNam.value = s.code + ' – ' + s.name;
    inp.classList.add('selected');
    close();
}

function open(){
    renderDD(inp.value);
    ddEl.classList.add('open');
    arrw.classList.add('open');
}

function close(){
    ddEl.classList.remove('open');
    arrw.classList.remove('open');
    hiIdx=-1;
}

inp.addEventListener('focus', open);
inp.addEventListener('input', ()=>{
    hTyp.value=''; hNam.value='';
    inp.classList.remove('selected');
    renderDD(inp.value);
    ddEl.classList.add('open'); arrw.classList.add('open');
});
inp.addEventListener('blur', ()=>{ setTimeout(close,160); });
inp.addEventListener('keydown', e=>{
    const items = ddEl.querySelectorAll('.dd-item');
    if(e.key==='ArrowDown'){ e.preventDefault(); hiIdx=Math.min(hiIdx+1,items.length-1); updHi(items); }
    else if(e.key==='ArrowUp'){ e.preventDefault(); hiIdx=Math.max(hiIdx-1,0); updHi(items); }
    else if(e.key==='Enter'){ e.preventDefault(); if(hiIdx>=0&&items[hiIdx]) items[hiIdx].dispatchEvent(new MouseEvent('mousedown')); }
    else if(e.key==='Escape') close();
});

function updHi(items){
    items.forEach((el,i)=>el.classList.toggle('hi',i===hiIdx));
    if(items[hiIdx]) items[hiIdx].scrollIntoView({block:'nearest'});
}

// ═══════════════════════════════════════════════════════
//  SPECIFICATION LOGIC
// ═══════════════════════════════════════════════════════
function calcSpecs(){
    const type     = hTyp.value;
    const label    = hNam.value || inp.value.trim();
    const spd      = parseInt(document.getElementById('speed').value);
    const mounting = document.getElementById('mounting').value;
    const resDiv   = document.getElementById('result');
    const resOut   = document.getElementById('resOut');
    const resHdr   = document.getElementById('resHdr');
    const resHdrT  = document.getElementById('resHdrTxt');

    // ── Error: nothing selected ──
    if(!type){
        resDiv.className='result-box err';
        resHdr.className='res-hdr err';
        resHdrT.textContent='No Sign Selected';
        resOut.innerHTML=`<div class="res-item span2">
            <span class="rl">Error</span>
            <span class="rv">Please select a valid IRC sign board from the dropdown list before generating specifications.</span>
        </div>`;
        pop(resDiv); return;
    }

    resDiv.className='result-box';
    resHdr.className='res-hdr';
    resHdrT.textContent='Specifications — ' + label;

    let shape='', dims='', placement='Edge clearance: 0.6 m – 3.0 m from earthen shoulder.', design='', postH='', postM='';

    // ── GANTRY / OVERHEAD ──
    if(mounting==='gantry'){
        postH = 'Minimum 5.5 m vertical clearance over entire carriageway width.';
        postM = 'Heavy structural Mild Steel (MS) truss / cantilever framework. Mandatory Hot-Dip Galvanization. Design must account for wind load per IS 875.';
        placement = 'Sign panel suspended directly above the lane(s) it applies to.';

        if(type==='informatory'||type==='facility'){
            shape='Large Rectangular Gantry Panel';
            design='Green background (National/State Highway) or Blue background (Expressway/Urban). Micro-prismatic Class C retroreflective sheeting mandatory.';
            dims = spd<=80 ? 'X-Height: 215 – 250 mm (panel size determined by text content)' : 'X-Height: 300 – 400 mm for Expressway gantries';
        } else {
            shape='Oversized Circular / Triangular (size upgraded by one class)';
            dims='Minimum 1500 mm diameter/side — upscaled for gantry visibility';
            design='Regulatory / Warning signs on gantries are upscaled to maximum size. Micro-prismatic Class C sheeting required.';
        }
    }
    // ── SHOULDER / POST MOUNTED ──
    else {
        postH = 'Urban (with kerb): 2.1 m – 2.5 m above kerb top.\nRural (no kerb): 2.0 m – 2.5 m above pavement crown.';
        postM = 'Hot-Dip GI Pipe 50 NB / 75 NB or MS Hollow Section. Use two posts if board area > 0.9 m².';

        if(type==='stop'){
            shape='Octagonal';
            design="Red background, white border 40 mm wide, 'STOP' in white Helvetica Bold. Class C sheeting on highways.";
            placement='Placed 1.5 m – 6 m before the stop line. Standalone post — no other sign on same post.';
            if(spd<=50)      { dims='750 mm height × 750 mm width'; }
            else if(spd<=65) { dims='900 mm height × 900 mm width'; }
            else             { dims='1200 mm height × 1200 mm width'; }
        }
        else if(type==='giveway'){
            shape='Inverted Equilateral Triangle (Apex Down)';
            design='Red border 50 mm wide, white background. Corners rounded 20 mm radius.';
            placement='Installed at or just before the yield line at the intersection.';
            if(spd<=50)       { dims='600 mm × 600 mm × 600 mm (side lengths)'; }
            else if(spd<=65)  { dims='750 mm × 750 mm × 750 mm (side lengths)'; }
            else if(spd<=80)  { dims='900 mm × 900 mm × 900 mm (side lengths)'; }
            else if(spd<=120) { dims='1200 mm × 1200 mm × 1200 mm (side lengths)'; }
            else              { dims='1500 mm × 1500 mm × 1500 mm (side lengths)'; }
        }
        else if(type==='mandatory'){
            shape='Circular';
            design='Prohibitory: red border, white background, black symbol.\nCompulsory: blue background, white symbol.';
            if(spd<=50)       { dims='600 mm diameter'; }
            else if(spd<=65)  { dims='750 mm diameter'; }
            else if(spd<=80)  { dims='900 mm diameter'; }
            else if(spd<=120) { dims='1200 mm diameter'; }
            else              { dims='1500 mm diameter'; }
        }
        else if(type==='cautionary'){
            shape='Equilateral Triangle (Apex Up)';
            design='Red border 50 mm, white background, black symbol. Corners rounded to 20 mm radius.';
            if(spd<=50)       { dims='600 mm side ≈ 600 × 520 mm (W × H)';  placement='Placed ~45 m before the hazard.'; }
            else if(spd<=65)  { dims='750 mm side ≈ 750 × 650 mm (W × H)';  placement='Placed 45 – 110 m before the hazard.'; }
            else if(spd<=80)  { dims='900 mm side ≈ 900 × 780 mm (W × H)';  placement='Placed 110 – 180 m before the hazard.'; }
            else if(spd<=120) { dims='1200 mm side ≈ 1200 × 1040 mm (W × H)'; placement='Placed 180 – 245 m before the hazard.'; }
            else              { dims='1500 mm side ≈ 1500 × 1300 mm (W × H)'; placement='Placed 245 – 305 m before the hazard.'; }
        }
        else if(type==='facility'){
            shape='Rectangular';
            design='Blue background, white border, standard white symbols. Class B or C sheeting per road class.';
            if(spd<=65)       { dims='600 mm × 450 mm (W × H)'; }
            else if(spd<=80)  { dims='800 mm × 600 mm (W × H)'; }
            else              { dims='900 mm × 600 mm (W × H)'; }
        }
        else { // informatory
            shape='Rectangular (panel size determined by text)';
            design='Green background — National/State Highways.\nBlue background — Expressways & Urban.\nWhite background — Village Roads.';
            placement='Advance direction signs: 500 m – 1 km before the intersection depending on design speed.';
            if(spd<=50)       { dims='X-Height: 100 mm (panel width by text)'; }
            else if(spd<=65)  { dims='X-Height: 125 mm (panel width by text)'; }
            else if(spd<=80)  { dims='X-Height: 150 mm (panel width by text)'; }
            else if(spd<=120) { dims='X-Height: 215 – 250 mm (panel width by text)'; }
            else              { dims='X-Height: 300 mm (panel width by text)'; }
        }
    }

    resOut.innerHTML=`
        <div class="res-item">
            <div class="rl">
                <svg xmlns="http://www.w3.org/2000/svg" width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><rect x="3" y="3" width="18" height="18" rx="2"/><path d="m9 12 2 2 4-4"/></svg>
                Board Shape &amp; Dimensions
            </div>
            <div class="rv" style="margin-bottom:0.3rem">${shape}</div>
            <div class="rv big">${dims}</div>
        </div>
        <div class="res-item">
            <div class="rl">
                <svg xmlns="http://www.w3.org/2000/svg" width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M12 2v20"/><path d="M17 5H9.5a3.5 3.5 0 0 0 0 7h5a3.5 3.5 0 0 1 0 7H6"/></svg>
                Support Post / Structure (IRC)
            </div>
            <div class="rv">${postM}</div>
        </div>
        <div class="res-item">
            <div class="rl">
                <svg xmlns="http://www.w3.org/2000/svg" width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M5 3h14"/><path d="m18 13-6-6-6 6"/><path d="M12 7v14"/></svg>
                Post Height / Clearance
            </div>
            <div class="rv" style="white-space:pre-line">${postH}</div>
        </div>
        <div class="res-item">
            <div class="rl">
                <svg xmlns="http://www.w3.org/2000/svg" width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><path d="M2 12h20"/><path d="m8 6-6 6 6 6"/></svg>
                Siting &amp; Placement
            </div>
            <div class="rv">${placement}</div>
        </div>
        <div class="res-item span2">
            <div class="rl">
                <svg xmlns="http://www.w3.org/2000/svg" width="12" height="12" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="10"/><path d="M12 16v-4"/><path d="M12 8h.01"/></svg>
                Design &amp; Colour Specifications
            </div>
            <div class="rv" style="white-space:pre-line">${design}</div>
        </div>
    `;

    pop(resDiv);
}

function pop(el){
    el.classList.remove('show');
    void el.offsetWidth;
    el.classList.add('show');
}
</script>
</body>
</html>
