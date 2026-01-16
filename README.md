<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>نظام إدارة وتقييم المناقشات | ربط المجموعات</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Tajawal:wght@400;500;700;900&display=swap" rel="stylesheet">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/PapaParse/5.3.2/papaparse.min.js"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        body { font-family: 'Tajawal', sans-serif; background-color: #f8fafc; }
        .score-input { border: 2px solid #e2e8f0; transition: all 0.2s; text-align: center; font-weight: 700; }
        .score-input:focus { border-color: #4f46e5; outline: none; background-color: #fffbeb; }
        .loading-overlay { position: fixed; inset: 0; background: rgba(255,255,255,0.9); display: flex; align-items: center; justify-content: center; z-index: 1000; }
        .glass-card { background: rgba(255, 255, 255, 0.95); backdrop-filter: blur(10px); }
        .result-badge { font-size: 10px; padding: 4px 12px; border-radius: 9999px; font-weight: 900; text-transform: uppercase; letter-spacing: 0.05em; }
        .print-only { display: none; }
        @media print { 
            .no-print { display: none !important; } 
            .print-only { display: block !important; } 
            .student-card { break-inside: avoid; }
            .print-header { background: linear-gradient(135deg, #4f46e5, #3730a3) !important; -webkit-print-color-adjust: exact; }
        }
    </style>
</head>
<body class="p-4 md:p-6">

    <!-- شاشة التحميل -->
    <div id="loading" class="loading-overlay hidden">
        <div class="text-center">
            <div class="inline-block animate-spin rounded-full h-16 w-16 border-4 border-indigo-600 border-t-transparent"></div>
            <p class="mt-4 font-black text-indigo-900 text-lg">جاري مزامنة البيانات السحابية...</p>
        </div>
    </div>

    <!-- ترويسة الطباعة -->
    <div class="print-only bg-gradient-to-br from-indigo-600 to-indigo-900 text-white p-8 rounded-[2rem] mb-6 print-header">
        <div class="text-center">
            <h1 class="text-3xl font-black">تقرير تقييم المشاريع النهائية</h1>
            <p class="mt-2 opacity-90">كلية الهندسة - قسم الحاسب الآلي</p>
            <div class="flex justify-center gap-8 mt-4 text-sm">
                <span id="print-date"></span>
                <span id="print-role"></span>
            </div>
        </div>
    </div>

    <div id="app" class="max-w-7xl mx-auto space-y-6">
        
        <!-- الواجهة الرئيسية / اختيار الدور -->
        <div id="roleSelection" class="bg-white p-12 rounded-[3rem] shadow-2xl text-center no-print border border-slate-200">
            <div class="mb-8">
                <span class="bg-indigo-100 text-indigo-700 px-4 py-1 rounded-full text-sm font-bold">الإصدار 2.1 - محسّن</span>
                <h2 class="text-4xl font-black mt-4 text-slate-800">نظام تنظيم وتقييم التخرج</h2>
                <p class="text-slate-500 mt-2">إدارة المجموعات، القاعات، ورصد الدرجات بشكل متكامل</p>
            </div>

            <div class="flex flex-wrap justify-center gap-4 mb-12">
                <button onclick="fetchSheetData()" class="flex items-center gap-2 bg-indigo-50 text-indigo-600 px-6 py-3 rounded-2xl font-bold border border-indigo-100 hover:bg-indigo-100 transition-all">
                    <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15"></path>
                    </svg>
                    تحديث البيانات من الرابط
                </button>
                <button onclick="toggleAdminView()" class="flex items-center gap-2 bg-slate-800 text-white px-6 py-3 rounded-2xl font-bold hover:bg-slate-900 transition-all">
                    <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10.325 4.317c.426-1.756 2.924-1.756 3.35 0a1.724 1.724 0 002.573 1.066c1.543-.94 3.31.826 2.37 2.37a1.724 1.724 0 001.065 2.572c1.756.426 1.756 2.924 0 3.35a1.724 1.724 0 00-1.066 2.573c.94 1.543-.826 3.31-2.37 2.37a1.724 1.724 0 00-2.572 1.065c-.426 1.756-2.924 1.756-3.35 0a1.724 1.724 0 00-2.573-1.066c-1.543.94-3.31-.826-2.37-2.37a1.724 1.724 0 00-1.065-2.572c-1.756-.426-1.756-2.924 0-3.35a1.724 1.724 0 001.066-2.573c-.94-1.543.826-3.31 2.37-2.37.996.608 2.296.07 2.572-1.065z"></path>
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z"></path>
                    </svg>
                    لوحة التحكم الإحصائية
                </button>
                <button onclick="exportAllData()" class="flex items-center gap-2 bg-emerald-50 text-emerald-600 px-6 py-3 rounded-2xl font-bold border border-emerald-100 hover:bg-emerald-100 transition-all">
                    <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 10v6m0 0l-3-3m3 3l3-3m2 8H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z"></path>
                    </svg>
                    تصدير جميع البيانات
                </button>
            </div>

            <div class="grid grid-cols-1 md:grid-cols-2 gap-8 max-w-3xl mx-auto">
                <button onclick="setRole('supervisor')" class="p-10 bg-white border-4 border-indigo-600 rounded-[2.5rem] hover:bg-indigo-600 hover:text-white transition-all shadow-xl group">
                    <div class="text-5xl mb-4 group-hover:scale-110 transition-transform">📝</div>
                    <div class="text-2xl font-black">تقييم المشرف</div>
                    <p class="text-sm mt-2 opacity-70">رصد أعمال الفصل والمتابعة المستمرة</p>
                </button>
                
                <button onclick="setRole('examiner')" class="p-10 bg-white border-4 border-emerald-600 rounded-[2.5rem] hover:bg-emerald-600 hover:text-white transition-all shadow-xl group">
                    <div class="text-5xl mb-4 group-hover:scale-110 transition-transform">🎓</div>
                    <div class="text-2xl font-black">تقييم المناقش</div>
                    <p class="text-sm mt-2 opacity-70">تقييم لجنة الحكم والعرض النهائي</p>
                </button>
            </div>
        </div>

        <!-- لوحة التحكم الإحصائية -->
        <div id="adminDashboard" class="hidden space-y-6 no-print">
            <div class="flex items-center justify-between bg-slate-900 text-white p-8 rounded-[2rem]">
                <div>
                    <h2 class="text-2xl font-black">لوحة التحكم الإحصائية</h2>
                    <p class="opacity-70">مراقبة توزيع المجموعات والقاعات والإحصائيات الشاملة</p>
                </div>
                <div class="flex gap-2">
                    <button onclick="exportStatistics()" class="bg-white/10 px-4 py-2 rounded-xl hover:bg-white/20 text-sm">
                        تصدير الإحصائيات
                    </button>
                    <button onclick="toggleAdminView()" class="bg-white/10 px-6 py-2 rounded-xl hover:bg-white/20">
                        رجوع للخلف
                    </button>
                </div>
            </div>

            <!-- إحصائيات سريعة -->
            <div class="grid grid-cols-1 md:grid-cols-4 gap-4">
                <div class="bg-white p-6 rounded-3xl border border-slate-200 shadow-sm">
                    <div class="text-slate-400 text-xs font-bold uppercase mb-1">إجمالي المجموعات</div>
                    <div id="statGroups" class="text-3xl font-black text-indigo-600">0</div>
                </div>
                <div class="bg-white p-6 rounded-3xl border border-slate-200 shadow-sm">
                    <div class="text-slate-400 text-xs font-bold uppercase mb-1">إجمالي الطلاب</div>
                    <div id="statStudents" class="text-3xl font-black text-emerald-600">0</div>
                </div>
                <div class="bg-white p-6 rounded-3xl border border-slate-200 shadow-sm">
                    <div class="text-slate-400 text-xs font-bold uppercase mb-1">عدد القاعات</div>
                    <div id="statRooms" class="text-3xl font-black text-orange-600">0</div>
                </div>
                <div class="bg-white p-6 rounded-3xl border border-slate-200 shadow-sm">
                    <div class="text-slate-400 text-xs font-bold uppercase mb-1">المشرفين</div>
                    <div id="statSupervisors" class="text-3xl font-black text-purple-600">0</div>
                </div>
            </div>

            <!-- رسومات بيانية -->
            <div class="grid grid-cols-1 lg:grid-cols-2 gap-6">
                <div class="bg-white p-6 rounded-3xl border border-slate-200 shadow-sm">
                    <h3 class="font-black text-slate-700 mb-4">توزيع الطلاب على القاعات</h3>
                    <canvas id="roomsChart"></canvas>
                </div>
                <div class="bg-white p-6 rounded-3xl border border-slate-200 shadow-sm">
                    <h3 class="font-black text-slate-700 mb-4">توزيع المشرفين</h3>
                    <canvas id="supervisorsChart"></canvas>
                </div>
            </div>

            <!-- جدول البيانات -->
            <div class="bg-white rounded-[2rem] shadow-sm border border-slate-200 overflow-hidden">
                <div class="p-4 border-b border-slate-100">
                    <input type="text" placeholder="🔍 بحث عن طالب أو قاعة أو مشروع..." 
                           class="w-full p-4 bg-slate-50 rounded-2xl outline-none font-bold text-slate-700"
                           oninput="filterAdminTable(this.value)">
                </div>
                <table class="w-full text-right">
                    <thead class="bg-slate-50 border-b border-slate-200">
                        <tr>
                            <th class="p-4 font-black text-slate-600">القاعة</th>
                            <th class="p-4 font-black text-slate-600">رقم المجموعة</th>
                            <th class="p-4 font-black text-slate-600">اسم المشروع</th>
                            <th class="p-4 font-black text-slate-600">الطلاب</th>
                            <th class="p-4 font-black text-slate-600">المشرف</th>
                            <th class="p-4 font-black text-slate-600">خيارات</th>
                        </tr>
                    </thead>
                    <tbody id="adminTableBody">
                        <!-- تظهر هنا البيانات -->
                    </tbody>
                </table>
            </div>
        </div>

        <!-- نموذج التقييم الرئيسي -->
        <div id="mainContainer" class="hidden bg-white shadow-2xl rounded-[3rem] overflow-hidden border border-slate-200">
            <div id="formHeader" class="p-10 text-white text-center relative transition-all duration-500">
                <button onclick="location.reload()" class="absolute top-8 left-8 bg-white/20 px-4 py-2 rounded-full text-xs font-bold hover:bg-white/30 transition-all no-print">
                    🏠 العودة للرئيسية
                </button>
                <button onclick="printEvaluation()" class="absolute top-8 right-8 bg-white/20 px-4 py-2 rounded-full text-xs font-bold hover:bg-white/30 transition-all no-print">
                    🖨️ طباعة التقرير
                </button>
                <h1 id="headerTitle" class="text-4xl font-black"></h1>
                <p id="headerSubtitle" class="mt-2 opacity-80 font-medium"></p>
            </div>

            <form id="evaluationForm" class="p-6 md:p-10 space-y-10">
                <!-- معلومات المجموعة -->
                <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6 p-8 bg-slate-50 rounded-[2rem] border border-slate-100 no-print">
                    <div class="space-y-1">
                        <label class="block font-black text-slate-500 text-xs px-2">رقم المجموعة / القاعة</label>
                        <select id="projectSelect" class="w-full p-4 bg-white border-2 border-slate-200 rounded-2xl outline-none font-bold text-indigo-700 shadow-sm focus:border-indigo-500" onchange="handleProjectChange()">
                            <option value="">-- اختر المجموعة --</option>
                        </select>
                    </div>
                    <div class="space-y-1">
                        <label class="block font-black text-slate-500 text-xs px-2">اسم المشروع</label>
                        <input type="text" id="projNameDisplay" class="w-full p-4 bg-white border-2 border-slate-100 rounded-2xl font-bold text-slate-700" readonly>
                    </div>
                    <div class="space-y-1">
                        <label class="block font-black text-slate-500 text-xs px-2">المشرف</label>
                        <input type="text" id="supName" class="w-full p-4 bg-white border-2 border-slate-100 rounded-2xl font-bold text-slate-700" readonly>
                    </div>
                    <div class="space-y-1">
                        <label class="block font-black text-slate-500 text-xs px-2">التاريخ</label>
                        <input type="date" id="evalDate" class="w-full p-4 bg-white border-2 border-slate-200 rounded-2xl font-bold" value="">
                    </div>
                </div>

                <!-- بطاقات الطلاب -->
                <div class="grid grid-cols-1 md:grid-cols-2 xl:grid-cols-3 gap-8" id="studentsWrapper"></div>

                <!-- ملخص النتائج -->
                <div id="resultsSummary" class="hidden bg-white border-2 border-slate-100 rounded-[2rem] p-8">
                    <h3 class="text-2xl font-black text-slate-800 mb-6">ملخص النتائج</h3>
                    <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                        <div class="text-center p-6 bg-slate-50 rounded-2xl">
                            <div class="text-4xl font-black text-emerald-600" id="summaryPassed">0</div>
                            <div class="text-sm font-bold text-slate-500 mt-2">عدد الناجحين</div>
                        </div>
                        <div class="text-center p-6 bg-slate-50 rounded-2xl">
                            <div class="text-4xl font-black text-rose-600" id="summaryFailed">0</div>
                            <div class="text-sm font-bold text-slate-500 mt-2">عدد الراسبين</div>
                        </div>
                        <div class="text-center p-6 bg-slate-50 rounded-2xl">
                            <div class="text-4xl font-black text-indigo-600" id="summaryAvg">0</div>
                            <div class="text-sm font-bold text-slate-500 mt-2">متوسط الدرجات</div>
                        </div>
                    </div>
                </div>

                <!-- أزرار التحكم -->
                <div class="pt-8 flex flex-wrap justify-center gap-4 no-print">
                    <button type="button" onclick="saveToCloud()" class="bg-indigo-600 text-white px-12 py-5 rounded-[1.5rem] font-black hover:bg-indigo-700 transition-all shadow-xl active:scale-95 flex items-center gap-2">
                        <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M8 7H5a2 2 0 00-2 2v9a2 2 0 002 2h14a2 2 0 002-2V9a2 2 0 00-2-2h-3m-1 4l-3 3m0 0l-3-3m3 3V4"></path>
                        </svg>
                        حفظ النتائج سحابياً
                    </button>
                    <button type="button" onclick="exportEvaluation()" class="bg-emerald-600 text-white px-8 py-5 rounded-[1.5rem] font-black hover:bg-emerald-700 transition-all shadow-xl active:scale-95 flex items-center gap-2">
                        <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 10v6m0 0l-3-3m3 3l3-3m2 8H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z"></path>
                        </svg>
                        تصدير النتائج
                    </button>
                    <button type="button" onclick="saveToLocal()" class="bg-slate-600 text-white px-8 py-5 rounded-[1.5rem] font-black hover:bg-slate-700 transition-all shadow-xl active:scale-95 flex items-center gap-2">
                        💾 حفظ محلي
                    </button>
                </div>
            </form>
        </div>
    </div>

    <!-- نموذج بطاقة الطالب -->
    <template id="studentTemplate">
        <div class="student-card bg-white border-2 border-slate-100 rounded-[2.5rem] p-8 shadow-sm flex flex-col h-full">
            <div class="flex justify-between items-start mb-6">
                <div>
                    <h4 class="student-name-display text-2xl font-black text-slate-800"></h4>
                    <span class="text-xs font-bold text-slate-400">طالب في المجموعة</span>
                </div>
                <div class="h-12 w-12 bg-indigo-50 rounded-2xl flex items-center justify-center text-2xl">👤</div>
            </div>
            <div class="criteria-list space-y-4 flex-grow"></div>
            <div class="mt-8 pt-6 border-t border-slate-50 flex justify-between items-end">
                <div>
                    <span class="text-4xl font-black text-indigo-600 student-total-display">0</span>
                    <span class="text-sm font-bold text-slate-400">/ 100</span>
                </div>
                <div class="result-badge student-result-text bg-slate-100 text-slate-400">قيد التقييم</div>
            </div>
        </div>
    </template>

    <script>
        const SHEET_ID = '1Ne4jRjMj75t2zk-w9bCoO2jZlkJkOIxtQAlqNG91p3U';
        const SHEET_URL = `[https://docs.google.com/spreadsheets/d/${SHEET_ID}/gviz/tq?tqx=out](https://docs.google.com/spreadsheets/d/1Ne4jRjMj75t2zk-w9bCoO2jZlkJkOIxtQAlqNG91p3U/edit?usp=sharing):csv`;
        const APPS_SCRIPT_URL = 'https://docs.google.com/spreadsheets/d/1Ne4jRjMj75t2zk-w9bCoO2jZlkJkOIxtQAlqNG91p3U/edit?usp=sharing'; // ضع رابط النشر الخاص بك هنا

        let db = [];
        let currentRole = '';
        let currentProject = null;
        let charts = {};

        const roles = {
            supervisor: { 
                title: "تقييم المشرف", 
                subtitle: "درجات أعمال الفصل والمتابعة المستمرة", 
                color: "from-indigo-600 to-indigo-900", 
                criteria: [
                    {id:'c1', label:'التوثيق والمستندات', max:25},
                    {id:'c2', label:'الجانب العملي والتطبيق', max:35},
                    {id:'c3', label:'المتابعة والحضور', max:20},
                    {id:'c4', label:'الأداء العام', max:20}
                ] 
            },
            examiner: { 
                title: "تقييم المناقش", 
                subtitle: "درجات اللجنة والعرض النهائي", 
                color: "from-emerald-600 to-emerald-900", 
                criteria: [
                    {id:'c1', label:'التقرير النهائي', max:25},
                    {id:'c2', label:'الجودة البرمجية', max:25},
                    {id:'c3', label:'المناقشة والحوار', max:25},
                    {id:'c4', label:'عرض المشروع', max:25}
                ] 
            }
        };

        // تهيئة التاريخ الحالي
        document.getElementById('evalDate').valueAsDate = new Date();

        async function fetchSheetData() {
            showLoading(true);
            try {
                const response = await fetch(SHEET_URL);
                const csvData = await response.text();
                
                Papa.parse(csvData, {
                    header: true,
                    skipEmptyLines: true,
                    complete: (results) => {
                        const raw = results.data;
                        let projects = {};
                        
                        raw.forEach(row => {
                            const pName = row['اسم المشروع'] || row['Project Name'];
                            const sName = row['اسم الطالب'] || row['Student Name'];
                            const sup = row['اسم المشرف'] || row['Supervisor'];
                            const gId = row['رقم المجموعة'] || row['Group ID'] || 'N/A';
                            const room = row['رقم القاعة'] || row['Room'] || 'N/A';
                            
                            if(pName && sName) {
                                const key = `${gId}-${pName}`;
                                if(!projects[key]) {
                                    projects[key] = { 
                                        title: pName, 
                                        supervisor: sup, 
                                        groupId: gId, 
                                        room: room,
                                        students: [] 
                                    };
                                }
                                projects[key].students.push(sName);
                            }
                        });
                        
                        db = Object.values(projects);
                        localStorage.setItem('grad_db_v3', JSON.stringify(db));
                        updateAdminStats();
                        updateCharts();
                        showNotification('تم تحديث بيانات المجموعات والقاعات بنجاح!', 'success');
                    },
                    error: (error) => {
                        showNotification('خطأ في تحليل البيانات', 'error');
                    }
                });
            } catch (err) {
                showNotification('خطأ في جلب البيانات من السحابة', 'error');
            } finally {
                showLoading(false);
            }
        }

        function showLoading(show) {
            document.getElementById('loading').classList.toggle('hidden', !show);
        }

        function showNotification(message, type = 'info') {
            const colors = {
                success: 'bg-emerald-100 border-emerald-300 text-emerald-700',
                error: 'bg-rose-100 border-rose-300 text-rose-700',
                info: 'bg-indigo-100 border-indigo-300 text-indigo-700'
            };
            
            const notification = document.createElement('div');
            notification.className = `fixed top-4 left-4 right-4 md:left-auto md:right-4 md:w-96 p-4 rounded-2xl border-2 ${colors[type]} font-bold shadow-xl z-50 transition-all transform translate-y-0`;
            notification.innerHTML = `
                <div class="flex items-center gap-3">
                    <div class="text-2xl">${type === 'success' ? '✅' : type === 'error' ? '❌' : 'ℹ️'}</div>
                    <div>${message}</div>
                </div>
            `;
            
            document.body.appendChild(notification);
            
            setTimeout(() => {
                notification.style.transform = 'translateY(-150%)';
                setTimeout(() => notification.remove(), 300);
            }, 3000);
        }

        function toggleAdminView() {
            const roleDiv = document.getElementById('roleSelection');
            const adminDiv = document.getElementById('adminDashboard');
            if(adminDiv.classList.contains('hidden')) {
                roleDiv.classList.add('hidden');
                adminDiv.classList.remove('hidden');
                renderAdminTable(db);
                updateAdminStats();
                updateCharts();
            } else {
                adminDiv.classList.add('hidden');
                roleDiv.classList.remove('hidden');
            }
        }

        function updateAdminStats() {
            if(db.length === 0) return;
            
            document.getElementById('statGroups').innerText = db.length;
            document.getElementById('statStudents').innerText = db.reduce((acc, p) => acc + p.students.length, 0);
            
            const rooms = [...new Set(db.map(p => p.room))].filter(r => r !== 'N/A');
            document.getElementById('statRooms').innerText = rooms.length;
            
            const supervisors = [...new Set(db.map(p => p.supervisor))].filter(s => s);
            document.getElementById('statSupervisors').innerText = supervisors.length;
        }

        function updateCharts() {
            if(db.length === 0) return;
            
            // توزيع الطلاب على القاعات
            const roomData = {};
            db.forEach(p => {
                const room = p.room || 'غير محدد';
                roomData[room] = (roomData[room] || 0) + p.students.length;
            });
            
            const roomsCtx = document.getElementById('roomsChart').getContext('2d');
            if(charts.rooms) charts.rooms.destroy();
            
            charts.rooms = new Chart(roomsCtx, {
                type: 'bar',
                data: {
                    labels: Object.keys(roomData),
                    datasets: [{
                        label: 'عدد الطلاب',
                        data: Object.values(roomData),
                        backgroundColor: [
                            'rgba(79, 70, 229, 0.7)',
                            'rgba(16, 185, 129, 0.7)',
                            'rgba(245, 158, 11, 0.7)',
                            'rgba(239, 68, 68, 0.7)',
                            'rgba(139, 92, 246, 0.7)'
                        ],
                        borderColor: [
                            'rgb(79, 70, 229)',
                            'rgb(16, 185, 129)',
                            'rgb(245, 158, 11)',
                            'rgb(239, 68, 68)',
                            'rgb(139, 92, 246)'
                        ],
                        borderWidth: 2
                    }]
                },
                options: {
                    responsive: true,
                    plugins: {
                        legend: { display: false },
                        title: { display: false }
                    }
                }
            });
            
            // توزيع المشرفين
            const supData = {};
            db.forEach(p => {
                const sup = p.supervisor || 'غير محدد';
                supData[sup] = (supData[sup] || 0) + 1;
            });
            
            const supCtx = document.getElementById('supervisorsChart').getContext('2d');
            if(charts.supervisors) charts.supervisors.destroy();
            
            charts.supervisors = new Chart(supCtx, {
                type: 'pie',
                data: {
                    labels: Object.keys(supData),
                    datasets: [{
                        data: Object.values(supData),
                        backgroundColor: [
                            'rgba(79, 70, 229, 0.7)',
                            'rgba(16, 185, 129, 0.7)',
                            'rgba(245, 158, 11, 0.7)',
                            'rgba(239, 68, 68, 0.7)',
                            'rgba(139, 92, 246, 0.7)'
                        ]
                    }]
                },
                options: {
                    responsive: true,
                    plugins: {
                        legend: {
                            position: 'bottom',
                            rtl: true
                        }
                    }
                }
            });
        }

        function renderAdminTable(data) {
            const body = document.getElementById('adminTableBody');
            body.innerHTML = data.map(p => `
                <tr class="border-b border-slate-100 hover:bg-slate-50 transition-colors">
                    <td class="p-4 font-bold text-orange-600">
                        <div class="flex items-center gap-2">
                            <div class="h-3 w-3 rounded-full bg-orange-500"></div>
                            قاعة ${p.room}
                        </div>
                    </td>
                    <td class="p-4 font-black">#${p.groupId}</td>
                    <td class="p-4 font-medium">${p.title}</td>
                    <td class="p-4">
                        <div class="flex flex-wrap gap-1">
                            ${p.students.map(s => `<span class="bg-slate-100 px-3 py-1.5 rounded-xl text-xs font-bold text-slate-600">${s}</span>`).join('')}
                        </div>
                    </td>
                    <td class="p-4">
                        <span class="bg-indigo-50 text-indigo-700 px-3 py-1.5 rounded-xl text-xs font-bold">${p.supervisor}</span>
                    </td>
                    <td class="p-4">
                        <button onclick="loadProjectForEvaluation('${p.groupId}', '${p.title}')" class="text-xs bg-slate-100 hover:bg-slate-200 px-3 py-1.5 rounded-lg font-bold transition-colors">
                            📝 تقييم
                        </button>
                    </td>
                </tr>
            `).join('');
        }

        function filterAdminTable(query) {
            const filtered = db.filter(p => 
                p.title.includes(query) || 
                p.room.includes(query) || 
                p.groupId.includes(query) ||
                p.supervisor.includes(query) ||
                p.students.some(s => s.includes(query))
            );
            renderAdminTable(filtered);
        }

        function setRole(role) {
            if(db.length === 0) {
                const local = localStorage.getItem('grad_db_v3');
                if(local) {
                    db = JSON.parse(local);
                } else {
                    return showNotification('الرجاء تحديث البيانات أولاً', 'error');
                }
            }
            
            currentRole = role;
            document.getElementById('roleSelection').classList.add('hidden');
            document.getElementById('mainContainer').classList.remove('hidden');
            const cfg = roles[role];
            
            // تحديث الترويسة
            const header = document.getElementById('formHeader');
            header.className = `p-10 text-white text-center relative bg-gradient-to-br ${cfg.color}`;
            document.getElementById('headerTitle').innerText = cfg.title;
            document.getElementById('headerSubtitle').innerText = cfg.subtitle;
            
            // تحديث خيارات المجموعات
            const sel = document.getElementById('projectSelect');
            sel.innerHTML = '<option value="">-- اختر المجموعة --</option>' + 
                db.map(p => `
                    <option value="${p.groupId}|${p.title}">
                        قاعة ${p.room} | مجموعة ${p.groupId} - ${p.title.substring(0, 30)}${p.title.length > 30 ? '...' : ''}
                    </option>
                `).join('');
        }

        function loadProjectForEvaluation(groupId, title) {
            setRole('supervisor');
            const select = document.getElementById('projectSelect');
            const option = Array.from(select.options).find(opt => opt.value === `${groupId}|${title}`);
            if(option) {
                select.value = option.value;
                handleProjectChange();
            }
        }

        function handleProjectChange() {
            const val = document.getElementById('projectSelect').value;
            if(!val) return;
            
            const [gId, title] = val.split('|');
            currentProject = db.find(p => p.groupId === gId && p.title === title);
            const wrap = document.getElementById('studentsWrapper');
            
            // تحديث معلومات المشروع
            document.getElementById('projNameDisplay').value = currentProject.title;
            document.getElementById('supName').value = currentProject.supervisor;
            wrap.innerHTML = '';
            
            // تحميل أي بيانات محفوظة مسبقاً
            const savedData = localStorage.getItem(`eval_${currentRole}_${gId}_${title}`);
            const savedScores = savedData ? JSON.parse(savedData) : {};
            
            // إنشاء بطاقات الطلاب
            currentProject.students.forEach(name => {
                const temp = document.getElementById('studentTemplate').content.cloneNode(true);
                const card = temp.querySelector('.student-card');
                card.dataset.studentName = name;
                card.querySelector('.student-name-display').innerText = name;
                
                const savedStudent = savedScores[name] || {};
                
                roles[currentRole].criteria.forEach(crit => {
                    const row = document.createElement('div');
                    row.innerHTML = `
                        <div class="flex justify-between text-[10px] font-black text-slate-400 mb-1 uppercase tracking-tighter">
                            <span>${crit.label}</span>
                            <span>أقصى ${crit.max}</span>
                        </div>
                        <input type="number" data-label="${crit.label}" min="0" max="${crit.max}" 
                               value="${savedStudent[crit.label] || 0}" 
                               class="score-input w-full p-3 rounded-2xl border-2" 
                               oninput="updateScore(this, ${crit.max})"
                               step="0.5">`;
                    card.querySelector('.criteria-list').appendChild(row);
                });
                wrap.appendChild(temp);
                
                // تحديث الإجمالي من البيانات المحفوظة
                if(savedStudent.total) {
                    card.querySelector('.student-total-display').innerText = savedStudent.total;
                    updateStudentResult(card, savedStudent.total);
                }
            });
            
            // إظهار ملخص النتائج
            document.getElementById('resultsSummary').classList.remove('hidden');
            updateSummary();
        }

        function updateScore(input, max) {
            let value = parseFloat(input.value) || 0;
            if(value > max) {
                value = max;
                input.value = max;
            }
            if(value < 0) {
                value = 0;
                input.value = 0;
            }
            
            const card = input.closest('.student-card');
            let total = 0;
            card.querySelectorAll('.score-input').forEach(i => total += (parseFloat(i.value) || 0));
            card.querySelector('.student-total-display').innerText = total.toFixed(1);
            
            updateStudentResult(card, total);
            updateSummary();
            saveToLocal(); // حفظ تلقائي
        }

        function updateStudentResult(card, total) {
            const badge = card.querySelector('.student-result-text');
            const passed = total >= 60;
            badge.innerText = passed ? "ناجح" : "راسب";
            badge.className = `result-badge ${passed ? 'bg-emerald-100 text-emerald-700' : 'bg-rose-100 text-rose-700'}`;
        }

        function updateSummary() {
            const cards = document.querySelectorAll('.student-card');
            let passed = 0;
            let failed = 0;
            let totalSum = 0;
            
            cards.forEach(card => {
                const total = parseFloat(card.querySelector('.student-total-display').innerText) || 0;
                totalSum += total;
                if(total >= 60) passed++; else failed++;
            });
            
            document.getElementById('summaryPassed').innerText = passed;
            document.getElementById('summaryFailed').innerText = failed;
            document.getElementById('summaryAvg').innerText = cards.length > 0 ? (totalSum / cards.length).toFixed(1) : '0';
        }

        function saveToLocal() {
            if(!currentProject) return;
            
            const studentsData = {};
            const cards = document.querySelectorAll('.student-card');
            
            cards.forEach(card => {
                const name = card.dataset.studentName;
                const scores = {};
                let total = 0;
                
                card.querySelectorAll('.score-input').forEach(input => {
                    scores[input.dataset.label] = parseFloat(input.value) || 0;
                    total += scores[input.dataset.label];
                });
                
                scores.total = total;
                studentsData[name] = scores;
            });
            
            const key = `eval_${currentRole}_${currentProject.groupId}_${currentProject.title}`;
            localStorage.setItem(key, JSON.stringify(studentsData));
            
            // إضافة إلى سجل التقييمات
            const evaluations = JSON.parse(localStorage.getItem('evaluations_history') || '[]');
            evaluations.push({
                role: currentRole,
                project: currentProject.title,
                groupId: currentProject.groupId,
                date: new Date().toISOString(),
                summary: {
                    passed: parseInt(document.getElementById('summaryPassed').innerText),
                    failed: parseInt(document.getElementById('summaryFailed').innerText),
                    average: parseFloat(document.getElementById('summaryAvg').innerText)
                }
            });
            localStorage.setItem('evaluations_history', JSON.stringify(evaluations));
        }

        async function saveToCloud() {
            if(!APPS_SCRIPT_URL) {
                showNotification('تنبيه: لم يتم ضبط رابط الحفظ السحابي. تم الحفظ محلياً فقط.', 'info');
                saveToLocal();
                return;
            }
            
            showLoading(true);
            try {
                const data = {
                    role: currentRole,
                    project: currentProject,
                    date: document.getElementById('evalDate').value,
                    scores: getCurrentScores()
                };
                
                const response = await fetch(APPS_SCRIPT_URL, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(data)
                });
                
                if(response.ok) {
                    showNotification('تم حفظ النتائج بنجاح في السحابة!', 'success');
                } else {
                    throw new Error('فشل في الحفظ');
                }
            } catch (err) {
                showNotification('خطأ في الحفظ السحابي. تم الحفظ محلياً فقط.', 'error');
                saveToLocal();
            } finally {
                showLoading(false);
            }
        }

        function getCurrentScores() {
            const scores = {};
            document.querySelectorAll('.student-card').forEach(card => {
                const name = card.dataset.studentName;
                scores[name] = {
                    total: parseFloat(card.querySelector('.student-total-display').innerText) || 0,
                    criteria: {}
                };
                
                card.querySelectorAll('.score-input').forEach(input => {
                    scores[name].criteria[input.dataset.label] = parseFloat(input.value) || 0;
                });
            });
            return scores;
        }

        function exportEvaluation() {
            if(!currentProject) return;
            
            const data = {
                مشروع: currentProject.title,
                المجموعة: currentProject.groupId,
                القاعة: currentProject.room,
                المشرف: currentProject.supervisor,
                التاريخ: document.getElementById('evalDate').value,
                الدور: roles[currentRole].title,
                النتائج: []
            };
            
            document.querySelectorAll('.student-card').forEach(card => {
                const student = {
                    الطالب: card.dataset.studentName,
                    الإجمالي: parseFloat(card.querySelector('.student-total-display').innerText) || 0,
                    الحالة: card.querySelector('.student-result-text').innerText
                };
                
                card.querySelectorAll('.score-input').forEach(input => {
                    student[input.dataset.label] = parseFloat(input.value) || 0;
                });
                
                data.النتائج.push(student);
            });
            
            // تصدير كملف JSON
            const json = JSON.stringify(data, null, 2);
            const blob = new Blob([json], { type: 'application/json' });
            const url = URL.createObjectURL(blob);
            const a = document.createElement('a');
            a.href = url;
            a.download = `تقييم_${currentProject.groupId}_${currentProject.title.replace(/\s+/g, '_')}.json`;
            a.click();
            
            showNotification('تم تصدير النتائج بنجاح!', 'success');
        }

        function exportAllData() {
            const data = {
                تاريخ_التصدير: new Date().toISOString(),
                مجموعات_المشاريع: db,
                سجل_التقييمات: JSON.parse(localStorage.getItem('evaluations_history') || '[]')
            };
            
            // تصدير كملف CSV
            let csv = 'المجموعة,القاعة,المشروع,المشرف,عدد الطلاب\n';
            db.forEach(p => {
                csv += `${p.groupId},${p.room},${p.title},${p.supervisor},${p.students.length}\n`;
            });
            
            const blob = new Blob([csv], { type: 'text/csv;charset=utf-8' });
            const url = URL.createObjectURL(blob);
            const a = document.createElement('a');
            a.href = url;
            a.download = `جميع_البيانات_${new Date().toISOString().split('T')[0]}.csv`;
            a.click();
            
            showNotification('تم تصدير جميع البيانات!', 'success');
        }

        function exportStatistics() {
            const stats = {
                مجموعات: db.length,
                طلاب: db.reduce((acc, p) => acc + p.students.length, 0),
                قاعات: [...new Set(db.map(p => p.room))].length,
                مشرفين: [...new Set(db.map(p => p.supervisor))].length,
                تاريخ: new Date().toISOString()
            };
            
            const json = JSON.stringify(stats, null, 2);
            const blob = new Blob([json], { type: 'application/json' });
            const url = URL.createObjectURL(blob);
            const a = document.createElement('a');
            a.href = url;
            a.download = `إحصائيات_${new Date().toISOString().split('T')[0]}.json`;
            a.click();
        }

        function printEvaluation() {
            // تحديث بيانات الطباعة
            document.getElementById('print-date').innerText = `التاريخ: ${new Date().toLocaleDateString('ar-SA')}`;
            document.getElementById('print-role').innerText = `الدور: ${roles[currentRole].title}`;
            
            // طباعة الصفحة
            window.print();
        }

        // تهيئة التطبيق عند التحميل
        window.onload = () => {
            const local = localStorage.getItem('grad_db_v3');
            if(local) {
                db = JSON.parse(local);
                updateAdminStats();
                showNotification('تم تحميل البيانات المحفوظة', 'info');
            }
            
            // تهيئة التاريخ
            document.getElementById('evalDate').valueAsDate = new Date();
        };
    </script>
</body>
</html>
