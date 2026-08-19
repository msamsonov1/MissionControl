<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>3D Orbital Oil Intelligence & Exporter Radar</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Three.js CDN -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js"></script>
    <!-- OrbitControls -->
    <script src="https://cdn.jsdelivr.net/npm/three@0.128.0/examples/js/controls/OrbitControls.js"></script>
    <!-- Chart.js CDN for Real-time Time Series Graph -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <!-- Lucide Icons CDN -->
    <script src="https://unpkg.com/lucide@latest"></script>
    <style>
        body {
            margin: 0;
            overflow: hidden;
            background-color: #030712;
            font-family: system-ui, -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
            user-select: none;
        }
        #canvas-container {
            width: 100vw;
            height: 100vh;
            position: absolute;
            top: 0;
            left: 0;
            z-index: 1;
        }
        .glass-panel {
            background: rgba(15, 23, 42, 0.82);
            backdrop-filter: blur(14px);
            border: 1px solid rgba(56, 189, 248, 0.25);
            box-shadow: 0 8px 32px 0 rgba(0, 0, 0, 0.6);
        }
        .glass-card {
            background: rgba(30, 41, 59, 0.65);
            backdrop-filter: blur(8px);
            border: 1px solid rgba(255, 255, 255, 0.1);
        }
        /* Custom Scrollbar for HUD Table */
        ::-webkit-scrollbar {
            width: 5px;
        }
        ::-webkit-scrollbar-track {
            background: rgba(15, 23, 42, 0.5);
        }
        ::-webkit-scrollbar-thumb {
            background: rgba(56, 189, 248, 0.4);
            border-radius: 4px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: rgba(56, 189, 248, 0.8);
        }
        .glow-text {
            text-shadow: 0 0 12px rgba(56, 189, 248, 0.7);
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
    </style>
</head>
<body class="text-slate-100">

    <!-- 3D WebGL Canvas -->
    <div id="canvas-container"></div>

    <!-- UI Overlay Layer -->
    <div class="relative z-10 pointer-events-none min-h-screen flex flex-col justify-between p-3 md:p-5">

        <!-- Top Header Navigation -->
        <div class="flex flex-col lg:flex-row justify-between items-start lg:items-center gap-3">
            
            <!-- Logo & Satellite Real-Time Timestamp Status -->
            <div class="glass-panel p-3.5 rounded-xl pointer-events-auto flex items-center gap-4">
                <div class="p-2.5 bg-cyan-500/10 border border-cyan-500/30 rounded-lg text-cyan-400">
                    <i data-lucide="satellite" class="w-6 h-6 animate-pulse"></i>
                </div>
                <div>
                    <div class="flex items-center gap-2">
                        <h1 class="font-bold text-lg tracking-wider text-white glow-text">AERO-OIL <span class="text-xs bg-cyan-500/20 text-cyan-300 px-2 py-0.5 rounded border border-cyan-500/30">CADENCE 1-2HR</span></h1>
                    </div>
                    <div class="flex items-center gap-2 mt-1">
                        <span class="w-2 h-2 rounded-full bg-emerald-400 pulse-dot"></span>
                        <span id="utc-clock" class="text-xs font-mono text-cyan-300 font-semibold">2026-08-18 23:31:00 UTC</span>
                        <span class="text-[10px] text-slate-400 bg-slate-800/80 px-1.5 py-0.5 rounded border border-slate-700">LIVE SATELLITE PASS</span>
                    </div>
                </div>
            </div>

            <!-- Global Financial Valuation HUD Header Bar -->
            <div class="glass-panel px-4 py-2.5 rounded-xl pointer-events-auto flex flex-wrap items-center gap-4 text-xs">
                
                <!-- Live Crude Benchmark Price -->
                <div class="flex items-center gap-2 border-r border-slate-700/80 pr-4">
                    <div class="p-1.5 bg-amber-500/10 text-amber-400 rounded">
                        <i data-lucide="dollar-sign" class="w-4 h-4"></i>
                    </div>
                    <div>
                        <div class="text-[10px] uppercase text-slate-400 font-medium">Brent Crude Benchmark</div>
                        <div class="flex items-center gap-1.5">
                            <span id="crude-price-display" class="text-base font-bold text-amber-300 font-mono">$78.50</span>
                            <span class="text-[10px] text-emerald-400 font-semibold">/ bbl</span>
                        </div>
                    </div>
                </div>

                <!-- Total Tracked Barrels -->
                <div class="flex items-center gap-2 border-r border-slate-700/80 pr-4">
                    <div class="p-1.5 bg-cyan-500/10 text-cyan-400 rounded">
                        <i data-lucide="database" class="w-4 h-4"></i>
                    </div>
                    <div>
                        <div class="text-[10px] uppercase text-slate-400 font-medium">Total Tracked Supply</div>
                        <div id="total-barrels-display" class="text-base font-bold text-cyan-300 font-mono">3,200.48 M bbl</div>
                    </div>
                </div>

                <!-- Total Dollar Valuation (Supply x Price) -->
                <div class="flex items-center gap-2">
                    <div class="p-1.5 bg-emerald-500/10 text-emerald-400 rounded">
                        <i data-lucide="coins" class="w-4 h-4"></i>
                    </div>
                    <div>
                        <div class="text-[10px] uppercase text-slate-400 font-medium">Total Supply Value ($USD)</div>
                        <div id="total-valuation-display" class="text-base font-bold text-emerald-400 font-mono glow-text">$251.24 Billion</div>
                    </div>
                </div>

            </div>
        </div>

        <!-- Right Side Panel: Exporter Table & Real-Time Graph -->
        <div class="absolute top-24 right-3 md:right-5 w-80 md:w-96 pointer-events-auto flex flex-col gap-3">
            
            <!-- REAL-TIME GRAPH: Barrels Over Time (24h) -->
            <div class="glass-panel rounded-xl p-3.5">
                <div class="flex justify-between items-center mb-2">
                    <div class="flex items-center gap-1.5">
                        <i data-lucide="activity" class="w-4 h-4 text-cyan-400"></i>
                        <h2 class="font-semibold text-xs text-slate-200">Global Storage Trend (Last 24 Hours)</h2>
                    </div>
                    <span class="text-[10px] text-cyan-300 font-mono bg-cyan-950/60 border border-cyan-800/60 px-1.5 py-0.5 rounded">X: Time | Y: M bbl</span>
                </div>
                <!-- Chart Canvas Container -->
                <div class="h-32 w-full">
                    <canvas id="barrelsChart"></canvas>
                </div>
            </div>

            <!-- Crude Oil Exporter HUD Table -->
            <div class="glass-panel rounded-xl p-3.5 flex flex-col max-h-[calc(100vh-340px)]">
                
                <!-- Table Header -->
                <div class="flex items-center justify-between pb-2.5 border-b border-slate-700/60 mb-2">
                    <div>
                        <h2 class="font-semibold text-xs text-slate-200 flex items-center gap-1.5">
                            <i data-lucide="bar-chart-3" class="w-4 h-4 text-cyan-400"></i>
                            Major Exporters & Storage Hubs
                        </h2>
                        <p class="text-[10px] text-slate-400">Inventory Value @ $78.50/bbl</p>
                    </div>
                    <span class="text-[10px] bg-emerald-950/80 text-emerald-300 px-2 py-0.5 rounded border border-emerald-800/60 font-mono">LIVE FEED</span>
                </div>

                <!-- Scrollable Exporters Table -->
                <div class="overflow-y-auto pr-1 space-y-2 text-xs">
                    
                    <!-- Saudi Arabia -->
                    <div class="glass-card p-2 rounded-lg hover:border-cyan-500/50 transition cursor-pointer country-row" onclick="focusLocation(25.0, 45.0, 'Saudi Arabia Hubs')">
                        <div class="flex justify-between items-center mb-1">
                            <span class="font-medium text-slate-100">🇸🇦 Saudi Arabia</span>
                            <span class="font-bold text-cyan-300 font-mono">6.10 M bpd</span>
                        </div>
                        <div class="space-y-1">
                            <div class="flex justify-between text-[10px] text-slate-400">
                                <span>Ras Tanura Fill: <strong class="text-emerald-400">78.4%</strong></span>
                                <span class="text-emerald-300 font-mono">142M bbl ($11.15B)</span>
                            </div>
                            <div class="w-full bg-slate-800 h-1.5 rounded-full overflow-hidden">
                                <div class="bg-emerald-500 h-full rounded-full" style="width: 78.4%"></div>
                            </div>
                        </div>
                    </div>

                    <!-- Russia -->
                    <div class="glass-card p-2 rounded-lg hover:border-cyan-500/50 transition cursor-pointer country-row" onclick="focusLocation(55.0, 37.0, 'Novorossiysk / Primorsk')">
                        <div class="flex justify-between items-center mb-1">
                            <span class="font-medium text-slate-100">🇷🇺 Russia</span>
                            <span class="font-bold text-cyan-300 font-mono">4.65 M bpd</span>
                        </div>
                        <div class="space-y-1">
                            <div class="flex justify-between text-[10px] text-slate-400">
                                <span>Baltic/CPC Storage: <strong class="text-amber-400">64.2%</strong></span>
                                <span class="text-emerald-300 font-mono">98M bbl ($7.69B)</span>
                            </div>
                            <div class="w-full bg-slate-800 h-1.5 rounded-full overflow-hidden">
                                <div class="bg-amber-500 h-full rounded-full" style="width: 64.2%"></div>
                            </div>
                        </div>
                    </div>

                    <!-- United States -->
                    <div class="glass-card p-2 rounded-lg hover:border-cyan-500/50 transition cursor-pointer country-row" onclick="focusLocation(35.9, -96.7, 'Cushing, OK Hub')">
                        <div class="flex justify-between items-center mb-1">
                            <span class="font-medium text-slate-100">🇺🇸 United States</span>
                            <span class="font-bold text-cyan-300 font-mono">4.12 M bpd</span>
                        </div>
                        <div class="space-y-1">
                            <div class="flex justify-between text-[10px] text-slate-400">
                                <span>Cushing OK Storage: <strong class="text-cyan-400">52.1%</strong></span>
                                <span class="text-emerald-300 font-mono">32M bbl ($2.51B)</span>
                            </div>
                            <div class="w-full bg-slate-800 h-1.5 rounded-full overflow-hidden">
                                <div class="bg-cyan-500 h-full rounded-full" style="width: 52.1%"></div>
                            </div>
                        </div>
                    </div>

                    <!-- Iraq -->
                    <div class="glass-card p-2 rounded-lg hover:border-cyan-500/50 transition cursor-pointer country-row" onclick="focusLocation(30.5, 47.8, 'Basra Oil Terminal')">
                        <div class="flex justify-between items-center mb-1">
                            <span class="font-medium text-slate-100">🇮🇶 Iraq</span>
                            <span class="font-bold text-cyan-300 font-mono">3.45 M bpd</span>
                        </div>
                        <div class="space-y-1">
                            <div class="flex justify-between text-[10px] text-slate-400">
                                <span>Basra Terminal: <strong class="text-emerald-400">71.8%</strong></span>
                                <span class="text-emerald-300 font-mono">44M bbl ($3.45B)</span>
                            </div>
                            <div class="w-full bg-slate-800 h-1.5 rounded-full overflow-hidden">
                                <div class="bg-emerald-500 h-full rounded-full" style="width: 71.8%"></div>
                            </div>
                        </div>
                    </div>

                    <!-- Canada -->
                    <div class="glass-card p-2 rounded-lg hover:border-cyan-500/50 transition cursor-pointer country-row" onclick="focusLocation(53.5, -113.5, 'Hardisty Alberta Storage')">
                        <div class="flex justify-between items-center mb-1">
                            <span class="font-medium text-slate-100">🇨🇦 Canada</span>
                            <span class="font-bold text-cyan-300 font-mono">3.38 M bpd</span>
                        </div>
                        <div class="space-y-1">
                            <div class="flex justify-between text-[10px] text-slate-400">
                                <span>Hardisty Terminal: <strong class="text-emerald-400">81.0%</strong></span>
                                <span class="text-emerald-300 font-mono">29M bbl ($2.28B)</span>
                            </div>
                            <div class="w-full bg-slate-800 h-1.5 rounded-full overflow-hidden">
                                <div class="bg-emerald-500 h-full rounded-full" style="width: 81.0%"></div>
                            </div>
                        </div>
                    </div>

                    <!-- United Arab Emirates -->
                    <div class="glass-card p-2 rounded-lg hover:border-cyan-500/50 transition cursor-pointer country-row" onclick="focusLocation(25.1, 56.3, 'Fujairah Oil Hub')">
                        <div class="flex justify-between items-center mb-1">
                            <span class="font-medium text-slate-100">🇦🇪 UAE (Fujairah)</span>
                            <span class="font-bold text-cyan-300 font-mono">2.75 M bpd</span>
                        </div>
                        <div class="space-y-1">
                            <div class="flex justify-between text-[10px] text-slate-400">
                                <span>Fujairah Bunkers: <strong class="text-amber-400">68.5%</strong></span>
                                <span class="text-emerald-300 font-mono">22M bbl ($1.73B)</span>
                            </div>
                            <div class="w-full bg-slate-800 h-1.5 rounded-full overflow-hidden">
                                <div class="bg-amber-500 h-full rounded-full" style="width: 68.5%"></div>
                            </div>
                        </div>
                    </div>

                    <!-- Kuwait -->
                    <div class="glass-card p-2 rounded-lg hover:border-cyan-500/50 transition cursor-pointer country-row" onclick="focusLocation(29.0, 48.1, 'Mina Al Ahmadi')">
                        <div class="flex justify-between items-center mb-1">
                            <span class="font-medium text-slate-100">🇰🇼 Kuwait</span>
                            <span class="font-bold text-cyan-300 font-mono">1.82 M bpd</span>
                        </div>
                        <div class="space-y-1">
                            <div class="flex justify-between text-[10px] text-slate-400">
                                <span>Mina Al Ahmadi: <strong class="text-emerald-400">74.0%</strong></span>
                                <span class="text-emerald-300 font-mono">19M bbl ($1.49B)</span>
                            </div>
                            <div class="w-full bg-slate-800 h-1.5 rounded-full overflow-hidden">
                                <div class="bg-emerald-500 h-full rounded-full" style="width: 74.0%"></div>
                            </div>
                        </div>
                    </div>

                </div>
            </div>
        </div>

        <!-- Target Inspection Modal -->
        <div id="target-modal" class="hidden absolute bottom-20 left-4 md:left-6 w-80 md:w-96 pointer-events-auto glass-panel p-4 rounded-xl border border-cyan-500/40">
            <div class="flex justify-between items-start mb-2">
                <div>
                    <div class="flex items-center gap-2">
                        <span id="target-type" class="text-[10px] uppercase tracking-wider px-2 py-0.5 rounded bg-cyan-500/20 text-cyan-300 border border-cyan-500/30">Storage Farm</span>
                        <span class="text-[10px] text-slate-400">Satellite Overhead Pass</span>
                    </div>
                    <h3 id="target-title" class="text-base font-bold text-white mt-1">Cushing, Oklahoma Hub</h3>
                </div>
                <button onclick="closeModal()" class="text-slate-400 hover:text-white p-1">
                    <i data-lucide="x" class="w-4 h-4"></i>
                </button>
            </div>

            <div class="space-y-2.5 my-3 text-xs">
                <div class="bg-slate-950/60 p-2.5 rounded-lg border border-slate-800 space-y-1.5">
                    <div class="flex justify-between">
                        <span class="text-slate-400">Shadow Rim Geometry:</span>
                        <span id="target-shadow" class="text-cyan-300 font-mono">1.84m cast (Sun 42.1°)</span>
                    </div>
                    <div class="flex justify-between">
                        <span class="text-slate-400">Calculated Volume:</span>
                        <span id="target-height" class="text-slate-200 font-mono">14.50m / Fill: 7.55m</span>
                    </div>
                    <div class="flex justify-between">
                        <span class="text-slate-400">Estimated Dollar Value:</span>
                        <span id="target-value" class="text-emerald-400 font-mono font-bold">$2.51 Billion</span>
                    </div>
                </div>
            </div>

            <p id="target-desc" class="text-[11px] text-slate-300 leading-relaxed">
                Primary pipeline transfer hub and pricing settlement point for WTI crude.
            </p>
        </div>

        <!-- Bottom Controls Bar -->
        <div class="glass-panel p-2.5 rounded-2xl pointer-events-auto self-center flex items-center gap-2 md:gap-4 shadow-2xl">
            
            <button id="btn-spin" onclick="toggleSpin()" class="p-2 bg-cyan-500/20 hover:bg-cyan-500/30 border border-cyan-500/40 rounded-xl text-cyan-300 transition flex items-center gap-1.5 text-xs font-medium">
                <i data-lucide="rotate-cw" class="w-4 h-4"></i>
                <span class="hidden md:inline">Auto Orbit</span>
            </button>

            <button id="btn-sun" onclick="toggleDayNight()" class="p-2 bg-slate-800 hover:bg-slate-700 border border-slate-700 rounded-xl text-amber-300 transition flex items-center gap-1.5 text-xs font-medium">
                <i data-lucide="sun-medium" class="w-4 h-4"></i>
                <span class="hidden md:inline">Sun Angle</span>
            </button>

            <div class="h-5 w-px bg-slate-700/80"></div>

            <!-- Layer Toggles -->
            <button id="layer-tanks" onclick="toggleLayer('tanks')" class="px-2.5 py-1.5 bg-emerald-500/20 border border-emerald-500/50 rounded-xl text-emerald-300 text-xs font-medium flex items-center gap-1.5">
                <i data-lucide="database" class="w-3.5 h-3.5"></i>
                <span>Tanks</span>
            </button>

            <button id="layer-ships" onclick="toggleLayer('ships')" class="px-2.5 py-1.5 bg-amber-500/20 border border-amber-500/50 rounded-xl text-amber-300 text-xs font-medium flex items-center gap-1.5">
                <i data-lucide="ship" class="w-3.5 h-3.5"></i>
                <span>Tankers</span>
            </button>

            <button id="layer-refineries" onclick="toggleLayer('refineries')" class="px-2.5 py-1.5 bg-rose-500/20 border border-rose-500/50 rounded-xl text-rose-300 text-xs font-medium flex items-center gap-1.5">
                <i data-lucide="factory" class="w-3.5 h-3.5"></i>
                <span>Refineries</span>
            </button>

            <div class="h-5 w-px bg-slate-700/80"></div>

            <!-- Zoom Control Buttons -->
            <button onclick="zoomCamera(-1.5)" class="p-2 bg-slate-800 hover:bg-slate-700 text-slate-200 rounded-xl border border-slate-700 transition">
                <i data-lucide="zoom-in" class="w-4 h-4"></i>
            </button>
            <button onclick="zoomCamera(1.5)" class="p-2 bg-slate-800 hover:bg-slate-700 text-slate-200 rounded-xl border border-slate-700 transition">
                <i data-lucide="zoom-out" class="w-4 h-4"></i>
            </button>
        </div>

    </div>

    <!-- JavaScript Logic -->
    <script>
        // Global Constants
        const CRUDE_PRICE_PER_BBL = 78.50; // $ USD per barrel
        const TOTAL_TRACKED_BARRELS_M = 3200.48; // Million barrels

        // Three.js Scene Variables
        let scene, camera, renderer, controls, earthMesh, atmosphereMesh, sunLight, ambientLight;
        let isSpinning = true;
        let dayNightMode = 0;
        const pinsGroup = new THREE.Group();
        const raysGroup = new THREE.Group();
        const raycaster = new THREE.Raycaster();
        const mouse = new THREE.Vector2();

        const layers = { tanks: true, ships: true, refineries: true };

        // Real-world Oil Infrastructure Points
        const oilNodes = [
            { id: 1, name: "Cushing, Oklahoma (USA)", lat: 35.9, lon: -96.7, type: "tanks", fill: "52.1%", volume: "32M bbl", shadow: "1.84m (Sun 42°)", desc: "Primary WTI crude oil storage and pricing hub." },
            { id: 2, name: "Ras Tanura Port (Saudi Arabia)", lat: 26.6, lon: 50.1, type: "tanks", fill: "78.4%", volume: "142M bbl", shadow: "0.92m (Sun 61°)", desc: "Saudi Aramco primary terminal." },
            { id: 3, name: "Port of Rotterdam (Netherlands)", lat: 51.9, lon: 4.1, type: "tanks", fill: "69.1%", volume: "48M bbl", shadow: "2.10m (Sun 31°)", desc: "Primary European crude import & refining hub." },
            { id: 4, name: "Jurong Island (Singapore)", lat: 1.27, lon: 103.7, type: "refineries", fill: "82.5%", volume: "38M bbl", shadow: "0.45m (Sun 78°)", desc: "Asian refining bottleneck." },
            { id: 5, name: "Strait of Hormuz (Chokepoint)", lat: 26.5, lon: 56.2, type: "ships", fill: "N/A", volume: "20.5M bpd in transit", shadow: "SAR Vessel Freeboard", desc: "Monitors 20% of global oil trade." },
            { id: 6, name: "Hardisty Storage Hub (Canada)", lat: 52.6, lon: -111.3, type: "tanks", fill: "81.0%", volume: "29M bbl", shadow: "2.40m (Sun 28°)", desc: "Western Canadian heavy crude gathering hub." },
            { id: 7, name: "Fujairah Oil Terminal (UAE)", lat: 25.1, lon: 56.3, type: "tanks", fill: "68.5%", volume: "22M bbl", shadow: "1.05m (Sun 58°)", desc: "Major bunkering & storage terminal outside Persian Gulf." },
            { id: 8, name: "Basra Oil Terminal (Iraq)", lat: 29.7, lon: 48.8, type: "ships", fill: "71.8%", volume: "3.4M bpd berth", shadow: "SAR Draft Measurement", desc: "Offshore crude export terminal." },
            { id: 9, name: "Santos Basin Deepwater FPSOs (Brazil)", lat: -25.0, lon: -43.0, type: "refineries", fill: "62.0%", volume: "1.7M bpd extraction", shadow: "Thermal Heat Signature", desc: "Pre-salt deepwater offshore production vessels." },
            { id: 10, name: "Novorossiysk CPC Terminal (Black Sea)", lat: 44.6, lon: 37.8, type: "tanks", fill: "64.2%", volume: "18M bbl", shadow: "1.75m (Sun 38°)", desc: "Black Sea export terminus." }
        ];

        // 1. Live Timestamp Clock Updates
        function initRealTimeClock() {
            const clockEl = document.getElementById('utc-clock');
            
            function updateClock() {
                const now = new Date();
                const year = now.getUTCFullYear();
                const month = String(now.getUTCMonth() + 1).padStart(2, '0');
                const day = String(now.getUTCDate()).padStart(2, '0');
                const hours = String(now.getUTCHours()).padStart(2, '0');
                const minutes = String(now.getUTCMinutes()).padStart(2, '0');
                const seconds = String(now.getUTCSeconds()).padStart(2, '0');

                clockEl.innerText = `${year}-${month}-${day} ${hours}:${minutes}:${seconds} UTC`;
            }

            updateClock();
            setInterval(updateClock, 1000);
        }

        // 2. Compute Global Dollar Valuation
        function computeValuation() {
            const totalValuationBillion = (TOTAL_TRACKED_BARRELS_M * CRUDE_PRICE_PER_BBL) / 1000.0;
            
            document.getElementById('crude-price-display').innerText = `$${CRUDE_PRICE_PER_BBL.toFixed(2)}`;
            document.getElementById('total-barrels-display').innerText = `${TOTAL_TRACKED_BARRELS_M.toLocaleString('en-US', {minimumFractionDigits: 2})} M bbl`;
            document.getElementById('total-valuation-display').innerText = `$${totalValuationBillion.toFixed(2)} Billion`;
        }

        // 3. Render Real-Time Time Series Chart (Barrels vs Time)
        function initBarrelsChart() {
            const ctx = document.getElementById('barrelsChart').getContext('2d');

            // Generate 24-hour time labels backwards from now
            const labels = [];
            const dataBarrels = [];
            const nowHour = new Date().getUTCHours();

            let baseVal = 3180.0;
            for (let i = 24; i >= 0; i--) {
                const hr = (nowHour - i + 24) % 24;
                labels.push(`${String(hr).padStart(2, '0')}:00`);
                // Simulate satellite pass variance
                baseVal += (Math.random() - 0.48) * 3.5;
                dataBarrels.push(parseFloat(baseVal.toFixed(2)));
            }
            // Ensure last point matches our HUD total
            dataBarrels[dataBarrels.length - 1] = TOTAL_TRACKED_BARRELS_M;

            new Chart(ctx, {
                type: 'line',
                data: {
                    labels: labels,
                    datasets: [{
                        label: 'Global Barrels (Millions)',
                        data: dataBarrels,
                        borderColor: '#38bdf8',
                        borderWidth: 2,
                        backgroundColor: (context) => {
                            const chart = context.chart;
                            const {ctx, chartArea} = chart;
                            if (!chartArea) return null;
                            const gradient = ctx.createLinearGradient(0, chartArea.top, 0, chartArea.bottom);
                            gradient.addColorStop(0, 'rgba(56, 189, 248, 0.35)');
                            gradient.addColorStop(1, 'rgba(56, 189, 248, 0.0)');
                            return gradient;
                        },
                        fill: true,
                        tension: 0.35,
                        pointRadius: 1.5,
                        pointHoverRadius: 4,
                        pointBackgroundColor: '#38bdf8'
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: { display: false },
                        tooltip: {
                            callbacks: {
                                label: (context) => ` Inventory: ${context.parsed.y}M bbl ($${((context.parsed.y * CRUDE_PRICE_PER_BBL)/1000).toFixed(2)}B)`
                            }
                        }
                    },
                    scales: {
                        x: {
                            grid: { color: 'rgba(255, 255, 255, 0.05)' },
                            ticks: { color: '#94a3b8', font: { size: 9 }, maxTicksLimit: 6 }
                        },
                        y: {
                            grid: { color: 'rgba(255, 255, 255, 0.05)' },
                            ticks: { color: '#94a3b8', font: { size: 9 } }
                        }
                    }
                }
            });
        }

        // Initialize 3D World Scene
        function initScene() {
            const container = document.getElementById('canvas-container');

            scene = new THREE.Scene();
            scene.fog = new THREE.FogExp2(0x030712, 0.015);

            camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.1, 1000);
            camera.position.set(0, 10, 22);

            renderer = new THREE.WebGLRenderer({ antialias: true, alpha: true });
            renderer.setSize(window.innerWidth, window.innerHeight);
            renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
            renderer.shadowMap.enabled = true;
            container.appendChild(renderer.domElement);

            controls = new THREE.OrbitControls(camera, renderer.domElement);
            controls.enableDamping = true;
            controls.dampingFactor = 0.05;
            controls.rotateSpeed = 0.8;
            controls.zoomSpeed = 1.2;
            controls.minDistance = 8;
            controls.maxDistance = 40;

            ambientLight = new THREE.AmbientLight(0x1e293b, 1.2);
            scene.add(ambientLight);

            sunLight = new THREE.DirectionalLight(0xffffff, 2.5);
            sunLight.position.set(25, 12, 20);
            sunLight.castShadow = true;
            scene.add(sunLight);

            buildProceduralEarth();
            addAtmosphere();
            createNodes();

            window.addEventListener('resize', onWindowResize);
            renderer.domElement.addEventListener('click', onDocumentOptionClick);

            lucide.createIcons();
            animate();
        }

        function buildProceduralEarth() {
            const canvas = document.createElement('canvas');
            canvas.width = 2048;
            canvas.height = 1024;
            const ctx = canvas.getContext('2d');

            const oceanGradient = ctx.createLinearGradient(0, 0, 0, canvas.height);
            oceanGradient.addColorStop(0, '#060d1a');
            oceanGradient.addColorStop(0.5, '#0b172a');
            oceanGradient.addColorStop(1, '#060d1a');
            ctx.fillStyle = oceanGradient;
            ctx.fillRect(0, 0, canvas.width, canvas.height);

            ctx.strokeStyle = 'rgba(56, 189, 248, 0.08)';
            ctx.lineWidth = 1;
            for (let x = 0; x <= canvas.width; x += 64) {
                ctx.beginPath(); ctx.moveTo(x, 0); ctx.lineTo(x, canvas.height); ctx.stroke();
            }
            for (let y = 0; y <= canvas.height; y += 64) {
                ctx.beginPath(); ctx.moveTo(0, y); ctx.lineTo(canvas.width, y); ctx.stroke();
            }

            ctx.fillStyle = 'rgba(30, 58, 138, 0.45)';
            ctx.strokeStyle = 'rgba(56, 189, 248, 0.3)';
            ctx.lineWidth = 1.5;

            const continents = [
                [[300, 200], [500, 180], [600, 320], [550, 450], [420, 520], [350, 380]],
                [[520, 550], [620, 580], [660, 720], [580, 900], [510, 750]],
                [[950, 180], [1200, 160], [1600, 220], [1700, 450], [1400, 500], [1150, 420], [980, 320]],
                [[920, 400], [1120, 420], [1180, 600], [1080, 800], [980, 720], [900, 520]],
                [[1500, 680], [1700, 680], [1720, 820], [1520, 820]]
            ];

            continents.forEach(poly => {
                ctx.beginPath();
                ctx.moveTo(poly[0][0], poly[0][1]);
                for (let i = 1; i < poly.length; i++) ctx.lineTo(poly[i][0], poly[i][1]);
                ctx.closePath();
                ctx.fill();
                ctx.stroke();
            });

            ctx.fillStyle = '#f59e0b';
            [[420, 420], [1120, 420], [980, 280], [1550, 510], [1130, 440], [350, 350], [580, 750]].forEach(pt => {
                ctx.beginPath(); ctx.arc(pt[0], pt[1], 4, 0, Math.PI * 2); ctx.fill();
            });

            const texture = new THREE.CanvasTexture(canvas);
            const earthGeo = new THREE.SphereGeometry(6, 64, 64);
            const earthMat = new THREE.MeshStandardMaterial({ map: texture, roughness: 0.6, metalness: 0.2 });

            earthMesh = new THREE.Mesh(earthGeo, earthMat);
            earthMesh.receiveShadow = true;
            earthMesh.castShadow = true;
            scene.add(earthMesh);
            earthMesh.add(pinsGroup);
            earthMesh.add(raysGroup);
        }

        function addAtmosphere() {
            const atmosGeo = new THREE.SphereGeometry(6.25, 64, 64);
            const atmosMat = new THREE.MeshBasicMaterial({ color: 0x38bdf8, transparent: true, opacity: 0.08, side: THREE.BackSide });
            atmosphereMesh = new THREE.Mesh(atmosGeo, atmosMat);
            scene.add(atmosphereMesh);
        }

        function latLonToVector3(lat, lon, radius) {
            const phi = (90 - lat) * (Math.PI / 180);
            const theta = (lon + 180) * (Math.PI / 180);
            return new THREE.Vector3(
                -(radius * Math.sin(phi) * Math.cos(theta)),
                (radius * Math.cos(phi)),
                (radius * Math.sin(phi) * Math.sin(theta))
            );
        }

        function createNodes() {
            oilNodes.forEach(node => {
                const pos = latLonToVector3(node.lat, node.lon, 6.08);

                let colorHex = 0x10b981;
                if (node.type === 'ships') colorHex = 0xf59e0b;
                if (node.type === 'refineries') colorHex = 0xf43f5e;

                const pinGeo = new THREE.SphereGeometry(0.12, 16, 16);
                const pinMat = new THREE.MeshBasicMaterial({ color: colorHex });
                const pinMesh = new THREE.Mesh(pinGeo, pinMat);
                pinMesh.position.copy(pos);
                pinMesh.userData = node;

                const ringGeo = new THREE.RingGeometry(0.18, 0.25, 32);
                const ringMat = new THREE.MeshBasicMaterial({ color: colorHex, side: THREE.DoubleSide, transparent: true, opacity: 0.8 });
                const ringMesh = new THREE.Mesh(ringGeo, ringMat);
                ringMesh.position.copy(pos.clone().multiplyScalar(1.01));
                ringMesh.lookAt(new THREE.Vector3(0, 0, 0));

                const rayPosEnd = pos.clone().multiplyScalar(1.25);
                const rayGeo = new THREE.BufferGeometry().setFromPoints([pos, rayPosEnd]);
                const rayMat = new THREE.LineBasicMaterial({ color: colorHex, transparent: true, opacity: 0.4 });
                const rayLine = new THREE.Line(rayGeo, rayMat);

                pinsGroup.add(pinMesh);
                pinsGroup.add(ringMesh);
                raysGroup.add(rayLine);
            });
        }

        function onDocumentOptionClick(event) {
            event.preventDefault();
            mouse.x = (event.clientX / window.innerWidth) * 2 - 1;
            mouse.y = -(event.clientY / window.innerHeight) * 2 + 1;

            raycaster.setFromCamera(mouse, camera);
            const intersects = raycaster.intersectObjects(pinsGroup.children);

            if (intersects.length > 0) {
                const clickedObj = intersects[0].object;
                if (clickedObj.userData && clickedObj.userData.name) {
                    showTargetModal(clickedObj.userData);
                }
            }
        }

        function showTargetModal(data) {
            document.getElementById('target-modal').classList.remove('hidden');
            document.getElementById('target-title').innerText = data.name;
            document.getElementById('target-shadow').innerText = data.shadow;
            document.getElementById('target-height').innerText = `${data.fill} (${data.volume})`;
            
            // Extract numeric million barrels if possible
            const match = data.volume.match(/(\d+)M/);
            if (match) {
                const bblM = parseFloat(match[1]);
                const valB = (bblM * CRUDE_PRICE_PER_BBL) / 1000.0;
                document.getElementById('target-value').innerText = `$${valB.toFixed(2)} Billion`;
            } else {
                document.getElementById('target-value').innerText = "In Transit";
            }

            document.getElementById('target-desc').innerText = data.desc;
            document.getElementById('target-type').innerText = data.type;

            focusLocation(data.lat, data.lon, data.name);
        }

        function closeModal() {
            document.getElementById('target-modal').classList.add('hidden');
        }

        function focusLocation(lat, lon, label) {
            isSpinning = false;
            document.getElementById('btn-spin').classList.remove('bg-cyan-500/20', 'text-cyan-300');
            document.getElementById('btn-spin').classList.add('bg-slate-800', 'text-slate-400');

            const targetPos = latLonToVector3(lat, lon, 18);
            const startPos = camera.position.clone();
            let progress = 0;

            function animateCamera() {
                progress += 0.04;
                camera.position.lerpVectors(startPos, targetPos, progress);
                camera.lookAt(0, 0, 0);
                if (progress < 1) requestAnimationFrame(animateCamera);
            }
            animateCamera();
        }

        function toggleSpin() {
            isSpinning = !isSpinning;
            const btn = document.getElementById('btn-spin');
            if (isSpinning) {
                btn.classList.add('bg-cyan-500/20', 'text-cyan-300');
                btn.classList.remove('bg-slate-800', 'text-slate-400');
            } else {
                btn.classList.remove('bg-cyan-500/20', 'text-cyan-300');
                btn.classList.add('bg-slate-800', 'text-slate-400');
            }
        }

        function toggleDayNight() {
            dayNightMode = (dayNightMode + 1) % 3;
            if (dayNightMode === 0) {
                sunLight.position.set(25, 12, 20);
                ambientLight.intensity = 1.2;
            } else if (dayNightMode === 1) {
                sunLight.position.set(0, 30, 0);
                ambientLight.intensity = 1.5;
            } else {
                sunLight.position.set(-30, -5, -20);
                ambientLight.intensity = 0.4;
            }
        }

        function toggleLayer(layerName) {
            layers[layerName] = !layers[layerName];
            const btn = document.getElementById(`layer-${layerName}`);
            btn.classList.toggle('opacity-100', layers[layerName]);
            btn.classList.toggle('opacity-40', !layers[layerName]);

            pinsGroup.children.forEach(child => {
                if (child.userData && child.userData.type === layerName) {
                    child.visible = layers[layerName];
                }
            });
        }

        function zoomCamera(delta) {
            const newZ = camera.position.length() + delta;
            if (newZ >= 8 && newZ <= 38) {
                camera.position.multiplyScalar(newZ / camera.position.length());
            }
        }

        function onWindowResize() {
            camera.aspect = window.innerWidth / window.innerHeight;
            camera.updateProjectionMatrix();
            renderer.setSize(window.innerWidth, window.innerHeight);
        }

        function animate() {
            requestAnimationFrame(animate);

            if (isSpinning && earthMesh) {
                earthMesh.rotation.y += 0.0015;
            }
            if (atmosphereMesh) {
                atmosphereMesh.rotation.y += 0.0008;
            }

            const time = Date.now() * 0.003;
            pinsGroup.children.forEach(child => {
                if (child.geometry instanceof THREE.RingGeometry) {
                    const scale = 1 + Math.sin(time * 2) * 0.15;
                    child.scale.set(scale, scale, scale);
                }
            });

            controls.update();
            renderer.render(scene, camera);
        }

        // Window Load Bootstrapper
        window.onload = function() {
            initRealTimeClock();
            computeValuation();
            initBarrelsChart();
            initScene();
        };
    </script>
</body>
</html>

