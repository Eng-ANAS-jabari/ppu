<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>نظام إدارة المناقشات | الإصدار النهائي المطور</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Tajawal:wght@400;500;700;900&display=swap" rel="stylesheet">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/PapaParse/5.3.2/papaparse.min.js"></script>
    <style>
        body { font-family: 'Tajawal', sans-serif; background-color: #f8fafc; color: #1e293b; }
        .score-input { border: 2px solid #e2e8f0; text-align: center; font-weight: 700; transition: all 0.2s; }
        .score-input:focus { border-color: #4f46e5; background-color: #fefce8; outline: none; }
        .loading-overlay { position: fixed; inset: 0; background: rgba(255,255,255,0.95); display: flex; align-items: center; justify-content: center; z-index: 1000; }
        .tab-active { border-bottom: 4px solid #4f46e5; color: #4f46e5; }
        .glass-header { background: rgba(255, 255, 255, 0.8); backdrop-filter: blur(10px); }
        @media print { .no-print { display: none !important; } }
    </style>
</head>
<body class="p-2 md:p-6">

    <!-- شاشة التحميل -->
    <div id="loading" class="loading-overlay hidden">
        <div class="text-center">
            <div class="inline-block animate-spin rounded-full h-16 w-16 border-4 border-indigo-600 border-t-transparent"></div>
            <p class="mt-4 font-black text-indigo-900 text-lg">جاري جلب البيانات من Google Sheets...</p>
        </div>
    </div>

    <div id="app" class="max-w-7xl mx-auto space-y-6">
        
        <!-- الهيدر الرئيسي -->
        <header class="bg-white p-6 md:p-10 rounded-[2.5rem] shadow-xl border border-slate-200 text-center relative overflow-hidden">
            <div class="relative z-10">
                <div class="flex justify-center mb-4">
                    <span class="bg-indigo-100 text-indigo-700 px-4 py-1 rounded-full text-xs font-bold uppercase tracking-widest">نظام الربط الذكي v3.1 (إصلاح المزامنة)</span>
                </div>
                <h1 class="text-3xl md:text-4xl font-black text-slate-800">إدارة مناقشات مشاريع التخرج</h1>
                <p class="text-slate-500 mt-2 font-medium">نظام الربط الآلي - يرجى التأكد من نشر الملف للويب</p>
                
                <div class="flex flex-wrap justify-center gap-3 mt-8 no-print">
                    <button onclick="fetchSheetData()" class="bg-indigo-600 text-white px-8 py-3 rounded-2xl font-bold hover:bg-indigo-700 transition-all shadow-lg flex items-center gap-2">
                        <span>🔄</span> تحديث البيانات الآن
                    </button>
                    <button onclick="switchSection('admin')" class="bg-slate-800 text-white px-8 py-3 rounded-2xl font-bold hover:bg-slate-900 transition-all shadow-lg flex items-center gap-2">
                        <span>📊</span> لوحة تحكم المسؤول
                    </button>
                </div>
            </div>
        </header>

        <!-- واجهة اختيار الدور -->
        <div id="roleSelection" class="grid grid-cols-1 md:grid-cols-2 gap-8 py-10">
            <div onclick="openEvalForm('supervisor')" class="cursor-pointer bg-white p-12 rounded-[3rem] border-4 border-indigo-600 shadow-xl hover:scale-[1.02] transition-all text-center group">
                <div class="text-6xl mb-6 group-hover:rotate-12 transition-transform">📝</div>
                <h2 class="text-3xl font-black text-slate-800">تقييم المشرف</h2>
                <div class="mt-6 inline-block bg-indigo-50 text-indigo-600 px-6 py-2 rounded-full font-bold">دخول النظام</div>
            </div>
            <div onclick="openEvalForm('examiner')" class="cursor-pointer bg-white p-12 rounded-[3rem] border-4 border-emerald-600 shadow-xl hover:scale-[1.02] transition-all text-center group">
                <div class="text-6xl mb-6 group-hover:rotate-12 transition-transform">🎓</div>
                <h2 class="text-3xl font-black text-slate-800">تقييم المناقش</h2>
                <div class="mt-6 inline-block bg-emerald-50 text-emerald-600 px-6 py-2 rounded-full font-bold">دخول النظام</div>
            </div>
        </div>

        <!-- لوحة المسؤول (Admin Panel) -->
        <div id="adminSection" class="hidden space-y-6 no-print">
            <div class="flex justify-between items-center bg-slate-900 text-white p-8 rounded-[2.5rem]">
                <h2 class="text-2xl font-black">قائمة المجموعات والطلاب</h2>
                <button onclick="switchSection('home')" class="bg-white/10 px-8 py-3 rounded-2xl font-bold hover:bg-white/20">العودة</button>
            </div>
            <div class="bg-white rounded-[2.5rem] shadow-xl overflow-hidden">
                <table class="w-full text-right text-sm md:text-base">
                    <thead class="bg-slate-100">
                        <tr>
                            <th class="p-4">القاعة</th>
                            <th class="p-4">المجموعة</th>
                            <th class="p-4">المشروع</th>
                            <th class="p-4">الطلاب</th>
                        </tr>
                    </thead>
                    <tbody id="adminTableBody"></tbody>
                </table>
            </div>
        </div>

        <!-- نموذج التقييم -->
        <div id="evalSection" class="hidden bg-white rounded-[3rem] shadow-2xl border border-slate-200 overflow-hidden">
            <div id="evalHeader" class="p-10 text-white text-center relative">
                <button onclick="switchSection('home')" class="absolute top-8 left-8 bg-white/20 px-5 py-2 rounded-full font-bold no-print">إلغاء</button>
                <h2 id="evalTitle" class="text-4xl font-black"></h2>
            </div>
            <div class="p-8 space-y-10">
                <div class="bg-slate-50 p-8 rounded-[2rem] grid grid-cols-1 md:grid-cols-3 gap-8">
                    <div class="md:col-span-2">
                        <label class="block text-xs font-black text-slate-400 mb-3">اختر المجموعة</label>
                        <select id="groupSelect" class="w-full p-5 rounded-2xl border-4 border-white shadow-sm font-black text-indigo-700 outline-none" onchange="renderStudentsCards()">
                            <option value="">-- تحميل البيانات... --</option>
                        </select>
                    </div>
                    <div>
                        <label class="block text-xs font-black text-slate-400 mb-3">التاريخ</label>
                        <input type="date" id="evalDate" class="w-full p-5 rounded-2xl border-4 border-white shadow-sm font-bold">
                    </div>
                </div>
                <div id="studentsGrid" class="grid grid-cols-1 lg:grid-cols-2 gap-8"></div>
            </div>
        </div>
    </div>

    <!-- قالب بطاقة الطالب -->
    <template id="studentCardTemplate">
        <div class="student-card bg-white p-8 rounded-[2.5rem] border-2 border-slate-100 shadow-lg flex flex-col h-full transition-all">
            <div class="flex justify-between items-start mb-8">
                <h4 class="student-name font-black text-2xl text-slate-800"></h4>
                <div class="w-12 h-12 bg-indigo-50 rounded-xl flex items-center justify-center">👤</div>
            </div>
            <div class="criteria-container space-y-5 flex-grow"></div>
            <div class="mt-10 pt-6 border-t border-slate-100 flex justify-between items-center">
                <div class="text-4xl font-black text-indigo-600 student-total">0</div>
                <div class="status-badge px-4 py-1 rounded-full font-bold text-xs bg-slate-100">قيد الانتظار</div>
            </div>
        </div>
    </template>

    <script>
        // --- إعدادات الربط ---
        const SHEET_ID = '1Ne4jRjMj75t2zk-w9bCoO2jZlkJkOIxtQAlqNG91p3U';
        const SHEET_NAME = 'Sheet1'; // غير هذا الاسم إذا كان اسم التبويب في ملفك مختلفاً (مثلاً: الطلاب)
        const SHEET_URL = `https://docs.google.com/spreadsheets/d/${SHEET_ID}/gviz/tq?tqx=out:csv&sheet=${SHEET_NAME}`;

        let mainDB = [];
        let activeRole = '';

        const roleSettings = {
            supervisor: { title: 'تقييم المشرف', color: 'bg-indigo-600', criteria: [{ name: 'التوثيق', max: 30 }, { name: 'المتابعة', max: 20 }, { name: 'العملي', max: 50 }] },
            examiner: { title: 'تقييم المناقش', color: 'bg-emerald-600', criteria: [{ name: 'العرض', max: 20 }, { name: 'النقاش', max: 30 }, { name: 'التنفيذ', max: 50 }] }
        };

        // دالة جلب البيانات مع معالجة الأخطاء
        async function fetchSheetData() {
            document.getElementById('loading').classList.remove('hidden');
            try {
                const response = await fetch(SHEET_URL);
                if (!response.ok) throw new Error('فشل الوصول للملف');
                const csvData = await response.text();
                
                Papa.parse(csvData, {
                    header: true,
                    skipEmptyLines: true,
                    complete: (results) => {
                        if (results.data.length === 0) {
                            alert("الملف فارغ أو لم يتم نشره للويب بشكل صحيح.");
                        } else {
                            processCSV(results.data);
                            alert(`تم بنجاح جلب ${results.data.length} صف من البيانات.`);
                        }
                        document.getElementById('loading').classList.add('hidden');
                    }
                });
            } catch (error) {
                console.error(error);
                alert('خطأ: تأكد من أن ملف جوجل شيت "منشور للويب" (File > Share > Publish to web).');
                document.getElementById('loading').classList.add('hidden');
            }
        }

        // معالجة البيانات (البحث عن المسميات بمرونة)
        function processCSV(rows) {
            let groupedData = {};
            
            rows.forEach(row => {
                // البحث عن القيم بأسماء أعمدة مختلفة (لحل مشكلة اختلاف المسميات)
                const groupID = row['رقم المجموعة'] || row['Group ID'] || row['ID'] || '0';
                const projectName = row['اسم المشروع'] || row['Project Name'] || row['Project'] || 'بدون عنوان';
                const studentName = row['اسم الطالب'] || row['Student Name'] || row['Student'] || '';
                const roomNumber = row['رقم القاعة'] || row['Room'] || row['قاعة'] || '-';
                const supervisor = row['اسم المشرف'] || row['Supervisor'] || '-';

                if (studentName.trim() !== "") {
                    const key = `${groupID}_${projectName}`;
                    if (!groupedData[key]) {
                        groupedData[key] = { id: groupID, project: projectName, room: roomNumber, supervisor: supervisor, students: [] };
                    }
                    groupedData[key].students.push(studentName);
                }
            });

            mainDB = Object.values(groupedData);
            localStorage.setItem('grad_sys_db_v3', JSON.stringify(mainDB));
            populateGroupSelect();
        }

        function populateGroupSelect() {
            const select = document.getElementById('groupSelect');
            select.innerHTML = '<option value="">-- اختر المجموعة --</option>';
            mainDB.forEach((group, index) => {
                const opt = document.createElement('option');
                opt.value = index;
                opt.innerText = `مجموعة ${group.id} | قاعة ${group.room} | ${group.project}`;
                select.appendChild(opt);
            });
        }

        function renderStudentsCards() {
            const idx = document.getElementById('groupSelect').value;
            const container = document.getElementById('studentsGrid');
            container.innerHTML = '';
            if (idx === '') return;

            const group = mainDB[idx];
            const settings = roleSettings[activeRole];

            group.students.forEach(name => {
                const temp = document.getElementById('studentCardTemplate').content.cloneNode(true);
                temp.querySelector('.student-name').innerText = name;
                const criteriaBox = temp.querySelector('.criteria-container');
                
                settings.criteria.forEach(crit => {
                    const d = document.createElement('div');
                    d.innerHTML = `<label class="text-xs font-bold text-slate-400">${crit.name} (ماكس ${crit.max})</label>
                                   <input type="number" max="${crit.max}" value="0" class="score-input w-full p-3 rounded-xl" oninput="calc(this, ${crit.max})">`;
                    criteriaBox.appendChild(d);
                });
                container.appendChild(temp);
            });
        }

        function calc(input, max) {
            if (parseInt(input.value) > max) input.value = max;
            const card = input.closest('.student-card');
            let total = 0;
            card.querySelectorAll('.score-input').forEach(i => total += (parseInt(i.value) || 0));
            card.querySelector('.student-total').innerText = total;
        }

        function switchSection(sec) {
            ['roleSelection', 'adminSection', 'evalSection'].forEach(id => document.getElementById(id).classList.add('hidden'));
            if (sec === 'home') document.getElementById('roleSelection').classList.remove('hidden');
            else if (sec === 'admin') {
                document.getElementById('adminSection').classList.remove('hidden');
                renderAdminTable();
            }
        }

        function openEvalForm(role) {
            if (mainDB.length === 0) return alert('يرجى تحديث البيانات أولاً.');
            activeRole = role;
            switchSection('eval');
            document.getElementById('evalSection').classList.remove('hidden');
            document.getElementById('evalHeader').className = `p-10 text-white text-center ${roleSettings[role].color}`;
            document.getElementById('evalTitle').innerText = roleSettings[role].title;
        }

        function renderAdminTable() {
            const body = document.getElementById('adminTableBody');
            body.innerHTML = '';
            mainDB.forEach(g => {
                const tr = document.createElement('tr');
                tr.innerHTML = `<td class="p-4">${g.room}</td><td class="p-4">#${g.id}</td><td class="p-4 font-bold">${g.project}</td>
                                <td class="p-4">${g.students.join('، ')}</td>`;
                body.appendChild(tr);
            });
        }

        window.onload = () => {
            const cached = localStorage.getItem('grad_sys_db_v3');
            if (cached) { mainDB = JSON.parse(cached); populateGroupSelect(); }
            document.getElementById('evalDate').valueAsDate = new Date();
        };
    </script>
</body>
</html>
