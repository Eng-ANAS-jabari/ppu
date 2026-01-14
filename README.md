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
        :root {
            --primary: #4f46e5;
            --primary-dark: #3730a3;
            --success: #10b981;
            --warning: #f59e0b;
            --danger: #ef4444;
        }
        
        body { 
            font-family: 'Tajawal', sans-serif; 
            background: linear-gradient(135deg, #f8fafc 0%, #e2e8f0 100%);
            min-height: 100vh;
        }
        
        .card {
            background: white;
            border-radius: 1.5rem;
            box-shadow: 0 4px 20px rgba(0, 0, 0, 0.05);
            transition: all 0.3s ease;
            border: 1px solid #e2e8f0;
        }
        
        .card:hover {
            box-shadow: 0 8px 30px rgba(0, 0, 0, 0.08);
            transform: translateY(-2px);
        }
        
        .score-input {
            border: 2px solid #e2e8f0;
            transition: all 0.2s;
            text-align: center;
            font-weight: 700;
            font-size: 1.1rem;
            border-radius: 0.75rem;
        }
        
        .score-input:focus {
            border-color: var(--primary);
            box-shadow: 0 0 0 3px rgba(79, 70, 229, 0.1);
            outline: none;
        }
        
        .score-input.good { border-color: var(--success); background-color: #f0fdf4; }
        .score-input.average { border-color: var(--warning); background-color: #fefce8; }
        .score-input.poor { border-color: var(--danger); background-color: #fef2f2; }
        
        .gradient-primary {
            background: linear-gradient(135deg, var(--primary) 0%, var(--primary-dark) 100%);
        }
        
        .gradient-success {
            background: linear-gradient(135deg, var(--success) 0%, #059669 100%);
        }
        
        .gradient-warning {
            background: linear-gradient(135deg, var(--warning) 0%, #d97706 100%);
        }
        
        .gradient-danger {
            background: linear-gradient(135deg, var(--danger) 0%, #dc2626 100%);
        }
        
        .status-badge {
            padding: 0.25rem 1rem;
            border-radius: 9999px;
            font-size: 0.75rem;
            font-weight: 900;
            text-transform: uppercase;
            letter-spacing: 0.05em;
        }
        
        .status-excellent { background: #dbeafe; color: #1e40af; }
        .status-good { background: #d1fae5; color: #065f46; }
        .status-average { background: #fef3c7; color: #92400e; }
        .status-fail { background: #fee2e2; color: #991b1b; }
        
        .logo-container {
            max-width: 200px;
            max-height: 100px;
            display: flex;
            align-items: center;
            justify-content: center;
            margin: 0 auto;
        }
        
        .logo-img {
            max-width: 100%;
            max-height: 100px;
            object-fit: contain;
        }
        
        .copyright {
            text-align: center;
            padding: 1rem;
            margin-top: 2rem;
            color: #64748b;
            font-size: 0.875rem;
            border-top: 1px solid #e2e8f0;
        }
        
        .developer {
            color: var(--primary);
            font-weight: bold;
            margin-right: 0.25rem;
        }
        
        .print-only { display: none; }
        
        @media print {
            .no-print { display: none !important; }
            .print-only { display: block !important; }
            body { background: white !important; padding: 0 !important; }
            .card { box-shadow: none !important; border: 1px solid #ddd !important; }
            .score-input { border: 1px solid #ccc !important; background: white !important; }
            .logo-container { max-width: 150px; }
        }
        
        ::-webkit-scrollbar {
            width: 8px;
            height: 8px;
        }
        
        ::-webkit-scrollbar-track {
            background: #f1f5f9;
            border-radius: 10px;
        }
        
        ::-webkit-scrollbar-thumb {
            background: #cbd5e1;
            border-radius: 10px;
        }
        
        ::-webkit-scrollbar-thumb:hover {
            background: #94a3b8;
        }
        
        .animate-fade-in {
            animation: fadeIn 0.5s ease-in-out;
        }
        
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }
    </style>
</head>
<body class="p-4 md:p-8">

    <!-- شاشة البداية -->
    <div id="roleSelection" class="max-w-6xl mx-auto animate-fade-in">
        <!-- الرأس مع الشعار -->
        <div class="text-center mb-12">
            <!-- منطقة عرض الشعار -->
            <div id="logoDisplay" class="logo-container mb-6">
                <!-- سيتم عرض الشعار هنا ديناميكياً -->
            </div>
            
            <h1 class="text-4xl md:text-5xl font-black text-slate-800 mb-3">نظام تقييم مشاريع التخرج</h1>
            <p class="text-slate-600 text-lg max-w-2xl mx-auto">
                نظام متكامل لإدارة وتقييم مشاريع التخرج الجامعية
            </p>
        </div>
        
        <!-- بطاقات الاختيار -->
        <div class="grid grid-cols-1 md:grid-cols-3 gap-8 mb-12">
            <!-- لوحة الإدارة -->
            <div class="card p-8 text-center hover:border-indigo-300 group cursor-pointer" onclick="requestAdminAccess()">
                <div class="w-16 h-16 gradient-primary rounded-2xl flex items-center justify-center mx-auto mb-6 group-hover:scale-110 transition-transform duration-300">
                    <i class="fas fa-cogs text-2xl text-white"></i>
                </div>
                <h3 class="text-2xl font-black text-slate-800 mb-3">لوحة الإدارة</h3>
                <p class="text-slate-600 mb-4 text-sm">
                    إدارة المشاريع والطلاب، استيراد البيانات من ملفات Excel
                </p>
            </div>
            
            <!-- نموذج المشرف -->
            <div class="card p-8 text-center hover:border-indigo-300 group cursor-pointer" onclick="setRole('supervisor')">
                <div class="w-16 h-16 gradient-success rounded-2xl flex items-center justify-center mx-auto mb-6 group-hover:scale-110 transition-transform duration-300">
                    <i class="fas fa-user-tie text-2xl text-white"></i>
                </div>
                <h3 class="text-2xl font-black text-slate-800 mb-3">تقييم المشرف</h3>
                <p class="text-slate-600 mb-4 text-sm">
                    تقييم الطلاب خلال مرحلة الإشراف على المشروع
                </p>
            </div>
            
            <!-- نموذج المناقش -->
            <div class="card p-8 text-center hover:border-indigo-300 group cursor-pointer" onclick="setRole('examiner')">
                <div class="w-16 h-16 gradient-warning rounded-2xl flex items-center justify-center mx-auto mb-6 group-hover:scale-110 transition-transform duration-300">
                    <i class="fas fa-user-graduate text-2xl text-white"></i>
                </div>
                <h3 class="text-2xl font-black text-slate-800 mb-3">تقييم المناقش</h3>
                <p class="text-slate-600 mb-4 text-sm">
                    تقييم نهائي للطلاب خلال مناقشة المشروع
                </p>
            </div>
        </div>
        
        <!-- معلومات سريعة -->
        <div class="bg-white card p-8 border-dashed border-2 border-slate-200">
            <div class="flex flex-col md:flex-row items-center justify-between gap-6">
                <div class="text-center md:text-right">
                    <h4 class="font-black text-slate-800 text-lg mb-2">💡 كيف يعمل النظام؟</h4>
                    <p class="text-slate-600 text-sm">
                        1. قم بإضافة المشاريع والطلاب عبر لوحة الإدارة<br>
                        2. اختر نوع التقييم (مشرف / مناقش)<br>
                        3. أدخل الدرجات للطلاب وحفظ التقرير<br>
                        4. قم بتصدير النتائج كملف Excel أو مشاركتها
                    </p>
                </div>
                <div class="flex items-center gap-3">
                    <div class="text-center">
                        <div class="text-2xl font-black text-indigo-600" id="statsProjects">0</div>
                        <div class="text-xs text-slate-500">المشاريع</div>
                    </div>
                    <div class="h-10 w-px bg-slate-200"></div>
                    <div class="text-center">
                        <div class="text-2xl font-black text-emerald-600" id="statsStudents">0</div>
                        <div class="text-xs text-slate-500">الطلاب</div>
                    </div>
                    <div class="h-10 w-px bg-slate-200"></div>
                    <div class="text-center">
                        <div class="text-2xl font-black text-amber-600" id="statsEvaluations">0</div>
                        <div class="text-xs text-slate-500">التقييمات</div>
                    </div>
                </div>
            </div>
        </div>
        
        <!-- حقوق الملكية -->
        <div class="copyright no-print">
            <p>© 2024 نظام تقييم مشاريع التخرج | إعداد: <span class="developer">المهندس أنس</span></p>
        </div>
    </div>

    <!-- لوحة الإدارة -->
    <div id="adminPanel" class="hidden max-w-6xl mx-auto">
        <!-- رأس اللوحة -->
        <div class="flex flex-col md:flex-row items-start md:items-center justify-between gap-6 mb-8">
            <div>
                <button onclick="goBack()" class="flex items-center gap-2 text-slate-600 hover:text-slate-800 mb-4 no-print">
                    <i class="fas fa-arrow-right"></i>
                    <span class="font-bold">العودة للرئيسية</span>
                </button>
                <h2 class="text-3xl font-black text-slate-800">لوحة إدارة النظام</h2>
                <p class="text-slate-600">إدارة كاملة لمشاريع التخرج والطلاب والمشرفين</p>
            </div>
            <div class="flex gap-3 no-print">
                <button onclick="exportFullDatabase()" class="px-5 py-2.5 bg-white border border-slate-300 text-slate-700 rounded-xl font-bold hover:bg-slate-50">
                    <i class="fas fa-download mr-2"></i>تصدير النسخة
                </button>
                <button onclick="importBackup()" class="px-5 py-2.5 bg-indigo-600 text-white rounded-xl font-bold hover:bg-indigo-700">
                    <i class="fas fa-upload mr-2"></i>استيراد بيانات
                </button>
            </div>
        </div>
        
        <!-- إشعارات وإحصائيات -->
        <div class="grid grid-cols-1 md:grid-cols-4 gap-6 mb-8">
            <div class="card p-6 bg-gradient-to-r from-indigo-50 to-white">
                <div class="flex justify-between items-center mb-4">
                    <i class="fas fa-project-diagram text-2xl text-indigo-500"></i>
                    <span class="text-2xl font-black text-indigo-700" id="adminProjectsCount">0</span>
                </div>
                <div class="text-sm font-bold text-indigo-800">المشاريع المسجلة</div>
                <div class="text-xs text-indigo-600">المشاريع النشطة في النظام</div>
            </div>
            
            <div class="card p-6 bg-gradient-to-r from-emerald-50 to-white">
                <div class="flex justify-between items-center mb-4">
                    <i class="fas fa-user-graduate text-2xl text-emerald-500"></i>
                    <span class="text-2xl font-black text-emerald-700" id="adminStudentsCount">0</span>
                </div>
                <div class="text-sm font-bold text-emerald-800">الطلاب المسجلين</div>
                <div class="text-xs text-emerald-600">الطلاب المشاركين بالمشاريع</div>
            </div>
            
            <div class="card p-6 bg-gradient-to-r from-amber-50 to-white">
                <div class="flex justify-between items-center mb-4">
                    <i class="fas fa-user-tie text-2xl text-amber-500"></i>
                    <span class="text-2xl font-black text-amber-700" id="adminSupervisorsCount">0</span>
                </div>
                <div class="text-sm font-bold text-amber-800">المشرفين</div>
                <div class="text-xs text-amber-600">المشرفين الأكاديميين</div>
            </div>
            
            <div class="card p-6 bg-gradient-to-r from-purple-50 to-white">
                <div class="flex justify-between items-center mb-4">
                    <i class="fas fa-chart-line text-2xl text-purple-500"></i>
                    <span class="text-2xl font-black text-purple-700" id="adminEvaluationsCount">0</span>
                </div>
                <div class="text-sm font-bold text-purple-800">التقييمات</div>
                <div class="text-xs text-purple-600">التقييمات المحفوظة</div>
            </div>
        </div>
        
        <!-- قسم إدارة الشعار -->
        <div class="card p-8 mb-8">
            <h3 class="text-xl font-black text-slate-800 mb-6 flex items-center gap-3">
                <i class="fas fa-image text-purple-500"></i>
                إدارة الشعار المؤسسي
            </h3>
            
            <div class="grid grid-cols-1 lg:grid-cols-2 gap-8">
                <!-- معاينة الشعار الحالي -->
                <div class="bg-gradient-to-r from-purple-50 to-pink-50 p-8 rounded-2xl border-2 border-dashed border-purple-200 text-center">
                    <h4 class="font-bold text-purple-800 mb-4">الشعار الحالي</h4>
                    
                    <div id="currentLogoPreview" class="logo-container mb-6 p-4 bg-white rounded-xl">
                        <!-- سيتم عرض الشعار الحالي هنا -->
                    </div>
                    
                    <div class="text-sm text-purple-600">
                        <div class="mb-2">
                            <i class="fas fa-info-circle mr-2"></i>
                            <span id="logoInfo">لم يتم رفع شعار بعد</span>
                        </div>
                        <div>
                            <i class="fas fa-ruler mr-2"></i>
                            الأبعاد الموصى بها: 200 × 100 بكسل
                        </div>
                    </div>
                </div>
                
                <!-- رفع شعار جديد -->
                <div class="bg-gradient-to-r from-blue-50 to-cyan-50 p-8 rounded-2xl border-2 border-dashed border-blue-200">
                    <h4 class="font-bold text-blue-800 mb-4">رفع شعار جديد</h4>
                    
                    <div class="space-y-4">
                        <!-- معاينة الشعار الجديد -->
                        <div id="newLogoPreview" class="logo-container p-4 bg-white rounded-xl hidden">
                            <img id="previewImage" class="logo-img" alt="معاينة الشعار">
                        </div>
                        
                        <!-- زر رفع الملف -->
                        <div>
                            <input type="file" id="logoUpload" accept="image/*" class="hidden" onchange="previewLogo(event)">
                            <button onclick="document.getElementById('logoUpload').click()" class="w-full bg-gradient-to-r from-blue-500 to-blue-600 text-white py-3.5 rounded-xl font-bold hover:shadow-lg transition-all">
                                <i class="fas fa-upload mr-2"></i>اختر صورة الشعار
                            </button>
                        </div>
                        
                        <!-- إعدادات الشعار -->
                        <div class="space-y-3">
                            <div>
                                <label class="block text-sm font-bold text-blue-700 mb-2">اسم الشعار (اختياري)</label>
                                <input type="text" id="logoName" class="w-full p-3 border border-blue-300 rounded-xl" placeholder="مثال: شعار الجامعة">
                            </div>
                            
                            <div class="grid grid-cols-2 gap-3">
                                <div>
                                    <label class="block text-sm font-bold text-blue-700 mb-2">العرض</label>
                                    <select id="logoWidth" class="w-full p-3 border border-blue-300 rounded-xl bg-white">
                                        <option value="auto">تلقائي</option>
                                        <option value="150">150px</option>
                                        <option value="200">200px</option>
                                        <option value="250">250px</option>
                                    </select>
                                </div>
                                <div>
                                    <label class="block text-sm font-bold text-blue-700 mb-2">حجم العرض</label>
                                    <select id="logoSize" class="w-full p-3 border border-blue-300 rounded-xl bg-white">
                                        <option value="small">صغير</option>
                                        <option value="medium" selected>متوسط</option>
                                        <option value="large">كبير</option>
                                    </select>
                                </div>
                            </div>
                        </div>
                        
                        <!-- زر الحفظ -->
                        <button onclick="saveLogo()" class="w-full bg-gradient-to-r from-green-500 to-emerald-600 text-white py-3.5 rounded-xl font-bold hover:shadow-lg transition-all mt-4">
                            <i class="fas fa-save mr-2"></i>حفظ الشعار
                        </button>
                        
                        <!-- زر إزالة الشعار -->
                        <button onclick="removeLogo()" class="w-full bg-gradient-to-r from-rose-500 to-red-600 text-white py-3.5 rounded-xl font-bold hover:shadow-lg transition-all">
                            <i class="fas fa-trash-alt mr-2"></i>حذف الشعار
                        </button>
                    </div>
                </div>
            </div>
        </div>

        <div class="grid grid-cols-1 lg:grid-cols-2 gap-8 mb-8">
            <!-- إدارة المشاريع -->
            <div class="card p-6">
                <div class="flex items-center justify-between mb-6">
                    <h3 class="text-xl font-black text-slate-800 flex items-center gap-3">
                        <i class="fas fa-project-diagram text-indigo-500"></i>
                        إدارة المشاريع
                    </h3>
                    <span class="bg-indigo-100 text-indigo-800 text-xs font-bold px-3 py-1 rounded-full">
                        <span id="projectsListCount">0</span> مشروع
                    </span>
                </div>
                
                <!-- نموذج إضافة مشروع -->
                <div class="bg-slate-50 p-5 rounded-2xl mb-6">
                    <h4 class="font-bold text-slate-700 mb-4">إضافة مشروع جديد</h4>
                    <div class="space-y-4">
                        <input type="text" id="newProjectTitle" class="w-full p-3 border border-slate-300 rounded-xl" placeholder="اسم المشروع">
                        <input type="text" id="newProjectSupervisor" class="w-full p-3 border border-slate-300 rounded-xl" placeholder="اسم المشرف">
                        <div class="grid grid-cols-2 gap-3">
                            <input type="text" id="newProjectYear" class="p-3 border border-slate-300 rounded-xl" placeholder="السنة (مثال: 2024)" value="2024">
                            <select id="newProjectStatus" class="p-3 border border-slate-300 rounded-xl bg-white">
                                <option value="active">نشط</option>
                                <option value="completed">مكتمل</option>
                                <option value="pending">قيد الإعداد</option>
                            </select>
                        </div>
                        <button onclick="addNewProject()" class="w-full gradient-primary text-white py-3.5 rounded-xl font-bold hover:shadow-lg transition-all">
                            <i class="fas fa-plus-circle mr-2"></i>إضافة المشروع
                        </button>
                    </div>
                </div>
                
                <!-- قائمة المشاريع -->
                <div class="max-h-80 overflow-y-auto pr-2">
                    <div class="text-sm font-bold text-slate-500 mb-3">قائمة المشاريع المسجلة</div>
                    <div id="adminProjectsList" class="space-y-3"></div>
                </div>
            </div>
            
            <!-- إدارة الطلاب -->
            <div class="card p-6">
                <div class="flex items-center justify-between mb-6">
                    <h3 class="text-xl font-black text-slate-800 flex items-center gap-3">
                        <i class="fas fa-users text-emerald-500"></i>
                        إدارة الطلاب
                    </h3>
                    <span class="bg-emerald-100 text-emerald-800 text-xs font-bold px-3 py-1 rounded-full">
                        <span id="studentsListCount">0</span> طالب
                    </span>
                </div>
                
                <!-- نموذج إضافة طالب -->
                <div class="bg-slate-50 p-5 rounded-2xl mb-6">
                    <h4 class="font-bold text-slate-700 mb-4">إضافة طالب جديد</h4>
                    <div class="space-y-4">
                        <input type="text" id="newStudentName" class="w-full p-3 border border-slate-300 rounded-xl" placeholder="الاسم الكامل للطالب">
                        <div class="grid grid-cols-2 gap-3">
                            <select id="newStudentProject" class="p-3 border border-slate-300 rounded-xl bg-white">
                                <option value="">اختر المشروع</option>
                            </select>
                            <input type="text" id="newStudentId" class="p-3 border border-slate-300 rounded-xl" placeholder="الرقم الجامعي">
                        </div>
                        <button onclick="addNewStudent()" class="w-full gradient-success text-white py-3.5 rounded-xl font-bold hover:shadow-lg transition-all">
                            <i class="fas fa-user-plus mr-2"></i>إضافة الطالب
                        </button>
                    </div>
                </div>
                
                <!-- قائمة الطلاب -->
                <div class="max-h-80 overflow-y-auto pr-2">
                    <div class="text-sm font-bold text-slate-500 mb-3">قائمة الطلاب المسجلين</div>
                    <div id="adminStudentsList" class="space-y-3"></div>
                </div>
            </div>
        </div>
        
        <!-- استيراد البيانات -->
        <div class="card p-8 mb-8">
            <h3 class="text-xl font-black text-slate-800 mb-6 flex items-center gap-3">
                <i class="fas fa-file-excel text-green-500"></i>
                استيراد البيانات من ملف Excel
            </h3>
            
            <div class="bg-gradient-to-r from-green-50 to-emerald-50 p-8 rounded-2xl border-2 border-dashed border-green-200 text-center">
                <div class="w-20 h-20 bg-green-100 rounded-2xl flex items-center justify-center mx-auto mb-6">
                    <i class="fas fa-cloud-upload-alt text-3xl text-green-500"></i>
                </div>
                <h4 class="font-bold text-green-800 text-lg mb-3">رفع ملف البيانات</h4>
                <p class="text-green-600 mb-6 max-w-2xl mx-auto text-sm">
                    يمكنك استيرال بيانات الطلاب والمشاريع والمشرفين دفعة واحدة من ملف Excel.
                    يجب أن يحتوي الملف على الأعمدة التالية: <span class="font-bold">اسم الطالب، اسم المشروع، اسم المشرف</span>
                </p>
                
                <div class="flex flex-col sm:flex-row gap-4 justify-center">
                    <input type="file" id="excelUpload" accept=".xlsx, .xls, .csv" class="hidden" onchange="importExcelFile(event)">
                    <button onclick="document.getElementById('excelUpload').click()" class="bg-gradient-to-r from-green-500 to-emerald-600 text-white px-8 py-3.5 rounded-xl font-bold shadow-lg hover:shadow-xl transition-all">
                        <i class="fas fa-upload mr-2"></i>اختر ملف Excel
                    </button>
                    <button onclick="downloadExcelTemplate()" class="bg-white border-2 border-green-300 text-green-700 px-8 py-3.5 rounded-xl font-bold hover:bg-green-50 transition-all">
                        <i class="fas fa-download mr-2"></i>تحميل النموذج
                    </button>
                </div>
            </div>
        </div>
        
        <!-- حقوق الملكية -->
        <div class="copyright no-print">
            <p>© 2024 نظام تقييم مشاريع التخرج | إعداد: <span class="developer">المهندس أنس</span></p>
        </div>
    </div>

    <!-- نموذج التقييم -->
    <div id="mainContainer" class="hidden max-w-7xl mx-auto">
        <!-- رأس النموذج -->
        <div class="flex flex-col md:flex-row items-start md:items-center justify-between gap-6 mb-8">
            <div>
                <button onclick="goBack()" class="flex items-center gap-2 text-slate-600 hover:text-slate-800 mb-4 no-print">
                    <i class="fas fa-arrow-right"></i>
                    <span class="font-bold">العودة للرئيسية</span>
                </button>
                <h1 id="headerTitle" class="text-3xl font-black text-slate-800"></h1>
                <p id="headerSubtitle" class="text-slate-600"></p>
            </div>
            <div class="flex items-center gap-4">
                <div class="text-right hidden md:block">
                    <div class="text-xs font-bold text-slate-500">التاريخ</div>
                    <div class="text-lg font-black text-slate-800" id="currentDate">--</div>
                </div>
                <button onclick="saveAllEvaluations()" class="px-5 py-2.5 gradient-success text-white rounded-xl font-bold hover:shadow-lg no-print">
                    <i class="fas fa-save mr-2"></i>حفظ التقييم
                </button>
            </div>
        </div>
        
        <!-- معلومات المشروع -->
        <div class="card p-8 mb-8">
            <div class="grid grid-cols-1 md:grid-cols-4 gap-6">
                <div class="space-y-2">
                    <label class="block text-sm font-bold text-slate-700">اختر المشروع</label>
                    <select id="projectSelect" class="w-full p-3 border-2 border-slate-200 rounded-xl font-bold text-slate-800 bg-white outline-none focus:border-indigo-500 transition-all" onchange="loadProjectData()">
                        <option value="">-- اختر المشروع --</option>
                    </select>
                </div>
                
                <div id="dynamicFields" class="md:col-span-3 grid grid-cols-1 md:grid-cols-3 gap-6">
                    <!-- سيتم تعبئتها تلقائياً -->
                </div>
            </div>
            
            <!-- ميزة الدمج (للمشرف فقط) -->
            <div id="syncSection" class="mt-6 bg-gradient-to-r from-amber-50 to-orange-50 p-5 rounded-2xl border border-amber-200 hidden">
                <div class="flex items-center justify-between">
                    <div class="flex items-center gap-4">
                        <div class="bg-amber-500 text-white p-3 rounded-xl">
                            <i class="fas fa-sync-alt"></i>
                        </div>
                        <div>
                            <div class="font-bold text-amber-800">دمج الدرجات المماثلة</div>
                            <div class="text-sm text-amber-600">تطبيق نفس الدرجة على جميع الطلاب للمعايير المشتركة</div>
                        </div>
                    </div>
                    <button type="button" onclick="toggleSync()" id="syncToggle" class="bg-gradient-to-r from-amber-500 to-amber-600 text-white px-5 py-2.5 rounded-xl font-bold hover:shadow-lg">
                        <i class="fas fa-link mr-2"></i>تفعيل الدمج
                    </button>
                </div>
            </div>
        </div>
        
        <!-- شبكة الطلاب -->
        <div class="mb-8">
            <div class="flex items-center justify-between mb-6">
                <h3 class="text-2xl font-black text-slate-800 flex items-center gap-3">
                    <i class="fas fa-user-graduate text-indigo-500"></i>
                    تقييم الطلاب
                </h3>
                <div class="text-sm font-bold text-slate-500">
                    <span id="studentsCount">0</span> طالب
                </div>
            </div>
            
            <div id="studentsWrapper" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
                <!-- سيتم تعبئتها تلقائياً -->
                <div class="col-span-full text-center py-16 opacity-40">
                    <div class="text-6xl mb-6">🔍</div>
                    <h4 class="text-2xl font-black text-slate-600 mb-3">لم يتم اختيار مشروع</h4>
                    <p class="text-slate-500">يرجى اختيار مشروع من القائمة أعلاه لبدء عملية التقييم</p>
                </div>
            </div>
        </div>
        
        <!-- ملخص النتائج -->
        <div id="resultsSummary" class="card p-8 mb-8 hidden">
            <h3 class="text-2xl font-black text-slate-800 mb-6 flex items-center gap-3">
                <i class="fas fa-chart-bar text-indigo-500"></i>
                ملخص نتائج التقييم
            </h3>
            <div class="grid grid-cols-1 md:grid-cols-4 gap-6" id="summaryCards">
                <!-- سيتم تعبئتها تلقائياً -->
            </div>
        </div>
        
        <!-- أزرار الإجراءات -->
        <div class="flex flex-wrap justify-center gap-4 pt-8 border-t border-slate-200 no-print">
            <button onclick="showResultsSummary()" class="px-8 py-3.5 bg-gradient-to-r from-indigo-600 to-indigo-700 text-white rounded-xl font-bold hover:shadow-lg transition-all">
                <i class="fas fa-chart-pie mr-2"></i>عرض الملخص
            </button>
            <button onclick="exportToExcel()" class="px-8 py-3.5 bg-gradient-to-r from-emerald-600 to-emerald-700 text-white rounded-xl font-bold hover:shadow-lg transition-all">
                <i class="fas fa-file-excel mr-2"></i>تصدير Excel
            </button>
            <button onclick="shareWhatsApp()" class="px-8 py-3.5 bg-gradient-to-r from-green-500 to-green-600 text-white rounded-xl font-bold hover:shadow-lg transition-all">
                <i class="fab fa-whatsapp mr-2"></i>مشاركة عبر واتساب
            </button>
            <button onclick="printReport()" class="px-8 py-3.5 bg-gradient-to-r from-slate-700 to-slate-800 text-white rounded-xl font-bold hover:shadow-lg transition-all">
                <i class="fas fa-print mr-2"></i>طباعة / PDF
            </button>
        </div>
        
        <!-- إشعار الحفظ -->
        <div id="saveNotification" class="fixed bottom-6 right-6 bg-gradient-to-r from-emerald-500 to-green-600 text-white p-4 rounded-xl shadow-2xl z-50 hidden no-print animate-fade-in">
            <div class="flex items-center gap-3">
                <i class="fas fa-check-circle text-xl"></i>
                <div>
                    <div class="font-bold">تم الحفظ بنجاح</div>
                    <div class="text-xs opacity-90">جميع البيانات محفوظة تلقائياً</div>
                </div>
            </div>
        </div>
        
        <!-- حقوق الملكية -->
        <div class="copyright no-print mt-8">
            <p>© 2024 نظام تقييم مشاريع التخرج | إعداد: <span class="developer">المهندس أنس</span></p>
        </div>
    </div>

    <!-- قالب بطاقة الطالب -->
    <template id="studentTemplate">
        <div class="card p-6 hover:border-indigo-300 transition-all duration-300 h-full">
            <!-- معلومات الطالب -->
            <div class="flex justify-between items-start mb-6">
                <div class="flex-1">
                    <div class="text-xs font-bold text-slate-400 uppercase tracking-widest mb-1">طالب مشروع تخرج</div>
                    <h4 class="student-name text-xl font-black text-slate-800 mb-1"></h4>
                    <div class="text-xs text-slate-500 student-id"></div>
                </div>
                <div class="text-3xl opacity-30">
                    👤
                </div>
            </div>
            
            <!-- معايير التقييم -->
            <div class="criteria-list space-y-4 mb-6"></div>
            
            <!-- النتيجة النهائية -->
            <div class="mt-auto pt-6 border-t border-slate-100">
                <div class="flex justify-between items-center mb-3">
                    <div class="text-xs font-bold text-slate-400">الدرجة النهائية</div>
                    <div class="text-xs font-bold text-slate-400">التقدير</div>
                </div>
                <div class="flex justify-between items-end">
                    <div class="flex items-baseline gap-1">
                        <span class="text-3xl font-black text-slate-800 student-total">0</span>
                        <span class="text-sm font-bold text-slate-400">/ 100</span>
                    </div>
                    <div class="student-result status-badge status-pending">قيد التقييم</div>
                </div>
            </div>
        </div>
    </template>

    <!-- قالب معيار التقييم -->
    <template id="criteriaTemplate">
        <div class="space-y-2">
            <div class="flex justify-between items-center">
                <div class="text-sm font-bold text-slate-700 criteria-label"></div>
                <div class="text-xs font-bold text-slate-500">الحد: <span class="criteria-max">0</span></div>
            </div>
            <input type="number" min="0" max="100" value="0" class="score-input w-full p-3 rounded-xl border criteria-input">
        </div>
    </template>

    <!-- مدخلات الملفات المخفية -->
    <input type="file" id="backupUpload" class="hidden" accept=".json">
    <input type="file" id="excelImport" class="hidden" accept=".xlsx,.xls,.csv">

    <script>
        // ===== التهيئة الأولية =====
        document.addEventListener('DOMContentLoaded', function() {
            // تحديث التاريخ الحالي
            const now = new Date();
            const dateStr = now.toLocaleDateString('ar-SA', {
                year: 'numeric',
                month: 'long',
                day: 'numeric'
            });
            document.getElementById('currentDate').textContent = dateStr;
            
            // تحميل الشعار
            loadLogo();
            
            // تحميل الإحصائيات
            updateAllStats();
            
            // إعداد تاريخ افتراضي في حقول التاريخ
            const today = now.toISOString().split('T')[0];
            document.querySelectorAll('input[type="date"]').forEach(input => {
                if (!input.value) input.value = today;
            });
            
            // تحديث القوائم عند التحميل
            updateProjectSelects();
        });

        // ===== قاعدة البيانات =====
        let db = JSON.parse(localStorage.getItem('graduation_system_db')) || {
            projects: [
                {
                    id: 1,
                    title: "نظام إدارة المستودعات الذكي",
                    supervisor: "د. محمد أحمد حسن",
                    year: "2024",
                    status: "active",
                    createdAt: new Date().toISOString()
                },
                {
                    id: 2,
                    title: "تطبيق التجارة الإلكترونية المتكامل",
                    supervisor: "د. سارة محمود خالد",
                    year: "2024",
                    status: "active",
                    createdAt: new Date().toISOString()
                }
            ],
            students: [
                {
                    id: 1,
                    name: "أحمد محمد علي",
                    studentId: "202410001",
                    projectId: 1,
                    createdAt: new Date().toISOString()
                },
                {
                    id: 2,
                    name: "سارة محمود حسن",
                    studentId: "202410002",
                    projectId: 1,
                    createdAt: new Date().toISOString()
                },
                {
                    id: 3,
                    name: "خالد عبد الله",
                    studentId: "202410003",
                    projectId: 2,
                    createdAt: new Date().toISOString()
                }
            ],
            evaluations: [],
            supervisors: ["د. محمد أحمد حسن", "د. سارة محمود خالد", "د. أحمد علي محمود"]
        };

        // ===== إدارة الشعار =====
        let logoData = JSON.parse(localStorage.getItem('graduation_system_logo')) || null;

        // دالة تحميل الشعار
        function loadLogo() {
            const logoDisplay = document.getElementById('logoDisplay');
            const currentLogoPreview = document.getElementById('currentLogoPreview');
            
            if (logoData && logoData.dataUrl) {
                // عرض الشعار في الصفحة الرئيسية
                logoDisplay.innerHTML = `
                    <img src="${logoData.dataUrl}" 
                         class="logo-img" 
                         alt="${logoData.name || 'شعار المؤسسة'}"
                         style="max-width: ${logoData.width || '200px'};">
                `;
                
                // عرض الشعار في معاينة المسؤول
                if (currentLogoPreview) {
                    currentLogoPreview.innerHTML = `
                        <img src="${logoData.dataUrl}" 
                             class="logo-img" 
                             alt="${logoData.name || 'شعار المؤسسة'}"
                             style="max-width: ${logoData.width || '200px'};">
                    `;
                    
                    // تحديث معلومات الشعار
                    document.getElementById('logoInfo').textContent = 
                        `شعار ${logoData.name || 'المؤسسة'}`;
                    
                    // تعيين القيم في النموذج
                    if (logoData.name && document.getElementById('logoName')) {
                        document.getElementById('logoName').value = logoData.name;
                    }
                    if (logoData.width && document.getElementById('logoWidth')) {
                        document.getElementById('logoWidth').value = logoData.width;
                    }
                    if (logoData.size && document.getElementById('logoSize')) {
                        document.getElementById('logoSize').value = logoData.size;
                    }
                }
            } else {
                // عرض الشعار الافتراضي
                logoDisplay.innerHTML = `
                    <div class="text-center">
                        <div class="w-20 h-20 gradient-primary rounded-2xl flex items-center justify-center mx-auto mb-4">
                            <i class="fas fa-graduation-cap text-3xl text-white"></i>
                        </div>
                        <div class="text-slate-500 text-sm">شعار المؤسسة</div>
                    </div>
                `;
                
                if (currentLogoPreview) {
                    currentLogoPreview.innerHTML = `
                        <div class="text-center py-6">
                            <i class="fas fa-image text-4xl text-slate-300 mb-3"></i>
                            <div class="text-slate-500">لا يوجد شعار</div>
                        </div>
                    `;
                }
            }
        }

        // دالة معاينة الشعار قبل الرفع
        function previewLogo(event) {
            const file = event.target.files[0];
            if (!file) return;
            
            // التحقق من نوع الملف
            if (!file.type.match('image.*')) {
                alert('الرجاء اختيار ملف صورة فقط');
                return;
            }
            
            // التحقق من حجم الملف (5MB كحد أقصى)
            if (file.size > 5 * 1024 * 1024) {
                alert('حجم الملف كبير جداً. الحد الأقصى هو 5 ميجابايت');
                return;
            }
            
            const reader = new FileReader();
            reader.onload = function(e) {
                // إظهار معاينة الشعار الجديد
                const previewContainer = document.getElementById('newLogoPreview');
                const previewImage = document.getElementById('previewImage');
                
                previewImage.src = e.target.result;
                previewContainer.classList.remove('hidden');
                
                // تعيين اسم الملف الافتراضي
                if (document.getElementById('logoName') && !document.getElementById('logoName').value) {
                    const fileName = file.name.replace(/\.[^/.]+$/, ""); // إزالة الامتداد
                    document.getElementById('logoName').value = fileName;
                }
            };
            
            reader.readAsDataURL(file);
        }

        // دالة حفظ الشعار
        function saveLogo() {
            const fileInput = document.getElementById('logoUpload');
            const logoName = document.getElementById('logoName')?.value || '';
            const logoWidth = document.getElementById('logoWidth')?.value || 'auto';
            const logoSize = document.getElementById('logoSize')?.value || 'medium';
            
            if (!fileInput.files[0]) {
                alert('الرجاء اختيار صورة الشعار أولاً');
                return;
            }
            
            const file = fileInput.files[0];
            const reader = new FileReader();
            
            reader.onload = function(e) {
                // حفظ بيانات الشعار
                logoData = {
                    dataUrl: e.target.result,
                    name: logoName || file.name,
                    width: logoWidth,
                    size: logoSize,
                    uploadDate: new Date().toISOString(),
                    fileType: file.type,
                    fileSize: file.size
                };
                
                // حفظ في localStorage
                localStorage.setItem('graduation_system_logo', JSON.stringify(logoData));
                
                // تحديث العرض
                loadLogo();
                
                // إخفاء المعاينة
                const newLogoPreview = document.getElementById('newLogoPreview');
                if (newLogoPreview) {
                    newLogoPreview.classList.add('hidden');
                }
                
                // إعادة تعيين حقل الرفع
                fileInput.value = '';
                
                // عرض رسالة نجاح
                showToast('تم حفظ الشعار بنجاح', 'success');
            };
            
            reader.readAsDataURL(file);
        }

        // دالة حذف الشعار
        function removeLogo() {
            if (!confirm('هل أنت متأكد من حذف الشعار الحالي؟')) {
                return;
            }
            
            // حذف بيانات الشعار
            logoData = null;
            localStorage.removeItem('graduation_system_logo');
            
            // تحديث العرض
            loadLogo();
            
            // إعادة تعيين النموذج
            if (document.getElementById('logoName')) {
                document.getElementById('logoName').value = '';
            }
            if (document.getElementById('logoWidth')) {
                document.getElementById('logoWidth').value = 'auto';
            }
            if (document.getElementById('logoSize')) {
                document.getElementById('logoSize').value = 'medium';
            }
            if (document.getElementById('logoUpload')) {
                document.getElementById('logoUpload').value = '';
            }
            const newLogoPreview = document.getElementById('newLogoPreview');
            if (newLogoPreview) {
                newLogoPreview.classList.add('hidden');
            }
            
            showToast('تم حذف الشعار بنجاح', 'info');
        }

        // ===== إعدادات النظام =====
        let currentRole = '';
        let isSyncing = false;
        let autoSaveTimer = null;

        const config = {
            supervisor: {
                title: "تقييم المشرف الأكاديمي",
                subtitle: "تقييم أداء الطلاب خلال مرحلة الإشراف على المشروع",
                color: "gradient-success",
                criteria: [
                    { id: 'research', label: 'جودة البحث العلمي', max: 25, description: 'الأصالة، المنهجية، المصادر' },
                    { id: 'implementation', label: 'التنفيذ العملي', max: 35, description: 'التطبيق، الكود، الوظائف' },
                    { id: 'documentation', label: 'التوثيق والتقارير', max: 20, description: 'التقارير، الملفات، التوثيق' },
                    { id: 'commitment', label: 'الالتزام والمتابعة', max: 20, description: 'الحضور، الاجتماعات، التقدم' }
                ],
                fields: [
                    { id: 'supervisorName', label: 'اسم المشرف', type: 'text', icon: 'user-tie', required: true },
                    { id: 'academicYear', label: 'العام الأكاديمي', type: 'text', icon: 'calendar', required: true },
                    { id: 'department', label: 'القسم / التخصص', type: 'text', icon: 'building', required: true }
                ]
            },
            examiner: {
                title: "تقييم لجنة المناقشة",
                subtitle: "التقييم النهائي للمشروع خلال جلسة المناقشة",
                color: "gradient-warning",
                criteria: [
                    { id: 'report', label: 'جودة التقرير النهائي', max: 25, description: 'التنظيم، الاكتمال، الشكل' },
                    { id: 'presentation', label: 'عرض المشروع', max: 25, description: 'العرض، الشرح، الوسائل' },
                    { id: 'technical', label: 'الجانب التقني', max: 25, description: 'الحلول، الابتكار، الجودة' },
                    { id: 'defense', label: 'المناقشة والرد', max: 25, description: 'الإجابات، التفاعل، الفهم' }
                ],
                fields: [
                    { id: 'examinerName', label: 'اسم المناقش', type: 'text', icon: 'user-graduate', required: true },
                    { id: 'examinationDate', label: 'تاريخ المناقشة', type: 'date', icon: 'calendar-day', required: true },
                    { id: 'committee', label: 'اسم اللجنة', type: 'text', icon: 'users', required: true }
                ]
            }
        };

        // ===== وظائف الواجهة الرئيسية =====
        function requestAdminAccess() {
            const password = prompt("🔐 الرجاء إدخال كلمة مرور المسؤول:");
            if (password === "admin123") {
                showSection('admin');
                updateAdminStats();
                renderAdminProjectsList();
                renderAdminStudentsList();
                updateProjectSelects();
            } else if (password !== null) {
                alert("❌ كلمة المرور غير صحيحة!");
            }
        }

        function setRole(role) {
            currentRole = role;
            const cfg = config[role];
            
            // تحديث الواجهة
            document.getElementById('roleSelection').classList.add('hidden');
            document.getElementById('mainContainer').classList.remove('hidden');
            document.getElementById('headerTitle').textContent = cfg.title;
            document.getElementById('headerSubtitle').textContent = cfg.subtitle;
            
            // تحديث قائمة المشاريع
            updateProjectSelect();
            
            // إعداد الحقول الديناميكية
            setupDynamicFields(cfg);
            
            // إظهار/إخفاء قسم الدمج (للمشرف فقط)
            const syncSection = document.getElementById('syncSection');
            syncSection.classList.toggle('hidden', role !== 'supervisor');
            
            // إعداد واجهة الطلاب
            setupStudentsPlaceholder();
        }

        function goBack() {
            // إخفاء جميع الأقسام
            document.getElementById('adminPanel').classList.add('hidden');
            document.getElementById('mainContainer').classList.add('hidden');
            
            // إظهار الشاشة الرئيسية
            document.getElementById('roleSelection').classList.remove('hidden');
            
            // تحديث الإحصائيات
            updateAllStats();
        }

        function showSection(section) {
            document.getElementById('roleSelection').classList.add('hidden');
            document.getElementById('adminPanel').classList.add('hidden');
            document.getElementById('mainContainer').classList.add('hidden');
            
            if (section === 'admin') {
                document.getElementById('adminPanel').classList.remove('hidden');
                updateAdminStats();
                renderAdminProjectsList();
                renderAdminStudentsList();
                updateProjectSelects();
                loadLogo(); // تحميل الشعار في لوحة الإدارة
            }
        }

        // ===== وظائف لوحة الإدارة =====
        function updateAdminStats() {
            // تحديث العدادات
            document.getElementById('adminProjectsCount').textContent = db.projects.length;
            document.getElementById('adminStudentsCount').textContent = db.students.length;
            document.getElementById('adminSupervisorsCount').textContent = db.supervisors.length;
            document.getElementById('adminEvaluationsCount').textContent = db.evaluations.length;
            document.getElementById('projectsListCount').textContent = db.projects.length;
            document.getElementById('studentsListCount').textContent = db.students.length;
        }

        function renderAdminProjectsList() {
            const container = document.getElementById('adminProjectsList');
            if (!container) return;
            
            container.innerHTML = db.projects.map(project => `
                <div class="flex justify-between items-center p-4 bg-slate-50 rounded-xl border border-slate-200 hover:border-indigo-300 transition-colors">
                    <div class="flex-1">
                        <div class="font-bold text-slate-800">${project.title}</div>
                        <div class="text-sm text-slate-600 mt-1">
                            <i class="fas fa-user-tie mr-1"></i> ${project.supervisor}
                            <span class="mx-2">•</span>
                            <i class="fas fa-calendar mr-1"></i> ${project.year}
                        </div>
                    </div>
                    <button onclick="deleteProject(${project.id})" class="text-rose-500 hover:text-rose-700 p-2 hover:bg-rose-50 rounded-lg transition-colors">
                        <i class="fas fa-trash-alt"></i>
                    </button>
                </div>
            `).join('');
            
            // إذا لم توجد مشاريع
            if (db.projects.length === 0) {
                container.innerHTML = `
                    <div class="text-center py-8 opacity-50">
                        <i class="fas fa-project-diagram text-4xl text-slate-300 mb-3"></i>
                        <div class="text-slate-500">لا توجد مشاريع مسجلة</div>
                    </div>
                `;
            }
        }

        function renderAdminStudentsList() {
            const container = document.getElementById('adminStudentsList');
            if (!container) return;
            
            container.innerHTML = db.students.map(student => {
                const project = db.projects.find(p => p.id === student.projectId);
                return `
                    <div class="flex justify-between items-center p-4 bg-slate-50 rounded-xl border border-slate-200 hover:border-emerald-300 transition-colors">
                        <div class="flex-1">
                            <div class="font-bold text-slate-800">${student.name}</div>
                            <div class="text-sm text-slate-600 mt-1">
                                <i class="fas fa-id-card mr-1"></i> ${student.studentId || 'بدون رقم جامعي'}
                                ${project ? `<span class="mx-2">•</span><i class="fas fa-project-diagram mr-1"></i> ${project.title}` : ''}
                            </div>
                        </div>
                        <button onclick="deleteStudent(${student.id})" class="text-rose-500 hover:text-rose-700 p-2 hover:bg-rose-50 rounded-lg transition-colors">
                            <i class="fas fa-trash-alt"></i>
                        </button>
                    </div>
                `;
            }).join('');
            
            // إذا لم توجد طلاب
            if (db.students.length === 0) {
                container.innerHTML = `
                    <div class="text-center py-8 opacity-50">
                        <i class="fas fa-user-graduate text-4xl text-slate-300 mb-3"></i>
                        <div class="text-slate-500">لا توجد طلاب مسجلين</div>
                    </div>
                `;
            }
        }

        function addNewProject() {
            const title = document.getElementById('newProjectTitle').value.trim();
            const supervisor = document.getElementById('newProjectSupervisor').value.trim();
            const year = document.getElementById('newProjectYear').value.trim();
            const status = document.getElementById('newProjectStatus').value;
            
            if (!title) {
                alert("❌ الرجاء إدخال اسم المشروع");
                return;
            }
            
            // إنشاء مشروع جديد
            const newProject = {
                id: Date.now(),
                title: title,
                supervisor: supervisor || "غير محدد",
                year: year || "2024",
                status: status,
                createdAt: new Date().toISOString()
            };
            
            // إضافة المشروع
            db.projects.push(newProject);
            
            // إضافة المشرف إذا كان جديداً
            if (supervisor && !db.supervisors.includes(supervisor)) {
                db.supervisors.push(supervisor);
            }
            
            // حفظ البيانات
            saveDatabase();
            
            // تحديث الواجهة
            renderAdminProjectsList();
            updateAdminStats();
            updateProjectSelects();
            
            // إعادة تعيين الحقول
            document.getElementById('newProjectTitle').value = '';
            document.getElementById('newProjectSupervisor').value = '';
            
            // رسالة نجاح
            showToast("تم إضافة المشروع بنجاح", "success");
        }

        function addNewStudent() {
            const name = document.getElementById('newStudentName').value.trim();
            const projectId = parseInt(document.getElementById('newStudentProject').value);
            const studentId = document.getElementById('newStudentId').value.trim();
            
            if (!name) {
                alert("❌ الرجاء إدخال اسم الطالب");
                return;
            }
            
            // إنشاء طالب جديد
            const newStudent = {
                id: Date.now(),
                name: name,
                studentId: studentId || '',
                projectId: projectId || null,
                createdAt: new Date().toISOString()
            };
            
            // إضافة الطالب
            db.students.push(newStudent);
            
            // حفظ البيانات
            saveDatabase();
            
            // تحديث الواجهة
            renderAdminStudentsList();
            updateAdminStats();
            
            // إعادة تعيين الحقول
            document.getElementById('newStudentName').value = '';
            document.getElementById('newStudentId').value = '';
            
            // رسالة نجاح
            showToast("تم إضافة الطالب بنجاح", "success");
        }

        function deleteProject(projectId) {
            if (!confirm("هل أنت متأكد من حذف هذا المشروع؟ سيتم حذف جميع الطلاب المرتبطين به.")) {
                return;
            }
            
            // حذف المشروع
            db.projects = db.projects.filter(p => p.id !== projectId);
            
            // حذف الطلاب المرتبطين
            db.students = db.students.filter(s => s.projectId !== projectId);
            
            // حفظ البيانات
            saveDatabase();
            
            // تحديث الواجهة
            renderAdminProjectsList();
            renderAdminStudentsList();
            updateAdminStats();
            updateProjectSelects();
            
            showToast("تم حذف المشروع بنجاح", "info");
        }

        function deleteStudent(studentId) {
            if (!confirm("هل أنت متأكد من حذف هذا الطالب؟")) {
                return;
            }
            
            // حذف الطالب
            db.students = db.students.filter(s => s.id !== studentId);
            
            // حفظ البيانات
            saveDatabase();
            
            // تحديث الواجهة
            renderAdminStudentsList();
            updateAdminStats();
            
            showToast("تم حذف الطالب بنجاح", "info");
        }

        function updateProjectSelects() {
            // تحديث قائمة المشاريع في نموذج إضافة الطالب
            const studentProjectSelect = document.getElementById('newStudentProject');
            if (studentProjectSelect) {
                studentProjectSelect.innerHTML = '<option value="">اختر المشروع</option>' +
                    db.projects.map(p => `<option value="${p.id}">${p.title}</option>`).join('');
            }
        }

        // ===== استيراد البيانات من Excel =====
        function importExcelFile(event) {
            const file = event.target.files[0];
            if (!file) return;
            
            const reader = new FileReader();
            reader.onload = function(e) {
                try {
                    const data = new Uint8Array(e.target.result);
                    const workbook = XLSX.read(data, { type: 'array' });
                    const firstSheet = workbook.Sheets[workbook.SheetNames[0]];
                    const json = XLSX.utils.sheet_to_json(firstSheet);
                    
                    let importedCount = 0;
                    
                    // معالجة كل صف
                    json.forEach(row => {
                        const studentName = row['اسم الطالب'] || row['student'] || row['Student'] || row['الطالب'];
                        const projectName = row['اسم المشروع'] || row['project'] || row['Project'] || row['المشروع'];
                        const supervisorName = row['اسم المشرف'] || row['supervisor'] || row['Supervisor'] || row['المشرف'];
                        const studentId = row['الرقم الجامعي'] || row['id'] || row['ID'] || row['رقم الطالب'];
                        
                        if (projectName && studentName) {
                            // البحث عن المشروع أو إنشاؤه
                            let project = db.projects.find(p => p.title === projectName);
                            if (!project) {
                                project = {
                                    id: Date.now() + Math.random(),
                                    title: projectName,
                                    supervisor: supervisorName || "غير محدد",
                                    year: "2024",
                                    status: "active",
                                    createdAt: new Date().toISOString()
                                };
                                db.projects.push(project);
                                importedCount++;
                                
                                // إضافة المشرف إذا كان جديداً
                                if (supervisorName && !db.supervisors.includes(supervisorName)) {
                                    db.supervisors.push(supervisorName);
                                }
                            }
                            
                            // إضافة الطالب إذا لم يكن موجوداً
                            const existingStudent = db.students.find(s => 
                                s.name === studentName && s.projectId === project.id
                            );
                            
                            if (!existingStudent) {
                                db.students.push({
                                    id: Date.now() + Math.random(),
                                    name: studentName,
                                    studentId: studentId || '',
                                    projectId: project.id,
                                    createdAt: new Date().toISOString()
                                });
                                importedCount++;
                            }
                        }
                    });
                    
                    // حفظ البيانات
                    saveDatabase();
                    
                    // تحديث الواجهة
                    setTimeout(() => {
                        renderAdminProjectsList();
                        renderAdminStudentsList();
                        updateAdminStats();
                        updateProjectSelects();
                        
                        showToast(`تم استيراد ${importedCount} عنصر بنجاح`, "success");
                    }, 500);
                    
                } catch (error) {
                    showToast("حدث خطأ في معالجة الملف", "error");
                }
            };
            
            reader.readAsArrayBuffer(file);
            
            // إعادة تعيين المدخل
            event.target.value = '';
        }

        function downloadExcelTemplate() {
            const templateData = [
                ["اسم الطالب", "اسم المشروع", "اسم المشرف", "الرقم الجامعي"],
                ["أحمد محمد", "نظام إدارة المستودعات", "د. محمد حسن", "202410001"],
                ["سارة خالد", "نظام إدارة المستودعات", "د. محمد حسن", "202410002"],
                ["علي محمود", "تطبيق التجارة الإلكترونية", "د. سارة أحمد", "202410003"]
            ];
            
            const worksheet = XLSX.utils.aoa_to_sheet(templateData);
            const workbook = XLSX.utils.book_new();
            XLSX.utils.book_append_sheet(workbook, worksheet, "نموذج البيانات");
            XLSX.writeFile(workbook, "نموذج_استيراد_الطلاب.xlsx");
        }

        // ===== وظائف النسخ الاحتياطي =====
        function exportFullDatabase() {
            const dataStr = JSON.stringify({db, logoData}, null, 2);
            const dataUri = 'data:application/json;charset=utf-8,' + encodeURIComponent(dataStr);
            const fileName = `نسخة_احتياطية_${new Date().toISOString().split('T')[0]}.json`;
            
            const link = document.createElement('a');
            link.href = dataUri;
            link.download = fileName;
            link.click();
            
            showToast("تم تصدير النسخة الاحتياطية", "success");
        }

        function importBackup() {
            document.getElementById('backupUpload').click();
        }

        document.getElementById('backupUpload').addEventListener('change', function(e) {
            const file = e.target.files[0];
            if (!file) return;
            
            const reader = new FileReader();
            reader.onload = function(event) {
                try {
                    const importedData = JSON.parse(event.target.result);
                    
                    if (confirm("سيتم استبدال جميع البيانات الحالية. هل تريد المتابعة؟")) {
                        db = importedData.db || importedData;
                        logoData = importedData.logoData || null;
                        
                        // حفظ البيانات
                        localStorage.setItem('graduation_system_db', JSON.stringify(db));
                        if (logoData) {
                            localStorage.setItem('graduation_system_logo', JSON.stringify(logoData));
                        }
                        
                        // تحديث جميع الواجهات
                        renderAdminProjectsList();
                        renderAdminStudentsList();
                        updateAdminStats();
                        updateProjectSelects();
                        updateAllStats();
                        loadLogo();
                        
                        showToast("تم استيراد النسخة الاحتياطية بنجاح", "success");
                    }
                } catch (error) {
                    showToast("خطأ في ملف البيانات", "error");
                }
            };
            reader.readAsText(file);
            
            // إعادة تعيين المدخل
            e.target.value = '';
        });

        // ===== وظائف نموذج التقييم =====
        function setupDynamicFields(cfg) {
            const container = document.getElementById('dynamicFields');
            if (!container) return;
            
            const today = new Date().toISOString().split('T')[0];
            
            container.innerHTML = cfg.fields.map(field => `
                <div class="space-y-2">
                    <label class="block text-sm font-bold text-slate-700">
                        <i class="fas fa-${field.icon} mr-2 text-indigo-500"></i>
                        ${field.label}
                    </label>
                    <input type="${field.type}" 
                           id="${field.id}" 
                           class="w-full p-3 border-2 border-slate-200 rounded-xl font-medium text-slate-800 bg-white outline-none focus:border-indigo-500 transition-all"
                           ${field.required ? 'required' : ''}
                           ${field.type === 'date' ? `value="${today}"` : ''}
                           oninput="autoSaveEvaluation()">
                </div>
            `).join('');
        }

        function updateProjectSelect() {
            const select = document.getElementById('projectSelect');
            if (!select) return;
            
            select.innerHTML = '<option value="">-- اختر المشروع --</option>' +
                db.projects.map(project => `
                    <option value="${project.id}">${project.title}</option>
                `).join('');
        }

        function loadProjectData() {
            const projectId = parseInt(document.getElementById('projectSelect').value);
            if (!projectId) {
                setupStudentsPlaceholder();
                return;
            }
            
            const project = db.projects.find(p => p.id === projectId);
            if (!project) return;
            
            // تحديث حقل المشرف إذا كان موجوداً
            const supervisorField = document.getElementById('supervisorName');
            if (supervisorField) {
                supervisorField.value = project.supervisor;
            }
            
            // تحميل الطلاب
            loadProjectStudents(projectId);
            
            // تحديث عدد الطلاب
            const studentsCount = db.students.filter(s => s.projectId === projectId).length;
            document.getElementById('studentsCount').textContent = studentsCount;
        }

        function loadProjectStudents(projectId) {
            const students = db.students.filter(s => s.projectId === projectId);
            const container = document.getElementById('studentsWrapper');
            const cfg = config[currentRole];
            
            container.innerHTML = '';
            
            if (students.length === 0) {
                container.innerHTML = `
                    <div class="col-span-full text-center py-16 opacity-40">
                        <div class="text-6xl mb-6">👥</div>
                        <h4 class="text-2xl font-black text-slate-600 mb-3">لا توجد طلاب في هذا المشروع</h4>
                        <p class="text-slate-500">يمكنك إضافة طلاب عبر لوحة الإدارة</p>
                    </div>
                `;
                return;
            }
            
            students.forEach(student => {
                const template = document.getElementById('studentTemplate').content.cloneNode(true);
                const card = template.querySelector('.card');
                
                // تعيين بيانات الطالب
                card.querySelector('.student-name').textContent = student.name;
                if (student.studentId) {
                    card.querySelector('.student-id').textContent = `#${student.studentId}`;
                }
                
                // إضافة معايير التقييم
                const criteriaList = card.querySelector('.criteria-list');
                cfg.criteria.forEach(criteria => {
                    const criteriaTemplate = document.getElementById('criteriaTemplate').content.cloneNode(true);
                    const criteriaItem = criteriaTemplate.querySelector('.space-y-2');
                    
                    criteriaItem.querySelector('.criteria-label').textContent = criteria.label;
                    criteriaItem.querySelector('.criteria-max').textContent = criteria.max;
                    
                    const input = criteriaItem.querySelector('.criteria-input');
                    input.max = criteria.max;
                    input.dataset.criteria = criteria.id;
                    
                    // إضافة حدث التغيير
                    input.addEventListener('input', function() {
                        updateStudentScore(this, card);
                        autoSaveEvaluation();
                    });
                    
                    criteriaList.appendChild(criteriaItem);
                });
                
                container.appendChild(template);
            });
        }

        function setupStudentsPlaceholder() {
            const container = document.getElementById('studentsWrapper');
            container.innerHTML = `
                <div class="col-span-full text-center py-16 opacity-40">
                    <div class="text-6xl mb-6">🔍</div>
                    <h4 class="text-2xl font-black text-slate-600 mb-3">لم يتم اختيار مشروع</h4>
                    <p class="text-slate-500">يرجى اختيار مشروع من القائمة أعلاه لبدء عملية التقييم</p>
                </div>
            `;
        }

        function updateStudentScore(input, card) {
            // التحقق من القيمة
            let value = parseInt(input.value) || 0;
            const max = parseInt(input.max);
            
            if (value > max) value = max;
            if (value < 0) value = 0;
            
            input.value = value;
            
            // تحديث مظهر المدخل حسب النسبة
            const percentage = (value / max) * 100;
            input.className = 'score-input w-full p-3 rounded-xl border criteria-input';
            
            if (percentage >= 80) input.classList.add('good');
            else if (percentage >= 60) input.classList.add('average');
            else input.classList.add('poor');
            
            // حساب المجموع
            let total = 0;
            card.querySelectorAll('.criteria-input').forEach(input => {
                total += parseInt(input.value) || 0;
            });
            
            // تحديث المجموع
            card.querySelector('.student-total').textContent = total;
            
            // تحديث التقدير
            updateStudentResult(total, card);
            
            // تطبيق الدمج إذا كان مفعلاً
            if (isSyncing && currentRole === 'supervisor') {
                applySyncToAll(input.dataset.criteria, value);
            }
        }

        function updateStudentResult(total, card) {
            const resultElement = card.querySelector('.student-result');
            resultElement.className = 'student-result status-badge ';
            
            if (total >= 90) {
                resultElement.textContent = 'ممتاز';
                resultElement.className += 'status-excellent';
            } else if (total >= 80) {
                resultElement.textContent = 'جيد جداً';
                resultElement.className += 'status-good';
            } else if (total >= 70) {
                resultElement.textContent = 'جيد';
                resultElement.className += 'status-average';
            } else if (total >= 60) {
                resultElement.textContent = 'مقبول';
                resultElement.className += 'status-average';
            } else if (total >= 50) {
                resultElement.textContent = 'ناجح';
                resultElement.className += 'status-good';
            } else {
                resultElement.textContent = 'راسب';
                resultElement.className += 'status-fail';
            }
        }

        function toggleSync() {
            isSyncing = !isSyncing;
            const button = document.getElementById('syncToggle');
            
            if (isSyncing) {
                button.innerHTML = '<i class="fas fa-unlink mr-2"></i>إيقاف الدمج';
                button.className = "bg-gradient-to-r from-rose-500 to-rose-600 text-white px-5 py-2.5 rounded-xl font-bold hover:shadow-lg";
            } else {
                button.innerHTML = '<i class="fas fa-link mr-2"></i>تفعيل الدمج';
                button.className = "bg-gradient-to-r from-amber-500 to-amber-600 text-white px-5 py-2.5 rounded-xl font-bold hover:shadow-lg";
            }
        }

        function applySyncToAll(criteriaId, value) {
            document.querySelectorAll(`.criteria-input[data-criteria="${criteriaId}"]`).forEach(input => {
                if (input.value !== value.toString()) {
                    input.value = value;
                    updateStudentScore(input, input.closest('.card'));
                }
            });
        }

        // ===== الحفظ التلقائي =====
        function autoSaveEvaluation() {
            clearTimeout(autoSaveTimer);
            autoSaveTimer = setTimeout(saveAllEvaluations, 2000);
        }

        function saveAllEvaluations() {
            const projectId = parseInt(document.getElementById('projectSelect').value);
            if (!projectId || !currentRole) return;
            
            const project = db.projects.find(p => p.id === projectId);
            if (!project) return;
            
            // جمع بيانات التقييم
            const evaluation = {
                id: Date.now(),
                projectId: projectId,
                projectTitle: project.title,
                role: currentRole,
                date: new Date().toISOString(),
                data: {},
                students: []
            };
            
            // جمع بيانات الحقول
            config[currentRole].fields.forEach(field => {
                const input = document.getElementById(field.id);
                if (input) {
                    evaluation.data[field.id] = input.value;
                }
            });
            
            // جمع بيانات الطلاب
            document.querySelectorAll('.student-card').forEach(card => {
                const studentName = card.querySelector('.student-name').textContent;
                const totalScore = parseInt(card.querySelector('.student-total').textContent) || 0;
                const result = card.querySelector('.student-result').textContent;
                
                const studentData = {
                    name: studentName,
                    totalScore: totalScore,
                    result: result,
                    criteria: {}
                };
                
                // جمع درجات المعايير
                card.querySelectorAll('.criteria-input').forEach(input => {
                    const criteriaId = input.dataset.criteria;
                    studentData.criteria[criteriaId] = parseInt(input.value) || 0;
                });
                
                evaluation.students.push(studentData);
            });
            
            // البحث عن تقييم موجود أو إضافة جديد
            const existingIndex = db.evaluations.findIndex(e => 
                e.projectId === projectId && e.role === currentRole
            );
            
            if (existingIndex >= 0) {
                db.evaluations[existingIndex] = evaluation;
            } else {
                db.evaluations.push(evaluation);
            }
            
            // حفظ البيانات
            saveDatabase();
            
            // إظهار إشعار الحفظ
            showSaveNotification();
            
            // تحديث الإحصائيات
            updateAllStats();
        }

        function showSaveNotification() {
            const notification = document.getElementById('saveNotification');
            notification.classList.remove('hidden');
            
            setTimeout(() => {
                notification.classList.add('hidden');
            }, 3000);
        }

        // ===== عرض النتائج =====
        function showResultsSummary() {
            const projectId = parseInt(document.getElementById('projectSelect').value);
            if (!projectId) {
                alert("يرجى اختيار مشروع أولاً");
                return;
            }
            
            const container = document.getElementById('resultsSummary');
            const cardsContainer = document.getElementById('summaryCards');
            
            const students = db.students.filter(s => s.projectId === projectId);
            const evaluation = db.evaluations.find(e => e.projectId === projectId && e.role === currentRole);
            
            if (!evaluation || !evaluation.students || evaluation.students.length === 0) {
                alert("لا توجد بيانات تقييم لعرض الملخص");
                return;
            }
            
            cardsContainer.innerHTML = evaluation.students.map((student, index) => {
                const percentage = (student.totalScore / 100) * 100;
                let statusColor = '';
                
                if (student.totalScore >= 90) statusColor = 'text-indigo-600';
                else if (student.totalScore >= 80) statusColor = 'text-emerald-600';
                else if (student.totalScore >= 70) statusColor = 'text-amber-600';
                else statusColor = 'text-rose-600';
                
                return `
                    <div class="card p-6">
                        <div class="flex justify-between items-center mb-4">
                            <div class="w-12 h-12 bg-indigo-100 text-indigo-600 rounded-xl flex items-center justify-center">
                                <i class="fas fa-user text-lg"></i>
                            </div>
                            <span class="text-xs font-bold bg-slate-100 text-slate-700 px-3 py-1 rounded-full">
                                الطالب ${index + 1}
                            </span>
                        </div>
                        <h4 class="font-bold text-lg text-slate-800 mb-2 truncate">${student.name}</h4>
                        <div class="space-y-3">
                            <div>
                                <div class="flex justify-between text-sm text-slate-500 mb-1">
                                    <span>الدرجة النهائية</span>
                                    <span>${student.totalScore}/100</span>
                                </div>
                                <div class="w-full bg-slate-100 rounded-full h-2.5">
                                    <div class="bg-indigo-600 h-2.5 rounded-full" style="width: ${percentage}%"></div>
                                </div>
                            </div>
                            <div class="flex justify-between items-center">
                                <span class="text-sm text-slate-500">التقدير</span>
                                <span class="font-bold ${statusColor}">${student.result}</span>
                            </div>
                        </div>
                    </div>
                `;
            }).join('');
            
            container.classList.remove('hidden');
        }

        // ===== الطباعة =====
        function printReport() {
            const projectId = parseInt(document.getElementById('projectSelect').value);
            if (!projectId) {
                alert("يرجى اختيار مشروع أولاً");
                return;
            }
            
            const project = db.projects.find(p => p.id === projectId);
            const evaluation = db.evaluations.find(e => e.projectId === projectId && e.role === currentRole);
            
            if (!evaluation || !evaluation.students || evaluation.students.length === 0) {
                alert("لا توجد بيانات تقييم للطباعة");
                return;
            }
            
            // إنشاء نافذة طباعة
            const printWindow = window.open('', '_blank');
            
            // بناء محتوى الطباعة
            let printContent = `
                <!DOCTYPE html>
                <html dir="rtl" lang="ar">
                <head>
                    <meta charset="UTF-8">
                    <meta name="viewport" content="width=device-width, initial-scale=1.0">
                    <title>تقرير تقييم مشاريع التخرج</title>
                    <style>
                        body { 
                            font-family: 'Tajawal', sans-serif; 
                            padding: 20px;
                            background: white;
                            color: #333;
                        }
                        .header { 
                            text-align: center; 
                            margin-bottom: 30px;
                            padding-bottom: 20px;
                            border-bottom: 2px solid #e2e8f0;
                        }
                        .logo-container {
                            max-width: 200px;
                            margin: 0 auto 20px;
                        }
                        .logo-container img {
                            max-width: 100%;
                            height: auto;
                        }
                        .title { 
                            font-size: 24px; 
                            font-weight: 900; 
                            margin-bottom: 10px;
                            color: #1e293b;
                        }
                        .subtitle { 
                            color: #64748b; 
                            margin-bottom: 20px;
                            font-size: 16px;
                        }
                        .info-table {
                            width: 100%;
                            margin: 20px 0;
                            border-collapse: collapse;
                        }
                        .info-table td {
                            padding: 8px;
                            border-bottom: 1px solid #e2e8f0;
                        }
                        .info-table td:first-child {
                            font-weight: bold;
                            width: 30%;
                        }
                        .students-table {
                            width: 100%;
                            margin: 30px 0;
                            border-collapse: collapse;
                            border: 1px solid #cbd5e1;
                        }
                        .students-table th, .students-table td {
                            padding: 12px;
                            text-align: center;
                            border: 1px solid #cbd5e1;
                        }
                        .students-table th {
                            background-color: #f1f5f9;
                            font-weight: bold;
                            color: #334155;
                        }
                        .students-table tr:nth-child(even) {
                            background-color: #f8fafc;
                        }
                        .footer { 
                            margin-top: 40px; 
                            text-align: center; 
                            color: #64748b; 
                            font-size: 12px;
                            padding-top: 20px;
                            border-top: 1px solid #e2e8f0;
                        }
                        .grade-excellent { color: #1e40af; font-weight: bold; }
                        .grade-good { color: #065f46; font-weight: bold; }
                        .grade-average { color: #92400e; font-weight: bold; }
                        .grade-fail { color: #991b1b; font-weight: bold; }
                        @page { margin: 20mm; }
                        @media print {
                            body { padding: 0; }
                            .header { margin-top: 0; }
                        }
                    </style>
                </head>
                <body>
                    <div class="header">
            `;
            
            // إضافة الشعار إذا كان موجوداً
            if (logoData && logoData.dataUrl) {
                printContent += `
                    <div class="logo-container">
                        <img src="${logoData.dataUrl}" alt="${logoData.name || 'شعار المؤسسة'}" style="max-width: ${logoData.width || '200px'};">
                    </div>
                `;
            }
            
            printContent += `
                        <div class="title">تقرير تقييم مشاريع التخرج</div>
                        <div class="subtitle">إعداد: المهندس أنس</div>
                    </div>
                    
                    <table class="info-table">
                        <tr>
                            <td>اسم المشروع:</td>
                            <td>${project.title}</td>
                        </tr>
                        <tr>
                            <td>نوع التقييم:</td>
                            <td>${currentRole === 'supervisor' ? 'تقييم المشرف' : 'تقييم المناقشة'}</td>
                        </tr>
                        <tr>
                            <td>اسم المشرف:</td>
                            <td>${project.supervisor}</td>
                        </tr>
                        <tr>
                            <td>تاريخ التقييم:</td>
                            <td>${new Date(evaluation.date).toLocaleDateString('ar-SA')}</td>
                        </tr>
                    </table>
                    
                    <h3 style="margin-top: 30px; color: #334155;">نتائج الطلاب</h3>
                    <table class="students-table">
                        <thead>
                            <tr>
                                <th>م</th>
                                <th>اسم الطالب</th>
                                <th>الدرجة النهائية</th>
                                <th>التقدير</th>
                                <th>النسبة المئوية</th>
                            </tr>
                        </thead>
                        <tbody>
            `;
            
            // إضافة بيانات الطلاب
            evaluation.students.forEach((student, index) => {
                let gradeClass = '';
                if (student.totalScore >= 90) gradeClass = 'grade-excellent';
                else if (student.totalScore >= 80) gradeClass = 'grade-good';
                else if (student.totalScore >= 70) gradeClass = 'grade-average';
                else gradeClass = 'grade-fail';
                
                printContent += `
                    <tr>
                        <td>${index + 1}</td>
                        <td>${student.name}</td>
                        <td>${student.totalScore}/100</td>
                        <td class="${gradeClass}">${student.result}</td>
                        <td>${student.totalScore}%</td>
                    </tr>
                `;
            });
            
            printContent += `
                        </tbody>
                    </table>
                    
                    <div class="footer">
                        <p>تم إنشاء هذا التقرير تلقائياً بواسطة نظام تقييم مشاريع التخرج</p>
                        <p>التاريخ: ${new Date().toLocaleDateString('ar-SA')}</p>
                    </div>
                    
                    <script>
                        window.onload = function() {
                            window.print();
                            setTimeout(function() {
                                window.close();
                            }, 1000);
                        }
                    <\/script>
                </body>
                </html>
            `;
            
            // كتابة المحتوى في نافذة الطباعة
            printWindow.document.write(printContent);
            printWindow.document.close();
        }

        // ===== التصدير والمشاركة =====
        function exportToExcel() {
            const projectId = parseInt(document.getElementById('projectSelect').value);
            if (!projectId) {
                alert("يرجى اختيار مشروع أولاً");
                return;
            }
            
            const project = db.projects.find(p => p.id === projectId);
            const evaluation = db.evaluations.find(e => e.projectId === projectId && e.role === currentRole);
            
            if (!evaluation || !evaluation.students || evaluation.students.length === 0) {
                alert("لا توجد بيانات تقييم للتصدير");
                return;
            }
            
            // إعداد البيانات
            const data = [
                ["تقرير تقييم مشاريع التخرج"],
                ["إعداد: المهندس أنس"],
                [],
                ["تفاصيل المشروع"],
                ["اسم المشروع:", project.title],
                ["نوع التقييم:", currentRole === 'supervisor' ? 'تقييم المشرف' : 'تقييم المناقشة'],
                ["اسم المشرف:", project.supervisor],
                ["تاريخ التقييم:", new Date(evaluation.date).toLocaleDateString('ar-SA')],
                [],
                ["نتائج الطلاب"],
                ["م", "اسم الطالب", "الدرجة النهائية", "التقدير", "النسبة المئوية"]
            ];
            
            evaluation.students.forEach((student, index) => {
                data.push([
                    index + 1,
                    student.name,
                    student.totalScore,
                    student.result,
                    `${student.totalScore}%`
                ]);
            });
            
            data.push([], ["ملاحظات:"], ["تم إنشاء هذا التقرير تلقائياً بواسطة نظام تقييم مشاريع التخرج"]);
            
            // إنشاء ملف Excel
            const worksheet = XLSX.utils.aoa_to_sheet(data);
            const workbook = XLSX.utils.book_new();
            XLSX.utils.book_append_sheet(wb, worksheet, "تقرير التقييم");
            
            // حفظ الملف
            const fileName = `تقرير_${project.title.replace(/\s+/g, '_')}.xlsx`;
            XLSX.writeFile(wb, fileName);
            
            showToast("تم تصدير الملف بنجاح", "success");
        }

        function shareWhatsApp() {
            const projectId = parseInt(document.getElementById('projectSelect').value);
            if (!projectId) {
                alert("يرجى اختيار مشروع أولاً");
                return;
            }
            
            const project = db.projects.find(p => p.id === projectId);
            const evaluation = db.evaluations.find(e => e.projectId === projectId && e.role === currentRole);
            
            if (!evaluation || !evaluation.students || evaluation.students.length === 0) {
                alert("لا توجد بيانات تقييم للمشاركة");
                return;
            }
            
            // بناء الرسالة
            let message = `*تقرير تقييم مشاريع التخرج*\n\n`;
            message += `*المشروع:* ${project.title}\n`;
            message += `*نوع التقييم:* ${currentRole === 'supervisor' ? 'تقييم المشرف' : 'تقييم المناقشة'}\n`;
            message += `*اسم المشرف:* ${project.supervisor}\n`;
            message += `*التاريخ:* ${new Date(evaluation.date).toLocaleDateString('ar-SA')}\n\n`;
            message += `*نتائج الطلاب:*\n`;
            
            evaluation.students.forEach((student, index) => {
                message += `${index + 1}. ${student.name}: ${student.totalScore}/100 (${student.result})\n`;
            });
            
            message += `\n---\nتم إنشاء هذا التقرير بواسطة نظام تقييم مشاريع التخرج`;
            
            // تشفير الرسالة للرابط
            const encodedMessage = encodeURIComponent(message);
            const whatsappUrl = `https://wa.me/?text=${encodedMessage}`;
            
            // فتح الرابط
            window.open(whatsappUrl, '_blank');
        }

        // ===== وظائف مساعدة =====
        function saveDatabase() {
            localStorage.setItem('graduation_system_db', JSON.stringify(db));
        }

        function updateAllStats() {
            const statsProjects = document.getElementById('statsProjects');
            const statsStudents = document.getElementById('statsStudents');
            const statsEvaluations = document.getElementById('statsEvaluations');
            
            if (statsProjects) statsProjects.textContent = db.projects.length;
            if (statsStudents) statsStudents.textContent = db.students.length;
            if (statsEvaluations) statsEvaluations.textContent = db.evaluations.length;
        }

        function showToast(message, type = 'info') {
            // إنشاء عنصر الإشعار
            const toast = document.createElement('div');
            toast.className = `fixed top-6 right-6 px-6 py-3 rounded-xl shadow-2xl z-50 animate-fade-in ${
                type === 'success' ? 'bg-gradient-to-r from-emerald-500 to-green-600 text-white' :
                type === 'error' ? 'bg-gradient-to-r from-rose-500 to-red-600 text-white' :
                'bg-gradient-to-r from-blue-500 to-indigo-600 text-white'
            }`;
            
            toast.innerHTML = `
                <div class="flex items-center gap-3">
                    <i class="fas fa-${type === 'success' ? 'check-circle' : type === 'error' ? 'exclamation-circle' : 'info-circle'} text-xl"></i>
                    <div>
                        <div class="font-bold">${message}</div>
                    </div>
                </div>
            `;
            
            // إضافة الإشعار للصفحة
            document.body.appendChild(toast);
            
            // إزالة الإشعار بعد 3 ثواني
            setTimeout(() => {
                toast.style.opacity = '0';
                toast.style.transform = 'translateX(100%)';
                setTimeout(() => {
                    document.body.removeChild(toast);
                }, 300);
            }, 3000);
        }
    </script>
</body>
</html>
