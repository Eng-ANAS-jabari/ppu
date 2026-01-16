<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>نظام إدارة المناقشات | الإصدار النهائي</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Tajawal:wght@400;500;700;900&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/PapaParse/5.3.2/papaparse.min.js"></script>
</head>
<body class="p-2 md:p-6 bg-gray-50 min-h-screen" style="font-family: 'Tajawal', sans-serif;">

    <div id="loading" class="fixed inset-0 bg-white/90 flex items-center justify-center z-50 hidden">
        <div class="text-center">
            <div class="block animate-spin rounded-full h-16 w-16 border-4 border-indigo-600 border-t-transparent"></div>
            <p class="mt-4 font-black text-indigo-900 text-lg">جاري مزامنة البيانات...</p>
        </div>
    </div>

    <div id="app" class="max-w-7xl mx-auto space-y-6">
        <!-- رأس الصفحة -->
        <header class="text-center py-6 bg-gradient-to-r from-indigo-900 to-indigo-700 rounded-2xl shadow-xl text-white">
            <h1 class="text-3xl md:text-4xl font-black mb-2">نظام إدارة تقييم المناقشات</h1>
            <p class="text-lg opacity-90">الإصدار النهائي - نظام متكامل للتقييم الإلكتروني</p>
            <div class="mt-4 flex flex-wrap justify-center gap-4">
                <button onclick="switchSection('home')" class="px-5 py-2 bg-white/20 hover:bg-white/30 rounded-lg transition"><i class="fas fa-home ml-2"></i>الرئيسية</button>
                <button onclick="switchSection('evaluation')" class="px-5 py-2 bg-white/20 hover:bg-white/30 rounded-lg transition"><i class="fas fa-clipboard-check ml-2"></i>التقييم</button>
                <button onclick="switchSection('admin')" class="px-5 py-2 bg-white/20 hover:bg-white/30 rounded-lg transition"><i class="fas fa-chart-bar ml-2"></i>الإحصائيات</button>
                <button onclick="fetchSheetData()" class="px-5 py-2 bg-emerald-500 hover:bg-emerald-600 rounded-lg transition"><i class="fas fa-sync-alt ml-2"></i>مزامنة البيانات</button>
            </div>
        </header>

        <!-- قسم اختيار الدور -->
        <section id="roleSection" class="bg-white p-6 rounded-2xl shadow-lg">
            <h2 class="text-2xl font-black text-gray-800 mb-4"><i class="fas fa-user-tie ml-2"></i>اختر دور التقييم</h2>
            <div class="grid md:grid-cols-2 gap-6">
                <div class="text-center p-6 border-2 border-indigo-200 rounded-xl hover:border-indigo-500 transition cursor-pointer" onclick="setActiveRole('supervisor')">
                    <div class="h-16 w-16 rounded-full bg-indigo-100 flex items-center justify-center mx-auto mb-4">
                        <i class="fas fa-user-graduate text-3xl text-indigo-600"></i>
                    </div>
                    <h3 class="text-xl font-black text-indigo-700">تقييم المشرف</h3>
                    <p class="text-gray-600 mt-2">خاص برصد درجات الفصل والمتابعة المستمرة</p>
                </div>
                <div class="text-center p-6 border-2 border-emerald-200 rounded-xl hover:border-emerald-500 transition cursor-pointer" onclick="setActiveRole('examiner')">
                    <div class="h-16 w-16 rounded-full bg-emerald-100 flex items-center justify-center mx-auto mb-4">
                        <i class="fas fa-chalkboard-teacher text-3xl text-emerald-600"></i>
                    </div>
                    <h3 class="text-xl font-black text-emerald-700">تقييم المناقش</h3>
                    <p class="text-gray-600 mt-2">تقييم العرض والمناقشة العلمية</p>
                </div>
            </div>
        </section>

        <!-- قسم التقييم (يظهر بعد اختيار الدور) -->
        <section id="evaluationSection" class="hidden bg-white p-6 rounded-2xl shadow-lg">
            <div class="flex flex-wrap items-center justify-between mb-6">
                <div>
                    <h2 id="evalTitle" class="text-2xl font-black"></h2>
                    <p id="evalSubtitle" class="text-gray-600"></p>
                </div>
                <div class="flex items-center gap-4">
                    <div id="roleBadge" class="px-4 py-2 rounded-full text-white font-bold"></div>
                    <button onclick="switchSection('home')" class="px-4 py-2 text-gray-600 hover:text-gray-900"><i class="fas fa-times"></i></button>
                </div>
            </div>

            <div class="grid md:grid-cols-3 gap-6 mb-6">
                <div class="md:col-span-2">
                    <label class="block font-bold text-gray-700 mb-2">اختر مجموعة الطلاب:</label>
                    <select id="groupSelect" onchange="renderStudentsCards()" class="w-full p-3 border-2 border-gray-300 rounded-lg focus:border-indigo-500 focus:ring-2 focus:ring-indigo-200 outline-none">
                        <option value="">-- اختر مجموعة --</option>
                    </select>
                </div>
                <div>
                    <label class="block font-bold text-gray-700 mb-2">تاريخ التقييم:</label>
                    <input type="date" id="evalDate" class="w-full p-3 border-2 border-gray-300 rounded-lg">
                </div>
            </div>

            <div id="studentsContainer" class="space-y-4">
                <!-- هنا سيتم عرض بطاقات الطلاب -->
            </div>

            <div id="criteriaSection" class="mt-8 hidden">
                <h3 class="text-xl font-black text-gray-800 mb-4">معايير التقييم</h3>
                <div id="criteriaList" class="space-y-4">
                    <!-- سيتم إضافة المعايير ديناميكيًا -->
                </div>
                <div class="mt-6 p-4 bg-gradient-to-r from-gray-50 to-gray-100 rounded-xl border border-gray-200">
                    <div class="flex justify-between items-center">
                        <span class="font-black text-lg text-gray-800">المجموع الكلي:</span>
                        <span id="totalScore" class="text-3xl font-black text-indigo-700">0</span>
                    </div>
                    <div class="mt-2 text-sm text-gray-600 text-center" id="maxScoreInfo"></div>
                </div>
                <div class="mt-6 flex gap-4">
                    <button onclick="saveResults()" class="flex-1 py-3 bg-gradient-to-r from-indigo-600 to-indigo-800 hover:from-indigo-700 hover:to-indigo-900 text-white font-black rounded-lg transition shadow-md hover:shadow-lg">
                        <i class="fas fa-save ml-2"></i>حفظ التقييم
                    </button>
                    <button onclick="resetForm()" class="px-6 py-3 bg-gray-200 hover:bg-gray-300 text-gray-800 font-bold rounded-lg transition">
                        <i class="fas fa-redo ml-2"></i>إعادة تعيين
                    </button>
                </div>
            </div>
        </section>

        <!-- قسم الإحصائيات -->
        <section id="adminSection" class="hidden bg-white p-6 rounded-2xl shadow-lg">
            <h2 class="text-2xl font-black text-gray-800 mb-6"><i class="fas fa-chart-bar ml-2"></i>لوحة الإحصائيات والنتائج</h2>
            
            <div class="grid grid-cols-1 md:grid-cols-4 gap-4 mb-6">
                <div class="bg-gradient-to-r from-indigo-500 to-indigo-600 text-white p-4 rounded-xl">
                    <div class="text-3xl font-black" id="totalGroups">0</div>
                    <div class="text-sm opacity-90">المجموعات الكلية</div>
                </div>
                <div class="bg-gradient-to-r from-emerald-500 to-emerald-600 text-white p-4 rounded-xl">
                    <div class="text-3xl font-black" id="totalStudents">0</div>
                    <div class="text-sm opacity-90">إجمالي الطلاب</div>
                </div>
                <div class="bg-gradient-to-r from-amber-500 to-amber-600 text-white p-4 rounded-xl">
                    <div class="text-3xl font-black" id="evaluatedCount">0</div>
                    <div class="text-sm opacity-90">تم تقييمهم</div>
                </div>
                <div class="bg-gradient-to-r from-purple-500 to-purple-600 text-white p-4 rounded-xl">
                    <div class="text-3xl font-black" id="avgScore">0</div>
                    <div class="text-sm opacity-90">متوسط الدرجات</div>
                </div>
            </div>

            <div class="mb-6">
                <label class="block font-bold text-gray-700 mb-2">فلترة النتائج:</label>
                <div class="flex flex-wrap gap-4">
                    <select id="filterRole" onchange="filterAdmin()" class="p-2 border rounded-lg">
                        <option value="">جميع الأدوار</option>
                        <option value="supervisor">تقييم المشرف</option>
                        <option value="examiner">تقييم المناقش</option>
                    </select>
                    <select id="filterGroup" onchange="filterAdmin()" class="p-2 border rounded-lg">
                        <option value="">جميع المجموعات</option>
                    </select>
                    <input type="text" id="searchStudent" onkeyup="filterAdmin()" placeholder="بحث عن طالب..." class="p-2 border rounded-lg flex-grow">
                </div>
            </div>

            <div class="overflow-x-auto">
                <table class="min-w-full bg-white border border-gray-300">
                    <thead class="bg-gray-100">
                        <tr>
                            <th class="py-3 px-4 border text-right">اسم الطالب</th>
                            <th class="py-3 px-4 border text-right">المجموعة</th>
                            <th class="py-3 px-4 border text-right">المشروع</th>
                            <th class="py-3 px-4 border text-right">نوع التقييم</th>
                            <th class="py-3 px-4 border text-right">الدرجة</th>
                            <th class="py-3 px-4 border text-right">التاريخ</th>
                            <th class="py-3 px-4 border text-right">الإجراءات</th>
                        </tr>
                    </thead>
                    <tbody id="resultsTable">
                        <!-- سيتم ملء الجدول ديناميكيًا -->
                    </tbody>
                </table>
            </div>

            <div class="mt-6 flex gap-4">
                <button onclick="exportToCSV()" class="px-6 py-3 bg-gradient-to-r from-green-500 to-green-700 hover:from-green-600 hover:to-green-800 text-white font-bold rounded-lg transition">
                    <i class="fas fa-file-export ml-2"></i>تصدير لـ CSV
                </button>
                <button onclick="clearAllData()" class="px-6 py-3 bg-gradient-to-r from-red-500 to-red-700 hover:from-red-600 hover:to-red-800 text-white font-bold rounded-lg transition">
                    <i class="fas fa-trash-alt ml-2"></i>حذف جميع البيانات
                </button>
            </div>
        </section>

        <!-- قسم التعليمات -->
        <footer class="bg-gradient-to-r from-gray-800 to-gray-900 text-white p-6 rounded-2xl shadow-lg">
            <h3 class="text-xl font-black mb-4"><i class="fas fa-info-circle ml-2"></i>تعليمات الاستخدام</h3>
            <div class="grid md:grid-cols-3 gap-6">
                <div class="space-y-2">
                    <p class="font-bold"><i class="fas fa-sync-alt ml-2"></i>مزامنة البيانات</p>
                    <p class="text-sm opacity-90">انقر على زر "مزامنة البيانات" لاستيراد أحدث البيانات من Google Sheets.</p>
                </div>
                <div class="space-y-2">
                    <p class="font-bold"><i class="fas fa-clipboard-check ml-2"></i>عملية التقييم</p>
                    <p class="text-sm opacity-90">اختر الدور، ثم المجموعة، ثم املأ معايير التقييم لكل طالب.</p>
                </div>
                <div class="space-y-2">
                    <p class="font-bold"><i class="fas fa-download ml-2"></i>تصدير النتائج</p>
                    <p class="text-sm opacity-90">يمكنك تصدير جميع النتائج إلى ملف CSV من لوحة الإحصائيات.</p>
                </div>
            </div>
            <div class="mt-6 pt-4 border-t border-white/20 text-center text-sm opacity-80">
                <p>نظام إدارة تقييم المناقشات - الإصدار النهائي © 2023</p>
            </div>
        </footer>
    </div>

    <script>
        // -------------------
        // ✅ الرابط الجديد: CSV مباشر من Google Sheets
        const SHEET_CSV_URL = "https://docs.google.com/spreadsheets/d/1Ne4jRjMj75t2zk-w9bCoO2jZlkJkOIxtQAlqNG91p3U/export?format=csv";
        let mainDB = [];
        let activeRole = '';
        let resultsDB = JSON.parse(localStorage.getItem('grad_sys_results')) || [];

        const roleSettings = {
            supervisor: { 
                title: 'تقييم المشرف', 
                subtitle: 'خاص برصد درجات الفصل والمتابعة', 
                color: 'bg-gradient-to-r from-indigo-600 to-indigo-800',
                badge: 'bg-indigo-600',
                criteria: [
                    { name: 'التوثيق والتقرير الشخصي', max: 30 },
                    { name: 'الالتزام بالمواعيد والمتابعة', max: 20 },
                    { name: 'جودة التنفيذ العملي', max: 50 }
                ]
            },
            examiner: { 
                title: 'تقييم المناقش', 
                subtitle: 'تقييم العرض والمناقشة', 
                color: 'bg-gradient-to-r from-emerald-600 to-emerald-800',
                badge: 'bg-emerald-600',
                criteria: [
                    { name: 'جودة العرض التقديمي', max: 20 },
                    { name: 'القدرة على النقاش العلمي', max: 30 },
                    { name: 'تكامل النظام المبرمج', max: 50 }
                ]
            }
        };

        async function fetchSheetData() {
            document.getElementById('loading').classList.remove('hidden');
            try {
                const res = await fetch(SHEET_CSV_URL);
                const csvText = await res.text();

                Papa.parse(csvText, {
                    header: true,
                    skipEmptyLines: true,
                    complete: (results) => {
                        processCSV(results.data);
                        alert('تم تحديث البيانات بنجاح من Google Sheets.');
                        document.getElementById('loading').classList.add('hidden');
                    },
                    error: (err) => {
                        alert('خطأ في تحليل البيانات: ' + err.message);
                        document.getElementById('loading').classList.add('hidden');
                    }
                });
            } catch (err) {
                alert('خطأ في جلب البيانات! تأكد من أن الملف عام للجميع.');
                console.error(err);
                document.getElementById('loading').classList.add('hidden');
            }
        }

        function processCSV(rows) {
            let groupedData = {};

            rows.forEach(row => {
                const studentName = row['اسم الطالب'];
                if (!studentName) return;

                const groupID = row['رقم المجموعة'] || 'غير معروف';
                const projectName = row['اسم المشروع'] || '';
                const roomNumber = row['رقم القاعة'] || '';
                const supervisor = row['اسم المشرف'] || '';

                const key = `${groupID}_${projectName}`;
                if (!groupedData[key]) {
                    groupedData[key] = {
                        id: groupID,
                        project: projectName,
                        room: roomNumber,
                        supervisor: supervisor,
                        students: []
                    };
                }

                groupedData[key].students.push(studentName);
            });

            mainDB = Object.values(groupedData);
            localStorage.setItem('grad_sys_db', JSON.stringify(mainDB));
            populateGroupSelect();
            updateStats();
        }

        function setActiveRole(role) {
            activeRole = role;
            const settings = roleSettings[role];
            
            document.getElementById('roleSection').classList.add('hidden');
            document.getElementById('evaluationSection').classList.remove('hidden');
            
            document.getElementById('evalTitle').textContent = settings.title;
            document.getElementById('evalSubtitle').textContent = settings.subtitle;
            document.getElementById('roleBadge').textContent = settings.title;
            document.getElementById('roleBadge').className = `px-4 py-2 rounded-full text-white font-bold ${settings.badge}`;
            
            document.getElementById('evalDate').valueAsDate = new Date();
        }

        function populateGroupSelect() {
            const select = document.getElementById('groupSelect');
            const filterSelect = document.getElementById('filterGroup');
            
            select.innerHTML = '<option value="">-- اختر مجموعة --</option>';
            filterSelect.innerHTML = '<option value="">جميع المجموعات</option>';
            
            mainDB.forEach(group => {
                const option = document.createElement('option');
                option.value = group.id;
                option.textContent = `المجموعة ${group.id}: ${group.project} (${group.students.length} طلاب)`;
                select.appendChild(option);
                
                const filterOption = document.createElement('option');
                filterOption.value = group.id;
                filterOption.textContent = `المجموعة ${group.id}`;
                filterSelect.appendChild(filterOption);
            });
        }

        function renderStudentsCards() {
            const groupId = document.getElementById('groupSelect').value;
            if (!groupId) {
                document.getElementById('studentsContainer').innerHTML = '';
                document.getElementById('criteriaSection').classList.add('hidden');
                return;
            }

            const group = mainDB.find(g => g.id === groupId);
            if (!group) return;

            let studentsHTML = '<div class="grid md:grid-cols-2 gap-4">';
            
            group.students.forEach(student => {
                studentsHTML += `
                <div class="border border-gray-300 rounded-xl p-4 hover:shadow-md transition">
                    <div class="flex items-center justify-between">
                        <div>
                            <h4 class="font-bold text-lg text-gray-800">${student}</h4>
                            <p class="text-sm text-gray-600">المجموعة: ${group.id} | المشروع: ${group.project}</p>
                        </div>
                        <button onclick="openEvalForm('${student}')" class="px-4 py-2 ${roleSettings[activeRole].color} text-white font-bold rounded-lg hover:opacity-90 transition">
                            <i class="fas fa-edit ml-2"></i>تقييم
                        </button>
                    </div>
                </div>`;
            });
            
            studentsHTML += '</div>';
            document.getElementById('studentsContainer').innerHTML = studentsHTML;
        }

        function openEvalForm(studentName) {
            const criteriaList = document.getElementById('criteriaList');
            criteriaList.innerHTML = '';
            
            const settings = roleSettings[activeRole];
            let maxTotal = 0;
            
            settings.criteria.forEach((criterion, index) => {
                maxTotal += criterion.max;
                
                const criterionHTML = `
                <div class="bg-gray-50 p-4 rounded-xl border border-gray-200">
                    <div class="flex justify-between items-center mb-3">
                        <label class="font-bold text-gray-800">${criterion.name}</label>
                        <span class="text-sm text-gray-600">الحد الأقصى: ${criterion.max}</span>
                    </div>
                    <input 
                        type="range" 
                        min="0" 
                        max="${criterion.max}" 
                        value="0" 
                        class="w-full h-3 bg-gray-300 rounded-lg appearance-none cursor-pointer"
                        oninput="document.getElementById('score${index}').textContent = this.value; calculateTotal()"
                    >
                    <div class="flex justify-between mt-2">
                        <span>0</span>
                        <span class="font-black text-lg ${settings.color.split(' ')[2]}">
                            <span id="score${index}">0</span> / ${criterion.max}
                        </span>
                        <span>${criterion.max}</span>
                    </div>
                </div>`;
                
                criteriaList.innerHTML += criterionHTML;
            });
            
            document.getElementById('maxScoreInfo').textContent = `الدرجة القصوى: ${maxTotal}`;
            document.getElementById('criteriaSection').classList.remove('hidden');
            document.getElementById('criteriaSection').dataset.student = studentName;
            
            window.scrollTo({ top: document.getElementById('criteriaSection').offsetTop, behavior: 'smooth' });
        }

        function calculateTotal() {
            const settings = roleSettings[activeRole];
            let total = 0;
            
            settings.criteria.forEach((criterion, index) => {
                const slider = document.querySelector(`#criteriaList input[type="range"]:nth-child(${index + 1})`);
                if (slider) total += parseInt(slider.value);
            });
            
            document.getElementById('totalScore').textContent = total;
        }

        function saveResults() {
            const studentName = document.getElementById('criteriaSection').dataset.student;
            const groupId = document.getElementById('groupSelect').value;
            const evalDate = document.getElementById('evalDate').value;
            
            if (!studentName || !groupId || !activeRole) {
                alert('يرجى تعبئة جميع الحقول!');
                return;
            }

            const settings = roleSettings[activeRole];
            let scores = [];
            let total = 0;
            
            settings.criteria.forEach((criterion, index) => {
                const slider = document.querySelector(`#criteriaList input[type="range"]:nth-child(${index + 1})`);
                const score = slider ? parseInt(slider.value) : 0;
                scores.push({
                    criterion: criterion.name,
                    score: score,
                    max: criterion.max
                });
                total += score;
            });

            const result = {
                id: Date.now(),
                student: studentName,
                group: groupId,
                role: activeRole,
                roleName: settings.title,
                scores: scores,
                total: total,
                date: evalDate,
                timestamp: new Date().toISOString()
            };

            resultsDB.push(result);
            localStorage.setItem('grad_sys_results', JSON.stringify(resultsDB));
            
            alert(`تم حفظ تقييم الطالب ${studentName} بنجاح! الدرجة: ${total}`);
            resetForm();
            updateStats();
            renderAdminTable();
        }

        function resetForm() {
            const sliders = document.querySelectorAll('#criteriaList input[type="range"]');
            sliders.forEach(slider => slider.value = 0);
            calculateTotal();
        }

        function switchSection(section) {
            document.querySelectorAll('section').forEach(sec => sec.classList.add('hidden'));
            
            if (section === 'home') {
                document.getElementById('roleSection').classList.remove('hidden');
                document.getElementById('evaluationSection').classList.add('hidden');
                document.getElementById('adminSection').classList.add('hidden');
            } else if (section === 'evaluation') {
                document.getElementById('roleSection').classList.remove('hidden');
            } else if (section === 'admin') {
                document.getElementById('adminSection').classList.remove('hidden');
                renderAdminTable();
                updateStats();
            }
        }

        function renderAdminTable() {
            const tableBody = document.getElementById('resultsTable');
            tableBody.innerHTML = '';
            
            if (resultsDB.length === 0) {
                tableBody.innerHTML = '<tr><td colspan="7" class="py-8 text-center text-gray-500">لا توجد نتائج مسجلة بعد</td></tr>';
                return;
            }
            
            resultsDB.forEach(result => {
                const group = mainDB.find(g => g.id === result.group) || {};
                const row = document.createElement('tr');
                row.className = 'hover:bg-gray-50';
                row.innerHTML = `
                    <td class="py-3 px-4 border">${result.student}</td>
                    <td class="py-3 px-4 border">${result.group}</td>
                    <td class="py-3 px-4 border">${group.project || 'غير معروف'}</td>
                    <td class="py-3 px-4 border">
                        <span class="px-3 py-1 rounded-full text-white ${result.role === 'supervisor' ? 'bg-indigo-600' : 'bg-emerald-600'} text-sm">
                            ${result.roleName}
                        </span>
                    </td>
                    <td class="py-3 px-4 border font-black ${result.total >= 70 ? 'text-green-600' : 'text-red-600'}">
                        ${result.total}
                    </td>
                    <td class="py-3 px-4 border">${result.date}</td>
                    <td class="py-3 px-4 border">
                        <button onclick="deleteResult(${result.id})" class="px-3 py-1 bg-red-100 text-red-700 hover:bg-red-200 rounded-lg text-sm">
                            <i class="fas fa-trash ml-1"></i>حذف
                        </button>
                    </td>
                `;
                tableBody.appendChild(row);
            });
        }

        function filterAdmin() {
            const roleFilter = document.getElementById('filterRole').value;
            const groupFilter = document.getElementById('filterGroup').value;
            const searchTerm = document.getElementById('searchStudent').value.toLowerCase();
            
            const filtered = resultsDB.filter(result => {
                const matchesRole = !roleFilter || result.role === roleFilter;
                const matchesGroup = !groupFilter || result.group === groupFilter;
                const matchesSearch = !searchTerm || result.student.toLowerCase().includes(searchTerm);
                return matchesRole && matchesGroup && matchesSearch;
            });
            
            const tableBody = document.getElementById('resultsTable');
            tableBody.innerHTML = '';
            
            filtered.forEach(result => {
                const group = mainDB.find(g => g.id === result.group) || {};
                const row = document.createElement('tr');
                row.className = 'hover:bg-gray-50';
                row.innerHTML = `
                    <td class="py-3 px-4 border">${result.student}</td>
                    <td class="py-3 px-4 border">${result.group}</td>
                    <td class="py-3 px-4 border">${group.project || 'غير معروف'}</td>
                    <td class="py-3 px-4 border">
                        <span class="px-3 py-1 rounded-full text-white ${result.role === 'supervisor' ? 'bg-indigo-600' : 'bg-emerald-600'} text-sm">
                            ${result.roleName}
                        </span>
                    </td>
                    <td class="py-3 px-4 border font-black ${result.total >= 70 ? 'text-green-600' : 'text-red-600'}">
                        ${result.total}
                    </td>
                    <td class="py-3 px-4 border">${result.date}</td>
                    <td class="py-3 px-4 border">
                        <button onclick="deleteResult(${result.id})" class="px-3 py-1 bg-red-100 text-red-700 hover:bg-red-200 rounded-lg text-sm">
                            <i class="fas fa-trash ml-1"></i>حذف
                        </button>
                    </td>
                `;
                tableBody.appendChild(row);
            });
        }

        function updateStats() {
            document.getElementById('totalGroups').textContent = mainDB.length;
            
            const totalStudents = mainDB.reduce((sum, group) => sum + group.students.length, 0);
            document.getElementById('totalStudents').textContent = totalStudents;
            
            document.getElementById('evaluatedCount').textContent = resultsDB.length;
            
            const avg = resultsDB.length > 0 
                ? (resultsDB.reduce((sum, result) => sum + result.total, 0) / resultsDB.length).toFixed(1)
                : '0.0';
            document.getElementById('avgScore').textContent = avg;
        }

        function deleteResult(id) {
            if (confirm('هل أنت متأكد من حذف هذا التقييم؟')) {
                resultsDB = resultsDB.filter(result => result.id !== id);
                localStorage.setItem('grad_sys_results', JSON.stringify(resultsDB));
                renderAdminTable();
                updateStats();
            }
        }

        function exportToCSV() {
            if (resultsDB.length === 0) {
                alert('لا توجد بيانات للتصدير!');
                return;
            }
            
            let csv = 'اسم الطالب,المجموعة,نوع التقييم,الدرجة الكلية,التاريخ\n';
            
            resultsDB.forEach(result => {
                csv += `"${result.student}","${result.group}","${result.roleName}",${result.total},"${result.date}"\n`;
            });
            
            const blob = new Blob(['\uFEFF' + csv], { type: 'text/csv;charset=utf-8;' });
            const link = document.createElement('a');
            link.href = URL.createObjectURL(blob);
            link.download = `نتائج_التقييم_${new Date().toISOString().split('T')[0]}.csv`;
            link.click();
        }

        function clearAllData() {
            if (confirm('هل أنت متأكد من حذف جميع البيانات؟ هذا الإجراء لا يمكن التراجع عنه!')) {
                localStorage.removeItem('grad_sys_results');
                localStorage.removeItem('grad_sys_db');
                resultsDB = [];
                mainDB = [];
                renderAdminTable();
                updateStats();
                populateGroupSelect();
                alert('تم حذف جميع البيانات.');
            }
        }

        window.onload = () => {
            document.getElementById('evalDate').valueAsDate = new Date();
            
            const storedDB = localStorage.getItem('grad_sys_db');
            const storedResults = localStorage.getItem('grad_sys_results');
            
            if (storedDB) {
                mainDB = JSON.parse(storedDB);
                populateGroupSelect();
            }
            
            if (storedResults) {
                resultsDB = JSON.parse(storedResults);
            }
            
            updateStats();
            switchSection('home');
        };
    </script>
</body>
</html>
