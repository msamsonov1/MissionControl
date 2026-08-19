<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>AERO-OIL | Orbital Intelligence Dashboard</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Three.js CDN -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <!-- OrbitControls -->
    <script src="https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/controls/OrbitControls.js"></script>
    <!-- Chart.js CDN -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <!-- D3.js and TopoJSON CDNs for Detailed Country Contours -->
    <script src="https://cdn.jsdelivr.net/npm/d3@7"></script>
    <script src="https://cdn.jsdelivr.net/npm/topojson-client@3"></script>
    <!-- Lucide Icons CDN -->
    <script src="https://unpkg.com/lucide@latest"></script>
    <style>
        body {
            background-color: #030712;
            color: #f1f5f9;
            font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
            overflow-x: hidden;
            margin: 0;
            padding: 0;
            user-select: none;
        }
        .glass-panel {
            background: rgba(15, 23, 42, 0.88);
            backdrop-filter: blur(14px);
            border: 1px solid rgba(56, 189, 248, 0.22);
            box-shadow: 0 8px 32px 0 rgba(0, 0, 0, 0.55);
        }
        .glass-card {
            background: rgba(30, 41, 59, 0.65);
            backdrop-filter: blur(8px);
            border: 1px solid rgba(255, 255, 255, 0.08);
        }
        .glow-text {
            text-shadow: 0 0 10px rgba(56, 189, 248, 0.6);
        }
        .pulse-dot {
            box-shadow: 0 0 0 0 rgba(16, 185, 129, 0.7);
            animation: pulse 2s infinite;
        }
        @keyframes pulse {
            0% { transform: scale(0.95); box-shadow: 0 0 0 0 rgba(16, 185, 129, 0.7); }
            70% { transform: scale(1.05); box-shadow: 0 0 0 8px rgba(16, 185, 129, 0); }
            100% { transform: scale(0.95); box-shadow: 0 0 0 0 rgba(16, 185, 129, 0); }
        }
        ::-webkit-scrollbar {
            width: 4px;
        }
        ::-webkit-scrollbar-track {
            background: rgba(15, 23, 42, 0.5);
        }
        ::-webkit-scrollbar-thumb {
            background: rgba(56, 189, 248, 0.4);
            border-radius: 4px;
        }
        #globe-canvas-container {
            width: 100%;
            height: 100%;
            min-height: 280px;
            position: relative;
        }
    </style>
