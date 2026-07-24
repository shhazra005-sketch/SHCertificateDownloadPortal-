```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Official Certificate Management Portal</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Google Fonts -->
    <link href="https://fonts.googleapis.com/css2?family=Cinzel:wght@600;700;800;900&family=Playfair+Display:ital,wght@0,600;0,700;1,500&family=Montserrat:wght@400;500;600;700;800;900&family=Fira+Code:wght@600;700&display=swap" rel="stylesheet">
    <!-- html2canvas Library -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/html2canvas/1.4.1/html2canvas.min.js"></script>

    <!-- Firebase SDK Compat Versions -->
    <script src="https://www.gstatic.com/firebasejs/11.6.1/firebase-app-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/11.6.1/firebase-auth-compat.js"></script>
    <script src="https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore-compat.js"></script>

    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        brand: {
                            50: '#fdf2f2',
                            100: '#fde8e8',
                            800: '#9b1c1c',
                            900: '#7e1e1e',
                            950: '#450a0a',
                        }
                    },
                    fontFamily: {
                        cinzel: ['Cinzel', 'serif'],
                        playfair: ['Playfair Display', 'serif'],
                        montserrat: ['Montserrat', 'sans-serif'],
                        mono: ['Fira Code', 'monospace']
                    }
                }
            }
        }
    </script>
    <style>
        ::-webkit-scrollbar {
            width: 8px;
            height: 8px;
        }
        ::-webkit-scrollbar-track {
            background: #f8fafc;
        }
        ::-webkit-scrollbar-thumb {
            background: #cbd5e1;
            border-radius: 4px;
        }
        ::-webkit-scrollbar-thumb:hover {
            background: #94a3b8;
        }

        @keyframes floatGradient {
            0% { background-position: 0% 50%; }
            50% { background-position: 100% 50%; }
            100% { background-position: 0% 50%; }
        }
        .animated-mesh-bg {
            background: linear-gradient(-45deg, #fef2f2, #fff1f2, #fefce8, #f8fafc);
            background-size: 400% 400%;
            animation: floatGradient 12s ease infinite;
        }

        /* Certificate Canvas - Standard HD Landscape Ratio (1123px x 794px) */
        .certificate-canvas { 
            width: 1123px; 
            height: 794px; 
            background: #ffffff; 
            position: relative; 
            overflow: hidden; 
            box-sizing: border-box;
        }

        .cert-border-outer { 
            border: 22px solid var(--cert-border-color, #450a0a); 
            height: 100%; 
            width: 100%; 
            padding: 16px; 
            position: relative;
            box-sizing: border-box;
            background: #ffffff;
        }

        .cert-border-inner { 
            border: 4px solid var(--cert-accent-color, #bfa15f); 
            height: 100%; 
            width: 100%; 
            padding: 24px 40px; 
            display: flex; 
            flex-direction: column; 
            justify-content: space-between; 
            text-align: center;
            position: relative;
            box-sizing: border-box;
            background-size: cover;
            background-position: center;
            background-repeat: no-repeat;
        }

        .corner-accent {
            position: absolute;
            width: 44px;
            height: 44px;
            border: 5px solid var(--cert-accent-color, #bfa15f);
            z-index: 10;
        }
        .c-tl { top: 6px; left: 6px; border-right: none; border-bottom: none; }
        .c-tr { top: 6px; right: 6px; border-left: none; border-bottom: none; }
        .c-bl { bottom: 6px; left: 6px; border-right: none; border-top: none; }
        .c-br { bottom: 6px; right: 6px; border-left: none; border-top: none; }

        .glass-panel {
            background: rgba(255, 255, 255, 0.92);
            backdrop-filter: blur(12px);
            border: 1px solid rgba(226, 232, 240, 0.9);
        }

        .color-gold { color: #bfa15f; }
        .color-ruby { color: #9b1c1c; }
        .color-emerald { color: #047857; }
        .color-sapphire { color: #1d4ed8; }
        .color-purple { color: #7e22ce; }
        .color-amber { color: #d97706; }
        .color-charcoal { color: #0f172a; }
        
        @keyframes shimmerText {
            0% { background-position: -200% center; }
            100% { background-position: 200% center; }
        }
        .color-shimmer-gold {
            background: linear-gradient(90deg, #bfa15f 0%, #ffe8ad 50%, #bfa15f 100%);
            background-size: 200% auto;
            color: transparent;
            -webkit-background-clip: text;
            background-clip: text;
            animation: shimmerText 4s linear infinite;
        }
        .color-shimmer-rainbow {
            background: linear-gradient(90deg, #ef4444, #f59e0b, #10b981, #3b82f6, #8b5cf6, #ef4444);
            background-size: 200% auto;
            color: transparent;
            -webkit-background-clip: text;
            background-clip: text;
            animation: shimmerText 5s linear infinite;
        }
    </style>
</head>
<body class="animated-mesh-bg min-h-screen font-montserrat flex flex-col justify-between text-slate-800 antialiased selection:bg-red-800 selection:text-white">

    <!-- Header Navigation -->
    <header class="bg-white/95 backdrop-blur-md border-b border-slate-200 sticky top-0 z-50 shadow-sm transition-all duration-300">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
            <div class="flex items-center justify-between h-20">
                
                <!-- Brand Logo & Title -->
                <div class="flex items-center space-x-4 cursor-pointer" onclick="switchTab('portal')">
                    <div class="h-14 w-14 flex items-center justify-center bg-red-50 rounded-2xl overflow-hidden shadow-inner border border-red-100">
                        <img id="nav-portal-logo" src="" alt="Portal Logo" class="h-12 w-auto object-contain hidden">
                        <div id="nav-portal-placeholder" class="text-red-950 font-cinzel font-black text-xl">CERT</div>
                    </div>
                    <div>
                        <span id="nav-main-title" class="font-cinzel text-base sm:text-lg font-black tracking-widest text-slate-900 block uppercase">OFFICIAL CERTIFICATE PORTAL</span>
                        <span id="nav-sub-header" class="text-[10px] text-red-800 tracking-wider uppercase block font-bold">Rishi Bankim Chandra Evening College &bull; NSS Unit-I</span>
                    </div>
                </div>

                <!-- Navigation Controls -->
                <div class="flex items-center space-x-3">
                    <nav class="flex space-x-2">
                        <button onclick="switchTab('portal')" id="tab-portal" class="nav-tab px-5 py-2.5 rounded-xl text-xs font-black bg-red-900 text-white shadow-md shadow-red-900/20 transition-all duration-300 tracking-widest uppercase">
                            PORTAL
                        </button>
                        <button onclick="openAdminModal()" id="tab-admin" class="nav-tab px-5 py-2.5 rounded-xl text-xs font-bold text-slate-700 hover:bg-red-50 hover:text-red-900 transition-all duration-300 flex items-center gap-1.5 tracking-widest uppercase">
                            <svg class="w-4 h-4" fill="none" stroke="currentColor" stroke-width="2.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M12 15v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2zm10-10V7a4 4 0 00-8 0v4h8z"></path></svg>
                            ADMIN PANEL
                        </button>
                    </nav>
                </div>

            </div>
        </div>
    </header>

    <!-- Toast Notification -->
    <div id="toast" class="fixed top-8 left-1/2 -translate-x-1/2 z-[300] max-w-xl w-11/12 bg-white border-2 rounded-2xl shadow-2xl p-4 transform -translate-y-48 opacity-0 transition-all duration-500 ease-out pointer-events-none">
        <div class="flex items-start gap-4">
            <div class="flex-shrink-0" id="toast-icon-box">
                <svg id="toast-icon-success" class="h-8 w-8 text-emerald-600" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2.5" d="M9 12l2 2 4-4m6 2a9 9 0 11-18 0 9 9 0 0118 0z" />
                </svg>
                <svg id="toast-icon-error" class="h-8 w-8 text-rose-600 hidden" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2.5" d="M12 8v4m0 4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
                </svg>
            </div>
            <div class="flex-grow">
                <p id="toast-title" class="text-xs font-black tracking-widest uppercase text-slate-900">System Notification</p>
                <p id="toast-message" class="text-xs text-slate-600 mt-0.5 font-semibold leading-relaxed">Operation completed.</p>
            </div>
        </div>
    </div>

    <!-- Main Content -->
    <main class="flex-grow max-w-7xl mx-auto w-full px-4 sm:px-6 lg:px-8 py-8">

        <!-- Status Banner -->
        <div id="status-banner" class="text-center p-3.5 rounded-2xl font-bold mb-8 shadow-sm border transition-all duration-300 text-xs tracking-wider">
            <!-- Dynamically Loaded -->
        </div>

        <!-- Portal Section -->
        <div id="section-portal" class="tab-content space-y-10">
            <div class="grid grid-cols-1 lg:grid-cols-12 gap-8 items-start">
                
                <!-- Form Control Panel -->
                <div class="lg:col-span-5 space-y-6">
                    
                    <!-- Segment Switcher -->
                    <div class="flex bg-white p-1.5 rounded-2xl border border-slate-200 shadow-sm">
                        <button onclick="toggleFormSegment('register')" id="segment-btn-register" class="flex-1 py-3 text-center rounded-xl text-xs font-black tracking-widest uppercase transition-all duration-300 bg-red-900 text-white shadow-md">
                            New Registration
                        </button>
                        <button onclick="toggleFormSegment('retrieve')" id="segment-btn-retrieve" class="flex-1 py-3 text-center rounded-xl text-xs font-black tracking-widest uppercase transition-all duration-300 text-slate-500 hover:text-red-900">
                            Retrieve Certificate
                        </button>
                    </div>

                    <!-- Student Registration Form -->
                    <div id="form-segment-register" class="glass-panel p-6 sm:p-8 rounded-3xl shadow-xl shadow-slate-200/50 transition-all duration-300">
                        <div class="border-b border-slate-200/80 pb-4 mb-6">
                            <h2 class="text-sm font-black text-slate-900 flex items-center gap-2 uppercase tracking-wider">
                                <span class="p-1.5 bg-red-50 text-red-800 rounded-xl">
                                    <svg class="w-4 h-4" fill="none" stroke="currentColor" stroke-width="2.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M16 7a4 4 0 11-8 0 4 4 0 018 0zM12 14a7 7 0 00-7 7h14a7 7 0 00-7-7z"></path></svg>
                                </span>
                                Student Registration
                            </h2>
                            <p class="text-xs text-slate-500 mt-1">Provide student credentials to issue official certificate.</p>
                        </div>

                        <form id="enrollmentForm" onsubmit="handleRegistrationSubmit(event)" class="space-y-5">
                            <div>
                                <label class="block text-[11px] font-bold text-slate-600 uppercase tracking-widest mb-2">Full Name</label>
                                <div class="relative">
                                    <input type="text" id="pName" required
                                           class="w-full px-4 py-3.5 bg-white border border-slate-200 rounded-xl focus:ring-2 focus:ring-red-800 focus:border-transparent outline-none transition-all duration-300 font-bold uppercase text-slate-800 text-xs tracking-wider"
                                           oninput="syncPreviewName(this.value)">
                                </div>
                            </div>

                            <div>
                                <label class="block text-[11px] font-bold text-slate-600 uppercase tracking-widest mb-2">Mobile Number (Indian 10-Digit)</label>
                                <div class="relative">
                                    <input type="tel" id="pPhone" required maxlength="10"
                                           class="w-full px-4 py-3.5 bg-white border border-slate-200 rounded-xl focus:ring-2 focus:ring-red-800 focus:border-transparent outline-none transition-all duration-300 font-bold text-slate-800 text-xs tracking-wider"
                                           onkeypress="return isNumberKey(event)"
                                           oninput="this.value = this.value.replace(/[^0-9]/g, '')">
                                </div>
                            </div>

                            <button type="submit" id="submit-btn" class="w-full bg-red-900 hover:bg-red-950 text-white py-4 px-4 rounded-xl font-black tracking-widest uppercase transition-all duration-300 shadow-lg shadow-red-950/10 flex items-center justify-center gap-2 text-xs">
                                <svg class="w-4 h-4 text-white" fill="none" stroke="currentColor" stroke-width="2.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4"></path></svg>
                                REGISTER & ISSUE CERTIFICATE
                            </button>
                        </form>
                    </div>

                    <!-- Certificate Retrieval Form -->
                    <div id="form-segment-retrieve" class="glass-panel p-6 sm:p-8 rounded-3xl shadow-xl shadow-slate-200/50 transition-all duration-300 hidden">
                        <div class="border-b border-slate-200/80 pb-4 mb-6">
                            <h2 class="text-sm font-black text-slate-900 flex items-center gap-2 uppercase tracking-wider">
                                <span class="p-1.5 bg-red-50 text-red-800 rounded-xl">
                                    <svg class="w-4 h-4" fill="none" stroke="currentColor" stroke-width="2.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M21 21l-6-6m2-5a7 7 0 11-14 0 7 7 0 0114 0z"></path></svg>
                                </span>
                                Certificate Retrieval
                            </h2>
                            <p class="text-xs text-slate-500 mt-1">Search and re-download your registered certificate.</p>
                        </div>

                        <form id="retrievalForm" onsubmit="handleRetrieval(event)" class="space-y-5">
                            <div>
                                <label class="block text-[11px] font-bold text-slate-600 uppercase tracking-widest mb-2">Registered Mobile Number</label>
                                <div class="relative">
                                    <input type="tel" id="rPhone" required maxlength="10"
                                           class="w-full px-4 py-3.5 bg-white border border-slate-200 rounded-xl focus:ring-2 focus:ring-red-800 focus:border-transparent outline-none transition-all duration-300 font-bold text-slate-800 text-xs tracking-wider"
                                           onkeypress="return isNumberKey(event)"
                                           oninput="this.value = this.value.replace(/[^0-9]/g, ''); handleLiveRetrievalSync(this.value)">
                                </div>
                            </div>

                            <button type="submit" class="w-full bg-red-900 hover:bg-red-950 text-white py-4 px-4 rounded-xl font-black tracking-widest uppercase transition-all duration-300 shadow-lg shadow-red-950/10 flex items-center justify-center gap-2 text-xs">
                                <svg class="w-4 h-4 text-white" fill="none" stroke="currentColor" stroke-width="2.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4"></path></svg>
                                SEARCH & DOWNLOAD (.JPG)
                            </button>
                        </form>
                    </div>

                    <!-- Instructions -->
                    <div class="bg-amber-50/90 border border-amber-200 p-5 rounded-2xl text-xs text-amber-950 space-y-1.5 font-medium">
                        <h3 class="font-bold uppercase tracking-wider text-[11px] flex items-center gap-1.5 text-amber-900">
                            <svg class="w-4 h-4 text-amber-700" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M13 16h-1v-4h-1m1-4h.01M21 12a9 9 0 11-18 0 9 9 0 0118 0z"></path></svg>
                            Instructions
                        </h3>
                        <p class="leading-relaxed">1. Enter student's full legal name in English.</p>
                        <p class="leading-relaxed">2. A valid 10-digit Indian mobile number starting with 6, 7, 8, or 9 is mandatory.</p>
                        <p class="leading-relaxed">3. Upon successful registration, the certificate will automatically download.</p>
                    </div>
                </div>

                <!-- Live Preview Panel -->
                <div class="lg:col-span-7 flex flex-col items-center">
                    <div class="w-full flex justify-between items-center mb-3 px-1">
                        <span class="text-xs font-bold text-red-900 uppercase tracking-wider flex items-center gap-2">
                            <span class="w-2.5 h-2.5 rounded-full bg-red-700 animate-ping"></span> Live Certificate Layout Preview
                        </span>
                        <span class="text-[10px] text-slate-400 font-bold uppercase tracking-widest">HD Render Engine</span>
                    </div>

                    <!-- Canvas Container -->
                    <div class="w-full bg-white border border-slate-200 rounded-3xl p-4 sm:p-6 flex items-center justify-center overflow-hidden shadow-xl shadow-slate-200/50">
                        <div id="preview-wrapper" class="w-full flex items-center justify-center" style="height: 320px;">
                            <div id="cert-preview-scale" class="origin-center transition-transform duration-200">
                                
                                <!-- Live Canvas -->
                                <div id="live-cert-node" class="certificate-canvas shadow-2xl">
                                    <div class="cert-border-outer">
                                        <div class="corner-accent c-tl"></div>
                                        <div class="corner-accent c-tr"></div>
                                        <div class="corner-accent c-bl"></div>
                                        <div class="corner-accent c-br"></div>
                                        
                                        <div id="live-cert-inner-bg" class="cert-border-inner">
                                            
                                            <!-- Three Logo Slots -->
                                            <div class="grid grid-cols-3 items-center px-4 mt-1 w-full h-28">
                                                <div class="flex justify-start h-28 items-center">
                                                    <img id="cert-logo-1" src="" alt="Left Logo" class="h-24 w-auto object-contain hidden max-w-[200px]">
                                                </div>
                                                <div class="flex justify-center h-28 items-center">
                                                    <img id="cert-logo-2" src="" alt="Center Logo" class="h-24 w-auto object-contain hidden max-w-[200px]">
                                                </div>
                                                <div class="flex justify-end h-28 items-center">
                                                    <img id="cert-logo-3" src="" alt="Right Logo" class="h-24 w-auto object-contain hidden max-w-[200px]">
                                                </div>
                                            </div>

                                            <!-- Header Text -->
                                            <div class="mt-2">
                                                <h1 id="preview-cert-title" class="text-[40px] font-cinzel font-black tracking-wider uppercase leading-none" style="color: var(--cert-border-color, #450a0a)">Certificate of Participation</h1>
                                                <div class="mt-3 flex justify-center">
                                                    <p id="preview-cert-subtext" class="text-2xl font-playfair italic text-slate-600">This is to certify that</p>
                                                </div>
                                            </div>

                                            <!-- Student Name & Body Text -->
                                            <div class="my-auto py-3">
                                                <div class="relative inline-block px-12 py-2">
                                                    <h2 id="cert-name" class="text-[48px] font-extrabold font-cinzel uppercase tracking-widest leading-none inline-block min-w-[450px] color-gold">
                                                        &nbsp;
                                                    </h2>
                                                </div>
                                                
                                                <div id="preview-body-container" class="text-[15px] leading-relaxed max-w-4xl mx-auto text-slate-700 font-montserrat font-medium mt-4 px-6">
                                                    <!-- Dynamic Content -->
                                                </div>
                                            </div>

                                            <!-- Signatures & Barcode Footer -->
                                            <div class="grid grid-cols-3 items-end pt-2 mb-1">
                                                
                                                <!-- Left Signature -->
                                                <div class="flex flex-col items-center justify-end h-28 text-center">
                                                    <div class="h-16 flex items-center justify-center mb-1">
                                                        <img id="cert-sig-1" src="" alt="Left Signature" class="h-14 w-auto object-contain hidden max-w-[200px]">
                                                    </div>
                                                    <div class="border-t-2 border-slate-300 w-4/5 pt-1 text-[11px] font-bold uppercase tracking-wider text-slate-800 leading-tight">
                                                        <span id="preview-sig1-desig">Teacher-In-Charge</span><br>
                                                        <span id="preview-sig1-sub" class="text-slate-500 text-[9px] block font-semibold mt-0.5">RISHI BANKIM CHANDRA<br>EVENING COLLEGE</span>
                                                    </div>
                                                </div>

                                                <!-- Center Barcode & Student ID -->
                                                <div id="cert-barcode-box" class="flex flex-col items-center justify-center invisible">
                                                    <div id="cert-barcode-svg" class="mb-1 opacity-90"></div>
                                                    <div class="text-[13px] text-slate-900 font-black font-mono tracking-wider">
                                                        ID: <span id="cert-id-tag-val" class="color-gold font-black">RBCEC/DAC/NSS/001</span>
                                                    </div>
                                                    <span id="cert-date-tag" class="text-[9.5px] text-slate-500 font-extrabold uppercase tracking-wider mt-0.5">Date: 20/07/2026</span>
                                                </div>

                                                <!-- Right Signature -->
                                                <div class="flex flex-col items-center justify-end h-28 text-center">
                                                    <div class="h-16 flex items-center justify-center mb-1">
                                                        <img id="cert-sig-2" src="" alt="Right Signature" class="h-14 w-auto object-contain hidden max-w-[200px]">
                                                    </div>
                                                    <div class="border-t-2 border-slate-300 w-4/5 pt-1 text-[11px] font-bold uppercase tracking-wider text-slate-800 leading-tight">
                                                        <span id="preview-sig2-desig">Programme Officer</span><br>
                                                        <span id="preview-sig2-sub" class="text-slate-500 text-[9px] block font-semibold mt-0.5">NSS UNIT - I<br>RISHI BANKIM CHANDRA<br>EVENING COLLEGE</span>
                                                    </div>
                                                </div>

                                            </div>
                                        </div>
                                    </div>
                                </div>

                            </div>
                        </div>
                    </div>
                </div>

            </div>
        </div>

        <!-- Admin Control Dashboard -->
        <div id="section-admin" class="tab-content hidden space-y-8">
            
            <!-- Admin Bar -->
            <div class="flex flex-col md:flex-row justify-between items-start md:items-center gap-4 bg-white p-6 rounded-3xl border border-slate-200 shadow-sm">
                <div>
                    <h2 class="text-base font-bold text-slate-900 flex items-center gap-2 uppercase tracking-wider">
                        <span class="p-2 bg-red-50 text-red-800 rounded-xl">
                            <svg class="w-5 h-5" fill="none" stroke="currentColor" stroke-width="2.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M12 6V4m0 2a2 2 0 100 4m0-4a2 2 0 110 4m-6 8a2 2 0 100-4m0 4a2 2 0 110-4m0 4v2m0-6V4m6 6v10m6-2a2 2 0 100-4m0 4a2 2 0 110-4m0 4v2m0-6V4"></path></svg>
                        </span>
                        Admin Control Dashboard
                    </h2>
                    <p class="text-xs text-slate-500 mt-1">Configure site branding, logos, certificate texts, themes, security password, and manage student records.</p>
                </div>
                <div class="flex flex-wrap gap-2">
                    <button id="btn-toggle-portal" onclick="togglePortalState()" class="px-5 py-2.5 rounded-xl text-xs font-black tracking-widest transition-all duration-300 shadow-sm uppercase border">
                        <!-- Dynamically Updated -->
                    </button>
                    <button onclick="logoutAdmin()" class="bg-red-50 hover:bg-red-900 border border-red-100 text-red-800 hover:text-white px-5 py-2.5 rounded-xl text-xs font-black tracking-widest transition-all duration-300 shadow-sm uppercase">
                        LOGOUT ADMIN
                    </button>
                </div>
            </div>

            <!-- Portal Header Customizer -->
            <div class="bg-white p-6 sm:p-8 rounded-3xl border border-slate-200 shadow-sm space-y-4">
                <h3 class="font-bold text-slate-900 text-sm uppercase tracking-wider flex items-center gap-2">
                    <svg class="w-4 h-4 text-red-800" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M19 21V5a2 2 0 00-2-2H7a2 2 0 00-2 2v16m14 0h2m-2 0h-5m-9 0H3m2 0h5M9 7h1m-1 4h1m4-4h1m-1 4h1m-5 10v-5a1 1 0 011-1h2a1 1 0 011 1v5m-4 0h4"></path></svg>
                    Website Header & Portal Branding
                </h3>
                <div class="grid grid-cols-1 md:grid-cols-2 gap-4 text-xs">
                    <div>
                        <label class="block font-bold text-slate-600 uppercase tracking-widest mb-2">Portal Main Header Title</label>
                        <input type="text" id="config-portal-title" class="w-full p-3 bg-slate-50 border border-slate-200 rounded-xl font-bold text-slate-800 outline-none focus:ring-2 focus:ring-red-800">
                    </div>
                    <div>
                        <label class="block font-bold text-slate-600 uppercase tracking-widest mb-2">Portal Sub-Header Text</label>
                        <input type="text" id="config-portal-sub" class="w-full p-3 bg-slate-50 border border-slate-200 rounded-xl font-bold text-slate-800 outline-none focus:ring-2 focus:ring-red-800">
                    </div>
                </div>
            </div>

            <!-- Border Theme Color Options -->
            <div class="bg-white p-6 sm:p-8 rounded-3xl border border-slate-200 shadow-sm space-y-4">
                <h3 class="font-bold text-slate-900 text-sm uppercase tracking-wider flex items-center gap-2">
                    <svg class="w-4 h-4 text-red-800" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M7 21a4 4 0 01-4-4V5a2 2 0 012-2h4a2 2 0 012 2v12a4 4 0 01-4 4zm0 0h12a2 2 0 002-2v-4a2 2 0 00-2-2h-2.343M11 7.343l1.657-1.357a2 2 0 012.828 0l2.829 2.829a2 2 0 010 2.828l-8.486 8.485M7 17h.01"></path></svg>
                    Certificate Outer Border & Accent Palette Selection
                </h3>
                <div class="flex flex-wrap gap-3 text-xs font-bold">
                    <button onclick="setThemeColor('#450a0a', '#bfa15f')" class="px-4 py-2.5 rounded-xl border bg-red-50 text-red-950 flex items-center gap-2 hover:shadow-md transition">
                        <span class="w-4 h-4 rounded-full bg-[#450a0a] border"></span> Crimson Maroon & Gold
                    </button>
                    <button onclick="setThemeColor('#0f172a', '#d97706')" class="px-4 py-2.5 rounded-xl border bg-slate-50 text-slate-950 flex items-center gap-2 hover:shadow-md transition">
                        <span class="w-4 h-4 rounded-full bg-[#0f172a] border"></span> Dark Charcoal & Gold
                    </button>
                    <button onclick="setThemeColor('#1e3a8a', '#eab308')" class="px-4 py-2.5 rounded-xl border bg-blue-50 text-blue-950 flex items-center gap-2 hover:shadow-md transition">
                        <span class="w-4 h-4 rounded-full bg-[#1e3a8a] border"></span> Deep Navy Blue & Gold
                    </button>
                    <button onclick="setThemeColor('#064e3b', '#facc15')" class="px-4 py-2.5 rounded-xl border bg-emerald-50 text-emerald-950 flex items-center gap-2 hover:shadow-md transition">
                        <span class="w-4 h-4 rounded-full bg-[#064e3b] border"></span> Royal Emerald Green
                    </button>
                    <button onclick="setThemeColor('#581c87', '#f59e0b')" class="px-4 py-2.5 rounded-xl border bg-purple-50 text-purple-950 flex items-center gap-2 hover:shadow-md transition">
                        <span class="w-4 h-4 rounded-full bg-[#581c87] border"></span> Imperial Purple & Amber
                    </button>
                    <button onclick="setThemeColor('#831843', '#fbbf24')" class="px-4 py-2.5 rounded-xl border bg-pink-50 text-pink-950 flex items-center gap-2 hover:shadow-md transition">
                        <span class="w-4 h-4 rounded-full bg-[#831843] border"></span> Deep Rose Velvet
                    </button>
                    <button onclick="setThemeColor('#134e4a', '#38bdf8')" class="px-4 py-2.5 rounded-xl border bg-teal-50 text-teal-950 flex items-center gap-2 hover:shadow-md transition">
                        <span class="w-4 h-4 rounded-full bg-[#134e4a] border"></span> Ocean Teal & Cyan
                    </button>
                </div>
            </div>

            <!-- Student Name & ID Font Color Selector -->
            <div class="bg-white p-6 sm:p-8 rounded-3xl border border-slate-200 shadow-sm space-y-4">
                <h3 class="font-bold text-slate-900 text-sm uppercase tracking-wider flex items-center gap-2">
                    <svg class="w-4 h-4 text-red-800" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M7 21a4 4 0 01-4-4V5a2 2 0 012-2h4a2 2 0 012 2v12a4 4 0 01-4 4zm0 0h12a2 2 0 002-2v-4a2 2 0 00-2-2h-2.343M11 7.343l1.657-1.357a2 2 0 012.828 0l2.829 2.829a2 2 0 010 2.828l-8.486 8.485M7 17h.01"></path></svg>
                    Student Name & ID Font Color Selector
                </h3>
                <div class="flex flex-wrap gap-3 text-xs font-bold">
                    <button onclick="setNameFontColor('color-gold')" class="px-4 py-2.5 rounded-xl border bg-amber-50 text-amber-900 font-extrabold flex items-center gap-2">
                        <span class="w-3.5 h-3.5 rounded-full bg-[#bfa15f]"></span> Classic Metallic Gold
                    </button>
                    <button onclick="setNameFontColor('color-shimmer-gold')" class="px-4 py-2.5 rounded-xl border bg-amber-100 text-amber-950 font-extrabold flex items-center gap-2">
                        <span class="w-3.5 h-3.5 rounded-full bg-gradient-to-r from-amber-400 to-yellow-200"></span> Animated Shimmer Gold
                    </button>
                    <button onclick="setNameFontColor('color-ruby')" class="px-4 py-2.5 rounded-xl border bg-red-50 text-red-900 font-extrabold flex items-center gap-2">
                        <span class="w-3.5 h-3.5 rounded-full bg-[#9b1c1c]"></span> Royal Ruby Red
                    </button>
                    <button onclick="setNameFontColor('color-emerald')" class="px-4 py-2.5 rounded-xl border bg-emerald-50 text-emerald-900 font-extrabold flex items-center gap-2">
                        <span class="w-3.5 h-3.5 rounded-full bg-[#047857]"></span> Deep Emerald
                    </button>
                    <button onclick="setNameFontColor('color-sapphire')" class="px-4 py-2.5 rounded-xl border bg-blue-50 text-blue-900 font-extrabold flex items-center gap-2">
                        <span class="w-3.5 h-3.5 rounded-full bg-[#1d4ed8]"></span> Sapphire Blue
                    </button>
                    <button onclick="setNameFontColor('color-purple')" class="px-4 py-2.5 rounded-xl border bg-purple-50 text-purple-900 font-extrabold flex items-center gap-2">
                        <span class="w-3.5 h-3.5 rounded-full bg-[#7e22ce]"></span> Velvet Purple
                    </button>
                    <button onclick="setNameFontColor('color-shimmer-rainbow')" class="px-4 py-2.5 rounded-xl border bg-slate-900 text-white font-extrabold flex items-center gap-2">
                        <span class="w-3.5 h-3.5 rounded-full bg-gradient-to-r from-red-500 via-green-500 to-blue-500"></span> Animated Spectrum Gradient
                    </button>
                </div>
            </div>

            <!-- Logos, Signatures & Background Manager -->
            <div class="bg-white p-6 sm:p-8 rounded-3xl border border-slate-200 shadow-sm space-y-6">
                <div>
                    <h3 class="font-bold text-slate-900 text-sm uppercase tracking-wider flex items-center gap-2">
                        <svg class="w-4 h-4 text-red-800" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M4 16l4.586-4.586a2 2 0 012.828 0L16 16m-2-2l1.586-1.586a2 2 0 012.828 0L20 14m-6-6h.01M6 20h12a2 2 0 002-2V6a2 2 0 00-2-2H6a2 2 0 00-2 2v12a2 2 0 002 2z"></path></svg>
                        Logos, Signatures & Inner Background Theme Manager
                    </h3>
                    <p class="text-xs text-slate-400 mt-1">Upload transparent PNG/JPG images (Max 200KB auto-compression active).</p>
                </div>

                <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6 text-xs">
                    
                    <!-- Slot 0: Portal Header Logo -->
                    <div class="p-4 bg-slate-50 border border-slate-200 rounded-2xl space-y-3">
                        <span class="font-black uppercase text-slate-800 block">Portal Header Logo</span>
                        <div class="h-20 w-full bg-white rounded-xl border flex items-center justify-center overflow-hidden">
                            <img id="thumb-portal-logo" src="" class="h-16 w-auto object-contain hidden">
                            <span id="thumb-portal-logo-empty" class="text-[10px] text-slate-400">No Image Active</span>
                        </div>
                        <input type="file" id="input-portal-logo" accept="image/*" class="w-full text-[11px] text-slate-500">
                        <div class="flex gap-2 pt-1">
                            <button onclick="uploadSingleAsset('portalLogo', 'input-portal-logo')" class="flex-1 bg-emerald-700 hover:bg-emerald-800 text-white font-bold py-2 rounded-lg transition">Upload Header Logo</button>
                            <button onclick="deleteSingleAsset('portalLogo')" class="bg-rose-100 hover:bg-rose-200 text-rose-800 font-bold px-3 py-2 rounded-lg transition">Delete</button>
                        </div>
                    </div>

                    <!-- Slot 1: Certificate Left Logo -->
                    <div class="p-4 bg-slate-50 border border-slate-200 rounded-2xl space-y-3">
                        <span class="font-black uppercase text-red-900 block">Logo 1 (Left Position)</span>
                        <div class="h-20 w-full bg-white rounded-xl border flex items-center justify-center overflow-hidden">
                            <img id="thumb-logo-1" src="" class="h-16 w-auto object-contain hidden">
                            <span id="thumb-logo-1-empty" class="text-[10px] text-slate-400">No Image Active</span>
                        </div>
                        <input type="file" id="input-logo-1" accept="image/*" class="w-full text-[11px] text-slate-500">
                        <div class="flex gap-2 pt-1">
                            <button onclick="uploadSingleAsset('logo1', 'input-logo-1')" class="flex-1 bg-emerald-700 hover:bg-emerald-800 text-white font-bold py-2 rounded-lg transition">Upload Logo 1</button>
                            <button onclick="deleteSingleAsset('logo1')" class="bg-rose-100 hover:bg-rose-200 text-rose-800 font-bold px-3 py-2 rounded-lg transition">Delete</button>
                        </div>
                    </div>

                    <!-- Slot 2: Certificate Center Logo -->
                    <div class="p-4 bg-slate-50 border border-slate-200 rounded-2xl space-y-3">
                        <span class="font-black uppercase text-amber-800 block">Logo 2 (Center Position)</span>
                        <div class="h-20 w-full bg-white rounded-xl border flex items-center justify-center overflow-hidden">
                            <img id="thumb-logo-2" src="" class="h-16 w-auto object-contain hidden">
                            <span id="thumb-logo-2-empty" class="text-[10px] text-slate-400">No Image Active</span>
                        </div>
                        <input type="file" id="input-logo-2" accept="image/*" class="w-full text-[11px] text-slate-500">
                        <div class="flex gap-2 pt-1">
                            <button onclick="uploadSingleAsset('logo2', 'input-logo-2')" class="flex-1 bg-emerald-700 hover:bg-emerald-800 text-white font-bold py-2 rounded-lg transition">Upload Logo 2</button>
                            <button onclick="deleteSingleAsset('logo2')" class="bg-rose-100 hover:bg-rose-200 text-rose-800 font-bold px-3 py-2 rounded-lg transition">Delete</button>
                        </div>
                    </div>

                    <!-- Slot 3: Certificate Right Logo -->
                    <div class="p-4 bg-slate-50 border border-slate-200 rounded-2xl space-y-3">
                        <span class="font-black uppercase text-blue-900 block">Logo 3 (Right Position)</span>
                        <div class="h-20 w-full bg-white rounded-xl border flex items-center justify-center overflow-hidden">
                            <img id="thumb-logo-3" src="" class="h-16 w-auto object-contain hidden">
                            <span id="thumb-logo-3-empty" class="text-[10px] text-slate-400">No Image Active</span>
                        </div>
                        <input type="file" id="input-logo-3" accept="image/*" class="w-full text-[11px] text-slate-500">
                        <div class="flex gap-2 pt-1">
                            <button onclick="uploadSingleAsset('logo3', 'input-logo-3')" class="flex-1 bg-emerald-700 hover:bg-emerald-800 text-white font-bold py-2 rounded-lg transition">Upload Logo 3</button>
                            <button onclick="deleteSingleAsset('logo3')" class="bg-rose-100 hover:bg-rose-200 text-rose-800 font-bold px-3 py-2 rounded-lg transition">Delete</button>
                        </div>
                    </div>

                    <!-- Slot 4: Left Signature -->
                    <div class="p-4 bg-slate-50 border border-slate-200 rounded-2xl space-y-3">
                        <span class="font-black uppercase text-purple-900 block">Left Signature</span>
                        <div class="h-20 w-full bg-white rounded-xl border flex items-center justify-center overflow-hidden">
                            <img id="thumb-sig-1" src="" class="h-16 w-auto object-contain hidden">
                            <span id="thumb-sig-1-empty" class="text-[10px] text-slate-400">No Signature Active</span>
                        </div>
                        <input type="file" id="input-sig-1" accept="image/*" class="w-full text-[11px] text-slate-500">
                        <div class="flex gap-2 pt-1">
                            <button onclick="uploadSingleAsset('sig1', 'input-sig-1')" class="flex-1 bg-emerald-700 hover:bg-emerald-800 text-white font-bold py-2 rounded-lg transition">Upload Signature</button>
                            <button onclick="deleteSingleAsset('sig1')" class="bg-rose-100 hover:bg-rose-200 text-rose-800 font-bold px-3 py-2 rounded-lg transition">Delete</button>
                        </div>
                    </div>

                    <!-- Slot 5: Right Signature -->
                    <div class="p-4 bg-slate-50 border border-slate-200 rounded-2xl space-y-3">
                        <span class="font-black uppercase text-teal-900 block">Right Signature</span>
                        <div class="h-20 w-full bg-white rounded-xl border flex items-center justify-center overflow-hidden">
                            <img id="thumb-sig-2" src="" class="h-16 w-auto object-contain hidden">
                            <span id="thumb-sig-2-empty" class="text-[10px] text-slate-400">No Signature Active</span>
                        </div>
                        <input type="file" id="input-sig-2" accept="image/*" class="w-full text-[11px] text-slate-500">
                        <div class="flex gap-2 pt-1">
                            <button onclick="uploadSingleAsset('sig2', 'input-sig-2')" class="flex-1 bg-emerald-700 hover:bg-emerald-800 text-white font-bold py-2 rounded-lg transition">Upload Signature</button>
                            <button onclick="deleteSingleAsset('sig2')" class="bg-rose-100 hover:bg-rose-200 text-rose-800 font-bold px-3 py-2 rounded-lg transition">Delete</button>
                        </div>
                    </div>

                    <!-- Slot 6: Inner Background Image Theme -->
                    <div class="p-4 bg-slate-50 border border-slate-200 rounded-2xl space-y-3 md:col-span-2 lg:col-span-1">
                        <span class="font-black uppercase text-indigo-900 block">Inner Certificate Background Theme</span>
                        <div class="h-20 w-full bg-white rounded-xl border flex items-center justify-center overflow-hidden">
                            <img id="thumb-bg-img" src="" class="h-16 w-auto object-cover hidden">
                            <span id="thumb-bg-img-empty" class="text-[10px] text-slate-400">No Background Active</span>
                        </div>
                        <input type="file" id="input-bg-img" accept="image/*" class="w-full text-[11px] text-slate-500">
                        <div class="flex gap-2 pt-1">
                            <button onclick="uploadSingleAsset('bgImg', 'input-bg-img')" class="flex-1 bg-emerald-700 hover:bg-emerald-800 text-white font-bold py-2 rounded-lg transition">Upload Background</button>
                            <button onclick="deleteSingleAsset('bgImg')" class="bg-rose-100 hover:bg-rose-200 text-rose-800 font-bold px-3 py-2 rounded-lg transition">Delete</button>
                        </div>
                    </div>

                </div>
            </div>

            <!-- Certificate Text Editor -->
            <div class="bg-white p-6 sm:p-8 rounded-3xl border border-slate-200 shadow-sm space-y-6">
                <div>
                    <h3 class="font-bold text-slate-900 text-sm uppercase tracking-wider flex items-center gap-2">
                        <svg class="w-4 h-4 text-red-800" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M11 5H6a2 2 0 00-2 2v11a2 2 0 002 2h11a2 2 0 002-2v-5m-1.414-9.414a2 2 0 112.828 2.828L11.828 15H9v-2.828l8.586-8.586z"></path></svg>
                        Certificate Title & Text Customizer
                    </h3>
                    <p class="text-xs text-slate-400 mt-1">Edit titles, body text, date, and signatory details.</p>
                </div>
                
                <div class="grid grid-cols-1 md:grid-cols-2 gap-5 text-xs">
                    <div>
                        <label class="block font-bold text-slate-600 uppercase tracking-widest mb-2">Main Certificate Title</label>
                        <input type="text" id="config-title" class="w-full p-3 bg-slate-50 border border-slate-200 rounded-xl font-bold text-slate-800 outline-none focus:ring-2 focus:ring-red-800">
                    </div>
                    <div>
                        <label class="block font-bold text-slate-600 uppercase tracking-widest mb-2">Sub-Text Before Student Name</label>
                        <input type="text" id="config-subtext" class="w-full p-3 bg-slate-50 border border-slate-200 rounded-xl font-bold text-slate-800 outline-none focus:ring-2 focus:ring-red-800">
                    </div>

                    <div>
                        <label class="block font-bold text-slate-600 uppercase tracking-widest mb-2">Certificate ID Prefix</label>
                        <input type="text" id="config-prefix" class="w-full p-3 bg-slate-50 border border-slate-200 rounded-xl font-bold text-slate-800 outline-none focus:ring-2 focus:ring-red-800">
                    </div>
                    <div>
                        <label class="block font-bold text-slate-600 uppercase tracking-widest mb-2">Starting Counter Index</label>
                        <input type="number" id="config-start-index" min="1" class="w-full p-3 bg-slate-50 border border-slate-200 rounded-xl font-bold text-slate-800 outline-none focus:ring-2 focus:ring-red-800">
                    </div>

                    <div>
                        <label class="block font-bold text-slate-600 uppercase tracking-widest mb-2">College / Institution Name</label>
                        <input type="text" id="config-college" class="w-full p-3 bg-slate-50 border border-slate-200 rounded-xl font-bold text-slate-800 outline-none focus:ring-2 focus:ring-red-800">
                    </div>
                    <div>
                        <label class="block font-bold text-slate-600 uppercase tracking-widest mb-2">Unit / Department Designation</label>
                        <input type="text" id="config-unit" class="w-full p-3 bg-slate-50 border border-slate-200 rounded-xl font-bold text-slate-800 outline-none focus:ring-2 focus:ring-red-800">
                    </div>

                    <div class="md:col-span-2">
                        <label class="block font-bold text-slate-600 uppercase tracking-widest mb-2">Campaign / Event Title</label>
                        <input type="text" id="config-event" class="w-full p-3 bg-slate-50 border border-slate-200 rounded-xl font-bold text-slate-800 outline-none focus:ring-2 focus:ring-red-800">
                    </div>
                    
                    <div class="md:col-span-2">
                        <label class="block font-bold text-slate-600 uppercase tracking-widest mb-2">Certificate Body Description Text</label>
                        <textarea id="config-body" rows="3" class="w-full p-3 bg-slate-50 border border-slate-200 rounded-xl font-medium text-slate-800 outline-none focus:ring-2 focus:ring-red-800"></textarea>
                    </div>

                    <div>
                        <label class="block font-bold text-slate-600 uppercase tracking-widest mb-2">Left Signatory Designation</label>
                        <input type="text" id="config-sig1-desig" class="w-full p-3 bg-slate-50 border border-slate-200 rounded-xl font-bold text-slate-800 outline-none focus:ring-2 focus:ring-red-800">
                    </div>
                    <div>
                        <label class="block font-bold text-slate-600 uppercase tracking-widest mb-2">Left Signatory Sub-Text</label>
                        <input type="text" id="config-sig1-sub" class="w-full p-3 bg-slate-50 border border-slate-200 rounded-xl font-bold text-slate-800 outline-none focus:ring-2 focus:ring-red-800">
                    </div>

                    <div>
                        <label class="block font-bold text-slate-600 uppercase tracking-widest mb-2">Right Signatory Designation</label>
                        <input type="text" id="config-sig2-desig" class="w-full p-3 bg-slate-50 border border-slate-200 rounded-xl font-bold text-slate-800 outline-none focus:ring-2 focus:ring-red-800">
                    </div>
                    <div>
                        <label class="block font-bold text-slate-600 uppercase tracking-widest mb-2">Right Signatory Sub-Text</label>
                        <input type="text" id="config-sig2-sub" class="w-full p-3 bg-slate-50 border border-slate-200 rounded-xl font-bold text-slate-800 outline-none focus:ring-2 focus:ring-red-800">
                    </div>

                    <div class="md:col-span-2">
                        <label class="block font-bold text-slate-600 uppercase tracking-widest mb-2">Event Issue Date (Printed on Certificate)</label>
                        <input type="text" id="config-date" class="w-full p-3 bg-slate-50 border border-slate-200 rounded-xl font-bold text-slate-800 outline-none focus:ring-2 focus:ring-red-800">
                    </div>

                    <div class="md:col-span-2 pt-2">
                        <button onclick="saveAndApplyTextConfig()" class="w-full bg-red-900 hover:bg-red-950 text-white font-black tracking-widest text-xs py-3.5 px-6 rounded-xl shadow-md transition-all uppercase flex items-center justify-center gap-2">
                            <svg class="w-4 h-4" fill="none" stroke="currentColor" stroke-width="2.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7"></path></svg>
                            SAVE & PUBLISH TEXTS TO CLOUD
                        </button>
                    </div>
                </div>
            </div>

            <!-- Admin Password Change Panel -->
            <div class="bg-white p-6 sm:p-8 rounded-3xl border border-slate-200 shadow-sm space-y-4">
                <h3 class="font-bold text-slate-900 text-sm uppercase tracking-wider flex items-center gap-2">
                    <svg class="w-4 h-4 text-red-800" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M12 15v2m-6 4h12a2 2 0 002-2v-6a2 2 0 00-2-2H6a2 2 0 00-2 2v6a2 2 0 002 2zm10-10V7a4 4 0 00-8 0v4h8z"></path></svg>
                    Change Admin Security Password
                </h3>
                <div class="flex flex-col sm:flex-row gap-4">
                    <input type="password" id="new-admin-pass" placeholder="" class="p-3 bg-slate-50 border border-slate-200 rounded-xl text-xs font-bold text-slate-800 outline-none focus:ring-2 focus:ring-red-800 flex-1">
                    <button onclick="changeAdminPassword()" class="bg-slate-900 hover:bg-slate-800 text-white px-6 py-3 rounded-xl font-black text-xs tracking-widest uppercase transition">UPDATE PASSWORD</button>
                </div>
            </div>

            <!-- Student Data Ledger Table -->
            <div class="bg-white border border-slate-200 rounded-3xl shadow-lg overflow-hidden">
                <div class="p-6 border-b border-slate-100 flex flex-col sm:flex-row justify-between items-start sm:items-center gap-4">
                    <div>
                        <h3 class="font-extrabold text-slate-800 text-sm uppercase tracking-wider">Registered Student Ledger</h3>
                        <p class="text-xs text-slate-400 mt-1">Edit names, re-download certificates, or clear ledger with security verification.</p>
                    </div>
                    <div class="flex flex-wrap sm:flex-nowrap gap-2 w-full sm:w-auto">
                        <input type="text" id="db-search" oninput="renderDatabaseTable()" placeholder="Search Name, Phone, ID..."
                               class="w-full sm:w-60 p-2.5 bg-slate-50 border border-slate-200 rounded-xl text-xs text-slate-800 outline-none focus:ring-2 focus:ring-red-800 transition-all">
                        <button onclick="exportDatabaseToCSV()" class="bg-emerald-700 hover:bg-emerald-800 text-white px-4 py-2.5 rounded-xl font-bold text-xs tracking-wider transition uppercase whitespace-nowrap">
                            Export CSV
                        </button>
                        <button onclick="openClearDataAuthModal()" class="bg-rose-600 hover:bg-rose-700 text-white px-4 py-2.5 rounded-xl font-bold text-xs tracking-wider transition uppercase whitespace-nowrap shadow-sm">
                            Clear All Records
                        </button>
                    </div>
                </div>

                <div class="overflow-x-auto">
                    <table class="w-full text-left border-collapse">
                        <thead>
                            <tr class="bg-slate-50 text-slate-500 text-xs font-bold uppercase tracking-wider border-b border-slate-200">
                                <th class="p-4">Sl.</th>
                                <th class="p-4">Certificate ID</th>
                                <th class="p-4">Student Name</th>
                                <th class="p-4">Mobile Number</th>
                                <th class="p-4">Timestamp</th>
                                <th class="p-4 text-right">Actions</th>
                            </tr>
                        </thead>
                        <tbody id="dbTableBody" class="text-slate-700 divide-y divide-slate-100 text-xs">
                            <!-- Dynamic Response -->
                        </tbody>
                    </table>
                </div>
            </div>
        </div>

    </main>

    <!-- Admin Security Login Modal -->
    <div id="admin-login-modal" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-[200] flex items-center justify-center p-4 hidden">
        <div class="bg-white rounded-3xl shadow-2xl w-full max-w-sm overflow-hidden transform scale-95 opacity-0 transition-all duration-300 border border-slate-100" id="login-modal-panel">
            <div class="bg-red-950 p-6 text-center text-white">
                <h3 class="font-cinzel text-base font-black text-amber-400 tracking-wider uppercase">ADMIN LOGIN</h3>
                <p class="text-xs text-red-100/80 mt-1">Enter security PIN code to access</p>
            </div>
            <div class="p-6 space-y-4">
                <div>
                    <input type="password" id="admin-pass-input" autocomplete="off" placeholder=""
                           onkeyup="if(event.key === 'Enter') handleAdminLogin();"
                           class="w-full p-3 bg-slate-50 border border-slate-200 text-slate-800 rounded-xl focus:ring-2 focus:ring-red-800 outline-none text-center font-black tracking-widest text-base">
                </div>
                
                <div class="flex gap-2">
                    <button onclick="closeAdminModal()" class="flex-1 bg-slate-100 hover:bg-slate-200 text-slate-600 py-2.5 rounded-xl font-bold transition-all text-xs uppercase tracking-wider">
                        Cancel
                    </button>
                    <button onclick="handleAdminLogin()" class="flex-1 bg-red-900 hover:bg-red-950 text-white py-2.5 rounded-xl font-black tracking-widest uppercase transition-all text-xs">
                        LOG IN
                    </button>
                </div>
            </div>
        </div>
    </div>

    <!-- Clear All Data Security Verification Modal -->
    <div id="clear-data-modal" class="fixed inset-0 bg-slate-900/70 backdrop-blur-sm z-[280] flex items-center justify-center p-4 hidden">
        <div class="bg-white rounded-3xl shadow-2xl w-full max-w-md overflow-hidden transform scale-95 opacity-0 transition-all duration-300 border border-slate-100" id="clear-data-panel">
            <div class="bg-rose-950 p-6 text-center text-white space-y-1">
                <div class="inline-flex p-3 bg-rose-900/50 rounded-2xl mb-1 text-rose-300">
                    <svg class="w-8 h-8" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16"></path></svg>
                </div>
                <h3 class="font-cinzel text-base font-black text-rose-200 tracking-wider uppercase">CLEAR ALL STUDENT LOGS</h3>
                <p class="text-xs text-rose-100/80">This action will permanently delete all student credentials!</p>
            </div>
            <div class="p-6 space-y-4 text-center">
                <p class="text-xs text-slate-600 font-semibold">Enter Security Admin Password to authorize total database reset:</p>
                <div>
                    <input type="password" id="clear-auth-pin" autocomplete="off" placeholder=""
                           class="w-full p-3 bg-slate-50 border border-slate-200 text-slate-800 rounded-xl focus:ring-2 focus:ring-rose-800 outline-none text-center font-black tracking-widest text-base">
                </div>
                
                <div class="flex gap-2 pt-2">
                    <button onclick="closeClearDataModal()" class="flex-1 bg-slate-100 hover:bg-slate-200 text-slate-600 py-3 rounded-xl font-bold transition-all text-xs uppercase tracking-wider">
                        Cancel
                    </button>
                    <button onclick="confirmClearAllStudentRecords()" class="flex-1 bg-rose-600 hover:bg-rose-700 text-white py-3 rounded-xl font-black tracking-widest uppercase transition-all text-xs shadow-md">
                        CONFIRM ERASE
                    </button>
                </div>
            </div>
        </div>
    </div>

    <!-- Success Dialog Modal -->
    <div id="success-modal" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-[250] flex items-center justify-center p-4 hidden">
        <div class="bg-white rounded-3xl shadow-2xl w-full max-w-md overflow-hidden transform scale-95 opacity-0 transition-all duration-300 border border-slate-100" id="success-modal-panel">
            <div class="p-8 text-center space-y-6">
                <div class="inline-flex items-center justify-center w-16 h-16 rounded-full bg-red-50 text-red-900 shadow-inner">
                    <svg class="w-8 h-8" fill="none" stroke="currentColor" stroke-width="2.5" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" d="M9 12l2 2 4-4M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
                    </svg>
                </div>
                
                <div class="space-y-1">
                    <h3 class="text-lg font-black tracking-tight text-slate-900">Certificate Downloaded!</h3>
                    <p class="text-xs text-slate-400">High-resolution certificate rendered and downloaded to your device.</p>
                </div>

                <div class="bg-slate-50 border border-slate-200 p-4 rounded-2xl text-left space-y-2 font-medium text-xs">
                    <div class="flex justify-between border-b border-slate-200 pb-2">
                        <span class="text-slate-400 font-semibold">Student Name:</span>
                        <span id="modal-student-name" class="font-extrabold text-slate-900 uppercase text-xs"></span>
                    </div>
                    <div class="flex justify-between pt-1">
                        <span class="text-slate-400 font-semibold">Certificate ID:</span>
                        <span id="modal-cert-id" class="font-mono font-black text-amber-700"></span>
                    </div>
                </div>

                <button onclick="closeSuccessModal()" class="w-full bg-red-900 hover:bg-red-950 text-white py-3 px-4 rounded-xl font-black tracking-widest uppercase transition-all text-xs shadow-md">
                    GOT IT
                </button>
            </div>
        </div>
    </div>

    <!-- Offscreen HD Rendering Frame (1123px x 794px) -->
    <div class="absolute" style="top: -9999px; left: -9999px; width: 1123px; height: 794px;">
        <div id="cert-render-node" class="certificate-canvas">
            <div class="cert-border-outer">
                <div class="corner-accent c-tl"></div>
                <div class="corner-accent c-tr"></div>
                <div class="corner-accent c-bl"></div>
                <div class="corner-accent c-br"></div>
                
                <div id="render-cert-inner-bg" class="cert-border-inner">
                    <!-- Logos -->
                    <div class="grid grid-cols-3 items-center px-4 mt-1 w-full h-28">
                        <div class="flex justify-start h-28 items-center">
                            <img id="render-logo-1" src="" class="h-24 w-auto object-contain hidden max-w-[200px]">
                        </div>
                        <div class="flex justify-center h-28 items-center">
                            <img id="render-logo-2" src="" class="h-24 w-auto object-contain hidden max-w-[200px]">
                        </div>
                        <div class="flex justify-end h-28 items-center">
                            <img id="render-logo-3" src="" class="h-24 w-auto object-contain hidden max-w-[200px]">
                        </div>
                    </div>

                    <!-- Title -->
                    <div class="mt-2">
                        <h1 id="render-cert-title" class="text-[40px] font-cinzel font-black tracking-wider uppercase leading-none" style="color: var(--cert-border-color, #450a0a)">Certificate of Participation</h1>
                        <div class="mt-3 flex justify-center">
                            <p id="render-cert-subtext" class="text-2xl font-playfair italic text-slate-600">This is to certify that</p>
                        </div>
                    </div>

                    <!-- Name & Body Text -->
                    <div class="my-auto py-3">
                        <div class="relative inline-block px-12 py-2">
                            <h2 id="render-name" class="text-[48px] font-extrabold font-cinzel uppercase tracking-widest leading-none inline-block min-w-[450px] color-gold">
                                &nbsp;
                            </h2>
                        </div>
                        
                        <div id="render-body-container" class="text-[15px] leading-relaxed max-w-4xl mx-auto text-slate-700 font-montserrat font-medium mt-4 px-6">
                            <!-- Dynamic Content -->
                        </div>
                    </div>

                    <!-- Signatures & Barcode -->
                    <div class="grid grid-cols-3 items-end pt-2 mb-1">
                        
                        <!-- Left Signature -->
                        <div class="flex flex-col items-center justify-end h-28 text-center">
                            <div class="h-16 flex items-center justify-center mb-1">
                                <img id="render-sig-1" src="" class="h-14 w-auto object-contain hidden max-w-[200px]">
                            </div>
                            <div class="border-t-2 border-slate-300 w-4/5 pt-1 text-[11px] font-bold uppercase tracking-wider text-slate-800 leading-tight">
                                <span id="render-sig1-desig">Teacher-In-Charge</span><br>
                                <span id="render-sig1-sub" class="text-slate-500 text-[9px] block font-semibold mt-0.5">RISHI BANKIM CHANDRA<br>EVENING COLLEGE</span>
                            </div>
                        </div>

                        <!-- Center Barcode & ID -->
                        <div class="flex flex-col items-center justify-center">
                            <div id="render-barcode-svg" class="mb-1 opacity-90"></div>
                            <div class="text-[13px] text-slate-900 font-black font-mono tracking-wider">
                                ID: <span id="render-id-tag-val" class="color-gold font-black">RBCEC/DAC/NSS/001</span>
                            </div>
                            <span id="render-date-tag" class="text-[9.5px] text-slate-500 font-extrabold uppercase tracking-wider mt-0.5">Date: 20/07/2026</span>
                        </div>

                        <!-- Right Signature -->
                        <div class="flex flex-col items-center justify-end h-28 text-center">
                            <div class="h-16 flex items-center justify-center mb-1">
                                <img id="render-sig-2" src="" class="h-14 w-auto object-contain hidden max-w-[200px]">
                            </div>
                            <div class="border-t-2 border-slate-300 w-4/5 pt-1 text-[11px] font-bold uppercase tracking-wider text-slate-800 leading-tight">
                                <span id="render-sig2-desig">Programme Officer</span><br>
                                <span id="render-sig2-sub" class="text-slate-500 text-[9px] block font-semibold mt-0.5">NSS UNIT - I<br>RISHI BANKIM CHANDRA<br>EVENING COLLEGE</span>
                            </div>
                        </div>

                    </div>
                </div>
            </div>
        </div>
    </div>

    <!-- Footer -->
    <footer class="bg-white border-t border-slate-200 text-slate-400 text-xs py-6 mt-12">
        <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 text-center space-y-1">
            <p class="font-extrabold text-red-900 tracking-wider text-[10px] uppercase font-montserrat" id="footer-college-tag">
                National Service Scheme (NSS) Unit–I &bull; Rishi Bankim Chandra Evening College
            </p>
            <p class="text-[10px] font-montserrat">
                Authorized digital credentials portal. All rights reserved.
            </p>
        </div>
    </footer>

    <!-- Script Logic -->
    <script>
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'default-app-id';
        const ADMIN_SESSION_KEY = 'nss_final_admin_session_v12';

        // Firebase Configuration
        const firebaseConfig = JSON.parse(__firebase_config);
        const app = firebase.initializeApp(firebaseConfig);
        const auth = firebase.auth();
        const db = firebase.firestore();

        // Dynamic Configuration
        let portalConfig = {
            portalTitle: "OFFICIAL CERTIFICATE PORTAL",
            portalSubHeader: "Rishi Bankim Chandra Evening College • NSS Unit-I",
            certTitle: "Certificate of Participation",
            certSubtext: "This is to certify that",
            idPrefix: "RBCEC/DAC/NSS/",
            startIndex: 1,
            collegeOrganizer: "Rishi Bankim Chandra Evening College",
            nssUnit: "NSS Unit–I",
            eventName: "Dengue Awareness Campaign",
            eventDate: "20/07/2026",
            bodyText: "has successfully participated in the event organized by the college NSS unit. In recognition of active participation and valuable contribution towards social responsibility.",
            sig1Desig: "Teacher-In-Charge",
            sig1Sub: "RISHI BANKIM CHANDRA\nEVENING COLLEGE",
            sig2Desig: "Programme Officer",
            sig2Sub: "NSS UNIT - I\nRISHI BANKIM CHANDRA\nEVENING COLLEGE",
            borderColor: "#450a0a",
            accentColor: "#bfa15f",
            nameFontColorClass: "color-gold",
            adminPass: "1947",
            isLocked: false
        };

        let registryList = [];
        let loadedAssets = {};
        let isFormActive = true;
        let isAdminLoggedIn = localStorage.getItem(ADMIN_SESSION_KEY) === 'true';

        // Theme Color Setting
        function setThemeColor(border, accent) {
            portalConfig.borderColor = border;
            portalConfig.accentColor = accent;

            document.documentElement.style.setProperty('--cert-border-color', border);
            document.documentElement.style.setProperty('--cert-accent-color', accent);

            saveAndApplyTextConfig();
        }

        // Student Name & ID Font Color Setter
        function setNameFontColor(colorClass) {
            portalConfig.nameFontColorClass = colorClass;
            applyFontColorToUI(colorClass);
            saveAndApplyTextConfig();
        }

        function applyFontColorToUI(colorClass) {
            const certNameEl = document.getElementById('cert-name');
            const certIdEl = document.getElementById('cert-id-tag-val');
            const renderNameEl = document.getElementById('render-name');
            const renderIdEl = document.getElementById('render-id-tag-val');

            const allColorClasses = ['color-gold', 'color-shimmer-gold', 'color-ruby', 'color-emerald', 'color-sapphire', 'color-purple', 'color-amber', 'color-charcoal', 'color-shimmer-rainbow'];
            
            allColorClasses.forEach(c => {
                certNameEl.classList.remove(c);
                certIdEl.classList.remove(c);
                renderNameEl.classList.remove(c);
                renderIdEl.classList.remove(c);
            });

            certNameEl.classList.add(colorClass);
            certIdEl.classList.add(colorClass);
            renderNameEl.classList.add(colorClass);
            renderIdEl.classList.add(colorClass);
        }

        // Transparent PNG / Image Processor & Compressor (Max 200KB limit preservation)
        function processAndCompressImage(file) {
            return new Promise((resolve, reject) => {
                const reader = new FileReader();
                reader.onerror = () => reject(new Error("Failed to read image file."));
                reader.onload = (e) => {
                    const img = new Image();
                    img.onerror = () => reject(new Error("Invalid image format."));
                    img.onload = () => {
                        const canvas = document.createElement('canvas');
                        let width = img.width;
                        let height = img.height;
                        const maxBound = 600;

                        if (width > maxBound || height > maxBound) {
                            if (width > height) {
                                height = Math.round((height * maxBound) / width);
                                width = maxBound;
                            } else {
                                width = Math.round((width * maxBound) / height);
                                height = maxBound;
                            }
                        }

                        canvas.width = width;
                        canvas.height = height;
                        const ctx = canvas.getContext('2d');
                        ctx.clearRect(0, 0, width, height);
                        ctx.drawImage(img, 0, 0, width, height);
                        
                        // Preserve transparency for PNG if background removed, otherwise compress to JPEG if needed
                        let mimeType = file.type === 'image/png' ? 'image/png' : 'image/jpeg';
                        let quality = 0.85;
                        let compressedBase64 = canvas.toDataURL(mimeType, quality);
                        
                        while (compressedBase64.length > 270000 && quality > 0.3) {
                            quality -= 0.15;
                            compressedBase64 = canvas.toDataURL(mimeType, quality);
                        }

                        resolve(compressedBase64);
                    };
                    img.src = e.target.result;
                };
                reader.readAsDataURL(file);
            });
        }

        // Single Asset Upload
        async function uploadSingleAsset(key, inputId) {
            const fileInput = document.getElementById(inputId);
            if (!fileInput || !fileInput.files || !fileInput.files[0]) {
                showToast("File Not Found", "Please select an image file first.", true);
                return;
            }

            const file = fileInput.files[0];
            showToast("Processing Image", "Optimizing image with transparency & compression...", false);

            try {
                const optimizedBase64 = await processAndCompressImage(file);
                showToast("Saving to Cloud", "Writing asset to database...", false);

                const assetsRef = db.collection('artifacts').doc(appId).collection('public').doc('data').collection('settings').doc('assets');
                await assetsRef.set({ [key]: optimizedBase64 }, { merge: true });
                
                fileInput.value = '';
                showToast("Upload Successful", "Asset successfully saved and aligned!");
            } catch (err) {
                console.error("Upload Error:", err);
                showToast("Upload Failed", err.message || "Failed to process image.", true);
            }
        }

        // Delete Asset
        async function deleteSingleAsset(key) {
            if(!confirm(`Are you sure you want to delete this asset?`)) return;

            showToast("Deleting Asset", "Removing image from cloud...", false);
            const assetsRef = db.collection('artifacts').doc(appId).collection('public').doc('data').collection('settings').doc('assets');

            try {
                await assetsRef.set({ [key]: firebase.firestore.FieldValue.delete() }, { merge: true });
                removeAssetFromUI(key);
                showToast("Deleted", "Asset removed successfully!");
            } catch (err) {
                console.error("Delete Error:", err);
                showToast("Error", "Could not delete asset.", true);
            }
        }

        function removeAssetFromUI(type) {
            delete loadedAssets[type];

            if (type === 'portalLogo') {
                document.getElementById('nav-portal-logo').classList.add('hidden');
                document.getElementById('nav-portal-placeholder').classList.remove('hidden');
                document.getElementById('thumb-portal-logo').classList.add('hidden');
                document.getElementById('thumb-portal-logo-empty').classList.remove('hidden');
            }
            if (type === 'logo1') {
                document.getElementById('cert-logo-1').classList.add('hidden');
                document.getElementById('render-logo-1').classList.add('hidden');
                document.getElementById('thumb-logo-1').classList.add('hidden');
                document.getElementById('thumb-logo-1-empty').classList.remove('hidden');
            }
            if (type === 'logo2') {
                document.getElementById('cert-logo-2').classList.add('hidden');
                document.getElementById('render-logo-2').classList.add('hidden');
                document.getElementById('thumb-logo-2').classList.add('hidden');
                document.getElementById('thumb-logo-2-empty').classList.remove('hidden');
            }
            if (type === 'logo3') {
                document.getElementById('cert-logo-3').classList.add('hidden');
                document.getElementById('render-logo-3').classList.add('hidden');
                document.getElementById('thumb-logo-3').classList.add('hidden');
                document.getElementById('thumb-logo-3-empty').classList.remove('hidden');
            }
            if (type === 'sig1') {
                document.getElementById('cert-sig-1').classList.add('hidden');
                document.getElementById('render-sig-1').classList.add('hidden');
                document.getElementById('thumb-sig-1').classList.add('hidden');
                document.getElementById('thumb-sig-1-empty').classList.remove('hidden');
            }
            if (type === 'sig2') {
                document.getElementById('cert-sig-2').classList.add('hidden');
                document.getElementById('render-sig-2').classList.add('hidden');
                document.getElementById('thumb-sig-2').classList.add('hidden');
                document.getElementById('thumb-sig-2-empty').classList.remove('hidden');
            }
            if (type === 'bgImg') {
                document.getElementById('live-cert-inner-bg').style.backgroundImage = 'none';
                document.getElementById('render-cert-inner-bg').style.backgroundImage = 'none';
                document.getElementById('thumb-bg-img').classList.add('hidden');
                document.getElementById('thumb-bg-img-empty').classList.remove('hidden');
            }
        }

        function applyAssetToUI(type, base64Data) {
            if (!base64Data) {
                removeAssetFromUI(type);
                return;
            }
            loadedAssets[type] = base64Data;

            if (type === 'portalLogo') {
                document.getElementById('nav-portal-logo').src = base64Data;
                document.getElementById('thumb-portal-logo').src = base64Data;
                document.getElementById('nav-portal-logo').classList.remove('hidden');
                document.getElementById('nav-portal-placeholder').classList.add('hidden');
                document.getElementById('thumb-portal-logo').classList.remove('hidden');
                document.getElementById('thumb-portal-logo-empty').classList.add('hidden');
            }
            if (type === 'logo1') {
                document.getElementById('cert-logo-1').src = base64Data;
                document.getElementById('render-logo-1').src = base64Data;
                document.getElementById('thumb-logo-1').src = base64Data;

                document.getElementById('cert-logo-1').classList.remove('hidden');
                document.getElementById('render-logo-1').classList.remove('hidden');
                document.getElementById('thumb-logo-1').classList.remove('hidden');
                document.getElementById('thumb-logo-1-empty').classList.add('hidden');
            }
            if (type === 'logo2') {
                document.getElementById('cert-logo-2').src = base64Data;
                document.getElementById('render-logo-2').src = base64Data;
                document.getElementById('thumb-logo-2').src = base64Data;

                document.getElementById('cert-logo-2').classList.remove('hidden');
                document.getElementById('render-logo-2').classList.remove('hidden');
                document.getElementById('thumb-logo-2').classList.remove('hidden');
                document.getElementById('thumb-logo-2-empty').classList.add('hidden');
            }
            if (type === 'logo3') {
                document.getElementById('cert-logo-3').src = base64Data;
                document.getElementById('render-logo-3').src = base64Data;
                document.getElementById('thumb-logo-3').src = base64Data;

                document.getElementById('cert-logo-3').classList.remove('hidden');
                document.getElementById('render-logo-3').classList.remove('hidden');
                document.getElementById('thumb-logo-3').classList.remove('hidden');
                document.getElementById('thumb-logo-3-empty').classList.add('hidden');
            }
            if (type === 'sig1') {
                document.getElementById('cert-sig-1').src = base64Data;
                document.getElementById('render-sig-1').src = base64Data;
                document.getElementById('thumb-sig-1').src = base64Data;

                document.getElementById('cert-sig-1').classList.remove('hidden');
                document.getElementById('render-sig-1').classList.remove('hidden');
                document.getElementById('thumb-sig-1').classList.remove('hidden');
                document.getElementById('thumb-sig-1-empty').classList.add('hidden');
            }
            if (type === 'sig2') {
                document.getElementById('cert-sig-2').src = base64Data;
                document.getElementById('render-sig-2').src = base64Data;
                document.getElementById('thumb-sig-2').src = base64Data;

                document.getElementById('cert-sig-2').classList.remove('hidden');
                document.getElementById('render-sig-2').classList.remove('hidden');
                document.getElementById('thumb-sig-2').classList.remove('hidden');
                document.getElementById('thumb-sig-2-empty').classList.add('hidden');
            }
            if (type === 'bgImg') {
                document.getElementById('live-cert-inner-bg').style.backgroundImage = `url('${base64Data}')`;
                document.getElementById('render-cert-inner-bg').style.backgroundImage = `url('${base64Data}')`;
                document.getElementById('thumb-bg-img').src = base64Data;

                document.getElementById('thumb-bg-img').classList.remove('hidden');
                document.getElementById('thumb-bg-img-empty').classList.add('hidden');
            }
        }

        function isNumberKey(evt) {
            var charCode = (evt.which) ? evt.which : evt.keyCode;
            if (charCode > 31 && (charCode < 48 || charCode > 57)) return false;
            return true;
        }

        const initAuth = async () => {
            if (typeof __initial_auth_token !== 'undefined' && __initial_auth_token) {
                await auth.signInWithCustomToken(__initial_auth_token);
            } else {
                await auth.signInAnonymously();
            }
        };

        const syncDatabase = () => {
            const user = auth.currentUser;
            if (!user) return;

            const registrationsRef = db.collection('artifacts').doc(appId).collection('public').doc('data').collection('registrations');
            registrationsRef.onSnapshot((querySnapshot) => {
                const tempRegistry = [];
                querySnapshot.forEach((doc) => {
                    tempRegistry.push({ id: doc.id, ...doc.data() });
                });
                
                tempRegistry.sort((a, b) => {
                    const idA = parseInt((a.certId || '0').split('/').pop()) || 0;
                    const idB = parseInt((b.certId || '0').split('/').pop()) || 0;
                    return idA - idB;
                });

                registryList = tempRegistry;
                renderDatabaseTable();
                initPreviewDefaults();
            });

            // Settings Snapshot
            const configRef = db.collection('artifacts').doc(appId).collection('public').doc('data').collection('settings').doc('config');
            configRef.onSnapshot((doc) => {
                if (doc.exists) {
                    const data = doc.data();
                    portalConfig = {
                        portalTitle: data.portalTitle || portalConfig.portalTitle,
                        portalSubHeader: data.portalSubHeader || portalConfig.portalSubHeader,
                        certTitle: data.certTitle || portalConfig.certTitle,
                        certSubtext: data.certSubtext || portalConfig.certSubtext,
                        idPrefix: data.idPrefix || portalConfig.idPrefix,
                        startIndex: data.startIndex !== undefined ? data.startIndex : portalConfig.startIndex,
                        collegeOrganizer: data.collegeOrganizer || portalConfig.collegeOrganizer,
                        nssUnit: data.nssUnit || portalConfig.nssUnit,
                        eventName: data.eventName || portalConfig.eventName,
                        eventDate: data.eventDate || portalConfig.eventDate,
                        bodyText: data.bodyText || portalConfig.bodyText,
                        sig1Desig: data.sig1Desig || portalConfig.sig1Desig,
                        sig1Sub: data.sig1Sub || portalConfig.sig1Sub,
                        sig2Desig: data.sig2Desig || portalConfig.sig2Desig,
                        sig2Sub: data.sig2Sub || portalConfig.sig2Sub,
                        borderColor: data.borderColor || portalConfig.borderColor,
                        accentColor: data.accentColor || portalConfig.accentColor,
                        nameFontColorClass: data.nameFontColorClass || portalConfig.nameFontColorClass,
                        adminPass: data.adminPass || portalConfig.adminPass,
                        isLocked: data.isLocked || false
                    };
                    
                    document.documentElement.style.setProperty('--cert-border-color', portalConfig.borderColor);
                    document.documentElement.style.setProperty('--cert-accent-color', portalConfig.accentColor);

                    isFormActive = !portalConfig.isLocked;
                    updateStatus();
                    applyTextConfigToUI();
                    applyFontColorToUI(portalConfig.nameFontColorClass);
                    initPreviewDefaults();
                } else {
                    configRef.set(portalConfig).catch(e => console.log("Init settings failed:", e));
                }
            });

            // Assets Snapshot
            const assetsRef = db.collection('artifacts').doc(appId).collection('public').doc('data').collection('settings').doc('assets');
            assetsRef.onSnapshot((doc) => {
                if (doc.exists) {
                    const cloudAssets = doc.data();
                    const types = ['portalLogo', 'logo1', 'logo2', 'logo3', 'sig1', 'sig2', 'bgImg'];
                    types.forEach(type => {
                        if (cloudAssets[type]) {
                            applyAssetToUI(type, cloudAssets[type]);
                        } else {
                            removeAssetFromUI(type);
                        }
                    });
                } else {
                    ['portalLogo', 'logo1', 'logo2', 'logo3', 'sig1', 'sig2', 'bgImg'].forEach(type => removeAssetFromUI(type));
                }
            });
        };

        function applyTextConfigToUI() {
            document.getElementById('nav-main-title').innerText = portalConfig.portalTitle;
            document.getElementById('nav-sub-header').innerText = portalConfig.portalSubHeader;

            document.getElementById('preview-cert-title').innerText = portalConfig.certTitle;
            document.getElementById('render-cert-title').innerText = portalConfig.certTitle;

            document.getElementById('preview-cert-subtext').innerText = portalConfig.certSubtext;
            document.getElementById('render-cert-subtext').innerText = portalConfig.certSubtext;

            const formattedBodyHTML = `
                has successfully participated in the 
                <strong class="font-extrabold block text-[18px] my-1 uppercase" style="color: var(--cert-border-color, #450a0a)">"${portalConfig.eventName}"</strong> 
                organized by <strong class="font-bold" style="color: var(--cert-border-color, #450a0a)">${portalConfig.collegeOrganizer}</strong>, 
                <strong class="font-bold" style="color: var(--cert-border-color, #450a0a)">${portalConfig.nssUnit}</strong>.<br>
                <span class="text-[13.5px] text-slate-600 italic font-medium block mt-2 px-4">${portalConfig.bodyText}</span>
            `;

            document.getElementById('preview-body-container').innerHTML = formattedBodyHTML;
            document.getElementById('render-body-container').innerHTML = formattedBodyHTML;

            document.getElementById('cert-date-tag').innerText = `Date: ${portalConfig.eventDate}`;
            document.getElementById('render-date-tag').innerText = `Date: ${portalConfig.eventDate}`;

            document.getElementById('preview-sig1-desig').innerText = portalConfig.sig1Desig;
            document.getElementById('render-sig1-desig').innerText = portalConfig.sig1Desig;
            document.getElementById('preview-sig1-sub').innerHTML = portalConfig.sig1Sub.replace(/\n/g, '<br>');
            document.getElementById('render-sig1-sub').innerHTML = portalConfig.sig1Sub.replace(/\n/g, '<br>');

            document.getElementById('preview-sig2-desig').innerText = portalConfig.sig2Desig;
            document.getElementById('render-sig2-desig').innerText = portalConfig.sig2Desig;
            document.getElementById('preview-sig2-sub').innerHTML = portalConfig.sig2Sub.replace(/\n/g, '<br>');
            document.getElementById('render-sig2-sub').innerHTML = portalConfig.sig2Sub.replace(/\n/g, '<br>');

            document.getElementById('footer-college-tag').innerText = `${portalConfig.nssUnit} • ${portalConfig.collegeOrganizer}`;

            document.getElementById('config-portal-title').value = portalConfig.portalTitle;
            document.getElementById('config-portal-sub').value = portalConfig.portalSubHeader;
            document.getElementById('config-title').value = portalConfig.certTitle;
            document.getElementById('config-subtext').value = portalConfig.certSubtext;
            document.getElementById('config-prefix').value = portalConfig.idPrefix;
            document.getElementById('config-start-index').value = portalConfig.startIndex;
            document.getElementById('config-college').value = portalConfig.collegeOrganizer;
            document.getElementById('config-unit').value = portalConfig.nssUnit;
            document.getElementById('config-event').value = portalConfig.eventName;
            document.getElementById('config-date').value = portalConfig.eventDate;
            document.getElementById('config-body').value = portalConfig.bodyText;
            document.getElementById('config-sig1-desig').value = portalConfig.sig1Desig;
            document.getElementById('config-sig1-sub').value = portalConfig.sig1Sub;
            document.getElementById('config-sig2-desig').value = portalConfig.sig2Desig;
            document.getElementById('config-sig2-sub').value = portalConfig.sig2Sub;
        }

        async function saveAndApplyTextConfig() {
            const user = auth.currentUser;
            if (!user) return;

            showToast("Publishing Settings", "Syncing configuration with cloud...", false);
            const configRef = db.collection('artifacts').doc(appId).collection('public').doc('data').collection('settings').doc('config');
            
            try {
                await configRef.set({
                    portalTitle: document.getElementById('config-portal-title').value.trim(),
                    portalSubHeader: document.getElementById('config-portal-sub').value.trim(),
                    certTitle: document.getElementById('config-title').value.trim(),
                    certSubtext: document.getElementById('config-subtext').value.trim(),
                    idPrefix: document.getElementById('config-prefix').value.trim(),
                    startIndex: parseInt(document.getElementById('config-start-index').value) || 1,
                    collegeOrganizer: document.getElementById('config-college').value.trim(),
                    nssUnit: document.getElementById('config-unit').value.trim(),
                    eventName: document.getElementById('config-event').value.trim(),
                    eventDate: document.getElementById('config-date').value.trim(),
                    bodyText: document.getElementById('config-body').value.trim(),
                    sig1Desig: document.getElementById('config-sig1-desig').value.trim(),
                    sig1Sub: document.getElementById('config-sig1-sub').value.trim(),
                    sig2Desig: document.getElementById('config-sig2-desig').value.trim(),
                    sig2Sub: document.getElementById('config-sig2-sub').value.trim(),
                    borderColor: portalConfig.borderColor,
                    accentColor: portalConfig.accentColor,
                    nameFontColorClass: portalConfig.nameFontColorClass,
                    adminPass: portalConfig.adminPass,
                    isLocked: portalConfig.isLocked
                });
                showToast("Published Successfully", "Settings saved live!");
            } catch (err) {
                console.error("Failed to save config:", err);
                showToast("Update Failed", "Database write error.", true);
            }
        }

        async function changeAdminPassword() {
            const newPass = document.getElementById('new-admin-pass').value.trim();
            if (!newPass) {
                showToast("Empty Input", "Please enter a valid password.", true);
                return;
            }

            portalConfig.adminPass = newPass;
            await saveAndApplyTextConfig();
            document.getElementById('new-admin-pass').value = '';
            showToast("Password Updated", "New admin security password active!");
        }

        async function togglePortalState() {
            portalConfig.isLocked = !portalConfig.isLocked;
            await saveAndApplyTextConfig();
            showToast("Portal State Updated", `Portal is now ${portalConfig.isLocked ? 'LOCKED' : 'ACTIVE'}.`);
        }

        initAuth().then(() => {
            syncDatabase();
        });

        const nameField = document.getElementById('pName');
        nameField.addEventListener('keypress', function(e) {
            const charCode = e.charCode;
            const isValidEnglishChar = (charCode >= 65 && charCode <= 90) || 
                                       (charCode >= 97 && charCode <= 122) || 
                                       charCode === 32 || 
                                       charCode === 46; 

            if (!isValidEnglishChar) {
                e.preventDefault();
                showToast("Blocked", "Only English alphabets are permitted.", true);
            }
        });

        function toggleFormSegment(segment) {
            const btnReg = document.getElementById('segment-btn-register');
            const btnRet = document.getElementById('segment-btn-retrieve');
            const formReg = document.getElementById('form-segment-register');
            const formRet = document.getElementById('form-segment-retrieve');

            if (segment === 'register') {
                btnReg.className = "flex-1 py-3 text-center rounded-xl text-xs font-black tracking-widest uppercase transition-all duration-300 bg-red-900 text-white shadow-md";
                btnRet.className = "flex-1 py-3 text-center rounded-xl text-xs font-black tracking-widest uppercase transition-all duration-300 text-slate-500 hover:text-red-900";
                formReg.classList.remove('hidden');
                formRet.classList.add('hidden');
                initPreviewDefaults();
            } else {
                btnRet.className = "flex-1 py-3 text-center rounded-xl text-xs font-black tracking-widest uppercase transition-all duration-300 bg-red-900 text-white shadow-md";
                btnReg.className = "flex-1 py-3 text-center rounded-xl text-xs font-black tracking-widest uppercase transition-all duration-300 text-slate-500 hover:text-red-900";
                formRet.classList.remove('hidden');
                formReg.classList.add('hidden');
                handleLiveRetrievalSync(document.getElementById('rPhone').value);
            }
        }

        function handleLiveRetrievalSync(phoneNo) {
            const trimmedPhone = phoneNo.trim();
            const certNameEl = document.getElementById('cert-name');
            const certBarcodeBox = document.getElementById('cert-barcode-box');

            if (trimmedPhone.length === 10) {
                const matchedRecord = registryList.find(item => item.phone === trimmedPhone);
                if (matchedRecord) {
                    certNameEl.innerText = matchedRecord.name.toUpperCase();
                    document.getElementById('cert-id-tag-val').innerText = matchedRecord.certId;
                    document.getElementById('cert-barcode-svg').innerHTML = generateBarcodeSVG(matchedRecord.certId);
                    certBarcodeBox.classList.remove('invisible');
                    showToast("Record Found", `Matched profile for ${matchedRecord.name}.`, false);
                    return;
                }
            }
            certNameEl.innerHTML = "&nbsp;";
            certBarcodeBox.classList.add('invisible');
        }

        function handleRetrieval(e) {
            e.preventDefault();
            const phoneVal = document.getElementById('rPhone').value.trim();

            if (!/^[6789][0-9]{9}$/.test(phoneVal)) {
                showToast("Invalid Mobile Number", "Please enter a valid 10-digit Indian Mobile Number (starting with 6, 7, 8 or 9).", true);
                return;
            }

            const record = registryList.find(item => item.phone === phoneVal);
            if (!record) {
                showToast("Not Found", "No registered certificate found for this mobile number.", true);
                return;
            }

            document.getElementById('render-name').innerText = record.name.toUpperCase();
            document.getElementById('render-id-tag-val').innerText = record.certId;
            document.getElementById('render-barcode-svg').innerHTML = generateBarcodeSVG(record.certId);

            showToast("Generating", "Rendering high-resolution file...", false);
            const renderNode = document.getElementById('cert-render-node');

            setTimeout(() => {
                html2canvas(renderNode, {
                    scale: 2,
                    useCORS: true,
                    allowTaint: false,
                    logging: false,
                    backgroundColor: "#ffffff",
                    width: 1123,
                    height: 794
                }).then(canvas => {
                    const link = document.createElement('a');
                    const fileLabel = record.name.toLowerCase().replace(/[^a-z0-9]+/g, '_');
                    link.download = `Certificate_${fileLabel}.jpg`;
                    link.href = canvas.toDataURL("image/jpeg", 0.95);
                    link.click();

                    document.getElementById('retrievalForm').reset();
                    document.getElementById('cert-name').innerHTML = "&nbsp;";
                    document.getElementById('cert-barcode-box').classList.add('invisible');

                    openSuccessModal(record.name, record.certId);
                }).catch(err => {
                    showToast("Error", "Certificate build failed.", true);
                    console.error("Canvas error: ", err);
                });
            }, 300);
        }

        function getFormattedTimestamp() {
            const date = new Date();
            const day = String(date.getDate()).padStart(2, '0');
            const month = String(date.getMonth() + 1).padStart(2, '0');
            const year = date.getFullYear();
            
            let hours = date.getHours();
            const minutes = String(date.getMinutes()).padStart(2, '0');
            const ampm = hours >= 12 ? 'PM' : 'AM';
            hours = hours % 12;
            hours = hours ? hours : 12; 
            const strTime = `${hours}:${minutes} ${ampm}`;
            
            return `${day}/${month}/${year} at ${strTime}`;
        }

        function generateBarcodeSVG(code) {
            let binary = "101011001101"; 
            for (let i = 0; i < code.length; i++) {
                const charCode = code.charCodeAt(i);
                binary += (charCode % 2 === 0) ? "11001010" : "10011010";
            }
            binary += "110110101"; 

            let svgContent = `<svg width="150" height="28" viewBox="0 0 ${binary.length} 28" xmlns="http://www.w3.org/2000/svg">`;
            for (let i = 0; i < binary.length; i++) {
                if (binary[i] === "1") {
                    svgContent += `<rect x="${i}" y="0" width="0.8" height="28" fill="#1e293b"/>`;
                }
            }
            svgContent += `</svg>`;
            return svgContent;
        }

        function handlePreviewScaling() {
            const scaleContainer = document.getElementById('cert-preview-scale');
            const wrapper = document.getElementById('preview-wrapper');
            const targetWidth = 1123;
            const targetHeight = 794;

            const availableWidth = wrapper.clientWidth;
            const scaleRatio = availableWidth / targetWidth;
            
            scaleContainer.style.transform = `scale(${scaleRatio})`;
            wrapper.style.height = `${targetHeight * scaleRatio}px`;
        }

        function showToast(title, message, isError = false) {
            const toast = document.getElementById('toast');
            const tTitle = document.getElementById('toast-title');
            const tMsg = document.getElementById('toast-message');
            const iconSuccess = document.getElementById('toast-icon-success');
            const iconError = document.getElementById('toast-icon-error');

            tTitle.innerText = title;
            tMsg.innerText = message;

            if (isError) {
                toast.classList.add('border-rose-500');
                toast.classList.remove('border-emerald-500');
                iconSuccess.classList.add('hidden');
                iconError.classList.remove('hidden');
            } else {
                toast.classList.add('border-emerald-500');
                toast.classList.remove('border-rose-500');
                iconSuccess.classList.remove('hidden');
                iconError.classList.add('hidden');
            }

            toast.classList.remove('-translate-y-48', 'opacity-0');
            toast.classList.add('translate-y-0', 'opacity-100');

            setTimeout(() => {
                toast.classList.remove('translate-y-0', 'opacity-100');
                toast.classList.add('-translate-y-48', 'opacity-0');
            }, 4000); 
        }

        function openSuccessModal(studentName, certId) {
            document.getElementById('modal-student-name').innerText = studentName;
            document.getElementById('modal-cert-id').innerText = certId;
            
            const modal = document.getElementById('success-modal');
            const panel = document.getElementById('success-modal-panel');
            
            modal.classList.remove('hidden');
            setTimeout(() => {
                panel.classList.remove('scale-95', 'opacity-0');
                panel.classList.add('scale-100', 'opacity-100');
            }, 50);
        }

        function closeSuccessModal() {
            const modal = document.getElementById('success-modal');
            const panel = document.getElementById('success-modal-panel');
            
            panel.classList.add('scale-95', 'opacity-0');
            panel.classList.remove('scale-100', 'opacity-100');
            setTimeout(() => {
                modal.classList.add('hidden');
            }, 300);
        }

        function switchTab(tabId) {
            document.querySelectorAll('.tab-content').forEach(el => el.classList.add('hidden'));
            
            document.querySelectorAll('.nav-tab').forEach(el => {
                el.classList.remove('bg-red-900', 'text-white', 'shadow-md', 'shadow-red-900/20');
                el.classList.add('text-slate-700', 'hover:bg-red-50');
            });

            if (tabId === 'portal') {
                document.getElementById('section-portal').classList.remove('hidden');
                document.getElementById('tab-portal').classList.add('bg-red-900', 'text-white', 'shadow-md', 'shadow-red-900/20');
                document.getElementById('tab-portal').classList.remove('text-slate-700', 'hover:bg-red-50');
                setTimeout(handlePreviewScaling, 100);
            } else if (tabId === 'admin') {
                if (isAdminLoggedIn) {
                    document.getElementById('section-admin').classList.remove('hidden');
                    document.getElementById('tab-admin').classList.add('bg-red-900', 'text-white', 'shadow-md', 'shadow-red-900/20');
                    document.getElementById('tab-admin').classList.remove('text-slate-700', 'hover:bg-red-50');
                    renderDatabaseTable();
                } else {
                    openAdminModal();
                }
            }
        }

        function updateStatus() {
            const banner = document.getElementById('status-banner');
            const submitBtn = document.getElementById('submit-btn');
            const pName = document.getElementById('pName');
            const pPhone = document.getElementById('pPhone');
            const toggleBtn = document.getElementById('btn-toggle-portal');
            
            if (toggleBtn) {
                if (isFormActive) {
                    toggleBtn.className = "bg-rose-50 hover:bg-rose-900 border border-rose-200 text-rose-800 hover:text-white px-5 py-2.5 rounded-xl text-xs font-black tracking-widest transition-all duration-300 shadow-sm uppercase";
                    toggleBtn.innerText = "LOCK REGISTRATION";
                } else {
                    toggleBtn.className = "bg-emerald-50 hover:bg-emerald-900 border border-emerald-200 text-emerald-800 hover:text-white px-5 py-2.5 rounded-xl text-xs font-black tracking-widest transition-all duration-300 shadow-sm uppercase";
                    toggleBtn.innerText = "ACTIVATE REGISTRATION";
                }
            }

            if (isFormActive) {
                banner.className = "text-center p-3.5 rounded-2xl font-bold mb-8 bg-emerald-50/80 border border-emerald-200 text-emerald-950 shadow-sm flex items-center justify-center gap-2 text-xs tracking-wider uppercase";
                banner.innerHTML = `
                    <span class="w-2.5 h-2.5 rounded-full bg-emerald-600 animate-ping"></span>
                    <span>PORTAL ACTIVE: ENTER YOUR NAME AND ISSUE CERTIFICATE</span>
                `;
                submitBtn.disabled = false;
                submitBtn.className = "w-full bg-red-900 hover:bg-red-950 text-white py-4 px-4 rounded-xl font-black tracking-widest uppercase transition-all duration-300 shadow-lg shadow-red-950/10 flex items-center justify-center gap-2 text-xs cursor-pointer";
                pName.disabled = false;
                pPhone.disabled = false;
            } else {
                banner.className = "text-center p-3.5 rounded-2xl font-bold mb-8 bg-rose-50 border border-rose-200 text-rose-950 shadow-sm flex items-center justify-center gap-2 text-xs tracking-wider uppercase";
                banner.innerHTML = `
                    <svg class="w-4 h-4 text-rose-600" fill="none" stroke="currentColor" stroke-width="2.5" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" d="M12 9v2m0 4h.01m-6.938 4h13.856c1.54 0 2.502-1.667 1.732-3L13.732 4c-.77-1.333-2.694-1.333-3.464 0L3.34 16c-.77 1.333.192 3 1.732 3z"></path></svg>
                    <span>PORTAL STATE: LOCKED &bull; Certificate issuance is temporarily locked by admin</span>
                `;
                submitBtn.disabled = true;
                submitBtn.className = "w-full bg-slate-200 text-slate-400 py-4 px-4 rounded-xl font-black tracking-widest uppercase cursor-not-allowed transition-all duration-300 flex items-center justify-center gap-2 text-xs";
                pName.disabled = true;
                pPhone.disabled = true;
            }
        }

        function syncPreviewName(val) {
            const cleanName = val.trim().toUpperCase();
            const certNameEl = document.getElementById('cert-name');
            const certBarcodeBox = document.getElementById('cert-barcode-box');
            
            if (cleanName === "") {
                certNameEl.innerHTML = "&nbsp;";
                certBarcodeBox.classList.add('invisible');
            } else {
                certNameEl.innerText = cleanName;
                certBarcodeBox.classList.remove('invisible');
            }
        }

        function generateNextID() {
            const prefix = portalConfig.idPrefix || "RBCEC/DAC/NSS/";
            const startIndex = parseInt(portalConfig.startIndex) || 1;
            const count = registryList.length + startIndex;
            const paddedNum = String(count).padStart(3, '0');
            return `${prefix}${paddedNum}`; 
        }

        function handleRegistrationSubmit(e) {
            e.preventDefault();
            if (!isFormActive) {
                showToast("Locked", "Registration is currently closed.", true);
                return;
            }

            const name = document.getElementById('pName').value.trim().toUpperCase();
            const phone = document.getElementById('pPhone').value.trim();

            if (name === "" || phone === "") {
                showToast("Empty Input", "Please fill in all details.", true);
                return;
            }

            if (!/^[6789][0-9]{9}$/.test(phone)) {
                showToast("Invalid Mobile Number", "Enter a valid 10-digit Indian Mobile Number (starting with 6, 7, 8 or 9).", true);
                return;
            }

            const phoneExists = registryList.some(item => item.phone === phone);
            if (phoneExists) {
                showToast("Already Registered", "This mobile number has already generated a certificate.", true);
                return;
            }

            const certId = generateNextID();
            const preciseTime = getFormattedTimestamp();

            showToast("Connecting", "Saving details to Firestore...", false);

            const user = auth.currentUser;
            if (!user) {
                showToast("Error", "Server connection timed out.", true);
                return;
            }

            const collectionRef = db.collection('artifacts').doc(appId).collection('public').doc('data').collection('registrations');
            
            collectionRef.add({
                certId: certId,
                name: name,
                phone: phone,
                timestamp: preciseTime
            }).then(() => {
                showToast("Rendering Output", "Generating high-resolution certificate...", false);

                document.getElementById('render-name').innerText = name;
                document.getElementById('render-id-tag-val').innerText = certId;
                document.getElementById('render-barcode-svg').innerHTML = generateBarcodeSVG(certId);

                const renderNode = document.getElementById('cert-render-node');

                setTimeout(() => {
                    html2canvas(renderNode, {
                        scale: 2,
                        useCORS: true,
                        allowTaint: false,
                        logging: false,
                        backgroundColor: "#ffffff",
                        width: 1123,
                        height: 794
                    }).then(canvas => {
                        const link = document.createElement('a');
                        const fileLabel = name.toLowerCase().replace(/[^a-z0-9]+/g, '_');
                        link.download = `Certificate_${fileLabel}.jpg`;
                        link.href = canvas.toDataURL("image/jpeg", 0.95);
                        link.click();

                        document.getElementById('enrollmentForm').reset();
                        document.getElementById('cert-name').innerHTML = "&nbsp;";
                        document.getElementById('cert-barcode-box').classList.add('invisible');

                        openSuccessModal(name, certId);
                    }).catch(err => {
                        showToast("Render Failure", "Compilation timed out.", true);
                        console.error("Canvas error:", err);
                    });
                }, 400);
            }).catch(err => {
                console.error("Registration save failed:", err);
                showToast("Save Failed", "Could not write record to database.", true);
            });
        }

        function openAdminModal() {
            const modal = document.getElementById('admin-login-modal');
            const panel = document.getElementById('login-modal-panel');
            modal.classList.remove('hidden');
            setTimeout(() => {
                panel.classList.remove('scale-95', 'opacity-0');
                panel.classList.add('scale-100', 'opacity-100');
                document.getElementById('admin-pass-input').focus();
            }, 50);
        }

        function closeAdminModal() {
            const modal = document.getElementById('admin-login-modal');
            const panel = document.getElementById('login-modal-panel');
            panel.classList.add('scale-95', 'opacity-0');
            panel.classList.remove('scale-100', 'opacity-100');
            setTimeout(() => {
                modal.classList.add('hidden');
                document.getElementById('admin-pass-input').value = "";
            }, 300);
        }

        function handleAdminLogin() {
            const password = document.getElementById('admin-pass-input').value;
            const validPass = portalConfig.adminPass || "1947";

            if (password === validPass) {
                isAdminLoggedIn = true;
                localStorage.setItem(ADMIN_SESSION_KEY, 'true');
                closeAdminModal();
                showToast("Authorized", "Admin console unlocked.", false);
                switchTab('admin');
            } else {
                showToast("Access Denied", "Incorrect Security PIN Code.", true);
            }
        }

        function logoutAdmin() {
            isAdminLoggedIn = false;
            localStorage.removeItem(ADMIN_SESSION_KEY);
            showToast("Logged Out", "Admin console locked.", false);
            switchTab('portal');
        }

        function renderDatabaseTable() {
            const tbody = document.getElementById('dbTableBody');
            const searchVal = document.getElementById('db-search').value.toLowerCase().trim();
            tbody.innerHTML = "";

            const filtered = registryList.filter(item => 
                (item.name || '').toLowerCase().includes(searchVal) ||
                (item.phone || '').includes(searchVal) ||
                (item.certId || '').toLowerCase().includes(searchVal)
            );

            if (filtered.length === 0) {
                tbody.innerHTML = `
                    <tr>
                        <td colspan="6" class="p-8 text-center text-slate-400 font-bold uppercase tracking-widest text-[10px]">
                            No matching registered logs found.
                        </td>
                    </tr>
                `;
                return;
            }

            filtered.slice().reverse().forEach((item, index) => {
                const tr = document.createElement('tr');
                tr.className = "hover:bg-slate-50 transition-colors border-b border-slate-100";
                tr.innerHTML = `
                    <td class="p-4 text-slate-400 font-bold">${filtered.length - index}</td>
                    <td class="p-4 font-mono font-black text-red-800">${item.certId}</td>
                    <td class="p-4 font-bold text-slate-800">
                        <span id="name-display-${item.id}">${item.name}</span>
                        <input type="text" id="name-edit-${item.id}" class="hidden p-2 bg-slate-50 border border-slate-200 rounded-xl text-xs uppercase text-slate-800 outline-none focus:ring-2 focus:ring-red-800 w-full" value="${item.name}">
                    </td>
                    <td class="p-4 font-medium text-slate-600">${item.phone}</td>
                    <td class="p-4 text-slate-400 font-bold font-mono text-[10px]">${item.timestamp}</td>
                    <td class="p-4 text-right space-x-1 whitespace-nowrap">
                        <button onclick="editMode('${item.id}', true)" id="btn-edit-${item.id}" class="bg-blue-50 hover:bg-blue-600 text-blue-600 hover:text-white px-3 py-1.5 rounded-lg font-bold transition text-xs">
                            Edit Name
                        </button>
                        <button onclick="saveEdit('${item.id}')" id="btn-save-${item.id}" class="hidden bg-emerald-800 text-white px-3 py-1.5 rounded-lg font-bold transition text-xs">
                            Save
                        </button>
                        <button onclick="downloadFromDB('${item.name}', '${item.certId}')" class="bg-slate-100 hover:bg-slate-200 text-slate-700 px-3 py-1.5 rounded-lg font-bold transition text-xs">
                            Redownload
                        </button>
                        <button onclick="deleteSingleRecord('${item.id}')" class="bg-rose-50 hover:bg-rose-600 text-rose-600 hover:text-white px-2.5 py-1.5 rounded-lg font-bold transition text-xs">
                            Delete
                        </button>
                    </td>
                `;
                tbody.appendChild(tr);
            });
        }

        function editMode(id, active) {
            const displaySpan = document.getElementById(`name-display-${id}`);
            const editInput = document.getElementById(`name-edit-${id}`);
            const btnEdit = document.getElementById(`btn-edit-${id}`);
            const btnSave = document.getElementById(`btn-save-${id}`);

            if (active) {
                displaySpan.classList.add('hidden');
                editInput.classList.remove('hidden');
                editInput.focus();
                btnEdit.classList.add('hidden');
                btnSave.classList.remove('hidden');
            } else {
                displaySpan.classList.remove('hidden');
                editInput.classList.add('hidden');
                btnEdit.classList.remove('hidden');
                btnSave.classList.add('hidden');
            }
        }

        async function saveEdit(id) {
            const editInput = document.getElementById(`name-edit-${id}`);
            const newName = editInput.value.trim().toUpperCase();

            if (!/^[a-zA-Z\s\.]+$/.test(newName)) {
                showToast("Invalid Entry", "English letters and spaces only.", true);
                return;
            }

            const docRef = db.collection('artifacts').doc(appId).collection('public').doc('data').collection('registrations').doc(id);
            try {
                await docRef.update({ name: newName });
                showToast("Success", "Record updated successfully.");
            } catch (err) {
                showToast("Update Failed", "Synchronization error.", true);
            }
        }

        async function deleteSingleRecord(id) {
            if(!confirm("Are you sure you want to delete this student record?")) return;

            try {
                await db.collection('artifacts').doc(appId).collection('public').doc('data').collection('registrations').doc(id).delete();
                showToast("Deleted", "Student record removed.");
            } catch (err) {
                showToast("Delete Failed", "Database operation failed.", true);
            }
        }

        function openClearDataAuthModal() {
            if (registryList.length === 0) {
                showToast("Empty Ledger", "There are no records to delete.", true);
                return;
            }
            const modal = document.getElementById('clear-data-modal');
            const panel = document.getElementById('clear-data-panel');
            modal.classList.remove('hidden');
            setTimeout(() => {
                panel.classList.remove('scale-95', 'opacity-0');
                panel.classList.add('scale-100', 'opacity-100');
                document.getElementById('clear-auth-pin').focus();
            }, 50);
        }

        function closeClearDataModal() {
            const modal = document.getElementById('clear-data-modal');
            const panel = document.getElementById('clear-data-panel');
            panel.classList.add('scale-95', 'opacity-0');
            panel.classList.remove('scale-100', 'opacity-100');
            setTimeout(() => {
                modal.classList.add('hidden');
                document.getElementById('clear-auth-pin').value = "";
            }, 300);
        }

        async function confirmClearAllStudentRecords() {
            const enteredPin = document.getElementById('clear-auth-pin').value.trim();
            const validPass = portalConfig.adminPass || "1947";

            if (enteredPin !== validPass) {
                showToast("Security Violation", "Incorrect Admin PIN Code! Erase request denied.", true);
                return;
            }

            closeClearDataModal();
            showToast("Erasing Database", "Clearing all records from Firestore...", false);

            try {
                const snapshot = await db.collection('artifacts').doc(appId).collection('public').doc('data').collection('registrations').get();
                
                if (snapshot.empty) {
                    showToast("Empty Database", "No student records found to delete.");
                    return;
                }

                const batchSize = 500;
                let batch = db.batch();
                let count = 0;

                for (const doc of snapshot.docs) {
                    batch.delete(doc.ref);
                    count++;
                    if (count % batchSize === 0) {
                        await batch.commit();
                        batch = db.batch();
                    }
                }

                if (count % batchSize !== 0) {
                    await batch.commit();
                }

                showToast("Ledger Cleared", "All student records have been permanently erased.");
            } catch (err) {
                console.error("Clear error:", err);
                showToast("Clear Failed", "Could not complete database erase.", true);
            }
        }

        function exportDatabaseToCSV() {
            if (registryList.length === 0) {
                showToast("No Data", "There are no student records to export.", true);
                return;
            }

            let csvContent = "data:text/csv;charset=utf-8,";
            csvContent += "Sl,Certificate ID,Student Name,Mobile Number,Timestamp\n";

            registryList.forEach((item, index) => {
                const row = [
                    index + 1,
                    `"${item.certId || ''}"`,
                    `"${item.name || ''}"`,
                    `"${item.phone || ''}"`,
                    `"${item.timestamp || ''}"`
                ].join(",");
                csvContent += row + "\n";
            });

            const encodedUri = encodeURI(csvContent);
            const link = document.createElement("a");
            link.setAttribute("href", encodedUri);
            link.setAttribute("download", `Student_Certificate_Ledger_${new Date().toISOString().slice(0, 10)}.csv`);
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
            showToast("Exported", "Student ledger exported as CSV file.");
        }

        function downloadFromDB(name, certId) {
            showToast("Compiling JPG", `Rebuilding output file...`);
            
            document.getElementById('render-name').innerText = name;
            document.getElementById('render-id-tag-val').innerText = certId;
            document.getElementById('render-barcode-svg').innerHTML = generateBarcodeSVG(certId);
            
            const renderNode = document.getElementById('cert-render-node');
            
            setTimeout(() => {
                html2canvas(renderNode, {
                    scale: 2,
                    useCORS: true,
                    allowTaint: false,
                    logging: false,
                    backgroundColor: "#ffffff",
                    width: 1123,
                    height: 794
                }).then(canvas => {
                    const link = document.createElement('a');
                    const fileLabel = name.toLowerCase().replace(/[^a-z0-9]+/g, '_');
                    link.download = `Certificate_${fileLabel}.jpg`;
                    link.href = canvas.toDataURL("image/jpeg", 0.95);
                    link.click();
                    showToast("Downloaded", "Certificate downloaded successfully.");
                });
            }, 300);
        }

        function initPreviewDefaults() {
            const nextID = generateNextID();
            
            document.getElementById('cert-id-tag-val').innerText = nextID;
            document.getElementById('cert-barcode-svg').innerHTML = generateBarcodeSVG(nextID);
            
            document.getElementById('render-id-tag-val').innerText = nextID;
            document.getElementById('render-barcode-svg').innerHTML = generateBarcodeSVG(nextID);
        }

        window.onload = function() {
            updateStatus();
            initPreviewDefaults();
            setTimeout(handlePreviewScaling, 150);
        };

        window.onresize = function() {
            handlePreviewScaling();
        };
    </script>
</body>
</html>
