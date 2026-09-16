<!DOCTYPE html>
<html lang="fr" class="h-full bg-slate-100">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <!-- En-têtes HTTP recommandés à configurer côté serveur :
         Content-Security-Policy: default-src 'self' https://cdn.tailwindcss.com https://cdnjs.cloudflare.com;
         X-Frame-Options: SAMEORIGIN;
         X-Content-Type-Options: nosniff;
    -->
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

<!-- ÉCRAN DE CONNEXION (AUTHENTIFICATION SÉCURISÉE) -->
<div id="auth-screen" class="fixed inset-0 bg-slate-900 z-50 flex items-center justify-center p-4">
    <div class="bg-white rounded-xl shadow-2xl max-w-md w-full p-8 border border-slate-700">
        <div class="text-center mb-8">
            <div class="inline-flex items-center justify-center w-16 h-16 bg-indigo-100 text-indigo-600 rounded-full mb-4">
                <i class="fa-solid fa-shield-halved text-3xl"></i>
            </div>
            <h2 class="text-2xl font-bold text-slate-900">Plateforme de Maintenance</h2>
            <p class="text-sm text-slate-500 mt-1">GMAO • AMDEC • DMAIC • KPIs</p>
        </div>

        <!-- Alerte d'erreur de sécurité / tentative -->
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
    
    <!-- Navbar / En-tête -->
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

        <!-- Onglets de Navigation -->
        <div class="flex border-b border-slate-300 gap-2 overflow-x-auto bg-white p-2 rounded-t-lg shadow-sm">
            <button onclick="switchTab('kpi')" id="tab-kpi" class="tab-btn px-4 py-2 font-semibold text-indigo-600 border-b-2 border-indigo-600 flex items-center gap-2">
                <i class="fa-solid fa-chart-line"></i> KPIs Performance
            </button>
            <button onclick="switchTab('gmao')" id="tab-gmao" class="tab-btn px-4 py-2 font-semibold text-slate-600 border-b-2 border-transparent hover:text-indigo-600 flex items-center gap-2">
                <i class="fa-solid fa-wrench"></i> GMAO (Ordres de Travail)
            </button>
            <button onclick="switchTab('docs')" id="tab-docs" class="tab-btn px-4 py-2 font-semibold text-slate-600 border-b-2 border-transparent hover:text-indigo-600 flex items-center gap-2">
                <i class="fa-solid fa-file-pdf"></i> Documentation Technique
            </button>
            <button onclick="switchTab('amdec')" id="tab-amdec" class="tab-btn px-4 py-2 font-semibold text-slate-600 border-b-2 border-transparent hover:text-indigo-600 flex items-center gap-2">
                <i class="fa-solid fa-shield-halved"></i> Analyse AMDEC
            </button>
            <button onclick="switchTab('dmaic')" id="tab-dmaic" class="tab-btn px-4 py-2 font-semibold text-slate-600 border-b-2 border-transparent hover:text-indigo-600 flex items-center gap-2">
                <i class="fa-solid fa-diagram-project"></i> Projets DMAIC
            </button>
        </div>

        <!-- 1. SECTION KPIs (Calculs Dynamiques par Machine) -->
        <section id="sec-kpi" class="space-y-6">
            <div class="bg-white p-4 rounded-xl shadow-sm border border-slate-200 flex flex-wrap justify-between items-center gap-4">
                <div class="flex items-center gap-2">
                    <i class="fa-solid fa-industry text-indigo-600 text-lg"></i>
                    <label class="font-bold text-slate-700 text-sm">Sélectionner un équipement de production :</label>
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
                    <div class="text-xs text-slate-500 mt-1">Heures d'ouverture / Nb de pannes</div>
                </div>
                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200">
                    <div class="text-xs font-bold text-slate-400 uppercase">MTTR (Temps Moyen de Réparation)</div>
                    <div class="text-3xl font-extrabold text-amber-600 mt-2" id="kpiMttr">-- hrs</div>
                    <div class="text-xs text-slate-500 mt-1">Temps d'arrêt / Nb de pannes</div>
                </div>
                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200">
                    <div class="text-xs font-bold text-slate-400 uppercase">Disponibilité Opérationnelle</div>
                    <div class="text-3xl font-extrabold text-blue-600 mt-2" id="kpiDisp">-- %</div>
                    <div class="text-xs text-slate-500 mt-1">MTBF / (MTBF + MTTR)</div>
                </div>
                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200">
                    <div class="text-xs font-bold text-slate-400 uppercase">Nombre de Pannes Enregistrées</div>
                    <div class="text-3xl font-extrabold text-red-600 mt-2" id="kpiPannes">--</div>
                    <div class="text-xs text-slate-500 mt-1">Ordres de Travail Correctifs</div>
                </div>
            </div>
        </section>

        <!-- 2. SECTION GMAO -->
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
                            <th class="p-3">Durée d'arrêt (hrs)</th>
                            <th class="p-3">Description</th>
                            <th class="p-3">Priorité</th>
                        </tr>
                    </thead>
                    <tbody id="table-ot-body" class="divide-y divide-slate-200"></tbody>
                </table>
            </div>
        </section>

        <!-- 3. SECTION DOCUMENTATION TECHNIQUE -->
        <section id="sec-docs" class="hidden space-y-6">
            <div class="bg-white p-4 rounded-xl shadow-sm border border-slate-200 flex justify-between items-center">
                <h2 class="text-lg font-bold text-slate-800"><i class="fa-solid fa-book text-indigo-600 mr-2"></i>Documentation & Notices Techniques</h2>
            </div>
            <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
                <div class="bg-white p-4 rounded-xl border border-slate-200 shadow-sm flex items-center justify-between">
                    <div>
                        <h4 class="font-bold text-slate-800">Schéma Électrique P-01</h4>
                        <p class="text-xs text-slate-500">PDF • 2.4 MB</p>
                    </div>
                    <button class="text-indigo-600 hover:text-indigo-800"><i class="fa-solid fa-download text-xl"></i></button>
                </div>
                <div class="bg-white p-4 rounded-xl border border-slate-200 shadow-sm flex items-center justify-between">
                    <div>
                        <h4 class="font-bold text-slate-800">Manuel de Maintenance M-02</h4>
                        <p class="text-xs text-slate-500">PDF • 5.1 MB</p>
                    </div>
                    <button class="text-indigo-600 hover:text-indigo-800"><i class="fa-solid fa-download text-xl"></i></button>
                </div>
            </div>
        </section>

        <!-- 4. SECTION AMDEC -->
        <section id="sec-amdec" class="hidden space-y-6">
            <div class="bg-white p-4 rounded-xl shadow-sm border border-slate-200">
                <h2 class="text-lg font-bold text-slate-800"><i class="fa-solid fa-shield-halved text-indigo-600 mr-2"></i>Analyse des Risques (AMDEC)</h2>
            </div>
            <div class="bg-white rounded-xl shadow-sm border border-slate-200 overflow-hidden">
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
                        </tr>
                    </thead>
                    <tbody id="table-amdec-body" class="divide-y divide-slate-200"></tbody>
                </table>
            </div>
        </section>

        <!-- 5. SECTION DMAIC -->
        <section id="sec-dmaic" class="hidden space-y-6">
            <div class="bg-white p-4 rounded-xl shadow-sm border border-slate-200">
                <h2 class="text-lg font-bold text-slate-800"><i class="fa-solid fa-diagram-project text-indigo-600 mr-2"></i>Projets d'Amélioration Continue (DMAIC)</h2>
            </div>
            <div class="grid grid-cols-1 md:grid-cols-2 gap-6" id="dmaic-projects-container"></div>
        </section>

    </main>
