<!DOCTYPE html>
<html lang="ar" dir="rtl" class="h-full bg-slate-100">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>منصة الصيانة الذكية - GMAO | AMDEC | DMAIC | KPIs</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome CDN -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Chart.js CDN -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        body { font-family: system-ui, -apple-system, sans-serif; }
    </style>
</head>
<body class="flex flex-col min-h-full text-slate-800">

<!-- شاشة تسجيل الدخول -->
<div id="auth-screen" class="fixed inset-0 bg-slate-900 z-50 flex items-center justify-center p-4">
    <div class="bg-white rounded-xl shadow-2xl max-w-md w-full p-8 border border-slate-700 text-right">
        <div class="text-center mb-8">
            <div class="inline-flex items-center justify-center w-16 h-16 bg-indigo-100 text-indigo-600 rounded-full mb-4">
                <i class="fa-solid fa-industry text-3xl"></i>
            </div>
            <h2 class="text-2xl font-bold text-slate-900">منصة الصيانة والتحسين المستمر</h2>
            <p class="text-sm text-slate-500 mt-1">GMAO • AMDEC • DMAIC • KPIs</p>
        </div>

        <form onsubmit="handleLogin(event)" class="space-y-4">
            <div>
                <label class="block text-xs font-bold text-slate-700 uppercase mb-1">البريد الإلكتروني</label>
                <input type="email" id="loginEmail" required value="admin@usine.com" class="w-full px-4 py-2 border rounded-lg text-sm focus:ring-2 focus:ring-indigo-500 outline-none text-left">
            </div>

            <div>
                <label class="block text-xs font-bold text-slate-700 uppercase mb-1">كلمة المرور</label>
                <input type="password" id="loginPassword" required value="123456" class="w-full px-4 py-2 border rounded-lg text-sm focus:ring-2 focus:ring-indigo-500 outline-none text-left">
            </div>

            <div>
                <label class="block text-xs font-bold text-slate-700 uppercase mb-1">الدور الوظيفي</label>
                <select id="loginRole" class="w-full border rounded-lg p-2 text-sm outline-none focus:ring-2 focus:ring-indigo-500 bg-white">
                    <option value="Admin">مدير الصيانة / Admin</option>
                    <option value="Technicien">تقني صيانة / Technicien</option>
                    <option value="Qualite">مهندس جودة / Qualité</option>
                </select>
            </div>

            <button type="submit" class="w-full bg-indigo-600 hover:bg-indigo-700 text-white font-semibold py-2.5 rounded-lg shadow-lg transition duration-200">
                <i class="fa-solid fa-right-to-bracket ml-2"></i> تسجيل الدخول
            </button>
        </form>
    </div>
</div>

