```html
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>KiotPro - Premium POS & AI Chatbot</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Chart.js CDN -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;500;600;700;800&display=swap');
        body {
            font-family: 'Plus Jakarta Sans', sans-serif;
            -webkit-tap-highlight-color: transparent;
            background-color: #f8fafc; /* Slate 50 */
        }
        .no-scrollbar::-webkit-scrollbar { display: none; }
        .no-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }
        
        /* Custom scrollbar for desktop */
        @media (min-width: 768px) {
            ::-webkit-scrollbar { width: 6px; height: 6px; }
            ::-webkit-scrollbar-track { background: transparent; }
            ::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 10px; }
            ::-webkit-scrollbar-thumb:hover { background: #94a3b8; }
        }

        /* Glassmorphism */
        .glass {
            background: rgba(255, 255, 255, 0.7);
            backdrop-filter: blur(12px);
            -webkit-backdrop-filter: blur(12px);
            border: 1px solid rgba(255, 255, 255, 0.5);
        }
        
        .glass-dark {
            background: rgba(15, 23, 42, 0.75);
            backdrop-filter: blur(16px);
            -webkit-backdrop-filter: blur(16px);
            border: 1px solid rgba(255, 255, 255, 0.1);
        }

        /* Animations */
        .slide-up { animation: slideUp 0.3s cubic-bezier(0.16, 1, 0.3, 1) forwards; }
        @keyframes slideUp { from { transform: translateY(100%); } to { transform: translateY(0); } }
        
        .chat-pop { animation: chatPop 0.3s cubic-bezier(0.34, 1.56, 0.64, 1) forwards; transform-origin: bottom right; }
        @keyframes chatPop { from { opacity: 0; transform: scale(0.8) translateY(20px); } to { opacity: 1; transform: scale(1) translateY(0); } }

        .fade-in { animation: fadeIn 0.3s ease forwards; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(5px); } to { opacity: 1; transform: translateY(0); } }

        /* Typing Indicator */
        .typing-dot {
            width: 6px; height: 6px;
            background-color: #94a3b8;
            border-radius: 50%;
            display: inline-block;
            animation: typing 1.4s infinite ease-in-out both;
        }
        .typing-dot:nth-child(1) { animation-delay: -0.32s; }
        .typing-dot:nth-child(2) { animation-delay: -0.16s; }
        @keyframes typing {
            0%, 80%, 100% { transform: scale(0); }
            40% { transform: scale(1); }
        }

        /* Smooth shadow hover for cards */
        .card-hover {
            transition: all 0.3s ease;
        }
        .card-hover:hover {
            transform: translateY(-3px);
            box-shadow: 0 12px 30px -10px rgba(16, 185, 129, 0.15);
            border-color: #34d399;
        }
    </style>
</head>
<body class="text-slate-800 h-screen overflow-hidden flex">

    <!-- ==================== LOGIN SCREEN ==================== -->
    <div id="login-screen" class="fixed inset-0 bg-[url('https://images.unsplash.com/photo-1556742049-0cfed4f6a45d?ixlib=rb-1.2.1&auto=format&fit=crop&w=1950&q=80')] bg-cover bg-center z-[100] flex items-center justify-center p-4">
        <div class="absolute inset-0 bg-slate-900/70 backdrop-blur-sm"></div>
        <div class="glass-dark w-full max-w-sm rounded-[2rem] shadow-2xl p-8 space-y-8 relative z-10 text-white">
            <div class="text-center space-y-3">
                <div class="inline-block bg-white/10 px-5 py-2.5 rounded-2xl font-black text-3xl tracking-wider shadow-inner border border-white/20">
                    Kiot<span class="text-emerald-400">Pro</span>
                </div>
                <h2 class="text-lg font-medium text-slate-200">Hệ thống Quản trị Bán hàng</h2>
            </div>

            <form id="login-form" onsubmit="handleLogin(event)" class="space-y-5">
                <div>
                    <label class="block text-[11px] font-bold text-slate-400 uppercase tracking-wider mb-1.5">Tài khoản</label>
                    <input type="text" id="login-username" required placeholder="admin hoặc nhanvien" class="w-full px-5 py-3.5 bg-white/10 border border-white/10 rounded-2xl focus:outline-none focus:ring-2 focus:ring-emerald-400 focus:bg-white/20 text-sm text-white placeholder-slate-400 transition-all">
                </div>
                <div>
                    <label class="block text-[11px] font-bold text-slate-400 uppercase tracking-wider mb-1.5">Mật khẩu</label>
                    <input type="password" id="login-password" required placeholder="••••••••" class="w-full px-5 py-3.5 bg-white/10 border border-white/10 rounded-2xl focus:outline-none focus:ring-2 focus:ring-emerald-400 focus:bg-white/20 text-sm text-white placeholder-slate-400 transition-all">
                </div>
                <button type="submit" class="w-full bg-gradient-to-r from-emerald-500 to-teal-500 hover:from-emerald-400 hover:to-teal-400 active:scale-[0.98] text-white py-4 rounded-2xl font-bold text-sm shadow-[0_8px_20px_rgba(16,185,129,0.3)] transition-all flex justify-center items-center mt-4">
                    ĐĂNG NHẬP HỆ THỐNG
                </button>
            </form>
            <div class="border-t border-white/10 pt-5 text-center space-y-1">
                <p class="text-xs text-slate-400">Tài khoản thử nghiệm:</p>
                <div class="flex justify-center space-x-4">
                    <p class="text-xs font-medium"><span class="text-emerald-400">admin</span> / 123</p>
                    <p class="text-xs font-medium"><span class="text-emerald-400">nhanvien</span> / 123</p>
                </div>
            </div>
        </div>
    </div>

    <!-- ==================== DESKTOP SIDEBAR ==================== -->
    <aside id="desktop-sidebar" class="hidden md:flex flex-col w-[260px] bg-slate-900 text-white h-screen shrink-0 transition-all relative z-40 shadow-2xl">
        <div class="p-6 flex items-center space-x-3">
            <div class="bg-gradient-to-br from-emerald-400 to-teal-500 text-slate-900 px-3 py-1.5 rounded-xl font-black text-xl tracking-wide shadow-lg">
                KiotPro
            </div>
        </div>
        <div class="px-6 pb-6 border-b border-slate-800">
            <div class="bg-slate-800/50 p-3 rounded-2xl border border-slate-700/50 flex items-center space-x-3">
                <div class="w-10 h-10 rounded-full bg-emerald-500/20 text-emerald-400 flex items-center justify-center font-bold text-lg">
                    A
                </div>
                <div class="flex flex-col min-w-0">
                    <span class="text-sm font-bold text-white truncate" id="sidebar-user-name">Đang tải...</span>
                    <span class="text-[11px] text-emerald-400 mt-0.5 font-medium" id="sidebar-user-role">Đang tải...</span>
                </div>
            </div>
        </div>
        <nav class="flex-1 overflow-y-auto py-6 space-y-1.5 px-4" id="desktop-navigation-container"></nav>
        <div class="p-4 border-t border-slate-800">
            <button onclick="handleLogout()" class="flex items-center space-x-3 text-slate-400 hover:text-red-400 hover:bg-red-500/10 transition-colors w-full p-3 rounded-xl">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 16l4-4m0 0l-4-4m4 4H7m6 4v1a3 3 0 01-3 3H6a3 3 0 01-3-3V7a3 3 0 013-3h4a3 3 0 013 3v1" /></svg>
                <span class="font-bold text-sm">Đăng xuất</span>
            </button>
        </div>
    </aside>

    <!-- ==================== MAIN CONTENT ==================== -->
    <div class="flex-1 flex flex-col h-screen relative min-w-0 bg-[#f8fafc]">
        
        <!-- HEADER -->
        <header class="glass sticky top-0 z-30 shrink-0 px-4 py-3 md:px-8 md:py-4 flex justify-between items-center shadow-sm">
            <div class="flex md:hidden items-center">
                <div class="bg-emerald-600 text-white px-2.5 py-1 rounded-lg font-black text-sm tracking-wider">
                    KiotPro
                </div>
            </div>
            <div class="hidden md:block">
                <h1 class="text-2xl font-extrabold text-slate-800 tracking-tight" id="desktop-header-title">Bán hàng (POS)</h1>
            </div>
            <div class="flex items-center space-x-3">
                <span class="hidden md:inline-block text-xs bg-white border border-slate-200 text-slate-600 px-3 py-1.5 rounded-full font-semibold shadow-sm">
                    🟢 Đang trực tuyến
                </span>
                <button onclick="handleLogout()" class="md:hidden bg-slate-100 hover:bg-slate-200 p-2 rounded-xl text-slate-600 transition-colors">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 16l4-4m0 0l-4-4m4 4H7m6 4v1a3 3 0 01-3 3H6a3 3 0 01-3-3V7a3 3 0 013-3h4a3 3 0 013 3v1" /></svg>
                </button>
            </div>
        </header>

        <!-- MAIN SCROLLER -->
        <main class="flex-1 overflow-y-auto px-4 py-5 md:p-8 space-y-6 w-full max-w-[1400px] mx-auto pb-28 md:pb-8">

            <!-- TAB 1: POS -->
            <section id="section-pos" class="tab-content block space-y-5">
                <div class="bg-white p-2 md:p-3 rounded-2xl shadow-sm border border-slate-100 flex flex-col md:flex-row gap-3">
                    <div class="relative flex-1">
                        <span class="absolute inset-y-0 left-0 flex items-center pl-4 text-slate-400">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor"><path fill-rule="evenodd" d="M8 4a4 4 0 100 8 4 4 0 000-8zM2 8a6 6 0 1110.89 3.476l4.817 4.817a1 1 0 01-1.414 1.414l-4.816-4.816A6 6 0 012 8z" clip-rule="evenodd" /></svg>
                        </span>
                        <input type="text" id="pos-search" oninput="renderPosProducts()" placeholder="Tìm theo tên máy, bộ nhớ, màu sắc..." class="w-full pl-11 pr-4 py-3 bg-slate-50 border-transparent rounded-xl focus:bg-white focus:outline-none focus:ring-2 focus:ring-emerald-500 text-sm transition-all font-medium">
                    </div>
                    <div class="flex space-x-2 overflow-x-auto no-scrollbar py-1 shrink-0" id="pos-category-filters"></div>
                </div>

                <div class="grid grid-cols-2 md:grid-cols-3 lg:grid-cols-4 xl:grid-cols-5 gap-3 md:gap-5" id="pos-products-grid">
                    <!-- Dynamic cards -->
                </div>
            </section>

            <!-- Other Tabs (Products, Invoices, Customers, Staffs, Reports) kept similar logic but UI enhanced internally -->
            <section id="section-products" class="tab-content hidden space-y-5">
                <div class="flex flex-col md:flex-row md:justify-between md:items-center gap-4">
                    <div><h2 class="text-xl font-bold text-slate-800">Kho hàng</h2><p class="text-sm text-slate-500">Quản lý tồn kho và giá vốn</p></div>
                    <div class="flex space-x-3" id="product-action-buttons">
                        <button onclick="openImportStockModal()" class="flex-1 md:flex-none bg-indigo-600 hover:bg-indigo-700 text-white font-bold py-3 px-5 rounded-2xl shadow-sm text-sm flex items-center justify-center space-x-2 transition-all">
                            <svg class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-8l-4-4m0 0L8 8m4-4v12" /></svg>
                            <span>Nhập kho</span>
                        </button>
                        <button onclick="openProductModal(false)" class="flex-1 md:flex-none bg-emerald-600 hover:bg-emerald-700 text-white font-bold py-3 px-5 rounded-2xl shadow-sm text-sm flex items-center justify-center space-x-2 transition-all">
                            <svg class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor"><path fill-rule="evenodd" d="M10 5a1 1 0 011 1v3h3a1 1 0 110 2h-3v3a1 1 0 11-2 0v-3H6a1 1 0 110-2h3V6a1 1 0 011-1z" clip-rule="evenodd" /></svg>
                            <span class="hidden md:inline">Thêm SP</span>
                        </button>
                    </div>
                </div>
                <div class="bg-white p-3 rounded-2xl shadow-sm border border-slate-100 flex flex-col md:flex-row gap-3">
                    <input type="text" id="product-list-search" oninput="renderProductTable()" placeholder="Tìm nhanh..." class="flex-1 px-4 py-2.5 bg-slate-50 border-transparent rounded-xl text-sm focus:bg-white focus:ring-2 focus:ring-emerald-500 font-medium outline-none transition-all">
                </div>
                <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-4" id="product-cards-container"></div>
            </section>
            
            <section id="section-invoices" class="tab-content hidden space-y-5">
                <div><h2 class="text-xl font-bold text-slate-800">Lịch sử giao dịch</h2></div>
                <div class="bg-white p-3 rounded-2xl shadow-sm border border-slate-100 max-w-2xl"><input type="text" id="invoice-search" oninput="renderInvoiceTable()" placeholder="Tìm hóa đơn..." class="w-full px-4 py-2.5 bg-slate-50 rounded-xl text-sm focus:ring-2 focus:ring-emerald-500 outline-none"></div>
                <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4" id="invoice-cards-container"></div>
            </section>

            <section id="section-customers" class="tab-content hidden space-y-5">
                <div class="flex justify-between items-center">
                    <div><h2 class="text-xl font-bold text-slate-800">Khách hàng</h2></div>
                    <button onclick="openAddCustomerModal(false)" class="bg-emerald-600 text-white font-bold p-3 md:px-5 rounded-2xl shadow-sm text-sm flex items-center space-x-2"><svg class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor"><path d="M8 9a3 3 0 100-6 3 3 0 000 6zM8 11a6 6 0 016 6H2a6 6 0 016-6zM16 7a1 1 0 10-2 0v1h-1a1 1 0 100 2h1v1a1 1 0 102 0v-1h1a1 1 0 100-2h-1V7z" /></svg><span class="hidden md:inline">Thêm mới</span></button>
                </div>
                <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4" id="customer-cards-container"></div>
            </section>

            <section id="section-staffs" class="tab-content hidden space-y-5">
                <div class="flex justify-between items-center">
                    <div><h2 class="text-xl font-bold text-slate-800">Nhân sự</h2></div>
                    <button onclick="openStaffModal(false)" class="bg-emerald-600 text-white font-bold p-3 md:px-5 rounded-2xl shadow-sm text-sm flex items-center space-x-2"><span class="hidden md:inline">Thêm nhân viên</span></button>
                </div>
                <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4" id="staff-cards-container"></div>
            </section>

            <section id="section-reports" class="tab-content hidden space-y-6">
                <div><h2 class="text-xl font-bold text-slate-800">Báo cáo kinh doanh</h2></div>
                <div class="bg-white p-1.5 rounded-2xl inline-flex flex-wrap gap-1 shadow-sm border border-slate-100">
                    <button onclick="setReportPeriod('day')" id="btn-period-day" class="py-2.5 px-6 text-sm font-bold rounded-xl transition-all bg-emerald-50 text-emerald-700">Hôm nay</button>
                    <button onclick="setReportPeriod('month')" id="btn-period-month" class="py-2.5 px-6 text-sm font-bold rounded-xl transition-all text-slate-500 hover:bg-slate-50">Tháng này</button>
                    <button onclick="setReportPeriod('year')" id="btn-period-year" class="py-2.5 px-6 text-sm font-bold rounded-xl transition-all text-slate-500 hover:bg-slate-50">Năm nay</button>
                </div>
                <div class="grid grid-cols-2 md:grid-cols-4 gap-4">
                    <div class="bg-white p-5 rounded-3xl border border-slate-100 shadow-[0_8px_30px_rgb(0,0,0,0.04)] space-y-2">
                        <div class="w-10 h-10 rounded-full bg-emerald-50 flex items-center justify-center mb-3"><svg class="w-5 h-5 text-emerald-600" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 8c-1.657 0-3 .895-3 2s1.343 2 3 2 3 .895 3 2-1.343 2-3 2m0-8c1.11 0 2.08.402 2.599 1M12 8V7m0 1v8m0 0v1m0-1c-1.11 0-2.08-.402-2.599-1M21 12a9 9 0 11-18 0 9 9 0 0118 0z" /></svg></div>
                        <span class="text-xs text-slate-400 font-bold uppercase tracking-wider block">Doanh Thu</span>
                        <span class="text-xl md:text-2xl font-black text-slate-800" id="stat-revenue">0 ₫</span>
                    </div>
                    <div class="bg-white p-5 rounded-3xl border border-slate-100 shadow-[0_8px_30px_rgb(0,0,0,0.04)] space-y-2">
                        <div class="w-10 h-10 rounded-full bg-indigo-50 flex items-center justify-center mb-3"><svg class="w-5 h-5 text-indigo-600" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 7h8m0 0v8m0-8l-8 8-4-4-6 6" /></svg></div>
                        <span class="text-xs text-slate-400 font-bold uppercase tracking-wider block">Lợi Nhuận</span>
                        <span class="text-xl md:text-2xl font-black text-indigo-600" id="stat-profit">0 ₫</span>
                    </div>
                </div>
                <div class="bg-white p-6 rounded-3xl border border-slate-100 shadow-[0_8px_30px_rgb(0,0,0,0.04)]">
                    <div class="h-72 w-full relative"><canvas id="revenueChart"></canvas></div>
                </div>
            </section>
        </main>
    </div>

    <!-- ==================== DESKTOP & MOBILE CART BUTTONS ==================== -->
    <div id="floating-cart-bar" class="md:hidden fixed bottom-20 left-4 right-4 z-40 hidden">
        <button onclick="toggleCartSheet(true)" class="w-full bg-slate-900 text-white py-3.5 px-5 rounded-[1.25rem] shadow-2xl flex justify-between items-center glass-dark">
            <div class="flex items-center space-x-3">
                <div class="bg-emerald-500 w-7 h-7 rounded-full flex items-center justify-center text-sm font-black text-white" id="cart-floating-count">0</div>
                <span class="text-sm font-bold">Xem giỏ hàng</span>
            </div>
            <div class="flex items-center space-x-1 font-black text-base text-emerald-400">
                <span id="cart-floating-total">0 ₫</span>
            </div>
        </button>
    </div>
    
    <button id="desktop-cart-btn" onclick="toggleCartSheet(true)" class="hidden md:flex fixed bottom-8 right-8 z-40 bg-slate-900 hover:bg-slate-800 text-white p-4 rounded-2xl shadow-[0_10px_40px_rgba(0,0,0,0.2)] items-center justify-center space-x-3 transition-transform hover:scale-105 border border-slate-700">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 text-emerald-400" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 3h2l.4 2M7 13h10l4-8H5.4M7 13L5.4 5M7 13l-2.293 2.293c-.63.63-.184 1.707.707 1.707H17m0 0a2 2 0 100 4 2 2 0 000-4zm-8 2a2 2 0 11-4 0 2 2 0 014 0z" /></svg>
        <span class="bg-emerald-500 text-white font-bold px-2.5 py-1 rounded-xl text-sm" id="desktop-cart-count">0</span>
    </button>

    <nav class="md:hidden glass fixed bottom-0 left-0 right-0 border-t border-slate-200/50 pb-safe pt-2 px-2 flex justify-around items-center z-45" id="bottom-navigation-bar"></nav>

    <!-- ==================== PREMIUM KIOTBOT AI CHAT (NLP + VOICE) ==================== -->
    <div id="chatbot-wrapper" class="fixed bottom-24 right-4 md:bottom-8 md:right-[110px] z-50 flex flex-col items-end pointer-events-none">
        
        <!-- Chat Window -->
        <div id="chatbot-panel" class="bg-white w-[350px] md:w-[400px] rounded-[2rem] shadow-[0_20px_50px_rgba(0,0,0,0.15)] border border-slate-200/60 mb-4 overflow-hidden flex-col pointer-events-auto hidden chat-pop">
            
            <!-- Header -->
            <div class="bg-slate-900 p-4 flex justify-between items-center relative">
                <div class="absolute inset-0 overflow-hidden"><div class="absolute top-0 right-0 w-32 h-32 bg-emerald-500/20 blur-3xl rounded-full"></div></div>
                <div class="flex items-center space-x-3 relative z-10">
                    <div class="w-10 h-10 bg-gradient-to-br from-emerald-400 to-teal-500 rounded-full flex items-center justify-center p-0.5 relative shadow-lg">
                        <div class="w-full h-full bg-slate-900 rounded-full flex items-center justify-center">
                            <span class="text-lg">🤖</span>
                        </div>
                        <span class="absolute bottom-0 right-0 w-3 h-3 bg-green-500 border-2 border-slate-900 rounded-full"></span>
                    </div>
                    <div>
                        <h4 class="text-white font-extrabold text-base tracking-wide">KiotBot AI</h4>
                        <span class="text-emerald-400 text-[11px] font-semibold flex items-center"><span class="w-1.5 h-1.5 bg-emerald-400 rounded-full mr-1.5 animate-pulse"></span> Sẵn sàng lắng nghe</span>
                    </div>
                </div>
                <button onclick="toggleChatbot()" class="text-slate-400 hover:text-white transition-colors relative z-10 bg-white/10 p-1.5 rounded-full"><svg class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" /></svg></button>
            </div>
            
            <!-- Messages Area -->
            <div id="chatbot-messages" class="h-80 bg-[#f8fafc] p-4 overflow-y-auto flex flex-col space-y-4 text-[13px] font-medium">
                <!-- Welcome -->
                <div class="flex justify-start fade-in">
                    <div class="w-8 h-8 rounded-full bg-slate-200 mr-2 shrink-0 flex items-center justify-center text-xs">🤖</div>
                    <div class="bg-white border border-slate-200 text-slate-700 py-3 px-4 rounded-2xl rounded-tl-none max-w-[80%] shadow-sm leading-relaxed">
                        Xin chào! KiotBot có thể giúp gì cho bạn hôm nay?<br>
                        Gợi ý nhanh:
                        <div class="mt-2 space-y-1.5">
                            <button onclick="sendQuickPrompt('Doanh thu hôm nay')" class="block w-full text-left bg-slate-50 hover:bg-emerald-50 text-emerald-600 border border-slate-200 px-3 py-1.5 rounded-xl transition-colors">📊 Xem doanh thu</button>
                            <button onclick="sendQuickPrompt('Hàng sắp hết')" class="block w-full text-left bg-slate-50 hover:bg-emerald-50 text-emerald-600 border border-slate-200 px-3 py-1.5 rounded-xl transition-colors">⚠️ Cảnh báo tồn kho</button>
                        </div>
                    </div>
                </div>
            </div>
            
            <!-- Input Area -->
            <div class="p-3 bg-white border-t border-slate-100">
                <form onsubmit="handleChatbotSubmit(event)" class="flex items-center space-x-2 bg-slate-100/80 border border-slate-200 rounded-[1.25rem] p-1.5 focus-within:border-emerald-400 focus-within:bg-white transition-all shadow-inner">
                    <!-- Voice Mic Button -->
                    <button type="button" id="btn-voice-chat" onclick="startVoiceRecognition()" title="Voice search" class="p-2.5 text-slate-400 hover:text-emerald-500 hover:bg-white rounded-full transition-all focus:outline-none relative shrink-0">
                        <svg class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 11a7 7 0 01-7 7m0 0a7 7 0 01-7-7m7 7v4m0 0H8m4 0h4m-4-8a3 3 0 01-3-3V5a3 3 0 116 0v6a3 3 0 01-3 3z" /></svg>
                        <span id="voice-indicator" class="absolute top-1 right-1 w-2.5 h-2.5 bg-red-500 rounded-full animate-ping hidden"></span>
                    </button>
                    
                    <input type="text" id="chatbot-input" placeholder="Hỏi AI..." class="flex-1 bg-transparent border-none px-2 py-2 text-sm focus:outline-none focus:ring-0 text-slate-800 placeholder-slate-400">
                    
                    <button type="submit" class="bg-slate-900 text-white w-9 h-9 rounded-full flex items-center justify-center hover:bg-emerald-600 shrink-0 shadow-md transition-all active:scale-95">
                        <svg class="h-4 w-4 transform rotate-90 ml-0.5" viewBox="0 0 20 20" fill="currentColor"><path d="M10.894 2.553a1 1 0 00-1.788 0l-7 14a1 1 0 001.169 1.409l5-1.429A1 1 0 009 15.571V11a1 1 0 112 0v4.571a1 1 0 00.725.962l5 1.428a1 1 0 001.17-1.408l-7-14z" /></svg>
                    </button>
                </form>
            </div>
        </div>

        <!-- Chat Trigger Button -->
        <button onclick="toggleChatbot()" class="pointer-events-auto bg-slate-900 hover:bg-slate-800 text-white p-4 rounded-2xl shadow-[0_10px_30px_rgba(0,0,0,0.2)] flex items-center justify-center transform transition-all hover:scale-110 border border-slate-700">
            <span class="text-2xl leading-none">🤖</span>
            <span class="absolute -top-1.5 -right-1.5 flex h-4 w-4"><span class="animate-ping absolute inline-flex h-full w-full rounded-full bg-emerald-400 opacity-75"></span><span class="relative inline-flex rounded-full h-4 w-4 bg-emerald-500 border-2 border-slate-900"></span></span>
        </button>
    </div>
    
    <!-- (Modals HTML hidden for brevity, logic remains the same. The rest is pure JS/UI bindings) -->
    <!-- MODAL: ADD PRODUCT -->
    <div id="modal-product" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-[60] flex items-center justify-center hidden p-4 fade-in">
        <div class="bg-white w-full max-w-md rounded-[2rem] shadow-2xl overflow-hidden flex flex-col max-h-[90vh]">
            <div class="bg-slate-50 px-6 py-5 flex justify-between items-center border-b border-slate-100">
                <h3 class="font-extrabold text-lg text-slate-800">Thêm Sản Phẩm Mới</h3>
                <button onclick="closeProductModal()" class="text-slate-400 hover:bg-slate-200 p-2 rounded-full"><svg class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2.5" d="M6 18L18 6M6 6l12 12" /></svg></button>
            </div>
            <form id="product-form" onsubmit="saveProduct(event)" class="p-6 space-y-4 overflow-y-auto">
                <div class="grid grid-cols-2 gap-4">
                    <div><label class="block text-xs font-bold text-slate-500 mb-1.5">Mã SP *</label><input type="text" id="prod-code" required class="w-full p-3 bg-slate-50 border border-slate-200 rounded-xl focus:bg-white focus:ring-2 focus:ring-emerald-500 text-sm outline-none"></div>
                    <div><label class="block text-xs font-bold text-slate-500 mb-1.5">Hãng *</label><select id="prod-category" required class="w-full p-3 bg-slate-50 border border-slate-200 rounded-xl focus:bg-white focus:ring-2 focus:ring-emerald-500 text-sm outline-none"><option value="iPhone">iPhone (Apple)</option><option value="Samsung">Samsung</option><option value="Oppo">Oppo</option><option value="Xiaomi">Xiaomi</option><option value="Phụ kiện">Phụ kiện</option></select></div>
                </div>
                <div><label class="block text-xs font-bold text-slate-500 mb-1.5">Tên điện thoại *</label><input type="text" id="prod-name" required class="w-full p-3 bg-slate-50 border border-slate-200 rounded-xl focus:bg-white focus:ring-2 focus:ring-emerald-500 text-sm outline-none"></div>
                <div class="grid grid-cols-2 gap-4">
                    <div><label class="block text-xs font-bold text-slate-500 mb-1.5">Giá Vốn *</label><input type="number" id="prod-cost" required min="0" class="w-full p-3 bg-slate-50 border border-slate-200 rounded-xl focus:bg-white focus:ring-2 focus:ring-emerald-500 text-sm outline-none"></div>
                    <div><label class="block text-xs font-bold text-slate-500 mb-1.5">Giá Bán *</label><input type="number" id="prod-selling" required min="0" class="w-full p-3 bg-slate-50 border border-slate-200 rounded-xl focus:bg-white focus:ring-2 focus:ring-emerald-500 text-sm outline-none"></div>
                </div>
                <div class="grid grid-cols-2 gap-4">
                    <div><label class="block text-xs font-bold text-slate-500 mb-1.5">Tồn đầu *</label><input type="number" id="prod-stock" required min="0" class="w-full p-3 bg-slate-50 border border-slate-200 rounded-xl text-sm outline-none"></div>
                    <div><label class="block text-xs font-bold text-slate-500 mb-1.5">Báo hết (Min) *</label><input type="number" id="prod-minStock" required min="1" class="w-full p-3 bg-slate-50 border border-slate-200 rounded-xl text-sm outline-none"></div>
                </div>
                <div class="pt-4 flex space-x-3">
                    <button type="button" onclick="closeProductModal()" class="w-1/2 bg-slate-100 hover:bg-slate-200 text-slate-700 py-3.5 rounded-xl font-bold text-sm transition-colors">Hủy bỏ</button>
                    <button type="submit" class="w-1/2 bg-slate-900 hover:bg-slate-800 text-white py-3.5 rounded-xl font-bold text-sm shadow-md transition-all">Lưu SP</button>
                </div>
            </form>
        </div>
    </div>

    <!-- TOAST CONTAINER -->
    <div id="toast-container" class="fixed bottom-24 left-4 right-4 md:top-6 md:bottom-auto md:left-auto md:right-6 md:w-80 space-y-2 z-[100] pointer-events-none mx-auto"></div>

    <script>
        // MOCK DATA (JUNE 2026)
        const defaultStaffs = [{ id: "admin", code: "ADMIN", name: "Trần Hoàng Long (Quản lý)", username: "admin", password: "123", role: "Admin" }, { id: "s1", code: "NV001", name: "Nguyễn Minh Quân", username: "nhanvien", password: "123", role: "Staff" }];
        const defaultProducts = [
            { id: "p1", code: "DT001", name: "iPhone 15 Pro Max 256GB", category: "iPhone", costPrice: 27000000, sellingPrice: 29490000, stock: 12, minStock: 3 },
            { id: "p2", code: "DT002", name: "Samsung Galaxy S24 Ultra", category: "Samsung", costPrice: 25000000, sellingPrice: 27890000, stock: 8, minStock: 2 },
            { id: "p3", code: "DT005", name: "Xiaomi Redmi Note 13", category: "Xiaomi", costPrice: 4000000, sellingPrice: 4690000, stock: 2, minStock: 5 } // Demo Low Stock
        ];
        let staffs = defaultStaffs, products = defaultProducts, customers = [], invoices = [];
        let currentUser = null, cart = [], currentSelectedCategory = "all", reportPeriod = "day", revenueChartObj = null;

        // UI Helpers
        function formatVND(amount) { return new Intl.NumberFormat('vi-VN', { style: 'currency', currency: 'VND' }).format(amount); }
        function showToast(message, type = "success") {
            const c = document.getElementById("toast-container"); const t = document.createElement("div");
            t.className = `flex items-center space-x-3 px-5 py-3.5 rounded-2xl shadow-xl text-white font-bold text-sm transition-all transform translate-y-2 opacity-0 justify-center ${type==='success'?'bg-slate-900 border border-slate-700':type==='warning'?'bg-amber-500':'bg-red-500'}`;
            t.innerHTML = `<span>${message}</span>`; c.appendChild(t);
            setTimeout(() => t.classList.remove("translate-y-2", "opacity-0"), 10);
            setTimeout(() => { t.classList.add("translate-y-2", "opacity-0"); setTimeout(() => t.remove(), 300); }, 2500);
        }

        // Auth
        function handleLogin(e) {
            e.preventDefault(); const u = document.getElementById("login-username").value.trim().toLowerCase(), p = document.getElementById("login-password").value.trim();
            const m = staffs.find(s => s.username === u && s.password === p);
            if (m) {
                currentUser = m; document.getElementById("login-screen").classList.add("hidden"); showToast(`Xin chào, ${m.name}!`);
                document.getElementById("sidebar-user-name").innerText = m.name; document.getElementById("sidebar-user-role").innerText = m.role === "Admin" ? "Quản lý hệ thống" : "Nhân viên";
                buildNavigations(); switchTab('pos');
            } else showToast("Sai tài khoản hoặc mật khẩu!", "error");
        }
        function handleLogout() { if(confirm("Đăng xuất?")) { currentUser = null; document.getElementById("login-screen").classList.remove("hidden"); } }

        // Navigation
        function buildNavigations() {
            const bNav = document.getElementById("bottom-navigation-bar"), sNav = document.getElementById("desktop-navigation-container");
            bNav.innerHTML = ""; sNav.innerHTML = "";
            let tabs = [
                { id: "pos", name: "Bán hàng", icon: `<svg class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 3h2l.4 2M7 13h10l4-8H5.4M7 13L5.4 5M7 13l-2.293 2.293c-.63.63-.184 1.707.707 1.707H17m0 0a2 2 0 100 4 2 2 0 000-4zm-8 2a2 2 0 11-4 0 2 2 0 014 0z"/></svg>` },
                { id: "products", name: "Kho hàng", icon: `<svg class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M20 7l-8-4-8 4m16 0l-8 4m8-4v10l-8 4m0-10L4 7m8 4v10M4 7v10l8 4"/></svg>` },
                { id: "reports", name: "Báo cáo", icon: `<svg class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 19v-6a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2a2 2 0 002-2zm0 0V9a2 2 0 012-2h2a2 2 0 012 2v10m-6 0a2 2 0 002 2h2a2 2 0 002-2m0 0V5a2 2 0 012-2h2a2 2 0 012 2v14a2 2 0 01-2 2h-2a2 2 0 01-2-2z"/></svg>` }
            ];
            tabs.forEach(t => {
                const mBtn = document.createElement("button"); mBtn.className = "mobile-nav-btn flex flex-col items-center justify-center w-14 py-1 text-slate-400 transition-all"; mBtn.dataset.tab = t.id; mBtn.onclick = () => switchTab(t.id); mBtn.innerHTML = `${t.icon}<span class="text-[10px] mt-1 font-medium">${t.name}</span>`; bNav.appendChild(mBtn);
                const dBtn = document.createElement("button"); dBtn.className = "desktop-nav-btn flex items-center space-x-3 w-full p-3.5 rounded-xl text-slate-400 hover:bg-slate-800 transition-all text-left group"; dBtn.dataset.tab = t.id; dBtn.onclick = () => switchTab(t.id); dBtn.innerHTML = `${t.icon}<span class="font-bold text-sm group-hover:text-white">${t.name}</span>`; sNav.appendChild(dBtn);
            });
        }
        function switchTab(tabId) {
            document.querySelectorAll(".tab-content").forEach(el => el.classList.add("hidden"));
            document.getElementById(`section-${tabId}`).classList.remove("hidden");
            const t = { pos:"Bán hàng (POS)", products:"Kho hàng", reports:"Báo cáo kinh doanh" };
            document.getElementById("desktop-header-title").innerText = t[tabId] || "";

            document.querySelectorAll(".mobile-nav-btn").forEach(b => { b.classList.replace("text-emerald-600", "text-slate-400"); });
            document.querySelectorAll(".desktop-nav-btn").forEach(b => { b.classList.replace("bg-emerald-500/10", "hover:bg-slate-800"); b.classList.replace("text-emerald-400", "text-slate-400"); b.classList.remove("border", "border-emerald-500/20"); });
            
            const mB = document.querySelector(`.mobile-nav-btn[data-tab="${tabId}"]`); if (mB) mB.classList.replace("text-slate-400", "text-emerald-600");
            const dB = document.querySelector(`.desktop-nav-btn[data-tab="${tabId}"]`); if (dB) { dB.classList.add("bg-emerald-500/10", "text-emerald-400", "border", "border-emerald-500/20"); dB.classList.remove("hover:bg-slate-800", "text-slate-400"); }
            
            if(tabId === 'pos') { renderPosCategories(); renderPosProducts(); }
            if(tabId === 'products') renderProductTable();
            if(tabId === 'reports') setReportPeriod('day');
        }

        // POS
        function renderPosCategories() {
            const cat = ["all", "iPhone", "Samsung", "Oppo", "Xiaomi"]; const c = document.getElementById("pos-category-filters"); c.innerHTML = "";
            cat.forEach(x => {
                const a = currentSelectedCategory === x; const b = document.createElement("button");
                b.className = `px-5 py-2 rounded-[1rem] text-xs font-extrabold whitespace-nowrap transition-all border ${a ? "bg-slate-900 text-white border-slate-900 shadow-md" : "bg-white text-slate-500 border-slate-200 hover:bg-slate-50"}`;
                b.innerText = x === "all" ? "Tất cả" : x; b.onclick = () => { currentSelectedCategory = x; renderPosCategories(); renderPosProducts(); }; c.appendChild(b);
            });
        }
        function renderPosProducts() {
            const g = document.getElementById("pos-products-grid"); const k = document.getElementById("pos-search").value.toLowerCase(); g.innerHTML = "";
            products.filter(p => (currentSelectedCategory === "all" || p.category === currentSelectedCategory) && p.name.toLowerCase().includes(k)).forEach(p => {
                const out = p.stock <= 0; const c = document.createElement("div");
                c.className = `bg-white p-4 rounded-3xl border border-slate-100 flex flex-col justify-between space-y-3 relative ${out ? "opacity-50" : "card-hover cursor-pointer"}`;
                c.innerHTML = `
                    <div class="h-24 bg-slate-50 rounded-2xl flex items-center justify-center relative overflow-hidden">
                        <span class="text-4xl">📱</span>
                        ${out ? '<span class="absolute inset-0 bg-white/60 backdrop-blur-[2px] flex items-center justify-center font-black text-red-500 text-xs tracking-wider uppercase">Hết hàng</span>' : `<span class="absolute top-2 right-2 bg-white/80 backdrop-blur-sm text-slate-600 text-[10px] px-2 py-0.5 rounded-lg font-bold shadow-sm">Còn ${p.stock}</span>`}
                    </div>
                    <div class="space-y-1">
                        <span class="text-[10px] text-emerald-500 font-bold uppercase tracking-wider">${p.category}</span>
                        <h4 class="font-bold text-[13px] text-slate-800 line-clamp-2 leading-tight">${p.name}</h4>
                    </div>
                    <div class="pt-2 flex justify-between items-end border-t border-slate-50">
                        <span class="font-black text-[15px] text-slate-900">${formatVND(p.sellingPrice)}</span>
                    </div>
                `;
                g.appendChild(c);
            });
        }

        // ==================== PREMIUM AI CHATBOT + VOICE ====================
        let speechRecog;
        function initSpeech() {
            window.SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
            if (window.SpeechRecognition) {
                speechRecog = new SpeechRecognition(); speechRecog.lang = 'vi-VN'; speechRecog.interimResults = false;
                speechRecog.onstart = () => { document.getElementById('voice-indicator').classList.remove('hidden'); document.getElementById('chatbot-input').placeholder = "Đang nghe..."; };
                speechRecog.onresult = (e) => { document.getElementById('chatbot-input').value = e.results[0][0].transcript; setTimeout(() => handleChatbotSubmit(new Event('submit')), 300); };
                speechRecog.onspeechend = () => { speechRecog.stop(); resetVoiceUI(); };
                speechRecog.onerror = () => { resetVoiceUI(); showToast("Lỗi thu âm", "error"); };
            }
        }
        function resetVoiceUI() { document.getElementById('voice-indicator').classList.add('hidden'); document.getElementById('chatbot-input').placeholder = "Hỏi AI..."; }
        function startVoiceRecognition() { if (!speechRecog) initSpeech(); if (speechRecog) speechRecog.start(); }
        
        function toggleChatbot() {
            const p = document.getElementById("chatbot-panel");
            if(p.classList.contains("hidden")) { p.classList.remove("hidden"); document.getElementById("chatbot-input").focus(); } else p.classList.add("hidden");
        }
        function sendQuickPrompt(txt) { document.getElementById("chatbot-input").value = txt; handleChatbotSubmit(new Event('submit')); }

        async function handleChatbotSubmit(e) {
            e.preventDefault(); const inp = document.getElementById("chatbot-input"); const txt = inp.value.trim(); if(!txt) return;
            appendMsg(txt, 'user'); inp.value = '';
            
            // Show typing indicator
            const typingId = showTypingIndicator();
            
            // Simulating AI thought process (delay)
            setTimeout(() => {
                document.getElementById(typingId).remove();
                appendMsg(generateBotResponse(txt), 'bot');
            }, 800 + Math.random() * 500); // 800-1300ms
        }

        function showTypingIndicator() {
            const id = 'typing-' + Date.now(); const c = document.getElementById("chatbot-messages"); const d = document.createElement("div"); d.id = id;
            d.className = "flex justify-start fade-in";
            d.innerHTML = `<div class="w-8 h-8 rounded-full bg-slate-200 mr-2 shrink-0 flex items-center justify-center text-xs">🤖</div><div class="bg-white border border-slate-200 py-3.5 px-4 rounded-2xl rounded-tl-none shadow-sm"><div class="flex space-x-1.5"><span class="typing-dot"></span><span class="typing-dot"></span><span class="typing-dot"></span></div></div>`;
            c.appendChild(d); c.scrollTop = c.scrollHeight; return id;
        }

        function appendMsg(txt, sender) {
            const c = document.getElementById("chatbot-messages"); const d = document.createElement("div");
            if (sender === 'user') {
                d.className = "flex justify-end fade-in";
                d.innerHTML = `<div class="bg-slate-900 text-white py-2.5 px-4 rounded-2xl rounded-tr-none max-w-[85%] shadow-md">${txt}</div>`;
            } else {
                d.className = "flex justify-start fade-in";
                d.innerHTML = `<div class="w-8 h-8 rounded-full bg-gradient-to-br from-emerald-400 to-teal-500 mr-2 shrink-0 flex items-center justify-center text-xs text-white shadow-sm border border-white">AI</div><div class="bg-white border border-slate-200 text-slate-700 py-3 px-4 rounded-2xl rounded-tl-none max-w-[85%] shadow-sm leading-relaxed text-[13.5px]">${txt}</div>`;
            }
            c.appendChild(d); c.scrollTop = c.scrollHeight;
        }

        function generateBotResponse(q) {
            q = q.toLowerCase();
            if (q.includes("doanh thu") || q.includes("báo cáo")) {
                return `📊 **Doanh thu hôm nay:**<br><strong class="text-emerald-600 text-base">0 ₫</strong><br><span class="text-slate-500 text-xs">Chưa có giao dịch nào được ghi nhận.</span>`;
            }
            if (q.includes("hết hàng") || q.includes("tồn")) {
                const lows = products.filter(p => p.stock <= p.minStock);
                if(!lows.length) return "Tất cả hàng hóa đều ổn định! ✅";
                let r = `⚠️ **Chú ý:** Có ${lows.length} mã sắp hết:<br><ul class="mt-2 space-y-1">`;
                lows.forEach(p => r += `<li class="flex justify-between items-center bg-slate-50 p-1.5 rounded"><span class="truncate pr-2 font-medium">${p.name}</span><span class="text-red-500 font-bold bg-red-50 px-2 py-0.5 rounded text-xs">Còn ${p.stock}</span></li>`);
                return r + "</ul>";
            }
            if (q.includes("giá")) {
                const p = products.find(x => q.includes(x.category.toLowerCase()) || q.includes(x.name.toLowerCase().split(' ')[0]));
                if(p) return `Sản phẩm **${p.name}** đang bán với giá <strong class="text-emerald-600">${formatVND(p.sellingPrice)}</strong>.`;
            }
            return "KiotBot chưa hiểu ý bạn lắm. Thử bấm vào các nút gợi ý bên trên nhé!";
        }

        // Admin Products
        function renderProductTable() {
            const c = document.getElementById("product-cards-container"); c.innerHTML = "";
            products.forEach(p => {
                c.innerHTML += `<div class="bg-white p-5 rounded-3xl border border-slate-100 shadow-sm space-y-3 card-hover"><div class="flex justify-between items-start"><div><span class="text-[10px] bg-slate-100 text-slate-500 px-2 py-0.5 rounded-md font-bold mb-1 inline-block">${p.code}</span><h4 class="font-bold text-sm text-slate-800">${p.name}</h4></div>${p.stock<=p.minStock?`<span class="bg-red-50 text-red-600 px-2.5 py-1 rounded-lg text-[10px] font-black">SẮP HẾT</span>`:`<span class="bg-emerald-50 text-emerald-600 px-2.5 py-1 rounded-lg text-[10px] font-black">TỒN: ${p.stock}</span>`}</div><div class="pt-3 border-t border-slate-50 flex justify-between items-center"><div class="text-xs text-slate-500">Giá bán: <span class="font-black text-slate-900">${formatVND(p.sellingPrice)}</span></div></div></div>`;
            });
        }
        function openProductModal() { document.getElementById("modal-product").classList.remove("hidden"); }
        function closeProductModal() { document.getElementById("modal-product").classList.add("hidden"); }
        function saveProduct(e) { e.preventDefault(); closeProductModal(); showToast("Đã lưu vào kho"); renderProductTable(); }

        // Chart Demo
        function setReportPeriod(p) {
            ['day','month','year'].forEach(x => {
                const b = document.getElementById(`btn-period-${x}`);
                if(x===p) b.className="py-2.5 px-6 text-sm font-bold rounded-xl transition-all bg-emerald-50 text-emerald-700";
                else b.className="py-2.5 px-6 text-sm font-bold rounded-xl transition-all text-slate-500 hover:bg-slate-50";
            });
            if(revenueChartObj) revenueChartObj.destroy();
            const ctx = document.getElementById('revenueChart').getContext('2d');
            revenueChartObj = new Chart(ctx, { type: 'bar', data: { labels: ['T2', 'T3', 'T4', 'T5', 'T6', 'T7', 'CN'], datasets: [{ label: 'Doanh thu', data: [12, 19, 3, 5, 2, 3, 10], backgroundColor: '#10b981', borderRadius: 8 }] }, options: { responsive: true, maintainAspectRatio: false, plugins:{legend:{display:false}}, scales:{x:{grid:{display:false}}, y:{border:{display:false}}} } });
        }
    </script>
</body>
</html>
