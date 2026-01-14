<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>نظام تقييم مشاريع التخرج | لوحة المسؤول المتقدمة</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Tajawal:wght@400;500;700&display=swap" rel="stylesheet">
    <!-- مكتبة Excel JS -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    <style>
        body { font-family: 'Tajawal', sans-serif; background-color: #f1f5f9; }
        .score-input { border: 2px solid #e2e8f0; transition: all 0.2s; text-align: center; font-weight: 700; font-size: 1.1rem; }
        .score-input:focus { border-color: #4f46e5; outline: none; background-color: #fffbeb; }
        .admin-card { border: 1px solid #e2e8f0; background: #ffffff; padding: 1.5rem; border-radius: 1.5rem; }
    </style>
</head>
<body class="p-4 md:p-8">

    <div id="app" class="max-w-6xl mx-auto space-y-6">
        
        <!-- Main Navigation (Roles) -->
        <div id="roleSelection" class="bg-white p-10 rounded-[2rem] shadow-2xl text-center no-print border border-gray-100">
            <h2 class="text-3xl font-black mb-2 text-slate-800">بوابة التقييم الرقمية</h2>
            <p class="text-slate-500 mb-10">اختر نوع الدخول للبدء</p>
            
            <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
                <!-- واجهة المسؤول -->
                <button onclick="requestAdminAccess()" class="group p-8 bg-slate-100 border-4 border-slate-300 rounded-[2rem] hover:bg-slate-800 hover:text-white transition-all duration-300">
                    <div class="text-4xl mb-4">🔐</div>
                    <div class="text-xl font-black">لوحة المسؤول</div>
                    <div class="text-xs opacity-70 mt-1">Admin Panel</div>
                </button>

                <!-- واجهة المشرف -->
                <button onclick="setRole('supervisor')" class="group p-8 bg-white border-4 border-indigo-600 rounded-[2rem] hover:bg-indigo-600 hover:text-white transition-all duration-300 shadow-xl">
                    <div class="text-4xl mb-4">📋</div>
                    <div class="text-xl font-black">نموذج المشرف</div>
                    <div class="text-xs opacity-70 mt-1">Supervisor</div>
                </button>
                
                <!-- واجهة المناقش -->
                <button onclick="setRole('examiner')" class="group p-8 bg-white border-4 border-emerald-600 rounded-[2rem] hover:bg-emerald-600 hover:text-white transition-all duration-300 shadow-xl">
                    <div class="text-4xl mb-4">🎓</div>
                    <div class="text-xl font-black">نموذج المناقش</div>
                    <div class="text-xs opacity-70 mt-1">Examiner</div>
                </button>
            </div>
        </div>

        <!-- واجهة المسؤول (إدارة البيانات) -->
        <div id="adminPanel" class="hidden bg-white shadow-2xl rounded-[2.5rem] overflow-hidden border border-gray-100">
            <div class="bg-slate-800 p-6 text-white flex justify-between items-center">
                <div class="flex items-center gap-3">
                    <span class="text-2xl">⚙️</span>
                    <h2 class="text-2xl font-bold">لوحة تحكم المسؤول</h2>
                </div>
                <button onclick="goBack()" class="bg-white/20 px-4 py-2 rounded-lg text-sm hover:bg-white/30">خروج آمن</button>
            </div>
            
            <div class="p-8 space-y-8">
                <!-- ميزة استيراد Excel -->
                <div class="bg-indigo-50 p-6 rounded-2xl border-2 border-dashed border-indigo-200 text-center">
                    <h3 class="font-bold text-indigo-800 mb-2">📥 استيراد البيانات من ملف Excel</h3>
                    <p class="text-sm text-indigo-600 mb-4">يجب أن يحتوي الملف على أعمدة: (اسم الطالب، اسم المشروع، اسم المشرف)</p>
                    <input type="file" id="excelUpload" accept=".xlsx, .xls" class="hidden" onchange="importExcel(event)">
                    <button onclick="document.getElementById('excelUpload').click()" class="bg-indigo-600 text-white px-8 py-3 rounded-xl font-bold hover:bg-indigo-700 shadow-md">
                        رفع ملف Excel
                    </button>
                </div>

                <div class="grid grid-cols-1 md:grid-cols-2 gap-8">
                    <!-- إدارة المشاريع -->
                    <div class="admin-card">
                        <h3 class="font-bold text-lg text-indigo-600 border-b pb-2 mb-4">📦 إدارة المشاريع والمشرفين</h3>
                        <div class="space-y-2 mb-4">
                            <input type="text" id="newProject" class="w-full p-2 border rounded-lg text-sm" placeholder="اسم المشروع">
                            <input type="text" id="newSupervisor" class="w-full p-2 border rounded-lg text-sm" placeholder="اسم المشرف المرتبط">
                            <button onclick="addProject()" class="w-full bg-indigo-600 text-white py-2 rounded-lg font-bold">+</button>
                        </div>
                        <ul id="adminProjectsList" class="bg-slate-50 p-4 rounded-xl max-h-60 overflow-y-auto space-y-2"></ul>
                    </div>
                    <!-- إدارة الطلاب -->
                    <div class="admin-card">
                        <h3 class="font-bold text-lg text-emerald-600 border-b pb-2 mb-4">👥 إدارة الطلاب</h3>
                        <div class="flex gap-2 mb-4">
                            <input type="text" id="newStudent" class="flex-grow p-2 border rounded-lg text-sm" placeholder="اسم الطالب الجديد">
                            <button onclick="addItem('students', 'newStudent')" class="bg-emerald-600 text-white px-4 py-2 rounded-lg">+</button>
                        </div>
                        <ul id="adminStudentsList" class="bg-slate-50 p-4 rounded-xl max-h-60 overflow-y-auto space-y-2"></ul>
                    </div>
                </div>
            </div>
        </div>

        <!-- Main Form Container (المشرف والمناقش) -->
        <div id="mainContainer" class="hidden bg-white shadow-2xl rounded-[2.5rem] overflow-hidden border border-gray-100">
            <div id="formHeader" class="p-10 text-white text-center relative">
                <button onclick="goBack()" class="absolute top-6 left-6 bg-white/20 hover:bg-white/40 px-4 py-2 rounded-full text-xs transition-all">الرئيسية</button>
                <h1 id="headerTitle" class="text-4xl font-black mb-1"></h1>
                <p id="headerSub" class="text-lg opacity-90 font-medium"></p>
            </div>

            <form id="evaluationForm" class="p-8 md:p-12 space-y-10">
                <div id="infoSection" class="grid grid-cols-1 md:grid-cols-3 gap-8 pb-8 border-b-2 border-slate-50">
                    <div class="space-y-1">
                        <label class="block font-bold text-slate-700 text-sm">اختر المشروع</label>
                        <select id="projectSelect" class="w-full p-2 bg-white border border-slate-200 rounded-lg outline-none font-medium" onchange="loadProjectData()">
                            <option value="">-- اختر المشروع --</option>
                        </select>
                        <input type="text" id="projectTitle" class="w-full p-2 mt-2 bg-gray-50 border border-slate-200 rounded-lg hidden">
                    </div>
                    <div id="dynamicFields" class="contents"></div>
                </div>

                <div id="syncSection" class="hidden no-print bg-amber-50 p-4 rounded-xl border border-amber-200 flex items-center justify-between">
                    <p class="text-sm text-amber-800 font-bold">💡 دمج علامات (الكتاب والعملي) لجميع الطلاب؟</p>
                    <button type="button" onclick="syncSharedMarks()" id="syncBtn" class="bg-amber-500 text-white px-4 py-2 rounded-lg text-xs font-bold transition-all">تفعيل الدمج</button>
                </div>

                <div class="grid grid-cols-1 lg:grid-cols-3 gap-8" id="studentsWrapper"></div>

                <div class="pt-8 flex flex-wrap justify-center gap-3 border-t-2 border-slate-50">
                    <button type="button" onclick="exportToExcel()" class="bg-emerald-600 text-white px-6 py-3 rounded-xl font-bold shadow-lg">ملف Excel</button>
                    <button type="button" onclick="shareWhatsApp()" class="bg-green-500 text-white px-6 py-3 rounded-xl font-bold shadow-lg">WhatsApp</button>
                    <button type="button" onclick="window.print()" class="bg-slate-800 text-white px-6 py-3 rounded-xl font-bold shadow-lg">طباعة PDF</button>
                </div>
            </form>
        </div>
    </div>

    <!-- Student Template -->
    <template id="studentTemplate">
        <div class="student-card bg-slate-50/50 border-2 border-slate-100 rounded-[2rem] p-6 flex flex-col h-full">
            <div class="mb-4">
                <label class="block text-[10px] font-black text-slate-400 mb-1 uppercase">الطالب / Student</label>
                <select class="student-name-select w-full bg-white border-2 border-slate-100 p-2 rounded-lg font-bold text-slate-700 outline-none">
                    <option value="">-- اختر الطالب --</option>
                </select>
                <input type="text" class="student-name-input w-full bg-white border-2 border-slate-100 p-2 rounded-lg font-bold text-slate-700 mt-2 hidden">
            </div>
            <div class="criteria-list space-y-4 flex-grow"></div>
            <div class="mt-6 pt-4 border-t-2 border-dashed border-slate-200 flex justify-between items-center">
                <div>
                    <span class="text-[10px] font-black text-slate-400 block uppercase">Total</span>
                    <span class="text-3xl font-black text-slate-800 student-total-display">0</span>
                </div>
                <span class="student-result-text font-bold text-[10px] px-2 py-1 bg-slate-200 rounded-full italic">N/A</span>
            </div>
        </div>
    </template>

    <script>
        let currentRole = '';
        let isSyncing = false;
        
        // البيانات الأولية مع تخزين اسم المشرف لكل مشروع
        let db = JSON.parse(localStorage.getItem('grad_db')) || {
            projects: [
                { title: "نظام إدارة المستودعات", supervisor: "د. محمد حسن" },
                { title: "تطبيق التجارة الإلكترونية", supervisor: "د. سارة خالد" }
            ],
            students: ["أحمد محمد علي", "سارة محمود حسن", "خالد عبد الله"]
        };

        const config = {
            supervisor: { title: "نموذج المشرف", color: "bg-indigo-700", criteria: [{id:'book',label:'الكتاب',max:25,shared:true},{id:'practical',label:'العملي',max:35,shared:true},{id:'reviews',label:'المراجعات',max:20},{id:'team',label:'التعاون',max:20}], fields: [{id:'supervisorName',label:'اسم المشرف'}] },
            examiner: { title: "نموذج المناقش", color: "bg-emerald-700", criteria: [{id:'report',label:'التقرير',max:25},{id:'demo',label:'العرض',max:30},{id:'presentation',label:'المهارات',max:20},{id:'scientific',label:'التمكن',max:25}], fields: [{id:'supervisorName',label:'المشرف'},{id:'examinerName',label:'المناقش'}] }
        };

        function requestAdminAccess() {
            const pass = prompt("الرجاء إدخال كلمة مرور المسؤول:");
            if (pass === "1234") {
                showSection('admin');
            } else {
                alert("كلمة مرور خاطئة!");
            }
        }

        function showSection(sectionId) {
            document.getElementById('roleSelection').classList.add('hidden');
            if(sectionId === 'admin') {
                document.getElementById('adminPanel').classList.remove('hidden');
                renderAdminLists();
            }
        }

        function goBack() {
            document.getElementById('adminPanel').classList.add('hidden');
            document.getElementById('mainContainer').classList.add('hidden');
            document.getElementById('roleSelection').classList.remove('hidden');
        }

        function setRole(role) {
            currentRole = role;
            const data = config[role];
            document.getElementById('roleSelection').classList.add('hidden');
            document.getElementById('mainContainer').classList.remove('hidden');
            document.getElementById('formHeader').className = `p-10 text-white text-center relative ${data.color}`;
            document.getElementById('headerTitle').innerText = data.title;
            
            if (role === 'supervisor') document.getElementById('syncSection').classList.remove('hidden');
            else document.getElementById('syncSection').classList.add('hidden');

            const projectSelect = document.getElementById('projectSelect');
            projectSelect.innerHTML = '<option value="">-- اختر المشروع --</option>' + db.projects.map(p => `<option value="${p.title}" data-sup="${p.supervisor}">${p.title}</option>`).join('') + '<option value="custom">مشروع يدوي...</option>';

            const dynFields = document.getElementById('dynamicFields');
            dynFields.innerHTML = data.fields.map(f => `<div><label class="block font-bold text-sm">${f.label}</label><input type="text" id="${f.id}" class="w-full p-2 border rounded-lg"></div>`).join('') + `<div><label class="block font-bold text-sm">التاريخ</label><input type="date" id="date" class="w-full p-2 border rounded-lg"></div>`;

            const wrapper = document.getElementById('studentsWrapper');
            const template = document.getElementById('studentTemplate');
            wrapper.innerHTML = '';
            for (let i = 0; i < 3; i++) {
                const clone = template.content.cloneNode(true);
                const card = clone.querySelector('.student-card');
                const nameSelect = clone.querySelector('.student-name-select');
                const nameInput = clone.querySelector('.student-name-input');
                
                nameSelect.innerHTML = '<option value="">-- اختر الطالب --</option>' + db.students.map(s => `<option value="${s}">${s}</option>`).join('') + '<option value="custom">اسم يدوي...</option>';
                nameSelect.onchange = (e) => {
                    if(e.target.value === 'custom') { nameInput.classList.remove('hidden'); nameInput.value = ""; }
                    else { nameInput.classList.add('hidden'); nameInput.value = e.target.value; }
                };

                data.criteria.forEach(c => {
                    const row = document.createElement('div');
                    row.innerHTML = `<div class="flex justify-between text-[10px] font-bold text-slate-500 mb-1"><span>${c.label}</span><span>Max: ${c.max}</span></div>
                                     <input type="number" min="0" max="${c.max}" value="0" class="score-input w-full p-1 rounded-lg border" data-id="${c.id}" data-shared="${c.shared || false}">`;
                    row.querySelector('input').addEventListener('input', (e) => {
                        let val = Math.min(parseInt(e.target.value) || 0, c.max);
                        e.target.value = val;
                        if (currentRole === 'supervisor' && isSyncing && c.shared) applySync(c.id, val);
                        updateTotal(card);
                    });
                    card.querySelector('.criteria-list').appendChild(row);
                });
                wrapper.appendChild(clone);
            }
        }

        // استيراد ملف Excel
        function importExcel(event) {
            const file = event.target.files[0];
            const reader = new FileReader();
            reader.onload = (e) => {
                const data = new Uint8Array(e.target.result);
                const workbook = XLSX.read(data, { type: 'array' });
                const firstSheetName = workbook.SheetNames[0];
                const worksheet = workbook.Sheets[firstSheetName];
                const json = XLSX.utils.sheet_to_json(worksheet);

                json.forEach(row => {
                    const student = row['اسم الطالب'];
                    const project = row['اسم المشروع'];
                    const supervisor = row['اسم المشرف'];

                    if (student && !db.students.includes(student)) db.students.push(student);
                    if (project && !db.projects.find(p => p.title === project)) {
                        db.projects.push({ title: project, supervisor: supervisor || "" });
                    }
                });

                localStorage.setItem('grad_db', JSON.stringify(db));
                renderAdminLists();
                alert("تم استيراد البيانات بنجاح!");
            };
            reader.readAsArrayBuffer(file);
        }

        // إدارة المشاريع والطلاب يدوياً
        function addProject() {
            const title = document.getElementById('newProject').value.trim();
            const sup = document.getElementById('newSupervisor').value.trim();
            if(title) {
                db.projects.push({ title: title, supervisor: sup });
                localStorage.setItem('grad_db', JSON.stringify(db));
                document.getElementById('newProject').value = '';
                document.getElementById('newSupervisor').value = '';
                renderAdminLists();
            }
        }

        function addItem(type, inputId) {
            const val = document.getElementById(inputId).value.trim();
            if(val) {
                db[type].push(val);
                localStorage.setItem('grad_db', JSON.stringify(db));
                document.getElementById(inputId).value = '';
                renderAdminLists();
            }
        }

        function removeItem(type, index) {
            db[type].splice(index, 1);
            localStorage.setItem('grad_db', JSON.stringify(db));
            renderAdminLists();
        }

        function renderAdminLists() {
            const pList = document.getElementById('adminProjectsList');
            const sList = document.getElementById('adminStudentsList');
            pList.innerHTML = db.projects.map((p, i) => `<li class="flex justify-between items-center bg-white p-2 rounded shadow-sm text-sm">
                <span><b>${p.title}</b> <small class="text-slate-400">(${p.supervisor})</small></span>
                <button onclick="removeItem('projects', ${i})" class="text-rose-500">🗑️</button></li>`).join('');
            sList.innerHTML = db.students.map((s, i) => `<li class="flex justify-between items-center bg-white p-2 rounded shadow-sm text-sm"><span>${s}</span><button onclick="removeItem('students', ${i})" class="text-rose-500">🗑️</button></li>`).join('');
        }

        function loadProjectData() {
            const select = document.getElementById('projectSelect');
            const input = document.getElementById('projectTitle');
            const supInput = document.getElementById('supervisorName');
            
            if(select.value === 'custom') { 
                input.classList.remove('hidden'); 
                input.value = ""; 
            } else { 
                input.classList.add('hidden'); 
                input.value = select.value;
                const selectedSup = select.options[select.selectedIndex].getAttribute('data-sup');
                if(supInput && selectedSup) supInput.value = selectedSup;
            }
        }

        function syncSharedMarks() {
            isSyncing = !isSyncing;
            const btn = document.getElementById('syncBtn');
            btn.innerText = isSyncing ? "إيقاف الدمج" : "تفعيل الدمج";
            btn.className = isSyncing ? "bg-red-500 text-white px-4 py-2 rounded-lg text-xs font-bold" : "bg-amber-500 text-white px-4 py-2 rounded-lg text-xs font-bold";
        }

        function applySync(id, val) {
            document.querySelectorAll(`.score-input[data-id="${id}"]`).forEach(inp => { inp.value = val; updateTotal(inp.closest('.student-card')); });
        }

        function updateTotal(card) {
            let total = 0;
            card.querySelectorAll('.score-input').forEach(i => total += (parseInt(i.value) || 0));
            card.querySelector('.student-total-display').innerText = total;
            const res = card.querySelector('.student-result-text');
            if (total >= 90) { res.innerText = "امتياز"; res.className = "student-result-text font-bold text-[10px] px-2 py-1 bg-indigo-100 rounded-full text-indigo-700"; }
            else if (total >= 50) { res.innerText = "ناجح"; res.className = "student-result-text font-bold text-[10px] px-2 py-1 bg-emerald-100 rounded-full text-emerald-700"; }
            else { res.innerText = "راسب"; res.className = "student-result-text font-bold text-[10px] px-2 py-1 bg-rose-100 rounded-full text-rose-700"; }
        }

        function exportToExcel() {
            const data = [["تقرير التقييم الرقمي"],["المشروع", document.getElementById('projectTitle').value],["المشرف", document.getElementById('supervisorName').value],[],["الاسم","المجموع","النتيجة"]];
            document.querySelectorAll('.student-card').forEach(c => {
                const n = c.querySelector('.student-name-input').value;
                if(n) data.push([n, c.querySelector('.student-total-display').innerText, c.querySelector('.student-result-text').innerText]);
            });
            const ws = XLSX.utils.aoa_to_sheet(data);
            const wb = XLSX.utils.book_new();
            XLSX.utils.book_append_sheet(wb, ws, "Evaluation");
            XLSX.writeFile(wb, "Final_Project_Report.xlsx");
        }

        function shareWhatsApp() {
            let msg = `*تقرير تقييم مشاريع التخرج*%0A*المشروع:* ${document.getElementById('projectTitle').value}%0A*المشرف:* ${document.getElementById('supervisorName').value}%0A------------------%0A`;
            document.querySelectorAll('.student-card').forEach(c => {
                const n = c.querySelector('.student-name-input').value;
                if(n) msg += `👤 *${n}*: ${c.querySelector('.student-total-display').innerText}/100 - (${c.querySelector('.student-result-text').innerText})%0A`;
            });
            window.open(`https://wa.me/?text=${msg}`, '_blank');
        }
    </script>
</body>
</html>
