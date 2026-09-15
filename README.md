<!DOCTYPE html>
<html lang="fr" class="h-full bg-slate-100">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>PLATEFORME INDUSTRIELLE INTEGRÉE - GMAO | AMDEC | DMAIC | KPIs</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome CDN -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Chart.js CDN -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    
    <style>
        .crit-faible { background-color: #dcfce7; color: #15803d; }
        .crit-moyen { background-color: #fef9c3; color: #a16207; }
        .crit-eleve { background-color: #ffedd5; color: #c2410c; }
        .crit-critique { background-color: #fee2e2; color: #b91c1c; }
    </style>
</head>
<body class="flex flex-col min-h-full font-sans text-slate-800">

<!-- PAGE DE CONNEXION (AUTHENTIFICATION) -->
<div id="auth-screen" class="fixed inset-0 bg-slate-900 z-50 flex items-center justify-center p-4">
    <div class="bg-white rounded-xl shadow-2xl max-w-md w-full p-8 border border-slate-700">
        <div class="text-center mb-8">
            <div class="inline-flex items-center justify-center w-16 h-16 bg-indigo-100 text-indigo-600 rounded-full mb-4">
                <i class="fa-solid fa-industry text-3xl"></i>
            </div>
            <h2 class="text-2xl font-bold text-slate-900">Plateforme Industrielle</h2>
            <p class="text-sm text-slate-500 mt-1">GMAO • AMDEC • DMAIC • KPIs</p>
        </div>

        <form onsubmit="handleLogin(event)" class="space-y-4">
            <div>
                <label class="block text-xs font-bold text-slate-700 uppercase mb-1">Adresse Email</label>
                <div class="relative">
                    <i class="fa-solid fa-envelope absolute left-3 top-3 text-slate-400"></i>
                    <input type="email" id="loginEmail" required value="admin@usine.com" class="w-full pl-10 pr-4 py-2 border rounded-lg text-sm focus:ring-2 focus:ring-indigo-500 outline-none">
                </div>
            </div>

            <div>
                <label class="block text-xs font-bold text-slate-700 uppercase mb-1">Mot de passe</label>
                <div class="relative">
                    <i class="fa-solid fa-lock absolute left-3 top-3 text-slate-400"></i>
                    <input type="password" id="loginPassword" required value="123456" class="w-full pl-10 pr-4 py-2 border rounded-lg text-sm focus:ring-2 focus:ring-indigo-500 outline-none">
                </div>
            </div>

            <div>
                <label class="block text-xs font-bold text-slate-700 uppercase mb-1">Rôle de Connexion</label>
                <select id="loginRole" class="w-full border rounded-lg p-2 text-sm outline-none focus:ring-2 focus:ring-indigo-500 bg-white">
                    <option value="Admin">Administrateur / Responsable Maintenance</option>
                    <option value="Technicien">Technicien GMAO</option>
                    <option value="Qualite">Ingénieur Amélioration Continue / Qualité</option>
                </select>
            </div>

            <button type="submit" class="w-full bg-indigo-600 hover:bg-indigo-700 text-white font-semibold py-2.5 rounded-lg shadow-lg transition duration-200">
                <i class="fa-solid fa-right-to-bracket mr-2"></i> Se Connecter
            </button>
        </form>
    </div>
</div>

<!-- APPLICATION PRINCIPALE -->
<div id="app-screen" class="hidden flex flex-col min-h-screen">
    
    <!-- Navbar -->
    <header class="bg-slate-900 text-white shadow-md sticky top-0 z-40">
        <div class="max-w-7xl mx-auto px-4 py-3 flex flex-wrap justify-between items-center gap-4">
            <div class="flex items-center gap-3">
                <i class="fa-solid fa-gears text-2xl text-indigo-400"></i>
                <h1 class="text-xl font-bold tracking-wide">SMART MAINTENANCE <span class="text-xs bg-indigo-600 px-2 py-0.5 rounded text-white font-normal">PRO</span></h1>
            </div>
            
            <div class="flex items-center gap-4">
                <div class="text-right hidden sm:block">
                    <div class="text-xs text-slate-400">Utilisateur connecté</div>
                    <div class="text-sm font-semibold text-indigo-300" id="userDisplay">Admin</div>
                </div>
                <button onclick="handleLogout()" class="bg-red-600/20 hover:bg-red-600 text-red-300 hover:text-white px-3 py-1.5 rounded text-sm transition border border-red-500/30">
                    <i class="fa-solid fa-power-off mr-1"></i> Déconnexion
                </button>
            </div>
        </div>
    </header>

    <!-- Content Area -->
    <main class="max-w-7xl mx-auto px-4 py-6 flex-grow w-full space-y-6">

        <!-- Navigation Tabs -->
        <div class="flex border-b border-slate-300 gap-2 overflow-x-auto bg-white p-2 rounded-t-lg shadow-sm">
            <button onclick="switchTab('kpi')" id="tab-kpi" class="tab-btn px-4 py-2 font-semibold text-indigo-600 border-b-2 border-indigo-600 flex items-center gap-2">
                <i class="fa-solid fa-chart-line"></i> KPIs Performance
            </button>
            <button onclick="switchTab('gmao')" id="tab-gmao" class="tab-btn px-4 py-2 font-semibold text-slate-600 border-b-2 border-transparent hover:text-indigo-600 flex items-center gap-2">
                <i class="fa-solid fa-wrench"></i> GMAO (Ordres de Travail)
            </button>
            <button onclick="switchTab('amdec')" id="tab-amdec" class="tab-btn px-4 py-2 font-semibold text-slate-600 border-b-2 border-transparent hover:text-indigo-600 flex items-center gap-2">
                <i class="fa-solid fa-triangle-exclamation"></i> AMDEC & Risques
            </button>
            <button onclick="switchTab('dmaic')" id="tab-dmaic" class="tab-btn px-4 py-2 font-semibold text-slate-600 border-b-2 border-transparent hover:text-indigo-600 flex items-center gap-2">
                <i class="fa-solid fa-diagram-project"></i> Projets DMAIC
            </button>
        </div>

        <!-- SECTION 1: INDICATEURS DE PERFORMANCE (KPIs) -->
        <section id="sec-kpi" class="space-y-6">
            <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200">
                    <div class="text-xs font-bold text-slate-400 uppercase">Taux de Rendement Synthétique (TRS)</div>
                    <div class="text-3xl font-extrabold text-emerald-600 mt-2">84.5 %</div>
                    <div class="text-xs text-slate-500 mt-1"><i class="fa-solid fa-arrow-up text-emerald-500"></i> +2.1% ce mois</div>
                </div>
                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200">
                    <div class="text-xs font-bold text-slate-400 uppercase">MTBF (Temps Moyen Entre Pannes)</div>
                    <div class="text-3xl font-extrabold text-indigo-600 mt-2">142 hrs</div>
                    <div class="text-xs text-slate-500 mt-1">Objectif: > 120 hrs</div>
                </div>
                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200">
                    <div class="text-xs font-bold text-slate-400 uppercase">MTTR (Temps Moyen de Réparation)</div>
                    <div class="text-3xl font-extrabold text-amber-600 mt-2">1.8 hrs</div>
                    <div class="text-xs text-slate-500 mt-1">Objectif: < 2.0 hrs</div>
                </div>
                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200">
                    <div class="text-xs font-bold text-slate-400 uppercase">Disponibilité Équipements</div>
                    <div class="text-3xl font-extrabold text-blue-600 mt-2">98.7 %</div>
                    <div class="text-xs text-slate-500 mt-1">Disponibilité Opérationnelle</div>
                </div>
            </div>

            <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200">
                    <h3 class="font-bold text-slate-800 mb-4">Évolution de la Disponibilité Machine (%)</h3>
                    <div class="h-64"><canvas id="kpiDispChart"></canvas></div>
                </div>
                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200">
                    <h3 class="font-bold text-slate-800 mb-4">Répartition du Temps d'Arrêt (Par Cause)</h3>
                    <div class="h-64"><canvas id="kpiArretChart"></canvas></div>
                </div>
            </div>
        </section>

        <!-- SECTION 2: GMAO (ORDRES DE TRAVAIL) -->
        <section id="sec-gmao" class="hidden space-y-6">
            <div class="bg-white p-4 rounded-xl shadow-sm border border-slate-200 flex justify-between items-center">
                <h2 class="text-lg font-bold text-slate-800"><i class="fa-solid fa-list-check text-indigo-600 mr-2"></i>Ordres de Travail (OT)</h2>
                <button onclick="openOtModal()" class="bg-indigo-600 hover:bg-indigo-700 text-white px-4 py-2 rounded-lg text-sm font-semibold shadow">
                    <i class="fa-solid fa-plus mr-1"></i> Créer un OT
                </button>
            </div>

            <div class="bg-white rounded-xl shadow-sm border border-slate-200 overflow-hidden">
                <table class="w-full text-left border-collapse text-sm">
                    <thead class="bg-slate-800 text-white uppercase text-xs">
                        <tr>
                            <th class="p-3">N° OT</th>
                            <th class="p-3">Équipement</th>
                            <th class="p-3">Type</th>
                            <th class="p-3">Description</th>
                            <th class="p-3">Statut</th>
                            <th class="p-3">Priorité</th>
                        </tr>
                    </thead>
                    <tbody id="table-ot-body" class="divide-y divide-slate-200"></tbody>
                </table>
            </div>
        </section>

        <!-- SECTION 3: AMDEC -->
        <section id="sec-amdec" class="hidden space-y-6">
            <div class="bg-white p-4 rounded-xl shadow-sm border border-slate-200 flex justify-between items-center">
                <h2 class="text-lg font-bold text-slate-800"><i class="fa-solid fa-shield-halved text-indigo-600 mr-2"></i>Analyse des Risques (AMDEC)</h2>
                <button onclick="openAmdecModal()" class="bg-indigo-600 hover:bg-indigo-700 text-white px-4 py-2 rounded-lg text-sm font-semibold shadow">
                    <i class="fa-solid fa-plus mr-1"></i> Nouvelle Fiche AMDEC
                </button>
            </div>

            <div class="bg-white rounded-xl shadow-sm border border-slate-200 overflow-hidden">
                <table class="w-full text-left border-collapse text-sm">
                    <thead class="bg-slate-800 text-white uppercase text-xs">
                        <tr>
                            <th class="p-3">Équipement</th>
                            <th class="p-3">Mode Défaillance</th>
                            <th class="p-3 text-center">G</th>
                            <th class="p-3 text-center">O</th>
                            <th class="p-3 text-center">D</th>
                            <th class="p-3 text-center">NPR</th>
                            <th class="p-3 text-center">Criticité</th>
                            <th class="p-3">Action Préconisée</th>
                        </tr>
                    </thead>
                    <tbody id="table-amdec-body" class="divide-y divide-slate-200"></tbody>
                </table>
            </div>
        </section>

        <!-- SECTION 4: DMAIC (AMÉLIORATION CONTINUE) -->
        <section id="sec-dmaic" class="hidden space-y-6">
            <div class="bg-white p-4 rounded-xl shadow-sm border border-slate-200 flex justify-between items-center">
                <h2 class="text-lg font-bold text-slate-800"><i class="fa-solid fa-diagram-project text-indigo-600 mr-2"></i>Projets d'Amélioration DMAIC</h2>
                <button onclick="openDmaicModal()" class="bg-indigo-600 hover:bg-indigo-700 text-white px-4 py-2 rounded-lg text-sm font-semibold shadow">
                    <i class="fa-solid fa-plus mr-1"></i> Nouveau Projet DMAIC
                </button>
            </div>

            <div class="grid grid-cols-1 md:grid-cols-2 gap-6" id="dmaic-projects-container"></div>
        </section>

    </main>
</div>

<!-- MODAL CRÉATION OT GMAO -->
<div id="modalOt" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm hidden z-50 flex justify-center items-center p-4">
    <div class="bg-white rounded-xl shadow-xl w-full max-w-md overflow-hidden">
        <div class="bg-slate-900 text-white px-6 py-4 flex justify-between items-center">
            <h3 class="font-bold">Nouveau Ordre de Travail (OT)</h3>
            <button onclick="closeOtModal()" class="text-slate-400 hover:text-white"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <form onsubmit="saveOt(event)" class="p-6 space-y-4">
            <div>
                <label class="block text-xs font-bold text-slate-700 uppercase mb-1">Équipement</label>
                <input type="text" id="otEquipement" required class="w-full border rounded p-2 text-sm outline-none">
            </div>
            <div>
                <label class="block text-xs font-bold text-slate-700 uppercase mb-1">Type d'intervention</label>
                <select id="otType" class="w-full border rounded p-2 text-sm outline-none">
                    <option value="Correctif">Correctif (Panne)</option>
                    <option value="Préventif">Préventif (Planifié)</option>
                    <option value="Amélioratif">Amélioratif (DMAIC)</option>
                </select>
            </div>
            <div>
                <label class="block text-xs font-bold text-slate-700 uppercase mb-1">Description du problème / travail</label>
                <textarea id="otDesc" required rows="2" class="w-full border rounded p-2 text-sm outline-none"></textarea>
            </div>
            <div>
                <label class="block text-xs font-bold text-slate-700 uppercase mb-1">Priorité</label>
                <select id="otPriorite" class="w-full border rounded p-2 text-sm outline-none">
                    <option value="Haute">Haute (Urgent)</option>
                    <option value="Moyenne" selected>Moyenne</option>
                    <option value="Basse">Basse</option>
                </select>
            </div>
            <div class="flex justify-end gap-3 pt-4 border-t">
                <button type="button" onclick="closeOtModal()" class="px-4 py-2 border rounded text-sm font-semibold text-slate-600">Annuler</button>
                <button type="submit" class="px-5 py-2 bg-indigo-600 text-white rounded text-sm font-semibold">Créer OT</button>
            </div>
        </form>
    </div>
</div>

<!-- MODAL CRÉATION PROJET DMAIC -->
<div id="modalDmaic" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm hidden z-50 flex justify-center items-center p-4">
    <div class="bg-white rounded-xl shadow-xl w-full max-w-lg overflow-hidden">
        <div class="bg-slate-900 text-white px-6 py-4 flex justify-between items-center">
            <h3 class="font-bold">Créer un Projet DMAIC</h3>
            <button onclick="closeDmaicModal()" class="text-slate-400 hover:text-white"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <form onsubmit="saveDmaic(event)" class="p-6 space-y-4">
            <div>
                <label class="block text-xs font-bold text-slate-700 uppercase mb-1">Titre du Projet</label>
                <input type="text" id="dmaicTitre" required placeholder="ex: Réduction du MTTR sur Ligne 1" class="w-full border rounded p-2 text-sm outline-none">
            </div>
            <div>
                <label class="block text-xs font-bold text-slate-700 uppercase mb-1">Équipement / Zone Cible</label>
                <input type="text" id="dmaicCible" required placeholder="ex: Presse Hydraulique P-200" class="w-full border rounded p-2 text-sm outline-none">
            </div>
            <div>
                <label class="block text-xs font-bold text-slate-700 uppercase mb-1">Étape Actuelle (DMAIC)</label>
                <select id="dmaicEtape" class="w-full border rounded p-2 text-sm outline-none">
                    <option value="D">D - Définir</option>
                    <option value="M">M - Mesurer</option>
                    <option value="A">A - Analyser</option>
                    <option value="I">I - Innover/Améliorer</option>
                    <option value="C">C - Contrôler</option>
                </select>
            </div>
            <div>
                <label class="block text-xs font-bold text-slate-700 uppercase mb-1">Objectif chiffré</label>
                <input type="text" id="dmaicObjectif" required placeholder="ex: Réduire les arrêts de 30%" class="w-full border rounded p-2 text-sm outline-none">
            </div>
            <div class="flex justify-end gap-3 pt-4 border-t">
                <button type="button" onclick="closeDmaicModal()" class="px-4 py-2 border rounded text-sm font-semibold text-slate-600">Annuler</button>
                <button type="submit" class="px-5 py-2 bg-indigo-600 text-white rounded text-sm font-semibold">Enregistrer Projet</button>
            </div>
        </form>
    </div>
</div>

<!-- SCRIPTS JAVASCRIPT -->
<script>
    // Stockage global en mémoire (sauvegardé dans localStorage)
    let currentUser = null;

    let dataOt = JSON.parse(localStorage.getItem('DATA_OT')) || [
        { id: 'OT-1001', equipement: 'Pompe Hydraulique P-01', type: 'Correctif', desc: 'Remplacement joint fuite huile', statut: 'En cours', priorite: 'Haute' },
        { id: 'OT-1002', equipement: 'Moteur Principal M-02', type: 'Préventif', desc: 'Graissage roulements mensuel', statut: 'Planifié', priorite: 'Moyenne' }
    ];

    let dataAmdec = JSON.parse(localStorage.getItem('DATA_AMDEC')) || [
        { equipement: 'Pompe P-01', mode: 'Fuite d\'huile', g: 6, o: 5, d: 4, npr: 120, action: 'Changement préventif joints' },
        { equipement: 'Moteur M-02', mode: 'Grippage roulement', g: 8, o: 3, d: 3, npr: 72, action: 'Plan de graissage hebdomadaire' }
    ];

    let dataDmaic = JSON.parse(localStorage.getItem('DATA_DMAIC')) || [
        { id: 1, titre: 'Optimisation de la disponibilité Extrudeuse', cible: 'Extrudeuse B-02', etape: 'A', objectif: 'Gain de 5% de TRS', progression: 50 },
        { id: 2, titre: 'Réduction du temps de micro-arrêts', cible: 'Ligne d\'emballage', etape: 'I', objectif: '-20% d\'arrêt non planifié', progression: 75 }
    ];

    document.addEventListener("DOMContentLoaded", () => {
        checkSession();
        initKpiCharts();
        renderOt();
        renderAmdec();
        renderDmaic();
    });

    // --- Authentification ---
    function handleLogin(e) {
        e.preventDefault();
        const role = document.getElementById('loginRole').value;
        currentUser = { email: document.getElementById('loginEmail').value, role: role };
        localStorage.setItem('SESSION_USER', JSON.stringify(currentUser));
        checkSession();
    }

    function handleLogout() {
        localStorage.removeItem('SESSION_USER');
        currentUser = null;
        checkSession();
    }

    function checkSession() {
        const stored = localStorage.getItem('SESSION_USER');
        if (stored) {
            currentUser = JSON.parse(stored);
            document.getElementById('auth-screen').classList.add('hidden');
            document.getElementById('app-screen').classList.remove('hidden');
            document.getElementById('userDisplay').textContent = `${currentUser.email} (${currentUser.role})`;
        } else {
            document.getElementById('auth-screen').classList.remove('hidden');
            document.getElementById('app-screen').classList.add('hidden');
        }
    }

    // --- Navigation ---
    function switchTab(tab) {
        ['kpi', 'gmao', 'amdec', 'dmaic'].forEach(t => {
            document.getElementById(`sec-${t}`).classList.add('hidden');
            document.getElementById(`tab-${t}`).classList.remove('text-indigo-600', 'border-indigo-600');
            document.getElementById(`tab-${t}`).classList.add('text-slate-600', 'border-transparent');
        });

        document.getElementById(`sec-${tab}`).classList.remove('hidden');
        document.getElementById(`tab-${tab}`).classList.add('text-indigo-600', 'border-indigo-600');
        document.getElementById(`tab-${tab}`).classList.remove('text-slate-600', 'border-transparent');
    }

    // --- GMAO ---
    function renderOt() {
        const tbody = document.getElementById('table-ot-body');
        tbody.innerHTML = '';
        dataOt.forEach(ot => {
            tbody.innerHTML += `
                <tr class="hover:bg-slate-50">
                    <td class="p-3 font-bold text-slate-800">${ot.id}</td>
                    <td class="p-3 font-semibold">${ot.equipement}</td>
                    <td class="p-3"><span class="px-2 py-0.5 rounded text-xs ${ot.type === 'Correctif' ? 'bg-red-100 text-red-700' : 'bg-blue-100 text-blue-700'}">${ot.type}</span></td>
                    <td class="p-3 text-slate-600">${ot.desc}</td>
                    <td class="p-3"><span class="px-2 py-0.5 rounded text-xs font-bold bg-amber-100 text-amber-800">${ot.statut}</span></td>
                    <td class="p-3 font-bold text-xs ${ot.priorite === 'Haute' ? 'text-red-600' : 'text-slate-600'}">${ot.priorite}</td>
                </tr>
            `;
        });
    }

    function openOtModal() { document.getElementById('modalOt').classList.remove('hidden'); }
    function closeOtModal() { document.getElementById('modalOt').classList.add('hidden'); }

    function saveOt(e) {
        e.preventDefault();
        const newOt = {
            id: 'OT-' + (1000 + dataOt.length + 1),
            equipement: document.getElementById('otEquipement').value,
            type: document.getElementById('otType').value,
            desc: document.getElementById('otDesc').value,
            statut: 'Planifié',
            priorite: document.getElementById('otPriorite').value
        };
        dataOt.push(newOt);
        localStorage.setItem('DATA_OT', JSON.stringify(dataOt));
        renderOt();
        closeOtModal();
    }

    // --- AMDEC ---
    function renderAmdec() {
        const tbody = document.getElementById('table-amdec-body');
        tbody.innerHTML = '';
        dataAmdec.forEach(item => {
            const critClass = item.npr >= 100 ? 'crit-critique' : (item.npr >= 70 ? 'crit-eleve' : 'crit-faible');
            const critLabel = item.npr >= 100 ? 'Critique' : (item.npr >= 70 ? 'Élevé' : 'Faible');
            tbody.innerHTML += `
                <tr class="hover:bg-slate-50">
                    <td class="p-3 font-semibold text-slate-800">${item.equipement}</td>
                    <td class="p-3">${item.mode}</td>
                    <td class="p-3 text-center">${item.g}</td>
                    <td class="p-3 text-center">${item.o}</td>
                    <td class="p-3 text-center">${item.d}</td>
                    <td class="p-3 text-center font-bold">${item.npr}</td>
                    <td class="p-3 text-center"><span class="px-2 py-0.5 rounded text-xs font-bold ${critClass}">${critLabel}</span></td>
                    <td class="p-3 text-slate-600">${item.action}</td>
                </tr>
            `;
        });
    }

    // --- DMAIC ---
    function renderDmaic() {
        const container = document.getElementById('dmaic-projects-container');
        container.innerHTML = '';
        
        const etapeLabels = { 'D': 'Définir', 'M': 'Mesurer', 'A': 'Analysier', 'I': 'Innover/Améliorer', 'C': 'Contrôler' };

        dataDmaic.forEach(p => {
            container.innerHTML += `
                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200 space-y-4">
                    <div class="flex justify-between items-start">
                        <div>
                            <span class="text-xs font-bold bg-indigo-100 text-indigo-700 px-2.5 py-1 rounded">Étape ${p.etape} - ${etapeLabels[p.etape]}</span>
                            <h3 class="font-bold text-slate-800 text-lg mt-2">${p.titre}</h3>
                            <p class="text-xs text-slate-500"><i class="fa-solid fa-cube"></i> Équipement: ${p.cible}</p>
                        </div>
                    </div>
                    
                    <div class="text-sm bg-slate-50 p-3 rounded border border-slate-100">
                        <strong class="text-slate-700">Objectif:</strong> ${p.objectif}
                    </div>

                    <div>
                        <div class="flex justify-between text-xs font-bold text-slate-600 mb-1">
                            <span>Avancement du Projet</span>
                            <span>${p.progression}%</span>
                        </div>
                        <div class="w-full bg-slate-200 h-2 rounded-full overflow-hidden">
                            <div class="bg-indigo-600 h-full" style="width: ${p.progression}%"></div>
                        </div>
                    </div>
                </div>
            `;
        });
    }

    function openDmaicModal() { document.getElementById('modalDmaic').classList.remove('hidden'); }
    function closeDmaicModal() { document.getElementById('modalDmaic').classList.add('hidden'); }

    function saveDmaic(e) {
        e.preventDefault();
        const newProj = {
            id: Date.now(),
            titre: document.getElementById('dmaicTitre').value,
            cible: document.getElementById('dmaicCible').value,
            etape: document.getElementById('dmaicEtape').value,
            objectif: document.getElementById('dmaicObjectif').value,
            progression: 20
        };
        dataDmaic.push(newProj);
        localStorage.setItem('DATA_DMAIC', JSON.stringify(dataDmaic));
        renderDmaic();
        closeDmaicModal();
    }

    // --- Chart.js Graphs (KPIs) ---
    function initKpiCharts() {
        // Graphique Disponibilité
        new Chart(document.getElementById('kpiDispChart').getContext('2d'), {
            type: 'line',
            data: {
                labels: ['Jan', 'Fév', 'Mar', 'Avr', 'Mai', 'Juin'],
                datasets: [{
                    label: 'Taux de Disponibilité (%)',
                    data: [94.2, 95.8, 93.5, 96.1, 97.4, 98.7],
                    borderColor: '#4f46e5',
                    backgroundColor: 'rgba(79, 70, 229, 0.1)',
                    fill: true,
                    tension: 0.3
                }]
            },
            options: { responsive: true, maintainAspectRatio: false }
        });

        // Graphique Répartition Arrêts
        new Chart(document.getElementById('kpiArretChart').getContext('2d'), {
            type: 'doughnut',
            data: {
                labels: ['Panne Mécanique', 'Défaut Électrique', 'Réglages/Changement d\'outil', 'Attente Matière'],
                datasets: [{
                    data: [40, 25, 20, 15],
                    backgroundColor: ['#ef4444', '#f59e0b', '#3b82f6', '#10b981']
                }]
            },
            options: { responsive: true, maintainAspectRatio: false }
        });
    }
</script>

</body>
</html>