<!-- التطبيق الرئيسي -->
<div id="app-screen" class="hidden flex flex-col min-h-screen">
    
    <!-- الشريط العلوي -->
    <header class="bg-slate-900 text-white shadow-md sticky top-0 z-40">
        <div class="max-w-7xl mx-auto px-4 py-3 flex justify-between items-center">
            <div class="flex items-center gap-3">
                <i class="fa-solid fa-gears text-2xl text-indigo-400"></i>
                <h1 class="text-xl font-bold tracking-wide">منصة الصيانة الذكية <span class="text-xs bg-indigo-600 px-2 py-0.5 rounded text-white">PRO</span></h1>
            </div>
            
            <div class="flex items-center gap-4">
                <div class="text-left hidden sm:block">
                    <div class="text-xs text-slate-400">المستخدم الحالي</div>
                    <div class="text-sm font-semibold text-indigo-300" id="userDisplay">Admin</div>
                </div>
                <button onclick="handleLogout()" class="bg-red-600/20 hover:bg-red-600 text-red-300 hover:text-white px-3 py-1.5 rounded text-sm transition border border-red-500/30">
                    <i class="fa-solid fa-power-off ml-1"></i> خروج
                </button>
            </div>
        </div>
    </header>

    <!-- المحتوى الرئيسي -->
    <main class="max-w-7xl mx-auto px-4 py-6 flex-grow w-full space-y-6">

        <!-- تبويبات التنقل -->
        <div class="flex border-b border-slate-300 gap-2 overflow-x-auto bg-white p-2 rounded-t-lg shadow-sm">
            <button onclick="switchTab('kpi')" id="tab-kpi" class="tab-btn px-4 py-2 font-semibold text-indigo-600 border-b-2 border-indigo-600 flex items-center gap-2">
                <i class="fa-solid fa-chart-line"></i> مؤشرات الأداء (KPIs)
            </button>
            <button onclick="switchTab('gmao')" id="tab-gmao" class="tab-btn px-4 py-2 font-semibold text-slate-600 border-b-2 border-transparent hover:text-indigo-600 flex items-center gap-2">
                <i class="fa-solid fa-wrench"></i> إدارة الصيانة (GMAO)
            </button>
            <button onclick="switchTab('docs')" id="tab-docs" class="tab-btn px-4 py-2 font-semibold text-slate-600 border-b-2 border-transparent hover:text-indigo-600 flex items-center gap-2">
                <i class="fa-solid fa-file-pdf"></i> الوثائق التقنية
            </button>
            <button onclick="switchTab('amdec')" id="tab-amdec" class="tab-btn px-4 py-2 font-semibold text-slate-600 border-b-2 border-transparent hover:text-indigo-600 flex items-center gap-2">
                <i class="fa-solid fa-shield-halved"></i> تحليلات AMDEC
            </button>
            <button onclick="switchTab('dmaic')" id="tab-dmaic" class="tab-btn px-4 py-2 font-semibold text-slate-600 border-b-2 border-transparent hover:text-indigo-600 flex items-center gap-2">
                <i class="fa-solid fa-diagram-project"></i> مشاريع DMAIC
            </button>
        </div>

        <!-- 1. قسم مؤشرات الأداء (KPIs الديناميكية حسب الآلة) -->
        <section id="sec-kpi" class="space-y-6">
            <!-- أداة تحديد الآلة -->
            <div class="bg-white p-4 rounded-xl shadow-sm border border-slate-200 flex flex-wrap justify-between items-center gap-4">
                <div class="flex items-center gap-2">
                    <i class="fa-solid fa-industry text-indigo-600 text-lg"></i>
                    <label class="font-bold text-slate-700 text-sm">اختر آلة الإنتاج لحساب المؤشرات:</label>
                </div>
                <select id="selectMachineKpi" onchange="updateKpiDashboard()" class="border rounded-lg p-2 text-sm bg-slate-50 border-slate-300 outline-none focus:ring-2 focus:ring-indigo-500">
                    <option value="ALL">جميع الآلات (إجمالي المصنع)</option>
                    <option value="الضاغط الهيدروليكي P-01">الضاغط الهيدروليكي P-01</option>
                    <option value="المحرك الرئيسي M-02">المحرك الرئيسي M-02</option>
                    <option value="خط البثق B-02">خط البثق B-02</option>
                </select>
            </div>

            <!-- بطاقات المؤشرات -->
            <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200">
                    <div class="text-xs font-bold text-slate-400 uppercase">MTBF (متوسط الوقت بين الأعطال)</div>
                    <div class="text-3xl font-extrabold text-indigo-600 mt-2" id="kpiMtbf">-- hrs</div>
                    <div class="text-xs text-slate-500 mt-1">ساعات التشغيل / عدد الأعطال</div>
                </div>
                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200">
                    <div class="text-xs font-bold text-slate-400 uppercase">MTTR (متوسط وقت الإصلاح)</div>
                    <div class="text-3xl font-extrabold text-amber-600 mt-2" id="kpiMttr">-- hrs</div>
                    <div class="text-xs text-slate-500 mt-1">ساعات التوقف / عدد الأعطال</div>
                </div>
                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200">
                    <div class="text-xs font-bold text-slate-400 uppercase">Disponibilité (الجاهزية)</div>
                    <div class="text-3xl font-extrabold text-blue-600 mt-2" id="kpiDisp">-- %</div>
                    <div class="text-xs text-slate-500 mt-1">MTBF / (MTBF + MTTR)</div>
                </div>
                <div class="bg-white p-5 rounded-xl shadow-sm border border-slate-200">
                    <div class="text-xs font-bold text-slate-400 uppercase">عدد الأعطال المسجلة</div>
                    <div class="text-3xl font-extrabold text-red-600 mt-2" id="kpiPannes">--</div>
                    <div class="text-xs text-slate-500 mt-1">أوامر العمل التصحيحية (Correctif)</div>
                </div>
            </div>
        </section>

        <!-- 2. قسم GMAO -->
        <section id="sec-gmao" class="hidden space-y-6">
            <div class="bg-white p-4 rounded-xl shadow-sm border border-slate-200 flex justify-between items-center">
                <h2 class="text-lg font-bold text-slate-800"><i class="fa-solid fa-list-check text-indigo-600 ml-2"></i>أوامر العمل (Ordres de Travail)</h2>
                <button onclick="openOtModal()" class="bg-indigo-600 hover:bg-indigo-700 text-white px-4 py-2 rounded-lg text-sm font-semibold shadow">
                    <i class="fa-solid fa-plus ml-1"></i> إضافة أمر عمل جديد
                </button>
            </div>

            <div class="bg-white rounded-xl shadow-sm border border-slate-200 overflow-hidden">
                <table class="w-full text-right border-collapse text-sm">
                    <thead class="bg-slate-800 text-white uppercase text-xs">
                        <tr>
                            <th class="p-3">رقم الأمر</th>
                            <th class="p-3">الآلة / المعدة</th>
                            <th class="p-3">النوع</th>
                            <th class="p-3">مدة التوقف (ساعة)</th>
                            <th class="p-3">الوصف</th>
                            <th class="p-3">الأولوية</th>
                        </tr>
                    </thead>
                    <tbody id="table-ot-body" class="divide-y divide-slate-200"></tbody>
                </table>
            </div>
        </section>

        <!-- 3. قسم الوثائق التقنية -->
        <section id="sec-docs" class="hidden space-y-6">
            <div class="bg-white p-4 rounded-xl shadow-sm border border-slate-200 flex justify-between items-center">
                <h2 class="text-lg font-bold text-slate-800"><i class="fa-solid fa-book text-indigo-600 ml-2"></i>المكتَبة والوثائق التقنية</h2>
            </div>
            <div class="grid grid-cols-1 md:grid-cols-3 gap-4">
                <div class="bg-white p-4 rounded-xl border border-slate-200 shadow-sm flex items-center justify-between">
                    <div>
                        <h4 class="font-bold text-slate-800">مخطط الكهرباء P-01</h4>
                        <p class="text-xs text-slate-500">PDF • 2.4 MB</p>
                    </div>
                    <button class="text-indigo-600 hover:text-indigo-800"><i class="fa-solid fa-download text-xl"></i></button>
                </div>
                <div class="bg-white p-4 rounded-xl border border-slate-200 shadow-sm flex items-center justify-between">
                    <div>
                        <h4 class="font-bold text-slate-800">دليل الصيانة M-02</h4>
                        <p class="text-xs text-slate-500">PDF • 5.1 MB</p>
                    </div>
                    <button class="text-indigo-600 hover:text-indigo-800"><i class="fa-solid fa-download text-xl"></i></button>
                </div>
            </div>
        </section>

        <!-- 4. قسم AMDEC -->
        <section id="sec-amdec" class="hidden space-y-6">
            <div class="bg-white p-4 rounded-xl shadow-sm border border-slate-200">
                <h2 class="text-lg font-bold text-slate-800"><i class="fa-solid fa-shield-halved text-indigo-600 ml-2"></i>تحليلات AMDEC</h2>
            </div>
            <div class="bg-white rounded-xl shadow-sm border border-slate-200 overflow-hidden">
                <table class="w-full text-right border-collapse text-sm">
                    <thead class="bg-slate-800 text-white uppercase text-xs">
                        <tr>
                            <th class="p-3">المعدة</th>
                            <th class="p-3">نمط الفشل</th>
                            <th class="p-3 text-center">G</th>
                            <th class="p-3 text-center">O</th>
                            <th class="p-3 text-center">D</th>
                            <th class="p-3 text-center">NPR</th>
                            <th class="p-3">الإجراء الوقائي</th>
                        </tr>
                    </thead>
                    <tbody id="table-amdec-body" class="divide-y divide-slate-200"></tbody>
                </table>
            </div>
        </section>

        <!-- 5. قسم DMAIC -->
        <section id="sec-dmaic" class="hidden space-y-6">
            <div class="bg-white p-4 rounded-xl shadow-sm border border-slate-200">
                <h2 class="text-lg font-bold text-slate-800"><i class="fa-solid fa-diagram-project text-indigo-600 ml-2"></i>مشاريع التحسين المستمر (DMAIC)</h2>
            </div>
            <div class="grid grid-cols-1 md:grid-cols-2 gap-6" id="dmaic-projects-container"></div>
        </section>

    </main>
