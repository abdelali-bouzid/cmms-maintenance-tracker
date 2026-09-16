<!DOCTYPE html>
<html lang="fr" class="h-full bg-slate-100">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>PLATEFORME INDUSTRIELLE INTÉGRÉE - GMAO | AMDEC | DMAIC | KPIs</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome CDN -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        body { font-family: system-ui, -apple-system, sans-serif; }
    </style>
</head>
<body class="flex flex-col min-h-full text-slate-800">

<!-- ÉCRAN DE CONNEXION -->
<div id="auth-screen" class="fixed inset-0 bg-slate-900 z-50 flex items-center justify-center p-4">
    <div class="bg-white rounded-xl shadow-2xl max-w-md w-full p-8 border border-slate-700">
        <div class="text-center mb-8">
            <div class="inline-flex items-center justify-center w-16 h-16 bg-indigo-100 text-indigo-600 rounded-full mb-4">
                <i class="fa-solid fa-shield-halved text-3xl"></i>
            </div>
            <h2 class="text-2xl font-bold text-slate-900">Plateforme de Maintenance</h2>
            <p class="text-sm text-slate-500 mt-1">GMAO • AMDEC • DMAIC • KPIs</p>
        </div>

        <div id="loginError" class="hidden mb-4 p-3 bg-red-100 border border-red-400 text-red-700 text-xs rounded-lg flex items-center gap-2">
            <i class="fa-solid fa-triangle-exclamation"></i>
            <span id="loginErrorText">Identifiants incorrects.</span>
        </div>

        <form onsubmit="handleLogin(event)" class="space-y-4" autocomplete="off">
            <div>
                <label class="block text-xs font-bold text-slate-700 uppercase mb-1">Adresse Email</label>
                <input type="email" id="loginEmail" required value="admin@usine.com" class="w-full px-4 py-2 border rounded-lg text-sm focus:ring-2 focus:ring-indigo-500 outline-none">
            </div>

            <div>
                <label class="block text-xs font-bold text-slate-700 uppercase mb-1">Mot de passe</label>
                <div class="relative">
                    <input type="password" id="loginPassword" required value="123456" class="w-full px-4 py-2 border rounded-lg text-sm focus:ring-2 focus:ring-indigo-500 outline-none pr-10">
                    <button type="button" onclick="togglePasswordVisibility()" class="absolute inset-y-0 right-0 pr-3 flex items-center text-slate-400 hover:text-indigo-600 focus:outline-none">
                        <i id="togglePasswordIcon" class="fa-solid fa-eye"></i>
                    </button>
                </div>
            </div>

            <div>
                <label class="block text-xs font-bold text-slate-700 uppercase mb-1">Rôle Utilisateur</label>
                <select id="loginRole" class="w-full border rounded-lg p-2 text-sm outline-none focus:ring-2 focus:ring-indigo-500 bg-white">
                    <option value="Admin">Responsable Maintenance / Admin</option>
                    <option value="Technicien">Technicien de Maintenance</option>
                    <option value="Qualite">Ingénieur Amélioration Continue / Qualité</option>
                </select>
            </div>

            <button type="submit" id="btnLogin" class="w-full bg-indigo-600 hover:bg-indigo-700 text-white font-semibold py-2.5 rounded-lg shadow-lg transition duration-200">
                <i class="fa-solid fa-right-to-bracket mr-2"></i> Se Connecter
            </button>
        </form>
    </div>
</div>

