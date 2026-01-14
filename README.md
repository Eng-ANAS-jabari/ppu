<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>نظام تقييم مشاريع التخرج</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Tajawal:wght@400;500;700;900&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    <style>
        :root { --primary: #4f46e5; --success: #10b981; --warning: #f59e0b; --danger: #ef4444; }
        body { font-family: 'Tajawal', sans-serif; background: linear-gradient(135deg, #f8fafc 0%, #e2e8f0 100%); min-height: 100vh; }
        .card { background: white; border-radius: 1.5rem; box-shadow: 0 4px 20px rgba(0, 0, 0, 0.05); transition: all 0.3s ease; border: 1px solid #e2e8f0; }
        .gradient-primary { background: linear-gradient(135deg, var(--primary) 0%, #3730a3 100%); }
        .status-badge { padding: 0.25rem 1rem; border-radius: 9999px; font-size: 0.75rem; font-weight: 900; }
        @media print { .no-print { display: none !important; } .print-only { display: block !important; } body { background: white !important; } }
    </style>
</head>
<body class="p-4 md:p-8">

    <div id="roleSelection" class="max-w-6xl mx-auto">
        <div class="text-center mb-12">
            <div id="logoDisplay" class="mb-6 h-24 flex justify-center items-center"></div>
            <h1 class="text-4xl font-black text-slate-800 mb-3">نظام تقييم مشاريع التخرج</h1>
        </div>
        
        <div class="grid grid-cols-1 md:grid-cols-3 gap-8">
            <div class="card p-8 text-center cursor-pointer" onclick="setRole('supervisor')">
                <div class="w-16 h-16 gradient-primary rounded-2xl flex items-center justify-center mx-auto mb-6 text-white text-2xl">
                    <i class="fas fa-user-tie"></i>
                </div>
                <h3 class="text-xl font-bold">تقييم المشرف</h3>
            </div>
            </div>
    </div>

    <div id="mainContainer" class="hidden max-w-7xl mx-auto">
        <div class="flex justify-between items-center mb-8">
            <h1 id="headerTitle" class="text-3xl font-black">نموذج التقييم</h1>
            <div id="currentDate" class="font-bold text-slate-600"></div>
        </div>

        <div class="card p-8 mb-8">
            <select id="projectSelect" class="w-full p-3 border-2 rounded-xl" onchange="loadProjectData()">
                <option value="">-- اختر المشروع --</option>
            </select>
        </div>

        <div id="studentsWrapper" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6"></div>

        <div class="flex flex-wrap justify-center gap-4 pt-8 no-print">
            <button onclick="exportToExcel()" class="px-8 py-3 bg-emerald-600 text-white rounded-xl font-bold">
                <i class="fas fa-file-excel mr-2"></i>تصدير Excel
            </button>
            <button onclick="shareWhatsApp()" class="px-8 py-3 bg-green-500 text-white rounded-xl font-bold">
                <i class="fab fa-whatsapp mr-2"></i>مشاركة واتساب
            </button>
            <button onclick="window.print()" class="px-8 py-3 bg-slate-800 text-white rounded-xl font-bold">
                <i class="fas fa-print mr-2"></i>طباعة التقرير
            </button>
        </div>
    </div>

    <script>
        // --- وظيفة مشاركة الواتساب ---
        function shareWhatsApp() {
            const project = document.getElementById('projectSelect').options[document.getElementById('projectSelect').selectedIndex].text;
            const students = document.querySelectorAll('.student-card');
            
            let message = `📊 *تقرير تقييم مشروع:* ${project}\n`;
            message += `📅 *التاريخ:* ${document.getElementById('currentDate').textContent}\n\n`;
            
            students.forEach(s => {
                const name = s.querySelector('.student-name').textContent;
                const total = s.querySelector('.student-total').textContent;
                message += `• ${name}: *${total}/100*\n`;
            });

            window.open(`https://wa.me/?text=${encodeURIComponent(message)}`, '_blank');
        }

        // --- وظيفة تصدير الإكسل ---
        function exportToExcel() {
            const data = [];
            document.querySelectorAll('.student-card').forEach(s => {
                data.push({
                    'اسم الطالب': s.querySelector('.student-name').textContent,
                    'الدرجة': s.querySelector('.student-total').textContent,
                    'المشروع': document.getElementById('projectSelect').value
                });
            });
            const ws = XLSX.utils.json_to_sheet(data);
            const wb = XLSX.utils.book_new();
            XLSX.utils.book_append_sheet(wb, ws, "النتائج");
            XLSX.writeFile(wb, "تقييم_المشاريع.xlsx");
        }

        // إعدادات التاريخ والتهيئة
        document.getElementById('currentDate').textContent = new Date().toLocaleDateString('ar-SA');
        
        function setRole(role) {
            document.getElementById('roleSelection').classList.add('hidden');
            document.getElementById('mainContainer').classList.remove('hidden');
            document.getElementById('headerTitle').textContent = role === 'supervisor' ? 'تقييم المشرف الأكاديمي' : 'تقييم لجنة المناقشة';
        }
    </script>
</body>
</html>