</div>

<!-- Modal إضافة OT -->
<div id="modalOt" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm hidden z-50 flex justify-center items-center p-4">
    <div class="bg-white rounded-xl shadow-xl w-full max-w-md overflow-hidden text-right">
        <div class="bg-slate-900 text-white px-6 py-4 flex justify-between items-center">
            <h3 class="font-bold">إضافة أمر عمل (OT)</h3>
            <button onclick="closeOtModal()" class="text-slate-400 hover:text-white"><i class="fa-solid fa-xmark"></i></button>
        </div>
        <form onsubmit="saveOt(event)" class="p-6 space-y-4">
            <div>
                <label class="block text-xs font-bold text-slate-700 uppercase mb-1">الآلة / المعدة</label>
                <select id="otEquipement" class="w-full border rounded p-2 text-sm outline-none">
                    <option value="الضاغط الهيدروليكي P-01">الضاغط الهيدروليكي P-01</option>
                    <option value="المحرك الرئيسي M-02">المحرك الرئيسي M-02</option>
                    <option value="خط البثق B-02">خط البثق B-02</option>
                </select>
            </div>
            <div>
                <label class="block text-xs font-bold text-slate-700 uppercase mb-1">نوع التدخل</label>
                <select id="otType" class="w-full border rounded p-2 text-sm outline-none">
                    <option value="Correctif">تصحيحي عطل (Correctif)</option>
                    <option value="Préventif">وقائي مخطط (Préventif)</option>
                </select>
            </div>
            <div>
                <label class="block text-xs font-bold text-slate-700 uppercase mb-1">مدة التوقف / الإصلاح (بالساعات)</label>
                <input type="number" step="0.1" id="otDuree" required value="2.0" class="w-full border rounded p-2 text-sm outline-none text-left">
            </div>
            <div>
                <label class="block text-xs font-bold text-slate-700 uppercase mb-1">وصف العطل / التدخل</label>
                <textarea id="otDesc" required rows="2" class="w-full border rounded p-2 text-sm outline-none"></textarea>
            </div>
            <div>
                <label class="block text-xs font-bold text-slate-700 uppercase mb-1">الأولوية</label>
                <select id="otPriorite" class="w-full border rounded p-2 text-sm outline-none">
                    <option value="عالية">عالية</option>
                    <option value="متوسطة" selected>متوسطة</option>
                    <option value="منخفضة">منخفضة</option>
                </select>
            </div>
            <div class="flex justify-end gap-3 pt-4 border-t">
                <button type="button" onclick="closeOtModal()" class="px-4 py-2 border rounded text-sm font-semibold text-slate-600">إلغاء</button>
                <button type="submit" class="px-5 py-2 bg-indigo-600 text-white rounded text-sm font-semibold">حفظ الأمر</button>
            </div>
        </form>
    </div>
