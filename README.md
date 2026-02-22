<!DOCTYPE html>
<html lang="ar" dir="rtl">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>نظام المحرر الاحترافي V36 - النسخة المطورة</title>

    <!-- المكتبات -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/mammoth/1.6.0/mammoth.browser.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <script src="https://cdn.tailwindcss.com"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/5.15.3/css/all.min.css">
    <link href="https://fonts.googleapis.com/css2?family=Cairo:wght@400;600;700;900&display=swap" rel="stylesheet">
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        primary: "var(--primary-color, #2563eb)",
                        dark: "#0f172a",
                        success: "#059669",
                        danger: "#dc2626",
                        info: "#0284c7",
                        library: "#7c3aed",
                        customers: "#f59e0b",
                        ribbon: "#f8fafc",
                        "pastel-blue": "#f0f7ff",
                        "pastel-pink": "#fff1f2",
                        "pastel-green": "#f0fdf4",
                        "pastel-yellow": "#fefce8",
                        "pastel-purple": "#faf5ff"
                    },
                    fontFamily: { 'cairo': ['Cairo', 'sans-serif'] }
                }
            }
        }
    </script>

    <style>
        body {
            font-family: 'Cairo', sans-serif;
            background-color: #f1f5f9;
            overflow: hidden;
            display: flex;
            flex-direction: column;
            height: 100vh;
        }

        * {
            font-variant-numeric: lining-nums !important;
            -moz-font-feature-settings: "lnum" !important;
            -webkit-font-feature-settings: "lnum" !important;
            font-feature-settings: "lnum" !important;
        }

        .ribbon-container {
            background: #ffffff;
            border-bottom: 1px solid #e2e8f0;
            padding: 8px 20px;
            display: flex;
            gap: 25px;
            align-items: center;
            z-index: 100;
            flex-shrink: 0;
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.02);
        }

        .ribbon-group {
            display: flex;
            flex-direction: column;
            align-items: center;
            border-left: 1px solid #f1f5f9;
            padding: 0 15px;
            gap: 4px;
        }

        .ribbon-label {
            font-size: 10px;
            color: #64748b;
            font-weight: 700;
            text-transform: uppercase;
            letter-spacing: 0.5px;
        }

        .tool-btn {
            width: 36px;
            height: 36px;
            display: flex;
            align-items: center;
            justify-content: center;
            border-radius: 8px;
            transition: all 0.2s;
            color: #475569;
            background: transparent;
            border: none;
            cursor: pointer;
        }

        .tool-btn:hover {
            background: #eff6ff;
            color: #2563eb;
            transform: translateY(-1px);
        }

        .tool-btn.active {
            background: #dbeafe;
            color: #1e40af;
        }

        .tool-btn i {
            font-size: 1.1rem;
        }

        .tool-select {
            border: 1px solid #e2e8f0;
            padding: 4px 8px;
            border-radius: 6px;
            font-size: 13px;
            outline: none;
            transition: border-color 0.2s;
        }

        .tool-select:focus {
            border-color: #3b82f6;
        }

        .a4-paper {
            width: 210mm;
            min-height: 297mm;
            background-color: white !important;
            padding: 20mm;
            box-shadow: 0 0 50px rgba(0, 0, 0, 0.1);
            position: relative;
            text-align: right;
            direction: rtl;
            outline: none;
            overflow: visible;
            z-index: 1;
            margin: auto;
            color: #1e293b !important;
            /* ضمان بقاء النص داكناً دائماً */
            line-height: 1.6;
        }

        .a4-content {
            min-height: 250mm;
            outline: none;
        }

        .img-container {
            position: absolute;
            display: inline-block;
            cursor: move;
            border: 2px dashed transparent;
            line-height: 0;
            z-index: 5;
        }

        .img-container:hover {
            border-color: #3b82f6;
            background: rgba(59, 130, 246, 0.05);
        }

        .img-container img {
            width: 100%;
            height: 100%;
            pointer-events: none;
        }

        .resizer {
            width: 12px;
            height: 12px;
            background: #3b82f6;
            position: absolute;
            right: -6px;
            bottom: -6px;
            cursor: nwse-resize;
            z-index: 10;
            border-radius: 50%;
            border: 2px solid white;
        }

        .variable-placeholder {
            background-color: #fef08a;
            border-bottom: 2px solid #eab308;
            color: black !important;
            padding: 0 4px;
            font-weight: bold;
            border-radius: 4px;
        }

        .line-clamp-2 {
            display: -webkit-box;
            -webkit-line-clamp: 2;
            line-clamp: 2;
            -webkit-box-orient: vertical;
            overflow: hidden;
        }

        .hidden-view {
            display: none !important;
        }

        .custom-scrollbar::-webkit-scrollbar {
            width: 6px;
        }

        .custom-scrollbar::-webkit-scrollbar-thumb {
            background: #cbd5e1;
            border-radius: 10px;
        }

        @media print {
            @page {
                margin: 0;
                /* هذا السطر يخفي مسار الملف والتاريخ واطراف المتصفح الافتراضية */
            }

            body {
                background: white !important;
                overflow: visible !important;
                height: auto !important;
                margin: 0 !important;
                padding: 0 !important;
            }

            .no-print,
            .resizer,
            .ribbon-container,
            header,
            aside,
            .fixed,
            #toast {
                display: none !important;
            }

            #contentArea {
                display: block !important;
                overflow: visible !important;
            }

            main {
                padding: 0 !important;
                margin: 0 !important;
                background: transparent !important;
                overflow: visible !important;
            }

            .pages-view {
                padding: 0 !important;
                margin: 0 !important;
                display: block !important;
            }

            .a4-paper {
                box-shadow: none !important;
                margin: 0 auto !important;
                border: none !important;
                width: 210mm !important;
                height: 297mm !important;
                padding: 15mm !important;
                /* تقليل الهامش قليلاً للطباعة الورقية */
                page-break-after: always;
                page-break-inside: avoid;
                color: black !important;
                background: white !important;
            }

            .variable-placeholder {
                background: transparent !important;
                border: none !important;
                padding: 0 !important;
                color: black !important;
                font-weight: normal !important;
            }

            .img-container {
                border: none !important;
                background: transparent !important;
                box-shadow: none !important;
            }

            .resizer,
            .no-print,
            [contenteditable]:focus {
                display: none !important;
                outline: none !important;
            }

            * {
                -webkit-print-color-adjust: exact !important;
                print-color-adjust: exact !important;
            }
        }

        /* Settings CSS */
        .settings-tab-btn {
            width: 100%;
            display: flex;
            align-items: center;
            gap: 12px;
            padding: 14px 20px;
            border-radius: 16px;
            font-size: 13px;
            font-weight: 900;
            color: #64748b;
            transition: all 0.3s;
            text-align: right;
        }

        .settings-tab-btn i {
            font-size: 16px;
            width: 20px;
        }

        .settings-tab-btn:hover {
            background: rgba(37, 99, 235, 0.05);
            color: #2563eb;
        }

        .settings-tab-btn.active {
            background: #2563eb;
            color: white;
            box-shadow: 0 10px 20px -5px rgba(37, 99, 235, 0.4);
        }

        .settings-section {
            display: none;
        }

        .settings-section.active {
            display: block;
            animation: fadeIn 0.4s ease-out;
        }

        @keyframes fadeIn {
            from {
                opacity: 0;
                transform: translateY(10px);
            }

            to {
                opacity: 1;
                transform: translateY(0);
            }
        }

        /* Switch Toggle */
        .switch {
            position: relative;
            display: inline-block;
            width: 54px;
            height: 28px;
        }

        .switch input {
            opacity: 0;
            width: 0;
            height: 0;
        }

        .slider {
            position: absolute;
            cursor: pointer;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background-color: #cbd5e1;
            transition: .4s;
        }

        .slider:before {
            position: absolute;
            content: "";
            height: 20px;
            width: 20px;
            left: 4px;
            bottom: 4px;
            background-color: white;
            transition: .4s;
        }

        input:checked+.slider {
            background-color: #2563eb;
        }

        input:focus+.slider {
            box-shadow: 0 0 1px #2563eb;
        }

        input:checked+.slider:before {
            transform: translateX(26px);
        }

        .slider.round {
            border-radius: 34px;
        }

        .slider.round:before {
            border-radius: 50%;
        }

        #settingsModal {
            opacity: 0;
            pointer-events: none;
        }

        #settingsModal.active {
            opacity: 1;
            pointer-events: auto;
        }

        #settingsModal.active .glass-card {
            transform: scale(1);
            opacity: 1;
        }

        #settingsModal .glass-card {
            transform: scale(0.95);
            opacity: 0;
            transition: all 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
        }

        /* Dark Mode */
        body.dark-mode {
            background-color: #0f172a;
            color: #f8fafc;
        }

        body.dark-mode .a4-paper {
            color: #1e293b !important;
            /* إجبار النص على اللون الداكن داخل الورقة حتى في الوضع الليلي */
        }

        .dark-mode .bg-white {
            background-color: #1e293b !important;
        }

        .dark-mode .bg-slate-50 {
            background-color: #0f172a !important;
        }

        .dark-mode .text-slate-800,
        .dark-mode .text-slate-700 {
            color: #f1f5f9 !important;
        }

        .dark-mode .text-slate-500,
        .dark-mode .text-slate-400 {
            color: #94a3b8 !important;
        }

        .dark-mode .border-slate-100,
        .dark-mode .border-slate-200 {
            border-color: #334155 !important;
        }

        .dark-mode .glass-card {
            background: rgba(30, 41, 59, 0.8) !important;
            border-color: rgba(255, 255, 255, 0.1) !important;
        }

        .dark-mode aside {
            background-color: #1e293b !important;
            border-color: #334155 !important;
        }

        .dark-mode input,
        .dark-mode select {
            background-color: #0f172a !important;
            border-color: #334155 !important;
            color: white !important;
        }

        /* Modal Styles */
        .modal-overlay {
            position: fixed;
            inset: 0;
            background: rgba(15, 23, 42, 0.7);
            backdrop-filter: blur(8px);
            z-index: 500;
            display: flex;
            align-items: center;
            justify-content: center;
            opacity: 0;
            pointer-events: none;
            transition: all 0.3s;
        }

        .modal-overlay.active {
            opacity: 1;
            pointer-events: auto;
        }

        .modal-content {
            background: white;
            width: 500px;
            max-width: 90vw;
            border-radius: 2.5rem;
            padding: 40px;
            box-shadow: 0 25px 50px -12px rgba(0, 0, 0, 0.25);
            transform: scale(0.95);
            transition: all 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
        }

        .modal-overlay.active .modal-content {
            transform: scale(1);
        }

        /* WhatsApp Modal Styles */
        #whatsappModal .modal-content {
            width: 800px;
            max-width: 95vw;
            padding: 0;
            overflow: hidden;
            border-radius: 2rem;
        }

        .wa-template-item {
            transition: all 0.2s;
            border: 1px solid transparent;
        }

        .wa-template-item:hover {
            background-color: #f0fdf4;
            border-color: #22c55e;
        }

        .dark-mode .wa-template-item:hover {
            background-color: rgba(34, 197, 94, 0.1);
        }
    </style>
</head>