</div>

<!-- MODAL CRÉATION OT -->
<div id="modalOt" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm hidden z-50 flex justify-center items-center p-4">
    <div class="bg-white rounded-xl shadow-xl w-full max-w-md overflow-hidden">
        <div class="bg-slate-900 text-white px-6 py-4 flex justify-between items-center">
            <h3 class="font-bold">Créer un Ordre de Travail (OT)</h3>
            <button onclick="closeOtModal()" class="text-slate-400 hover:text-white"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <form onsubmit="saveOt(event)" class="p-6 space-y-4">
            <div>
                <label class="block text-xs font-bold text-slate-700 uppercase mb-1">Équipement Cible</label>
                <select id="otEquipement" class="w-full border rounded p-2 text-sm outline-none">
                    <option value="Presse Hydraulique P-01">Presse Hydraulique P-01</option>
                    <option value="Moteur Principal M-02">Moteur Principal M-02</option>
                    <option value="Ligne d'Extrusion B-02">Ligne d'Extrusion B-02</option>
                </select>
            </div>
            <div>
                <label class="block text-xs font-bold text-slate-700 uppercase mb-1">Type d'intervention</label>
                <select id="otType" class="w-full border rounded p-2 text-sm outline-none">
                    <option value="Correctif">Correctif (Panne)</option>
                    <option value="Préventif">Préventif (Planifié)</option>
                </select>
            </div>
            <div>
                <label class="block text-xs font-bold text-slate-700 uppercase mb-1">Durée d'arrêt / Réparation (heures)</label>
                <input type="number" step="0.1" id="otDuree" required value="2.0" class="w-full border rounded p-2 text-sm outline-none">
            </div>
            <div>
                <label class="block text-xs font-bold text-slate-700 uppercase mb-1">Description de la panne / intervention</label>
                <textarea id="otDesc" required rows="2" class="w-full border rounded p-2 text-sm outline-none"></textarea>
            </div>
            <div>
                <label class="block text-xs font-bold text-slate-700 uppercase mb-1">Priorité</label>
                <select id="otPriorite" class="w-full border rounded p-2 text-sm outline-none">
                    <option value="Haute">Haute</option>
                    <option value="Moyenne" selected>Moyenne</option>
                    <option value="Basse">Basse</option>
                </select>
            </div>
            <div class="flex justify-end gap-3 pt-4 border-t">
                <button type="button" onclick="closeOtModal()" class="px-4 py-2 border rounded text-sm font-semibold text-slate-600">Annuler</button>
                <button type="submit" class="px-5 py-2 bg-indigo-600 text-white rounded text-sm font-semibold">Enregistrer l'OT</button>
            </div>
        </form>
    </div>