<!-- APPLICATION PRINCIPALE -->
<div id="app-screen" class="hidden flex flex-col min-h-screen">
    
    <!-- Navbar -->
    <header class="bg-slate-900 text-white shadow-md sticky top-0 z-40">
        <div class="max-w-7xl mx-auto px-4 py-3 flex justify-between items-center">
            <div class="flex items-center gap-3">
                <i class="fa-solid fa-gears text-2xl text-indigo-400"></i>
                <h1 class="text-xl font-bold tracking-wide">SMART MAINTENANCE <span class="text-xs bg-indigo-600 px-2 py-0.5 rounded text-white">PRO</span></h1>
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

    <!-- Contenu Principal -->
    <main class="max-w-7xl mx-auto px-4 py-6 flex-grow w-full space-y-6">

        <!-- Onglets -->
        <div class="flex border-b border-slate-300 gap-2 overflow-x-auto bg-white p-2 rounded-t-lg shadow-sm">
            <button onclick="switchTab('kpi')" id="tab-kpi" class="tab-btn px-4 py-2 font-semibold text-indigo-600 border-b-2 border-indigo-600 flex items-center gap-2">
                <i class="fa-solid fa-chart-line"></i> KPIs Performance
            </button>
            <button onclick="switchTab('gmao')" id="tab-gmao" class="tab-btn px-4 py-2 font-semibold text-slate-600 border-b-2 border-transparent hover:text-indigo-600 flex items-center gap-2">
                <i class="fa-solid fa-wrench"></i> GMAO (Ordres de Travail)
            </button>
            <button onclick="switchTab('amdec')" id="tab-amdec" class="tab-btn px-4 py-2 font-semibold text-slate-600 border-b-2 border-transparent hover:text-indigo-600 flex items-center gap-2">
                <i class="fa-solid fa-shield-halved"></i> Analyse AMDEC
            </button>
            <button onclick="switchTab('dmaic')" id="tab-dmaic" class="tab-btn px-4 py-2 font-semibold text-slate-600 border-b-2 border-transparent hover:text-indigo-600 flex items-center gap-2">
                <i class="fa-solid fa-diagram-project"></i> Projets DMAIC
            </button>
        </div>

        <!-- 1. SECTION KPIs -->
        <section id="sec-kpi" class="space-y-6">
            <div class="bg-white p-4 rounded-xl shadow-sm border border-slate-200 flex flex-wrap justify-between items-center gap-4">
                <div class="flex items-center gap-2">
                    <i class="fa-solid fa-industry text-indigo-600 text-lg"></i>
                    <label class="font-bold text-slate-700 text-sm">Sélectionner un équipement :</label>
                </div>
                <select id="selectMachineKpi" onchange="updateKpiDashboard()" class="border rounded-lg p-2 text-sm bg-slate-50 border-slate-300 outline-none focus:ring-2 focus:ring-indigo-500">
                    <option value="ALL">Toutes les machines (Total Usine)</option>
                    <option value="Presse Hydraulique P-01">Presse Hydraulique P-01</option>
                    <option value="Moteur Principal M-02">Moteur Principal M-02</option>
                    <option value="Ligne d'Extrusion B-02">Ligne d'Extrusion B-02</option>
                </select>
            </div>

            <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200">
                    <div class="text-xs font-bold text-slate-400 uppercase">MTBF (Temps Moyen Entre Pannes)</div>
                    <div class="text-3xl font-extrabold text-indigo-600 mt-2" id="kpiMtbf">-- hrs</div>
                </div>
                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200">
                    <div class="text-xs font-bold text-slate-400 uppercase">MTTR (Temps Moyen de Réparation)</div>
                    <div class="text-3xl font-extrabold text-amber-600 mt-2" id="kpiMttr">-- hrs</div>
                </div>
                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200">
                    <div class="text-xs font-bold text-slate-400 uppercase">Disponibilité Opérationnelle</div>
                    <div class="text-3xl font-extrabold text-blue-600 mt-2" id="kpiDisp">-- %</div>
                </div>
                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200">
                    <div class="text-xs font-bold text-slate-400 uppercase">Nombre de Pannes</div>
                    <div class="text-3xl font-extrabold text-red-600 mt-2" id="kpiPannes">--</div>
                </div>
            </div>
        </section>

        <!-- 2. SECTION GMAO -->
        <section id="sec-gmao" class="hidden space-y-6">
            <div class="bg-white p-4 rounded-xl shadow-sm border border-slate-200 flex justify-between items-center">
                <h2 class="text-lg font-bold text-slate-800"><i class="fa-solid fa-list-check text-indigo-600 mr-2"></i>Ordres de Travail (OT)</h2>
                <button onclick="openOtModal()" class="bg-indigo-600 hover:bg-indigo-700 text-white px-4 py-2 rounded-lg text-sm font-semibold shadow">
                    <i class="fa-solid fa-plus mr-1"></i> Ajouter un OT
                </button>
            </div>

            <div class="bg-white rounded-xl shadow-sm border border-slate-200 overflow-x-auto">
                <table class="w-full text-left border-collapse text-sm">
                    <thead class="bg-slate-800 text-white uppercase text-xs">
                        <tr>
                            <th class="p-3">N° OT</th>
                            <th class="p-3">Équipement</th>
                            <th class="p-3">Type</th>
                            <th class="p-3">Durée d'arrêt (h)</th>
                            <th class="p-3">Description</th>
                            <th class="p-3">Priorité</th>
                            <th class="p-3 text-center">Actions</th>
                        </tr>
                    </thead>
                    <tbody id="table-ot-body" class="divide-y divide-slate-200"></tbody>
                </table>
            </div>
        </section>

        <!-- 3. SECTION AMDEC -->
        <section id="sec-amdec" class="hidden space-y-6">
            <div class="bg-white p-4 rounded-xl shadow-sm border border-slate-200 flex justify-between items-center">
                <h2 class="text-lg font-bold text-slate-800"><i class="fa-solid fa-shield-halved text-indigo-600 mr-2"></i>Analyse AMDEC</h2>
                <button onclick="openAmdecModal()" class="bg-indigo-600 hover:bg-indigo-700 text-white px-4 py-2 rounded-lg text-sm font-semibold shadow">
                    <i class="fa-solid fa-plus mr-1"></i> Ajouter une ligne AMDEC
                </button>
            </div>
            <div class="bg-white rounded-xl shadow-sm border border-slate-200 overflow-x-auto">
                <table class="w-full text-left border-collapse text-sm">
                    <thead class="bg-slate-800 text-white uppercase text-xs">
                        <tr>
                            <th class="p-3">Équipement</th>
                            <th class="p-3">Mode de Défaillance</th>
                            <th class="p-3 text-center">G</th>
                            <th class="p-3 text-center">O</th>
                            <th class="p-3 text-center">D</th>
                            <th class="p-3 text-center">NPR</th>
                            <th class="p-3">Action Préconisée</th>
                            <th class="p-3 text-center">Actions</th>
                        </tr>
                    </thead>
                    <tbody id="table-amdec-body" class="divide-y divide-slate-200"></tbody>
                </table>
            </div>
        </section>

        <!-- 4. SECTION DMAIC -->
        <section id="sec-dmaic" class="hidden space-y-6">
            <div class="bg-white p-4 rounded-xl shadow-sm border border-slate-200 flex justify-between items-center">
                <h2 class="text-lg font-bold text-slate-800"><i class="fa-solid fa-diagram-project text-indigo-600 mr-2"></i>Projets DMAIC</h2>
                <button onclick="openDmaicModal()" class="bg-indigo-600 hover:bg-indigo-700 text-white px-4 py-2 rounded-lg text-sm font-semibold shadow">
                    <i class="fa-solid fa-plus mr-1"></i> Ajouter un Projet
                </button>
            </div>
            <div class="grid grid-cols-1 md:grid-cols-2 gap-6" id="dmaic-projects-container"></div>
        </section>

    </main>