</head>
<body class="min-h-screen flex flex-col p-2 md:p-4 gap-3">

    <!-- Top Header Navigation Bar -->
    <header class="glass-panel rounded-xl p-3 flex flex-col md:flex-row items-center justify-between gap-3">
        <!-- Logo & UTC Real-Time Clock -->
        <div class="flex items-center gap-3 w-full md:w-auto justify-between md:justify-start">
            <div class="flex items-center gap-2.5">
                <div class="p-2 bg-cyan-500/10 border border-cyan-500/30 rounded-lg text-cyan-400">
                    <i data-lucide="satellite" class="w-5 h-5 animate-pulse"></i>
                </div>
                <div>
                    <h1 class="font-bold text-base tracking-wider text-white glow-text leading-tight">AERO-OIL <span class="text-[10px] bg-cyan-500/20 text-cyan-300 px-1.5 py-0.5 rounded border border-cyan-500/30">1-2HR SATELLITE</span></h1>
                    <div class="flex items-center gap-1.5 mt-0.5">
                        <span class="w-2 h-2 rounded-full bg-emerald-400 pulse-dot"></span>
                        <span id="utc-clock" class="text-[11px] font-mono text-cyan-300 font-semibold">2026-08-19 00:00:00 UTC</span>
                    </div>
                </div>
            </div>
            <span class="md:hidden text-[10px] text-slate-400 bg-slate-800/80 px-2 py-1 rounded border border-slate-700">LIVE</span>
        </div>

        <!-- Global Oil Financial Valuation Header Bar -->
        <div class="grid grid-cols-3 gap-2 w-full md:w-auto text-xs">
            <div class="glass-card p-2 rounded-lg text-center">
                <div class="text-[9px] uppercase text-slate-400 font-medium">Brent Price</div>
                <div class="text-sm font-bold text-amber-300 font-mono" id="crude-price-display">$78.50 <span class="text-[9px] text-slate-400">/bbl</span></div>
            </div>
            <div class="glass-card p-2 rounded-lg text-center">
                <div class="text-[9px] uppercase text-slate-400 font-medium">Tracked Supply</div>
                <div class="text-sm font-bold text-cyan-300 font-mono" id="total-barrels-display">3,200.48M</div>
            </div>
            <div class="glass-card p-2 rounded-lg text-center border-emerald-500/30">
                <div class="text-[9px] uppercase text-emerald-400 font-medium">Total Value ($)</div>
                <div class="text-sm font-bold text-emerald-400 font-mono glow-text" id="total-valuation-display">$251.24B</div>
            </div>
        </div>
    </header>

    <!-- Main Responsive Dashboard Grid -->
    <main class="grid grid-cols-1 md:grid-cols-2 gap-3 flex-1">

        <!-- TOP-LEFT: 3D Globe with Pink Satellite Orbits & Hover/Tap Popups -->
        <section class="glass-panel rounded-xl p-3 flex flex-col h-[380px] md:h-[420px] relative overflow-hidden">
            <div class="flex items-center justify-between mb-2 z-10 pointer-events-none">
                <div class="flex items-center gap-2">
                    <i data-lucide="globe" class="w-4 h-4 text-cyan-400"></i>
                    <h2 class="font-semibold text-xs text-slate-200">Orbital Map & Satellites</h2>
                </div>
                <div class="flex items-center gap-1 pointer-events-auto">
                    <button id="btn-spin" onclick="toggleSpin()" class="px-2 py-1 bg-cyan-500/20 border border-cyan-500/40 rounded text-[10px] text-cyan-300 flex items-center gap-1 font-medium">
                        <i data-lucide="rotate-cw" class="w-3 h-3"></i> Orbit
                    </button>
                    <button onclick="toggleDayNight()" class="p-1 bg-slate-800 border border-slate-700 rounded text-amber-300">
                        <i data-lucide="sun" class="w-3.5 h-3.5"></i>
                    </button>
                </div>
            </div>

            <!-- 3D WebGL Canvas Container -->
            <div id="globe-canvas-container" class="rounded-lg overflow-hidden flex-1 relative">
                
                <!-- Anchored Floating Tooltip Card (Appears directly over tapped/hovered dot) -->
                <div id="globe-tooltip" class="absolute hidden pointer-events-auto z-30 transform -translate-x-1/2 -translate-y-full mb-3 w-64 glass-panel p-3 rounded-xl border border-cyan-500/50 shadow-2xl transition-opacity duration-150">
                    <div class="flex justify-between items-start mb-1">
                        <span id="tt-badge" class="text-[9px] font-bold uppercase tracking-wider px-1.5 py-0.5 rounded bg-pink-500/20 text-pink-300 border border-pink-500/40">Satellite</span>
                        <button onclick="hideTooltip()" class="text-slate-400 hover:text-white p-0.5">
                            <i data-lucide="x" class="w-3.5 h-3.5"></i>
                        </button>
                    </div>
                    <div id="tt-title" class="font-bold text-xs text-white">Sentinel-1A (SAR Radar)</div>
                    <div id="tt-details" class="text-[10px] text-slate-300 space-y-1 mt-1.5 bg-slate-950/80 p-2 rounded border border-slate-800 font-mono">
                        <!-- Populated via JS -->
                    </div>
                </div>

            </div>

            <!-- Layer Toggles Bar (Including Pink Satellites & Pipelines) -->
            <div class="flex flex-wrap items-center justify-center gap-1.5 mt-2 z-10 text-[10px]">
                <button id="layer-satellites" onclick="toggleLayer('satellites')" class="px-2 py-1 bg-pink-500/20 border border-pink-500/50 rounded text-pink-300 font-medium flex items-center gap-1">
                    <span class="w-2 h-2 rounded-full bg-pink-400"></span> Satellites
                </button>
                <button id="layer-tanks" onclick="toggleLayer('tanks')" class="px-2 py-1 bg-emerald-500/20 border border-emerald-500/50 rounded text-emerald-300 font-medium flex items-center gap-1">
                    <span class="w-2 h-2 rounded-full bg-emerald-400"></span> Tanks
                </button>
                <button id="layer-ships" onclick="toggleLayer('ships')" class="px-2 py-1 bg-amber-500/20 border border-amber-500/50 rounded text-amber-300 font-medium flex items-center gap-1">
                    <span class="w-2 h-2 rounded-full bg-amber-400"></span> Tankers
                </button>
                <button id="layer-pipelines" onclick="toggleLayer('pipelines')" class="px-2 py-1 bg-cyan-500/20 border border-cyan-500/50 rounded text-cyan-300 font-medium flex items-center gap-1">
                    <span class="w-2 h-2 rounded-full bg-cyan-400"></span> Pipelines
                </button>
                <button id="layer-refineries" onclick="toggleLayer('refineries')" class="px-2 py-1 bg-rose-500/20 border border-rose-500/50 rounded text-rose-300 font-medium flex items-center gap-1">
                    <span class="w-2 h-2 rounded-full bg-rose-400"></span> Refineries
                </button>
            </div>
        </section>

        <!-- TOP-RIGHT: Real-Time 24h Graph (Time X-Axis | Barrels Y-Axis) -->
        <section class="glass-panel rounded-xl p-3 flex flex-col h-[380px] md:h-[420px]">
            <div class="flex items-center justify-between mb-2">
                <div class="flex items-center gap-2">
                    <i data-lucide="activity" class="w-4 h-4 text-cyan-400"></i>
                    <h2 class="font-semibold text-xs text-slate-200">Global Supply Trend (24h)</h2>
                </div>
                <span class="text-[9px] font-mono text-cyan-300 bg-cyan-950/80 border border-cyan-800 px-1.5 py-0.5 rounded">X: Time | Y: Barrels (M)</span>
            </div>

            <!-- Chart Canvas Container -->
            <div class="flex-1 w-full relative">
                <canvas id="barrelsChart"></canvas>
            </div>

            <div class="grid grid-cols-2 gap-2 mt-2 text-[10px] text-slate-400 border-t border-slate-800 pt-2">
                <div>Highest Fill Peak: <strong class="text-slate-200 font-mono">3,205.10M bbl</strong></div>
                <div>24h Inventory Change: <strong class="text-emerald-400 font-mono">+18.4M bbl</strong></div>
            </div>
        </section>

        <!-- BOTTOM-LEFT: Exporter Countries & Storage Hubs List -->
        <section class="glass-panel rounded-xl p-3 flex flex-col max-h-[380px]">
            <div class="flex items-center justify-between pb-2 border-b border-slate-800 mb-2">
                <div class="flex items-center gap-2">
                    <i data-lucide="bar-chart-3" class="w-4 h-4 text-cyan-400"></i>
                    <h2 class="font-semibold text-xs text-slate-200">Major Exporters & Storage Hubs</h2>
                </div>
                <span class="text-[9px] text-slate-400 font-mono">Valuation @ $78.50/bbl</span>
            </div>

            <div class="overflow-y-auto space-y-2 pr-1 text-xs">
                <!-- Saudi Arabia -->
                <div class="glass-card p-2 rounded-lg hover:border-cyan-500/40 transition cursor-pointer" onclick="focusLocation(25.0, 45.0, 'Saudi Arabia')">
                    <div class="flex justify-between items-center mb-1">
                        <span class="font-semibold text-slate-100">🇸🇦 Saudi Arabia</span>
                        <span class="font-bold text-cyan-300 font-mono">6.10 M bpd</span>
                    </div>
                    <div class="flex justify-between text-[10px] text-slate-400 mb-1">
                        <span>Ras Tanura Fill: <strong class="text-emerald-400">78.4%</strong></span>
                        <span class="text-emerald-300 font-mono">142M bbl ($11.15B)</span>
                    </div>
                    <div class="w-full bg-slate-800 h-1 rounded-full overflow-hidden">
                        <div class="bg-emerald-500 h-full" style="width: 78.4%"></div>
                    </div>
                </div>

                <!-- Russia -->
                <div class="glass-card p-2 rounded-lg hover:border-cyan-500/40 transition cursor-pointer" onclick="focusLocation(55.0, 37.0, 'Russia')">
                    <div class="flex justify-between items-center mb-1">
                        <span class="font-semibold text-slate-100">🇷🇺 Russia</span>
                        <span class="font-bold text-cyan-300 font-mono">4.65 M bpd</span>
                    </div>
                    <div class="flex justify-between text-[10px] text-slate-400 mb-1">
                        <span>Baltic/CPC Storage: <strong class="text-amber-400">64.2%</strong></span>
                        <span class="text-emerald-300 font-mono">98M bbl ($7.69B)</span>
                    </div>
                    <div class="w-full bg-slate-800 h-1 rounded-full overflow-hidden">
                        <div class="bg-amber-500 h-full" style="width: 64.2%"></div>
                    </div>
                </div>

                <!-- United States -->
                <div class="glass-card p-2 rounded-lg hover:border-cyan-500/40 transition cursor-pointer" onclick="focusLocation(35.9, -96.7, 'Cushing, OK')">
                    <div class="flex justify-between items-center mb-1">
                        <span class="font-semibold text-slate-100">🇺🇸 United States</span>
                        <span class="font-bold text-cyan-300 font-mono">4.12 M bpd</span>
                    </div>
                    <div class="flex justify-between text-[10px] text-slate-400 mb-1">
                        <span>Cushing OK Hub: <strong class="text-cyan-400">52.1%</strong></span>
                        <span class="text-emerald-300 font-mono">32M bbl ($2.51B)</span>
                    </div>
                    <div class="w-full bg-slate-800 h-1 rounded-full overflow-hidden">
                        <div class="bg-cyan-500 h-full" style="width: 52.1%"></div>
                    </div>
                </div>

                <!-- Iraq -->
                <div class="glass-card p-2 rounded-lg hover:border-cyan-500/40 transition cursor-pointer" onclick="focusLocation(30.5, 47.8, 'Basra Terminal')">
                    <div class="flex justify-between items-center mb-1">
                        <span class="font-semibold text-slate-100">🇮🇶 Iraq</span>
                        <span class="font-bold text-cyan-300 font-mono">3.45 M bpd</span>
                    </div>
                    <div class="flex justify-between text-[10px] text-slate-400 mb-1">
                        <span>Basra Terminal: <strong class="text-emerald-400">71.8%</strong></span>
                        <span class="text-emerald-300 font-mono">44M bbl ($3.45B)</span>
                    </div>
                    <div class="w-full bg-slate-800 h-1 rounded-full overflow-hidden">
                        <div class="bg-emerald-500 h-full" style="width: 71.8%"></div>
                    </div>
                </div>

                <!-- Canada -->
                <div class="glass-card p-2 rounded-lg hover:border-cyan-500/40 transition cursor-pointer" onclick="focusLocation(53.5, -113.5, 'Hardisty Hub')">
                    <div class="flex justify-between items-center mb-1">
                        <span class="font-semibold text-slate-100">🇨🇦 Canada</span>
                        <span class="font-bold text-cyan-300 font-mono">3.38 M bpd</span>
                    </div>
                    <div class="flex justify-between text-[10px] text-slate-400 mb-1">
                        <span>Hardisty Hub: <strong class="text-emerald-400">81.0%</strong></span>
                        <span class="text-emerald-300 font-mono">29M bbl ($2.28B)</span>
                    </div>
                    <div class="w-full bg-slate-800 h-1 rounded-full overflow-hidden">
                        <div class="bg-emerald-500 h-full" style="width: 81.0%"></div>
                    </div>
                </div>
            </div>
        </section>

        <!-- BOTTOM-RIGHT: Satellite Target Inspector -->
        <section id="target-inspector" class="glass-panel rounded-xl p-3 flex flex-col max-h-[380px]">
            <div class="flex items-center justify-between pb-2 border-b border-slate-800 mb-2">
                <div class="flex items-center gap-2">
                    <i data-lucide="scan" class="w-4 h-4 text-cyan-400"></i>
                    <h2 class="font-semibold text-xs text-slate-200">Satellite Target Inspector</h2>
                </div>
                <span id="target-type-badge" class="text-[9px] uppercase px-1.5 py-0.5 rounded bg-cyan-500/20 text-cyan-300 border border-cyan-500/30">Storage Tank</span>
            </div>

            <div class="space-y-2.5 text-xs flex-1">
                <div>
                    <h3 id="target-title" class="font-bold text-sm text-white">Cushing, Oklahoma Hub</h3>
                    <p id="target-sub" class="text-[10px] text-slate-400 mt-0.5">Overhead Pass: GOES-16 / VIIRS Thermal | Lat 35.9°, Lon -96.7°</p>
                </div>

                <div class="bg-slate-950/80 p-2.5 rounded-lg border border-slate-800/80 space-y-1.5 text-[11px]">
                    <div class="flex justify-between">
                        <span class="text-slate-400">Metric 1:</span>
                        <span id="target-shadow" class="text-cyan-300 font-mono">1.84m cast (Sun 42.1°)</span>
                    </div>
                    <div class="flex justify-between">
                        <span class="text-slate-400">Calculated Payload:</span>
                        <span id="target-fill" class="text-slate-200 font-mono">52.1% (32M bbl)</span>
                    </div>
                    <div class="flex justify-between">
                        <span class="text-slate-400">Calculated Dollar Value:</span>
                        <span id="target-value" class="text-emerald-400 font-mono font-bold">$2.51 Billion</span>
                    </div>
                </div>

                <p id="target-desc" class="text-[11px] text-slate-300 leading-relaxed">
                    Primary WTI pipeline hub. 140+ floating roof tanks monitored via computer vision optical shadow rim measurements and SAR radar phase shifts.
                </p>

                <div class="flex justify-between items-center pt-2 border-t border-slate-800 text-[10px]">
                    <span class="text-slate-400">AI Confidence Score:</span>
                    <span class="text-emerald-400 font-semibold flex items-center gap-1">
                        <i data-lucide="check-circle" class="w-3 h-3"></i> 98.4% Match
                    </span>
                </div>
            </div>
        </section>

    </main>

    <!-- JavaScript Application Engine -->
    <script>
        // Financial & Target Constants
        const CRUDE_PRICE = 78.50; // $ USD
        const TOTAL_BARRELS_M = 3200.48; // Million barrels

        // Three.js Globe Variables
        let scene, camera, renderer, controls, earthMesh, earthTexture, sunLight, ambientLight;
        let isSpinning = true;
        let dayNightMode = 0;
        let activeSelectedObject = null;
        let tooltipVisible = false;

        const pinsGroup = new THREE.Group();
        const raysGroup = new THREE.Group();
        const raycaster = new THREE.Raycaster();
        const mouse = new THREE.Vector2();

        const layers = { satellites: true, tanks: true, ships: true, pipelines: true, refineries: true };

        // Infrastructure Nodes & Orbital Satellites
        const oilNodes = [
            // Pink Satellites in Orbit
            { id: 101, name: "Sentinel-1A (ESA)", type: "satellites", lat: 45.0, lon: 10.0, altMiles: 430, sensor: "C-Band SAR Radar", cadence: "90 Mins", desc: "Cloud-penetrating Synthetic Aperture Radar measuring floating tank roof heights and ship displacement." },
            { id: 102, name: "NOAA-20 / VIIRS", type: "satellites", lat: 20.0, lon: -100.0, altMiles: 512, sensor: "SWIR Thermal & Day-Night Band", cadence: "100 Mins", desc: "Measures flaring heat intensity at refineries and oil fields in 375m resolution." },
            { id: 103, name: "GOES-16 East (NOAA)", type: "satellites", lat: 0.0, lon: -75.2, altMiles: 22236, sensor: "Advanced Baseline Imager (ABI)", cadence: "5-15 Mins", desc: "Geostationary weather satellite monitoring cloud vectors and solar irradiance." },
            { id: 104, name: "Landsat 9 (NASA/USGS)", type: "satellites", lat: -30.0, lon: 130.0, altMiles: 438, sensor: "OLI-2 & TIRS-2 Thermal", cadence: "16 Days", desc: "Provides high-resolution multi-spectral surface reflection for offshore oil slicks." },
            { id: 105, name: "Capella-5 SAR", type: "satellites", lat: 60.0, lon: -120.0, altMiles: 310, sensor: "Sub-meter X-Band SAR", cadence: "On-Demand Tasking", desc: "Commercial high-resolution microwave radar array targeting oil storage terminals." },

            // Storage Tanks (Green)
            { id: 1, name: "Cushing, Oklahoma Hub (USA)", lat: 35.9, lon: -96.7, altMiles: 0, type: "tanks", fill: "52.1%", volume: "32M bbl", shadow: "1.84m (Sun 42°)", desc: "Primary WTI crude storage hub with 140+ floating roof tanks scanned via optical shadow trigonometry." },
            { id: 2, name: "Ras Tanura Terminal (Saudi Arabia)", lat: 26.6, lon: 50.1, altMiles: 0, type: "tanks", fill: "78.4%", volume: "142M bbl", shadow: "0.92m (Sun 61°)", desc: "Saudi Aramco main export terminal with massive floating roof storage capacity." },
            { id: 3, name: "Port of Rotterdam (Netherlands)", lat: 51.9, lon: 4.1, altMiles: 0, type: "tanks", fill: "69.1%", volume: "48M bbl", shadow: "2.10m (Sun 31°)", desc: "European refining and import gateway monitored by SAR radar altimetry." },
            { id: 6, name: "Hardisty Storage Hub (Canada)", lat: 52.6, lon: -111.3, altMiles: 0, type: "tanks", fill: "81.0%", volume: "29M bbl", shadow: "2.40m (Sun 28°)", desc: "Western Canadian heavy crude gathering terminal." },
            { id: 7, name: "Fujairah Oil Terminal (UAE)", lat: 25.1, lon: 56.3, altMiles: 0, type: "tanks", fill: "68.5%", volume: "22M bbl", shadow: "1.05m (Sun 58°)", desc: "Major bunkering & storage terminal outside Persian Gulf." },

            // Tankers (Amber)
            { id: 5, name: "Strait of Hormuz Supertankers", lat: 26.5, lon: 56.2, altMiles: 0, type: "ships", fill: "N/A", volume: "20.5M bpd in transit", shadow: "SAR Freeboard Height", desc: "Monitors supertanker hull submersion depths to calculate crude weight in transit." },
            { id: 8, name: "Malacca Strait Tanker Queue", lat: 1.4, lon: 103.2, altMiles: 0, type: "ships", fill: "N/A", volume: "15.2M bpd in transit", shadow: "SAR Hull Draft", desc: "Primary maritime bottleneck connecting Middle East crude to Asian importers." },

            // Refineries (Rose)
            { id: 4, name: "Jurong Island Petrochemical (Singapore)", lat: 1.27, lon: 103.7, altMiles: 0, type: "refineries", fill: "82.5%", volume: "38M bbl", shadow: "0.45m (Sun 78°)", desc: "Southeast Asian refining bottleneck monitored via thermal infrared flaring intensity." },
            { id: 9, name: "Jamnagar Refinery Complex (India)", lat: 22.3, lon: 69.8, altMiles: 0, type: "refineries", fill: "75.0%", volume: "1.24M bpd capacity", shadow: "SWIR Thermal Flare", desc: "World's largest single-location petroleum refinery." },

            // Pipelines (Cyan)
            { id: 201, name: "Colonial Pipeline System (USA)", lat: 33.5, lon: -84.4, altMiles: 0, type: "pipelines", fill: "95.0%", volume: "2.5M bpd capacity", shadow: "Thermal Flow Radar", desc: "2,700-mile pipeline transporting refined crude from Texas to New York." },
            { id: 202, name: "Trans-Alaska Pipeline / TAPS (USA)", lat: 64.8, lon: -147.7, altMiles: 0, type: "pipelines", fill: "88.0%", volume: "500k bpd capacity", shadow: "IR Thermal Pipeline", desc: "800-mile pipeline spanning Prudhoe Bay to Valdez Alaska." },
            { id: 203, name: "Druzhba Crude Pipeline (Europe)", lat: 52.5, lon: 30.0, altMiles: 0, type: "pipelines", fill: "70.0%", volume: "1.4M bpd capacity", shadow: "Satellite Synthetic Aperture", desc: "World's longest crude oil pipeline network stretching 2,500 miles." }
        ];

        // Real-Time Clock
        function initClock() {
            const clockEl = document.getElementById('utc-clock');
            function update() {
                const now = new Date();
                const y = now.getUTCFullYear();
                const m = String(now.getUTCMonth() + 1).padStart(2, '0');
                const d = String(now.getUTCDate()).padStart(2, '0');
                const hh = String(now.getUTCHours()).padStart(2, '0');
                const mm = String(now.getUTCMinutes()).padStart(2, '0');
                const ss = String(now.getUTCSeconds()).padStart(2, '0');
                clockEl.innerText = `${y}-${m}-${d} ${hh}:${mm}:${ss} UTC`;
            }
            update();
            setInterval(update, 1000);
        }

        // Compute Financial Values
        function initValuation() {
            const totalValB = (TOTAL_BARRELS_M * CRUDE_PRICE) / 1000.0;
            document.getElementById('crude-price-display').innerHTML = `$${CRUDE_PRICE.toFixed(2)} <span class="text-[9px] text-slate-400">/bbl</span>`;
            document.getElementById('total-barrels-display').innerText = `${TOTAL_BARRELS_M.toLocaleString('en-US', {minimumFractionDigits: 2})}M`;
            document.getElementById('total-valuation-display').innerText = `$${totalValB.toFixed(2)}B`;
        }

        // Initialize Chart.js Real-Time Graph
        function initChart() {
            const ctx = document.getElementById('barrelsChart').getContext('2d');
            const labels = [];
            const data = [];
            const nowH = new Date().getUTCHours();

            let val = 3180.0;
            for (let i = 24; i >= 0; i--) {
                const hr = (nowH - i + 24) % 24;
                labels.push(`${String(hr).padStart(2, '0')}:00`);
                val += (Math.random() - 0.48) * 3.2;
                data.push(parseFloat(val.toFixed(2)));
            }
            data[data.length - 1] = TOTAL_BARRELS_M;

            new Chart(ctx, {
                type: 'line',
                data: {
                    labels: labels,
                    datasets: [{
                        label: 'Supply (M bbl)',
                        data: data,
                        borderColor: '#38bdf8',
                        borderWidth: 2,
                        backgroundColor: 'rgba(56, 189, 248, 0.15)',
                        fill: true,
                        tension: 0.35,
                        pointRadius: 2,
                        pointBackgroundColor: '#38bdf8'
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: { legend: { display: false } },
                    scales: {
                        x: { grid: { color: 'rgba(255, 255, 255, 0.05)' }, ticks: { color: '#94a3b8', font: { size: 9 }, maxTicksLimit: 6 } },
                        y: { grid: { color: 'rgba(255, 255, 255, 0.05)' }, ticks: { color: '#94a3b8', font: { size: 9 } } }
                    }
                }
            });
        }

        // Draw High-Resolution World Map Texture
        function drawWorldTextureCanvas(canvas, geoJsonData) {
            const ctx = canvas.getContext('2d');
            const width = canvas.width;
            const height = canvas.height;

            const oceanGrad = ctx.createLinearGradient(0, 0, 0, height);
            oceanGrad.addColorStop(0, '#040914');
            oceanGrad.addColorStop(0.5, '#0a1628');
            oceanGrad.addColorStop(1, '#040914');
            ctx.fillStyle = oceanGrad;
            ctx.fillRect(0, 0, width, height);

            ctx.strokeStyle = 'rgba(56, 189, 248, 0.08)';
            ctx.lineWidth = 1;
            for (let x = 0; x <= width; x += width / 24) {
                ctx.beginPath(); ctx.moveTo(x, 0); ctx.lineTo(x, height); ctx.stroke();
            }
            for (let y = 0; y <= height; y += height / 12) {
                ctx.beginPath(); ctx.moveTo(0, y); ctx.lineTo(width, y); ctx.stroke();
            }

            const projection = d3.geoEquirectangular()
                .translate([width / 2, height / 2])
                .scale(width / (2 * Math.PI));

            const path = d3.geoPath().projection(projection).context(ctx);

            if (geoJsonData) {
                const countries = topojson.feature(geoJsonData, geoJsonData.objects.countries);

                ctx.fillStyle = '#0f274a';
                ctx.beginPath(); path(countries); ctx.fill();

                ctx.strokeStyle = '#38bdf8';
                ctx.lineWidth = 1.2;
                ctx.beginPath(); path(countries); ctx.stroke();

                ctx.strokeStyle = '#00f2fe';
                ctx.lineWidth = 0.8;
                ctx.beginPath(); path(topojson.mesh(geoJsonData, geoJsonData.objects.countries, (a, b) => a === b)); ctx.stroke();
            }

            if (earthTexture) earthTexture.needsUpdate = true;
        }

        // Initialize 3D Globe with Satellites & Hover/Tap Popups
        function initGlobe() {
            const container = document.getElementById('globe-canvas-container');
            const width = container.clientWidth;
            const height = container.clientHeight;

            scene = new THREE.Scene();
            camera = new THREE.PerspectiveCamera(45, width / height, 0.1, 1000);
            camera.position.set(0, 6, 17);

            renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
            renderer.setSize(width, height);
            renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
            container.appendChild(renderer.domElement);

            controls = new THREE.OrbitControls(camera, renderer.domElement);
            controls.enableDamping = true;
            controls.dampingFactor = 0.05;

            ambientLight = new THREE.AmbientLight(0x1e293b, 1.4);
            scene.add(ambientLight);

            sunLight = new THREE.DirectionalLight(0xffffff, 2.2);
            sunLight.position.set(20, 10, 15);
            scene.add(sunLight);

            const canvas = document.createElement('canvas');
            canvas.width = 2048; canvas.height = 1024;
            drawWorldTextureCanvas(canvas, null);

            earthTexture = new THREE.CanvasTexture(canvas);
            const earthGeo = new THREE.SphereGeometry(5, 64, 64);
            const earthMat = new THREE.MeshStandardMaterial({
                map: earthTexture,
                roughness: 0.5,
                metalness: 0.1
            });

            earthMesh = new THREE.Mesh(earthGeo, earthMat);
            scene.add(earthMesh);

            earthMesh.add(pinsGroup);
            earthMesh.add(raysGroup);

            // Fetch Country Contours
            d3.json('https://cdn.jsdelivr.net/npm/world-atlas@2/countries-110m.json').then(worldData => {
                drawWorldTextureCanvas(canvas, worldData);
            }).catch(err => console.warn("Fallback contours ready", err));

            // Create Node Markers & Pink Satellites
            oilNodes.forEach(node => {
                const phi = (90 - node.lat) * (Math.PI / 180);
                const theta = (node.lon + 180) * (Math.PI / 180);

                // Satellites orbit higher above sea level (radius 5.8 to 6.8)
                let radius = 5.05;
                if (node.type === 'satellites') {
                    radius = node.altMiles > 1000 ? 6.8 : 5.8;
                }

                const pos = new THREE.Vector3(
                    -(radius * Math.sin(phi) * Math.cos(theta)),
                    (radius * Math.cos(phi)),
                    (radius * Math.sin(phi) * Math.sin(theta))
                );

                let color = 0x10b981; // Green Tanks
                if (node.type === 'satellites') color = 0xec4899; // Pink Satellites
                if (node.type === 'ships') color = 0xf59e0b; // Amber Tankers
                if (node.type === 'pipelines') color = 0x06b6d4; // Cyan Pipelines
                if (node.type === 'refineries') color = 0xf43f5e; // Rose Refineries

                // Node Geometry
                const pinGeo = new THREE.SphereGeometry(node.type === 'satellites' ? 0.16 : 0.12, 16, 16);
                const pinMat = new THREE.MeshBasicMaterial({ color: color });
                const pinMesh = new THREE.Mesh(pinGeo, pinMat);
                pinMesh.position.copy(pos);
                pinMesh.userData = node;

                // Pulsing Ring
                const ringGeo = new THREE.RingGeometry(0.18, 0.26, 32);
                const ringMat = new THREE.MeshBasicMaterial({ color: color, side: THREE.DoubleSide, transparent: true, opacity: 0.8 });
                const ringMesh = new THREE.Mesh(ringGeo, ringMat);
                ringMesh.position.copy(pos.clone().multiplyScalar(1.01));
                ringMesh.lookAt(new THREE.Vector3(0, 0, 0));

                pinsGroup.add(pinMesh);
                pinsGroup.add(ringMesh);

                // Add laser beam down to Earth for satellites
                if (node.type === 'satellites') {
                    const groundPos = pos.clone().multiplyScalar(5.05 / radius);
                    const rayGeo = new THREE.BufferGeometry().setFromPoints([pos, groundPos]);
                    const rayMat = new THREE.LineDashedMaterial({ color: 0xec4899, dashSize: 0.1, gapSize: 0.1, opacity: 0.5, transparent: true });
                    const rayLine = new THREE.Line(rayGeo, rayMat);
                    rayLine.computeLineDistances();
                    raysGroup.add(rayLine);
                }
            });

            window.addEventListener('resize', onWindowResize);
            
            // Interaction Listeners for Tap & Hover Popups
            const canvasEl = renderer.domElement;
            canvasEl.addEventListener('click', handlePointerEvent);
            canvasEl.addEventListener('mousemove', handlePointerEvent);
            canvasEl.addEventListener('touchstart', handlePointerEvent, { passive: true });

            animate();
        }

        // Pointer Hover / Tap Interaction Handler
        function handlePointerEvent(e) {
            const rect = renderer.domElement.getBoundingClientRect();
            let clientX = e.clientX;
            let clientY = e.clientY;

            if (e.touches && e.touches.length > 0) {
                clientX = e.touches[0].clientX;
                clientY = e.touches[0].clientY;
            }

            mouse.x = ((clientX - rect.left) / rect.width) * 2 - 1;
            mouse.y = -((clientY - rect.top) / rect.height) * 2 + 1;

            raycaster.setFromCamera(mouse, camera);
            const intersects = raycaster.intersectObjects(pinsGroup.children);

            if (intersects.length > 0) {
                const node = intersects[0].object.userData;
                if (node && node.name) {
                    activeSelectedObject = intersects[0].object;
                    showGlobeTooltip(node);
                    updateTargetInspector(node);
                    if (e.type === 'click' || e.type === 'touchstart') {
                        isSpinning = false;
                    }
                }
            }
        }

        // Show Anchored Floating Popup Tooltip directly on top of 3D Point
        function showGlobeTooltip(node) {
            const tt = document.getElementById('globe-tooltip');
            const badge = document.getElementById('tt-badge');
            const title = document.getElementById('tt-title');
            const details = document.getElementById('tt-details');

            badge.innerText = node.type;
            
            // Style badge color based on category
            if (node.type === 'satellites') {
                badge.className = "text-[9px] font-bold uppercase tracking-wider px-1.5 py-0.5 rounded bg-pink-500/20 text-pink-300 border border-pink-500/40";
            } else if (node.type === 'pipelines') {
                badge.className = "text-[9px] font-bold uppercase tracking-wider px-1.5 py-0.5 rounded bg-cyan-500/20 text-cyan-300 border border-cyan-500/40";
            } else if (node.type === 'ships') {
                badge.className = "text-[9px] font-bold uppercase tracking-wider px-1.5 py-0.5 rounded bg-amber-500/20 text-amber-300 border border-amber-500/40";
            } else {
                badge.className = "text-[9px] font-bold uppercase tracking-wider px-1.5 py-0.5 rounded bg-emerald-500/20 text-emerald-300 border border-emerald-500/40";
            }

            title.innerText = node.name;

            if (node.type === 'satellites') {
                details.innerHTML = `
                    <div class="flex justify-between text-pink-300"><span>Altitude:</span><strong>${node.altMiles.toLocaleString()} Miles</strong></div>
                    <div class="flex justify-between"><span>Sensor:</span><span>${node.sensor}</span></div>
                    <div class="flex justify-between"><span>Pass Cadence:</span><span>${node.cadence}</span></div>
                `;
            } else if (node.type === 'pipelines') {
                details.innerHTML = `
                    <div class="flex justify-between text-cyan-300"><span>Capacity:</span><strong>${node.volume}</strong></div>
                    <div class="flex justify-between"><span>Fill Rate:</span><span>${node.fill}</span></div>
                    <div class="flex justify-between"><span>Sensor:</span><span>${node.shadow}</span></div>
                `;
            } else {
                details.innerHTML = `
                    <div class="flex justify-between text-emerald-300"><span>Volume/Payload:</span><strong>${node.volume}</strong></div>
                    <div class="flex justify-between"><span>Calculated Fill:</span><span>${node.fill || 'In Transit'}</span></div>
                    <div class="flex justify-between"><span>Measurement:</span><span>${node.shadow}</span></div>
                `;
            }

            tt.classList.remove('hidden');
            tooltipVisible = true;
            updateTooltipPosition();
        }

        // Dynamically track Tooltip position on 3D canvas
        function updateTooltipPosition() {
            if (!activeSelectedObject || !tooltipVisible) return;

            const worldPos = new THREE.Vector3();
            activeSelectedObject.getWorldPosition(worldPos);

            const clone = worldPos.clone();
            clone.project(camera);

            const container = document.getElementById('globe-canvas-container');
            const widthHalf = container.clientWidth / 2;
            const heightHalf = container.clientHeight / 2;

            const x = (clone.x * widthHalf) + widthHalf;
            const y = -(clone.y * heightHalf) + heightHalf;

            const tt = document.getElementById('globe-tooltip');
            tt.style.left = `${Math.max(80, Math.min(container.clientWidth - 80, x))}px`;
            tt.style.top = `${Math.max(60, Math.min(container.clientHeight - 40, y))}px`;
        }

        function hideTooltip() {
            document.getElementById('globe-tooltip').classList.add('hidden');
            tooltipVisible = false;
            activeSelectedObject = null;
        }

        // Update Bottom-Right Inspector
        function updateTargetInspector(data) {
            document.getElementById('target-title').innerText = data.name;
            document.getElementById('target-type-badge').innerText = data.type;
            document.getElementById('target-desc').innerText = data.desc;

            if (data.type === 'satellites') {
                document.getElementById('target-sub').innerText = `Orbital Altitude: ${data.altMiles.toLocaleString()} Miles | Lat ${data.lat}°, Lon ${data.lon}°`;
                document.getElementById('target-shadow').innerText = `${data.sensor}`;
                document.getElementById('target-fill').innerText = `Pass Cadence: ${data.cadence}`;
                document.getElementById('target-value').innerText = `Active Telemetry Stream`;
            } else {
                document.getElementById('target-sub').innerText = `Pass ID: GOES-16 / VIIRS Thermal | Lat ${data.lat}°, Lon ${data.lon}°`;
                document.getElementById('target-shadow').innerText = data.shadow;
                document.getElementById('target-fill').innerText = `${data.fill || 'N/A'} (${data.volume})`;

                const match = data.volume.match(/(\d+)M/);
                if (match) {
                    const val = (parseFloat(match[1]) * CRUDE_PRICE) / 1000.0;
                    document.getElementById('target-value').innerText = `$${val.toFixed(2)} Billion`;
                } else {
                    document.getElementById('target-value').innerText = "In Transit";
                }
            }
        }

        function focusLocation(lat, lon, name) {
            isSpinning = false;
            const target = oilNodes.find(n => n.name.includes(name));
            if (target) {
                updateTargetInspector(target);
                showGlobeTooltip(target);
            }
        }

        function toggleSpin() {
            isSpinning = !isSpinning;
        }

        function toggleDayNight() {
            dayNightMode = (dayNightMode + 1) % 2;
            sunLight.position.set(dayNightMode === 0 ? 20 : -20, 10, dayNightMode === 0 ? 15 : -15);
        }

        function toggleLayer(type) {
            layers[type] = !layers[type];
            pinsGroup.children.forEach(c => {
                if (c.userData && c.userData.type === type) c.visible = layers[type];
            });
        }

        function onWindowResize() {
            const container = document.getElementById('globe-canvas-container');
            if (!container) return;
            camera.aspect = container.clientWidth / container.clientHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(container.clientWidth, container.clientHeight);
        }

        function animate() {
            requestAnimationFrame(animate);
            if (isSpinning && earthMesh) earthMesh.rotation.y += 0.0018;

            const time = Date.now() * 0.003;
            pinsGroup.children.forEach(child => {
                if (child.geometry instanceof THREE.RingGeometry) {
                    const scale = 1 + Math.sin(time * 2) * 0.15;
                    child.scale.set(scale, scale, scale);
                }
            });

            if (tooltipVisible) {
                updateTooltipPosition();
            }

            controls.update();
            renderer.render(scene, camera);
        }

        window.onload = function() {
            initClock();
            initValuation();
            initChart();
            initGlobe();
            lucide.createIcons();
        };
    </script>
</body>
</html>

