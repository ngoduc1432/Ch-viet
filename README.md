```html
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>KiotPro - Quản Lý Bán Hàng Đa Nền Tảng</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Chart.js CDN -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;500;600;700;800&display=swap');
        body {
            font-family: 'Plus Jakarta Sans', sans-serif;
            -webkit-tap-highlight-color: transparent;
        }
        .no-scrollbar::-webkit-scrollbar {
            display: none;
        }
        .no-scrollbar {
            -ms-overflow-style: none;
            scrollbar-width: none;
        }
        /* Custom scrollbar for desktop */
        @media (min-width: 768px) {
            ::-webkit-scrollbar {
                width: 6px;
                height: 6px;
            }
            ::-webkit-scrollbar-track {
                background: #f1f5f9; 
            }
            ::-webkit-scrollbar-thumb {
                background: #cbd5e1; 
                border-radius: 10px;
            }
            ::-webkit-scrollbar-thumb:hover {
                background: #94a3b8; 
            }
        }
        
        .slide-up {
            animation: slideUp 0.3s cubic-bezier(0.16, 1, 0.3, 1) forwards;
        }
        @keyframes slideUp {
            from { transform: translateY(100%); }
            to { transform: translateY(0); }
        }
        .chat-slide-up {
            animation: chatSlideUp 0.3s ease-out forwards;
        }
        @keyframes chatSlideUp {
            from { opacity: 0; transform: translateY(20px) scale(0.95); }
            to { opacity: 1; transform: translateY(0) scale(1); }
        }
    </style>
</head>
<body class="bg-slate-50 text-slate-800 h-screen overflow-hidden flex">

    <!-- ==================== SCREEN 1: LOGIN SCREEN ==================== -->
    <div id="login-screen" class="fixed inset-0 bg-gradient-to-br from-slate-900 via-emerald-950 to-emerald-900 z-[100] flex items-center justify-center p-4">
        <div class="bg-white/95 backdrop-blur-md w-full max-w-sm rounded-3xl shadow-2xl p-6 space-y-6 border border-white/20">
            <div class="text-center space-y-2">
                <div class="inline-block bg-emerald-50 text-emerald-600 px-4 py-2 rounded-2xl font-black text-2xl tracking-wider shadow-inner">
                    Kiot<span class="text-amber-500">Pro</span>
                </div>
                <h2 class="text-lg font-bold text-slate-800">Hệ thống Đăng Nhập</h2>
                <p class="text-xs text-slate-400">Ứng dụng đa nền tảng (Mobile & PC)</p>
            </div>

            <form id="login-form" onsubmit="handleLogin(event)" class="space-y-4">
                <div>
                    <label class="block text-xs font-bold text-slate-500 uppercase tracking-wide mb-1">Tài khoản</label>
                    <input type="text" id="login-username" required placeholder="admin hoặc nhanvien" class="w-full px-4 py-2.5 border border-slate-200 rounded-2xl focus:outline-none focus:ring-2 focus:ring-emerald-500 text-sm">
                </div>
                <div>
                    <label class="block text-xs font-bold text-slate-500 uppercase tracking-wide mb-1">Mật khẩu</label>
                    <input type="password" id="login-password" required placeholder="••••••••" class="w-full px-4 py-2.5 border border-slate-200 rounded-2xl focus:outline-none focus:ring-2 focus:ring-emerald-500 text-sm">
                </div>
                <button type="submit" class="w-full bg-emerald-600 hover:bg-emerald-700 active:scale-[0.98] text-white py-3 rounded-2xl font-bold text-sm shadow-lg transition-all flex justify-center items-center">
                    <span>ĐĂNG NHẬP</span>
                </button>
            </form>

            <div class="border-t border-slate-100 pt-4 text-center">
                <p class="text-[10px] font-bold text-slate-500">Admin: <span class="text-emerald-600">admin / 123</span></p>
                <p class="text-[10px] font-bold text-slate-500">Nhân viên: <span class="text-emerald-600">nhanvien / 123</span></p>
            </div>
        </div>
    </div>

    <!-- ==================== DESKTOP SIDEBAR ==================== -->
    <aside id="desktop-sidebar" class="hidden md:flex flex-col w-64 bg-slate-900 text-white h-screen shrink-0 transition-all duration-300 relative z-40">
        <div class="p-6 border-b border-slate-800 flex items-center space-x-3">
            <div class="bg-white text-emerald-600 px-3 py-1 rounded-xl font-black text-lg tracking-wider">
                Kiot<span class="text-amber-500">Pro</span>
            </div>
        </div>
        <div class="p-4 border-b border-slate-800">
            <div class="flex flex-col">
                <span class="text-sm font-bold text-white" id="sidebar-user-name">Đang tải...</span>
                <span class="text-xs text-emerald-400 mt-1" id="sidebar-user-role">Đang tải...</span>
            </div>
        </div>
        <nav class="flex-1 overflow-y-auto py-4 space-y-1 px-3" id="desktop-navigation-container">
            <!-- Dynamic menu -->
        </nav>
        <div class="p-4 border-t border-slate-800">
            <button onclick="handleLogout()" class="flex items-center space-x-3 text-slate-400 hover:text-white transition-colors w-full p-2 rounded-xl hover:bg-slate-800">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 16l4-4m0 0l-4-4m4 4H7m6 4v1a3 3 0 01-3 3H6a3 3 0 01-3-3V7a3 3 0 013-3h4a3 3 0 013 3v1" />
                </svg>
                <span class="font-medium text-sm">Đăng xuất</span>
            </button>
        </div>
    </aside>

    <!-- ==================== MAIN CONTENT AREA ==================== -->
    <div class="flex-1 flex flex-col h-screen relative min-w-0">
        
        <!-- HEADER MOBILE & DESKTOP TOP BAR -->
        <header class="bg-gradient-to-r from-emerald-600 to-teal-700 text-white px-4 py-3 flex justify-between items-center shadow-lg sticky top-0 z-30 shrink-0 md:bg-white md:text-slate-800 md:shadow-sm md:border-b md:border-slate-200">
            <div class="flex md:hidden items-center space-x-2">
                <div class="bg-white text-emerald-600 px-2 py-0.5 rounded-lg font-black text-sm tracking-wider shadow-inner">
                    Kiot<span class="text-amber-500">Pro</span>
                </div>
            </div>
            <div class="hidden md:block">
                <h1 class="text-xl font-extrabold text-slate-800" id="desktop-header-title">Bán hàng (POS)</h1>
            </div>
            
            <div class="flex items-center space-x-3">
                <span class="text-[10px] md:text-xs bg-white/20 md:bg-emerald-100 md:text-emerald-700 text-white px-3 py-1 rounded-full font-bold">Chi nhánh Trung tâm</span>
                <button onclick="handleLogout()" class="md:hidden bg-white/10 hover:bg-white/20 p-2 rounded-xl text-xs transition-colors">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2.5" d="M17 16l4-4m0 0l-4-4m4 4H7m6 4v1a3 3 0 01-3 3H6a3 3 0 01-3-3V7a3 3 0 013-3h4a3 3 0 013 3v1" /></svg>
                </button>
            </div>
        </header>

        <!-- MAIN SCROLLER -->
        <main class="flex-1 overflow-y-auto px-4 py-5 md:p-8 space-y-4 w-full max-w-7xl mx-auto pb-24 md:pb-8">

            <!-- TAB 1: POS -->
            <section id="section-pos" class="tab-content block space-y-4">
                <div class="bg-white p-3 md:p-4 rounded-2xl shadow-sm border border-slate-150 space-y-3">
                    <div class="relative max-w-2xl">
                        <span class="absolute inset-y-0 left-0 flex items-center pl-3 text-slate-400">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
                                <path fill-rule="evenodd" d="M8 4a4 4 0 100 8 4 4 0 000-8zM2 8a6 6 0 1110.89 3.476l4.817 4.817a1 1 0 01-1.414 1.414l-4.816-4.816A6 6 0 012 8z" clip-rule="evenodd" />
                            </svg>
                        </span>
                        <input type="text" id="pos-search" oninput="renderPosProducts()" placeholder="Tìm tên điện thoại, mã máy..." class="w-full pl-10 pr-4 py-2.5 border border-slate-200 rounded-xl focus:outline-none focus:ring-2 focus:ring-emerald-500 text-sm">
                    </div>
                    <div class="flex space-x-2 overflow-x-auto no-scrollbar py-1" id="pos-category-filters">
                        <!-- Dynamic loaded -->
                    </div>
                </div>

                <div class="grid grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-3 md:gap-4" id="pos-products-grid">
                    <!-- Dynamic cards -->
                </div>
            </section>

            <!-- TAB 2: PRODUCTS -->
            <section id="section-products" class="tab-content hidden space-y-4">
                <div class="flex flex-col md:flex-row md:justify-between md:items-center gap-3">
                    <div>
                        <h2 class="text-lg md:text-xl font-bold text-slate-800">Danh mục & Kho hàng</h2>
                        <p class="text-xs text-slate-500">Quản lý sản phẩm và định mức tồn kho</p>
                    </div>
                    <div class="flex space-x-2" id="product-action-buttons">
                        <button onclick="openImportStockModal()" class="flex-1 md:flex-none bg-blue-600 hover:bg-blue-700 text-white font-bold py-2.5 px-4 rounded-xl shadow-sm text-sm flex items-center justify-center space-x-2">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-8l-4-4m0 0L8 8m4-4v12" /></svg>
                            <span>Nhập kho</span>
                        </button>
                        <button onclick="openProductModal(false)" class="flex-1 md:flex-none bg-emerald-600 hover:bg-emerald-700 text-white font-bold py-2.5 px-4 rounded-xl shadow-sm text-sm flex items-center justify-center space-x-2">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor"><path fill-rule="evenodd" d="M10 5a1 1 0 011 1v3h3a1 1 0 110 2h-3v3a1 1 0 11-2 0v-3H6a1 1 0 110-2h3V6a1 1 0 011-1z" clip-rule="evenodd" /></svg>
                            <span class="hidden md:inline">Thêm SP</span>
                        </button>
                    </div>
                </div>

                <div class="bg-white p-4 rounded-2xl shadow-sm border border-slate-100 flex flex-col md:flex-row gap-3">
                    <input type="text" id="product-list-search" oninput="renderProductTable()" placeholder="Tìm nhanh theo tên, mã..." class="flex-1 px-4 py-2 border border-slate-200 rounded-xl text-sm focus:outline-none focus:ring-2 focus:ring-emerald-500">
                    <select id="product-list-category" onchange="renderProductTable()" class="w-full md:w-48 py-2 px-3 border border-slate-200 rounded-xl text-sm bg-white focus:outline-none">
                        <option value="all">Tất cả hãng</option>
                        <option value="iPhone">iPhone</option>
                        <option value="Samsung">Samsung</option>
                        <option value="Oppo">Oppo</option>
                        <option value="Xiaomi">Xiaomi</option>
                    </select>
                    <select id="product-list-status" onchange="renderProductTable()" class="w-full md:w-48 py-2 px-3 border border-slate-200 rounded-xl text-sm bg-white focus:outline-none">
                        <option value="all">Tất cả tồn kho</option>
                        <option value="low-stock">Sắp hết hàng</option>
                        <option value="out-of-stock">Hết hàng (Tồn = 0)</option>
                    </select>
                </div>

                <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-3 md:gap-4" id="product-cards-container"></div>
            </section>

            <!-- TAB 3: INVOICES -->
            <section id="section-invoices" class="tab-content hidden space-y-4">
                <div>
                    <h2 class="text-lg md:text-xl font-bold text-slate-800">Nhật ký bán hàng</h2>
                    <p class="text-xs text-slate-500">Tra cứu hóa đơn và doanh thu</p>
                </div>
                <div class="bg-white p-3 rounded-2xl shadow-sm border border-slate-100 max-w-2xl">
                    <input type="text" id="invoice-search" oninput="renderInvoiceTable()" placeholder="Tìm theo mã hóa đơn, tên khách..." class="w-full px-4 py-2.5 border border-slate-200 rounded-xl text-sm focus:outline-none focus:ring-2 focus:ring-emerald-500">
                </div>
                <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-3" id="invoice-cards-container"></div>
            </section>

            <!-- TAB 4: CUSTOMERS -->
            <section id="section-customers" class="tab-content hidden space-y-4">
                <div class="flex justify-between items-center">
                    <div>
                        <h2 class="text-lg md:text-xl font-bold text-slate-800">Khách hàng</h2>
                        <p class="text-xs text-slate-500">Lịch sử giao dịch đối tác</p>
                    </div>
                    <button onclick="openAddCustomerModal(false)" class="bg-emerald-600 hover:bg-emerald-700 text-white font-bold p-2.5 md:py-2 md:px-4 rounded-xl shadow-md text-sm flex items-center space-x-2">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor"><path d="M8 9a3 3 0 100-6 3 3 0 000 6zM8 11a6 6 0 016 6H2a6 6 0 016-6zM16 7a1 1 0 10-2 0v1h-1a1 1 0 100 2h1v1a1 1 0 102 0v-1h1a1 1 0 100-2h-1V7z" /></svg>
                        <span class="hidden md:inline">Thêm mới</span>
                    </button>
                </div>
                <div class="bg-white p-3 rounded-2xl shadow-sm border border-slate-100 max-w-2xl">
                    <input type="text" id="customer-search" oninput="renderCustomerTable()" placeholder="Nhập tên, số điện thoại..." class="w-full px-4 py-2 border border-slate-200 rounded-xl text-sm focus:outline-none focus:ring-2 focus:ring-emerald-500">
                </div>
                <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-3" id="customer-cards-container"></div>
            </section>

            <!-- TAB 5: STAFFS -->
            <section id="section-staffs" class="tab-content hidden space-y-4">
                <div class="flex justify-between items-center">
                    <div>
                        <h2 class="text-lg md:text-xl font-bold text-slate-800">Nhân sự</h2>
                        <p class="text-xs text-slate-500">Quản lý tài khoản truy cập</p>
                    </div>
                    <button onclick="openStaffModal(false)" class="bg-emerald-600 hover:bg-emerald-700 text-white font-bold p-2.5 md:py-2 md:px-4 rounded-xl shadow-md text-sm flex items-center space-x-2">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor"><path fill-rule="evenodd" d="M10 9a3 3 0 100-6 3 3 0 000 6zm-7 9a7 7 0 1114 0H3z" clip-rule="evenodd" /></svg>
                        <span class="hidden md:inline">Thêm nhân viên</span>
                    </button>
                </div>
                <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-3" id="staff-cards-container"></div>
            </section>

            <!-- TAB 6: REPORTS -->
            <section id="section-reports" class="tab-content hidden space-y-5">
                <div>
                    <h2 class="text-lg md:text-xl font-bold text-slate-800">Báo cáo kinh doanh</h2>
                    <p class="text-xs text-slate-500">Phân tích chuyên sâu</p>
                </div>
                <div class="bg-slate-200/80 p-1.5 rounded-2xl inline-flex flex-wrap gap-1 md:gap-2 text-center max-w-full">
                    <button onclick="setReportPeriod('day')" id="btn-period-day" class="py-2 px-4 text-xs md:text-sm font-bold rounded-xl transition-all bg-white text-emerald-700 shadow-sm">Hôm nay</button>
                    <button onclick="setReportPeriod('week')" id="btn-period-week" class="py-2 px-4 text-xs md:text-sm font-bold rounded-xl transition-all text-slate-600 hover:bg-white/50">Tuần này</button>
                    <button onclick="setReportPeriod('month')" id="btn-period-month" class="py-2 px-4 text-xs md:text-sm font-bold rounded-xl transition-all text-slate-600 hover:bg-white/50">Tháng này</button>
                    <button onclick="setReportPeriod('year')" id="btn-period-year" class="py-2 px-4 text-xs md:text-sm font-bold rounded-xl transition-all text-slate-600 hover:bg-white/50">Năm nay</button>
                </div>

                <div class="grid grid-cols-2 md:grid-cols-4 gap-3 md:gap-4">
                    <div class="bg-white p-4 rounded-2xl border border-slate-100 shadow-sm space-y-1">
                        <span class="text-[10px] md:text-xs text-slate-400 font-extrabold uppercase tracking-wide block">Doanh Thu</span>
                        <span class="text-base md:text-xl font-extrabold text-slate-850" id="stat-revenue">0 ₫</span>
                    </div>
                    <div class="bg-white p-4 rounded-2xl border border-slate-100 shadow-sm space-y-1">
                        <span class="text-[10px] md:text-xs text-slate-400 font-extrabold uppercase tracking-wide block">Lợi Nhuận</span>
                        <span class="text-base md:text-xl font-extrabold text-emerald-600" id="stat-profit">0 ₫</span>
                    </div>
                    <div class="bg-white p-4 rounded-2xl border border-slate-100 shadow-sm space-y-1">
                        <span class="text-[10px] md:text-xs text-slate-400 font-extrabold uppercase tracking-wide block">Số Đơn</span>
                        <span class="text-base md:text-xl font-extrabold text-slate-800" id="stat-orders">0</span>
                    </div>
                    <div class="bg-white p-4 rounded-2xl border border-slate-100 shadow-sm space-y-1">
                        <span class="text-[10px] md:text-xs text-slate-400 font-extrabold uppercase tracking-wide block">Máy Bán Ra</span>
                        <span class="text-base md:text-xl font-extrabold text-blue-600" id="stat-qty-sold">0</span>
                    </div>
                </div>

                <div class="grid grid-cols-1 lg:grid-cols-3 gap-5">
                    <div class="lg:col-span-2 bg-white p-5 rounded-2xl border border-slate-100 shadow-sm">
                        <div class="flex justify-between items-center mb-4">
                            <h3 class="text-sm font-extrabold text-slate-800 uppercase tracking-wide">Biểu đồ doanh thu</h3>
                            <span class="text-[10px] bg-emerald-50 text-emerald-600 px-2 py-1 rounded-full font-bold" id="chart-period-lbl">Hôm nay</span>
                        </div>
                        <div class="h-64 w-full relative">
                            <canvas id="revenueChart"></canvas>
                        </div>
                    </div>
                    <div class="bg-white p-5 rounded-2xl border border-slate-100 shadow-sm space-y-4">
                        <div class="flex justify-between items-center pb-2 border-b border-slate-50">
                            <h3 class="text-sm font-extrabold text-slate-800 uppercase tracking-wide">Bán chạy nhất</h3>
                        </div>
                        <div class="space-y-4" id="top-products-progress-container"></div>
                    </div>
                </div>
            </section>
        </main>
    </div>

    <!-- ==================== CART FLOATING & BOTTOM NAVIGATION ==================== -->
    <div id="floating-cart-bar" class="md:hidden fixed bottom-16 left-4 right-4 z-40 hidden">
        <button onclick="toggleCartSheet(true)" class="w-full bg-amber-500 text-white py-3 px-4 rounded-2xl shadow-xl flex justify-between items-center">
            <div class="flex items-center space-x-2">
                <div class="bg-amber-600 w-6 h-6 rounded-full flex items-center justify-center text-xs font-black" id="cart-floating-count">0</div>
                <span class="text-sm font-bold">Giỏ hàng</span>
            </div>
            <div class="flex items-center space-x-1 font-black text-sm">
                <span id="cart-floating-total">0 ₫</span>
                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5l7 7-7 7" /></svg>
            </div>
        </button>
    </div>
    
    <button id="desktop-cart-btn" onclick="toggleCartSheet(true)" class="hidden md:flex fixed bottom-6 right-6 z-40 bg-amber-500 hover:bg-amber-600 text-white p-4 rounded-full shadow-2xl items-center justify-center space-x-2 transition-transform hover:scale-105">
        <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 3h2l.4 2M7 13h10l4-8H5.4M7 13L5.4 5M7 13l-2.293 2.293c-.63.63-.184 1.707.707 1.707H17m0 0a2 2 0 100 4 2 2 0 000-4zm-8 2a2 2 0 11-4 0 2 2 0 014 0z" /></svg>
        <span class="bg-white text-amber-600 font-bold px-2 py-0.5 rounded-full text-xs" id="desktop-cart-count">0</span>
    </button>

    <nav class="md:hidden fixed bottom-0 left-0 right-0 bg-white border-t border-slate-200 shadow-[0_-4px_6px_-1px_rgba(0,0,0,0.05)] px-1 py-1 flex justify-around items-center z-45" id="bottom-navigation-bar"></nav>

    <!-- ==================== CART SHEET ==================== -->
    <div id="cart-bottom-sheet" class="fixed inset-0 bg-slate-950/60 backdrop-blur-sm z-50 hidden flex justify-end md:justify-end flex-col md:flex-row">
        <div class="flex-1" onclick="toggleCartSheet(false)"></div>
        <div class="bg-white md:w-[400px] w-full max-h-[85vh] md:max-h-full rounded-t-3xl md:rounded-none flex flex-col shadow-2xl slide-up md:animate-none md:h-screen">
            <div class="w-12 h-1 bg-slate-200 rounded-full mx-auto my-3 shrink-0 md:hidden"></div>
            <div class="px-5 py-4 border-b border-slate-100 flex justify-between items-center bg-slate-50 shrink-0">
                <h3 class="font-extrabold text-base text-slate-800">Giỏ hàng (<span id="pos-cart-count">0</span>)</h3>
                <button onclick="toggleCartSheet(false)" class="md:hidden text-slate-500"><svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" /></svg></button>
                <button onclick="toggleCartSheet(false)" class="hidden md:block text-slate-500 hover:text-red-500"><svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" /></svg></button>
            </div>
            <div class="flex-1 overflow-y-auto px-5 py-2 divide-y divide-slate-100" id="pos-cart-container"></div>
            <div class="bg-white p-5 border-t border-slate-200 space-y-3 shrink-0 text-sm shadow-[0_-4px_6px_-1px_rgba(0,0,0,0.05)]">
                <div>
                    <label class="block text-[10px] font-bold text-slate-400 uppercase tracking-wide mb-1">Khách hàng</label>
                    <div class="flex items-center space-x-2">
                        <select id="pos-customer-select" class="flex-1 py-2 px-3 rounded-xl border border-slate-200 bg-slate-50 text-xs font-bold focus:outline-none"></select>
                        <button onclick="toggleCartSheet(false); openAddCustomerModal(true);" class="bg-emerald-50 text-emerald-600 p-2 rounded-xl border border-emerald-100"><svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor"><path d="M8 9a3 3 0 100-6 3 3 0 000 6zM8 11a6 6 0 016 6H2a6 6 0 016-6zM16 7a1 1 0 10-2 0v1h-1a1 1 0 100 2h1v1a1 1 0 102 0v-1h1a1 1 0 100-2h-1V7z" /></svg></button>
                    </div>
                </div>
                <div class="grid grid-cols-2 gap-3">
                    <div>
                        <label class="block text-[10px] font-bold text-slate-400 uppercase tracking-wide mb-1">Chiết khấu (%)</label>
                        <input type="number" id="pos-discount" min="0" max="100" value="0" oninput="calcCartTotals()" class="w-full border border-slate-200 rounded-xl py-2 px-3 focus:outline-none text-center font-bold text-slate-800 bg-slate-50">
                    </div>
                    <div>
                        <label class="block text-[10px] font-bold text-slate-400 uppercase tracking-wide mb-1">Phương thức</label>
                        <select id="pos-payment" class="w-full border border-slate-200 rounded-xl py-2 px-3 bg-slate-50 font-bold text-slate-800">
                            <option value="Tiền mặt">Tiền mặt</option>
                            <option value="Chuyển khoản">Chuyển khoản</option>
                            <option value="Quẹt thẻ">Quẹt thẻ</option>
                        </select>
                    </div>
                </div>
                <div class="flex justify-between items-center pt-3 border-t border-dashed border-slate-200">
                    <div>
                        <span class="text-[10px] text-slate-400 font-bold block">Tổng cộng</span>
                        <span id="pos-subtotal" class="text-xs text-slate-400 line-through">0 ₫</span>
                    </div>
                    <span id="pos-total-amount" class="text-xl font-black text-emerald-600">0 ₫</span>
                </div>
                <button onclick="checkoutCart()" class="w-full bg-emerald-600 hover:bg-emerald-700 text-white font-bold py-3.5 px-4 rounded-2xl shadow-lg flex justify-center items-center space-x-2">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor"><path fill-rule="evenodd" d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z" clip-rule="evenodd" /></svg>
                    <span>Thanh Toán Đơn Hàng</span>
                </button>
            </div>
        </div>
    </div>

    <!-- ==================== KIOTBOT AI CHAT (ADVANCED WITH VOICE) ==================== -->
    <div id="chatbot-wrapper" class="fixed bottom-24 right-4 md:bottom-6 md:right-24 z-50 flex flex-col items-end pointer-events-none">
        
        <!-- Chat Panel -->
        <div id="chatbot-panel" class="bg-white w-[340px] md:w-96 rounded-2xl shadow-2xl border border-slate-200 mb-3 overflow-hidden flex-col pointer-events-auto hidden chat-slide-up">
            
            <div class="bg-gradient-to-r from-emerald-600 to-teal-600 p-3.5 flex justify-between items-center shadow-md z-10 relative">
                <div class="flex items-center space-x-3">
                    <div class="w-9 h-9 bg-white/20 rounded-full flex items-center justify-center relative">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-white" viewBox="0 0 20 20" fill="currentColor"><path fill-rule="evenodd" d="M10 2a8 8 0 100 16 8 8 0 000-16zM7 9a1 1 0 000 2h6a1 1 0 100-2H7z" clip-rule="evenodd" /></svg>
                        <span class="absolute bottom-0 right-0 w-2.5 h-2.5 bg-green-400 border-2 border-emerald-600 rounded-full"></span>
                    </div>
                    <div>
                        <h4 class="text-white font-extrabold text-sm leading-tight tracking-wide">KiotBot AI</h4>
                        <span class="text-emerald-100 text-[10px] font-medium">Trợ lý cửa hàng (Có Voice)</span>
                    </div>
                </div>
                <button onclick="toggleChatbot()" class="text-white hover:text-emerald-200 transition-colors"><svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" /></svg></button>
            </div>
            
            <div id="chatbot-messages" class="h-72 bg-slate-50 p-4 overflow-y-auto flex flex-col space-y-3.5 text-sm">
                <!-- Welcome Message -->
                <div class="flex justify-start">
                    <div class="bg-white border border-slate-200 text-slate-700 py-2.5 px-3.5 rounded-2xl rounded-tl-none max-w-[85%] shadow-sm leading-relaxed">
                        Chào bạn, tôi là KiotBot AI 🤖.<br>
                        Bạn có thể nhập tin nhắn hoặc bấm 🎤 để hỏi về:<br>
                        - <span class="text-emerald-600 font-bold">"Doanh thu hôm nay/tháng này"</span><br>
                        - <span class="text-emerald-600 font-bold">"Giá iPhone 15"</span><br>
                        - <span class="text-emerald-600 font-bold">"Còn bao nhiêu Xiaomi"</span><br>
                        - <span class="text-emerald-600 font-bold">"Kiểm tra hàng sắp hết"</span>
                    </div>
                </div>
            </div>
            
            <div class="p-3 border-t border-slate-100 bg-white">
                <form onsubmit="handleChatbotSubmit(event)" class="flex items-center space-x-1.5 bg-slate-50 border border-slate-200 rounded-xl p-1 pr-1.5 focus-within:border-emerald-500 focus-within:ring-1 focus-within:ring-emerald-500 transition-all">
                    <!-- Voice Mic Button -->
                    <button type="button" id="btn-voice-chat" onclick="startVoiceRecognition()" title="Tìm kiếm bằng giọng nói" class="p-2 text-slate-400 hover:text-emerald-600 transition-colors rounded-full focus:outline-none relative shrink-0">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 11a7 7 0 01-7 7m0 0a7 7 0 01-7-7m7 7v4m0 0H8m4 0h4m-4-8a3 3 0 01-3-3V5a3 3 0 116 0v6a3 3 0 01-3 3z" /></svg>
                        <span id="voice-indicator" class="absolute top-1 right-1 w-2 h-2 bg-red-500 rounded-full animate-ping hidden"></span>
                    </button>
                    
                    <input type="text" id="chatbot-input" placeholder="Hỏi AI hoặc nói..." class="flex-1 bg-transparent border-none px-1 py-2 text-sm focus:outline-none focus:ring-0 text-slate-700 placeholder-slate-400">
                    
                    <button type="submit" class="bg-emerald-600 text-white w-8 h-8 rounded-lg flex items-center justify-center hover:bg-emerald-700 shrink-0 shadow-sm transition-transform active:scale-95">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 transform rotate-90" viewBox="0 0 20 20" fill="currentColor"><path d="M10.894 2.553a1 1 0 00-1.788 0l-7 14a1 1 0 001.169 1.409l5-1.429A1 1 0 009 15.571V11a1 1 0 112 0v4.571a1 1 0 00.725.962l5 1.428a1 1 0 001.17-1.408l-7-14z" /></svg>
                    </button>
                </form>
            </div>
        </div>

        <!-- Trigger Button -->
        <button onclick="toggleChatbot()" class="pointer-events-auto bg-gradient-to-r from-emerald-500 to-teal-500 hover:from-emerald-600 hover:to-teal-600 text-white p-3.5 rounded-full shadow-2xl flex items-center justify-center transform transition-transform hover:scale-110">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2.5" d="M8 10h.01M12 10h.01M16 10h.01M9 16H5a2 2 0 01-2-2V6a2 2 0 012-2h14a2 2 0 012 2v8a2 2 0 01-2 2h-5l-5 5v-5z" /></svg>
        </button>
    </div>

    <!-- ==================== MODALS (Import, Product, Customer, Staff, Receipt) ==================== -->
    <div id="modal-import-stock" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-[60] flex items-center justify-center hidden p-4">
        <div class="bg-white w-full max-w-2xl rounded-2xl shadow-xl overflow-hidden flex flex-col max-h-full">
            <div class="bg-blue-600 text-white px-5 py-4 flex justify-between items-center shrink-0">
                <h3 class="font-bold text-base">Nhập Kho Hàng Loạt</h3>
                <button onclick="closeImportStockModal()" class="text-white hover:text-slate-200"><svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" /></svg></button>
            </div>
            <div class="p-4 bg-slate-50 border-b border-slate-200 shrink-0">
                <div class="relative">
                    <span class="absolute inset-y-0 left-0 flex items-center pl-3 text-slate-400">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" viewBox="0 0 20 20" fill="currentColor"><path fill-rule="evenodd" d="M8 4a4 4 0 100 8 4 4 0 000-8zM2 8a6 6 0 1110.89 3.476l4.817 4.817a1 1 0 01-1.414 1.414l-4.816-4.816A6 6 0 012 8z" clip-rule="evenodd" /></svg>
                    </span>
                    <input type="text" id="import-search" oninput="searchImportProduct()" placeholder="Gõ tên sản phẩm cần nhập..." class="w-full pl-9 pr-3 py-2 border border-slate-300 rounded-xl focus:outline-none focus:ring-2 focus:ring-blue-500 text-sm">
                    <div id="import-search-results" class="absolute top-full left-0 right-0 mt-1 bg-white border border-slate-200 rounded-xl shadow-lg max-h-48 overflow-y-auto hidden z-10"></div>
                </div>
            </div>
            <div class="flex-1 overflow-y-auto p-4">
                <h4 class="text-xs font-bold text-slate-500 mb-3 uppercase">Danh sách sản phẩm nhập</h4>
                <div id="import-list-container" class="space-y-3"></div>
            </div>
            <div class="p-4 border-t border-slate-200 bg-white flex justify-between items-center shrink-0">
                <div class="text-sm font-bold text-slate-600">Tổng tiền nhập: <span id="import-total-money" class="text-blue-600 text-lg">0 ₫</span></div>
                <button onclick="processBulkImport()" class="bg-blue-600 hover:bg-blue-700 text-white font-bold py-2.5 px-6 rounded-xl shadow">Xác nhận Lưu kho</button>
            </div>
        </div>
    </div>

    <div id="modal-product" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-[60] flex items-center justify-center hidden p-4">
        <div class="bg-white w-full max-w-md rounded-2xl shadow-xl overflow-hidden flex flex-col max-h-[90vh]">
            <div class="bg-emerald-600 text-white px-5 py-4 flex justify-between items-center shrink-0">
                <h3 class="font-bold text-base" id="product-modal-title">Thêm Sản Phẩm Mới</h3>
                <button onclick="closeProductModal()" class="text-white hover:text-slate-200"><svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" /></svg></button>
            </div>
            <form id="product-form" onsubmit="saveProduct(event)" class="p-5 space-y-4 overflow-y-auto">
                <input type="hidden" id="edit-product-id">
                <div class="grid grid-cols-2 gap-3">
                    <div>
                        <label class="block text-xs font-bold text-slate-500 mb-1">Mã SP *</label>
                        <input type="text" id="prod-code" required class="w-full p-2.5 border border-slate-200 rounded-xl focus:ring-1 focus:ring-emerald-500 text-sm focus:outline-none">
                    </div>
                    <div>
                        <label class="block text-xs font-bold text-slate-500 mb-1">Hãng *</label>
                        <select id="prod-category" required class="w-full p-2.5 border border-slate-200 rounded-xl bg-white text-sm focus:outline-none">
                            <option value="iPhone">iPhone (Apple)</option>
                            <option value="Samsung">Samsung</option>
                            <option value="Oppo">Oppo</option>
                            <option value="Xiaomi">Xiaomi</option>
                            <option value="Phụ kiện">Phụ kiện</option>
                        </select>
                    </div>
                </div>
                <div><label class="block text-xs font-bold text-slate-500 mb-1">Tên điện thoại *</label><input type="text" id="prod-name" required class="w-full p-2.5 border border-slate-200 rounded-xl focus:ring-1 focus:ring-emerald-500 text-sm focus:outline-none"></div>
                <div class="grid grid-cols-2 gap-3">
                    <div><label class="block text-xs font-bold text-slate-500 mb-1">Giá Vốn *</label><input type="number" id="prod-cost" required min="0" class="w-full p-2.5 border border-slate-200 rounded-xl text-sm focus:outline-none"></div>
                    <div><label class="block text-xs font-bold text-slate-500 mb-1">Giá Bán Lẻ *</label><input type="number" id="prod-selling" required min="0" class="w-full p-2.5 border border-slate-200 rounded-xl text-sm focus:outline-none"></div>
                </div>
                <div class="grid grid-cols-2 gap-3">
                    <div><label class="block text-xs font-bold text-slate-500 mb-1">Tồn kho ban đầu *</label><input type="number" id="prod-stock" required min="0" class="w-full p-2.5 border border-slate-200 rounded-xl text-sm focus:outline-none"></div>
                    <div><label class="block text-xs font-bold text-slate-500 mb-1">Báo hết (Min) *</label><input type="number" id="prod-minStock" required min="1" class="w-full p-2.5 border border-slate-200 rounded-xl text-sm focus:outline-none"></div>
                </div>
                <div class="pt-2 flex space-x-2">
                    <button type="button" onclick="closeProductModal()" class="w-1/2 bg-slate-100 text-slate-700 py-3 rounded-xl font-bold text-sm">Hủy bỏ</button>
                    <button type="submit" class="w-1/2 bg-emerald-600 text-white py-3 rounded-xl font-bold text-sm shadow">Lưu sản phẩm</button>
                </div>
            </form>
        </div>
    </div>

    <!-- Custom HTML structure kept for Customers, Staff, Receipts... Skipped redundancy in comments -->
    <div id="modal-customer" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-[60] flex items-center justify-center hidden p-4">
        <!-- Kept exactly as before -->
        <div class="bg-white w-full max-w-md rounded-2xl shadow-xl overflow-hidden">
            <div class="bg-emerald-600 text-white px-5 py-4 flex justify-between items-center"><h3 class="font-bold text-base" id="customer-modal-title">Thêm Khách Hàng</h3><button onclick="closeCustomerModal()" class="text-white hover:text-slate-200"><svg class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" /></svg></button></div>
            <form id="customer-form" onsubmit="saveCustomer(event)" class="p-5 space-y-4"><input type="hidden" id="edit-customer-id"><input type="hidden" id="is-pos-adding" value="false"><div class="grid grid-cols-2 gap-3"><div class="col-span-2"><label class="block text-xs font-bold text-slate-500 mb-1">Tên khách hàng *</label><input type="text" id="cust-name" required class="w-full p-2.5 border border-slate-200 rounded-xl text-sm focus:outline-none"></div><div><label class="block text-xs font-bold text-slate-500 mb-1">Mã khách *</label><input type="text" id="cust-code" required class="w-full p-2.5 border border-slate-200 rounded-xl text-sm focus:outline-none"></div><div><label class="block text-xs font-bold text-slate-500 mb-1">Số điện thoại *</label><input type="tel" id="cust-phone" required class="w-full p-2.5 border border-slate-200 rounded-xl text-sm focus:outline-none"></div></div><div><label class="block text-xs font-bold text-slate-500 mb-1">Địa chỉ giao dịch</label><input type="text" id="cust-address" class="w-full p-2.5 border border-slate-200 rounded-xl text-sm focus:outline-none"></div><div class="pt-2 flex space-x-2"><button type="button" onclick="closeCustomerModal()" class="w-1/2 bg-slate-100 text-slate-700 py-3 rounded-xl font-bold text-sm">Hủy bỏ</button><button type="submit" class="w-1/2 bg-emerald-600 text-white py-3 rounded-xl font-bold text-sm shadow">Lưu lại</button></div></form>
        </div>
    </div>
    
    <div id="modal-staff" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-[60] flex items-center justify-center hidden p-4">
        <!-- Kept exactly as before -->
        <div class="bg-white w-full max-w-md rounded-2xl shadow-xl overflow-hidden"><div class="bg-emerald-600 text-white px-5 py-4 flex justify-between items-center"><h3 class="font-bold text-base" id="staff-modal-title">Thêm Nhân Sự</h3><button onclick="closeStaffModal()" class="text-white hover:text-slate-200"><svg class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" /></svg></button></div><form id="staff-form" onsubmit="saveStaff(event)" class="p-5 space-y-4"><input type="hidden" id="edit-staff-id"><div class="grid grid-cols-2 gap-3"><div><label class="block text-xs font-bold text-slate-500 mb-1">Mã NV *</label><input type="text" id="staff-code" required class="w-full p-2.5 border border-slate-200 rounded-xl text-sm focus:outline-none"></div><div><label class="block text-xs font-bold text-slate-500 mb-1">Phân quyền *</label><select id="staff-role" required class="w-full p-2.5 border border-slate-200 rounded-xl bg-white text-sm focus:outline-none"><option value="Staff">Staff (Bán hàng)</option><option value="Admin">Admin (Quản lý)</option></select></div><div class="col-span-2"><label class="block text-xs font-bold text-slate-500 mb-1">Họ và Tên *</label><input type="text" id="staff-name" required class="w-full p-2.5 border border-slate-200 rounded-xl text-sm focus:outline-none"></div><div><label class="block text-xs font-bold text-slate-500 mb-1">Tài khoản *</label><input type="text" id="staff-username" required class="w-full p-2.5 border border-slate-200 rounded-xl text-sm focus:outline-none"></div><div><label class="block text-xs font-bold text-slate-500 mb-1">Mật khẩu *</label><input type="password" id="staff-password" required class="w-full p-2.5 border border-slate-200 rounded-xl text-sm focus:outline-none"></div></div><div class="pt-2 flex space-x-2"><button type="button" onclick="closeStaffModal()" class="w-1/2 bg-slate-100 text-slate-700 py-3 rounded-xl font-bold text-sm">Hủy</button><button type="submit" class="w-1/2 bg-emerald-600 text-white py-3 rounded-xl font-bold text-sm shadow">Lưu nhân sự</button></div></form></div>
    </div>
    
    <div id="modal-receipt" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-[60] flex items-center justify-center p-4 hidden">
        <div class="bg-white w-full max-w-sm rounded-2xl shadow-xl overflow-hidden flex flex-col max-h-[90vh]">
            <div class="bg-slate-800 text-white px-4 py-3 flex justify-between items-center shrink-0">
                <h3 class="font-bold text-sm flex items-center">Hóa Đơn #<span id="receipt-invoice-code"></span></h3>
                <button onclick="closeReceiptModal()" class="text-slate-400 hover:text-white"><svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" /></svg></button>
            </div>
            <div class="flex-1 overflow-y-auto p-4 bg-amber-50/20">
                <div class="bg-white border border-slate-200 rounded-xl p-4 shadow-sm space-y-3 text-xs" id="printable-receipt-content">
                    <div class="text-center space-y-0.5">
                        <h4 class="font-bold text-sm text-emerald-600 uppercase">KiotPro Store</h4>
                        <p class="text-[10px] text-slate-400">Trụ sở chính</p>
                    </div>
                    <div class="border-t border-dashed border-slate-300"></div>
                    <div class="space-y-1 text-slate-600 text-[11px]">
                        <div class="flex justify-between"><span>Mã hóa đơn:</span><span class="font-bold text-slate-800" id="receipt-id"></span></div>
                        <div class="flex justify-between"><span>Thời gian:</span><span id="receipt-date"></span></div>
                        <div class="flex justify-between"><span>Khách hàng:</span><span class="font-bold text-slate-800" id="receipt-customer-name"></span></div>
                        <div class="flex justify-between"><span>Nhân viên:</span><span class="font-bold text-emerald-700" id="receipt-seller"></span></div>
                    </div>
                    <div class="border-t border-dashed border-slate-300"></div>
                    <div class="space-y-2" id="receipt-items-container"></div>
                    <div class="border-t border-dashed border-slate-300"></div>
                    <div class="space-y-1 text-[11px]">
                        <div class="flex justify-between text-slate-500"><span>Tổng hàng:</span><span id="receipt-subtotal"></span></div>
                        <div class="flex justify-between text-slate-500"><span>Chiết khấu:</span><span id="receipt-discount"></span></div>
                        <div class="flex justify-between font-bold text-sm text-slate-800 pt-1.5 border-t border-slate-100">
                            <span>Thanh toán:</span><span class="text-emerald-600" id="receipt-total-amount"></span>
                        </div>
                    </div>
                </div>
            </div>
            <div class="bg-slate-50 px-4 py-3 border-t border-slate-100 flex space-x-2 shrink-0">
                <button onclick="closeReceiptModal()" class="w-full bg-slate-200 text-slate-700 font-bold py-2.5 rounded-xl text-xs hover:bg-slate-300">Đóng</button>
            </div>
        </div>
    </div>

    <!-- TOAST CONTAINER -->
    <div id="toast-container" class="fixed bottom-24 left-4 right-4 md:top-6 md:bottom-auto md:left-auto md:right-6 md:w-80 space-y-2 z-[100] pointer-events-none mx-auto"></div>

    <script>
        // MOCK DATA (JUNE 2026)
        const defaultStaffs = [
            { id: "s1", code: "NV001", name: "Nguyễn Minh Quân", username: "nhanvien", password: "123", role: "Staff" },
            { id: "admin", code: "ADMIN", name: "Trần Hoàng Long (Quản lý)", username: "admin", password: "123", role: "Admin" }
        ];
        const defaultProducts = [
            { id: "p1", code: "DT001", name: "iPhone 15 Pro Max 256GB", category: "iPhone", costPrice: 27000000, sellingPrice: 29490000, stock: 12, minStock: 3 },
            { id: "p2", code: "DT002", name: "Samsung Galaxy S24 Ultra", category: "Samsung", costPrice: 25000000, sellingPrice: 27890000, stock: 8, minStock: 2 },
            { id: "p3", code: "DT003", name: "iPhone 13 128GB", category: "iPhone", costPrice: 12500000, sellingPrice: 13990000, stock: 18, minStock: 4 },
            { id: "p4", code: "DT004", name: "Oppo Reno11 5G", category: "Oppo", costPrice: 8000000, sellingPrice: 9190000, stock: 5, minStock: 3 },
            { id: "p5", code: "DT005", name: "Xiaomi Redmi Note 13", category: "Xiaomi", costPrice: 4000000, sellingPrice: 4690000, stock: 2, minStock: 5 } // Demo Low Stock
        ];
        const defaultCustomers = [
            { id: "c1", code: "KH001", name: "Phan Anh Tuấn", phone: "0901234567", address: "Quận 3, TP. HCM", totalSpent: 43480000 },
            { id: "c2", code: "KH002", name: "Trần Thị Ánh Tuyết", phone: "0987654321", address: "Hoàn Kiếm, Hà Nội", totalSpent: 13990000 },
            { id: "c_guest", code: "KHACH_LE", name: "Khách lẻ", phone: "-", address: "-", totalSpent: 0 }
        ];
        const defaultInvoices = [
            { id: "inv1", code: "HD5320", date: "2026-06-03 10:30", customerId: "c1", customerName: "Phan Anh Tuấn", paymentMethod: "Chuyển khoản", items: [{ id: "p1", code: "DT001", name: "iPhone 15 Pro Max 256GB", sellingPrice: 29490000, quantity: 1, costPrice: 27000000 }], subtotal: 29490000, discount: 0, totalAmount: 29490000, costTotal: 27000000, profit: 2490000, seller: "Trần Hoàng Long" }
        ];

        let staffs = JSON.parse(localStorage.getItem("kiot_an_staffs")) || defaultStaffs;
        let products = JSON.parse(localStorage.getItem("kiot_an_products")) || defaultProducts;
        let customers = JSON.parse(localStorage.getItem("kiot_an_customers")) || defaultCustomers;
        let invoices = JSON.parse(localStorage.getItem("kiot_an_invoices")) || defaultInvoices;

        let currentUser = null;
        let cart = [];
        let importCart = [];
        let currentSelectedCategory = "all";
        let reportPeriod = "day";
        let revenueChartObj = null;

        function saveState() {
            localStorage.setItem("kiot_an_staffs", JSON.stringify(staffs));
            localStorage.setItem("kiot_an_products", JSON.stringify(products));
            localStorage.setItem("kiot_an_customers", JSON.stringify(customers));
            localStorage.setItem("kiot_an_invoices", JSON.stringify(invoices));
        }

        // ==================== AUTH & LAYOUT ====================
        function handleLogin(event) {
            event.preventDefault();
            const u = document.getElementById("login-username").value.trim().toLowerCase();
            const p = document.getElementById("login-password").value.trim();
            const matched = staffs.find(s => s.username === u && s.password === p);
            if (matched) {
                currentUser = matched;
                document.getElementById("login-screen").classList.add("hidden");
                showToast(`Xin chào, ${matched.name}!`);
                document.getElementById("sidebar-user-name").innerText = matched.name;
                document.getElementById("sidebar-user-role").innerText = matched.role === "Admin" ? "Quản lý hệ thống" : "Nhân viên bán hàng";
                buildNavigations();
                switchTab('pos');
            } else {
                showToast("Sai tài khoản hoặc mật khẩu!", "error");
            }
        }

        function handleLogout() {
            if (confirm("Đăng xuất hệ thống?")) {
                currentUser = null;
                cart = []; renderCart();
                document.getElementById("login-screen").classList.remove("hidden");
            }
        }

        function buildNavigations() {
            const bottomNav = document.getElementById("bottom-navigation-bar");
            const sideNav = document.getElementById("desktop-navigation-container");
            bottomNav.innerHTML = ""; sideNav.innerHTML = "";
            let tabs = [
                { id: "pos", name: "Bán hàng", icon: `<svg class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor"><path d="M3 1a1 1 0 000 2h1.22l.305 1.222a.997.997 0 00.01.042l1.358 5.43-.893.892C3.74 11.846 4.532 13 5.619 13H9c1.105 0 2-.895 2-2V8h2v3c0 1.105.895 2 2 2h1.381c1.087 0 1.878-1.154 1.319-2.116l-2-3.46A1 1 0 0014 7h-3V3a1 1 0 00-2 0v4H7V3.586L5.707 2.293A1 1 0 005 2H3.82L3.516 1.18A1 1 0 003 1z" /><path d="M6 16a2 2 0 114 0 2 2 0 01-4 0zM14 16a2 2 0 114 0 2 2 0 01-4 0z" /></svg>` },
                { id: "products", name: "Kho hàng", icon: `<svg class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor"><path fill-rule="evenodd" d="M10 2a8 8 0 100 16 8 8 0 000-16zM7 9a1 1 0 000 2h6a1 1 0 100-2H7z" clip-rule="evenodd" /></svg>` },
                { id: "invoices", name: "Hóa đơn", icon: `<svg class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor"><path fill-rule="evenodd" d="M4 4a2 2 0 012-2h4.586A1 1 0 0113 2.586L15.414 5A1 1 0 0116 5.586V16a2 2 0 01-2 2H6a2 2 0 01-2-2V4zm2 6a1 1 0 011-1h6a1 1 0 110 2H7a1 1 0 01-1-1zm1 3a1 1 0 100 2h6a1 1 0 100-2H7z" clip-rule="evenodd" /></svg>` },
                { id: "customers", name: "Khách hàng", icon: `<svg class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor"><path d="M9 6a3 3 0 11-6 0 3 3 0 016 0zM17 6a3 3 0 11-6 0 3 3 0 006 0zM12.93 17c.046-.327.07-.66.07-1a6.97 6.97 0 00-1.5-4.33A5 5 0 0119 16v1h-6.07zM6 11a5 5 0 015 5v1H1v-1a5 5 0 015-5z" /></svg>` }
            ];
            if (currentUser && currentUser.role === "Admin") {
                tabs.push(
                    { id: "staffs", name: "Nhân viên", icon: `<svg class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor"><path d="M13 6a3 3 0 11-6 0 3 3 0 016 0zM18 8a2 2 0 11-4 0 2 2 0 014 0zM14 15a4 4 0 00-8 0v3h8v-3zM6 8a2 2 0 11-4 0 2 2 0 014 0zM16 18v-3a5.972 5.972 0 00-.75-2.906A3.005 3.005 0 0119 15v3h-3zM4.75 12.094A5.973 5.973 0 004 15v3H1v-3a3 3 0 013.75-2.906z" /></svg>` },
                    { id: "reports", name: "Báo cáo", icon: `<svg class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor"><path d="M2 10a8 8 0 018-8v8h8a8 8 0 11-16 0z" /><path d="M12 2.252A8.014 8.014 0 0117.748 8H12V2.252z" /></svg>` }
                );
            }
            tabs.forEach(t => {
                const btnMobile = document.createElement("button");
                btnMobile.className = "mobile-nav-btn flex flex-col items-center justify-center w-14 py-1 text-slate-400 transition-all";
                btnMobile.dataset.tab = t.id; btnMobile.onclick = () => switchTab(t.id);
                btnMobile.innerHTML = `${t.icon}<span class="text-[9px] mt-0.5">${t.name}</span>`;
                bottomNav.appendChild(btnMobile);

                const btnDesktop = document.createElement("button");
                btnDesktop.className = "desktop-nav-btn flex items-center space-x-3 w-full p-3 rounded-xl text-slate-400 hover:bg-slate-800 transition-all text-left";
                btnDesktop.dataset.tab = t.id; btnDesktop.onclick = () => switchTab(t.id);
                btnDesktop.innerHTML = `${t.icon}<span class="font-medium text-sm">${t.name}</span>`;
                sideNav.appendChild(btnDesktop);
            });
        }

        function switchTab(tabId) {
            if (!currentUser) return;
            document.querySelectorAll(".tab-content").forEach(el => el.classList.add("hidden"));
            document.getElementById(`section-${tabId}`).classList.remove("hidden");
            const titles = { pos:"Bán hàng (POS)", products:"Quản lý Kho", invoices:"Hóa đơn", customers:"Khách hàng", staffs:"Nhân sự", reports:"Báo cáo" };
            document.getElementById("desktop-header-title").innerText = titles[tabId];

            document.querySelectorAll(".mobile-nav-btn").forEach(b => { b.classList.replace("text-emerald-600", "text-slate-400"); b.classList.remove("font-extrabold"); });
            document.querySelectorAll(".desktop-nav-btn").forEach(b => { b.classList.replace("bg-emerald-600", "hover:bg-slate-800"); b.classList.replace("text-white", "text-slate-400"); });
            
            const mBtn = document.querySelector(`.mobile-nav-btn[data-tab="${tabId}"]`);
            if (mBtn) { mBtn.classList.replace("text-slate-400", "text-emerald-600"); mBtn.classList.add("font-extrabold"); }
            const dBtn = document.querySelector(`.desktop-nav-btn[data-tab="${tabId}"]`);
            if (dBtn) { dBtn.classList.replace("hover:bg-slate-800", "bg-emerald-600"); dBtn.classList.replace("text-slate-400", "text-white"); }

            const prodBtnGrp = document.getElementById("product-action-buttons");
            if (prodBtnGrp) prodBtnGrp.style.display = currentUser.role === "Admin" ? "flex" : "none";

            if (tabId === 'pos') initPosScreen();
            else if (tabId === 'products') renderProductTable();
            else if (tabId === 'invoices') renderInvoiceTable();
            else if (tabId === 'customers') renderCustomerTable();
            else if (tabId === 'staffs') renderStaffTable();
            else if (tabId === 'reports') renderReportDashboard();
        }

        function formatVND(amount) { return new Intl.NumberFormat('vi-VN', { style: 'currency', currency: 'VND' }).format(amount); }

        function showToast(message, type = "success") {
            const container = document.getElementById("toast-container");
            const toast = document.createElement("div");
            toast.className = `flex items-center space-x-2 px-4 py-3 rounded-2xl shadow-xl text-white font-semibold text-xs transition-all duration-300 transform translate-y-2 opacity-0 justify-center ${type==='success'?'bg-emerald-600':type==='warning'?'bg-amber-500':'bg-red-600'}`;
            toast.innerHTML = `<span>${message}</span>`;
            container.appendChild(toast);
            setTimeout(() => toast.classList.remove("translate-y-2", "opacity-0"), 10);
            setTimeout(() => { toast.classList.add("translate-y-2", "opacity-0"); setTimeout(() => toast.remove(), 300); }, 2500);
        }

        // ==================== ADVANCED AI CHATBOT + VOICE SEARCH ====================
        let speechRecognition;

        function initSpeechRecognition() {
            window.SpeechRecognition = window.SpeechRecognition || window.webkitSpeechRecognition;
            if (window.SpeechRecognition) {
                speechRecognition = new SpeechRecognition();
                speechRecognition.lang = 'vi-VN';
                speechRecognition.interimResults = false;
                speechRecognition.maxAlternatives = 1;

                speechRecognition.onstart = function() {
                    document.getElementById('voice-indicator').classList.remove('hidden');
                    document.getElementById('btn-voice-chat').classList.add('text-red-500', 'bg-red-50');
                    document.getElementById('chatbot-input').placeholder = "Đang nghe bạn nói...";
                };

                speechRecognition.onresult = function(event) {
                    const transcript = event.results[0][0].transcript;
                    document.getElementById('chatbot-input').value = transcript;
                    // Tự động gửi sau khi nhận diện xong
                    setTimeout(() => {
                        handleChatbotSubmit(new Event('submit'));
                    }, 300);
                };

                speechRecognition.onspeechend = function() {
                    speechRecognition.stop();
                    resetVoiceUI();
                };

                speechRecognition.onerror = function(event) {
                    console.error('Speech recognition error:', event.error);
                    resetVoiceUI();
                    showToast("Không nhận diện được âm thanh. Vui lòng thử lại.", "warning");
                };
            }
        }

        function resetVoiceUI() {
            document.getElementById('voice-indicator').classList.add('hidden');
            document.getElementById('btn-voice-chat').classList.remove('text-red-500', 'bg-red-50');
            document.getElementById('chatbot-input').placeholder = "Hỏi AI hoặc nói...";
        }

        function startVoiceRecognition() {
            if (!speechRecognition) {
                initSpeechRecognition();
            }
            if (speechRecognition) {
                try {
                    speechRecognition.start();
                } catch(e) {
                    console.log("Recognition is already running.");
                }
            } else {
                showToast("Trình duyệt của bạn không hỗ trợ tìm kiếm bằng giọng nói.", "error");
            }
        }

        function toggleChatbot() {
            const panel = document.getElementById("chatbot-panel");
            if(panel.classList.contains("hidden")) {
                panel.classList.remove("hidden");
                document.getElementById("chatbot-input").focus();
            } else {
                panel.classList.add("hidden");
            }
        }

        function handleChatbotSubmit(e) {
            e.preventDefault();
            const input = document.getElementById("chatbot-input");
            const text = input.value.trim();
            if(!text) return;

            appendChatMessage(text, 'user');
            input.value = '';

            setTimeout(() => {
                const response = generateBotResponse(text);
                appendChatMessage(response, 'bot');
            }, 500);
        }

        function appendChatMessage(text, sender) {
            const container = document.getElementById("chatbot-messages");
            const div = document.createElement("div");
            if (sender === 'user') {
                div.className = "flex justify-end";
                div.innerHTML = `<div class="bg-emerald-600 text-white py-2 px-3.5 rounded-2xl rounded-tr-none max-w-[85%] shadow-sm">${text}</div>`;
            } else {
                div.className = "flex justify-start";
                div.innerHTML = `<div class="bg-white border border-slate-200 text-slate-700 py-2.5 px-3.5 rounded-2xl rounded-tl-none max-w-[90%] shadow-sm leading-relaxed">${text}</div>`;
            }
            container.appendChild(div);
            container.scrollTop = container.scrollHeight;
        }

        function generateBotResponse(query) {
            const q = query.toLowerCase().trim();
            
            // 1. Chào hỏi
            if (q === "hi" || q === "hello" || q.includes("chào") || q === "alo") {
                return `Chào ${currentUser ? currentUser.name.split(' ').pop() : 'bạn'}, KiotBot đã sẵn sàng hỗ trợ. Bạn muốn tra cứu doanh thu, tồn kho hay giá bán hôm nay?`;
            }

            // 2. Tra cứu Doanh thu
            if (q.includes("doanh thu") || q.includes("bán được") || q.includes("tiền")) {
                let title = "Hôm nay (03/06/2026)";
                let filteredInv = invoices.filter(i => i.date.startsWith("2026-06-03")); // Giả lập data ngày hiện tại

                if (q.includes("tháng") || q.includes("tháng này")) {
                    title = "Tháng 06/2026";
                    filteredInv = invoices.filter(i => i.date.startsWith("2026-06"));
                } else if (q.includes("năm") || q.includes("năm nay")) {
                    title = "Năm 2026";
                    filteredInv = invoices.filter(i => i.date.startsWith("2026"));
                }

                const sum = filteredInv.reduce((acc, curr) => acc + curr.totalAmount, 0);
                const profit = filteredInv.reduce((acc, curr) => acc + curr.profit, 0);
                
                return `📊 **Doanh thu ${title}**: <strong class="text-emerald-600 text-base block mt-1">${formatVND(sum)}</strong> <span class="text-xs text-slate-500">Từ ${filteredInv.length} đơn hàng. Lợi nhuận: ${formatVND(profit)}</span>`;
            }

            // 3. Cảnh báo Hết hàng / Nhập kho
            if (q.includes("hết hàng") || q.includes("sắp hết") || q.includes("nhập thêm") || q.includes("cần nhập")) {
                const lows = products.filter(p => p.stock <= p.minStock);
                if(lows.length === 0) return "Tuyệt vời, tất cả hàng trong kho đều ở mức an toàn! ✅";
                
                let res = `⚠️ Có **${lows.length}** sản phẩm sắp hết hàng:<br><ul class="list-disc pl-5 mt-1 space-y-1">`;
                lows.slice(0, 5).forEach(p => res += `<li>${p.name} (Tồn: <strong class="text-red-500">${p.stock}</strong>)</li>`);
                if (lows.length > 5) res += `<li>...và ${lows.length - 5} mã khác.</li>`;
                res += "</ul>";
                return res;
            }

            // 4. Tra cứu Giá bán cụ thể
            if (q.includes("giá") || q.includes("bao nhiêu tiền")) {
                let foundProd = products.find(p => q.includes(p.name.toLowerCase()));
                
                if (!foundProd) {
                    // Logic tìm tương đối (cắt bớt từ khóa)
                    let words = q.replace("giá", "").replace("bao nhiêu tiền", "").replace("của", "").trim().split(" ");
                    let searchKey = words.find(w => w.length >= 3); // Lấy key dài
                    if (searchKey) {
                        foundProd = products.find(p => p.name.toLowerCase().includes(searchKey));
                    }
                }

                if (foundProd) {
                    return `🏷️ **${foundProd.name}**:<br>Giá bán lẻ: <strong class="text-emerald-600">${formatVND(foundProd.sellingPrice)}</strong> (Tồn kho: ${foundProd.stock}).`;
                } else {
                     return "Tôi không tìm thấy sản phẩm này. Bạn vui lòng đọc tên đầy đủ hơn (Ví dụ: Giá iPhone 15 Pro Max).";
                }
            }

            // 5. Kiểm tra Tồn kho / Số lượng cụ thể
            if (q.includes("còn bao nhiêu") || q.includes("tồn kho") || q.includes("số lượng")) {
                let foundProd = products.find(p => q.includes(p.name.toLowerCase()));
                if (foundProd) {
                    return `📦 Sản phẩm **${foundProd.name}** đang còn <strong class="text-blue-600">${foundProd.stock}</strong> máy.`;
                }
                
                // Nếu hỏi chung theo Hãng
                const brands = ["iphone", "samsung", "oppo", "xiaomi", "realme"];
                for (let brand of brands) {
                    if (q.includes(brand)) {
                         const brProds = products.filter(p => p.category.toLowerCase() === brand);
                         const qty = brProds.reduce((a,b)=>a+b.stock, 0);
                         return `📦 Hãng **${brand.toUpperCase()}** đang còn tổng cộng <strong class="text-blue-600">${qty}</strong> máy trong kho.`;
                    }
                }

                const totalStock = products.reduce((acc, curr) => acc + curr.stock, 0);
                return `Hiện tổng kho KiotPro đang lưu trữ **${totalStock}** thiết bị. Bạn muốn xem số lượng của mã máy nào?`;
            }

            // 6. Tra cứu Khách hàng / VIP
            if (q.includes("khách hàng")) {
                if (q.includes("mua nhiều nhất") || q.includes("vip") || q.includes("chi tiêu")) {
                    let sorted = [...customers].sort((a,b) => b.totalSpent - a.totalSpent).filter(c => c.code !== "KHACH_LE");
                    if(sorted.length > 0) {
                         return `👑 Khách VIP của cửa hàng là **${sorted[0].name}**.<br>Đã chi tiêu tổng cộng: <strong class="text-emerald-600">${formatVND(sorted[0].totalSpent)}</strong>.`;
                    }
                }
                return `Cửa hàng đang có **${customers.length - 1}** khách hàng thân thiết được lưu trữ.`;
            }

            // Fallback (Mặc định)
            return "Xin lỗi, tôi chưa hiểu rõ ý bạn 🤔.<br>Bạn có thể thử hỏi:<br>🗣️ <i>'Doanh thu hôm nay'</i><br>🗣️ <i>'Giá iPhone 15'</i><br>🗣️ <i>'Hàng sắp hết'</i>";
        }


        // ==================== POS SYSTEM LOGIC ====================
        function initPosScreen() { renderPosCategories(); renderPosProducts(); populatePosCustomerDropdown(); calcCartTotals(); }
        function renderPosCategories() {
            const categories = ["all", "iPhone", "Samsung", "Oppo", "Xiaomi", "Phụ kiện"];
            const catContainer = document.getElementById("pos-category-filters");
            catContainer.innerHTML = "";
            categories.forEach(cat => {
                const isActive = currentSelectedCategory === cat;
                const btn = document.createElement("button");
                btn.className = `px-4 py-1.5 rounded-xl text-xs font-bold whitespace-nowrap transition-all border ${isActive ? "bg-emerald-600 text-white border-emerald-600 shadow-sm" : "bg-slate-50 text-slate-600 border-slate-200"}`;
                btn.innerText = cat === "all" ? "Tất cả" : cat;
                btn.onclick = () => { currentSelectedCategory = cat; renderPosCategories(); renderPosProducts(); };
                catContainer.appendChild(btn);
            });
        }
        function renderPosProducts() {
            const grid = document.getElementById("pos-products-grid");
            const searchKey = document.getElementById("pos-search").value.toLowerCase().trim();
            grid.innerHTML = "";
            const filtered = products.filter(p => {
                const matchCat = currentSelectedCategory === "all" || p.category === currentSelectedCategory;
                const matchSearch = p.name.toLowerCase().includes(searchKey) || p.code.toLowerCase().includes(searchKey);
                return matchCat && matchSearch;
            });
            filtered.forEach(p => {
                const isOutOfStock = p.stock <= 0;
                const card = document.createElement("div");
                card.className = `bg-white p-3 rounded-2xl border border-slate-200 flex flex-col justify-between space-y-2 relative ${isOutOfStock ? "opacity-60 bg-slate-50" : "active:scale-[0.98] transition-all cursor-pointer hover:border-emerald-500"}`;
                if (!isOutOfStock) card.onclick = () => addToCart(p);
                const badge = isOutOfStock ? `<span class="absolute top-2 right-2 bg-red-600 text-white text-[9px] px-1.5 py-0.5 rounded-md font-bold">HẾT HÀNG</span>` : `<span class="absolute top-2 right-2 bg-slate-100 text-slate-500 text-[9px] px-1.5 py-0.5 rounded-md font-bold">Còn ${p.stock}</span>`;
                card.innerHTML = `${badge}<div class="space-y-1 pt-2"><span class="text-[9px] text-emerald-600 font-bold block uppercase">${p.category}</span><h4 class="font-bold text-sm text-slate-800 line-clamp-2 leading-snug">${p.name}</h4></div><div class="flex justify-between items-center pt-2 border-t border-slate-50"><span class="font-extrabold text-sm text-slate-900">${formatVND(p.sellingPrice)}</span><div class="w-6 h-6 rounded-full bg-emerald-50 text-emerald-600 flex items-center justify-center font-bold text-lg hover:bg-emerald-600 hover:text-white transition-all">+</div></div>`;
                grid.appendChild(card);
            });
        }
        function populatePosCustomerDropdown() {
            const dropdown = document.getElementById("pos-customer-select");
            dropdown.innerHTML = "";
            customers.forEach(c => {
                const opt = document.createElement("option");
                opt.value = c.id; opt.innerText = c.name + (c.phone !== "-" ? ` (${c.phone})` : "");
                if (c.code === "KHACH_LE") opt.selected = true;
                dropdown.appendChild(opt);
            });
        }
        function addToCart(product) {
            const existing = cart.find(item => item.id === product.id);
            if (existing && existing.quantity + 1 > product.stock) return showToast("Không đủ hàng trong kho!", "warning");
            if (existing) existing.quantity++;
            else cart.push({ id: product.id, name: product.name, sellingPrice: product.sellingPrice, costPrice: product.costPrice, quantity: 1 });
            renderCart(); calcCartTotals(); showToast("Đã thêm vào giỏ");
        }
        function toggleCartSheet(show) {
            const sheet = document.getElementById("cart-bottom-sheet");
            if (show) sheet.classList.remove("hidden"); else sheet.classList.add("hidden");
        }
        function renderCart() {
            const container = document.getElementById("pos-cart-container");
            const floatBar = document.getElementById("floating-cart-bar");
            const deskCount = document.getElementById("desktop-cart-count");
            container.innerHTML = "";
            let count = 0;
            cart.forEach((item, index) => {
                count += item.quantity;
                container.innerHTML += `<div class="py-3 flex items-center justify-between space-x-2"><div class="flex-1 min-w-0 pr-2"><h5 class="font-bold text-xs text-slate-800 truncate">${item.name}</h5><span class="text-[10px] text-slate-400 font-bold block">${formatVND(item.sellingPrice)}</span></div><div class="flex items-center space-x-1 shrink-0"><button onclick="updateCartQty(${index}, -1)" class="w-6 h-6 rounded-full border border-slate-200 bg-white flex items-center justify-center font-bold text-xs">-</button><span class="w-6 text-center font-bold text-xs">${item.quantity}</span><button onclick="updateCartQty(${index}, 1)" class="w-6 h-6 rounded-full border border-slate-200 bg-white flex items-center justify-center font-bold text-xs">+</button></div></div>`;
            });
            if (count === 0) { container.innerHTML = `<div class="py-10 text-center text-slate-400 text-xs font-semibold">Giỏ hàng rỗng</div>`; floatBar.classList.add("hidden"); } 
            else { floatBar.classList.remove("hidden"); }
            document.getElementById("pos-cart-count").innerText = count; document.getElementById("cart-floating-count").innerText = count; deskCount.innerText = count;
        }
        function updateCartQty(index, offset) {
            const item = cart[index]; const original = products.find(p => p.id === item.id);
            if (item.quantity + offset <= 0) cart.splice(index, 1);
            else if (item.quantity + offset > original.stock) return showToast("Hết số lượng kho!", "warning");
            else item.quantity += offset;
            renderCart(); calcCartTotals();
        }
        function calcCartTotals() {
            let subtotal = 0; cart.forEach(item => subtotal += item.sellingPrice * item.quantity);
            const discountPct = parseInt(document.getElementById("pos-discount").value) || 0;
            const total = subtotal - Math.round(subtotal * (discountPct / 100));
            document.getElementById("pos-subtotal").innerText = formatVND(subtotal); document.getElementById("pos-total-amount").innerText = formatVND(total); document.getElementById("cart-floating-total").innerText = formatVND(total);
        }
        function checkoutCart() {
            if (cart.length === 0) return;
            const custId = document.getElementById("pos-customer-select").value; const customer = customers.find(c => c.id === custId);
            const discountPct = parseInt(document.getElementById("pos-discount").value) || 0;
            let subtotal = 0, costTotal = 0; cart.forEach(item => { subtotal += item.sellingPrice * item.quantity; costTotal += item.costPrice * item.quantity; });
            const totalAmount = subtotal - Math.round(subtotal * (discountPct / 100));
            
            const invoiceObj = { id: "inv_" + Date.now(), code: "HD" + Math.floor(1000 + Math.random() * 9000), date: "2026-06-03 " + new Date().getHours() + ":" + new Date().getMinutes(), customerId: customer.id, customerName: customer.name, paymentMethod: document.getElementById("pos-payment").value, items: [...cart], subtotal, discount: discountPct, totalAmount, costTotal, profit: totalAmount - costTotal, seller: currentUser ? currentUser.name : "Hệ thống" };
            cart.forEach(item => { const orig = products.find(p => p.id === item.id); if (orig) orig.stock -= item.quantity; });
            if (customer.code !== "KHACH_LE") customer.totalSpent += totalAmount;
            
            invoices.unshift(invoiceObj); saveState(); cart = []; renderCart(); calcCartTotals(); toggleCartSheet(false); renderPosProducts(); showToast(`Giao dịch thành công #${invoiceObj.code}`);
            
            // Show Receipt
            document.getElementById("receipt-invoice-code").innerText = invoiceObj.code; document.getElementById("receipt-id").innerText = invoiceObj.code; document.getElementById("receipt-date").innerText = invoiceObj.date; document.getElementById("receipt-customer-name").innerText = invoiceObj.customerName; document.getElementById("receipt-seller").innerText = invoiceObj.seller;
            const rcptCont = document.getElementById("receipt-items-container"); rcptCont.innerHTML = "";
            invoiceObj.items.forEach(item => { rcptCont.innerHTML += `<div class="flex justify-between text-[11px] font-semibold text-slate-700 py-1"><div class="pr-2 truncate"><span class="block text-slate-800">${item.name}</span><span class="text-[9px] text-slate-400">${item.quantity} x ${formatVND(item.sellingPrice)}</span></div><span class="font-extrabold text-slate-900 shrink-0">${formatVND(item.sellingPrice * item.quantity)}</span></div>`; });
            document.getElementById("receipt-subtotal").innerText = formatVND(invoiceObj.subtotal); document.getElementById("receipt-discount").innerText = invoiceObj.discount > 0 ? `${invoiceObj.discount}%` : "0"; document.getElementById("receipt-total-amount").innerText = formatVND(invoiceObj.totalAmount);
            document.getElementById("modal-receipt").classList.remove("hidden");
        }
        function closeReceiptModal() { document.getElementById("modal-receipt").classList.add("hidden"); }

        // ==================== INVENTORY BULK IMPORT ====================
        function openImportStockModal() { importCart = []; document.getElementById("import-search").value = ""; document.getElementById("import-search-results").classList.add("hidden"); renderImportList(); document.getElementById("modal-import-stock").classList.remove("hidden"); }
        function closeImportStockModal() { document.getElementById("modal-import-stock").classList.add("hidden"); }
        function searchImportProduct() {
            const q = document.getElementById("import-search").value.toLowerCase().trim(); const resDiv = document.getElementById("import-search-results");
            if(q.length < 1) { resDiv.classList.add("hidden"); return; }
            const matches = products.filter(p => p.name.toLowerCase().includes(q) || p.code.toLowerCase().includes(q)); resDiv.innerHTML = "";
            if(matches.length === 0) resDiv.innerHTML = `<div class="p-3 text-xs text-slate-500 text-center">Không tìm thấy sản phẩm</div>`;
            else matches.forEach(p => { const btn = document.createElement("button"); btn.className = "w-full text-left p-3 hover:bg-slate-50 border-b border-slate-100 flex justify-between items-center text-sm"; btn.innerHTML = `<span class="font-bold text-slate-700">${p.name}</span> <span class="text-xs text-slate-400">${p.code}</span>`; btn.onclick = () => addProductToImport(p); resDiv.appendChild(btn); });
            resDiv.classList.remove("hidden");
        }
        function addProductToImport(p) {
            document.getElementById("import-search").value = ""; document.getElementById("import-search-results").classList.add("hidden");
            const existing = importCart.find(i => i.id === p.id);
            if (existing) existing.qty += 1; else importCart.push({ id: p.id, name: p.name, code: p.code, costPrice: p.costPrice, qty: 1 });
            renderImportList();
        }
        function renderImportList() {
            const container = document.getElementById("import-list-container"); container.innerHTML = ""; let total = 0;
            if (importCart.length === 0) container.innerHTML = `<div class="text-center py-6 text-slate-400 text-sm">Chưa chọn sản phẩm nào</div>`;
            else importCart.forEach((item, idx) => {
                total += (item.qty * item.costPrice);
                container.innerHTML += `<div class="bg-white p-3 rounded-xl border border-slate-200 flex flex-col md:flex-row md:items-center justify-between gap-3 shadow-sm"><div class="flex-1"><h5 class="font-bold text-sm text-slate-800">${item.name}</h5><span class="text-[10px] text-slate-500 bg-slate-100 px-2 py-0.5 rounded">${item.code}</span></div><div class="flex items-center space-x-3"><div><label class="text-[10px] text-slate-400 block">Số lượng nhập</label><input type="number" min="1" value="${item.qty}" onchange="updateImportItem(${idx}, 'qty', this.value)" class="w-16 border border-slate-300 rounded px-2 py-1 text-sm font-bold text-center"></div><div><label class="text-[10px] text-slate-400 block">Đơn giá nhập</label><input type="number" min="0" value="${item.costPrice}" onchange="updateImportItem(${idx}, 'costPrice', this.value)" class="w-28 border border-slate-300 rounded px-2 py-1 text-sm font-bold text-right text-blue-600"></div><button onclick="removeImportItem(${idx})" class="mt-4 text-red-500 p-1 hover:bg-red-50 rounded"><svg class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" /></svg></button></div></div>`;
            });
            document.getElementById("import-total-money").innerText = formatVND(total);
        }
        function updateImportItem(idx, field, val) { val = parseInt(val) || 0; if(field === 'qty' && val < 1) val = 1; importCart[idx][field] = val; renderImportList(); }
        function removeImportItem(idx) { importCart.splice(idx, 1); renderImportList(); }
        function processBulkImport() {
            if(importCart.length === 0) return;
            importCart.forEach(item => { const prod = products.find(p => p.id === item.id); if(prod) { prod.stock += item.qty; prod.costPrice = item.costPrice; } });
            saveState(); closeImportStockModal(); renderProductTable(); showToast(`Đã nhập kho ${importCart.length} sản phẩm!`);
        }

        // ==================== INVENTORY, CUSTOMERS, REPORTS ====================
        function renderProductTable() {
            const container = document.getElementById("product-cards-container"); container.innerHTML = "";
            const search = document.getElementById("product-list-search").value.toLowerCase();
            products.filter(p => p.name.toLowerCase().includes(search)).forEach(p => {
                const card = document.createElement("div"); card.className = "bg-white p-4 rounded-2xl border border-slate-100 shadow-sm space-y-3";
                card.innerHTML = `<div class="flex justify-between items-start"><div><span class="text-[9px] text-slate-400 font-extrabold block">${p.code}</span><h4 class="font-bold text-sm text-slate-800">${p.name}</h4></div>${p.stock <= p.minStock && p.stock > 0 ? `<span class="bg-amber-100 text-amber-700 px-2 py-0.5 rounded text-[9px] font-bold">Sắp hết: ${p.stock}</span>` : (p.stock <= 0 ? '<span class="bg-red-100 text-red-700 px-2 py-0.5 rounded text-[9px] font-bold">HẾT</span>' : `<span class="bg-green-100 text-green-700 px-2 py-0.5 rounded text-[9px] font-bold">Tồn: ${p.stock}</span>`)}</div><div class="flex justify-between text-[11px] text-slate-500 font-semibold bg-slate-50 p-2 rounded-xl"><span>Giá nhập: ${formatVND(p.costPrice)}</span><span class="text-emerald-600">Giá bán: ${formatVND(p.sellingPrice)}</span></div>${currentUser.role === 'Admin' ? `<div class="flex justify-end space-x-3 text-xs pt-2 font-bold"><button onclick="openProductModal(true, '${p.id}')" class="text-blue-600">Sửa</button><button onclick="deleteProduct('${p.id}')" class="text-red-500">Xóa</button></div>` : ''}`;
                container.appendChild(card);
            });
        }
        function openProductModal() { document.getElementById("modal-product").classList.remove("hidden"); }
        function closeProductModal() { document.getElementById("modal-product").classList.add("hidden"); }
        function saveProduct(e) { e.preventDefault(); closeProductModal(); showToast("Lưu thành công"); }
        
        function renderInvoiceTable() {
            const container = document.getElementById("invoice-cards-container"); container.innerHTML = "";
            invoices.forEach(inv => {
                container.innerHTML += `<div class="bg-white p-3.5 rounded-2xl border border-slate-100 shadow-sm space-y-2"><div class="flex justify-between items-center text-xs"><span class="font-bold text-emerald-600 font-mono">#${inv.code}</span><span class="text-slate-400 font-bold">${inv.date}</span></div><div class="flex justify-between items-end"><div><span class="text-[9px] block text-slate-400 font-bold uppercase">Khách Hàng</span><span class="text-xs font-extrabold text-slate-800">${inv.customerName}</span></div><div class="text-right"><span class="text-[9px] block text-slate-400 font-bold uppercase">Thanh Toán</span><span class="text-sm font-black text-slate-900">${formatVND(inv.totalAmount)}</span></div></div></div>`;
            });
        }
        function renderCustomerTable() {
            const container = document.getElementById("customer-cards-container"); container.innerHTML = "";
            customers.forEach(c => {
                container.innerHTML += `<div class="bg-white p-3.5 rounded-2xl border border-slate-100 shadow-sm space-y-2"><div class="flex justify-between items-start"><div class="space-y-1"><span class="text-[9px] text-slate-400 font-bold block">${c.code}</span><h4 class="font-extrabold text-sm text-slate-800">${c.name}</h4><span class="text-[11px] font-bold text-slate-500 block">SĐT: ${c.phone}</span></div><div class="text-right"><span class="text-[9px] text-slate-400 font-bold block uppercase">Chi tiêu lũy kế</span><span class="text-xs font-extrabold text-emerald-600">${formatVND(c.totalSpent)}</span></div></div></div>`;
            });
        }
        function openAddCustomerModal() { document.getElementById("modal-customer").classList.remove("hidden"); }
        function closeCustomerModal() { document.getElementById("modal-customer").classList.add("hidden"); }
        function saveCustomer(e) { e.preventDefault(); closeCustomerModal(); showToast("Đã lưu khách hàng"); }
        
        function renderStaffTable() {
            const container = document.getElementById("staff-cards-container"); container.innerHTML = "";
            staffs.forEach(s => { container.innerHTML += `<div class="bg-white p-3.5 rounded-2xl border border-slate-100 shadow-sm space-y-2"><div class="flex justify-between items-start"><div><span class="text-[9px] text-slate-400 font-bold block">${s.code}</span><h4 class="font-extrabold text-sm text-slate-800">${s.name}</h4></div><span class="px-2.5 py-0.5 rounded-lg text-[10px] font-bold ${s.role === "Admin" ? "bg-amber-100 text-amber-700" : "bg-blue-100 text-blue-700"}">${s.role}</span></div></div>`; });
        }
        function openStaffModal() { document.getElementById("modal-staff").classList.remove("hidden"); }
        function closeStaffModal() { document.getElementById("modal-staff").classList.add("hidden"); }
        function saveStaff(e) { e.preventDefault(); closeStaffModal(); showToast("Đã lưu nhân viên"); }

        function setReportPeriod(period) { reportPeriod = period; renderReportDashboard(); }
        function renderReportDashboard() {
            // Simplified reporting demo for 2026
            let totalRev = invoices.reduce((a,b)=>a+b.totalAmount,0); let totalPro = invoices.reduce((a,b)=>a+b.profit,0);
            document.getElementById("stat-revenue").innerText = formatVND(totalRev); document.getElementById("stat-profit").innerText = formatVND(totalPro);
            document.getElementById("stat-orders").innerText = invoices.length; document.getElementById("stat-qty-sold").innerText = invoices.length;
            
            if (revenueChartObj) revenueChartObj.destroy();
            const ctx = document.getElementById('revenueChart').getContext('2d');
            revenueChartObj = new Chart(ctx, { type: 'line', data: { labels: ['08h', '12h', '16h', '20h'], datasets: [{ label: 'Doanh Thu', data: [0, totalRev/2, totalRev, totalRev], borderColor: '#10b981', fill: true, backgroundColor: 'rgba(16, 185, 129, 0.1)', tension: 0.4 }] }, options: { responsive: true, maintainAspectRatio: false, plugins: { legend: { display: false } }, scales: { x: { grid: { display: false } } } } });
        }
    </script>
</body>
</html>