</div>

<!-- MODAL OT (Ajout / Édition) -->
<div id="modalOt" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm hidden z-50 flex justify-center items-center p-4">
    <div class="bg-white rounded-xl shadow-xl w-full max-w-md overflow-hidden">
        <div class="bg-slate-900 text-white px-6 py-4 flex justify-between items-center">
            <h3 class="font-bold" id="modalOtTitle">Gérer un OT</h3>
            <button onclick="closeOtModal()" class="text-slate-400 hover:text-white"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <form onsubmit="saveOt(event)" class="p-6 space-y-4">
            <input type="hidden" id="otIndex">
            <div>
                <label class="block text-xs font-bold text-slate-700 uppercase mb-1">Équipement</label>
                <select id="otEquipement" class="w-full border rounded p-2 text-sm outline-none">
                    <option value="Presse Hydraulique P-01">Presse Hydraulique P-01</option>
                    <option value="Moteur Principal M-02">Moteur Principal M-02</option>
                    <option value="Ligne d'Extrusion B-02">Ligne d'Extrusion B-02</option>
                </select>
            </div>
            <div>
                <label class="block text-xs font-bold text-slate-700 uppercase mb-1">Type d'intervention</label>
                <select id="otType" class="w-full border rounded p-2 text-sm outline-none">
                    <option value="Correctif">Correctif</option>
                    <option value="Préventif">Préventif</option>
                </select>
            </div>
            <div>
                <label class="block text-xs font-bold text-slate-700 uppercase mb-1">Durée (heures)</label>
                <input type="number" step="0.1" id="otDuree" required class="w-full border rounded p-2 text-sm outline-none">
            </div>
            <div>
                <label class="block text-xs font-bold text-slate-700 uppercase mb-1">Description</label>
                <textarea id="otDesc" required rows="2" class="w-full border rounded p-2 text-sm outline-none"></textarea>
            </div>
            <div>
                <label class="block text-xs font-bold text-slate-700 uppercase mb-1">Priorité</label>
                <select id="otPriorite" class="w-full border rounded p-2 text-sm outline-none">
                    <option value="Haute">Haute</option>
                    <option value="Moyenne">Moyenne</option>
                    <option value="Basse">Basse</option>
                </select>
            </div>
            <div class="flex justify-end gap-3 pt-4 border-t">
                <button type="button" onclick="closeOtModal()" class="px-4 py-2 border rounded text-sm font-semibold text-slate-600">Annuler</button>
                <button type="submit" class="px-5 py-2 bg-indigo-600 text-white rounded text-sm font-semibold">Enregistrer</button>
            </div>
        </form>
    </div>