</div>

<!-- البرمجة السلوكية (JavaScript) -->
<script>
    const HEURES_OUVERTURE_MENSUEL = 160; // ساعات العمل الشهرية المخططة لكل آلة

    let dataOt = JSON.parse(localStorage.getItem('DATA_OT')) || [
        { id: 'OT-1001', equipement: 'الضاغط الهيدروليكي P-01', type: 'Correctif', duree: 3.5, desc: 'تسرب زيت الهيدروليك', priorite: 'عالية' },
        { id: 'OT-1002', equipement: 'الضاغط الهيدروليكي P-01', type: 'Correctif', duree: 1.5, desc: 'استبدال الصمام الرئيسي', priorite: 'عالية' },
        { id: 'OT-1003', equipement: 'المحرك الرئيسي M-02', type: 'Correctif', duree: 2.0, desc: 'ارتفاع حرارة المحمل', priorite: 'متوسطة' },
        { id: 'OT-1004', equipement: 'خط البثق B-02', type: 'Préventif', duree: 4.0, desc: 'صيانة وقائية شهرية', priorite: 'متوسطة' }
    ];

    let dataAmdec = [
        { equipement: 'P-01', mode: 'تسرب زيت', g: 6, o: 5, d: 4, npr: 120, action: 'تغيير المفاصل الوقائي' },
        { equipement: 'M-02', mode: 'تآكل المحمل', g: 8, o: 3, d: 3, npr: 72, action: 'تطبيق جدول تشحيم أسبوعي' }
    ];

    let dataDmaic = [
        { titre: 'تقليل أعطال الضاغط P-01', cible: 'الضاغط P-01', etape: 'A', objectif: 'رفع الجاهزية إلى 98%', progression: 60 }
    ];

    document.addEventListener("DOMContentLoaded", () => {
        checkSession();
        renderOt();
        renderAmdec();
        renderDmaic();
        updateKpiDashboard();
    });

    // حساب المؤشرات ديناميكياً
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

    // إدارة الجلسات والتنقل
    function handleLogin(e) {
        e.preventDefault();
        const user = { email: document.getElementById('loginEmail').value, role: document.getElementById('loginRole').value };
        localStorage.setItem('SESSION_USER', JSON.stringify(user));
        checkSession();
    }

    function handleLogout() {
        localStorage.removeItem('SESSION_USER');
        checkSession();
    }

    function checkSession() {
        const stored = localStorage.getItem('SESSION_USER');
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
        ['kpi', 'gmao', 'docs', 'amdec', 'dmaic'].forEach(t => {
            document.getElementById(`sec-${t}`).classList.add('hidden');
            document.getElementById(`tab-${t}`).classList.remove('text-indigo-600', 'border-indigo-600');
            document.getElementById(`tab-${t}`).classList.add('text-slate-600', 'border-transparent');
        });
        document.getElementById(`sec-${tab}`).classList.remove('hidden');
        document.getElementById(`tab-${tab}`).classList.add('text-indigo-600', 'border-indigo-600');
        document.getElementById(`tab-${tab}`).classList.remove('text-slate-600', 'border-transparent');
    }

    // عرض وتحديث البيانات
    function renderOt() {
        const tbody = document.getElementById('table-ot-body');
        tbody.innerHTML = '';
        dataOt.forEach(ot => {
            tbody.innerHTML += `
                <tr class="hover:bg-slate-50">
                    <td class="p-3 font-bold text-slate-800">${ot.id}</td>
                    <td class="p-3 font-semibold">${ot.equipement}</td>
                    <td class="p-3"><span class="px-2 py-0.5 rounded text-xs ${ot.type === 'Correctif' ? 'bg-red-100 text-red-700' : 'bg-blue-100 text-blue-700'}">${ot.type}</span></td>
                    <td class="p-3 font-bold text-slate-700">${ot.duree}</td>
                    <td class="p-3 text-slate-600">${ot.desc}</td>
                    <td class="p-3 font-bold text-xs ${ot.priorite === 'عالية' ? 'text-red-600' : 'text-slate-600'}">${ot.priorite}</td>
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
                    <td class="p-3 font-semibold text-slate-800">${item.equipement}</td>
                    <td class="p-3">${item.mode}</td>
                    <td class="p-3 text-center">${item.g}</td>
                    <td class="p-3 text-center">${item.o}</td>
                    <td class="p-3 text-center">${item.d}</td>
                    <td class="p-3 text-center font-bold text-red-600">${item.npr}</td>
                    <td class="p-3 text-slate-600">${item.action}</td>
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
                    <span class="text-xs font-bold bg-indigo-100 text-indigo-700 px-2.5 py-1 rounded">مرحلة ${p.etape}</span>
                    <h3 class="font-bold text-slate-800 text-lg">${p.titre}</h3>
                    <p class="text-xs text-slate-500">الهدف: ${p.objectif}</p>
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
            equipement: document.getElementById('otEquipement').value,
            type: document.getElementById('otType').value,
            duree: parseFloat(document.getElementById('otDuree').value),
            desc: document.getElementById('otDesc').value,
            priorite: document.getElementById('otPriorite').value
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