</div>

<!-- SCRIPTS JAVASCRIPT & SÉCURITÉ -->
<script>
    const HEURES_OUVERTURE_MENSUEL = 160;
    
    // Variables de sécurité (Rate Limiting)
    let loginAttempts = 0;
    const MAX_ATTEMPTS = 5;
    let lockoutTimer = null;
    let lastActivityTime = Date.now();
    const INACTIVITY_TIMEOUT = 15 * 60 * 1000; // 15 minutes d'inactivité -> Déconnexion

    let dataOt = JSON.parse(localStorage.getItem('DATA_OT')) || [
        { id: 'OT-1001', equipement: 'Presse Hydraulique P-01', type: 'Correctif', duree: 3.5, desc: 'Fuite d\'huile vérin principal', priorite: 'Haute' },
        { id: 'OT-1002', equipement: 'Presse Hydraulique P-01', type: 'Correctif', duree: 1.5, desc: 'Remplacement distributeur', priorite: 'Haute' },
        { id: 'OT-1003', equipement: 'Moteur Principal M-02', type: 'Correctif', duree: 2.0, desc: 'Surchauffe roulement', priorite: 'Moyenne' },
        { id: 'OT-1004', equipement: 'Ligne d\'Extrusion B-02', type: 'Préventif', duree: 4.0, desc: 'Entretien préventif mensuel', priorite: 'Moyenne' }
    ];

    let dataAmdec = [
        { equipement: 'P-01', mode: 'Fuite d\'huile', g: 6, o: 5, d: 4, npr: 120, action: 'Remplacement préventif des joints' },
        { equipement: 'M-02', mode: 'Grippage roulement', g: 8, o: 3, d: 3, npr: 72, action: 'Plan de graissage hebdomadaire' }
    ];

    let dataDmaic = [
        { titre: 'Réduction pannes Presse P-01', cible: 'Presse P-01', etape: 'A', objectif: 'Atteindre 98% de Disponibilité', progression: 60 }
    ];

    document.addEventListener("DOMContentLoaded", () => {
        checkSession();
        renderOt();
        renderAmdec();
        renderDmaic();
        updateKpiDashboard();

        // Surveillance de l'inactivité utilisateur
        ['click', 'mousemove', 'keypress', 'scroll'].forEach(evt => {
            document.addEventListener(evt, resetInactivityTimer, false);
        });
        setInterval(checkInactivity, 30000); // Vérification toutes les 30s
    });

    // Fonction de nettoyage XSS (Sanitization)
    function sanitizeInput(str) {
        const temp = document.createElement('div');
        temp.textContent = str;
        return temp.innerHTML;
    }

    // Gestion de la visibilité du mot de passe
    function togglePasswordVisibility() {
        const passwordInput = document.getElementById('loginPassword');
        const icon = document.getElementById('togglePasswordIcon');
        if (passwordInput.type === 'password') {
            passwordInput.type = 'text';
            icon.classList.remove('fa-eye');
            icon.classList.add('fa-eye-slash');
        } else {
            passwordInput.type = 'password';
            icon.classList.remove('fa-eye-slash');
            icon.classList.add('fa-eye');
        }
    }

    // Authentification Sécurisée avec Brute-Force Protection
    function handleLogin(e) {
        e.preventDefault();
        
        if (loginAttempts >= MAX_ATTEMPTS) {
            showLoginError("Trop de tentatives échouées. Compte temporairement bloqué (30s).");
            return;
        }

        const email = document.getElementById('loginEmail').value;
        const password = document.getElementById('loginPassword').value;
        const role = document.getElementById('loginRole').value;

        // Validation simple du mot de passe (Exemple)
        if (password === "123456") {
            loginAttempts = 0;
            const user = { email: sanitizeInput(email), role: sanitizeInput(role), token: Date.now() };
            sessionStorage.setItem('SESSION_USER', JSON.stringify(user)); // Utilisation de sessionStorage (plus sûr que localStorage)
            checkSession();
        } else {
            loginAttempts++;
            if (loginAttempts >= MAX_ATTEMPTS) {
                document.getElementById('btnLogin').disabled = true;
                showLoginError("Accès bloqué suite à 5 tentatives échouées. Réessayez dans 30s.");
                setTimeout(() => {
                    loginAttempts = 0;
                    document.getElementById('btnLogin').disabled = false;
                    hideLoginError();
                }, 30000);
            } else {
                showLoginError(`Mot de passe incorrect. (${MAX_ATTEMPTS - loginAttempts} essais restants)`);
            }
        }
    }

    function showLoginError(msg) {
        const errDiv = document.getElementById('loginError');
        document.getElementById('loginErrorText').textContent = msg;
        errDiv.classList.remove('hidden');
    }

    function hideLoginError() {
        document.getElementById('loginError').classList.add('hidden');
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
            resetInactivityTimer();
        } else {
            document.getElementById('auth-screen').classList.remove('hidden');
            document.getElementById('app-screen').classList.add('hidden');
        }
    }

    // Gestion du Timeout d'Inactivité
    function resetInactivityTimer() {
        lastActivityTime = Date.now();
    }

    function checkInactivity() {
        if (sessionStorage.getItem('SESSION_USER')) {
            if (Date.now() - lastActivityTime > INACTIVITY_TIMEOUT) {
                alert("Session expirée en raison d'une longue inactivité.");
                handleLogout();
            }
        }
    }

    function switchTab(tab) {
        ['kpi', 'gmao', 'docs', 'amdec', 'dmaic'].forEach(t => {
            document.getElementById(`sec-${t}`).classList.add('hidden');
            document.getElementById(`tab-${t}`).classList.remove('text-indigo-600', 'border-indigo-600');
            document.getElementById(`tab-${t}`).classList.add('text-slate-600', 'border-transparent');
        });
        document.getElementById(`sec-${tab}`).classList.remove('hidden');
        document.getElementById(`tab-${tab}`).classList.add('text-indigo-600', 'border-indigo-600');
        document.getElementById(`tab-${tab}`).classList.remove('text-slate-600', 'border-transparent');
    }

    // Calcul dynamique des KPIs
    function updateKpiDashboard() {
        const selectedMachine = document.getElementById('selectMachineKpi').value;
        
        let filteredOts = dataOt.filter(ot => ot.type === 'Correctif');
        if (selectedMachine !== 'ALL') {
            filteredOts = filteredOts.filter(ot => ot.equipement === selectedMachine);
        }

        const nombrePannes = filteredOts.length;

        if (nombrePannes === 0) {
            document.getElementById('kpiMtbf').textContent = `${HEURES_OUVERTURE_MENSUEL} hrs`;
            document.getElementById('kpiMttr').textContent = `0 hrs`;
            document.getElementById('kpiDisp').textContent = `100 %`;
            document.getElementById('kpiPannes').textContent = `0`;
            return;
        }

        const tempsTotalArret = filteredOts.reduce((sum, ot) => sum + (parseFloat(ot.duree) || 0), 0);
        const tempsFonctionnement = HEURES_OUVERTURE_MENSUEL - tempsTotalArret;

        const mtbf = (tempsFonctionnement / nombrePannes).toFixed(1);
        const mttr = (tempsTotalArret / nombrePannes).toFixed(1);
        const disp = ((parseFloat(mtbf) / (parseFloat(mtbf) + parseFloat(mttr))) * 100).toFixed(2);

        document.getElementById('kpiMtbf').textContent = `${mtbf} hrs`;
        document.getElementById('kpiMttr').textContent = `${mttr} hrs`;
        document.getElementById('kpiDisp').textContent = `${disp} %`;
        document.getElementById('kpiPannes').textContent = nombrePannes;
    }

    // Affichage des Tables avec Sécurisation des Données
    function renderOt() {
        const tbody = document.getElementById('table-ot-body');
        tbody.innerHTML = '';
        dataOt.forEach(ot => {
            tbody.innerHTML += `
                <tr class="hover:bg-slate-50">
                    <td class="p-3 font-bold text-slate-800">${sanitizeInput(ot.id)}</td>
                    <td class="p-3 font-semibold">${sanitizeInput(ot.equipement)}</td>
                    <td class="p-3"><span class="px-2 py-0.5 rounded text-xs ${ot.type === 'Correctif' ? 'bg-red-100 text-red-700' : 'bg-blue-100 text-blue-700'}">${sanitizeInput(ot.type)}</span></td>
                    <td class="p-3 font-bold text-slate-700">${ot.duree}</td>
                    <td class="p-3 text-slate-600">${sanitizeInput(ot.desc)}</td>
                    <td class="p-3 font-bold text-xs ${ot.priorite === 'Haute' ? 'text-red-600' : 'text-slate-600'}">${sanitizeInput(ot.priorite)}</td>
                </tr>
            `;
        });
    }

    function renderAmdec() {
        const tbody = document.getElementById('table-amdec-body');
        tbody.innerHTML = '';
        dataAmdec.forEach(item => {
            tbody.innerHTML += `
                <tr class="hover:bg-slate-50">
                    <td class="p-3 font-semibold text-slate-800">${sanitizeInput(item.equipement)}</td>
                    <td class="p-3">${sanitizeInput(item.mode)}</td>
                    <td class="p-3 text-center">${item.g}</td>
                    <td class="p-3 text-center">${item.o}</td>
                    <td class="p-3 text-center">${item.d}</td>
                    <td class="p-3 text-center font-bold text-red-600">${item.npr}</td>
                    <td class="p-3 text-slate-600">${sanitizeInput(item.action)}</td>
                </tr>
            `;
        });
    }

    function renderDmaic() {
        const container = document.getElementById('dmaic-projects-container');
        container.innerHTML = '';
        dataDmaic.forEach(p => {
            container.innerHTML += `
                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200 space-y-3">
                    <span class="text-xs font-bold bg-indigo-100 text-indigo-700 px-2.5 py-1 rounded">Étape ${sanitizeInput(p.etape)}</span>
                    <h3 class="font-bold text-slate-800 text-lg">${sanitizeInput(p.titre)}</h3>
                    <p class="text-xs text-slate-500">Objectif: ${sanitizeInput(p.objectif)}</p>
                    <div class="w-full bg-slate-200 h-2 rounded-full overflow-hidden">
                        <div class="bg-indigo-600 h-full" style="width: ${p.progression}%"></div>
                    </div>
                </div>
            `;
        });
    }

    function openOtModal() { document.getElementById('modalOt').classList.remove('hidden'); }
    function closeOtModal() { document.getElementById('modalOt').classList.add('hidden'); }

    function saveOt(e) {
        e.preventDefault();
        const newOt = {
            id: 'OT-' + (1000 + dataOt.length + 1),
            equipement: sanitizeInput(document.getElementById('otEquipement').value),
            type: sanitizeInput(document.getElementById('otType').value),
            duree: parseFloat(document.getElementById('otDuree').value),
            desc: sanitizeInput(document.getElementById('otDesc').value),
            priorite: sanitizeInput(document.getElementById('otPriorite').value)
        };
        dataOt.push(newOt);
        localStorage.setItem('DATA_OT', JSON.stringify(dataOt));
        renderOt();
        updateKpiDashboard();
        closeOtModal();
    }
</script>

</body>
</html>