</div>

<!-- MODAL AMDEC -->
<div id="modalAmdec" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm hidden z-50 flex justify-center items-center p-4">
    <div class="bg-white rounded-xl shadow-xl w-full max-w-md overflow-hidden">
        <div class="bg-slate-900 text-white px-6 py-4 flex justify-between items-center">
            <h3 class="font-bold" id="modalAmdecTitle">Gérer ligne AMDEC</h3>
            <button onclick="closeAmdecModal()" class="text-slate-400 hover:text-white"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <form onsubmit="saveAmdec(event)" class="p-6 space-y-4">
            <input type="hidden" id="amdecIndex">
            <div>
                <label class="block text-xs font-bold text-slate-700 uppercase mb-1">Équipement</label>
                <input type="text" id="amdecEquipement" required class="w-full border rounded p-2 text-sm outline-none">
            </div>
            <div>
                <label class="block text-xs font-bold text-slate-700 uppercase mb-1">Mode de Défaillance</label>
                <input type="text" id="amdecMode" required class="w-full border rounded p-2 text-sm outline-none">
            </div>
            <div class="grid grid-cols-3 gap-2">
                <div>
                    <label class="block text-xs font-bold text-slate-700 uppercase mb-1">G (Gravité)</label>
                    <input type="number" min="1" max="10" id="amdecG" required class="w-full border rounded p-2 text-sm outline-none">
                </div>
                <div>
                    <label class="block text-xs font-bold text-slate-700 uppercase mb-1">O (Occ.)</label>
                    <input type="number" min="1" max="10" id="amdecO" required class="w-full border rounded p-2 text-sm outline-none">
                </div>
                <div>
                    <label class="block text-xs font-bold text-slate-700 uppercase mb-1">D (Détect.)</label>
                    <input type="number" min="1" max="10" id="amdecD" required class="w-full border rounded p-2 text-sm outline-none">
                </div>
            </div>
            <div>
                <label class="block text-xs font-bold text-slate-700 uppercase mb-1">Action Préconisée</label>
                <textarea id="amdecAction" required rows="2" class="w-full border rounded p-2 text-sm outline-none"></textarea>
            </div>
            <div class="flex justify-end gap-3 pt-4 border-t">
                <button type="button" onclick="closeAmdecModal()" class="px-4 py-2 border rounded text-sm font-semibold text-slate-600">Annuler</button>
                <button type="submit" class="px-5 py-2 bg-indigo-600 text-white rounded text-sm font-semibold">Enregistrer</button>
            </div>
        </form>
    </div>
</div>