<body class="h-screen flex flex-col">

    <input type="color" id="nativeColorPicker" class="hidden" onchange="window.applyColor(this.value)">

    <!-- Loading -->
    <div id="loadingOverlay"
        class="fixed inset-0 bg-dark/70 z-[300] hidden items-center justify-center flex-col text-white backdrop-blur-md">
        <div class="animate-spin rounded-full h-16 w-16 border-t-4 border-b-4 border-primary mb-4"></div>
        <p class="text-xl font-bold tracking-widest animate-pulse">جاري المزامنة السحابية...</p>
    </div>

    <!-- Modal: New Document Setup -->
    <div id="setupModal" class="modal-overlay no-print">
        <div class="modal-content">
            <h2 class="text-2xl font-black text-slate-800 mb-6 flex items-center gap-3">
                <i class="fas fa-plus-circle text-primary"></i> إعداد مستند جديد
            </h2>

            <div class="space-y-6">
                <div>
                    <label class="block text-xs font-black text-slate-400 mb-2 mr-2">1. اختيار القسم</label>
                    <select id="setupCatSelect" onchange="window.toggleNewCatInput(this.value)"
                        class="w-full border-2 border-slate-100 rounded-2xl p-4 text-sm font-bold outline-none focus:border-primary transition-all">
                        <option value="new">+ إنشاء قسم جديد</option>
                    </select>
                </div>

                <div id="newCatInputGroup">
                    <label class="block text-xs font-black text-slate-400 mb-2 mr-2">اسم القسم الجديد</label>
                    <input type="text" id="setupNewCatInput" placeholder="مثلاً: عقود تجارية"
                        class="w-full border-2 border-slate-100 rounded-2xl p-4 text-sm font-bold outline-none focus:border-primary transition-all">
                </div>

                <div>
                    <label class="block text-xs font-black text-slate-400 mb-2 mr-2">2. اسم النموذج</label>
                    <input type="text" id="setupModelNameInput" placeholder="مثلاً: عقد عمل موحد"
                        class="w-full border-2 border-slate-100 rounded-2xl p-4 text-sm font-bold outline-none focus:border-primary transition-all">
                </div>

                <div class="flex gap-3 pt-4">
                    <button onclick="window.confirmDesignSetup()"
                        class="flex-1 bg-primary text-white py-4 rounded-2xl font-black shadow-lg shadow-blue-200 hover:bg-blue-700 transition">إنشاء
                        التصميم</button>
                    <button onclick="window.closeDesignSetup()"
                        class="px-8 bg-slate-100 text-slate-500 py-4 rounded-2xl font-black hover:bg-slate-200 transition">إلغاء</button>
                </div>
            </div>
        </div>
    </div>

    <!-- Header -->
    <header class="bg-white border-b px-6 py-3 flex justify-between items-center z-[110] no-print">
        <div class="flex items-center gap-4">
            <div
                class="bg-primary text-white w-10 h-10 flex items-center justify-center rounded-xl shadow-lg shadow-blue-200">
                <i class="fas fa-file-signature text-lg"></i>
            </div>
            <div>
                <h1 class="text-lg font-black text-slate-800 leading-none">نظام المحرر الذكي</h1>
                <span id="activeModelNameDisp"
                    class="text-[10px] font-bold text-primary bg-blue-50 px-2 py-0.5 rounded-full mt-1 inline-block">لم
                    يتم اختيار قالب</span>
            </div>
        </div>
        <div class="flex items-center gap-2">
            <button id="printBtn" onclick="window.print()"
                class="hidden bg-dark text-white px-4 py-2 rounded-lg text-xs font-bold hover:bg-slate-800 transition">طباعة</button>
            <button onclick="window.goToStats()"
                class="bg-indigo-600 text-white px-4 py-2 rounded-lg text-xs font-bold shadow-sm hover:bg-indigo-700 transition">الإحصائيات</button>
            <button onclick="window.goToCustomerList()"
                class="bg-customers text-white px-4 py-2 rounded-lg text-xs font-bold shadow-sm hover:opacity-90 transition">السجلات</button>
            <button onclick="window.goToLibrary()"
                class="bg-slate-100 text-slate-600 px-4 py-2 rounded-lg text-xs font-bold border hover:bg-slate-200 transition">المكتبة</button>
            <button onclick="window.openWhatsAppModal()"
                class="bg-emerald-500 text-white px-4 py-2 rounded-lg text-xs font-bold shadow-sm hover:bg-emerald-600 transition flex items-center gap-2">
                <i class="fab fa-whatsapp text-lg"></i> الواتساب
            </button>
            <div class="h-6 w-px bg-slate-200 mx-2"></div>

            <button onclick="window.openSettings()" class="tool-btn hover:bg-blue-50 text-slate-500 transition"
                title="الإعدادات">
                <i class="fas fa-cog text-xl"></i>
            </button>

            <button onclick="window.exportToWord()" class="text-blue-600 hover:bg-blue-50 p-2 rounded-lg transition"
                title="تصدير Word"><i class="fas fa-file-word text-xl"></i></button>
            <button onclick="window.exportToPDF()" class="text-danger hover:bg-red-50 p-2 rounded-lg transition"
                title="تصدير PDF"><i class="fas fa-file-pdf text-xl"></i></button>
            <button onclick="window.exportAllDataJSON()"
                class="text-amber-600 hover:bg-amber-50 p-2 rounded-lg transition" title="تصدير JSON (نسخة احتياطية)">
                <i class="fas fa-file-export text-xl"></i>
            </button>
            <button onclick="window.importAllDataJSON()"
                class="text-emerald-600 hover:bg-emerald-50 p-2 rounded-lg transition" title="استيراد JSON (استعادة)">
                <i class="fas fa-file-import text-xl"></i>
            </button>
            <input type="file" id="importJsonInput" accept=".json" style="display:none"
                onchange="window.handleJsonImport(event)">
            <button onclick="window.editCurrentModel()"
                class="bg-slate-800 text-white px-6 py-2 rounded-xl font-bold text-sm shadow-lg hover:bg-slate-700 transition mx-1">تعديل
                النموذج</button>
            <button onclick="window.openDesignSetup()"
                class="bg-primary text-white px-6 py-2 rounded-xl font-black text-sm shadow-lg hover:bg-blue-700 transition mx-1">+
                مستند جديد</button>
        </div>
    </header>

    <!-- Ribbon -->
    <div id="globalRibbon" class="ribbon-container no-print">
        <div class="ribbon-group">
            <div class="flex items-center gap-2">
                <select onchange="window.doCommand('fontName', this.value)" class="tool-select w-36 font-bold">
                    <option value="Cairo">Cairo</option>
                    <option value="Arial">Arial</option>
                    <option value="Tahoma">Tahoma</option>
                    <option value="Courier New">Courier New</option>
                </select>
                <select onchange="window.doCommand('fontSize', this.value)" class="tool-select w-16 font-bold">
                    <option value="3">12</option>
                    <option value="4">14</option>
                    <option value="5">18</option>
                    <option value="6">24</option>
                    <option value="7">36</option>
                </select>
            </div>
            <div class="ribbon-label">الخط والحجم</div>
        </div>

        <div class="ribbon-group">
            <div class="flex gap-1">
                <button onclick="window.doCommand('bold')" class="tool-btn" title="عريض"><i
                        class="fas fa-bold"></i></button>
                <button onclick="window.doCommand('italic')" class="tool-btn" title="مائل"><i
                        class="fas fa-italic"></i></button>
                <button onclick="window.doCommand('underline')" class="tool-btn" title="تحته خط"><i
                        class="fas fa-underline"></i></button>
                <div class="w-px h-6 bg-slate-100 mx-1 self-center"></div>
                <button onclick="window.pickColor('foreColor')" class="tool-btn" title="لون الخط"><i
                        class="fas fa-font text-blue-600"></i></button>
                <button onclick="window.pickColor('hiliteColor')" class="tool-btn" title="لون الخلفية"><i
                        class="fas fa-fill-drip text-orange-500"></i></button>
            </div>
            <div class="ribbon-label">تنسيق النص</div>
        </div>

        <div class="ribbon-group">
            <div class="flex gap-1">
                <button onclick="window.doCommand('justifyRight')" class="tool-btn"><i
                        class="fas fa-align-right"></i></button>
                <button onclick="window.doCommand('justifyCenter')" class="tool-btn"><i
                        class="fas fa-align-center"></i></button>
                <button onclick="window.doCommand('justifyLeft')" class="tool-btn"><i
                        class="fas fa-align-left"></i></button>
                <div class="w-px h-6 bg-slate-100 mx-1 self-center"></div>
                <button onclick="window.changeDir('rtl')" class="tool-btn" title="من اليمين"><i
                        class="fas fa-paragraph fa-flip-horizontal"></i></button>
                <button onclick="window.changeDir('ltr')" class="tool-btn" title="من اليسار"><i
                        class="fas fa-paragraph"></i></button>
            </div>
            <div class="ribbon-label">المحاذاة</div>
        </div>

        <div class="ribbon-group">
            <div class="flex gap-1">
                <button onclick="window.doCommand('insertUnorderedList')" class="tool-btn"><i
                        class="fas fa-list-ul"></i></button>
                <button onclick="window.doCommand('insertOrderedList')" class="tool-btn"><i
                        class="fas fa-list-ol"></i></button>
                <button onclick="window.doCommand('removeFormat')" class="tool-btn text-danger"><i
                        class="fas fa-eraser"></i></button>
            </div>
            <div class="ribbon-label">أدوات إضافية</div>
        </div>

        <div class="flex-1"></div> <!-- Spacer -->

        <div id="editModeIndicator" class="hidden">
            <button onclick="window.saveHybridTemplate()"
                class="bg-success text-white px-6 py-2 rounded-xl font-bold shadow-lg hover:scale-105 transition flex items-center gap-2">
                <i class="fas fa-check"></i> حفظ التعديلات
            </button>
        </div>
    </div>

    <!-- Main Content -->
    <div id="contentArea" class="flex-1 flex overflow-hidden">

        <!-- الرئيسية -->
        <div id="mainPage" class="flex-1 flex overflow-hidden">
            <aside class="w-80 bg-white border-l p-6 overflow-y-auto custom-scrollbar flex flex-col shadow-xl no-print">
                <!-- زر إضافة وثيقة جديدة -->
                <div class="mb-6 p-4 bg-blue-50/50 rounded-2xl border border-blue-100 flex flex-col items-center gap-3">
                    <button onclick="window.openAddModelModal()"
                        class="w-full bg-primary text-white py-4 rounded-2xl font-black text-sm shadow-lg hover:bg-blue-700 transition flex items-center justify-center gap-3">
                        <i class="fas fa-plus-circle text-lg"></i>
                        إضافة وثيقة جديدة
                    </button>
                    <p class="text-[10px] font-bold text-slate-400">يمكنك إضافة عدة نماذج لنفس الشخص</p>
                </div>
                <!-- قائمة النماذج النشطة -->
                <div id="activeSessionModels" class="mb-4 flex flex-wrap gap-2 px-2"></div>
                <div id="fillFieldsContainer" class="space-y-4 flex-1"></div>
                <button onclick="window.saveHybridRecord()"
                    class="w-full mt-6 bg-success text-white py-4 rounded-2xl font-black text-sm shadow-xl hover:bg-emerald-700 transition flex items-center justify-center gap-2">
                    <i class="fas fa-print"></i> حفظ وأرشفة الوثيقة
                </button>
            </aside>
            <main class="flex-1 bg-slate-200 overflow-y-auto p-12 custom-scrollbar">
                <div id="mainSessionContainer" class="flex flex-col items-center gap-12">
                    <!-- سيتم تعبئة النماذج النشطة هنا بواسطة JS -->
                </div>
            </main>
        </div>

        <!-- التصميم -->
        <div id="designPage" class="flex-1 flex overflow-hidden hidden-view">
            <aside class="w-96 bg-white border-l shadow-2xl z-20 overflow-y-auto custom-scrollbar p-6 no-print">
                <button onclick="window.saveAndReturn()"
                    class="w-full mb-3 bg-slate-100 text-slate-600 py-3 rounded-2xl font-black text-xs hover:bg-slate-200 transition flex items-center justify-center gap-2">
                    <i class="fas fa-arrow-right"></i> الرجوع للرئيسية
                </button>
                <button onclick="window.clearDesignContent()"
                    class="w-full mb-6 border-2 border-red-50 text-red-500 py-3 rounded-2xl font-black text-xs hover:bg-red-50 transition flex items-center justify-center gap-2">
                    <i class="fas fa-trash-alt"></i> مسح محتوى القالب
                </button>
                <div class="mb-10 pb-6 border-b-2 border-slate-50">
                    <div class="text-[10px] font-black text-primary uppercase tracking-wider mb-2">النموذج النشط</div>
                    <h2 class="text-3xl font-black text-slate-800 leading-tight" id="designModelTitle">---</h2>
                </div>
                <div class="space-y-8">
                    <section>
                        <h3 class="font-black text-slate-800 border-r-4 border-primary pr-3 mb-4 text-sm">1. استيراد ملف
                            خارجي</h3>
                        <input type="file" id="wordInput" class="hidden" accept=".docx,.doc"
                            onchange="window.handleWordLoad(event)">
                        <button onclick="document.getElementById('wordInput').click()"
                            class="w-full border-2 border-dashed border-primary/20 py-10 rounded-[2.5rem] text-primary font-black text-xs hover:bg-blue-50 transition flex flex-col items-center gap-3">
                            <i class="fas fa-file-upload text-3xl"></i>
                            تحميل ملف Word (DOCX / DOC)
                        </button>
                    </section>

                    <section>
                        <h3 class="font-black text-slate-800 border-r-4 border-primary pr-3 mb-4 text-sm">2. إدراج عناصر
                        </h3>
                        <input type="file" id="imageInsertInput" class="hidden" accept="image/*"
                            onchange="window.processInsertedImage(event)">
                        <button onclick="document.getElementById('imageInsertInput').click()"
                            class="w-full bg-slate-100 py-4 rounded-2xl text-slate-600 font-black text-xs hover:bg-slate-200 transition flex items-center justify-center gap-2">
                            <i class="fas fa-image"></i> إدراج صورة للقالب
                        </button>
                    </section>

                    <section id="propertiesPanel" class="hidden bg-blue-50 p-4 rounded-2xl border border-blue-100">
                        <label class="block text-[10px] font-black text-blue-400 mb-2 uppercase">تعديل مسمى
                            المتغير</label>
                        <input type="text" id="editLabelInput" oninput="window.updateActiveFieldLabel(this.value)"
                            class="w-full border-2 border-white p-3 rounded-xl font-bold outline-none shadow-sm focus:border-primary">
                    </section>

                    <section>
                        <h3 class="font-black text-slate-800 border-r-4 border-primary pr-3 mb-4 text-sm">3. الحقول
                            والمتغيرات</h3>
                        <div id="designFieldsList" class="space-y-2"></div>
                    </section>
                </div>
            </aside>
            <main class="flex-1 bg-slate-200 overflow-y-auto p-12 custom-scrollbar relative flex justify-center">
                <div id="designA4" class="a4-paper">
                    <div id="designA4Content" class="a4-content outline-none" contenteditable="true"></div>
                </div>
                <div class="fixed bottom-10 left-1/2 -translate-x-1/2 no-print z-50">
                    <button onclick="window.makeSelectedTextVariable()"
                        class="bg-info text-white px-12 py-5 rounded-full font-black shadow-2xl hover:scale-105 transition-all flex items-center gap-3 decoration-none">
                        <i class="fas fa-highlighter text-xl"></i>
                        تحويل النص المظلل لمتغير ذكي
                    </button>
                </div>
            </main>
        </div>

        <!-- نافذة الإعدادات -->
        <div id="settingsModal"
            class="fixed inset-0 z-[100] flex items-center justify-center p-4 transition-all duration-300">
            <div class="absolute inset-0 bg-slate-900/40 backdrop-blur-sm" onclick="window.closeSettings()"></div>
            <div
                class="bg-white w-full max-w-4xl max-h-[90vh] rounded-[2.5rem] shadow-2xl relative z-10 overflow-hidden flex flex-col md:flex-row border border-white/20 glass-card">
                <!-- شريط جانبي للتنقل -->
                <aside class="w-full md:w-64 bg-slate-50/50 border-l border-slate-100 p-8 flex flex-col gap-2">
                    <h3 class="text-xl font-black text-slate-800 mb-6 flex items-center gap-2">
                        <i class="fas fa-sliders-h text-primary"></i> الإعدادات
                    </h3>
                    <button onclick="window.switchSettingsTab('ui')" class="settings-tab-btn active" data-tab="ui">
                        <i class="fas fa-paint-brush"></i> الواجهة والمظهر
                    </button>
                    <button onclick="window.switchSettingsTab('pdf')" class="settings-tab-btn" data-tab="pdf">
                        <i class="fas fa-file-pdf"></i> إعدادات PDF
                    </button>
                    <button onclick="window.switchSettingsTab('word')" class="settings-tab-btn" data-tab="word">
                        <i class="fas fa-file-word"></i> إعدادات Word
                    </button>
                    <button onclick="window.switchSettingsTab('firebase')" class="settings-tab-btn" data-tab="firebase">
                        <i class="fas fa-cloud"></i> السحابة والمزامنة
                    </button>
                    <button onclick="window.switchSettingsTab('security')" class="settings-tab-btn" data-tab="security">
                        <i class="fas fa-shield-alt"></i> الأمان
                    </button>

                    <div class="mt-auto pt-6 border-t border-slate-200">
                        <button onclick="window.closeSettings()"
                            class="w-full bg-slate-800 text-white py-3 rounded-2xl font-black text-xs hover:bg-slate-900 transition">إغلاق
                            وحفظ</button>
                    </div>
                </aside>

                <!-- محتوى الإعدادات -->
                <main class="flex-1 p-10 overflow-y-auto custom-scrollbar bg-white/70">
                    <!-- قسم الواجهة -->
                    <div id="tab-ui" class="settings-section active">
                        <h4 class="text-lg font-black text-slate-800 mb-8 pb-2 border-b-2 border-primary/10">إعدادات
                            الواجهة والمظهر</h4>
                        <div class="space-y-8">
                            <div class="flex items-center justify-between p-4 bg-slate-50 rounded-2xl">
                                <div>
                                    <p class="font-black text-slate-800 text-sm">الوضع الليلي</p>
                                    <p class="text-[10px] text-slate-500 font-bold">تغيير ألوان الواجهة للنمط الداكن</p>
                                </div>
                                <label class="switch">
                                    <input type="checkbox" id="set-darkMode"
                                        onchange="window.updateSetting('ui', 'darkMode', this.checked)">
                                    <span class="slider round"></span>
                                </label>
                            </div>

                            <div class="grid grid-cols-2 gap-6">
                                <div>
                                    <label class="block text-xs font-black text-slate-500 mb-2 mr-2">لون السمة
                                        (Accent)</label>
                                    <input type="color" id="set-accentColor"
                                        onchange="window.updateSetting('ui', 'accentColor', this.value)"
                                        class="w-full h-12 rounded-xl cursor-pointer border-none p-1">
                                </div>
                                <div>
                                    <label class="block text-xs font-black text-slate-500 mb-2 mr-2">نوع الخط
                                        الأساسي</label>
                                    <select id="set-fontFamily"
                                        onchange="window.updateSetting('ui', 'fontFamily', this.value)"
                                        class="w-full bg-slate-50 border-2 border-slate-100 rounded-xl p-3 text-xs font-bold focus:border-primary outline-none transition-all">
                                        <option value="'Cairo', sans-serif">Cairo (أساسي)</option>
                                        <option value="'Almarai', sans-serif">Almarai</option>
                                        <option value="'Tajawal', sans-serif">Tajawal</option>
                                        <option value="Arial, sans-serif">Arial</option>
                                    </select>
                                </div>
                            </div>
                        </div>
                    </div>

                    <!-- قسم PDF -->
                    <div id="tab-pdf" class="settings-section">
                        <h4 class="text-lg font-black text-slate-800 mb-8 pb-2 border-b-2 border-primary/10">إعدادات
                            تصدير PDF</h4>
                        <div class="space-y-6">
                            <div class="grid grid-cols-2 gap-6">
                                <div>
                                    <label class="block text-xs font-black text-slate-500 mb-2 mr-2">حجم الصفحة</label>
                                    <select id="set-pdfPageSize"
                                        onchange="window.updateSetting('pdf', 'pageSize', this.value)"
                                        class="w-full bg-slate-50 border-2 border-slate-100 rounded-xl p-3 text-xs font-bold outline-none transition-all">
                                        <option value="A4">A4 (Standard)</option>
                                        <option value="A5">A5 (Small)</option>
                                        <option value="Letter">Letter</option>
                                    </select>
                                </div>
                                <div>
                                    <label class="block text-xs font-black text-slate-500 mb-2 mr-2">الهوامش
                                        (mm)</label>
                                    <input type="number" id="set-pdfMargin" value="20"
                                        onchange="window.updateSetting('pdf', 'margin', this.value)"
                                        class="w-full bg-slate-50 border-2 border-slate-100 rounded-xl p-3 text-xs font-bold outline-none">
                                </div>
                            </div>
                            <div class="flex items-center justify-between p-4 bg-slate-50 rounded-2xl">
                                <div>
                                    <p class="font-black text-slate-800 text-sm">إظهار التاريخ في التذييل</p>
                                </div>
                                <label class="switch">
                                    <input type="checkbox" id="set-pdfShowDate"
                                        onchange="window.updateSetting('pdf', 'showDate', this.checked)">
                                    <span class="slider round"></span>
                                </label>
                            </div>
                        </div>
                    </div>

                    <!-- قسم Word -->
                    <div id="tab-word" class="settings-section">
                        <h4 class="text-lg font-black text-slate-800 mb-8 pb-2 border-b-2 border-primary/10">إعدادات
                            تصدير Word</h4>
                        <div class="space-y-6">
                            <div class="flex items-center justify-between p-4 bg-slate-50 rounded-2xl">
                                <div>
                                    <p class="font-black text-slate-800 text-sm">فرض اتجاه RTL</p>
                                    <p class="text-[10px] text-slate-500 font-bold">لضمان تنسيق اللغة العربية بشكل صحيح
                                    </p>
                                </div>
                                <label class="switch">
                                    <input type="checkbox" id="set-wordForceRTL" checked
                                        onchange="window.updateSetting('word', 'forceRTL', this.checked)">
                                    <span class="slider round"></span>
                                </label>
                            </div>
                        </div>
                    </div>

                    <!-- قسم الأمان -->
                    <div id="tab-security" class="settings-section">
                        <h4 class="text-lg font-black text-slate-800 mb-8 pb-2 border-b-2 border-primary/10">إعدادات
                            الأمان</h4>
                        <div class="space-y-6 max-w-sm ml-auto">
                            <div>
                                <label class="block text-xs font-black text-slate-500 mb-2 mr-2">كلمة المرور
                                    الحالية</label>
                                <input type="password" id="currentPass"
                                    class="w-full bg-slate-50 border-2 border-slate-100 rounded-xl p-3 text-sm font-bold outline-none focus:border-primary transition-all">
                            </div>
                            <div>
                                <label class="block text-xs font-black text-slate-500 mb-2 mr-2">كلمة المرور
                                    الجديدة</label>
                                <input type="password" id="newPass"
                                    class="w-full bg-slate-50 border-2 border-slate-100 rounded-xl p-3 text-sm font-bold outline-none focus:border-primary transition-all">
                            </div>
                            <div>
                                <label class="block text-xs font-black text-slate-500 mb-2 mr-2">تأكيد كلمة المرور
                                    الجديدة</label>
                                <input type="password" id="confirmPass"
                                    class="w-full bg-slate-50 border-2 border-slate-100 rounded-xl p-3 text-sm font-bold outline-none focus:border-primary transition-all">
                            </div>
                            <button onclick="window.changePassword()"
                                class="w-full bg-primary text-white py-3 rounded-xl font-black text-xs hover:bg-blue-700 transition shadow-lg shadow-blue-100">تحديث
                                كلمة المرور</button>
                        </div>
                    </div>
            </div>
        </div>

        <!-- WhatsApp Modal -->
        <div id="whatsappModal" class="modal-overlay no-print">
            <div class="modal-content flex flex-col md:flex-row h-[600px] overflow-hidden">
                <!-- Side Panel: Templates -->
                <aside class="w-full md:w-80 bg-slate-50 border-l p-6 flex flex-col overflow-hidden">
                    <h3 class="font-black text-slate-800 mb-4 flex items-center gap-2">
                        <i class="fas fa-layer-group text-emerald-500"></i> قوالب الرسائل
                    </h3>
                    <div id="waTemplatesList" class="flex-1 overflow-y-auto custom-scrollbar space-y-2 mb-4 pr-1">
                        <!-- Templates will be injected here -->
                    </div>
                    <div class="pt-4 border-t border-slate-200">
                        <textarea id="newWaTemplateText" placeholder="اكتب قالب رسالة جديد هنا..."
                            class="w-full p-3 text-xs font-bold border-2 border-slate-200 rounded-xl focus:border-emerald-500 outline-none resize-none h-20 mb-2 transition-all"></textarea>
                        <button onclick="window.addWhatsAppTemplate()"
                            class="w-full bg-emerald-500 text-white py-2 rounded-xl text-xs font-black shadow-lg hover:bg-emerald-600 transition flex items-center justify-center gap-2">
                            <i class="fas fa-plus"></i> إضافة قالب مخصص
                        </button>
                    </div>
                </aside>

                <!-- Main Content: Form -->
                <main class="flex-1 p-8 flex flex-col bg-white overflow-hidden">
                    <div class="flex justify-between items-center mb-8">
                        <h2 class="text-2xl font-black text-slate-800 flex items-center gap-3">
                            <i class="fab fa-whatsapp text-emerald-500 text-3xl"></i> إرسال عبر واتساب
                        </h2>
                        <button onclick="window.closeWhatsAppModal()"
                            class="text-slate-400 hover:text-slate-600 transition">
                            <i class="fas fa-times text-xl"></i>
                        </button>
                    </div>

                    <div class="space-y-6 flex-1 overflow-y-auto pr-1">
                        <div class="grid grid-cols-2 gap-4">
                            <div>
                                <label class="block text-[10px] font-black text-slate-400 uppercase mb-2 mr-2">اسم
                                    الزبون</label>
                                <input type="text" id="waCustomerName" placeholder="سيتم جلبه تلقائياً إن وجد"
                                    class="w-full border-2 border-slate-100 rounded-2xl p-4 text-sm font-bold outline-none focus:border-emerald-500 transition-all">
                            </div>
                            <div>
                                <label class="block text-[10px] font-black text-slate-400 uppercase mb-2 mr-2">رقم
                                    الهاتف</label>
                                <input type="text" id="waCustomerPhone" placeholder="مثال: 0612345678"
                                    class="w-full border-2 border-slate-100 rounded-2xl p-4 text-sm font-bold outline-none focus:border-emerald-500 transition-all text-left"
                                    dir="ltr">
                            </div>
                        </div>

                        <div class="flex-1">
                            <label class="block text-[10px] font-black text-slate-400 uppercase mb-2 mr-2">نص
                                الرسالة</label>
                            <textarea id="waMessageBody" placeholder="اكتب رسالتك هنا أو اختر قالباً..."
                                class="w-full border-2 border-slate-100 rounded-2xl p-4 text-sm font-bold outline-none focus:border-emerald-500 transition-all h-48 resize-none"></textarea>
                        </div>
                    </div>

                    <div class="flex gap-4 pt-6 mt-auto border-t border-slate-100">
                        <button onclick="window.sendWhatsAppMessage()"
                            class="flex-1 bg-emerald-500 text-white py-4 rounded-2xl font-black shadow-lg shadow-emerald-100 hover:bg-emerald-600 transition flex items-center justify-center gap-3">
                            <i class="fab fa-whatsapp text-xl"></i> إرسال الآن
                        </button>
                        <button onclick="window.closeWhatsAppModal()"
                            class="px-8 bg-slate-100 text-slate-500 py-4 rounded-2xl font-black hover:bg-slate-200 transition">إلغاء</button>
                    </div>
                </main>
            </div>
        </div>
        <div id="customerListPage" class="flex-1 flex flex-col bg-slate-50 overflow-hidden hidden-view p-8">
            <div class="flex flex-col md:flex-row justify-between items-center gap-6 mb-10">
                <div class="flex items-center gap-4">
                    <div class="bg-customers/10 text-customers p-4 rounded-2xl">
                        <i class="fas fa-history text-2xl"></i>
                    </div>
                    <div>
                        <h2 class="text-2xl font-black text-slate-800">أرشيف الوثائق الذكية</h2>
                        <p class="text-[10px] font-bold text-slate-400 mt-1">إدارة وبحث السجلات المؤرشفة</p>
                    </div>
                </div>

                <div class="flex-1 max-w-xl relative group">
                    <i
                        class="fas fa-search absolute right-5 top-1/2 -translate-y-1/2 text-primary text-xl transition-all group-focus-within:scale-110"></i>
                    <input type="text" id="recordSearch" oninput="window.renderCustomerList(this.value)"
                        placeholder="البحث بالاسم الكامل أو رقم البطاقة الوطنية..."
                        class="w-full bg-white border-4 border-primary/20 rounded-[2rem] py-5 pr-14 pl-6 text-lg font-black text-slate-800 outline-none focus:border-primary focus:ring-8 focus:ring-primary/10 transition-all shadow-xl placeholder:text-slate-300">
                </div>

                <div class="flex gap-3">
                    <button onclick="window.fetchRecordsFromFirebase(true)"
                        class="bg-emerald-500 text-white px-8 py-3 rounded-2xl font-black hover:bg-emerald-600 transition shadow-lg shadow-emerald-200 flex items-center gap-2">
                        <i class="fas fa-sync-alt"></i> مزامنة السحاب
                    </button>
                    <button onclick="window.exitCustomerList()"
                        class="bg-primary text-white px-8 py-3 rounded-2xl font-black hover:bg-blue-700 transition shadow-lg shadow-blue-200">الرجوع
                        للرئيسية</button>
                </div>
            </div>
            <div class="flex-1 overflow-y-auto custom-scrollbar px-2">
                <div class="bg-white rounded-3xl shadow-sm border border-slate-100 overflow-hidden mb-10">
                    <div
                        class="grid grid-cols-5 gap-4 px-8 py-4 bg-slate-100/50 border-b border-slate-100 text-[11px] font-black text-slate-500 uppercase tracking-widest">
                        <div class="col-span-1">الاسم الكامل</div>
                        <div class="col-span-1 text-center">رقم البطاقة</div>
                        <div class="col-span-1 text-center">نوع النموذج</div>
                        <div class="col-span-1 text-center">التاريخ والوقت</div>
                        <div class="col-span-1 text-left">إجراءات</div>
                    </div>
                    <div id="customerRecordsGrid" class="divide-y divide-slate-50"></div>
                </div>
            </div>
        </div>

        <!-- المكتبة -->
        <div id="libraryPage" class="flex-1 flex flex-col bg-slate-50 overflow-hidden hidden-view p-8">
            <div class="flex flex-col md:flex-row justify-between items-center gap-6 mb-6">
                <div class="flex items-center gap-4">
                    <div class="bg-library/10 text-library p-4 rounded-2xl">
                        <i class="fas fa-book-open text-2xl"></i>
                    </div>
                    <div>
                        <h2 class="text-2xl font-black text-slate-800">مكتبة النماذج</h2>
                        <p class="text-[10px] font-bold text-slate-400 mt-1">تصفح واختيار القوالب المتاحة</p>
                    </div>
                </div>

                <div class="flex-1 max-w-lg relative group">
                    <i
                        class="fas fa-search absolute right-5 top-1/2 -translate-y-1/2 text-library text-lg transition-all group-focus-within:scale-110"></i>
                    <input type="text" id="librarySearch" oninput="window.renderLibrary(this.value)"
                        placeholder="ابحث عن نموذج..."
                        class="w-full bg-white border-2 border-library/20 rounded-[2rem] py-4 pr-12 pl-5 text-sm font-black text-slate-800 outline-none focus:border-library focus:ring-4 focus:ring-library/10 transition-all shadow-md placeholder:text-slate-300">
                </div>

                <button onclick="window.exitLibrary()"
                    class="bg-primary text-white px-8 py-3 rounded-2xl font-black hover:bg-blue-700 transition shadow-lg shadow-blue-200">الرجوع
                    للرئيسية</button>
            </div>
            <div id="libraryContainer" class="flex-1 overflow-y-auto custom-scrollbar px-2"></div>
        </div>

        <!-- الإحصائيات -->
        <div id="statsPage" class="flex-1 flex flex-col bg-slate-50 overflow-hidden hidden-view p-8">
            <div class="flex flex-col md:flex-row justify-between items-center gap-6 mb-10">
                <div class="flex items-center gap-4">
                    <div class="bg-indigo-100 text-indigo-600 p-4 rounded-2xl">
                        <i class="fas fa-chart-line text-2xl"></i>
                    </div>
                    <div>
                        <h2 class="text-2xl font-black text-slate-800">لوحة الإحصائيات الذكية</h2>
                        <p class="text-[10px] font-bold text-slate-400 mt-1">تحليل أداء الإنتاج ومؤشرات النمو</p>
                    </div>
                </div>
                <button onclick="window.exitStats()"
                    class="bg-primary text-white px-8 py-3 rounded-2xl font-black hover:bg-blue-700 transition shadow-lg shadow-blue-200">الرجوع
                    للرئيسية</button>
            </div>

            <div class="flex-1 overflow-y-auto custom-scrollbar">
                <!-- KPI Cards -->
                <div class="grid grid-cols-1 md:grid-cols-4 gap-6 mb-8">
                    <div
                        class="bg-white p-6 rounded-3xl shadow-sm border border-slate-100 flex flex-col items-center text-center">
                        <span class="text-[10px] font-black text-slate-400 uppercase mb-2">إجمالي اليوم</span>
                        <span id="stat-todayCount" class="text-3xl font-black text-indigo-600">0</span>
                        <span class="text-[9px] font-bold text-slate-400 mt-1">وثيقة منشأة</span>
                    </div>
                    <div
                        class="bg-white p-6 rounded-3xl shadow-sm border border-slate-100 flex flex-col items-center text-center">
                        <span class="text-[10px] font-black text-slate-400 uppercase mb-2">إجمالي هذا الشهر</span>
                        <span id="stat-monthCount" class="text-3xl font-black text-primary">0</span>
                        <div id="stat-growth" class="flex items-center gap-1 mt-1">
                            <i class="fas fa-arrow-up text-[9px]"></i>
                            <span class="text-[10px] font-black">0%</span>
                        </div>
                    </div>
                    <div
                        class="bg-white p-6 rounded-3xl shadow-sm border border-slate-100 flex flex-col items-center text-center">
                        <span class="text-[10px] font-black text-slate-400 uppercase mb-2">متوسط الإنتاج اليومي</span>
                        <span id="stat-avgDaily" class="text-3xl font-black text-success">0</span>
                        <span class="text-[9px] font-bold text-slate-400 mt-1">وثيقة / يوم</span>
                    </div>
                    <div
                        class="bg-white p-6 rounded-3xl shadow-sm border border-slate-100 flex flex-col items-center text-center">
                        <span class="text-[10px] font-black text-slate-400 uppercase mb-2">أكثر القوالب استخداماً</span>
                        <span id="stat-topTemplate"
                            class="text-[13px] font-black text-slate-700 line-clamp-1 h-11 flex items-center">---</span>
                    </div>
                </div>

                <!-- Charts Row 1 -->
                <div class="grid grid-cols-1 lg:grid-cols-2 gap-8 mb-8">
                    <div class="bg-white p-8 rounded-3xl shadow-sm border border-slate-100 h-[400px] flex flex-col">
                        <h4 class="font-black text-slate-800 mb-6 text-sm">نمو الإنتاج الشهر الحالي</h4>
                        <div class="flex-1 relative">
                            <canvas id="dailyGrowthChart"></canvas>
                        </div>
                    </div>
                    <div class="bg-white p-8 rounded-3xl shadow-sm border border-slate-100 h-[400px] flex flex-col">
                        <h4 class="font-black text-slate-800 mb-6 text-sm">توزيع الوثائق حسب القالب</h4>
                        <div class="flex-1 relative">
                            <canvas id="templateDistChart"></canvas>
                        </div>
                    </div>
                </div>

                <!-- Details List -->
                <div class="bg-white rounded-3xl shadow-sm border border-slate-100 overflow-hidden mb-8">
                    <div class="px-8 py-5 bg-slate-50 border-b border-slate-100 flex justify-between items-center">
                        <h4 class="font-black text-slate-800 text-sm">تحليل ساعات الذروة وأيام العمل</h4>
                        <span class="text-[10px] font-bold text-slate-400 bg-white px-3 py-1 rounded-full border">آخر 30
                            يوم</span>
                    </div>
                    <div id="statsAnalysisGrid" class="p-8 grid grid-cols-1 md:grid-cols-2 gap-12">
                        <!-- Content via JS -->
                    </div>
                </div>
            </div>
        </div>
    </div>

    <!-- Firebase SDK (Modular) -->
    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getFirestore, collection, addDoc, getDocs, doc, updateDoc, deleteDoc, serverTimestamp, query, orderBy, where } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js";

        const firebaseConfig = {
            apiKey: "AIzaSyBGhgc5Rc2emY6W1xcP3W-bTuBu7KXsM9I",
            authDomain: "chorohat-dc1c8.firebaseapp.com",
            projectId: "chorohat-dc1c8",
            storageBucket: "chorohat-dc1c8.firebasestorage.app",
            messagingSenderId: "859507000702",
            appId: "1:859507000702:web:84c2d322cb8cb8ef751f2f"
        };

        const app = initializeApp(firebaseConfig);
        window.db = getFirestore(app);
        window.fs = { collection, addDoc, getDocs, doc, updateDoc, deleteDoc, serverTimestamp, query, orderBy, where };

        // يتم استدعاء المزامنة عند اكتمال تحميل الصفحة بالكامل لضمان جاهزية كافة المتغيرات
        window.addEventListener('load', () => {
            console.log("Page loaded - Initializing Firebase sync...");
            if (window.fetchTemplatesFromFirebase) window.fetchTemplatesFromFirebase();
            if (window.fetchRecordsFromFirebase) window.fetchRecordsFromFirebase();
        });
    </script>

    <script>
        // --- الإعدادات والمزامنة ---
        let appData = JSON.parse(localStorage.getItem('hybrid_docs_v36')) || [];
        let archive = JSON.parse(localStorage.getItem('hybrid_archive_v36')) || [];
        let settings = JSON.parse(localStorage.getItem('hybrid_settings_v36')) || {
            ui: { darkMode: false, accentColor: "#2563eb", fontFamily: "'Cairo', sans-serif" },
            pdf: { pageSize: "A4", margin: 20, showDate: false },
            word: { forceRTL: true },
            firebase: { autoSync: true },
            security: { password: "1988" }
        };

        window.sessionModIds = JSON.parse(localStorage.getItem('hybrid_session_ids_v36')) || [];
        window.activeModId = localStorage.getItem('hybrid_active_id_v36') || null;

        function syncLocalStorage() {
            localStorage.setItem('hybrid_docs_v36', JSON.stringify(appData));
            localStorage.setItem('hybrid_archive_v36', JSON.stringify(archive));
            localStorage.setItem('hybrid_settings_v36', JSON.stringify(settings));
            localStorage.setItem('hybrid_session_ids_v36', JSON.stringify(window.sessionModIds));
            if (window.activeModId) localStorage.setItem('hybrid_active_id_v36', window.activeModId);
        }

        window.applySettings = function () {
            // تطبيق الوضع الليلي
            if (settings.ui.darkMode) document.body.classList.add('dark-mode');
            else document.body.classList.remove('dark-mode');

            // تطبيق لون السمة
            document.documentElement.style.setProperty('--primary-color', settings.ui.accentColor);

            // تطبيق الخط
            document.body.style.fontFamily = settings.ui.fontFamily;

            // تحديث عناصر التحكم في النافذة
            const dm = document.getElementById('set-darkMode'); if (dm) dm.checked = settings.ui.darkMode;
            const ac = document.getElementById('set-accentColor'); if (ac) ac.value = settings.ui.accentColor;
            const ff = document.getElementById('set-fontFamily'); if (ff) ff.value = settings.ui.fontFamily;
            const ps = document.getElementById('set-pdfPageSize'); if (ps) ps.value = settings.pdf.pageSize;
            const pm = document.getElementById('set-pdfMargin'); if (pm) pm.value = settings.pdf.margin;
            const sd = document.getElementById('set-pdfShowDate'); if (sd) sd.checked = settings.pdf.showDate;
            const wr = document.getElementById('set-wordForceRTL'); if (wr) wr.checked = settings.word.forceRTL;
            const as = document.getElementById('set-autoSync'); if (as) as.checked = settings.firebase.autoSync;
        };

        window.openSettings = function () {
            console.log("Opening settings...");
            window.applySettings();
            const modal = document.getElementById('settingsModal');
            if (modal) {
                modal.classList.add('active');
            } else {
                console.error("settingsModal not found!");
            }
        };

        window.closeSettings = function () {
            document.getElementById('settingsModal').classList.remove('active');
            syncLocalStorage();
            showToast("تم حفظ الإعدادات بنجاح");
        };

        window.switchSettingsTab = function (tabId) {
            document.querySelectorAll('.settings-tab-btn').forEach(btn => {
                btn.classList.toggle('active', btn.getAttribute('data-tab') === tabId);
            });
            document.querySelectorAll('.settings-section').forEach(sec => {
                sec.classList.toggle('active', sec.id === `tab-${tabId}`);
            });
        };

        window.updateSetting = function (group, key, val) {
            if (!settings[group]) settings[group] = {};
            settings[group][key] = val;
            window.applySettings();
            syncLocalStorage();
        };

        window.changePassword = function () {
            const current = document.getElementById('currentPass').value;
            const newP = document.getElementById('newPass').value;
            const confirmP = document.getElementById('confirmPass').value;

            if (current !== (settings.security ? settings.security.password : "1988")) {
                showToast("كلمة المرور الحالية غير صحيحة", "error");
                return;
            }
            if (!newP) {
                showToast("يرجى إدخال كلمة مرور جديدة", "error");
                return;
            }
            if (newP !== confirmP) {
                showToast("كلمة المرور الجديدة غير متطابقة", "error");
                return;
            }

            if (!settings.security) settings.security = {};
            settings.security.password = newP;
            syncLocalStorage();
            showToast("تم تغيير كلمة المرور بنجاح ✓");

            // تفريغ الحقول
            document.getElementById('currentPass').value = "";
            document.getElementById('newPass').value = "";
            document.getElementById('confirmPass').value = "";
        };

        window.verifyActionPassword = function () {
            const pass = prompt("يرجى إدخال كلمة المرور لتأكيد العملية:");
            const savedPass = (settings.security && settings.security.password) ? settings.security.password : "1988";
            if (pass === savedPass) return true;
            if (pass !== null) showToast("كلمة المرور غير صحيحة", "error");
            return false;
        };

        window.clearAllLocalData = function () {
            if (!confirm("تحذير: سيتم مسح كافة البيانات والنماذج المخزنة محلياً! هل أنت متأكد؟")) return;
            if (!window.verifyActionPassword()) return;
            localStorage.clear();
            location.reload();
        };

        window.addEventListener('DOMContentLoaded', () => {
            window.applySettings();
            if (window.sessionModIds && window.sessionModIds.length > 0) {
                window.renderMainView();
                // تحديث اسم النموذج في الهيدر
                const m = getModelById(window.activeModId);
                if (m) {
                    const disp = document.getElementById('activeModelNameDisp');
                    if (disp) disp.innerText = m.name;
                    const btn = document.getElementById('editCurrentModel');
                    if (btn) btn.classList.remove('opacity-50');
                }
            }
        });

        // --- محرك الصور والسحب ---
        let activeEl = null, isDrag = false, isRes = false, sx, sy, sw, sh, sl, st;

        function initImgControl(container) {
            if (container.querySelector('.resizer')) return;
            const resizer = document.createElement('div');
            resizer.className = "resizer no-print";
            container.appendChild(resizer);

            const startAction = (e) => {
                activeEl = container;
                const touch = e.type === 'touchstart' ? e.touches[0] : e;
                sx = touch.clientX; sy = touch.clientY;
                sl = parseInt(window.getComputedStyle(container).left, 10) || 0;
                st = parseInt(window.getComputedStyle(container).top, 10) || 0;
                sw = parseInt(window.getComputedStyle(container).width, 10);
                sh = parseInt(window.getComputedStyle(container).height, 10);
            };

            container.addEventListener('mousedown', function (e) {
                if (e.target === resizer) return;
                isDrag = true; startAction(e);
                e.preventDefault();
            });
            container.addEventListener('touchstart', (e) => {
                if (e.target === resizer) return;
                isDrag = true; startAction(e);
            }, { passive: false });

            resizer.addEventListener('mousedown', function (e) {
                isRes = true; startAction(e);
                e.preventDefault(); e.stopPropagation();
            });
            resizer.addEventListener('touchstart', (e) => {
                isRes = true; startAction(e);
                e.stopPropagation();
            }, { passive: false });
        }

        const handleMove = (e) => {
            if (!activeEl) return;
            const touch = e.type === 'touchmove' ? e.touches[0] : e;
            const dx = touch.clientX - sx;
            const dy = touch.clientY - sy;

            if (isDrag) {
                activeEl.style.left = (sl + dx) + 'px';
                activeEl.style.top = (st + dy) + 'px';
            }
            if (isRes) {
                activeEl.style.width = Math.max(20, sw + dx) + 'px';
                activeEl.style.height = Math.max(20, sh + dy) + 'px';
            }
        };

        document.addEventListener('mousemove', handleMove);
        document.addEventListener('touchmove', handleMove, { passive: false });

        const stopAction = () => {
            if (activeEl) {
                isDrag = false; isRes = false; activeEl = null;
                window.syncMainToModel();
            }
        };

        document.addEventListener('mouseup', stopAction);
        document.addEventListener('touchend', stopAction);

        // --- الملاحة والوظائف ---
        window.doCommand = function (cmd, val = null) {
            const isDesign = !document.getElementById('designPage').classList.contains('hidden-view');
            const targetId = isDesign ? 'designA4' : 'mainA4';
            const target = document.getElementById(targetId);
            if (target) {
                target.focus();
                document.execCommand(cmd, false, val);
            }
        };

        window.pickColor = function (mode) {
            window.activeColorMode = mode;
            document.getElementById('nativeColorPicker').click();
        };

        window.applyColor = function (c) {
            window.doCommand(window.activeColorMode, c);
        };

        window.changeDir = function (d) {
            const sel = window.getSelection();
            if (sel.rangeCount) {
                let node = sel.getRangeAt(0).commonAncestorContainer;
                if (node.nodeType === 3) node = node.parentElement;
                const p = node.closest('div, p, h1, h2, li');
                if (p) {
                    p.style.direction = d;
                    p.style.textAlign = (d === 'rtl' ? 'right' : 'left');
                }
            }
        };

        window.clearSessionFields = function () {
            if (!confirm("هل أنت متأكد من مسح كافة المعلومات المدخلة في الجبسة الحالية؟")) return;

            // مسح كافة القيم في حقول البيانات المشتركة
            appData.forEach(c => {
                c.models.forEach(m => {
                    m.variables.forEach(v => {
                        v.currentValue = "";
                    });
                });
            });

            syncLocalStorage();
            window.renderMainView();
            showToast("تم مسح كافة الحقول بنجاح 🧹");
        };

        window.renderMainView = function () {
            const container = document.getElementById('mainSessionContainer');
            const fieldsCont = document.getElementById('fillFieldsContainer');
            const sessionListCont = document.getElementById('activeSessionModels');

            if (!window.sessionModIds || window.sessionModIds.length === 0) {
                container.innerHTML = `<div class="text-center py-20 text-slate-400 font-bold"><i class="fas fa-file-alt text-6xl mb-4 block"></i>لا توجد نماذج نشطة حالياً. اختر نموذجاً واضفه للجلسة.</div>`;
                fieldsCont.innerHTML = "";
                sessionListCont.innerHTML = "";
                return;
            }

            // 1. عرض علامات النماذج النشطة (Session Badges)
            sessionListCont.innerHTML = window.sessionModIds.map(mid => {
                const m = getModelById(mid);
                return `
                    <div class="bg-primary/10 text-primary px-3 py-1 rounded-full text-[10px] font-black flex items-center gap-2 border border-primary/20">
                        ${m ? m.name : 'قالب'}
                        <button onclick="window.removeModelFromSession('${mid}')" class="hover:text-danger"><i class="fas fa-times"></i></button>
                    </div>`;
            }).join('');

            // 2. تجميع كافة المتغيرات الفريدة من كافة النماذج النشطة (بناءً على التسمية Label)
            const allVarsMap = new Map();
            window.sessionModIds.forEach(mid => {
                const m = getModelById(mid);
                if (m) {
                    m.variables.forEach(v => {
                        if (!allVarsMap.has(v.label)) {
                            allVarsMap.set(v.label, { label: v.label, currentValue: v.currentValue });
                        }
                    });
                }
            });

            fieldsCont.innerHTML = `
                <div class="flex justify-between items-center border-b border-blue-50 pb-2 mb-4">
                    <h4 class="font-black text-primary text-xs">حقول تعبئة الجلسة المتعددة</h4>
                    <button onclick="window.clearSessionFields()" class="text-[9px] font-black text-danger hover:bg-red-50 px-2 py-1 rounded-lg transition border border-red-50 flex items-center gap-1">
                        <i class="fas fa-eraser"></i> مسح الحقول
                    </button>
                </div>`;

            allVarsMap.forEach((vData, label) => {
                const isDate = label.includes("تاريخ") || label.includes("التاريخ") || label.toLowerCase().includes("date");
                let initialVal = vData.currentValue || '';

                // تعبئة التاريخ تلقائياً إذا كان فارغاً
                if (isDate && !initialVal) {
                    initialVal = new Date().toISOString().split('T')[0];
                    vData.currentValue = initialVal; // تحديث القيمة في الخريطة ليتم عكسها على المستند لاحقاً
                }

                const div = document.createElement('div');
                div.className = "flex flex-col gap-1";
                div.innerHTML = `
                    <label class="text-[11px] font-bold text-slate-500 mr-1">${label}</label>
                    <input type="${isDate ? 'date' : 'text'}" 
                        value="${initialVal}" 
                        oninput="window.updateGlobalText('${label}', this.value, this)" 
                        onkeydown="window.handleFieldNavigation(event)"
                        class="w-full border-2 border-slate-100 rounded-xl p-3 text-sm font-bold focus:border-primary outline-none transition-all">`;
                fieldsCont.appendChild(div);
            });

            // 3. عرض كافة النماذج في ساحة العمل
            container.innerHTML = window.sessionModIds.map(mid => {
                const m = getModelById(mid);
                if (!m) return "";
                return `
                    <div class="pages-view w-full flex justify-center relative group" data-mid="${mid}">
                        <!-- زر حذف الوثيقة -->
                        <button onclick="window.removeModelFromSession('${mid}')" 
                            class="absolute top-2 right-[calc(50%-120mm)] z-10 w-10 h-10 bg-white/80 backdrop-blur-md text-danger rounded-full shadow-lg border border-red-50 opacity-0 group-hover:opacity-100 transition-all flex items-center justify-center hover:bg-danger hover:text-white" title="إزالة هذه الوثيقة">
                            <i class="fas fa-trash-alt"></i>
                        </button>
                        
                        <div class="a4-paper relative">
                            <div class="absolute -top-10 left-0 bg-white shadow-sm border px-4 py-1 rounded-t-xl text-[10px] font-black text-slate-400 no-print">نموذج: ${m.name}</div>
                            <div id="content-${mid}" class="a4-content outline-none" contenteditable="true" oninput="window.syncMultiToModel('${mid}')">
                                ${m.htmlContent}
                            </div>
                        </div>
                    </div>`;
            }).join('');

            // تحديث العرض الأولي للنصوص المتغيرة في كل الأوراق النشطة
            allVarsMap.forEach((vData, label) => {
                window.updateGlobalText(label, vData.currentValue || "");
            });

            document.querySelectorAll('.img-container').forEach(c => initImgControl(c));
        };

        window.updateGlobalText = function (label, val, inputEl = null) {
            let finalVal = val;
            const isCIN = label.includes("بطاقة") || label.includes("هوية") || label.toUpperCase().includes("CIN");

            if (isCIN) {
                // قاموس تحويل الأحرف من العربية إلى اللاتينية (تخطيط لوحة المفاتيح)
                const arToLatMap = {
                    'ض': 'Q', 'ص': 'W', 'ث': 'E', 'ق': 'R', 'ف': 'T', 'غ': 'Y', 'ع': 'U', 'ه': 'I', 'خ': 'O', 'ح': 'P',
                    'ش': 'A', 'س': 'S', 'ي': 'D', 'ب': 'F', 'ل': 'G', 'ا': 'H', 'ت': 'J', 'ن': 'K', 'م': 'L',
                    'ئ': 'Z', 'ء': 'X', 'ؤ': 'C', 'ر': 'V', 'لأ': 'B', 'لآ': 'B', 'لأ': 'B', 'لا': 'B', 'ى': 'N', 'ة': 'M',
                    '٠': '0', '١': '1', '٢': '2', '٣': '3', '٤': '4', '٥': '5', '٦': '6', '٧': '7', '٨': '8', '٩': '9'
                };

                let transliterated = "";
                for (let char of val) {
                    transliterated += arToLatMap[char] || char;
                }

                finalVal = transliterated.replace(/[^a-zA-Z0-9]/g, '').toUpperCase();
                if (inputEl) inputEl.value = finalVal;
            }

            // تحديث كافة المتغيرات في appData التي تحمل نفس التسمية (Shared Data Sync)
            appData.forEach(c => {
                c.models.forEach(m => {
                    m.variables.forEach(v => {
                        if (v.label === label) {
                            v.currentValue = finalVal;
                            const contentEl = document.getElementById(`content-${m.id}`);
                            if (contentEl) {
                                let dV = finalVal; if (label.includes("التاريخ") && finalVal) dV = finalVal.replace(/-/g, '/');
                                contentEl.querySelectorAll(`[data-var-id="${v.id}"]`).forEach(el => el.innerText = dV || v.original);
                            }
                        }
                    });
                });
            });

            // ملء تلقائي عند إدخال رقم البطاقة الوطنية
            if (isCIN) {
                const searchVal = finalVal.trim();
                if (searchVal.length >= 4) {
                    const lastRecord = archive.find(r => (r.nationalId || "").toUpperCase() === searchVal);
                    if (lastRecord && lastRecord.data) {
                        const fillContainer = document.getElementById('fillFieldsContainer');
                        Object.keys(lastRecord.data).forEach(sharedLabel => {
                            if (sharedLabel === label) return;
                            const newVal = lastRecord.data[sharedLabel] || '';

                            // تحديث appData والأوراق مباشرة
                            appData.forEach(c => {
                                c.models.forEach(m => {
                                    m.variables.forEach(v => {
                                        if (v.label === sharedLabel) {
                                            v.currentValue = newVal;
                                            const contentEl = document.getElementById(`content-${m.id}`);
                                            if (contentEl) {
                                                let dV = newVal;
                                                if (sharedLabel.includes("التاريخ") && newVal) dV = newVal.replace(/-/g, '/');
                                                contentEl.querySelectorAll(`[data-var-id="${v.id}"]`).forEach(el => el.innerText = dV || v.original);
                                            }
                                        }
                                    });
                                });
                            });

                            // تحديث حقل الإدخال في الشريط الجانبي مباشرة
                            if (fillContainer) {
                                fillContainer.querySelectorAll('input').forEach(input => {
                                    const oninput = input.getAttribute('oninput') || '';
                                    if (oninput.includes(`'${sharedLabel}'`) || oninput.includes(`"${sharedLabel}"`)) {
                                        input.value = newVal;
                                    }
                                });
                            }
                        });
                        window.showToast("تم ملء البيانات تلقائياً من الأرشيف ✓");
                    }
                }
            }

            window.syncLocalStorage();
        };

        window.handleFieldNavigation = function (e) {
            if (e.key === 'ArrowDown' || e.key === 'ArrowUp') {
                const inputs = Array.from(document.getElementById('fillFieldsContainer').querySelectorAll('input'));
                const index = inputs.indexOf(e.target);
                if (index === -1) return;

                if (e.key === 'ArrowDown' && index < inputs.length - 1) {
                    inputs[index + 1].focus();
                    e.preventDefault();
                } else if (e.key === 'ArrowUp' && index > 0) {
                    inputs[index - 1].focus();
                    e.preventDefault();
                }
            }
        };

        window.handleModelSelection = function () {
            const mId = document.getElementById('mainModelSelect').value;
            if (mId) {
                if (!window.sessionModIds || window.sessionModIds.length === 0) {
                    window.sessionModIds = [mId];
                    window.renderMainView();
                }
            }
        };

        window.addModelToSession = function () {
            const mId = document.getElementById('mainModelSelect').value;
            if (!mId) return showToast("اختر قالباً أولاً");
            if (window.sessionModIds.includes(mId)) return showToast("هذا القالب موجود بالفعل في الجلسة");

            window.sessionModIds.push(mId);
            window.renderMainView();
            showToast("تم إضافة القالب للجلسة");
        };

        window.removeModelFromSession = function (mid) {
            window.sessionModIds = window.sessionModIds.filter(id => id !== mid);
            window.renderMainView();
        };

        window.syncMultiToModel = function (mid) {
            const content = document.getElementById(`content-${mid}`).innerHTML;
            const m = getModelById(mid);
            if (m) {
                m.htmlContent = content;
                window.syncLocalStorage();
            }
        };

        function getModelById(id) {
            const idStr = String(id);
            for (let c of appData) {
                const m = c.models.find(x => String(x.id) === idStr);
                if (m) return m;
            }
            return null;
        }

        window.syncMainToModel = function () {
            // هذه الوظيفة كانت مستخدمة للنموذج المفرد، الآن نستخدم syncMultiToModel لكل نموذج
        };


        window.goToCustomerList = function () {
            document.getElementById('mainPage').classList.add('hidden-view');
            document.getElementById('designPage').classList.add('hidden-view');
            document.getElementById('libraryPage').classList.add('hidden-view');
            document.getElementById('customerListPage').classList.remove('hidden-view');
            window.renderCustomerList();
        };

        window.exitCustomerList = function () {
            document.getElementById('customerListPage').classList.add('hidden-view');
            document.getElementById('mainPage').classList.remove('hidden-view');
            // تأكد من تحديث العرض الرئيسي عند العودة
            window.renderMainView();
        };

        window.renderCustomerList = function (query = "") {
            const grid = document.getElementById('customerRecordsGrid');

            const q = query.toLowerCase();
            const filtered = archive.filter(r =>
                (r.customerName || "").toLowerCase().includes(q) ||
                (r.nationalId || "").toLowerCase().includes(q) ||
                (r.modelName || "").toLowerCase().includes(q)
            );

            grid.innerHTML = filtered.map((r, i) => {
                const latinDate = r.readableDate.replace(/[٠-٩]/g, d => "٠١٢٣٤٥٦٧٨٩".indexOf(d));
                // ألوان بارزة لكل صف
                const rowColors = ['border-blue-500', 'border-purple-500', 'border-emerald-500', 'border-pink-500', 'border-amber-500'];
                const accentColor = rowColors[i % rowColors.length];

                return `
                <div class="grid grid-cols-5 gap-4 px-8 py-6 bg-white border-r-8 ${accentColor} hover:bg-slate-50 transition-all cursor-pointer group items-center mb-3 rounded-2xl shadow-sm hover:shadow-md" onclick="window.loadRecord('${r.id}')">
                    <div class="col-span-1 flex flex-col">
                        <span class="text-[10px] font-black text-slate-400 uppercase mb-1">الاسم الكامل</span>
                        <span class="font-black text-slate-800 text-base">${r.customerName}</span>
                    </div>
                    <div class="col-span-1 text-center flex flex-col">
                        <span class="text-[10px] font-black text-slate-400 uppercase mb-1">رقم البطاقة</span>
                        <span class="font-black text-primary text-sm bg-blue-50 py-1 rounded-lg">${r.nationalId}</span>
                    </div>
                    <div class="col-span-1 text-center flex flex-col items-center">
                        <span class="text-[10px] font-black text-slate-400 uppercase mb-1">نوع المنتج</span>
                        <span class="bg-dark text-white px-4 py-1 rounded-full text-[10px] font-black tracking-wide">${r.modelName}</span>
                    </div>
                    <div class="col-span-1 text-center flex flex-col">
                        <span class="text-[10px] font-black text-slate-400 uppercase mb-1">تاريخ الحفظ</span>
                        <span class="font-bold text-slate-500 text-[11px]">${latinDate}</span>
                    </div>
                    <div class="col-span-1 flex justify-end gap-3">
                        <button onclick="event.stopPropagation(); window.deleteRecord('${r.id}')" class="bg-red-50 text-danger p-3 rounded-xl hover:bg-danger hover:text-white transition-all shadow-sm">
                            <i class="fas fa-trash-alt"></i>
                        </button>
                        <div class="bg-primary/10 text-primary p-3 rounded-xl group-hover:bg-primary group-hover:text-white transition-all shadow-sm">
                             <i class="fas fa-external-link-alt"></i>
                        </div>
                    </div>
                </div>`;
            }).reverse().join('');
        };

        window.deleteRecord = async function (rid) {
            if (!confirm("هل أنت متأكد من حذف هذا السجل نهائياً؟")) return;
            if (!window.verifyActionPassword()) return;

            const ridStr = String(rid);
            const idx = archive.findIndex(r => String(r.id) === ridStr);
            if (idx === -1) return showToast("لم يتم العثور على السجل محلياً");

            archive.splice(idx, 1);
            syncLocalStorage();
            window.renderCustomerList(document.getElementById('recordSearch').value);

            try {
                console.log("Searching for cloud record with ID:", ridStr);
                // محاولة البحث عن السجل كـ String أولاً، ثم كـ Number احتياطاً للتوافق
                let q = window.fs.query(window.fs.collection(window.db, "customerRecords"), window.fs.where("id", "==", ridStr));
                let qs = await window.fs.getDocs(q);

                if (qs.empty && !isNaN(ridStr)) {
                    console.log("Record not found as string, trying as number...");
                    q = window.fs.query(window.fs.collection(window.db, "customerRecords"), window.fs.where("id", "==", Number(ridStr)));
                    qs = await window.fs.getDocs(q);
                }

                if (qs.empty) {
                    console.warn("Cloud record not found.");
                    showToast("تم الحذف محلياً فقط (لم يتم العثور عليه في السحاب)");
                } else {
                    let deleteCount = 0;
                    for (const d of qs.docs) {
                        await window.fs.deleteDoc(window.fs.doc(window.db, "customerRecords", d.id));
                        deleteCount++;
                    }
                    console.log(`Deleted ${deleteCount} records from cloud.`);
                    showToast("تم الحذف من السحابة بنجاح ✓");
                }
            } catch (e) {
                console.error("Delete Firebase Error", e);
                showToast("خطأ في الحذف من السحاب: تحقق من الاتصال", "error");
            }
        };

        window.loadRecord = function (rid) {
            const rec = archive.find(r => String(r.id) === String(rid));
            if (!rec) return showToast("لم يتم العثور على السجل");

            // البحث عن النموذج المرتبط بالسجل
            let model = null;
            for (const c of appData) {
                const found = c.models.find(m => String(m.id) === String(rec.modelId));
                if (found) { model = found; break; }
            }

            if (!model) {
                return showToast("النموذج المرتبط بهذا السجل غير موجود في المكتبة");
            }

            // استعادة قيم المتغيرات من السجل إلى النموذج
            if (rec.data) {
                model.variables.forEach(v => {
                    if (rec.data[v.label] !== undefined) {
                        v.currentValue = rec.data[v.label];
                    }
                });
            }

            // تعيين النموذج كجلسة نشطة وحيدة والانتقال للصفحة الرئيسية
            window.activeModId = model.id;
            window.sessionModIds = [model.id];
            syncLocalStorage();

            // إخفاء صفحة السجلات وإظهار الصفحة الرئيسية
            document.getElementById('customerListPage').classList.add('hidden-view');
            document.getElementById('designPage').classList.add('hidden-view');
            document.getElementById('mainPage').classList.remove('hidden-view');

            window.renderMainView();
            window.showToast(`تم فتح سجل "${rec.customerName}" — ${rec.modelName} ✓`);
        };

        window.exportRecordToPDF = async function (rid) {
            const paper = document.getElementById('recordViewPaper');
            if (!paper) return;
            const rec = archive.find(r => String(r.id) === String(rid));
            try {
                const { jsPDF } = window.jspdf;
                const pdf = new jsPDF('p', 'mm', 'a4');
                const pdfWidth = pdf.internal.pageSize.getWidth();
                const canvas = await html2canvas(paper, { scale: 3, useCORS: true, backgroundColor: '#fff' });
                const imgHeight = (canvas.height * pdfWidth) / canvas.width;
                pdf.addImage(canvas.toDataURL('image/jpeg', 1.0), 'JPEG', 0, 0, pdfWidth, imgHeight);
                pdf.save(`${rec ? rec.modelName : 'سجل'}_${Date.now()}.pdf`);
                window.showToast("تم تصدير PDF بنجاح ✓");
            } catch (e) { alert("حدث خطأ أثناء التصدير"); }
        };

        window.goToLibrary = function () {
            document.getElementById('mainPage').classList.add('hidden-view');
            document.getElementById('designPage').classList.add('hidden-view');
            document.getElementById('customerListPage').classList.add('hidden-view');
            document.getElementById('libraryPage').classList.remove('hidden-view');
            window.renderLibrary();
        };

        window.exitLibrary = function () {
            document.getElementById('libraryPage').classList.add('hidden-view');
            document.getElementById('mainPage').classList.remove('hidden-view');
        };

        window.renderLibrary = function (query) {
            const cont = document.getElementById('libraryContainer');
            const q = (query || '').trim().toLowerCase();

            // تصفية البيانات حسب البحث
            const filtered = appData.map(c => ({
                ...c,
                models: q ? c.models.filter(m => m.name.toLowerCase().includes(q)) : c.models
            })).filter(c => c.models.length > 0);

            if (filtered.length === 0) {
                cont.innerHTML = `<div class="flex flex-col items-center justify-center py-24 text-center">
                    <i class="fas fa-search text-5xl text-slate-200 mb-5"></i>
                    <p class="font-black text-slate-400 text-lg">لا توجد نتائج لـ "${q}"</p>
                    <p class="text-xs text-slate-300 mt-2">جرب كلمة بحث مختلفة</p>
                </div>`;
                return;
            }

            cont.innerHTML = filtered.map((c, catIdx) => `
                <div class="mb-12">
                    <div class="flex items-center gap-3 mb-6">
                        <div class="w-1.5 h-7 bg-library rounded-full"></div>
                        <h3 class="text-lg font-black text-slate-800">${c.name}</h3>
                        <div class="h-px flex-1 bg-slate-100"></div>
                        <span class="text-[10px] font-black text-slate-400 bg-slate-50 px-3 py-1 rounded-full border border-slate-100 uppercase tracking-tighter">${c.models.length} نماذج</span>
                    </div>
                    <div class="grid grid-cols-2 sm:grid-cols-4 md:grid-cols-6 lg:grid-cols-8 xl:grid-cols-10 gap-3">
                        ${c.models.map((m, modIdx) => {
                return `
                            <div class="bg-white border-2 border-slate-50 hover:border-library/30 p-3 rounded-2xl transition-all cursor-pointer group flex flex-col items-center text-center relative hover:shadow-xl hover:-translate-y-1 shadow-sm" onclick="window.selectModel('${c.id}', '${m.id}')">
                                <button onclick="event.stopPropagation(); window.deleteModel('${c.id}', '${m.id}')" class="absolute top-2 left-2 bg-red-50 text-danger w-6 h-6 flex items-center justify-center rounded-lg opacity-0 group-hover:opacity-100 transition-all shadow-sm hover:bg-danger hover:text-white" title="حذف نموذج">
                                    <i class="fas fa-trash-alt text-[9px]"></i>
                                </button>
                                <div class="w-12 h-14 bg-slate-50 rounded-xl mb-3 flex items-center justify-center text-slate-200 group-hover:text-library transition-colors ring-4 ring-transparent group-hover:ring-library/5">
                                    <i class="fas fa-file-alt text-2xl"></i>
                                </div>
                                <h4 class="font-black text-slate-700 text-[11px] mb-1 leading-tight line-clamp-2 h-7 flex items-center justify-center">${m.name}</h4>
                                <div class="bg-slate-50 border border-slate-100 px-2 py-0.5 rounded-full group-hover:bg-blue-50 group-hover:border-blue-100 transition-colors">
                                    <span class="text-[8px] font-black text-slate-400 group-hover:text-primary transition-colors">${m.variables ? m.variables.length : 0} متغير</span>
                                </div>
                            </div>`;
            }).join('')}
                    </div>
                </div>`).join('');
        };

        window.deleteModel = async function (cid, mid) {
            if (!confirm("هل أنت متأكد من حذف هذا النموذج نهائياً؟")) return;
            if (!window.verifyActionPassword()) return;
            const cidStr = String(cid);
            const midStr = String(mid);
            const cat = appData.find(c => String(c.id) === cidStr);
            if (!cat) return;
            cat.models = cat.models.filter(m => String(m.id) !== midStr);

            // إذا أصبح القسم فارغاً، نحذفه أيضاً (اختياري، يفضل إبقاءه أو حذفه حسب رغبة المستخدم، هنا سنبقيه)
            syncLocalStorage();
            window.renderLibrary();
            window.updateMainDropdowns();

            try {
                // إذا كان المعرف من فيرليس (طويل أكثر من 15 حرف) نقوم بحذفه من السحاب فوراً
                if (typeof midStr === "string" && midStr.length > 15) {
                    await window.fs.deleteDoc(window.fs.doc(window.db, "templates", midStr));
                    showToast("تم حذف النموذج من الجهاز والسحابة");
                } else {
                    showToast("تم الحذف محلياً");
                }
            } catch (e) {
                console.warn("Firebase Delete Error", e);
                showToast("تم الحذف محلياً (حدث خطأ في المزامنة)");
            }
        };

        // --- نظام الإحصائيات ---
        let charts = {};

        window.goToStats = function () {
            document.getElementById('mainPage').classList.add('hidden-view');
            document.getElementById('customerListPage').classList.add('hidden-view');
            document.getElementById('libraryPage').classList.add('hidden-view');
            document.getElementById('statsPage').classList.remove('hidden-view');
            window.renderStats();
        };

        window.exitStats = function () {
            document.getElementById('statsPage').classList.add('hidden-view');
            document.getElementById('mainPage').classList.remove('hidden-view');
        };

        window.renderStats = function () {
            const now = new Date();
            const startOfToday = new Date(now.getFullYear(), now.getMonth(), now.getDate()).getTime();
            const startOfMonth = new Date(now.getFullYear(), now.getMonth(), 1).getTime();
            const startOfLastMonth = new Date(now.getFullYear(), now.getMonth() - 1, 1).getTime();

            // 1. حساب الأساسيات
            const todayRecords = archive.filter(r => (r.id > startOfToday));
            const monthRecords = archive.filter(r => (r.id > startOfMonth));
            const lastMonthRecords = archive.filter(r => (r.id > startOfLastMonth && r.id < startOfMonth));

            document.getElementById('stat-todayCount').innerText = todayRecords.length;
            document.getElementById('stat-monthCount').innerText = monthRecords.length;

            // حساب النمو
            const growth = lastMonthRecords.length === 0 ? 100 : Math.round(((monthRecords.length - lastMonthRecords.length) / lastMonthRecords.length) * 100);
            const growthEl = document.getElementById('stat-growth');
            growthEl.innerHTML = `
                <i class="fas fa-${growth >= 0 ? 'arrow-up' : 'arrow-down'} text-[9px]"></i>
                <span class="text-[10px] font-black">${Math.abs(growth)}%</span>
            `;
            growthEl.className = `flex items-center gap-1 mt-1 ${growth >= 0 ? 'text-success' : 'text-danger'}`;

            // متوسط يومي (آخر 30 يوم)
            const uniqueDays = new Set(archive.map(r => new Date(r.id).toDateString())).size;
            document.getElementById('stat-avgDaily').innerText = uniqueDays > 0 ? (archive.length / uniqueDays).toFixed(1) : 0;

            // أكثر القوالب استخداماً
            const templateCounts = {};
            archive.forEach(r => { templateCounts[r.modelName] = (templateCounts[r.modelName] || 0) + 1; });
            const topTemplate = Object.entries(templateCounts).sort((a, b) => b[1] - a[1])[0];
            document.getElementById('stat-topTemplate').innerText = topTemplate ? topTemplate[0] : "---";

            // 2. الرسوم البيانية
            renderDailyChart(monthRecords);
            renderTemplateChart(templateCounts);
            renderPeakAnalysis();
        };

        function renderDailyChart(monthRecords) {
            const canvas = document.getElementById('dailyGrowthChart');
            if (!canvas) return;
            if (charts.daily) charts.daily.destroy();

            const daysInMonth = new Date(new Date().getFullYear(), new Date().getMonth() + 1, 0).getDate();
            const labels = Array.from({ length: daysInMonth }, (_, i) => i + 1);
            const data = Array(daysInMonth).fill(0);

            monthRecords.forEach(r => {
                const day = new Date(r.id).getDate();
                data[day - 1]++;
            });

            charts.daily = new Chart(canvas, {
                type: 'line',
                data: {
                    labels: labels,
                    datasets: [{
                        label: 'الوثائق المنشأة',
                        data: data,
                        borderColor: '#2563eb',
                        backgroundColor: 'rgba(37, 99, 235, 0.1)',
                        fill: true,
                        tension: 0.4,
                        borderWidth: 3,
                        pointRadius: 2
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: { legend: { display: false } },
                    scales: {
                        y: { beginAtZero: true, ticks: { stepSize: 1, font: { family: 'Cairo', weight: 'bold' } } },
                        x: { grid: { display: false }, ticks: { font: { family: 'Cairo', size: 10 } } }
                    }
                }
            });
        }

        function renderTemplateChart(counts) {
            const canvas = document.getElementById('templateDistChart');
            if (!canvas) return;
            if (charts.templates) charts.templates.destroy();

            const sorted = Object.entries(counts).sort((a, b) => b[1] - a[1]).slice(0, 5);

            charts.templates = new Chart(canvas, {
                type: 'doughnut',
                data: {
                    labels: sorted.map(i => i[0]),
                    datasets: [{
                        data: sorted.map(i => i[1]),
                        backgroundColor: ['#2563eb', '#7c3aed', '#f59e0b', '#059669', '#dc2626'],
                        borderWidth: 0
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: { position: 'bottom', labels: { font: { family: 'Cairo', size: 10, weight: 'bold' }, padding: 20 } }
                    },
                    cutout: '70%'
                }
            });
        }

        function renderPeakAnalysis() {
            const grid = document.getElementById('statsAnalysisGrid');
            if (!grid) return;

            // تحليل الساعات
            const hours = Array(24).fill(0);
            archive.forEach(r => { hours[new Date(r.id).getHours()]++; });
            const peakHour = hours.indexOf(Math.max(...hours));

            // تحليل الأيام
            const dayNames = ["الأحد", "الاثنين", "الثلاثاء", "الأربعاء", "الخميس", "الجمعة", "السبت"];
            const days = Array(7).fill(0);
            archive.forEach(r => { days[new Date(r.id).getDay()]++; });
            const peakDay = days.indexOf(Math.max(...days));

            grid.innerHTML = `
                <div class="flex items-center gap-6">
                    <div class="w-16 h-16 bg-amber-50 rounded-2xl flex items-center justify-center text-amber-500 shrink-0">
                        <i class="fas fa-clock text-2xl"></i>
                    </div>
                    <div>
                        <p class="text-[10px] font-black text-slate-400 uppercase mb-1">ساعة الذروة اليومية</p>
                        <p class="text-xl font-black text-slate-700">${peakHour}:00 - ${peakHour + 1}:00</p>
                        <p class="text-[10px] font-bold text-slate-400 mt-1">الفترة التي تشهد أعلى كثافة في إنشاء الوثائق</p>
                    </div>
                </div>
                <div class="flex items-center gap-6">
                    <div class="w-16 h-16 bg-emerald-50 rounded-2xl flex items-center justify-center text-emerald-500 shrink-0">
                        <i class="fas fa-calendar-check text-2xl"></i>
                    </div>
                    <div>
                        <p class="text-[10px] font-black text-slate-400 uppercase mb-1">يوم العمل الأكثر نشاطاً</p>
                        <p class="text-xl font-black text-slate-700">${dayNames[peakDay]}</p>
                        <p class="text-[10px] font-bold text-slate-400 mt-1">اليوم الذي يتم فيه إنجاز أكبر قدر من المهام</p>
                    </div>
                </div>
            `;
        }

        window.selectModel = function (cId, mId) {
            window.activeModId = mId;
            window.sessionModIds = [mId]; // Set as the only active session model
            syncLocalStorage();

            window.exitLibrary();
            window.updateMainDropdowns();

            const catSel = document.getElementById('mainCatSelect');
            if (catSel) {
                catSel.value = cId;
                window.updateMainModelList();
                const modelSel = document.getElementById('mainModelSelect');
                if (modelSel) {
                    modelSel.value = mId;
                }
            }

            window.renderMainView();
            window.showToast("تم تحميل النموذج بنجاح ✓");
        };

        window.saveAndReturn = function () {
            window.syncMainToModel();
            syncLocalStorage();
            document.getElementById('designPage').classList.add('hidden-view');
            document.getElementById('mainPage').classList.remove('hidden-view');
            document.getElementById('editModeIndicator').classList.add('hidden');
            window.updateMainDropdowns();
            window.renderMainView();
        };

        window.makeSelectedTextVariable = function () {
            const sel = window.getSelection(); const text = sel.toString().trim();
            if (!text) return alert("يرجى تظليل النص الذي تريد تحويله لمتغير أولاً");
            const vName = prompt("أدخل مسمى لهذا الحقل (مثلاً: اسم العامل، تاريخ العقد...):");
            if (!vName) return;

            const m = getCurrentModel(), vId = "v-" + Date.now();
            if (!m.variables) m.variables = [];
            m.variables.push({ id: vId, label: vName, original: text, currentValue: "" });

            const range = sel.getRangeAt(0);
            range.deleteContents();
            const span = document.createElement("span");
            span.className = "variable-placeholder";
            span.setAttribute("data-var-id", vId);
            span.innerText = text;
            range.insertNode(span);

            window.syncMainToModel();
            window.renderDesignView();
        };

        window.saveHybridTemplate = async function () {
            const m = getCurrentModel(); if (!m) return;
            m.htmlContent = document.getElementById('designA4Content').innerHTML;
            syncLocalStorage();
            document.getElementById('loadingOverlay').classList.remove('hidden');

            try {
                const cat = appData.find(c => c.models.some(mod => mod.id === m.id));
                const data = {
                    categoryName: cat.name,
                    modelName: m.name,
                    htmlContent: m.htmlContent,
                    variables: m.variables,
                    updatedAt: window.fs.serverTimestamp()
                };

                if (typeof m.id === "string" && m.id.length > 15) {
                    await window.fs.updateDoc(window.fs.doc(window.db, "templates", m.id), data);
                } else {
                    const docRef = await window.fs.addDoc(window.fs.collection(window.db, "templates"), data);
                    m.id = docRef.id;
                    syncLocalStorage();
                }
                showToast("تم حفظ القالب بنجاح");
                location.reload(); // لإعادة التنشيط
            } catch (e) {
                showToast("تم الحفظ محلياً (لا يوجد اتصال)");
                window.saveAndReturn();
            } finally {
                document.getElementById('loadingOverlay').classList.add('hidden');
            }
        };

        window.handleWordLoad = function (event) {
            const file = event.target.files[0]; if (!file) return;

            // التحقق من صيغة الملف
            const fileName = file.name.toLowerCase();
            if (fileName.endsWith('.doc')) {
                // منع التكملة وإظهار إرشادات التحويل
                showToast("صيغة .DOC قديمة؛ يرجى فتح الملف وحفظه بصيغة .DOCX للاستيراد");
                alert("⚠️ تنبيه: الملف المحدد بصيغة Word قديمة (.doc)\n\nلضمان استيراد النصوص والجداول بشكل صحيح، يرجى:\n1. فتح الملف في برنامج Word.\n2. اختيار 'حفظ باسم' (Save As).\n3. اختيار الصيغة الحديثة Word Document (.docx).\n4. ثم حاول استيراد الملف الجديد هنا.");
                return;
            }

            document.getElementById('loadingOverlay').classList.remove('hidden');
            const reader = new FileReader();
            reader.onload = function (e) {
                mammoth.convertToHtml({ arrayBuffer: e.target.result }, {
                    styleMap: [
                        "p[style-name='Heading 1'] => h1:fresh",
                        "p[style-name='Heading 2'] => h2:fresh",
                        "p[style-name='Heading 3'] => h3:fresh",
                        "p[style-name='Heading 4'] => h4:fresh",
                        "p[style-name='Heading 5'] => h5:fresh",
                        "p[style-name='Heading 6'] => h6:fresh",
                        "p[style-name='Normal'] => p:fresh",
                        "p[style-name='Title'] => h1.title:fresh",
                        "p[style-name='Subtitle'] => h2.subtitle:fresh",
                        "p[style-name='Quote'] => blockquote:fresh",
                        "p[style-name='Intense Quote'] => blockquote.intense:fresh",
                        "r[style-name='Strong'] => b",
                        "r[style-name='Emphasis'] => i",
                        "b => b",
                        "i => i",
                        "u => u",
                        "strike => s",
                        "small-caps => span.small-caps"
                    ],
                    ignoreEmptyParagraphs: false,
                    convertImage: mammoth.images.inline(imageElement => {
                        return imageElement.read("base64").then(imageContents => ({
                            src: `data:${imageElement.contentType};base64,${imageContents.data}`
                        }));
                    })
                }).then(result => {
                    const m = getCurrentModel();
                    if (m) {
                        const wrapper = document.createElement('div');
                        wrapper.innerHTML = result.value;

                        // Universal RTL and Font Styling
                        wrapper.querySelectorAll('*').forEach(el => {
                            if (['P', 'H1', 'H2', 'H3', 'H4', 'H5', 'H6', 'LI', 'TD', 'TH', 'BLOCKQUOTE'].includes(el.tagName)) {
                                el.style.direction = 'rtl';
                                el.style.textAlign = 'right';
                                el.style.fontFamily = "'Cairo', sans-serif";
                                el.style.lineHeight = '1.6';
                                el.style.marginBottom = '0.5em';
                            }
                        });

                        // Enhanced Table Styling
                        wrapper.querySelectorAll('table').forEach(t => {
                            t.style.borderCollapse = 'collapse';
                            t.style.width = '100.2%'; // Small offset to fix border rendering in some browsers
                            t.style.margin = '10px 0';
                            t.style.direction = 'rtl';
                        });
                        wrapper.querySelectorAll('td, th').forEach(cell => {
                            cell.style.border = '1px solid #cbd5e1';
                            cell.style.padding = '8px 12px';
                            cell.style.minHeight = '1.5em';
                            cell.style.verticalAlign = 'top';
                        });

                        // Fix for lists (mammoth lists can be tricky)
                        wrapper.querySelectorAll('ul, ol').forEach(list => {
                            list.style.paddingRight = '40px';
                            list.style.direction = 'rtl';
                        });

                        m.htmlContent = wrapper.innerHTML;
                        m.variables = [];
                        window.renderDesignView();
                        showToast("تم استيراد ملف Word بنجاح ✓");
                    }
                    document.getElementById('loadingOverlay').classList.add('hidden');
                }).catch(err => {
                    console.error('mammoth error', err);
                    document.getElementById('loadingOverlay').classList.add('hidden');
                    showToast("حدث خطأ في قراءة ملف Word");
                });
            };
            reader.readAsArrayBuffer(file);
        };

        window.saveHybridRecord = async function () {
            if (!window.sessionModIds || window.sessionModIds.length === 0) {
                return showToast("لا توجد وثائق نشطة في الجلسة للأرشفة");
            }

            // جمع كل القيم المشتركة من جميع النماذج النشطة
            const allData = {};
            let customerName = "غير معروف";
            let nationalId = "---";

            window.sessionModIds.forEach(mid => {
                const m = getModelById(mid);
                if (!m) return;
                m.variables.forEach(v => {
                    allData[v.label] = v.currentValue;
                    if (v.label.includes("اسم") && v.currentValue) customerName = v.currentValue;
                    if ((v.label.includes("بطاقة") || v.label.includes("هوية")) && v.currentValue) nationalId = v.currentValue;
                });
            });

            const timestamp = Date.now();
            let lastSavedRec = null;

            for (const mid of window.sessionModIds) {
                const m = getModelById(mid);
                if (!m) continue;

                // مزامنة المحتوى من العرض إلى النموذج قبل الحفظ
                const contentEl = document.getElementById(`content-${mid}`);
                if (contentEl) m.htmlContent = contentEl.innerHTML;

                const rec = {
                    id: String(timestamp + Math.random()),
                    modelId: m.id,
                    modelName: m.name,
                    customerName: customerName,
                    nationalId: nationalId,
                    data: allData,
                    htmlContent: m.htmlContent,
                    readableDate: new Date().toLocaleString('ar-SA')
                };

                archive.push(rec);
                lastSavedRec = rec;

                try {
                    await window.fs.addDoc(window.fs.collection(window.db, "customerRecords"), rec);
                } catch (e) {
                    console.warn("Firebase save error for model", mid, e);
                }
            }

            syncLocalStorage();
            document.getElementById('printBtn').classList.remove('hidden');
            showToast(`تم حفظ وأرشفة ${window.sessionModIds.length} وثيقة بنجاح ✓`);
        };

        // --- دوال المساعدة ---
        window.removeVariable = function (vid) {
            const m = getCurrentModel(); if (!m) return;
            const v = m.variables.find(x => x.id === vid);
            if (!v) return;

            const isDesign = !document.getElementById('designPage').classList.contains('hidden-view');
            const contId = isDesign ? 'designA4Content' : 'mainA4Content';
            const cont = document.getElementById(contId);
            if (cont) {
                const placeholders = cont.querySelectorAll(`[data-var-id="${vid}"]`);
                placeholders.forEach(span => {
                    span.replaceWith(document.createTextNode(v.original || span.innerText));
                });
            }

            m.variables = m.variables.filter(x => x.id !== vid);
            window.syncMainToModel();
            syncLocalStorage();
            window.renderDesignView();
        };

        window.moveVariable = function (vid, dir) {
            const m = getCurrentModel(); if (!m) return;
            const idx = m.variables.findIndex(v => v.id === vid);
            if (idx === -1) return;
            const targetIdx = idx + dir;
            if (targetIdx < 0 || targetIdx >= m.variables.length) return;

            const [v] = m.variables.splice(idx, 1);
            m.variables.splice(targetIdx, 0, v);

            syncLocalStorage();
            window.renderDesignView();
        };

        window.openDesignSetup = function () {
            const modal = document.getElementById('setupModal');
            const catSelect = document.getElementById('setupCatSelect');

            // تهيئة القائمة بالخيارات الموجودة
            catSelect.innerHTML = '<option value="new">+ إنشاء قسم جديد</option>' +
                appData.map(c => `<option value="${c.id}">${c.name}</option>`).join('');

            document.getElementById('setupModelNameInput').value = "";
            document.getElementById('setupNewCatInput').value = "";

            // ضبط الحالة الافتراضية بناءً على الخيار الأول
            catSelect.value = "new";
            window.toggleNewCatInput("new");

            modal.classList.add('active');
        };

        window.toggleNewCatInput = function (val) {
            const group = document.getElementById('newCatInputGroup');
            group.style.display = (val === 'new') ? 'block' : 'none';
        };

        window.closeDesignSetup = function () {
            document.getElementById('setupModal').classList.remove('active');
        };

        window.editCurrentModel = function () {
            const m = getCurrentModel(); if (!m) return alert("اختر نموذجاً أولاً");
            document.getElementById('mainPage').classList.add('hidden-view');
            document.getElementById('designPage').classList.remove('hidden-view');
            document.getElementById('editModeIndicator').classList.remove('hidden');
            window.renderDesignView();
        };

        window.renderDesignView = function () {
            const m = getCurrentModel(); if (!m) return;
            const designCont = document.getElementById('designA4Content');
            // الصفحة فارغة تماماً عند البداية كما طلب المستخدم
            designCont.innerHTML = m.htmlContent || '';
            document.getElementById('designModelTitle').innerText = m.name;

            // إحاطة الصور الخام بحاويات متحركة
            const rawImages = designCont.querySelectorAll('img:not(.img-container img)');
            rawImages.forEach(img => {
                const container = document.createElement('div');
                container.className = 'img-container';
                container.style.width = (img.width || 200) + 'px';
                container.style.height = (img.height || 200) + 'px';
                container.style.left = '50px';
                container.style.top = '50px';
                container.setAttribute('contenteditable', 'false');
                img.parentNode.insertBefore(container, img);
                container.appendChild(img);
            });

            const fieldsHtml = m.variables.length > 0
                ? m.variables.map((v, i) => `
                    <div id="field-item-${v.id}" class="p-4 bg-white border border-slate-100 rounded-2xl flex justify-between items-center group cursor-pointer hover:border-primary transition shadow-sm mb-2" onclick="window.selectField('${v.id}')">
                        <div class="flex flex-col">
                            <div class="text-[12px] font-black text-slate-700">${v.label}</div>
                            <div class="text-[8px] font-bold text-slate-400">ID: ${v.id.substring(0, 8)}</div>
                        </div>
                        <div class="flex items-center gap-2">
                            <div class="flex flex-col bg-slate-50 rounded-lg p-0.5">
                                <button onclick="event.stopPropagation(); window.moveVariable('${v.id}', -1)" class="text-slate-400 hover:text-primary p-1 transition ${i === 0 ? 'opacity-20 pointer-events-none' : ''}" title="تحريك لأعلى"><i class="fas fa-chevron-up text-[10px]"></i></button>
                                <button onclick="event.stopPropagation(); window.moveVariable('${v.id}', 1)" class="text-slate-400 hover:text-primary p-1 transition ${i === m.variables.length - 1 ? 'opacity-20 pointer-events-none' : ''}" title="تحريك لأسفل"><i class="fas fa-chevron-down text-[10px]"></i></button>
                            </div>
                            <button onclick="event.stopPropagation(); window.removeVariable('${v.id}')" class="text-danger/40 hover:text-danger p-2 hover:bg-red-50 rounded-xl transition" title="حذف الحقل"><i class="fas fa-trash-alt text-sm"></i></button>
                        </div>
                    </div>`).join('')
                : `<div class="text-center py-10 bg-slate-50/50 rounded-[2rem] border-2 border-dashed border-slate-100">
                    <i class="fas fa-magic text-slate-200 text-3xl mb-3"></i>
                    <p class="text-[10px] font-bold text-slate-400">لا توجد حقول ذكية بعد</p>
                  </div>`;

            document.getElementById('designFieldsList').innerHTML = fieldsHtml;

            designCont.querySelectorAll('.img-container').forEach(c => {
                c.setAttribute('contenteditable', 'false');
                initImgControl(c);
            });
        };

        window.confirmDesignSetup = function () {
            const catSelect = document.getElementById('setupCatSelect');
            const newCatInput = document.getElementById('setupNewCatInput');
            const modelNameInput = document.getElementById('setupModelNameInput');

            let catId = catSelect.value;
            let modelName = modelNameInput.value.trim();

            if (!modelName) return alert("يرجى إدخال اسم النموذج");

            let cat;
            if (catId === 'new') {
                const catName = newCatInput.value.trim();
                if (!catName) return alert("يرجى إدخال اسم القسم الجديد");
                cat = { id: Date.now().toString(), name: catName, models: [] };
                appData.push(cat);
            } else {
                cat = appData.find(c => String(c.id) === String(catId));
            }

            if (!cat) return alert("حدث خطأ في تحديد القسم - يرجى المحاولة مرة أخرى");

            const newMod = {
                id: (Date.now() + 1).toString(),
                name: modelName,
                htmlContent: "",
                variables: []
            };

            cat.models.push(newMod);
            window.activeModId = newMod.id;
            syncLocalStorage();

            window.closeDesignSetup();
            window.editCurrentModel();
        };

        window.processInsertedImage = function (event) {
            const file = event.target.files[0]; if (!file) return;
            const reader = new FileReader();
            reader.onload = function (e) {
                const container = document.createElement('div');
                container.className = "img-container";
                container.style.width = "150px";
                container.style.height = "150px";
                container.style.left = "50px";
                container.style.top = "50px";

                const img = document.createElement('img');
                img.src = e.target.result;
                container.appendChild(img);

                const isDesign = !document.getElementById('designPage').classList.contains('hidden-view');
                const targetId = isDesign ? 'designA4Content' : 'mainA4Content';
                document.getElementById(targetId).appendChild(container);
                initImgControl(container);
                window.syncMainToModel();
            };
            reader.readAsDataURL(file);
        };

        window.clearDesignContent = function () {
            if (!confirm("هل أنت متأكد من مسح محتوى القالب بالكامل؟")) return;
            const m = getCurrentModel(); if (!m) return;
            m.htmlContent = "";
            m.variables = [];
            syncLocalStorage();
            window.renderDesignView();
            showToast("تم مسح محتوى القالب");
        };

        window.exportAllDataJSON = function () {
            const dataToExport = {
                appData: appData,
                archive: archive,
                settings: settings,
                exportDate: new Date().toISOString(),
                version: "v3.6"
            };

            const jsonString = JSON.stringify(dataToExport, null, 2);
            const blob = new Blob([jsonString], { type: 'application/json' });
            const url = URL.createObjectURL(blob);

            const link = document.createElement('a');
            link.href = url;
            const dateStr = new Date().toISOString().split('T')[0].replace(/-/g, '_');
            link.download = `hybrid_backup_${dateStr}.json`;
            link.click();

            setTimeout(() => URL.revokeObjectURL(url), 100);
            showToast("تم تصدير نسخة احتياطية JSON بنجاح ✓");
        };

        window.importAllDataJSON = function () {
            document.getElementById('importJsonInput').click();
        };

        window.handleJsonImport = function (event) {
            const file = event.target.files[0];
            if (!file) return;

            const reader = new FileReader();
            reader.onload = function (e) {
                try {
                    const importedData = JSON.parse(e.target.result);

                    // التحقق من صحة الملف
                    if (!importedData.appData || !importedData.archive) {
                        throw new Error("ملف JSON غير صالح أو لا يحتوي على البيانات المطلوبة");
                    }

                    if (!confirm("تحذير: استيراد البيانات سيؤدي إلى استبدال البيانات الحالية (النماذج والسجلات والإعدادات). هل ترغب في المتابعة؟")) {
                        event.target.value = "";
                        return;
                    }

                    // تحديث البيانات
                    appData = importedData.appData;
                    archive = importedData.archive;
                    if (importedData.settings) settings = importedData.settings;

                    syncLocalStorage();
                    showToast("تم استيراد البيانات بنجاح! سيتم إعادة تحميل الصفحة...");

                    setTimeout(() => {
                        location.reload();
                    }, 1500);

                } catch (err) {
                    console.error("Import Error:", err);
                    showToast("فشل الاستيراد: " + err.message, "error");
                }
            };
            reader.readAsText(file);
        };

        window.exportToWord = function () {
            const isDesign = !document.getElementById('designPage').classList.contains('hidden-view');

            let content = '';
            let fileName = '';

            if (isDesign) {
                const m = getCurrentModel();
                if (!m) return alert("يرجى اختيار نموذج أولاً");
                content = document.getElementById('designA4Content').innerHTML;
                fileName = m.name;
            } else {
                if (!window.sessionModIds || window.sessionModIds.length === 0)
                    return alert("لا توجد وثائق نشطة في الجلسة");
                window.sessionModIds.forEach(mid => {
                    const el = document.getElementById(`content-${mid}`);
                    if (el) content += el.innerHTML + '<div style="page-break-after:always;"></div>';
                });
                const firstM = getModelById(window.sessionModIds[0]);
                fileName = firstM ? firstM.name : 'وثيقة';
            }

            const dir = settings.word.forceRTL ? 'rtl' : 'ltr';
            const header = `<html xmlns:o='urn:schemas-microsoft-com:office:office' xmlns:w='urn:schemas-microsoft-com:office:word' xmlns='http://www.w3.org/TR/REC-html40' dir='${dir}'>
            <head><meta charset='utf-8'><title>Export</title>
            <style>
                @font-face { font-family: 'Arial'; }
                body { font-family: 'Arial', sans-serif; direction: rtl; text-align: right; }
                img { max-width: 100%; }
                .img-container { position: absolute; z-index: -1; border: none !important; }
                .variable-placeholder { background: transparent; border: none; padding: 0; color: black !important; }
            </style>
            </head><body>`;
            const footer = "</body></html>";
            const sourceHTML = header + content + footer;

            const blob = new Blob(['\ufeff', sourceHTML], { type: 'application/msword' });
            const link = document.createElement('a');
            link.href = URL.createObjectURL(blob);
            link.download = `${fileName}_${Date.now()}.doc`;
            link.click();
        };

        window.exportToPDF = async function () {
            const isDesign = !document.getElementById('designPage').classList.contains('hidden-view');

            // تحديد العناصر المصدر للتصدير
            let sourceElements = [];
            let fileName = 'وثيقة';

            if (isDesign) {
                const m = getCurrentModel();
                if (!m) return alert("يرجى اختيار نموذج أولاً");
                sourceElements = [document.getElementById('designA4')];
                fileName = m.name;
            } else {
                if (!window.sessionModIds || window.sessionModIds.length === 0)
                    return alert("لا توجد وثائق نشطة في الجلسة");
                window.sessionModIds.forEach(mid => {
                    const wrapper = document.querySelector(`[data-mid="${mid}"] .a4-paper`);
                    if (wrapper) sourceElements.push(wrapper);
                });
                const firstM = getModelById(window.sessionModIds[0]);
                fileName = firstM ? firstM.name : 'وثيقة';
            }

            if (sourceElements.length === 0) return alert("لم يتم العثور على محتوى للتصدير");

            window.showToast("جاري تجهيز PDF... الرجاء الانتظار");

            const pageWidth = settings.pdf.pageSize === 'A4' ? '210mm' : (settings.pdf.pageSize === 'A5' ? '148mm' : '216mm');

            const cleanCSS = `
                .variable-placeholder { background: transparent !important; border: none !important; padding: 0 !important; color: black !important; outline: none !important; }
                .img-container { border: none !important; background: transparent !important; box-shadow: none !important; }
                .resizer, .no-print { display: none !important; }
                * { color: black !important; }
            `;

            try {
                const { jsPDF } = window.jspdf;
                const pdf = new jsPDF('p', 'mm', 'a4');
                const pdfWidth = pdf.internal.pageSize.getWidth();
                const pdfPageHeight = pdf.internal.pageSize.getHeight();

                for (let i = 0; i < sourceElements.length; i++) {
                    const cleanContainer = document.createElement('div');
                    cleanContainer.style.cssText = `position:absolute;top:-99999px;left:0;width:${pageWidth};background:white;margin:0;padding:0;`;

                    const styleEl = document.createElement('style');
                    styleEl.innerHTML = cleanCSS;

                    const contentClone = sourceElements[i].cloneNode(true);
                    contentClone.style.boxShadow = 'none';
                    contentClone.style.border = 'none';
                    contentClone.style.margin = '0';
                    contentClone.style.padding = `${settings.pdf.margin}mm`;
                    contentClone.style.width = '100%';
                    contentClone.style.height = 'auto';
                    contentClone.style.position = 'relative';

                    cleanContainer.appendChild(styleEl);
                    cleanContainer.appendChild(contentClone);
                    document.body.appendChild(cleanContainer);

                    const canvas = await html2canvas(cleanContainer, {
                        scale: 3,
                        useCORS: true,
                        logging: false,
                        backgroundColor: "#ffffff"
                    });

                    document.body.removeChild(cleanContainer);

                    const imgData = canvas.toDataURL('image/jpeg', 1.0);
                    const imgHeight = (canvas.height * pdfWidth) / canvas.width;

                    if (i > 0) pdf.addPage();
                    pdf.addImage(imgData, 'JPEG', 0, 0, pdfWidth, imgHeight);
                }

                const safeName = fileName.replace(/[٠-٩]/g, d => "٠١٢٣٤٥٦٧٨٩".indexOf(d));
                pdf.save(`${safeName}_${Date.now()}.pdf`);
                window.showToast(`تم تصدير PDF بنجاح (${sourceElements.length} صفحة) ✓`);
            } catch (err) {
                console.error(err);
                alert("حدث خطأ أثناء تصدير PDF");
            }
        };

        window.selectField = function (vid) {
            window.selectedFieldId = vid;
            const m = getCurrentModel();
            const f = m.variables.find(x => x.id === vid);

            // تمييز العنصر في القائمة الجانبية
            document.querySelectorAll('[id^="field-item-"]').forEach(el => {
                el.classList.remove('border-primary', 'bg-blue-50/50', 'ring-2', 'ring-primary/20');
            });
            const selectedItem = document.getElementById(`field-item-${vid}`);
            if (selectedItem) {
                selectedItem.classList.add('border-primary', 'bg-blue-50/50', 'ring-2', 'ring-primary/20');
            }

            if (f) {
                document.getElementById('propertiesPanel').classList.remove('hidden');
                document.getElementById('editLabelInput').value = f.label;
            }
        };

        window.updateActiveFieldLabel = function (v) {
            if (selectedFieldId) {
                getCurrentModel().variables.find(x => x.id === selectedFieldId).label = v;
                window.renderDesignView();
            }
        };

        window.syncMainToModel = function () {
            const m = getCurrentModel();
            const isDesign = !document.getElementById('designPage').classList.contains('hidden-view');
            const contId = isDesign ? 'designA4Content' : 'mainA4Content';
            const cont = document.getElementById(contId);
            if (m && cont) m.htmlContent = cont.innerHTML;
        };

        function getCurrentModel() {
            if (!window.activeModId) return null;
            const targetId = String(window.activeModId);
            for (let c of appData) {
                const m = c.models.find(x => String(x.id) === targetId);
                if (m) return m;
            }
            return null;
        }

        function getModelById(mid) {
            const targetId = String(mid);
            for (let c of appData) {
                const m = c.models.find(x => String(x.id) === targetId);
                if (m) return m;
            }
            return null;
        }
        window.getModelById = getModelById;

        window.showToast = function (m) {
            const existing = document.getElementById('toast');
            if (existing) existing.remove();
            const t = document.createElement('div');
            t.id = "toast";
            t.className = "fixed bottom-10 left-10 bg-dark text-white px-8 py-4 rounded-2xl shadow-2xl z-[400] flex items-center gap-3 animate-bounce";
            t.innerHTML = `<i class="fas fa-check-circle text-success"></i><p class="font-bold text-sm">${m}</p>`;
            document.body.appendChild(t);
            setTimeout(() => t.remove(), 4000);
        };

        window.updateMainDropdowns = function () {
            const catSelect = document.getElementById('mainCatSelect');
            if (!catSelect) return;
            catSelect.innerHTML = appData.map(c => `<option value="${c.id}">${c.name}</option>`).join('');
            window.updateMainModelList();
        };

        window.updateMainModelList = function () {
            const catId = document.getElementById('mainCatSelect').value;
            const cat = appData.find(c => c.id == catId);
            if (cat) {
                document.getElementById('mainModelSelect').innerHTML = cat.models.map(m => `<option value="${m.id}">${m.name}</option>`).join('');
                window.loadMainModel();
            }
        };

        window.loadMainModel = function () {
            const mId = document.getElementById('mainModelSelect').value;
            if (mId) {
                window.activeModId = mId;
                // عند تغيير القالب من القائمة المنسدلة، نقوم ببدء جلسة جديدة بهذا القالب فقط
                window.sessionModIds = [mId];
                window.renderMainView();
            }
        };

        window.fetchTemplatesFromFirebase = async function () {
            if (!window.db) return;
            try {
                const qS = await window.fs.getDocs(window.fs.collection(window.db, "templates"));
                const cats = {};
                qS.forEach((d) => {
                    const item = d.data();
                    if (!cats[item.categoryName]) cats[item.categoryName] = { id: Date.now() + Math.random(), name: item.categoryName, models: [] };
                    cats[item.categoryName].models.push({ id: d.id, name: item.modelName, htmlContent: item.htmlContent, variables: item.variables || [] });
                });
                if (Object.keys(cats).length > 0) {
                    appData = Object.values(cats);
                    syncLocalStorage();
                    window.updateMainDropdowns();
                }
            } catch (e) { console.warn("Firebase Templates Sync Error", e); }
        }

        window.fetchRecordsFromFirebase = async function (isManual = false) {
            console.log("Attempting to fetch records from Firebase...");
            if (!window.db || !window.fs) {
                console.warn("Firebase not initialized yet.");
                if (isManual) showToast("قاعدة البيانات غير جاهزة بعد", "error");
                return;
            }

            if (isManual) showToast("جاري مزامنة السجلات من السحاب...");

            try {
                const qS = await window.fs.getDocs(window.fs.collection(window.db, "customerRecords"));
                console.log(`Cloud returned ${qS.size} records.`);

                let count = 0;
                qS.forEach((d) => {
                    const cloudRec = d.data();
                    if (!cloudRec.id) return;

                    const exists = archive.some(localRec => String(localRec.id) === String(cloudRec.id));
                    if (!exists) {
                        archive.push(cloudRec);
                        count++;
                    }
                });

                if (count > 0) {
                    archive.sort((a, b) => (a.id || 0) - (b.id || 0));
                    syncLocalStorage();
                    if (!document.getElementById('customerListPage').classList.contains('hidden-view')) {
                        window.renderCustomerList();
                    }
                    showToast(`تم مزامنة ${count} سجل جديد بنجاح ✓`);
                } else if (isManual) {
                    showToast("السجلات محدثة بالفعل ✅");
                }
                console.log(`Sync completed. Added ${count} new records.`);
            } catch (e) {
                console.error("Firebase Records Sync Error:", e);
                if (isManual) showToast("فشلت المزامنة: تحقق من الاتصال", "error");
            }
        }
    </script>

    <!-- نافذة إضافة نموذج للجلسة -->
    <div id="addModelModal"
        class="fixed inset-0 bg-dark/60 backdrop-blur-sm z-[100] hidden flex items-center justify-center p-4">
        <div class="bg-white w-full max-w-md rounded-[2.5rem] shadow-2xl overflow-hidden animate-slideUp">
            <div class="p-8 border-b border-slate-50 bg-slate-50/50">
                <div class="flex justify-between items-center mb-2">
                    <h3 class="text-xl font-black text-slate-800">إضافة وثيقة للجلسة</h3>
                    <button onclick="window.closeAddModelModal()" class="text-slate-400 hover:text-danger transition"><i
                            class="fas fa-times text-xl"></i></button>
                </div>
                <p class="text-xs font-bold text-slate-400">اختر القسم ثم القالب الذي ترغب في إضافته</p>
            </div>
            <div class="p-8 space-y-6">
                <div class="space-y-2">
                    <label class="text-[11px] font-black text-slate-400 mr-2 uppercase">القسم</label>
                    <select id="modalCatSelect" onchange="window.updateModalModelList()"
                        class="w-full border-2 border-slate-100 rounded-2xl p-4 text-sm font-bold outline-none focus:border-primary transition-all"></select>
                </div>
                <div class="space-y-2">
                    <label class="text-[11px] font-black text-slate-400 mr-2 uppercase">القالب</label>
                    <select id="modalModelSelect"
                        class="w-full border-2 border-slate-100 rounded-2xl p-4 text-sm font-bold outline-none focus:border-primary transition-all"></select>
                </div>
                <button onclick="window.confirmAddModelToSession()"
                    class="w-full bg-primary text-white py-4 rounded-2xl font-black text-sm shadow-xl hover:bg-blue-700 transition flex items-center justify-center gap-2">
                    <i class="fas fa-check-circle"></i>
                    تأكيد الإضافة
                </button>
            </div>
        </div>
    </div>

    <script>
        // وظائف نافذة إضافة النماذج
        window.openAddModelModal = function () {
            const modal = document.getElementById('addModelModal');
            modal.classList.remove('hidden');

            const catSelect = document.getElementById('modalCatSelect');
            catSelect.innerHTML = appData.map(c => `<option value="${c.id}">${c.name}</option>`).join('');
            window.updateModalModelList();
        };

        window.closeAddModelModal = function () {
            document.getElementById('addModelModal').classList.add('hidden');
        };

        window.updateModalModelList = function () {
            const catId = document.getElementById('modalCatSelect').value;
            const cat = appData.find(c => c.id == catId);
            if (cat) {
                document.getElementById('modalModelSelect').innerHTML = cat.models.map(m => `<option value="${m.id}">${m.name}</option>`).join('');
            }
        };

        window.confirmAddModelToSession = function () {
            const mId = document.getElementById('modalModelSelect').value;
            if (!mId) return showToast("اختر قالباً أولاً");

            if (!window.sessionModIds) window.sessionModIds = [];
            if (window.sessionModIds.includes(mId)) {
                showToast("هذا القالب موجود بالفعل في الجلسة");
                return;
            }

            window.sessionModIds.push(mId);
            window.renderMainView();
            window.closeAddModelModal();
            showToast("تم إضافة الوثيقة بنجاح");
        };

        // --- WhatsApp Logic ---
        window.openWhatsAppModal = function () {
            const modal = document.getElementById('whatsappModal');
            modal.classList.add('active');

            // Reset fields
            document.getElementById('waCustomerName').value = "";
            document.getElementById('waCustomerPhone').value = "";
            document.getElementById('waMessageBody').value = "";

            // Auto-fill from session if exists
            if (window.sessionModIds && window.sessionModIds.length > 0) {
                let foundName = "";
                let foundPhone = "";

                // Look through active models for name/phone
                window.sessionModIds.forEach(mid => {
                    const m = getModelById(mid);
                    if (m && m.variables) {
                        m.variables.forEach(v => {
                            const lbl = v.label.toLowerCase();
                            if ((lbl.includes("اسم") || lbl.includes("زبون") || lbl.includes("name")) && v.currentValue) {
                                if (!foundName) foundName = v.currentValue;
                            }
                            if ((lbl.includes("هاتف") || lbl.includes("رقم") || lbl.includes("phone") || lbl.includes("mobile")) && v.currentValue) {
                                if (!foundPhone) foundPhone = v.currentValue;
                            }
                        });
                    }
                });

                document.getElementById('waCustomerName').value = foundName;
                document.getElementById('waCustomerPhone').value = foundPhone;
            }

            window.renderWhatsAppTemplates();
        };

        window.closeWhatsAppModal = function () {
            document.getElementById('whatsappModal').classList.remove('active');
        };

        window.renderWhatsAppTemplates = function () {
            const list = document.getElementById('waTemplatesList');
            const templates = JSON.parse(localStorage.getItem('hybrid_wa_templates') || '[]');

            if (templates.length === 0) {
                list.innerHTML = `
                    <div class="text-center py-8 bg-white/50 rounded-2xl border-2 border-dashed border-slate-200">
                        <p class="text-[10px] font-bold text-slate-400">لا توجد قوالب رسائل حالياً</p>
                    </div>`;
                return;
            }

            list.innerHTML = templates.map((t, i) => `
                <div class="wa-template-item p-3 bg-white rounded-xl shadow-sm cursor-pointer relative group flex flex-col gap-1" onclick="window.selectWhatsAppTemplate(${i})">
                    <p class="text-[11px] font-bold text-slate-700 line-clamp-2">${t}</p>
                    <button onclick="event.stopPropagation(); window.deleteWhatsAppTemplate(${i})" 
                        class="absolute top-1 left-1 bg-red-50 text-danger w-5 h-5 flex items-center justify-center rounded-lg opacity-0 group-hover:opacity-100 transition-all">
                        <i class="fas fa-trash-alt text-[9px]"></i>
                    </button>
                </div>
            `).join('');
        };

        window.addWhatsAppTemplate = function () {
            const text = document.getElementById('newWaTemplateText').value.trim();
            if (!text) return showToast("اكتب نص القالب أولاً");

            const templates = JSON.parse(localStorage.getItem('hybrid_wa_templates') || '[]');
            templates.push(text);
            localStorage.setItem('hybrid_wa_templates', JSON.stringify(templates));

            document.getElementById('newWaTemplateText').value = "";
            window.renderWhatsAppTemplates();
            showToast("تم إضافة قالب الرسالة بنجاح ✓");
        };

        window.deleteWhatsAppTemplate = function (index) {
            if (!confirm("هل أنت متأكد من حذف هذا القالب؟")) return;
            const templates = JSON.parse(localStorage.getItem('hybrid_wa_templates') || '[]');
            templates.splice(index, 1);
            localStorage.setItem('hybrid_wa_templates', JSON.stringify(templates));
            window.renderWhatsAppTemplates();
        };

        window.selectWhatsAppTemplate = function (index) {
            const templates = JSON.parse(localStorage.getItem('hybrid_wa_templates') || '[]');
            const text = templates[index];
            if (text) {
                document.getElementById('waMessageBody').value = text;
                showToast("تم اختيار القالب بنجاح ✓");
            }
        };

        window.sendWhatsAppMessage = function () {
            let phone = document.getElementById('waCustomerPhone').value.trim();
            const message = document.getElementById('waMessageBody').value.trim();

            if (!phone) return showToast("يرجى إدخال رقم الهاتف");
            if (!message) return showToast("يرجى كتابة نص الرسالة");

            // Clean phone number (keep only digits)
            phone = phone.replace(/\D/g, '');

            // Basic Moroccan phone formatting logic
            // Handle: 06..., 07..., 6..., 7...
            if (phone.length === 10 && phone.startsWith('0')) {
                phone = '212' + phone.substring(1);
            } else if (phone.length === 9 && (phone.startsWith('6') || phone.startsWith('7'))) {
                phone = '212' + phone;
            }

            // Fallback for international numbers or already correct format
            // If it doesn't start with a known country code and is short, we could assume Moroccan +212

            const encodedMsg = encodeURIComponent(message);
            const waUrl = `https://wa.me/${phone}?text=${encodedMsg}`;

            window.open(waUrl, '_blank');
            showToast("جاري توجيهك إلى واتساب...");
        };

        // تهيئة أولية
        console.log("Hybrid Editor v3.6 - WhatsApp & Modal Session Add Active");
    </script>
</body>

</html>

</html>
