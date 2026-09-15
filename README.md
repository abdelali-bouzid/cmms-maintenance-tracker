# cmms-maintenance-tracker
Scripts and tools for industrial maintenance management and automation.
HTML
<!DOCTYPE html>
<html lang="fr">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>AMDEC PRO V1 - Gestion de la Maintenance & Criticité</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Chart.js for Pareto -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        .crit-faible { background-color: #10b981; color: white; }
        .crit-moyen { background-color: #f59e0b; color: white; }
        .crit-eleve { background-color: #f97316; color: white; }
        .crit-critique { background-color: #ef4444; color: white; }
    </style>
</head>
<body class="bg-slate-100 font-sans text-slate-800 antialiased min-h-screen flex flex-col">

    <!-- Header / Navbar -->
    <header class="bg-slate-900 text-white shadow-md sticky top-0 z-50">
        <div class="max-w-7xl mx-auto px-4 py-4 flex flex-wrap justify-between items-center gap-4">
            <div class="flex items-center gap-3">
                <i class="fa-solid fa-gears text-2xl text-indigo-400"></i>
                <h1 class="text-xl font-bold tracking-wide">AMDEC PRO <span class="text-xs bg-indigo-600 px-2 py-0.5 rounded text-white font-normal">V1.0</span></h1>
            </div>
            
            <div class="flex items-center gap-2 flex-wrap">
                <button onclick="exportJSON()" class="bg-slate-800 hover:bg-slate-700 text-slate-200 px-3 py-1.5 rounded text-sm flex items-center gap-2 border border-slate-700 transition">
                    <i class="fa-solid fa-download text-indigo-400"></i> Export JSON
                </button>
                <label class="bg-slate-800 hover:bg-slate-700 text-slate-200 px-3 py-1.5 rounded text-sm flex items-center gap-2 border border-slate-700 cursor-pointer transition">
                    <i class="fa-solid fa-upload text-emerald-400"></i> Import JSON
                    <input type="file" id="importJsonFile" accept=".json" class="hidden" onchange="importJSON(event)">
                </label>
                <button onclick="exportCSV()" class="bg-emerald-700 hover:bg-emerald-600 text-white px-3 py-1.5 rounded text-sm flex items-center gap-2 transition">
                    <i class="fa-solid fa-file-excel"></i> Export CSV
                </button>
            </div>
        </div>
    </header>

    <!-- Main Container -->
    <main class="max-w-7xl mx-auto px-4 py-6 flex-grow w-full space-y-6">

        <!-- Navigation Tabs -->
        <div class="flex border-b border-slate-300 gap-2 overflow-x-auto">
            <button onclick="switchTab('dashboard')" id="tab-dashboard" class="tab-btn px-4 py-2 font-semibold text-indigo-600 border-b-2 border-indigo-600 flex items-center gap-2">
                <i class="fa-solid fa-chart-pie"></i> Dashboard
            </button>
            <button onclick="switchTab('amdec')" id="tab-amdec" class="tab-btn px-4 py-2 font-semibold text-slate-600 border-b-2 border-transparent hover:text-indigo-600 flex items-center gap-2">
                <i class="fa-solid fa-list-check"></i> Analyses AMDEC
            </button>
            <button onclick="switchTab('equipements')" id="tab-equipements" class="tab-btn px-4 py-2 font-semibold text-slate-600 border-b-2 border-transparent hover:text-indigo-600 flex items-center gap-2">
                <i class="fa-solid fa-wrench"></i> Équipements
            </button>
            <button onclick="switchTab('pareto')" id="tab-pareto" class="tab-btn px-4 py-2 font-semibold text-slate-600 border-b-2 border-transparent hover:text-indigo-600 flex items-center gap-2">
                <i class="fa-solid fa-chart-bar"></i> Diagramme de Pareto
            </button>
        </div>

        <!-- SECTION 1: DASHBOARD -->
        <section id="sec-dashboard" class="space-y-6">
            <!-- Stats Grid -->
            <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
                <div class="bg-white p-5 rounded-lg shadow border border-slate-200">
                    <div class="text-sm font-medium text-slate-500">Total Analyses AMDEC</div>
                    <div class="text-3xl font-bold text-slate-800 mt-2" id="stat-total">0</div>
                </div>
                <div class="bg-white p-5 rounded-lg shadow border border-slate-200">
                    <div class="text-sm font-medium text-slate-500">Risques Critiques (NPR ≥ 100)</div>
                    <div class="text-3xl font-bold text-red-600 mt-2" id="stat-critique">0</div>
                </div>
                <div class="bg-white p-5 rounded-lg shadow border border-slate-200">
                    <div class="text-sm font-medium text-slate-500">Équipements Enregistrés</div>
                    <div class="text-3xl font-bold text-indigo-600 mt-2" id="stat-equipements">0</div>
                </div>
                <div class="bg-white p-5 rounded-lg shadow border border-slate-200">
                    <div class="text-sm font-medium text-slate-500">Actions En Cours / A Faire</div>
                    <div class="text-3xl font-bold text-amber-600 mt-2" id="stat-actions">0</div>
                </div>
            </div>

            <!-- Summary Table / Recent High Risks -->
            <div class="bg-white rounded-lg shadow border border-slate-200 p-5">
                <h2 class="text-lg font-bold text-slate-800 mb-4 flex items-center gap-2">
                    <i class="fa-solid fa-triangle-exclamation text-amber-500"></i> Points de Vigilance Élevés & Critiques
                </h2>
                <div class="overflow-x-auto">
                    <table class="w-full text-left border-collapse text-sm">
                        <thead>
                            <tr class="bg-slate-100 border-b border-slate-200 text-slate-600">
                                <th class="p-3">Équipement</th>
                                <th class="p-3">Mode de Défaillance</th>
                                <th class="p-3">Cause</th>
                                <th class="p-3">NPR</th>
                                <th class="p-3">Criticité</th>
                                <th class="p-3">Action Réductrice</th>
                            </tr>
                        </thead>
                        <tbody id="table-dashboard-high-risk">
                            <!-- Injected JS -->
                        </tbody>
                    </table>
                </div>
            </div>
        </section>

        <!-- SECTION 2: AMDEC MANAGEMENT -->
        <section id="sec-amdec" class="hidden space-y-6">
            <!-- Control Bar -->
            <div class="bg-white p-4 rounded-lg shadow border border-slate-200 flex flex-wrap gap-4 items-center justify-between">
                <div class="flex items-center gap-3 flex-grow max-w-lg">
                    <div class="relative w-full">
                        <i class="fa-solid fa-magnifying-glass absolute left-3 top-3 text-slate-400"></i>
                        <input type="text" id="searchAmdec" oninput="renderAMDEC()" placeholder="Rechercher équipement, élément, cause..." class="w-full pl-9 pr-4 py-2 border rounded-md text-sm focus:ring-2 focus:ring-indigo-500 outline-none">
                    </div>
                    <select id="filterCriticite" onchange="renderAMDEC()" class="border py-2 px-3 rounded-md text-sm outline-none focus:ring-2 focus:ring-indigo-500">
                        <option value="">Toutes les criticités</option>
                        <option value="Faible">Faible</option>
                        <option value="Moyen">Moyen</option>
                        <option value="Élevé">Élevé</option>
                        <option value="Critique">Critique</option>
                    </select>
                </div>
                <button onclick="openAmdecModal()" class="bg-indigo-600 hover:bg-indigo-700 text-white px-4 py-2 rounded-md text-sm font-semibold flex items-center gap-2 shadow transition">
                    <i class="fa-solid fa-plus"></i> Nouvelle Analyse AMDEC
                </button>
            </div>

            <!-- AMDEC Table -->
            <div class="bg-white rounded-lg shadow border border-slate-200 overflow-hidden">
                <div class="overflow-x-auto">
                    <table class="w-full text-left border-collapse text-xs md:text-sm">
                        <thead class="bg-slate-800 text-white uppercase text-xs">
                            <tr>
                                <th class="p-3">Équipement</th>
                                <th class="p-3">Élément / S.Ensemble</th>
                                <th class="p-3">Mode Défaillance</th>
                                <th class="p-3">Effet</th>
                                <th class="p-3">Cause</th>
                                <th class="p-3 text-center">G</th>
                                <th class="p-3 text-center">O</th>
                                <th class="p-3 text-center">D</th>
                                <th class="p-3 text-center">NPR</th>
                                <th class="p-3 text-center">Criticité</th>
                                <th class="p-3">Action Préconisée</th>
                                <th class="p-3 text-center">Actions</th>
                            </tr>
                        </thead>
                        <tbody id="table-amdec-body" class="divide-y divide-slate-200">
                            <!-- Injected JS -->
                        </tbody>
                    </table>
                </div>
            </div>
        </section>

        <!-- SECTION 3: EQUIPEMENTS -->
        <section id="sec-equipements" class="hidden space-y-6">
            <div class="bg-white p-4 rounded-lg shadow border border-slate-200 flex justify-between items-center">
                <h2 class="text-lg font-bold text-slate-800">Gestion du Parc Équipement</h2>
                <button onclick="openEquipementModal()" class="bg-indigo-600 hover:bg-indigo-700 text-white px-4 py-2 rounded-md text-sm font-semibold flex items-center gap-2 shadow transition">
                    <i class="fa-solid fa-plus"></i> Ajouter Équipement
                </button>
            </div>

            <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4" id="grid-equipements">
                <!-- Injected JS -->
            </div>
        </section>

        <!-- SECTION 4: PARETO -->
        <section id="sec-pareto" class="hidden space-y-6">
            <div class="bg-white p-5 rounded-lg shadow border border-slate-200">
                <div class="flex flex-wrap justify-between items-center mb-4 gap-4">
                    <h2 class="text-lg font-bold text-slate-800">Analyse de Pareto des Risques (NPR Cumulé)</h2>
                    <select id="paretoFilterType" onchange="updateParetoChart()" class="border py-2 px-3 rounded-md text-sm outline-none">
                        <option value="equipement">Par Équipement</option>
                        <option value="zone">Par Zone / Secteur</option>
                    </select>
                </div>
                <div class="relative h-96 w-full">
                    <canvas id="paretoChart"></canvas>
                </div>
            </div>
        </section>

    </main>

    <!-- MODAL AMDEC (Form Add/Edit) -->
    <div id="modalAmdec" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm hidden z-50 flex justify-center items-center p-4 overflow-y-auto">
        <div class="bg-white rounded-lg shadow-xl w-full max-w-2xl overflow-hidden my-8">
            <div class="bg-slate-900 text-white px-6 py-4 flex justify-between items-center">
                <h3 class="text-lg font-bold" id="modalAmdecTitle">Nouvelle Analyse AMDEC</h3>
                <button onclick="closeAmdecModal()" class="text-slate-400 hover:text-white"><i class="fa-solid fa-xmark text-xl"></i></button>
            </div>
            <form id="formAmdec" onsubmit="saveAMDEC(event)" class="p-6 space-y-4">
                <input type="hidden" id="amdecId">
                
                <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                    <div>
                        <label class="block text-xs font-bold text-slate-700 uppercase mb-1">Équipement *</label>
                        <select id="amdecEquipement" required class="w-full border rounded p-2 text-sm outline-none focus:ring-2 focus:ring-indigo-500">
                            <!-- Injected JS -->
                        </select>
                    </div>
                    <div>
                        <label class="block text-xs font-bold text-slate-700 uppercase mb-1">Sous-ensemble / Élément *</label>
                        <input type="text" id="amdecElement" required class="w-full border rounded p-2 text-sm outline-none focus:ring-2 focus:ring-indigo-500" placeholder="ex: Roulement, Moteur...">
                    </div>
                </div>

                <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                    <div>
                        <label class="block text-xs font-bold text-slate-700 uppercase mb-1">Mode de Défaillance *</label>
                        <input type="text" id="amdecMode" required class="w-full border rounded p-2 text-sm outline-none focus:ring-2 focus:ring-indigo-500" placeholder="ex: Surchauffe, Grippage...">
                    </div>
                    <div>
                        <label class="block text-xs font-bold text-slate-700 uppercase mb-1">Effet de la Défaillance *</label>
                        <input type="text" id="amdecEffet" required class="w-full border rounded p-2 text-sm outline-none focus:ring-2 focus:ring-indigo-500" placeholder="ex: Arrêt ligne, Casse...">
                    </div>
                </div>

                <div>
                    <label class="block text-xs font-bold text-slate-700 uppercase mb-1">Cause de la Défaillance *</label>
                    <input type="text" id="amdecCause" required class="w-full border rounded p-2 text-sm outline-none focus:ring-2 focus:ring-indigo-500" placeholder="ex: Défaut de lubrification...">
                </div>

                <!-- GOD Rating Grid -->
                <div class="bg-slate-50 p-4 rounded-lg border border-slate-200 grid grid-cols-3 gap-3 text-center">
                    <div>
                        <label class="block text-xs font-bold text-slate-700 mb-1">Gravité (G) [1-10]</label>
                        <input type="number" min="1" max="10" id="amdecG" value="1" oninput="calcNPR()" required class="w-full border rounded p-2 text-center text-sm font-bold text-slate-800">
                    </div>
                    <div>
                        <label class="block text-xs font-bold text-slate-700 mb-1">Occurrence (O) [1-10]</label>
                        <input type="number" min="1" max="10" id="amdecO" value="1" oninput="calcNPR()" required class="w-full border rounded p-2 text-center text-sm font-bold text-slate-800">
                    </div>
                    <div>
                        <label class="block text-xs font-bold text-slate-700 mb-1">Détection (D) [1-10]</label>
                        <input type="number" min="1" max="10" id="amdecD" value="1" oninput="calcNPR()" required class="w-full border rounded p-2 text-center text-sm font-bold text-slate-800">
                    </div>
                </div>

                <!-- NPR Preview -->
                <div class="flex justify-between items-center p-3 bg-indigo-50 border border-indigo-200 rounded-lg">
                    <span class="text-sm font-semibold text-indigo-900">NPR Calculé = G × O × D</span>
                    <div class="flex items-center gap-3">
                        <span id="previewNPR" class="text-xl font-black text-indigo-600">1</span>
                        <span id="previewCriticite" class="text-xs font-bold px-2.5 py-1 rounded crit-faible">Faible</span>
                    </div>
                </div>

                <div>
                    <label class="block text-xs font-bold text-slate-700 uppercase mb-1">Plan d'action Préconisé</label>
                    <textarea id="amdecAction" rows="2" class="w-full border rounded p-2 text-sm outline-none focus:ring-2 focus:ring-indigo-500" placeholder="Planifier graissage mensuel..."></textarea>
                </div>

                <div class="flex justify-end gap-3 pt-4 border-t">
                    <button type="button" onclick="closeAmdecModal()" class="px-4 py-2 border rounded text-sm font-semibold text-slate-600 hover:bg-slate-100">Annuler</button>
                    <button type="submit" class="px-5 py-2 bg-indigo-600 hover:bg-indigo-700 text-white rounded text-sm font-semibold shadow">Enregistrer</button>
                </div>
            </form>
        </div>
    </div>

    <!-- MODAL EQUIPEMENT -->
    <div id="modalEquipement" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm hidden z-50 flex justify-center items-center p-4">
        <div class="bg-white rounded-lg shadow-xl w-full max-w-md overflow-hidden">
            <div class="bg-slate-900 text-white px-6 py-4 flex justify-between items-center">
                <h3 class="text-lg font-bold">Ajouter un Équipement</h3>
                <button onclick="closeEquipementModal()" class="text-slate-400 hover:text-white"><i class="fa-solid fa-xmark text-xl"></i></button>
            </div>
            <form id="formEquipement" onsubmit="saveEquipement(event)" class="p-6 space-y-4">
                <div>
                    <label class="block text-xs font-bold text-slate-700 uppercase mb-1">Nom / Code Équipement *</label>
                    <input type="text" id="eqNom" required class="w-full border rounded p-2 text-sm outline-none focus:ring-2 focus:ring-indigo-500" placeholder="ex: Moteur Pompe P-101">
                </div>
                <div>
                    <label class="block text-xs font-bold text-slate-700 uppercase mb-1">Zone / Secteur *</label>
                    <input type="text" id="eqZone" required class="w-full border rounded p-2 text-sm outline-none focus:ring-2 focus:ring-indigo-500" placeholder="ex: Zone Broyage">
                </div>
                <div class="flex justify-end gap-3 pt-4 border-t">
                    <button type="button" onclick="closeEquipementModal()" class="px-4 py-2 border rounded text-sm font-semibold text-slate-600 hover:bg-slate-100">Annuler</button>
                    <button type="submit" class="px-5 py-2 bg-indigo-600 hover:bg-indigo-700 text-white rounded text-sm font-semibold shadow">Créer</button>
                </div>
            </form>
        </div>
    </div>

    <!-- JavaScript Logic -->
    <script>
        // Data Structures & Storage
        let dataEquipements = JSON.parse(localStorage.getItem('AMDEC_EQUIPEMENTS')) || [
            { id: 1, nom: "Pompe Hydraulique P-01", zone: "Zone A - Extrusion" },
            { id: 2, nom: "Moteur Principal M-02", zone: "Zone B - Broyage" }
        ];

        let dataAmdec = JSON.parse(localStorage.getItem('AMDEC_DATA')) || [
            { id: 1, equipementId: 1, element: "Joint d'étanchéité", mode: "Fuite d'huile", effet: "Baisse de pression systeme", cause: "Usure naturelle / Pression", g: 6, o: 5, d: 4, npr: 120, criticite: "Critique", action: "Remplacement préventif tous les 6 mois" },
            { id: 2, equipementId: 2, element: "Roulement à billes", mode: "Grippage", effet: "Arrêt complet du moteur", cause: "Manque de graissage", g: 8, o: 3, d: 3, npr: 72, criticite: "Élevé", action: "Mettre en place un plan de graissage hebdo" }
        ];

        let paretoChartInstance = null;

        // Initialize App
        document.addEventListener("DOMContentLoaded", () => {
            saveDataLocally();
            renderDashboard();
            renderAMDEC();
            renderEquipements();
            initParetoChart();
        });

        function saveDataLocally() {
            localStorage.setItem('AMDEC_EQUIPEMENTS', JSON.stringify(dataEquipements));
            localStorage.setItem('AMDEC_DATA', JSON.stringify(dataAmdec));
        }

        // Tab Switcher
        function switchTab(tab) {
            ['dashboard', 'amdec', 'equipements', 'pareto'].forEach(t => {
                document.getElementById(`sec-${t}`).classList.add('hidden');
                document.getElementById(`tab-${t}`).classList.remove('text-indigo-600', 'border-indigo-600');
                document.getElementById(`tab-${t}`).classList.add('text-slate-600', 'border-transparent');
            });

            document.getElementById(`sec-${tab}`).classList.remove('hidden');
            document.getElementById(`tab-${tab}`).classList.add('text-indigo-600', 'border-indigo-600');
            document.getElementById(`tab-${tab}`).classList.remove('text-slate-600', 'border-transparent');

            if (tab === 'dashboard') renderDashboard();
            if (tab === 'pareto') updateParetoChart();
        }

        // Helper: Get Criticite Level
        function calculateCriticite(npr) {
            if (npr < 30) return { label: 'Faible', class: 'crit-faible' };
            if (npr < 70) return { label: 'Moyen', class: 'crit-moyen' };
            if (npr < 100) return { label: 'Élevé', class: 'crit-eleve' };
            return { label: 'Critique', class: 'crit-critique' };
        }

        // Calculate NPR Live
        function calcNPR() {
            const g = parseInt(document.getElementById('amdecG').value) || 1;
            const o = parseInt(document.getElementById('amdecO').value) || 1;
            const d = parseInt(document.getElementById('amdecD').value) || 1;
            const npr = g * o * d;
            
            const critInfo = calculateCriticite(npr);
            const previewNPR = document.getElementById('previewNPR');
            const previewCrit = document.getElementById('previewCriticite');

            previewNPR.textContent = npr;
            previewCrit.textContent = critInfo.label;
            previewCrit.className = `text-xs font-bold px-2.5 py-1 rounded ${critInfo.class}`;
        }

        // Render Dashboard
        function renderDashboard() {
            document.getElementById('stat-total').textContent = dataAmdec.length;
            document.getElementById('stat-critique').textContent = dataAmdec.filter(a => a.npr >= 100).length;
            document.getElementById('stat-equipements').textContent = dataEquipements.length;
            document.getElementById('stat-actions').textContent = dataAmdec.filter(a => a.action && a.action.trim() !== '').length;

            const highRiskTable = document.getElementById('table-dashboard-high-risk');
            highRiskTable.innerHTML = '';

            const highRisks = dataAmdec.filter(a => a.npr >= 70).sort((a, b) => b.npr - a.npr);

            if (highRisks.length === 0) {
                highRiskTable.innerHTML = `<tr><td colspan="6" class="p-4 text-center text-slate-400">Aucun risque élevé ou critique détecté.</td></tr>`;
                return;
            }

            highRisks.forEach(item => {
                const eq = dataEquipements.find(e => e.id === item.equipementId);
                const crit = calculateCriticite(item.npr);
                highRiskTable.innerHTML += `
                    <tr class="border-b border-slate-100 hover:bg-slate-50">
                        <td class="p-3 font-semibold text-slate-700">${eq ? eq.nom : 'Inconnu'}</td>
                        <td class="p-3">${item.mode}</td>
                        <td class="p-3">${item.cause}</td>
                        <td class="p-3 font-bold">${item.npr}</td>
                        <td class="p-3"><span class="px-2 py-0.5 rounded text-xs font-bold ${crit.class}">${crit.label}</span></td>
                        <td class="p-3 text-slate-600">${item.action || 'Aucune'}</td>
                    </tr>
                `;
            });
        }

        // Render AMDEC Table
        function renderAMDEC() {
            const tbody = document.getElementById('table-amdec-body');
            const search = document.getElementById('searchAmdec').value.toLowerCase();
            const filterCrit = document.getElementById('filterCriticite').value;

            tbody.innerHTML = '';

            let filtered = dataAmdec.filter(item => {
                const eq = dataEquipements.find(e => e.id === item.equipementId);
                const eqName = eq ? eq.nom.toLowerCase() : '';
                const matchesSearch = eqName.includes(search) || 
                                      item.element.toLowerCase().includes(search) || 
                                      item.mode.toLowerCase().includes(search) || 
                                      item.cause.toLowerCase().includes(search);
                const crit = calculateCriticite(item.npr).label;
                const matchesCrit = filterCrit === '' || crit === filterCrit;

                return matchesSearch && matchesCrit;
            });

            if (filtered.length === 0) {
                tbody.innerHTML = `<tr><td colspan="12" class="p-6 text-center text-slate-400">Aucune donnée AMDEC trouvée.</td></tr>`;
                return;
            }

            filtered.forEach(item => {
                const eq = dataEquipements.find(e => e.id === item.equipementId);
                const crit = calculateCriticite(item.npr);
                tbody.innerHTML += `
                    <tr class="hover:bg-slate-50">
                        <td class="p-3 font-medium text-slate-800">${eq ? eq.nom : 'Inconnu'}</td>
                        <td class="p-3">${item.element}</td>
                        <td class="p-3">${item.mode}</td>
                        <td class="p-3 text-slate-500">${item.effet}</td>
                        <td class="p-3 text-slate-500">${item.cause}</td>
                        <td class="p-3 text-center font-semibold">${item.g}</td>
                        <td class="p-3 text-center font-semibold">${item.o}</td>
                        <td class="p-3 text-center font-semibold">${item.d}</td>
                        <td class="p-3 text-center font-bold text-slate-900">${item.npr}</td>
                        <td class="p-3 text-center"><span class="px-2 py-0.5 rounded text-xs font-bold ${crit.class}">${crit.label}</span></td>
                        <td class="p-3 text-slate-600">${item.action}</td>
                        <td class="p-3 text-center space-x-2">
                            <button onclick="editAMDEC(${item.id})" class="text-indigo-600 hover:text-indigo-900"><i class="fa-solid fa-pen-to-square"></i></button>
                            <button onclick="deleteAMDEC(${item.id})" class="text-red-500 hover:text-red-700"><i class="fa-solid fa-trash"></i></button>
                        </td>
                    </tr>
                `;
            });
        }

        // Open/Close Modals AMDEC
        function openAmdecModal(editId = null) {
            const selectEq = document.getElementById('amdecEquipement');
            selectEq.innerHTML = '';
            dataEquipements.forEach(eq => {
                selectEq.innerHTML += `<option value="${eq.id}">${eq.nom} (${eq.zone})</option>`;
            });

            if (editId) {
                const item = dataAmdec.find(a => a.id === editId);
                document.getElementById('modalAmdecTitle').textContent = "Modifier l'Analyse AMDEC";
                document.getElementById('amdecId').value = item.id;
                document.getElementById('amdecEquipement').value = item.equipementId;
                document.getElementById('amdecElement').value = item.element;
                document.getElementById('amdecMode').value = item.mode;
                document.getElementById('amdecEffet').value = item.effet;
                document.getElementById('amdecCause').value = item.cause;
                document.getElementById('amdecG').value = item.g;
                document.getElementById('amdecO').value = item.o;
                document.getElementById('amdecD').value = item.d;
                document.getElementById('amdecAction').value = item.action;
            } else {
                document.getElementById('modalAmdecTitle').textContent = "Nouvelle Analyse AMDEC";
                document.getElementById('formAmdec').reset();
                document.getElementById('amdecId').value = '';
            }
            calcNPR();
            document.getElementById('modalAmdec').classList.remove('hidden');
        }

        function closeAmdecModal() {
            document.getElementById('modalAmdec').classList.add('hidden');
        }

        // Save AMDEC Item
        function saveAMDEC(e) {
            e.preventDefault();
            const id = document.getElementById('amdecId').value;
            const g = parseInt(document.getElementById('amdecG').value);
            const o = parseInt(document.getElementById('amdecO').value);
            const d = parseInt(document.getElementById('amdecD').value);
            const npr = g * o * d;

            const amdecObj = {
                id: id ? parseInt(id) : Date.now(),
                equipementId: parseInt(document.getElementById('amdecEquipement').value),
                element: document.getElementById('amdecElement').value,
                mode: document.getElementById('amdecMode').value,
                effet: document.getElementById('amdecEffet').value,
                cause: document.getElementById('amdecCause').value,
                g, o, d, npr,
                criticite: calculateCriticite(npr).label,
                action: document.getElementById('amdecAction').value
            };

            if (id) {
                const index = dataAmdec.findIndex(a => a.id === parseInt(id));
                dataAmdec[index] = amdecObj;
            } else {
                dataAmdec.push(amdecObj);
            }

            saveDataLocally();
            closeAmdecModal();
            renderAMDEC();
        }

        function editAMDEC(id) {
            openAmdecModal(id);
        }

        function deleteAMDEC(id) {
            if (confirm("Voulez-vous vraiment supprimer cette ligne AMDEC ?")) {
                dataAmdec = dataAmdec.filter(a => a.id !== id);
                saveDataLocally();
                renderAMDEC();
            }
        }

        // Equipements Management
        function renderEquipements() {
            const grid = document.getElementById('grid-equipements');
            grid.innerHTML = '';

            dataEquipements.forEach(eq => {
                const countAmdec = dataAmdec.filter(a => a.equipementId === eq.id).length;
                grid.innerHTML += `
                    <div class="bg-white p-5 rounded-lg shadow border border-slate-200 flex justify-between items-start">
                        <div>
                            <h3 class="font-bold text-slate-800 text-base">${eq.nom}</h3>
                            <p class="text-xs text-slate-500 mt-1"><i class="fa-solid fa-location-dot"></i> ${eq.zone}</p>
                            <span class="inline-block mt-3 bg-indigo-50 text-indigo-700 text-xs px-2.5 py-1 rounded-full font-medium">
                                ${countAmdec} Analyse(s) AMDEC
                            </span>
                        </div>
                        <button onclick="deleteEquipement(${eq.id})" class="text-slate-400 hover:text-red-500"><i class="fa-solid fa-trash"></i></button>
                    </div>
                `;
            });
        }

        function openEquipementModal() {
            document.getElementById('formEquipement').reset();
            document.getElementById('modalEquipement').classList.remove('hidden');
        }

        function closeEquipementModal() {
            document.getElementById('modalEquipement').classList.add('hidden');
        }

        function saveEquipement(e) {
            e.preventDefault();
            const newEq = {
                id: Date.now(),
                nom: document.getElementById('eqNom').value,
                zone: document.getElementById('eqZone').value
            };
            dataEquipements.push(newEq);
            saveDataLocally();
            closeEquipementModal();
            renderEquipements();
        }

        function deleteEquipement(id) {
            if (confirm("Supprimer cet équipement et ses analyses associées ?")) {
                dataEquipements = dataEquipements.filter(e => e.id !== id);
                dataAmdec = dataAmdec.filter(a => a.equipementId !== id);
                saveDataLocally();
                renderEquipements();
                renderAMDEC();
            }
        }

        // Pareto Chart Implementation
        function initParetoChart() {
            const ctx = document.getElementById('paretoChart').getContext('2d');
            paretoChartInstance = new Chart(ctx, {
                type: 'bar',
                data: { labels: [], datasets: [] },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    scales: {
                        y: { beginAtZero: true, title: { display: true, text: 'NPR Cumulé' } }
                    }
                }
            });
        }

        function updateParetoChart() {
            const type = document.getElementById('paretoFilterType').value;
            let groupData = {};

            dataAmdec.forEach(item => {
                const eq = dataEquipements.find(e => e.id === item.equipementId);
                let key = "Inconnu";
                if (eq) {
                    key = type === 'equipement' ? eq.nom : eq.zone;
                }
                groupData[key] = (groupData[key] || 0) + item.npr;
            });

            // Sort descending
            const sortedKeys = Object.keys(groupData).sort((a, b) => groupData[b] - groupData[a]);
            const sortedValues = sortedKeys.map(k => groupData[k]);

            paretoChartInstance.data = {
                labels: sortedKeys,
                datasets: [{
                    label: 'NPR Total (Criticité)',
                    data: sortedValues,
                    backgroundColor: '#6366f1'
                }]
            };
            paretoChartInstance.update();
        }

        // Import & Export Data
        function exportJSON() {
            const fullData = { equipements: dataEquipements, amdec: dataAmdec };
            const blob = new Blob([JSON.stringify(fullData, null, 2)], { type: 'application/json' });
            const a = document.createElement('a');
            a.href = URL.createObjectURL(blob);
            a.download = `AMDEC_PRO_DATA_${new Date().toISOString().slice(0, 10)}.json`;
            a.click();
        }

        function importJSON(event) {
            const file = event.target.files[0];
            if (!file) return;
            const reader = new FileReader();
            reader.onload = function(e) {
                try {
                    const parsed = JSON.parse(e.target.result);
                    if (parsed.equipements && parsed.amdec) {
                        dataEquipements = parsed.equipements;
                        dataAmdec = parsed.amdec;
                        saveDataLocally();
                        renderDashboard();
                        renderAMDEC();
                        renderEquipements();
                        alert("Données importées avec succès !");
                    } else {
                        alert("Format JSON non valide pour AMDEC PRO.");
                    }
                } catch (err) {
                    alert("Erreur lors de la lecture du fichier JSON.");
                }
            };
            reader.readAsText(file);
        }

        function exportCSV() {
            let csv = "Equipement;Zone;Element;Mode_Defaillance;Effet;Cause;G;O;D;NPR;Criticite;Action\n";
            dataAmdec.forEach(a => {
                const eq = dataEquipements.find(e => e.id === a.equipementId);
                csv += `"${eq ? eq.nom : ''}";"${eq ? eq.zone : ''}";"${a.element}";"${a.mode}";"${a.effet}";"${a.cause}";${a.g};${a.o};${a.d};${a.npr};"${a.criticite}";"${a.action}"\n`;
            });

            const blob = new Blob([csv], { type: 'text/csv;charset=utf-8;' });
            const a = document.createElement('a');
            a.href = URL.createObjectURL(blob);
            a.download = `AMDEC_PRO_EXPORT_${new Date().toISOString().slice(0, 10)}.csv`;
            a.click();
        }
    </script>
</body>
</html>