<!-- MODAL DMAIC -->
<div id="modalDmaic" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm hidden z-50 flex justify-center items-center p-4">
    <div class="bg-white rounded-xl shadow-xl w-full max-w-md overflow-hidden">
        <div class="bg-slate-900 text-white px-6 py-4 flex justify-between items-center">
            <h3 class="font-bold" id="modalDmaicTitle">Gérer Projet DMAIC</h3>
            <button onclick="closeDmaicModal()" class="text-slate-400 hover:text-white"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <form onsubmit="saveDmaic(event)" class="p-6 space-y-4">
            <input type="hidden" id="dmaicIndex">
            <div>
                <label class="block text-xs font-bold text-slate-700 uppercase mb-1">Titre du projet</label>
                <input type="text" id="dmaicTitre" required class="w-full border rounded p-2 text-sm outline-none">
            </div>
            <div>
                <label class="block text-xs font-bold text-slate-700 uppercase mb-1">Étape Actuelle</label>
                <select id="dmaicEtape" class="w-full border rounded p-2 text-sm outline-none">
                    <option value="Définir">D - Définir</option>
                    <option value="Mesurer">M - Mesurer</option>
                    <option value="Analyser">A - Analyser</option>
                    <option value="Améliorer">I - Améliorer</option>
                    <option value="Contrôler">C - Contrôler</option>
                </select>
            </div>
            <div>
                <label class="block text-xs font-bold text-slate-700 uppercase mb-1">Objectif</label>
                <input type="text" id="dmaicObjectif" required class="w-full border rounded p-2 text-sm outline-none">
            </div>
            <div>
                <label class="block text-xs font-bold text-slate-700 uppercase mb-1">Progression (%)</label>
                <input type="number" min="0" max="100" id="dmaicProgression" required class="w-full border rounded p-2 text-sm outline-none">
            </div>
            <div class="flex justify-end gap-3 pt-4 border-t">
                <button type="button" onclick="closeDmaicModal()" class="px-4 py-2 border rounded text-sm font-semibold text-slate-600">Annuler</button>
                <button type="submit" class="px-5 py-2 bg-indigo-600 text-white rounded text-sm font-semibold">Enregistrer</button>
            </div>
        </form>
    </div>
</div>

<!-- SCRIPTS JAVASCRIPT ET LOGIQUE SÉCURISÉE DE MODIFICATION / SUPPRESSION -->
<script>
    const HEURES_OUVERTURE_MENSUEL = 160;
    let loginAttempts = 0;
    const MAX_ATTEMPTS = 5;
    let lastActivityTime = Date.now();
    const INACTIVITY_TIMEOUT = 15 * 60 * 1000;

    // Données stockées localement
    let dataOt = JSON.parse(localStorage.getItem('DATA_OT')) || [
        { id: 'OT-1001', equipement: 'Presse Hydraulique P-01', type: 'Correctif', duree: 3.5, desc: 'Fuite d\'huile vérin principal', priorite: 'Haute' },
        { id: 'OT-1002', equipement: 'Presse Hydraulique P-01', type: 'Correctif', duree: 1.5, desc: 'Remplacement distributeur', priorite: 'Haute' },
        { id: 'OT-1003', equipement: 'Moteur Principal M-02', type: 'Correctif', duree: 2.0, desc: 'Surchauffe roulement', priorite: 'Moyenne' }
    ];

    let dataAmdec = JSON.parse(localStorage.getItem('DATA_AMDEC')) || [
        { equipement: 'P-01', mode: 'Fuite d\'huile', g: 6, o: 5, d: 4, npr: 120, action: 'Remplacement préventif des joints' },
        { equipement: 'M-02', mode: 'Grippage roulement', g: 8, o: 3, d: 3, npr: 72, action: 'Plan de graissage hebdomadaire' }
    ];

    let dataDmaic = JSON.parse(localStorage.getItem('DATA_DMAIC')) || [
        { titre: 'Réduction pannes Presse P-01', etape: 'Analyser', objectif: 'Atteindre 98% de Disponibilité', progression: 60 }
    ];

    document.addEventListener("DOMContentLoaded", () => {
        checkSession();
        renderOt();
        renderAmdec();
        renderDmaic();
        updateKpiDashboard();

        ['click', 'mousemove', 'keypress', 'scroll'].forEach(evt => {
            document.addEventListener(evt, () => lastActivityTime = Date.now(), false);
        });
        setInterval(() => {
            if (sessionStorage.getItem('SESSION_USER') && (Date.now() - lastActivityTime > INACTIVITY_TIMEOUT)) {
                alert("Session expirée pour inactivité.");
                handleLogout();
            }
        }, 30000);
    });

    function sanitizeInput(str) {
        const temp = document.createElement('div');
        temp.textContent = str;
        return temp.innerHTML;
    }

    function togglePasswordVisibility() {
        const input = document.getElementById('loginPassword');
        const icon = document.getElementById('togglePasswordIcon');
        if (input.type === 'password') {
            input.type = 'text';
            icon.className = 'fa-solid fa-eye-slash';
        } else {
            input.type = 'password';
            icon.className = 'fa-solid fa-eye';
        }
    }

    function handleLogin(e) {
        e.preventDefault();
        if (loginAttempts >= MAX_ATTEMPTS) return;
        const email = document.getElementById('loginEmail').value;
        const password = document.getElementById('loginPassword').value;
        const role = document.getElementById('loginRole').value;

        if (password === "123456") {
            loginAttempts = 0;
            sessionStorage.setItem('SESSION_USER', JSON.stringify({ email: sanitizeInput(email), role: sanitizeInput(role) }));
            checkSession();
        } else {
            loginAttempts++;
            document.getElementById('loginError').classList.remove('hidden');
        }
    }

    function handleLogout() {
        sessionStorage.removeItem('SESSION_USER');
        checkSession();
    }

    function checkSession() {
        const stored = sessionStorage.getItem('SESSION_USER');
        if (stored) {
            const user = JSON.parse(stored);
            document.getElementById('auth-screen').classList.add('hidden');
            document.getElementById('app-screen').classList.remove('hidden');
            document.getElementById('userDisplay').textContent = `${user.email} (${user.role})`;
        } else {
            document.getElementById('auth-screen').classList.remove('hidden');
            document.getElementById('app-screen').classList.add('hidden');
        }
    }

    function switchTab(tab) {
        ['kpi', 'gmao', 'amdec', 'dmaic'].forEach(t => {
            document.getElementById(`sec-${t}`).classList.add('hidden');
            document.getElementById(`tab-${t}`).classList.remove('text-indigo-600', 'border-indigo-600');
            document.getElementById(`tab-${t}`).classList.add('text-slate-600', 'border-transparent');
        });
        document.getElementById(`sec-${tab}`).classList.remove('hidden');
        document.getElementById(`tab-${tab}`).classList.add('text-indigo-600', 'border-indigo-600');
    }

    function updateKpiDashboard() {
        const selectedMachine = document.getElementById('selectMachineKpi').value;
        let filtered = dataOt.filter(ot => ot.type === 'Correctif');
        if (selectedMachine !== 'ALL') filtered = filtered.filter(ot => ot.equipement === selectedMachine);

        const pannes = filtered.length;
        if (pannes === 0) {
            document.getElementById('kpiMtbf').textContent = `${HEURES_OUVERTURE_MENSUEL} hrs`;
            document.getElementById('kpiMttr').textContent = `0 hrs`;
            document.getElementById('kpiDisp').textContent = `100 %`;
            document.getElementById('kpiPannes').textContent = `0`;
            return;
        }

        const arret = filtered.reduce((s, ot) => s + (parseFloat(ot.duree) || 0), 0);
        const mtbf = ((HEURES_OUVERTURE_MENSUEL - arret) / pannes).toFixed(1);
        const mttr = (arret / pannes).toFixed(1);
        const disp = ((parseFloat(mtbf) / (parseFloat(mtbf) + parseFloat(mttr))) * 100).toFixed(2);

        document.getElementById('kpiMtbf').textContent = `${mtbf} hrs`;
        document.getElementById('kpiMttr').textContent = `${mttr} hrs`;
        document.getElementById('kpiDisp').textContent = `${disp} %`;
        document.getElementById('kpiPannes').textContent = pannes;
    }

    /* --- GESTION DES OT (CRUD) --- */
    function renderOt() {
        const tbody = document.getElementById('table-ot-body');
        tbody.innerHTML = '';
        dataOt.forEach((ot, idx) => {
            tbody.innerHTML += `
                <tr class="hover:bg-slate-50">
                    <td class="p-3 font-bold text-slate-800">${sanitizeInput(ot.id)}</td>
                    <td class="p-3">${sanitizeInput(ot.equipement)}</td>
                    <td class="p-3"><span class="px-2 py-0.5 rounded text-xs ${ot.type === 'Correctif' ? 'bg-red-100 text-red-700' : 'bg-blue-100 text-blue-700'}">${sanitizeInput(ot.type)}</span></td>
                    <td class="p-3 font-bold">${ot.duree}</td>
                    <td class="p-3 text-slate-600">${sanitizeInput(ot.desc)}</td>
                    <td class="p-3 font-bold text-xs ${ot.priorite === 'Haute' ? 'text-red-600' : 'text-slate-600'}">${sanitizeInput(ot.priorite)}</td>
                    <td class="p-3 text-center space-x-2">
                        <button onclick="editOt(${idx})" class="text-indigo-600 hover:text-indigo-900"><i class="fa-solid fa-pen"></i></button>
                        <button onclick="deleteOt(${idx})" class="text-red-600 hover:text-red-900"><i class="fa-solid fa-trash"></i></button>
                    </td>
                </tr>
            `;
        });
    }

    function openOtModal() {
        document.getElementById('otIndex').value = '';
        document.getElementById('otDuree').value = '1.0';
        document.getElementById('otDesc').value = '';
        document.getElementById('modalOtTitle').textContent = 'Ajouter un Ordre de Travail';
        document.getElementById('modalOt').classList.remove('hidden');
    }
    function closeOtModal() { document.getElementById('modalOt').classList.add('hidden'); }

    function editOt(index) {
        const ot = dataOt[index];
        document.getElementById('otIndex').value = index;
        document.getElementById('otEquipement').value = ot.equipement;
        document.getElementById('otType').value = ot.type;
        document.getElementById('otDuree').value = ot.duree;
        document.getElementById('otDesc').value = ot.desc;
        document.getElementById('otPriorite').value = ot.priorite;
        document.getElementById('modalOtTitle').textContent = 'Modifier l\'Ordre de Travail';
        document.getElementById('modalOt').classList.remove('hidden');
    }

    function deleteOt(index) {
        if (confirm("Voulez-vous vraiment supprimer cet OT ?")) {
            dataOt.splice(index, 1);
            saveData('DATA_OT', dataOt);
            renderOt();
            updateKpiDashboard();
        }
    }

    function saveOt(e) {
        e.preventDefault();
        const idx = document.getElementById('otIndex').value;
        const obj = {
            id: idx !== '' ? dataOt[idx].id : 'OT-' + (1000 + dataOt.length + 1),
            equipement: sanitizeInput(document.getElementById('otEquipement').value),
            type: sanitizeInput(document.getElementById('otType').value),
            duree: parseFloat(document.getElementById('otDuree').value),
            desc: sanitizeInput(document.getElementById('otDesc').value),
            priorite: sanitizeInput(document.getElementById('otPriorite').value)
        };

        if (idx !== '') dataOt[idx] = obj;
        else dataOt.push(obj);

        saveData('DATA_OT', dataOt);
        renderOt();
        updateKpiDashboard();
        closeOtModal();
    }

    /* --- GESTION AMDEC (CRUD) --- */
    function renderAmdec() {
        const tbody = document.getElementById('table-amdec-body');
        tbody.innerHTML = '';
        dataAmdec.forEach((item, idx) => {
            tbody.innerHTML += `
                <tr class="hover:bg-slate-50">
                    <td class="p-3 font-semibold">${sanitizeInput(item.equipement)}</td>
                    <td class="p-3">${sanitizeInput(item.mode)}</td>
                    <td class="p-3 text-center">${item.g}</td>
                    <td class="p-3 text-center">${item.o}</td>
                    <td class="p-3 text-center">${item.d}</td>
                    <td class="p-3 text-center font-bold text-red-600">${item.npr}</td>
                    <td class="p-3 text-slate-600">${sanitizeInput(item.action)}</td>
                    <td class="p-3 text-center space-x-2">
                        <button onclick="editAmdec(${idx})" class="text-indigo-600 hover:text-indigo-900"><i class="fa-solid fa-pen"></i></button>
                        <button onclick="deleteAmdec(${idx})" class="text-red-600 hover:text-red-900"><i class="fa-solid fa-trash"></i></button>
                    </td>
                </tr>
            `;
        });
    }

    function openAmdecModal() {
        document.getElementById('amdecIndex').value = '';
        document.getElementById('amdecEquipement').value = '';
        document.getElementById('amdecMode').value = '';
        document.getElementById('amdecG').value = '1';
        document.getElementById('amdecO').value = '1';
        document.getElementById('amdecD').value = '1';
        document.getElementById('amdecAction').value = '';
        document.getElementById('modalAmdecTitle').textContent = 'Ajouter une ligne AMDEC';
        document.getElementById('modalAmdec').classList.remove('hidden');
    }
    function closeAmdecModal() { document.getElementById('modalAmdec').classList.add('hidden'); }

    function editAmdec(index) {
        const a = dataAmdec[index];
        document.getElementById('amdecIndex').value = index;
        document.getElementById('amdecEquipement').value = a.equipement;
        document.getElementById('amdecMode').value = a.mode;
        document.getElementById('amdecG').value = a.g;
        document.getElementById('amdecO').value = a.o;
        document.getElementById('amdecD').value = a.d;
        document.getElementById('amdecAction').value = a.action;
        document.getElementById('modalAmdecTitle').textContent = 'Modifier ligne AMDEC';
        document.getElementById('modalAmdec').classList.remove('hidden');
    }

    function deleteAmdec(index) {
        if (confirm("Supprimer cette entrée AMDEC ?")) {
            dataAmdec.splice(index, 1);
            saveData('DATA_AMDEC', dataAmdec);
            renderAmdec();
        }
    }

    function saveAmdec(e) {
        e.preventDefault();
        const idx = document.getElementById('amdecIndex').value;
        const g = parseInt(document.getElementById('amdecG').value);
        const o = parseInt(document.getElementById('amdecO').value);
        const d = parseInt(document.getElementById('amdecD').value);

        const obj = {
            equipement: sanitizeInput(document.getElementById('amdecEquipement').value),
            mode: sanitizeInput(document.getElementById('amdecMode').value),
            g: g, o: o, d: d, npr: g * o * d,
            action: sanitizeInput(document.getElementById('amdecAction').value)
        };

        if (idx !== '') dataAmdec[idx] = obj;
        else dataAmdec.push(obj);

        saveData('DATA_AMDEC', dataAmdec);
        renderAmdec();
        closeAmdecModal();
    }

    /* --- GESTION DMAIC (CRUD) --- */
    function renderDmaic() {
        const container = document.getElementById('dmaic-projects-container');
        container.innerHTML = '';
        dataDmaic.forEach((p, idx) => {
            container.innerHTML += `
                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200 space-y-3 relative">
                    <div class="flex justify-between items-center">
                        <span class="text-xs font-bold bg-indigo-100 text-indigo-700 px-2.5 py-1 rounded">${sanitizeInput(p.etape)}</span>
                        <div class="space-x-2">
                            <button onclick="editDmaic(${idx})" class="text-indigo-600 hover:text-indigo-900"><i class="fa-solid fa-pen"></i></button>
                            <button onclick="deleteDmaic(${idx})" class="text-red-600 hover:text-red-900"><i class="fa-solid fa-trash"></i></button>
                        </div>
                    </div>
                    <h3 class="font-bold text-slate-800 text-lg">${sanitizeInput(p.titre)}</h3>
                    <p class="text-xs text-slate-500">Objectif: ${sanitizeInput(p.objectif)}</p>
                    <div class="w-full bg-slate-200 h-2 rounded-full overflow-hidden">
                        <div class="bg-indigo-600 h-full" style="width: ${p.progression}%"></div>
                    </div>
                </div>
            `;
        });
    }

    function openDmaicModal() {
        document.getElementById('dmaicIndex').value = '';
        document.getElementById('dmaicTitre').value = '';
        document.getElementById('dmaicObjectif').value = '';
        document.getElementById('dmaicProgression').value = '0';
        document.getElementById('modalDmaicTitle').textContent = 'Ajouter Projet DMAIC';
        document.getElementById('modalDmaic').classList.remove('hidden');
    }
    function closeDmaicModal() { document.getElementById('modalDmaic').classList.add('hidden'); }

    function editDmaic(index) {
        const d = dataDmaic[index];
        document.getElementById('dmaicIndex').value = index;
        document.getElementById('dmaicTitre').value = d.titre;
        document.getElementById('dmaicEtape').value = d.etape;
        document.getElementById('dmaicObjectif').value = d.objectif;
        document.getElementById('dmaicProgression').value = d.progression;
        document.getElementById('modalDmaicTitle').textContent = 'Modifier Projet DMAIC';
        document.getElementById('modalDmaic').classList.remove('hidden');
    }

    function deleteDmaic(index) {
        if (confirm("Supprimer ce projet DMAIC ?")) {
            dataDmaic.splice(index, 1);
            saveData('DATA_DMAIC', dataDmaic);
            renderDmaic();
        }
    }

    function saveDmaic(e) {
        e.preventDefault();
        const idx = document.getElementById('dmaicIndex').value;
        const obj = {
            titre: sanitizeInput(document.getElementById('dmaicTitre').value),
            etape: sanitizeInput(document.getElementById('dmaicEtape').value),
            objectif: sanitizeInput(document.getElementById('dmaicObjectif').value),
            progression: parseInt(document.getElementById('dmaicProgression').value)
        };

        if (idx !== '') dataDmaic[idx] = obj;
        else dataDmaic.push(obj);

        saveData('DATA_DMAIC', dataDmaic);
        renderDmaic();
        closeDmaicModal();
    }

    function saveData(key, data) {
        localStorage.setItem(key, JSON.stringify(data));
    }
</script>

</body>
</html>
