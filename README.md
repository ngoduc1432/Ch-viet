```html
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no, viewport-fit=cover">
    <title>KiotPro - Hệ thống Quản Lý Đa Nền Tảng</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Chart.js CDN -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@300;400;500;600;700;800&display=swap');
        
        body {
            font-family: 'Plus Jakarta Sans', sans-serif;
            -webkit-tap-highlight-color: transparent;
            background-color: #f8fafc;
        }

        /* Ẩn scrollbar nhưng vẫn cuộn tốt */
        .no-scrollbar::-webkit-scrollbar { display: none; }
        .no-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }
        
        /* Đảm bảo scroll mượt mà trên iOS */
        .scroll-smooth-touch {
            -webkit-overflow-scrolling: touch;
        }

        /* Responsive Safe Area Padding dưới cho iPhone đời mới */
        .pb-safe {
            padding-bottom: env(safe-area-inset-bottom, 0px);
        }

        /* Hiệu ứng kính mờ cao cấp */
        .glass {
            background: rgba(255, 255, 255, 0.85);
            backdrop-filter: blur(16px);
            -webkit-backdrop-filter: blur(16px);
            border: 1px solid rgba(255, 255, 255, 0.4);
        }
        
        .glass-dark {
            background: rgba(15, 23, 42, 0.85);
            backdrop-filter: blur(20px);
            -webkit-backdrop-filter: blur(20px);
            border: 1px solid rgba(255, 255, 255, 0.1);
        }

        /* Animation chuyển động */
        .slide-up {
            animation: slideUp 0.3s cubic-bezier(0.16, 1, 0.3, 1) forwards;
        }
        @keyframes slideUp {
            from { transform: translateY(100%); }
            to { transform: translateY(0); }
        }
        
        .chat-pop {
            animation: chatPop 0.3s cubic-bezier(0.34, 1.56, 0.64, 1) forwards;
            transform-origin: bottom right;
        }
        @keyframes chatPop {
            from { opacity: 0; transform: scale(0.8) translateY(20px); }
            to { opacity: 1; transform: scale(1) translateY(0); }
        }

        /* Hiệu ứng gõ chữ của Bot */
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

        /* Card tương tác */
        .card-hover {
            transition: all 0.25s cubic-bezier(0.4, 0, 0.2, 1);
        }
        .card-hover:hover {
            transform: translateY(-2px);
            box-shadow: 0 12px 20px -8px rgba(16, 185, 129, 0.15);
        }
    </style>
</head>
<body class="text-slate-800 h-screen overflow-hidden flex flex-col md:flex-row">

    <!-- ==================== MÀN HÌNH ĐĂNG NHẬP (LOGIN) ==================== -->
    <div id="login-screen" class="fixed inset-0 bg-gradient-to-br from-slate-900 via-emerald-950 to-teal-950 z-[100] flex items-center justify-center p-4">
        <div class="bg-white/95 backdrop-blur-md w-full max-w-sm rounded-[2.5rem] shadow-2xl p-8 space-y-6 border border-white/20">
            <div class="text-center space-y-2">
                <div class="inline-block bg-emerald-50 text-emerald-600 px-5 py-2 rounded-2xl font-black text-3xl tracking-wider shadow-inner">
                    Kiot<span class="text-amber-500">Pro</span>
                </div>
                <h2 class="text-lg font-bold text-slate-800">Hệ Thống Quản Lý POS</h2>
                <p class="text-xs text-slate-400">Ứng dụng đa nền tảng tối ưu di động & PC</p>
            </div>

            <form id="login-form" onsubmit="handleLogin(event)" class="space-y-4">
                <div>
                    <label class="block text-xs font-bold text-slate-500 uppercase tracking-wider mb-1">Tài khoản</label>
                    <input type="text" id="login-username" required placeholder="admin hoặc nhanvien" class="w-full px-4 py-3 border border-slate-200 rounded-2xl focus:outline-none focus:ring-2 focus:ring-emerald-500 text-sm">
                </div>
                <div>
                    <label class="block text-xs font-bold text-slate-500 uppercase tracking-wider mb-1">Mật khẩu</label>
                    <input type="password" id="login-password" required placeholder="••••••••" class="w-full px-4 py-3 border border-slate-200 rounded-2xl focus:outline-none focus:ring-2 focus:ring-emerald-500 text-sm">
                </div>
                <button type="submit" class="w-full bg-emerald-600 hover:bg-emerald-700 active:scale-[0.98] text-white py-3.5 rounded-2xl font-bold text-sm shadow-lg transition-all flex justify-center items-center">
                    ĐĂNG NHẬP
                </button>
            </form>

            <div class="border-t border-slate-100 pt-4 text-center">
                <p class="text-[10px] text-slate-400 mb-1">Tài khoản dùng thử:</p>
                <div class="flex justify-center space-x-4 text-[11px] font-bold text-slate-500">
                    <p>Admin: <span class="text-emerald-600">admin / 123</span></p>
                    <p>Nhân viên: <span class="text-emerald-600">nhanvien / 123</span></p>
                </div>
            </div>
        </div>
    </div>

    <!-- ==================== SIDEBAR TRÊN MÁY TÍNH (DESKTOP SIDEBAR) ==================== -->
    <aside id="desktop-sidebar" class="hidden md:flex flex-col w-[260px] bg-slate-900 text-white h-screen shrink-0 relative z-40 shadow-2xl">
        <div class="p-6 flex items-center space-x-3 border-b border-slate-800">
            <div class="bg-gradient-to-br from-emerald-400 to-teal-500 text-slate-900 px-3.5 py-1.5 rounded-xl font-black text-xl tracking-wide shadow-lg">
                KiotPro
            </div>
        </div>
        <div class="p-4 border-b border-slate-800">
            <div class="bg-slate-800/60 p-3 rounded-2xl border border-slate-700/50 flex items-center space-x-3">
                <div class="w-9 h-9 rounded-full bg-emerald-500/20 text-emerald-400 flex items-center justify-center font-bold text-base">
                    👤
                </div>
                <div class="flex flex-col min-w-0">
                    <span class="text-xs font-bold text-white truncate" id="sidebar-user-name">Đang tải...</span>
                    <span class="text-[10px] text-emerald-400 font-medium" id="sidebar-user-role">Đang tải...</span>
                </div>
            </div>
        </div>
        <nav class="flex-1 overflow-y-auto py-4 space-y-1 px-3" id="desktop-navigation-container"></nav>
        <div class="p-4 border-t border-slate-800">
            <button onclick="handleLogout()" class="flex items-center space-x-3 text-slate-400 hover:text-red-400 hover:bg-red-500/10 transition-colors w-full p-2.5 rounded-xl">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 16l4-4m0 0l-4-4m4 4H7m6 4v1a3 3 0 01-3 3H6a3 3 0 01-3-3V7a3 3 0 013-3h4a3 3 0 013 3v1" /></svg>
                <span class="font-bold text-sm">Đăng xuất</span>
            </button>
        </div>
    </aside>

    <!-- ==================== KHU VỰC HIỂN THỊ CHÍNH ==================== -->
    <div class="flex-1 flex flex-col h-screen relative min-w-0">
        
        <!-- HEADER TRÊN DI ĐỘNG & THANH TRÊN MÁY TÍNH -->
        <header class="bg-gradient-to-r from-emerald-600 to-teal-700 text-white md:bg-white md:text-slate-800 px-4 py-3 md:px-8 md:py-4 flex justify-between items-center shadow-md md:shadow-sm md:border-b md:border-slate-100 sticky top-0 z-30 shrink-0">
            <div class="flex md:hidden items-center space-x-2">
                <div class="bg-white text-emerald-600 px-2.5 py-1 rounded-xl font-black text-sm tracking-wider shadow-inner">
                    Kiot<span class="text-amber-500">Pro</span>
                </div>
                <div class="flex flex-col">
                    <span class="text-[10px] font-bold leading-none" id="header-user-name">Đang tải...</span>
                    <span class="text-[8px] bg-emerald-800 text-emerald-100 px-1.5 py-0.2 rounded-full font-medium inline-block mt-0.5" id="header-user-role">Đang tải...</span>
                </div>
            </div>
            
            <div class="hidden md:block">
                <h1 class="text-xl font-extrabold text-slate-850 tracking-tight" id="desktop-header-title">Bán hàng (POS)</h1>
            </div>

            <div class="flex items-center space-x-2">
                <span class="text-[9px] md:text-xs bg-white/20 md:bg-emerald-50 md:text-emerald-700 text-white md:border md:border-emerald-100 px-2.5 py-1 rounded-full font-bold">Trung tâm Quận 1</span>
                <button onclick="handleLogout()" class="md:hidden bg-white/10 p-2 rounded-xl text-xs transition-colors">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2.5" d="M17 16l4-4m0 0l-4-4m4 4H7m6 4v1a3 3 0 01-3 3H6a3 3 0 01-3-3V7a3 3 0 013-3h4a3 3 0 013 3v1" /></svg>
                </button>
            </div>
        </header>

        <!-- CONTAINER CUỘN NỘI DUNG (MAIN CONTENT CONTAINER) -->
        <!-- pb-32 trên di động để không bị che bởi Bottom Nav và Giỏ Hàng nổi -->
        <main class="flex-1 overflow-y-auto px-4 py-4 md:p-8 space-y-4 w-full max-w-7xl mx-auto pb-32 md:pb-8 scroll-smooth-touch">

            <!-- TAB 1: BÁN HÀNG (POS) -->
            <section id="section-pos" class="tab-content block space-y-3">
                <div class="bg-white p-3 rounded-2xl shadow-sm border border-slate-100 space-y-2.5">
                    <div class="relative">
                        <span class="absolute inset-y-0 left-0 flex items-center pl-3.5 text-slate-400">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" viewBox="0 0 20 20" fill="currentColor"><path fill-rule="evenodd" d="M8 4a4 4 0 100 8 4 4 0 000-8zM2 8a6 6 0 1110.89 3.476l4.817 4.817a1 1 0 01-1.414 1.414l-4.816-4.816A6 6 0 012 8z" clip-rule="evenodd" /></svg>
                        </span>
                        <input type="text" id="pos-search" oninput="renderPosProducts()" placeholder="Gõ tìm sản phẩm hoặc gọi trợ lý bằng giọng nói..." class="w-full pl-10 pr-4 py-2.5 border border-slate-200 rounded-xl focus:outline-none focus:ring-2 focus:ring-emerald-500 text-sm font-medium">
                    </div>
                    <div class="flex space-x-2 overflow-x-auto no-scrollbar py-0.5" id="pos-category-filters"></div>
                </div>

                <!-- Lưới hiển thị sản phẩm trực quan -->
                <div class="grid grid-cols-2 sm:grid-cols-3 lg:grid-cols-4 xl:grid-cols-5 gap-3" id="pos-products-grid"></div>
            </section>

            <!-- TAB 2: KHO HÀNG (PRODUCTS) -->
            <section id="section-products" class="tab-content hidden space-y-4">
                <div class="flex justify-between items-center">
                    <div>
                        <h2 class="text-lg font-extrabold text-slate-800">Quản lý kho hàng</h2>
                        <p class="text-xs text-slate-400">Định mức an toàn kho & bổ sung tồn nhanh</p>
                    </div>
                    <div class="flex space-x-1.5 shrink-0" id="product-action-buttons">
                        <button onclick="openImportStockModal()" class="bg-indigo-600 hover:bg-indigo-700 text-white font-bold p-2.5 rounded-xl shadow-md text-xs flex items-center space-x-1">
                            <svg class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-8l-4-4m0 0L8 8m4-4v12" /></svg>
                            <span>Nhập kho</span>
                        </button>
                        <button onclick="openProductModal(false)" class="bg-emerald-600 hover:bg-emerald-700 text-white font-bold p-2.5 rounded-xl shadow-md text-xs flex items-center space-x-1">
                            <svg class="h-4 w-4" viewBox="0 0 20 20" fill="currentColor"><path fill-rule="evenodd" d="M10 5a1 1 0 011 1v3h3a1 1 0 110 2h-3v3a1 1 0 11-2 0v-3H6a1 1 0 110-2h3V6a1 1 0 011-1z" clip-rule="evenodd" /></svg>
                            <span>Thêm SP</span>
                        </button>
                    </div>
                </div>

                <div class="bg-white p-3 rounded-2xl shadow-sm border border-slate-100 flex flex-col md:flex-row gap-2">
                    <input type="text" id="product-list-search" oninput="renderProductTable()" placeholder="Lọc theo tên, mã sản phẩm..." class="flex-1 px-3 py-2 border border-slate-200 rounded-xl text-sm focus:outline-none focus:ring-2 focus:ring-emerald-500">
                    <div class="grid grid-cols-2 gap-2">
                        <select id="product-list-category" onchange="renderProductTable()" class="py-2 px-3 border border-slate-200 rounded-xl text-xs bg-white">
                            <option value="all">Tất cả hãng</option>
                            <option value="iPhone">iPhone</option>
                            <option value="Samsung">Samsung</option>
                            <option value="Oppo">Oppo</option>
                            <option value="Xiaomi">Xiaomi</option>
                        </select>
                        <select id="product-list-status" onchange="renderProductTable()" class="py-2 px-3 border border-slate-200 rounded-xl text-xs bg-white">
                            <option value="all">Tất cả trạng thái</option>
                            <option value="low-stock">Sắp hết hàng</option>
                            <option value="out-of-stock">Hết hàng (Tồn = 0)</option>
                        </select>
                    </div>
                </div>

                <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-3" id="product-cards-container"></div>
            </section>

            <!-- TAB 3: HOÁ ĐƠN (INVOICES) -->
            <section id="section-invoices" class="tab-content hidden space-y-3">
                <div>
                    <h2 class="text-lg font-extrabold text-slate-800">Nhật ký bán hàng</h2>
                    <p class="text-xs text-slate-400">Theo dõi doanh thu, in lại biên lai bán lẻ</p>
                </div>
                <div class="bg-white p-3 rounded-2xl shadow-sm border border-slate-100 max-w-md">
                    <input type="text" id="invoice-search" oninput="renderInvoiceTable()" placeholder="Gõ mã đơn, tên khách..." class="w-full px-3 py-2 border border-slate-200 rounded-xl text-sm focus:outline-none">
                </div>
                <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-3" id="invoice-cards-container"></div>
            </section>

            <!-- TAB 4: KHÁCH HÀNG (CUSTOMERS) -->
            <section id="section-customers" class="tab-content hidden space-y-3">
                <div class="flex justify-between items-center">
                    <div>
                        <h2 class="text-lg font-extrabold text-slate-800">Khách hàng</h2>
                        <p class="text-xs text-slate-400">Doanh số mua hàng tích lũy</p>
                    </div>
                    <button onclick="openAddCustomerModal(false)" class="bg-emerald-600 hover:bg-emerald-700 text-white font-bold p-2.5 rounded-xl shadow-md text-xs">Thêm khách</button>
                </div>
                <div class="bg-white p-3 rounded-2xl shadow-sm border border-slate-100 max-w-md">
                    <input type="text" id="customer-search" oninput="renderCustomerTable()" placeholder="Tìm số điện thoại, tên..." class="w-full px-3 py-2 border border-slate-200 rounded-xl text-sm">
                </div>
                <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-3" id="customer-cards-container"></div>
            </section>

            <!-- TAB 5: NHÂN VIÊN (STAFFS) -->
            <section id="section-staffs" class="tab-content hidden space-y-3">
                <div class="flex justify-between items-center">
                    <div>
                        <h2 class="text-lg font-extrabold text-slate-800">Danh sách nhân sự</h2>
                        <p class="text-xs text-slate-400">Quản lý và cấp quyền truy cập hệ thống</p>
                    </div>
                    <button onclick="openStaffModal(false)" class="bg-emerald-600 hover:bg-emerald-700 text-white font-bold p-2.5 rounded-xl text-xs">Thêm nhân viên</button>
                </div>
                <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-3" id="staff-cards-container"></div>
            </section>

            <!-- TAB 6: BÁO CÁO KINH DOANH -->
            <section id="section-reports" class="tab-content hidden space-y-4">
                <div>
                    <h2 class="text-lg font-extrabold text-slate-800">Phân tích kinh doanh</h2>
                    <p class="text-xs text-slate-400">Xem doanh số thực bán & lợi nhuận gộp</p>
                </div>

                <div class="bg-slate-200/70 p-1 rounded-2xl grid grid-cols-3 gap-1 text-center shrink-0 max-w-sm">
                    <button onclick="setReportPeriod('day')" id="btn-period-day" class="py-2 text-xs font-bold rounded-xl transition-all">Hôm nay</button>
                    <button onclick="setReportPeriod('month')" id="btn-period-month" class="py-2 text-xs font-bold rounded-xl transition-all">Tháng này</button>
                    <button onclick="setReportPeriod('year')" id="btn-period-year" class="py-2 text-xs font-bold rounded-xl transition-all">Năm nay</button>
                </div>

                <div class="grid grid-cols-2 lg:grid-cols-4 gap-3">
                    <div class="bg-white p-4 rounded-2xl border border-slate-100 shadow-sm">
                        <span class="text-[9px] text-slate-400 font-bold block uppercase tracking-wider">DOANH THU THỰC</span>
                        <span class="text-base font-extrabold text-slate-850" id="stat-revenue">0 ₫</span>
                    </div>
                    <div class="bg-white p-4 rounded-2xl border border-slate-100 shadow-sm">
                        <span class="text-[9px] text-slate-400 font-bold block uppercase tracking-wider">LỢI NHUẬN GỘP</span>
                        <span class="text-base font-extrabold text-emerald-600" id="stat-profit">0 ₫</span>
                    </div>
                    <div class="bg-white p-4 rounded-2xl border border-slate-100 shadow-sm">
                        <span class="text-[9px] text-slate-400 font-bold block uppercase tracking-wider">TỔNG SỐ ĐƠN</span>
                        <span class="text-base font-extrabold text-slate-800" id="stat-orders">0 Đơn</span>
                    </div>
                    <div class="bg-white p-4 rounded-2xl border border-slate-100 shadow-sm">
                        <span class="text-[9px] text-slate-400 font-bold block uppercase tracking-wider">MÁY ĐÃ BÁN</span>
                        <span class="text-base font-extrabold text-blue-600" id="stat-qty-sold">0 Máy</span>
                    </div>
                </div>

                <div class="grid grid-cols-1 lg:grid-cols-3 gap-4">
                    <div class="lg:col-span-2 bg-white p-4 rounded-2xl border border-slate-150 shadow-sm">
                        <h3 class="text-xs font-bold text-slate-700 uppercase tracking-wide mb-3">Xu hướng tăng trưởng</h3>
                        <div class="h-60 relative">
                            <canvas id="revenueChart"></canvas>
                        </div>
                    </div>
                    <div class="bg-white p-4 rounded-2xl border border-slate-150 shadow-sm space-y-3">
                        <h3 class="text-xs font-bold text-slate-700 uppercase tracking-wide border-b border-slate-50 pb-2">Top sản phẩm bán chạy</h3>
                        <div id="top-products-progress-container" class="space-y-3.5"></div>
                    </div>
                </div>
            </section>
        </main>
    </div>

    <!-- ==================== GIỎ HÀNG NỔI (CHỈ TRÊN DI ĐỘNG) ==================== -->
    <div id="floating-cart-bar" class="md:hidden fixed bottom-16 left-0 right-0 px-4 py-2 z-40 hidden pb-safe">
        <button onclick="toggleCartSheet(true)" class="w-full bg-slate-900 text-white py-3.5 px-4 rounded-2xl shadow-2xl flex justify-between items-center glass-dark active:scale-[0.98] transition-transform">
            <div class="flex items-center space-x-2">
                <div class="bg-amber-500 text-slate-950 w-6 h-6 rounded-full flex items-center justify-center text-xs font-black shadow-lg" id="cart-floating-count">0</div>
                <span class="text-xs font-extrabold tracking-wide">Xem giỏ thanh toán</span>
            </div>
            <div class="flex items-center space-x-1 font-extrabold text-sm text-emerald-400">
                <span id="cart-floating-total">0 ₫</span>
                <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2.5" d="M9 5l7 7-7 7" /></svg>
            </div>
        </button>
    </div>
    
    <!-- GIỎ HÀNG NỔI TRÊN MÁY TÍNH (DESKTOP CART) -->
    <button id="desktop-cart-btn" onclick="toggleCartSheet(true)" class="hidden md:flex fixed bottom-6 right-6 z-40 bg-slate-950 hover:bg-slate-800 border border-slate-800 text-white p-4 rounded-2xl shadow-xl items-center justify-center space-x-3 transition-transform hover:scale-105">
        <div class="relative">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-emerald-400" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 3h2l.4 2M7 13h10l4-8H5.4M7 13L5.4 5M7 13l-2.293 2.293c-.63.63-.184 1.707.707 1.707H17m0 0a2 2 0 100 4 2 2 0 000-4zm-8 2a2 2 0 11-4 0 2 2 0 014 0z" /></svg>
            <span class="absolute -top-3.5 -right-3.5 bg-emerald-500 text-white font-extrabold px-1.5 py-0.2 text-[9px] rounded-full" id="desktop-cart-count">0</span>
        </div>
        <span class="text-xs font-bold">Giỏ hàng POS</span>
    </button>

    <!-- ==================== THANH MENU DI ĐỘNG (MOBILE BOTTOM NAV) ==================== -->
    <nav class="md:hidden glass fixed bottom-0 left-0 right-0 border-t border-slate-200/60 flex justify-around items-center z-40 pb-safe shadow-[0_-4px_12px_rgba(0,0,0,0.03)]" id="bottom-navigation-bar">
        <!-- Đổ tự động qua JS -->
    </nav>

    <!-- ==================== DIỆN DIỆN GIỎ HÀNG (CART SHEET / SIDEBAR) ==================== -->
    <div id="cart-bottom-sheet" class="fixed inset-0 bg-slate-950/60 backdrop-blur-sm z-50 hidden flex flex-col justify-end md:flex-row">
        <div class="flex-1" onclick="toggleCartSheet(false)"></div>
        <div class="bg-white md:w-[420px] w-full max-h-[85vh] md:max-h-full rounded-t-[2rem] md:rounded-none flex flex-col shadow-2xl slide-up md:animate-none md:h-screen">
            <div class="w-12 h-1 bg-slate-200 rounded-full mx-auto my-3 shrink-0 md:hidden"></div>
            
            <div class="px-5 py-4 border-b border-slate-100 flex justify-between items-center bg-slate-50 shrink-0">
                <h3 class="font-extrabold text-sm text-slate-800">Chi tiết giỏ hàng (<span id="pos-cart-count">0</span>)</h3>
                <button onclick="clearCart()" class="text-xs font-extrabold text-red-500 hover:bg-red-50 px-2 py-1 rounded-lg">Xoá giỏ</button>
            </div>

            <div class="flex-1 overflow-y-auto px-5 py-2 divide-y divide-slate-100" id="pos-cart-container"></div>

            <div class="bg-slate-50 p-5 border-t border-slate-200 space-y-3 shrink-0 text-xs shadow-[0_-4px_12px_rgba(0,0,0,0.03)]">
                <div>
                    <label class="block text-[10px] font-bold text-slate-400 uppercase tracking-wider mb-1">Khách hàng áp dụng</label>
                    <div class="flex items-center space-x-1.5">
                        <select id="pos-customer-select" class="flex-1 py-2 px-3 rounded-xl border border-slate-200 bg-white font-bold text-slate-700 outline-none"></select>
                        <button onclick="toggleCartSheet(false); openAddCustomerModal(true);" class="bg-emerald-50 text-emerald-600 p-2.5 rounded-xl border border-emerald-100">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-4.5 w-4.5" viewBox="0 0 20 20" fill="currentColor"><path d="M8 9a3 3 0 100-6 3 3 0 000 6zM8 11a6 6 0 016 6H2a6 6 0 016-6zM16 7a1 1 0 10-2 0v1h-1a1 1 0 100 2h1v1a1 1 0 102 0v-1h1a1 1 0 100-2h-1V7z" /></svg>
                        </button>
                    </div>
                </div>

                <div class="grid grid-cols-2 gap-3">
                    <div>
                        <label class="block text-[10px] font-bold text-slate-400 uppercase tracking-wider mb-1">Chiết khấu (%)</label>
                        <input type="number" id="pos-discount" min="0" max="100" value="0" oninput="calcCartTotals()" class="w-full border border-slate-200 rounded-xl py-2 px-3 font-bold text-slate-800 text-center">
                    </div>
                    <div>
                        <label class="block text-[10px] font-bold text-slate-400 uppercase tracking-wider mb-1">Thanh toán</label>
                        <select id="pos-payment" class="w-full border border-slate-200 rounded-xl py-2 px-3 bg-white font-bold text-slate-800">
                            <option value="Tiền mặt">Tiền mặt</option>
                            <option value="Chuyển khoản">Chuyển khoản</option>
                            <option value="Quẹt thẻ">Quẹt thẻ</option>
                        </select>
                    </div>
                </div>

                <div class="flex justify-between items-center pt-3 border-t border-dashed border-slate-200">
                    <div>
                        <span class="text-[10px] text-slate-400 font-bold block">Tổng tiền gốc</span>
                        <span id="pos-subtotal" class="text-xs text-slate-400 line-through">0 ₫</span>
                    </div>
                    <span id="pos-total-amount" class="text-lg font-black text-emerald-600">0 ₫</span>
                </div>

                <button onclick="checkoutCart()" class="w-full bg-emerald-600 hover:bg-emerald-700 active:scale-[0.99] text-white font-bold py-3 px-4 rounded-xl shadow-lg transition-transform flex justify-center items-center space-x-2">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-4.5 w-4.5" viewBox="0 0 20 20" fill="currentColor"><path fill-rule="evenodd" d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z" clip-rule="evenodd" /></svg>
                    <span>Hoàn tất & Xuất hoá đơn</span>
                </button>
            </div>
        </div>
    </div>

    <!-- ==================== TRỢ LÝ KIOTBOT AI (ADVANCED NLP + VOICE CONTROL) ==================== -->
    <div id="chatbot-wrapper" class="fixed bottom-16 md:bottom-6 right-4 md:right-[150px] z-50 flex flex-col items-end pointer-events-none pb-safe">
        
        <!-- Cửa sổ Chat -->
        <div id="chatbot-panel" class="bg-white w-[calc(100vw-2rem)] sm:w-[360px] md:w-[400px] rounded-[2rem] shadow-[0_12px_40px_rgba(0,0,0,0.18)] border border-slate-150 mb-3 overflow-hidden flex flex-col pointer-events-auto hidden chat-pop">
            
            <!-- Đầu Chatbox -->
            <div class="bg-slate-900 p-4 flex justify-between items-center relative shrink-0">
                <div class="flex items-center space-x-3 relative z-10">
                    <div class="w-9 h-9 bg-gradient-to-br from-emerald-400 to-teal-400 rounded-xl flex items-center justify-center p-0.5 relative shadow-md">
                        <span class="text-xl">🤖</span>
                        <span class="absolute bottom-0 right-0 w-2.5 h-2.5 bg-green-500 border-2 border-slate-900 rounded-full"></span>
                    </div>
                    <div>
                        <h4 class="text-white font-extrabold text-sm leading-tight tracking-wide">KiotBot Pro AI</h4>
                        <span class="text-emerald-400 text-[10px] font-semibold flex items-center">
                            <span class="w-1.5 h-1.5 bg-emerald-400 rounded-full mr-1 animate-pulse"></span>
                            Điều khiển giọng nói & lệnh hành động
                        </span>
                    </div>
                </div>
                <button onclick="toggleChatbot()" class="text-slate-400 hover:text-white bg-white/10 p-1.5 rounded-full transition-colors"><svg class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" /></svg></button>
            </div>
            
            <!-- Khu vực tin nhắn -->
            <div id="chatbot-messages" class="h-64 bg-slate-50 p-4 overflow-y-auto flex flex-col space-y-3.5 text-xs">
                <div class="flex justify-start">
                    <div class="bg-white border border-slate-200 text-slate-700 py-3 px-4 rounded-2xl rounded-tl-none max-w-[85%] shadow-sm leading-relaxed">
                        Xin chào! Tôi có thể **thực thi lệnh trực tiếp**. Hãy thử nói hoặc gõ:<br>
                        - <strong class="text-emerald-600 font-bold">"Mở báo cáo"</strong> (hoặc mở kho, mở POS)<br>
                        - <strong class="text-emerald-600 font-bold">"Tìm điện thoại iPhone"</strong><br>
                        - <strong class="text-emerald-600 font-bold">"Mua máy Samsung"</strong> (tự thêm vào giỏ hàng)<br>
                        - <strong class="text-emerald-600 font-bold">"Kiểm tra hàng sắp hết"</strong>
                    </div>
                </div>
            </div>
            
            <!-- Khu vực nhập liệu -->
            <div class="p-3 bg-white border-t border-slate-100 shrink-0">
                <form onsubmit="handleChatbotSubmit(event)" class="flex items-center space-x-1.5 bg-slate-100 border border-slate-200 rounded-xl p-1 focus-within:border-emerald-500 focus-within:bg-white transition-all">
                    <!-- Nút Micro giọng nói -->
                    <button type="button" id="btn-voice-chat" onclick="startVoiceRecognition()" title="Lệnh giọng nói" class="p-2 text-slate-400 hover:text-emerald-600 rounded-lg transition-all focus:outline-none relative">
                        <svg class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2.5" d="M19 11a7 7 0 01-7 7m0 0a7 7 0 01-7-7m7 7v4m0 0H8m4 0h4m-4-8a3 3 0 01-3-3V5a3 3 0 116 0v6a3 3 0 01-3 3z" /></svg>
                        <span id="voice-indicator" class="absolute top-1.5 right-1.5 w-2 h-2 bg-red-500 rounded-full animate-ping hidden"></span>
                    </button>
                    
                    <input type="text" id="chatbot-input" placeholder="Hỏi hoặc nói lệnh tại đây..." class="flex-1 bg-transparent border-none px-2 py-1.5 text-xs focus:outline-none focus:ring-0 text-slate-800">
                    
                    <button type="submit" class="bg-slate-900 text-white w-8 h-8 rounded-lg flex items-center justify-center hover:bg-emerald-600 shadow transition-transform active:scale-95">
                        <svg class="h-4.5 w-4.5 transform rotate-90" viewBox="0 0 20 20" fill="currentColor"><path d="M10.894 2.553a1 1 0 00-1.788 0l-7 14a1 1 0 001.169 1.409l5-1.429A1 1 0 009 15.571V11a1 1 0 112 0v4.571a1 1 0 00.725.962l5 1.428a1 1 0 001.17-1.408l-7-14z" /></svg>
                    </button>
                </form>
            </div>
        </div>

        <!-- Nút kích hoạt Bot nổi -->
        <button onclick="toggleChatbot()" class="pointer-events-auto bg-slate-900 text-white p-3.5 rounded-2xl shadow-[0_8px_30px_rgba(0,0,0,0.2)] flex items-center justify-center border border-slate-800 transition-transform active:scale-90">
            <span class="text-xl">🤖</span>
            <span class="absolute -top-1 -right-1 flex h-3.5 w-3.5"><span class="animate-ping absolute inline-flex h-full w-full rounded-full bg-emerald-400 opacity-75"></span><span class="relative inline-flex rounded-full h-3.5 w-3.5 bg-emerald-500 border border-slate-900"></span></span>
        </button>
    </div>

    <!-- ==================== MODAL 1: THÊM/SỬA SẢN PHẨM ==================== -->
    <div id="modal-product" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-[60] flex items-end md:items-center justify-center hidden p-4">
        <div class="bg-white w-full max-w-md rounded-t-[2rem] md:rounded-2xl shadow-xl overflow-hidden flex flex-col max-h-[90vh] slide-up md:animate-none">
            <div class="bg-emerald-600 text-white px-5 py-4 flex justify-between items-center shrink-0">
                <h3 class="font-bold text-sm" id="product-modal-title">Thêm Sản Phẩm Mới</h3>
                <button onclick="closeProductModal()" class="text-white hover:text-slate-200"><svg class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" /></svg></button>
            </div>
            
            <form id="product-form" onsubmit="saveProduct(event)" class="p-5 space-y-3.5 overflow-y-auto">
                <input type="hidden" id="edit-product-id">
                
                <div class="grid grid-cols-2 gap-3">
                    <div>
                        <label class="block text-[10px] font-bold text-slate-500 mb-1 uppercase">Mã sản phẩm *</label>
                        <input type="text" id="prod-code" required class="w-full p-2.5 bg-slate-50 border border-slate-200 rounded-xl text-xs focus:outline-none">
                    </div>
                    <div>
                        <label class="block text-[10px] font-bold text-slate-500 mb-1 uppercase">Hãng / Danh mục *</label>
                        <select id="prod-category" required class="w-full p-2.5 bg-slate-50 border border-slate-200 rounded-xl text-xs">
                            <option value="iPhone">iPhone (Apple)</option>
                            <option value="Samsung">Samsung</option>
                            <option value="Oppo">Oppo</option>
                            <option value="Xiaomi">Xiaomi</option>
                            <option value="Phụ kiện">Phụ kiện</option>
                        </select>
                    </div>
                </div>

                <div>
                    <label class="block text-[10px] font-bold text-slate-500 mb-1 uppercase">Tên thiết bị *</label>
                    <input type="text" id="prod-name" required placeholder="Ví dụ: iPhone 15 Pro Max 256GB" class="w-full p-2.5 bg-slate-50 border border-slate-200 rounded-xl text-xs focus:outline-none">
                </div>

                <div class="grid grid-cols-2 gap-3">
                    <div>
                        <label class="block text-[10px] font-bold text-slate-500 mb-1 uppercase">Giá Vốn (Giá nhập) *</label>
                        <input type="number" id="prod-cost" required min="0" class="w-full p-2.5 bg-slate-50 border border-slate-200 rounded-xl text-xs focus:outline-none">
                    </div>
                    <div>
                        <label class="block text-[10px] font-bold text-slate-500 mb-1 uppercase">Giá Bán Lẻ *</label>
                        <input type="number" id="prod-selling" required min="0" class="w-full p-2.5 bg-slate-50 border border-slate-200 rounded-xl text-xs focus:outline-none">
                    </div>
                </div>

                <div class="grid grid-cols-2 gap-3">
                    <div>
                        <label class="block text-[10px] font-bold text-slate-500 mb-1 uppercase">Tồn kho ban đầu *</label>
                        <input type="number" id="prod-stock" required min="0" class="w-full p-2.5 bg-slate-50 border border-slate-200 rounded-xl text-xs focus:outline-none">
                    </div>
                    <div>
                        <label class="block text-[10px] font-bold text-slate-500 mb-1 uppercase">Mức tồn tối thiểu cảnh báo *</label>
                        <input type="number" id="prod-minStock" required min="1" class="w-full p-2.5 bg-slate-50 border border-slate-200 rounded-xl text-xs focus:outline-none">
                    </div>
                </div>

                <div class="pt-2 flex space-x-2">
                    <button type="button" onclick="closeProductModal()" class="w-1/2 bg-slate-100 text-slate-700 py-3 rounded-xl font-bold text-xs">Hủy bỏ</button>
                    <button type="submit" class="w-1/2 bg-emerald-600 text-white py-3 rounded-xl font-bold text-xs shadow-md">Lưu sản phẩm</button>
                </div>
            </form>
        </div>
    </div>

    <!-- ==================== MODAL 2: THÊM KHÁCH HÀNG ==================== -->
    <div id="modal-customer" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-[60] flex items-end md:items-center justify-center hidden p-4">
        <div class="bg-white w-full max-w-md rounded-t-[2rem] md:rounded-2xl shadow-xl overflow-hidden slide-up md:animate-none">
            <div class="bg-emerald-600 text-white px-5 py-4 flex justify-between items-center">
                <h3 class="font-bold text-sm" id="customer-modal-title">Thêm Khách Hàng</h3>
                <button onclick="closeCustomerModal()" class="text-white"><svg class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" /></svg></button>
            </div>
            
            <form id="customer-form" onsubmit="saveCustomer(event)" class="p-5 space-y-4">
                <input type="hidden" id="edit-customer-id">
                <input type="hidden" id="is-pos-adding" value="false">

                <div class="grid grid-cols-2 gap-3">
                    <div>
                        <label class="block text-[10px] font-bold text-slate-500 mb-1 uppercase">Mã khách *</label>
                        <input type="text" id="cust-code" required class="w-full p-2.5 bg-slate-50 border border-slate-200 rounded-xl text-xs">
                    </div>
                    <div>
                        <label class="block text-[10px] font-bold text-slate-500 mb-1 uppercase">Số điện thoại *</label>
                        <input type="tel" id="cust-phone" required class="w-full p-2.5 bg-slate-50 border border-slate-200 rounded-xl text-xs">
                    </div>
                </div>

                <div>
                    <label class="block text-[10px] font-bold text-slate-500 mb-1 uppercase">Họ và Tên khách hàng *</label>
                    <input type="text" id="cust-name" required class="w-full p-2.5 bg-slate-50 border border-slate-200 rounded-xl text-xs">
                </div>

                <div>
                    <label class="block text-[10px] font-bold text-slate-500 mb-1 uppercase">Địa chỉ</label>
                    <input type="text" id="cust-address" class="w-full p-2.5 bg-slate-50 border border-slate-200 rounded-xl text-xs">
                </div>

                <div class="pt-2 flex space-x-2">
                    <button type="button" onclick="closeCustomerModal()" class="w-1/2 bg-slate-100 text-slate-700 py-3 rounded-xl font-bold text-xs">Hủy</button>
                    <button type="submit" class="w-1/2 bg-emerald-600 text-white py-3 rounded-xl font-bold text-xs shadow-md">Lưu thông tin</button>
                </div>
            </form>
        </div>
    </div>

    <!-- ==================== MODAL 3: NHẬP KHO HÀNG LOẠT (BULK IMPORT) ==================== -->
    <div id="modal-import-stock" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-[60] flex items-end md:items-center justify-center p-4 hidden">
        <div class="bg-white w-full max-w-2xl rounded-t-[2rem] md:rounded-2xl shadow-xl overflow-hidden flex flex-col max-h-[90vh] slide-up md:animate-none">
            <div class="bg-indigo-600 text-white px-5 py-4 flex justify-between items-center shrink-0">
                <h3 class="font-bold text-sm">Nhập bổ sung kho hàng loạt</h3>
                <button onclick="closeImportStockModal()" class="text-white"><svg class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" /></svg></button>
            </div>
            
            <div class="p-3 bg-slate-50 border-b border-slate-200 shrink-0">
                <div class="relative">
                    <span class="absolute inset-y-0 left-0 flex items-center pl-3.5 text-slate-400">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" viewBox="0 0 20 20" fill="currentColor"><path fill-rule="evenodd" d="M8 4a4 4 0 100 8 4 4 0 000-8zM2 8a6 6 0 1110.89 3.476l4.817 4.817a1 1 0 01-1.414 1.414l-4.816-4.816A6 6 0 012 8z" clip-rule="evenodd" /></svg>
                    </span>
                    <input type="text" id="import-search" oninput="searchImportProduct()" placeholder="Gõ tìm sản phẩm cần nhập thêm vào danh sách..." class="w-full pl-9 pr-3 py-2 bg-white border border-slate-300 rounded-xl focus:outline-none focus:ring-2 focus:ring-indigo-500 text-xs">
                    <!-- Kết quả tìm kiếm nhanh -->
                    <div id="import-search-results" class="absolute top-full left-0 right-0 mt-1 bg-white border border-slate-200 rounded-xl shadow-lg max-h-48 overflow-y-auto hidden z-10 no-scrollbar"></div>
                </div>
            </div>

            <div class="flex-1 overflow-y-auto p-4 space-y-3" id="import-list-container">
                <!-- Chứa danh sách các máy chuẩn bị nhập -->
            </div>

            <div class="p-4 border-t border-slate-200 bg-slate-50 flex justify-between items-center shrink-0">
                <div class="text-xs font-bold text-slate-600">Tổng ngân sách nhập: <span id="import-total-money" class="text-indigo-600 text-sm block md:inline font-black">0 ₫</span></div>
                <button onclick="processBulkImport()" class="bg-indigo-600 hover:bg-indigo-700 text-white font-bold py-2.5 px-5 rounded-xl text-xs shadow-md">Xác nhận nhập lô hàng</button>
            </div>
        </div>
    </div>

    <!-- ==================== MODAL 4: THÊM NHÂN VIÊN ==================== -->
    <div id="modal-staff" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-[60] flex items-end md:items-center justify-center hidden p-4">
        <div class="bg-white w-full max-w-md rounded-t-[2rem] md:rounded-2xl shadow-xl overflow-hidden slide-up md:animate-none">
            <div class="bg-emerald-600 text-white px-5 py-4 flex justify-between items-center">
                <h3 class="font-bold text-sm" id="staff-modal-title">Thêm Nhân Sự</h3>
                <button onclick="closeStaffModal()" class="text-white"><svg class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" /></svg></button>
            </div>
            <form id="staff-form" onsubmit="saveStaff(event)" class="p-5 space-y-4">
                <input type="hidden" id="edit-staff-id">
                <div class="grid grid-cols-2 gap-3">
                    <div>
                        <label class="block text-[10px] font-bold text-slate-500 mb-1 uppercase">Mã NV *</label>
                        <input type="text" id="staff-code" required class="w-full p-2.5 bg-slate-50 border border-slate-200 rounded-xl text-xs">
                    </div>
                    <div>
                        <label class="block text-[10px] font-bold text-slate-500 mb-1 uppercase">Phân quyền *</label>
                        <select id="staff-role" required class="w-full p-2.5 bg-slate-50 border border-slate-200 rounded-xl text-xs">
                            <option value="Staff">Staff (Nhân viên)</option>
                            <option value="Admin">Admin (Quản lý)</option>
                        </select>
                    </div>
                </div>
                <div>
                    <label class="block text-[10px] font-bold text-slate-500 mb-1 uppercase">Họ và Tên *</label>
                    <input type="text" id="staff-name" required class="w-full p-2.5 bg-slate-50 border border-slate-200 rounded-xl text-xs">
                </div>
                <div class="grid grid-cols-2 gap-3">
                    <div>
                        <label class="block text-[10px] font-bold text-slate-500 mb-1 uppercase">Tên đăng nhập *</label>
                        <input type="text" id="staff-username" required class="w-full p-2.5 bg-slate-50 border border-slate-200 rounded-xl text-xs">
                    </div>
                    <div>
                        <label class="block text-[10px] font-bold text-slate-500 mb-1 uppercase">Mật khẩu *</label>
                        <input type="password" id="staff-password" required class="w-full p-2.5 bg-slate-50 border border-slate-200 rounded-xl text-xs">
                    </div>
                </div>
                <div class="pt-2 flex space-x-2">
                    <button type="button" onclick="closeStaffModal()" class="w-1/2 bg-slate-100 text-slate-700 py-3 rounded-xl font-bold text-xs">Hủy</button>
                    <button type="submit" class="w-1/2 bg-emerald-600 text-white py-3 rounded-xl font-bold text-xs shadow-md">Lưu tài khoản</button>
                </div>
            </form>
        </div>
    </div>

    <!-- ==================== MODAL 5: HÓA ĐƠN CHI TIẾT (RECEIPT) ==================== -->
    <div id="modal-receipt" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-[60] flex items-center justify-center p-4 hidden">
        <div class="bg-white w-full max-w-sm rounded-2xl shadow-xl overflow-hidden flex flex-col max-h-[90vh]">
            <div class="bg-slate-800 text-white px-4 py-3 flex justify-between items-center shrink-0">
                <h3 class="font-bold text-xs">Biên Lai Bán Hàng #<span id="receipt-invoice-code"></span></h3>
                <button onclick="closeReceiptModal()" class="text-slate-400 hover:text-white"><svg class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" /></svg></button>
            </div>
            <div class="flex-1 overflow-y-auto p-4 bg-amber-50/10">
                <div class="bg-white border border-slate-200 rounded-xl p-4 shadow-sm space-y-3 text-[11px]" id="printable-receipt-content">
                    <div class="text-center space-y-0.5">
                        <h4 class="font-bold text-emerald-600 uppercase">KiotPro Store</h4>
                        <p class="text-[9px] text-slate-400">Hệ thống phân phối điện thoại di động chính hãng</p>
                    </div>
                    <div class="border-t border-dashed border-slate-300"></div>
                    <div class="space-y-1 text-slate-600">
                        <div class="flex justify-between"><span>Số phiếu:</span><span class="font-bold text-slate-800" id="receipt-id"></span></div>
                        <div class="flex justify-between"><span>Thời gian lập:</span><span id="receipt-date"></span></div>
                        <div class="flex justify-between"><span>Khách mua hàng:</span><span class="font-bold text-slate-800" id="receipt-customer-name"></span></div>
                        <div class="flex justify-between"><span>Người thanh toán:</span><span class="font-bold text-emerald-700" id="receipt-seller"></span></div>
                    </div>
                    <div class="border-t border-dashed border-slate-300"></div>
                    <div class="space-y-2" id="receipt-items-container"></div>
                    <div class="border-t border-dashed border-slate-300"></div>
                    <div class="space-y-1 text-[11px]">
                        <div class="flex justify-between text-slate-500"><span>Tổng cộng hàng bán:</span><span id="receipt-subtotal"></span></div>
                        <div class="flex justify-between text-slate-500"><span>Chiết khấu hoá đơn:</span><span id="receipt-discount"></span></div>
                        <div class="flex justify-between font-bold text-xs text-slate-800 pt-1.5 border-t border-slate-150">
                            <span>Thực tế khách thanh toán:</span><span class="text-emerald-600 font-extrabold" id="receipt-total-amount"></span>
                        </div>
                    </div>
                </div>
            </div>
            <div class="bg-slate-50 px-4 py-3 border-t border-slate-100 flex space-x-2 shrink-0">
                <button onclick="closeReceiptModal()" class="w-full bg-slate-900 text-white font-bold py-2.5 rounded-xl text-xs hover:bg-slate-800">Xác nhận đóng</button>
            </div>
        </div>
    </div>

    <!-- TOAST THÔNG BÁO DƯỚI GÓC -->
    <div id="toast-container" class="fixed bottom-24 left-4 right-4 md:top-6 md:bottom-auto md:left-auto md:right-6 md:w-80 space-y-2 z-[100] pointer-events-none mx-auto"></div>

    <!-- ==================== LOGIC XỬ LÝ SỰ KIỆN JAVASCRIPT ==================== -->
    <script>
        // DATA KHỞI TẠO HỆ THỐNG
        const defaultStaffs = [
            { id: "admin", code: "ADMIN", name: "Trần Hoàng Long (Quản lý)", username: "admin", password: "123", role: "Admin" },
            { id: "s1", code: "NV001", name: "Nguyễn Minh Quân", username: "nhanvien", password: "123", role: "Staff" }
        ];

        const defaultProducts = [
            { id: "p1", code: "DT001", name: "iPhone 15 Pro Max 256GB", category: "iPhone", costPrice: 27000000, sellingPrice: 29490000, stock: 12, minStock: 3 },
            { id: "p2", code: "DT002", name: "Samsung Galaxy S24 Ultra", category: "Samsung", costPrice: 25000000, sellingPrice: 27890000, stock: 8, minStock: 2 },
            { id: "p3", code: "DT003", name: "iPhone 13 128GB", category: "iPhone", costPrice: 12500000, sellingPrice: 13990000, stock: 18, minStock: 4 },
            { id: "p4", code: "DT004", name: "Oppo Reno11 5G", category: "Oppo", costPrice: 8000000, sellingPrice: 9190000, stock: 5, minStock: 3 },
            { id: "p5", code: "DT005", name: "Xiaomi Redmi Note 13", category: "Xiaomi", costPrice: 4000000, sellingPrice: 4690000, stock: 1, minStock: 5 }
        ];

        const defaultCustomers = [
            { id: "c1", code: "KH001", name: "Phan Anh Tuấn", phone: "0901234567", address: "Quận 3, TP. Hồ Chí Minh", totalSpent: 43480000 },
            { id: "c2", code: "KH002", name: "Trần Thị Ánh Tuyết", phone: "0987654321", address: "Hoàn Kiếm, Hà Nội", totalSpent: 13990000 },
            { id: "c_guest", code: "KHACH_LE", name: "Khách vãng lai", phone: "-", address: "-", totalSpent: 0 }
        ];

        const defaultInvoices = [
            { 
                id: "inv1", code: "HD5320", date: "2026-06-03 10:30", customerId: "c1", customerName: "Phan Anh Tuấn", paymentMethod: "Chuyển khoản",
                items: [{ id: "p1", code: "DT001", name: "iPhone 15 Pro Max 256GB", sellingPrice: 29490000, quantity: 1, costPrice: 27000000 }],
                subtotal: 29490000, discount: 0, totalAmount: 29490000, costTotal: 27000000, profit: 2490000, seller: "Trần Hoàng Long"
            }
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

        // ĐỊNH DẠNG TIỀN VÀ THÔNG BÁO TOAST
        function formatVND(amount) { return new Intl.NumberFormat('vi-VN', { style: 'currency', currency: 'VND' }).format(amount); }
        
        function showToast(message, type = "success") {
            const container = document.getElementById("toast-container");
            const toast = document.createElement("div");
            toast.className = `flex items-center space-x-3 px-5 py-3 rounded-2xl shadow-xl text-white font-bold text-xs transition-all duration-300 transform translate-y-2 opacity-0 justify-center ${type==='success'?'bg-slate-900 border border-slate-700':type==='warning'?'bg-amber-500':'bg-red-500'}`;
            toast.innerHTML = `<span>${message}</span>`;
            container.appendChild(toast);
            setTimeout(() => toast.classList.remove("translate-y-2", "opacity-0"), 10);
            setTimeout(() => { toast.classList.add("translate-y-2", "opacity-0"); setTimeout(() => toast.remove(), 300); }, 2500);
        }

        // ==================== KHỞI TẠO BỘ ĐĂNG NHẬP & PHÂN QUYỀN ====================
        function handleLogin(event) {
            event.preventDefault();
            const u = document.getElementById("login-username").value.trim().toLowerCase();
            const p = document.getElementById("login-password").value.trim();

            const matched = staffs.find(s => s.username === u && s.password === p);
            if (matched) {
                currentUser = matched;
                document.getElementById("login-screen").classList.add("hidden");
                showToast(`Đăng nhập thành công, chào ${matched.name}!`);

                // Ghi nhận thông tin header
                document.getElementById("header-user-name").innerText = matched.name.split(' ').pop();
                document.getElementById("header-user-role").innerText = matched.role === "Admin" ? "Quản lý" : "Nhân viên";
                document.getElementById("sidebar-user-name").innerText = matched.name;
                document.getElementById("sidebar-user-role").innerText = matched.role === "Admin" ? "Quản trị cao cấp" : "Nhân viên trực ca";

                buildNavigations();
                switchTab('pos');
            } else {
                showToast("Tài khoản hoặc mật khẩu không chính xác!", "error");
            }
        }

        function handleLogout() {
            if (confirm("Bạn có chắc chắn muốn đăng xuất khỏi hệ thống?")) {
                currentUser = null;
                cart = [];
                renderCart();
                document.getElementById("login-screen").classList.remove("hidden");
                showToast("Hẹn gặp lại bạn lần sau!");
            }
        }

        function buildNavigations() {
            const bottomNav = document.getElementById("bottom-navigation-bar");
            const sideNav = document.getElementById("desktop-navigation-container");
            bottomNav.innerHTML = "";
            sideNav.innerHTML = "";

            let tabs = [
                { id: "pos", name: "Bán hàng", icon: `<svg class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 3h2l.4 2M7 13h10l4-8H5.4M7 13L5.4 5M7 13l-2.293 2.293c-.63.63-.184 1.707.707 1.707H17m0 0a2 2 0 100 4 2 2 0 000-4zm-8 2a2 2 0 11-4 0 2 2 0 014 0z"/></svg>` },
                { id: "products", name: "Kho hàng", icon: `<svg class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M20 7l-8-4-8 4m16 0l-8 4m8-4v10l-8 4m0-10L4 7m8 4v10M4 7v10l8 4"/></svg>` },
                { id: "invoices", name: "Hóa đơn", icon: `<svg class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5H7a2 2 0 00-2 2v12a2 2 0 002 2h10a2 2 0 002-2V7a2 2 0 00-2-2h-2M9 5a2 2 0 002 2h2a2 2 0 002-2M9 5a2 2 0 012-2h2a2 2 0 012 2"/></svg>` },
                { id: "customers", name: "Khách hàng", icon: `<svg class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4.354a4 4 0 110 5.292M15 21H3a1 1 0 01-1-1 9 9 0 018.369-8.981 9 9 0 018.369 8.981 1 1 0 01-1 1zM18 10a3 3 0 11-6 0 3 3 0 016 0zm-1 4a6.002 6.002 0 01-4 5.36M11 20h2"/></svg>` }
            ];

            if (currentUser && currentUser.role === "Admin") {
                tabs.push(
                    { id: "staffs", name: "Nhân viên", icon: `<svg class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4.354a4 4 0 110 5.292M15 21H3a1 1 0 01-1-1 9 9 0 018.369-8.981 9 9 0 018.369 8.981 1 1 0 01-1 1z"/></svg>` },
                    { id: "reports", name: "Báo cáo", icon: `<svg class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 19v-6a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2a2 2 0 002-2zm0 0V9a2 2 0 012-2h2a2 2 0 012 2v10m-6 0a2 2 0 002 2h2a2 2 0 002-2m0 0V5a2 2 0 012-2h2a2 2 0 012 2v14a2 2 0 01-2 2h-2a2 2 0 01-2-2z"/></svg>` }
                );
            }

            tabs.forEach(t => {
                // Di động Bottom Navigation
                const btnMobile = document.createElement("button");
                btnMobile.className = "mobile-nav-btn flex flex-col items-center justify-center w-14 py-2 text-slate-400 transition-all focus:outline-none";
                btnMobile.dataset.tab = t.id;
                btnMobile.onclick = () => switchTab(t.id);
                btnMobile.innerHTML = `${t.icon}<span class="text-[9px] mt-0.5">${t.name}</span>`;
                bottomNav.appendChild(btnMobile);

                // Máy tính Sidebar Navigation
                const btnDesktop = document.createElement("button");
                btnDesktop.className = "desktop-nav-btn flex items-center space-x-3.5 w-full p-3.5 rounded-2xl text-slate-400 hover:bg-slate-800 transition-all text-left";
                btnDesktop.dataset.tab = t.id;
                btnDesktop.onclick = () => switchTab(t.id);
                btnDesktop.innerHTML = `${t.icon}<span class="font-bold text-sm">${t.name}</span>`;
                sideNav.appendChild(btnDesktop);
            });
        }

        function switchTab(tabId) {
            if (!currentUser) return;
            document.querySelectorAll(".tab-content").forEach(el => el.classList.add("hidden"));
            document.getElementById(`section-${tabId}`).classList.remove("hidden");

            // Cập nhật tiêu đề màn hình máy tính
            const titles = { pos: "Màn Hình Bán Hàng (POS)", products: "Quản Lý Danh Mục Kho", invoices: "Nhật Ký Giao Dịch", customers: "Danh Mục Khách Hàng", staffs: "Danh Sách Nhân Sự", reports: "Biểu Đồ Kết Quả Kinh Doanh" };
            document.getElementById("desktop-header-title").innerText = titles[tabId];

            // Đổi trạng thái hiển thị Active các nút điều hướng
            document.querySelectorAll(".mobile-nav-btn").forEach(b => { b.classList.replace("text-emerald-600", "text-slate-400"); b.classList.remove("font-extrabold"); });
            document.querySelectorAll(".desktop-nav-btn").forEach(b => { b.classList.remove("bg-emerald-500/10", "text-emerald-400", "border", "border-emerald-500/20"); b.classList.add("text-slate-400"); });

            const mActive = document.querySelector(`.mobile-nav-btn[data-tab="${tabId}"]`);
            if (mActive) { mActive.classList.replace("text-slate-400", "text-emerald-600"); mActive.classList.add("font-extrabold"); }

            const dActive = document.querySelector(`.desktop-nav-btn[data-tab="${tabId}"]`);
            if (dActive) {
                dActive.classList.add("bg-emerald-500/10", "text-emerald-400", "border", "border-emerald-500/20");
                dActive.classList.remove("text-slate-400");
            }

            // Phân quyền hiển thị các nút thao tác nhanh
            const prodActionGroup = document.getElementById("product-action-buttons");
            if (prodActionGroup) {
                prodActionGroup.style.display = currentUser.role === "Admin" ? "flex" : "none";
            }

            // Tải dữ liệu tương ứng
            if (tabId === 'pos') initPosScreen();
            else if (tabId === 'products') renderProductTable();
            else if (tabId === 'invoices') renderInvoiceTable();
            else if (tabId === 'customers') renderCustomerTable();
            else if (tabId === 'staffs') renderStaffTable();
            else if (tabId === 'reports') setReportPeriod('day');
        }

        // ==================== POS BÁN HÀNG ====================
        function initPosScreen() {
            renderPosCategories();
            renderPosProducts();
            populatePosCustomerDropdown();
            calcCartTotals();
        }

        function renderPosCategories() {
            const categories = ["all", "iPhone", "Samsung", "Oppo", "Xiaomi", "Phụ kiện"];
            const container = document.getElementById("pos-category-filters");
            container.innerHTML = "";

            categories.forEach(cat => {
                const isActive = currentSelectedCategory === cat;
                const btn = document.createElement("button");
                btn.className = `px-4.5 py-2 rounded-xl text-xs font-bold whitespace-nowrap transition-all border ${isActive ? 'bg-slate-900 text-white border-slate-900 shadow-md' : 'bg-slate-100 text-slate-500 border-transparent hover:bg-slate-200'}`;
                btn.innerText = cat === "all" ? "Tất cả hàng hóa" : cat;
                btn.onclick = () => {
                    currentSelectedCategory = cat;
                    renderPosCategories();
                    renderPosProducts();
                };
                container.appendChild(btn);
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

            if (filtered.length === 0) {
                grid.innerHTML = `<div class="col-span-full text-center py-10 text-slate-400 text-xs font-semibold">Không tìm thấy sản phẩm nào phù hợp</div>`;
                return;
            }

            filtered.forEach(p => {
                const isOutOfStock = p.stock <= 0;
                const card = document.createElement("div");
                card.className = `bg-white p-3.5 rounded-2xl border border-slate-100 flex flex-col justify-between space-y-2 relative transition-all ${isOutOfStock ? "opacity-50 select-none bg-slate-50" : "card-hover cursor-pointer border-slate-100 shadow-sm"}`;
                if (!isOutOfStock) {
                    card.onclick = () => addToCart(p);
                }

                const badge = isOutOfStock 
                    ? `<span class="absolute top-2.5 right-2.5 bg-red-600 text-white text-[8px] px-1.5 py-0.5 rounded-lg font-black shadow-sm">HẾT HÀNG</span>`
                    : `<span class="absolute top-2.5 right-2.5 bg-emerald-50 text-emerald-600 border border-emerald-100 text-[8px] px-1.5 py-0.5 rounded-lg font-black">CÒN ${p.stock}</span>`;

                card.innerHTML = `
                    <div class="space-y-1">
                        ${badge}
                        <span class="text-[9px] text-slate-400 font-extrabold uppercase block tracking-wider pt-2">${p.category}</span>
                        <h4 class="font-bold text-xs text-slate-800 line-clamp-2 leading-tight">${p.name}</h4>
                    </div>
                    <div class="flex justify-between items-center pt-2.5 border-t border-slate-50">
                        <span class="font-black text-xs text-slate-900">${formatVND(p.sellingPrice)}</span>
                        <div class="w-6 h-6 rounded-lg bg-emerald-50 text-emerald-600 border border-emerald-100 flex items-center justify-center font-bold text-xs">+</div>
                    </div>
                `;
                grid.appendChild(card);
            });
        }

        function populatePosCustomerDropdown() {
            const dropdown = document.getElementById("pos-customer-select");
            dropdown.innerHTML = "";
            customers.forEach(c => {
                const opt = document.createElement("option");
                opt.value = c.id;
                opt.innerText = c.name + (c.phone !== "-" ? ` (${c.phone})` : "");
                if (c.code === "KHACH_LE") opt.selected = true;
                dropdown.appendChild(opt);
            });
        }

        function addToCart(product) {
            const existing = cart.find(item => item.id === product.id);
            const currentQty = existing ? existing.quantity : 0;

            if (currentQty + 1 > product.stock) {
                showToast(`Không đủ số lượng máy trong kho (Còn ${product.stock} sản phẩm)`, "warning");
                return;
            }

            if (existing) {
                existing.quantity++;
            } else {
                cart.push({ id: product.id, name: product.name, sellingPrice: product.sellingPrice, costPrice: product.costPrice, quantity: 1 });
            }

            renderCart();
            calcCartTotals();
            showToast("Đã thêm sản phẩm vào giỏ hàng");
        }

        function toggleCartSheet(show) {
            const sheet = document.getElementById("cart-bottom-sheet");
            if (show) sheet.classList.remove("hidden");
            else sheet.classList.add("hidden");
        }

        function renderCart() {
            const container = document.getElementById("pos-cart-container");
            const floatBar = document.getElementById("floating-cart-bar");
            const deskCount = document.getElementById("desktop-cart-count");
            container.innerHTML = "";

            let count = 0;
            cart.forEach((item, index) => {
                count += item.quantity;
                const row = document.createElement("div");
                row.className = "py-3 flex items-center justify-between space-x-2";
                row.innerHTML = `
                    <div class="flex-1 min-w-0">
                        <h5 class="font-bold text-xs text-slate-800 truncate">${item.name}</h5>
                        <span class="text-[10px] text-slate-400 font-bold block">${formatVND(item.sellingPrice)}</span>
                    </div>
                    <div class="flex items-center space-x-1.5 shrink-0">
                        <button onclick="updateCartQty(${index}, -1)" class="w-6 h-6 rounded-full border border-slate-250 bg-white flex items-center justify-center font-bold text-xs">-</button>
                        <span class="w-5 text-center font-bold text-xs">${item.quantity}</span>
                        <button onclick="updateCartQty(${index}, 1)" class="w-6 h-6 rounded-full border border-slate-250 bg-white flex items-center justify-center font-bold text-xs">+</button>
                    </div>
                `;
                container.appendChild(row);
            });

            if (count === 0) {
                container.innerHTML = `<div class="py-10 text-center text-slate-400 text-xs font-semibold">Giỏ hàng bán lẻ đang trống</div>`;
                floatBar.classList.add("hidden");
            } else {
                floatBar.classList.remove("hidden");
            }

            document.getElementById("pos-cart-count").innerText = count;
            document.getElementById("cart-floating-count").innerText = count;
            deskCount.innerText = count;
        }

        function updateCartQty(index, offset) {
            const item = cart[index];
            const original = products.find(p => p.id === item.id);
            if (item.quantity + offset <= 0) {
                cart.splice(index, 1);
                showToast("Đã xoá sản phẩm khỏi giỏ hàng");
            } else if (item.quantity + offset > original.stock) {
                showToast("Vượt quá số lượng tồn kho khả dụng!", "warning");
                return;
            } else {
                item.quantity += offset;
            }
            renderCart();
            calcCartTotals();
        }

        function clearCart() {
            cart = [];
            renderCart();
            calcCartTotals();
            toggleCartSheet(false);
            showToast("Đã dọn sạch giỏ hàng");
        }

        function calcCartTotals() {
            let subtotal = 0;
            cart.forEach(item => subtotal += item.sellingPrice * item.quantity);
            const discountPct = parseInt(document.getElementById("pos-discount").value) || 0;
            const discountAmt = Math.round(subtotal * (discountPct / 100));
            const total = subtotal - discountAmt;

            document.getElementById("pos-subtotal").innerText = formatVND(subtotal);
            document.getElementById("pos-total-amount").innerText = formatVND(total);
            document.getElementById("cart-floating-total").innerText = formatVND(total);
        }

        function checkoutCart() {
            if (cart.length === 0) return;
            const custId = document.getElementById("pos-customer-select").value;
            const customer = customers.find(c => c.id === custId);
            const discountPct = parseInt(document.getElementById("pos-discount").value) || 0;
            
            let subtotal = 0, costTotal = 0;
            cart.forEach(item => {
                subtotal += item.sellingPrice * item.quantity;
                costTotal += item.costPrice * item.quantity;
            });
            const totalAmount = subtotal - Math.round(subtotal * (discountPct / 100));

            const invoiceObj = {
                id: "inv_" + Date.now(),
                code: "HD" + Math.floor(1000 + Math.random() * 9000),
                date: "2026-06-05 " + String(new Date().getHours()).padStart(2,'0') + ":" + String(new Date().getMinutes()).padStart(2,'0'),
                customerId: customer.id,
                customerName: customer.name,
                paymentMethod: document.getElementById("pos-payment").value,
                items: [...cart],
                subtotal,
                discount: discountPct,
                totalAmount,
                costTotal,
                profit: totalAmount - costTotal,
                seller: currentUser ? currentUser.name : "Admin"
            };

            // Trừ hàng trong kho
            cart.forEach(item => {
                const orig = products.find(p => p.id === item.id);
                if (orig) orig.stock = Math.max(0, orig.stock - item.quantity);
            });

            if (customer.code !== "KHACH_LE") {
                customer.totalSpent += totalAmount;
            }

            invoices.unshift(invoiceObj);
            saveState();

            // Khôi phục lại trạng thái POS
            cart = [];
            renderCart();
            document.getElementById("pos-discount").value = 0;
            calcCartTotals();
            toggleCartSheet(false);
            renderPosProducts();
            showToast(`Giao dịch xuất đơn hàng #${invoiceObj.code} thành công`);

            // Hiển thị Biên Lai In Ấn
            document.getElementById("receipt-invoice-code").innerText = invoiceObj.code;
            document.getElementById("receipt-id").innerText = invoiceObj.code;
            document.getElementById("receipt-date").innerText = invoiceObj.date;
            document.getElementById("receipt-customer-name").innerText = invoiceObj.customerName;
            document.getElementById("receipt-seller").innerText = invoiceObj.seller;

            const rcptCont = document.getElementById("receipt-items-container");
            rcptCont.innerHTML = "";
            invoiceObj.items.forEach(item => {
                rcptCont.innerHTML += `
                    <div class="flex justify-between py-1">
                        <div class="pr-2 truncate">
                            <span class="block font-bold text-slate-800">${item.name}</span>
                            <span class="text-[9px] text-slate-400">SL: ${item.quantity} x ${formatVND(item.sellingPrice)}</span>
                        </div>
                        <span class="font-extrabold text-slate-900 shrink-0">${formatVND(item.sellingPrice * item.quantity)}</span>
                    </div>
                `;
            });

            document.getElementById("receipt-subtotal").innerText = formatVND(invoiceObj.subtotal);
            document.getElementById("receipt-discount").innerText = invoiceObj.discount > 0 ? `-${formatVND(Math.round(invoiceObj.subtotal * (invoiceObj.discount / 100)))} (${invoiceObj.discount}%)` : "0 ₫";
            document.getElementById("receipt-total-amount").innerText = formatVND(invoiceObj.totalAmount);
            document.getElementById("modal-receipt").classList.remove("hidden");
        }

        function closeReceiptModal() { document.getElementById("modal-receipt").classList.add("hidden"); }

        // ==================== KHO HÀNG & NHẬP HÀNG LOẠT ====================
        function renderProductTable() {
            const container = document.getElementById("product-cards-container");
            container.innerHTML = "";

            const search = document.getElementById("product-list-search").value.toLowerCase();
            const category = document.getElementById("product-list-category").value;
            const status = document.getElementById("product-list-status").value;

            const filtered = products.filter(p => {
                const matchSearch = p.name.toLowerCase().includes(search) || p.code.toLowerCase().includes(search);
                const matchCat = category === "all" || p.category === category;
                let matchStatus = true;
                if (status === "low-stock") {
                    matchStatus = p.stock <= p.minStock && p.stock > 0;
                } else if (status === "out-of-stock") {
                    matchStatus = p.stock === 0;
                }
                return matchSearch && matchCat && matchStatus;
            });

            if (filtered.length === 0) {
                container.innerHTML = `<div class="col-span-full text-center py-10 text-slate-400 text-xs font-semibold">Chưa có sản phẩm nào tương ứng</div>`;
                return;
            }

            filtered.forEach(p => {
                const card = document.createElement("div");
                card.className = "bg-white p-4 rounded-2xl border border-slate-100 shadow-sm space-y-3 card-hover";
                
                let badge = `<span class="bg-emerald-50 text-emerald-700 border border-emerald-100 px-2 py-0.5 rounded-lg text-[9px] font-bold">An Toàn</span>`;
                if (p.stock === 0) {
                    badge = `<span class="bg-red-50 text-red-700 border border-red-100 px-2 py-0.5 rounded-lg text-[9px] font-bold">Hết hàng</span>`;
                } else if (p.stock <= p.minStock) {
                    badge = `<span class="bg-amber-50 text-amber-700 border border-amber-100 px-2 py-0.5 rounded-lg text-[9px] font-bold">Dưới định mức</span>`;
                }

                card.innerHTML = `
                    <div class="flex justify-between items-start">
                        <div>
                            <span class="text-[9px] text-slate-400 font-extrabold block uppercase tracking-wider">${p.code} | ${p.category}</span>
                            <h4 class="font-bold text-xs text-slate-800 leading-tight">${p.name}</h4>
                        </div>
                        ${badge}
                    </div>
                    <div class="grid grid-cols-2 gap-2 text-[10px] font-bold text-slate-500 bg-slate-50 p-2.5 rounded-xl">
                        <span>Giá nhập: <span class="text-slate-800">${formatVND(p.costPrice)}</span></span>
                        <span class="text-right">Tồn hiện tại: <span class="text-slate-900 text-xs font-black">${p.stock} máy</span></span>
                        <span class="col-span-2 pt-1 border-t border-slate-200 text-emerald-600">Giá bán lẻ: ${formatVND(p.sellingPrice)}</span>
                    </div>
                    ${currentUser.role === 'Admin' ? `
                    <div class="flex justify-end space-x-3 text-[11px] font-extrabold pt-2 border-t border-slate-100">
                        <button onclick="openProductModal(true, '${p.id}')" class="text-blue-600 hover:underline">Sửa thông tin</button>
                        <button onclick="deleteProduct('${p.id}')" class="text-red-500 hover:underline">Xoá thiết bị</button>
                    </div>
                    ` : ''}
                `;
                container.appendChild(card);
            });
        }

        function openProductModal(isEdit, id = "") {
            const modal = document.getElementById("modal-product");
            const title = document.getElementById("product-modal-title");
            document.getElementById("product-form").reset();

            if (isEdit) {
                title.innerText = "Chỉnh sửa sản phẩm";
                const p = products.find(x => x.id === id);
                if (p) {
                    document.getElementById("edit-product-id").value = p.id;
                    document.getElementById("prod-code").value = p.code;
                    document.getElementById("prod-category").value = p.category;
                    document.getElementById("prod-name").value = p.name;
                    document.getElementById("prod-cost").value = p.costPrice;
                    document.getElementById("prod-selling").value = p.sellingPrice;
                    document.getElementById("prod-stock").value = p.stock;
                    document.getElementById("prod-minStock").value = p.minStock;
                }
            } else {
                title.innerText = "Thêm sản phẩm mới";
                document.getElementById("edit-product-id").value = "";
                document.getElementById("prod-code").value = "DT0" + (products.length + 1);
            }
            modal.classList.remove("hidden");
        }

        function closeProductModal() { document.getElementById("modal-product").classList.add("hidden"); }

        function saveProduct(event) {
            event.preventDefault();
            const id = document.getElementById("edit-product-id").value;
            const code = document.getElementById("prod-code").value.trim().toUpperCase();
            const category = document.getElementById("prod-category").value;
            const name = document.getElementById("prod-name").value.trim();
            const costPrice = parseInt(document.getElementById("prod-cost").value) || 0;
            const sellingPrice = parseInt(document.getElementById("prod-selling").value) || 0;
            const stock = parseInt(document.getElementById("prod-stock").value) || 0;
            const minStock = parseInt(document.getElementById("prod-minStock").value) || 0;

            if (id) {
                const idx = products.findIndex(p => p.id === id);
                if (idx !== -1) {
                    products[idx] = { id, code, category, name, costPrice, sellingPrice, stock, minStock };
                    showToast("Cập nhật thông tin thành công");
                }
            } else {
                if (products.some(p => p.code === code)) {
                    showToast("Mã sản phẩm này đã trùng!", "error");
                    return;
                }
                products.push({ id: "prod_" + Date.now(), code, category, name, costPrice, sellingPrice, stock, minStock });
                showToast("Đã lưu thiết bị mới vào danh mục");
            }
            saveState();
            closeProductModal();
            renderProductTable();
        }

        function deleteProduct(id) {
            if (confirm("Hành động này sẽ xóa sản phẩm khỏi danh mục vĩnh viễn. Đồng ý?")) {
                products = products.filter(p => p.id !== id);
                saveState();
                renderProductTable();
                showToast("Đã xóa sản phẩm");
            }
        }

        // ==================== NHẬP KHO HÀNG LOẠT (BULK IMPORT) ====================
        function openImportStockModal() {
            importCart = [];
            document.getElementById("import-search").value = "";
            document.getElementById("import-search-results").classList.add("hidden");
            renderImportList();
            document.getElementById("modal-import-stock").classList.remove("hidden");
        }

        function closeImportStockModal() {
            document.getElementById("modal-import-stock").classList.add("hidden");
        }

        function searchImportProduct() {
            const q = document.getElementById("import-search").value.toLowerCase().trim();
            const resDiv = document.getElementById("import-search-results");
            if (q.length < 1) { resDiv.classList.add("hidden"); return; }

            const matches = products.filter(p => p.name.toLowerCase().includes(q) || p.code.toLowerCase().includes(q));
            resDiv.innerHTML = "";
            if (matches.length === 0) {
                resDiv.innerHTML = `<div class="p-3 text-xs text-slate-400 text-center">Không tìm thấy mã máy này</div>`;
            } else {
                matches.forEach(p => {
                    const btn = document.createElement("button");
                    btn.type = "button";
                    btn.className = "w-full text-left p-2.5 hover:bg-slate-50 border-b border-slate-100 flex justify-between items-center text-xs font-bold text-slate-700";
                    btn.innerHTML = `<span>${p.name}</span><span class="text-[10px] text-indigo-500 font-extrabold">${p.code}</span>`;
                    btn.onclick = () => addProductToImport(p);
                    resDiv.appendChild(btn);
                });
            }
            resDiv.classList.remove("hidden");
        }

        function addProductToImport(p) {
            document.getElementById("import-search").value = "";
            document.getElementById("import-search-results").classList.add("hidden");

            const existing = importCart.find(i => i.id === p.id);
            if (existing) {
                existing.qty++;
            } else {
                importCart.push({ id: p.id, name: p.name, code: p.code, costPrice: p.costPrice, qty: 1 });
            }
            renderImportList();
        }

        function renderImportList() {
            const container = document.getElementById("import-list-container");
            container.innerHTML = "";
            let total = 0;

            if (importCart.length === 0) {
                container.innerHTML = `<div class="text-center py-10 text-slate-400 text-xs font-semibold">Chưa có sản phẩm nào được chọn để bổ sung</div>`;
            } else {
                importCart.forEach((item, index) => {
                    total += (item.qty * item.costPrice);
                    const row = document.createElement("div");
                    row.className = "bg-white p-3.5 rounded-xl border border-slate-200 flex flex-col sm:flex-row justify-between items-start sm:items-center gap-3";
                    row.innerHTML = `
                        <div class="flex-1 min-w-0">
                            <h5 class="font-extrabold text-xs text-slate-800 truncate">${item.name}</h5>
                            <span class="text-[9px] text-slate-400 block font-bold">${item.code}</span>
                        </div>
                        <div class="flex items-center space-x-3 w-full sm:w-auto">
                            <div class="w-20">
                                <label class="text-[9px] text-slate-400 font-bold block mb-0.5">SỐ LƯỢNG</label>
                                <input type="number" min="1" value="${item.qty}" onchange="updateImportItem(${index}, 'qty', this.value)" class="w-full border border-slate-200 rounded-lg p-1 text-center font-extrabold text-xs">
                            </div>
                            <div class="flex-1 sm:w-28">
                                <label class="text-[9px] text-slate-400 font-bold block mb-0.5">ĐƠN GIÁ NHẬP</label>
                                <input type="number" min="0" value="${item.costPrice}" onchange="updateImportItem(${index}, 'costPrice', this.value)" class="w-full border border-slate-200 rounded-lg p-1 text-right font-extrabold text-xs text-indigo-600">
                            </div>
                            <button onclick="removeImportItem(${index})" class="text-red-500 mt-3.5 p-1 hover:bg-red-50 rounded-lg">
                                <svg class="h-4.5 w-4.5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" /></svg>
                            </button>
                        </div>
                    `;
                    container.appendChild(row);
                });
            }
            document.getElementById("import-total-money").innerText = formatVND(total);
        }

        function updateImportItem(index, field, val) {
            val = parseInt(val) || 0;
            if (field === 'qty' && val < 1) val = 1;
            importCart[index][field] = val;
            renderImportList();
        }

        function removeImportItem(index) {
            importCart.splice(index, 1);
            renderImportList();
        }

        function processBulkImport() {
            if (importCart.length === 0) return;

            importCart.forEach(item => {
                const p = products.find(prod => prod.id === item.id);
                if (p) {
                    p.stock += item.qty;
                    p.costPrice = item.costPrice; // Cập nhật lại giá vốn thực tế mới nhất
                }
            });

            saveState();
            closeImportStockModal();
            renderProductTable();
            showToast(`Cập nhật bổ sung thành công kho hàng loạt (${importCart.length} thiết bị)`);
        }

        // ==================== KHÁCH HÀNG & NHÂN VIÊN & HOÁ ĐƠN ====================
        function renderInvoiceTable() {
            const container = document.getElementById("invoice-cards-container");
            container.innerHTML = "";
            const q = document.getElementById("invoice-search").value.toLowerCase();

            const filtered = invoices.filter(inv => {
                return inv.code.toLowerCase().includes(q) || inv.customerName.toLowerCase().includes(q);
            });

            if (filtered.length === 0) {
                container.innerHTML = `<div class="col-span-full text-center py-10 text-slate-400 text-xs">Chưa phát sinh giao dịch nào</div>`;
                return;
            }

            filtered.forEach(inv => {
                container.innerHTML += `
                    <div class="bg-white p-4 rounded-2xl border border-slate-100 shadow-sm space-y-2.5">
                        <div class="flex justify-between items-center text-[10px] font-bold text-slate-400 uppercase">
                            <span class="text-emerald-600 font-mono text-xs font-black">#${inv.code}</span>
                            <span>${inv.date}</span>
                        </div>
                        <div class="flex justify-between items-end">
                            <div>
                                <span class="text-[9px] text-slate-400 font-extrabold uppercase block tracking-wider">Khách hàng</span>
                                <span class="text-xs font-black text-slate-800">${inv.customerName}</span>
                            </div>
                            <div class="text-right">
                                <span class="text-[9px] text-slate-400 font-extrabold uppercase block tracking-wider">Giá trị thực</span>
                                <span class="text-sm font-black text-slate-900">${formatVND(inv.totalAmount)}</span>
                            </div>
                        </div>
                        <div class="pt-2.5 border-t border-slate-50 flex justify-between items-center text-[10px] text-slate-400 font-bold">
                            <span>Thanh toán: <span class="text-slate-600">${inv.paymentMethod}</span></span>
                            <button onclick="viewInvoiceDetail('${inv.id}')" class="text-emerald-600 hover:underline">In biên lai</button>
                        </div>
                    </div>
                `;
            });
        }

        function viewInvoiceDetail(id) {
            const inv = invoices.find(i => i.id === id);
            if (inv) {
                document.getElementById("receipt-invoice-code").innerText = inv.code;
                document.getElementById("receipt-id").innerText = inv.code;
                document.getElementById("receipt-date").innerText = inv.date;
                document.getElementById("receipt-customer-name").innerText = inv.customerName;
                document.getElementById("receipt-seller").innerText = inv.seller;

                const container = document.getElementById("receipt-items-container");
                container.innerHTML = "";
                inv.items.forEach(item => {
                    container.innerHTML += `
                        <div class="flex justify-between py-1">
                            <div class="pr-2 truncate">
                                <span class="block font-bold text-slate-800">${item.name}</span>
                                <span class="text-[9px] text-slate-400">SL: ${item.quantity} x ${formatVND(item.sellingPrice)}</span>
                            </div>
                            <span class="font-extrabold text-slate-900 shrink-0">${formatVND(item.sellingPrice * item.quantity)}</span>
                        </div>
                    `;
                });

                document.getElementById("receipt-subtotal").innerText = formatVND(inv.subtotal);
                document.getElementById("receipt-discount").innerText = inv.discount > 0 ? `-${formatVND(Math.round(inv.subtotal * (inv.discount / 100)))} (${inv.discount}%)` : "0 ₫";
                document.getElementById("receipt-total-amount").innerText = formatVND(inv.totalAmount);
                document.getElementById("modal-receipt").classList.remove("hidden");
            }
        }

        function renderCustomerTable() {
            const container = document.getElementById("customer-cards-container");
            container.innerHTML = "";
            const q = document.getElementById("customer-search").value.toLowerCase();

            customers.filter(c => c.name.toLowerCase().includes(q) || c.phone.includes(q)).forEach(c => {
                const isGuest = c.code === "KHACH_LE";
                container.innerHTML += `
                    <div class="bg-white p-4 rounded-2xl border border-slate-100 shadow-sm space-y-2">
                        <div class="flex justify-between items-start">
                            <div class="space-y-0.5">
                                <span class="text-[9px] text-slate-400 font-bold block">${c.code}</span>
                                <h4 class="font-bold text-xs text-slate-800 leading-tight">${c.name}</h4>
                                <span class="text-[10px] text-slate-500 block">SĐT: ${c.phone}</span>
                            </div>
                            <div class="text-right">
                                <span class="text-[9px] text-slate-400 font-bold block uppercase tracking-wider">Doanh số lũy kế</span>
                                <span class="text-xs font-black text-emerald-600">${formatVND(c.totalSpent)}</span>
                            </div>
                        </div>
                        ${!isGuest ? `
                        <div class="flex justify-end space-x-3 text-[11px] font-extrabold pt-2 border-t border-slate-150">
                            <button onclick="openAddCustomerModal(false, '${c.id}')" class="text-blue-600 hover:underline">Sửa</button>
                            <button onclick="deleteCustomer('${c.id}')" class="text-red-500 hover:underline">Xoá</button>
                        </div>
                        ` : ''}
                    </div>
                `;
            });
        }

        function openAddCustomerModal(isPosAdding = false, id = "") {
            const modal = document.getElementById("modal-customer");
            document.getElementById("customer-form").reset();
            document.getElementById("is-pos-adding").value = isPosAdding ? "true" : "false";

            if (id) {
                document.getElementById("customer-modal-title").innerText = "Chỉnh sửa khách hàng";
                const c = customers.find(x => x.id === id);
                if (c) {
                    document.getElementById("edit-customer-id").value = c.id;
                    document.getElementById("cust-code").value = c.code;
                    document.getElementById("cust-name").value = c.name;
                    document.getElementById("cust-phone").value = c.phone;
                    document.getElementById("cust-address").value = c.address;
                }
            } else {
                document.getElementById("customer-modal-title").innerText = "Đăng ký thành viên";
                document.getElementById("edit-customer-id").value = "";
                document.getElementById("cust-code").value = "KH0" + (customers.length);
            }
            modal.classList.remove("hidden");
        }

        function closeCustomerModal() { document.getElementById("modal-customer").classList.add("hidden"); }

        function saveCustomer(event) {
            event.preventDefault();
            const id = document.getElementById("edit-customer-id").value;
            const code = document.getElementById("cust-code").value.trim().toUpperCase();
            const name = document.getElementById("cust-name").value.trim();
            const phone = document.getElementById("cust-phone").value.trim();
            const address = document.getElementById("cust-address").value.trim() || "-";
            const isPos = document.getElementById("is-pos-adding").value === "true";

            if (id) {
                const idx = customers.findIndex(c => c.id === id);
                if (idx !== -1) {
                    customers[idx] = { ...customers[idx], code, name, phone, address };
                    showToast("Cập nhật thành viên thành công");
                }
            } else {
                if (customers.some(c => c.code === code || (c.phone === phone && phone !== "-"))) {
                    showToast("Mã khách hoặc Số điện thoại bị trùng!", "error");
                    return;
                }
                const newCust = { id: "cust_" + Date.now(), code, name, phone, address, totalSpent: 0 };
                customers.push(newCust);
                showToast("Đã đăng ký thành viên mới");

                if (isPos) {
                    setTimeout(() => {
                        populatePosCustomerDropdown();
                        document.getElementById("pos-customer-select").value = newCust.id;
                        toggleCartSheet(true);
                    }, 50);
                }
            }

            saveState();
            closeCustomerModal();
            if (isPos) switchTab('pos');
            else renderCustomerTable();
        }

        function deleteCustomer(id) {
            if (confirm("Xóa thẻ thành viên này?")) {
                customers = customers.filter(c => c.id !== id);
                saveState();
                renderCustomerTable();
                showToast("Đã xóa thẻ khách hàng");
            }
        }

        function renderStaffTable() {
            const container = document.getElementById("staff-cards-container");
            container.innerHTML = "";
            staffs.forEach(s => {
                const isSelf = currentUser && s.id === currentUser.id;
                container.innerHTML += `
                    <div class="bg-white p-4 rounded-2xl border border-slate-100 shadow-sm space-y-2">
                        <div class="flex justify-between items-start">
                            <div>
                                <span class="text-[9px] text-slate-400 font-bold block">${s.code}</span>
                                <h4 class="font-bold text-xs text-slate-800 leading-tight">${s.name}</h4>
                                <span class="text-[10px] text-slate-500 block">Username: <span class="font-mono text-emerald-600">${s.username}</span></span>
                            </div>
                            <span class="px-2.5 py-0.5 rounded-lg text-[9px] font-black ${s.role === "Admin" ? 'bg-amber-50 text-amber-700 border border-amber-100' : 'bg-blue-50 text-blue-700 border border-blue-100'}">${s.role}</span>
                        </div>
                        ${!isSelf ? `
                        <div class="flex justify-end space-x-3 text-[11px] font-extrabold pt-2 border-t border-slate-150">
                            <button onclick="openStaffModal(true, '${s.id}')" class="text-blue-600 hover:underline">Sửa quyền</button>
                            <button onclick="deleteStaff('${s.id}')" class="text-red-500 hover:underline">Đóng tài khoản</button>
                        </div>
                        ` : '<span class="text-[9px] text-emerald-600 italic block text-right font-bold pt-2 border-t border-slate-100">Đang trực hệ thống</span>'}
                    </div>
                `;
            });
        }

        function openStaffModal(isEdit, id = "") {
            const modal = document.getElementById("modal-staff");
            document.getElementById("staff-form").reset();
            if (isEdit) {
                document.getElementById("staff-modal-title").innerText = "Thay đổi nhân quyền";
                const s = staffs.find(x => x.id === id);
                if (s) {
                    document.getElementById("edit-staff-id").value = s.id;
                    document.getElementById("staff-code").value = s.code;
                    document.getElementById("staff-name").value = s.name;
                    document.getElementById("staff-username").value = s.username;
                    document.getElementById("staff-password").value = s.password;
                    document.getElementById("staff-role").value = s.role;
                }
            } else {
                document.getElementById("staff-modal-title").innerText = "Thêm tài khoản nhân sự";
                document.getElementById("edit-staff-id").value = "";
                document.getElementById("staff-code").value = "NV0" + (staffs.length + 1);
            }
            modal.classList.remove("hidden");
        }

        function closeStaffModal() { document.getElementById("modal-staff").classList.add("hidden"); }

        function saveStaff(event) {
            event.preventDefault();
            const id = document.getElementById("edit-staff-id").value;
            const code = document.getElementById("staff-code").value.trim().toUpperCase();
            const name = document.getElementById("staff-name").value.trim();
            const username = document.getElementById("staff-username").value.trim().toLowerCase();
            const password = document.getElementById("staff-password").value.trim();
            const role = document.getElementById("staff-role").value;

            if (id) {
                const idx = staffs.findIndex(s => s.id === id);
                if (idx !== -1) {
                    staffs[idx] = { id, code, name, username, password, role };
                    showToast("Cập nhật nhân viên thành công");
                }
            } else {
                if (staffs.some(s => s.username === username)) {
                    showToast("Tên đăng nhập đã tồn tại!", "error");
                    return;
                }
                staffs.push({ id: "staff_" + Date.now(), code, name, username, password, role });
                showToast("Thêm nhân viên mới thành công");
            }
            saveState();
            closeStaffModal();
            renderStaffTable();
        }

        function deleteStaff(id) {
            if (confirm("Vô hiệu hóa tài khoản nhân sự này?")) {
                staffs = staffs.filter(s => s.id !== id);
                saveState();
                renderStaffTable();
                showToast("Đã khóa quyền truy cập");
            }
        }

        // ==================== BÁO CÁO DOANH THU & ĐỒ THỊ ====================
        function setReportPeriod(period) {
            reportPeriod = period;
            ['day', 'month', 'year'].forEach(p => {
                const btn = document.getElementById(`btn-period-${p}`);
                if (p === period) {
                    btn.className = "py-2 text-xs font-black rounded-xl bg-white text-emerald-700 shadow-sm border border-slate-200/50";
                } else {
                    btn.className = "py-2 text-xs font-bold rounded-xl text-slate-500 hover:bg-slate-100";
                }
            });
            renderReportDashboard();
        }

        function renderReportDashboard() {
            let filtered = [];
            const today = "2026-06-05"; // Giả lập ngày mốc

            if (reportPeriod === 'day') {
                filtered = invoices.filter(i => i.date.startsWith(today));
            } else if (reportPeriod === 'month') {
                filtered = invoices.filter(i => i.date.startsWith("2026-06"));
            } else {
                filtered = invoices;
            }

            let totalRevenue = 0, totalProfit = 0, totalQty = 0;
            const topProducts = {};

            filtered.forEach(inv => {
                totalRevenue += inv.totalAmount;
                totalProfit += inv.profit;
                inv.items.forEach(item => {
                    totalQty += item.quantity;
                    topProducts[item.name] = (topProducts[item.name] || 0) + item.quantity;
                });
            });

            document.getElementById("stat-revenue").innerText = formatVND(totalRevenue);
            document.getElementById("stat-profit").innerText = formatVND(totalProfit);
            document.getElementById("stat-orders").innerText = filtered.length + " Đơn";
            document.getElementById("stat-qty-sold").innerText = totalQty + " Máy";

            // Vẽ bảng xếp hạng sản phẩm bán chạy nhất
            const topContainer = document.getElementById("top-products-progress-container");
            topContainer.innerHTML = "";
            const sorted = Object.keys(topProducts).map(name => ({ name, qty: topProducts[name] })).sort((a,b) => b.qty - a.qty).slice(0, 4);
            
            if (sorted.length === 0) {
                topContainer.innerHTML = `<p class="text-slate-400 text-xs font-semibold text-center py-5">Chưa phát sinh số liệu</p>`;
            } else {
                const max = Math.max(...sorted.map(s => s.qty));
                sorted.forEach((item, idx) => {
                    const pct = (item.qty / max) * 100;
                    topContainer.innerHTML += `
                        <div class="space-y-1">
                            <div class="flex justify-between text-xs font-bold text-slate-700">
                                <span class="truncate">${idx+1}. ${item.name}</span>
                                <span class="text-emerald-600 shrink-0">${item.qty} máy</span>
                            </div>
                            <div class="w-full bg-slate-100 h-2 rounded-full overflow-hidden">
                                <div class="bg-emerald-500 h-full rounded-full" style="width: ${pct}%"></div>
                            </div>
                        </div>
                    `;
                });
            }

            // Vẽ biểu đồ
            if (revenueChartObj) revenueChartObj.destroy();
            const ctx = document.getElementById('revenueChart').getContext('2d');
            revenueChartObj = new Chart(ctx, {
                type: 'bar',
                data: {
                    labels: reportPeriod === 'day' ? ['Sáng', 'Trưa', 'Chiều', 'Tối'] : ['Tuần 1', 'Tuần 2', 'Tuần 3', 'Tuần 4'],
                    datasets: [
                        {
                            label: 'Doanh số thực tế',
                            data: reportPeriod === 'day' ? [totalRevenue * 0.3, totalRevenue * 0.4, totalRevenue * 0.2, totalRevenue * 0.1] : [totalRevenue * 0.2, totalRevenue * 0.3, totalRevenue * 0.35, totalRevenue * 0.15],
                            backgroundColor: '#10b981',
                            borderRadius: 6
                        }
                    ]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: { legend: { display: false } },
                    scales: { x: { grid: { display: false } } }
                }
            });
        }

        // ==================== TRỢ LÝ GIỌNG NÓI & NLP CHATBOT KIOTBOT AI ====================
        let recognition;

        function setupSpeechRecognition() {
            const SpeechAPI = window.SpeechRecognition || window.webkitSpeechRecognition;
            if (SpeechAPI) {
                recognition = new SpeechAPI();
                recognition.lang = 'vi-VN';
                recognition.interimResults = false;
                recognition.maxAlternatives = 1;

                recognition.onstart = () => {
                    document.getElementById("voice-indicator").classList.remove("hidden");
                    document.getElementById("btn-voice-chat").classList.add("text-red-500");
                    document.getElementById("chatbot-input").placeholder = "Tôi đang nghe lệnh từ bạn...";
                };

                recognition.onresult = (event) => {
                    const text = event.results[0][0].transcript;
                    document.getElementById("chatbot-input").value = text;
                    setTimeout(() => {
                        handleChatbotSubmit(new Event('submit'));
                    }, 400);
                };

                recognition.onspeechend = () => {
                    recognition.stop();
                    resetVoiceIcon();
                };

                recognition.onerror = () => {
                    recognition.stop();
                    resetVoiceIcon();
                    showToast("Nhận diện giọng nói thất bại hoặc chưa được cấp quyền micro!", "warning");
                };
            }
        }

        function resetVoiceIcon() {
            document.getElementById("voice-indicator").classList.add("hidden");
            document.getElementById("btn-voice-chat").classList.remove("text-red-500");
            document.getElementById("chatbot-input").placeholder = "Hỏi hoặc nói lệnh tại đây...";
        }

        function startVoiceRecognition() {
            if (!recognition) setupSpeechRecognition();
            if (recognition) {
                try { recognition.start(); } catch(e) { recognition.stop(); }
            } else {
                showToast("Trình duyệt không hỗ trợ Web Speech API!", "error");
            }
        }

        function toggleChatbot() {
            const panel = document.getElementById("chatbot-panel");
            if (panel.classList.contains("hidden")) {
                panel.classList.remove("hidden");
                document.getElementById("chatbot-input").focus();
            } else {
                panel.classList.add("hidden");
            }
        }

        function handleChatbotSubmit(event) {
            event.preventDefault();
            const input = document.getElementById("chatbot-input");
            const val = input.value.trim();
            if (!val) return;

            // Đưa tin nhắn người dùng lên màn hình
            appendChatMsg(val, 'user');
            input.value = "";

            // Giả lập hiệu ứng gõ chữ
            const botTypingId = showBotTyping();

            setTimeout(() => {
                document.getElementById(botTypingId).remove();
                const response = parseCommandAndReply(val);
                appendChatMsg(response, 'bot');
            }, 800);
        }

        function showBotTyping() {
            const id = "typing_" + Date.now();
            const container = document.getElementById("chatbot-messages");
            const div = document.createElement("div");
            div.id = id;
            div.className = "flex justify-start items-start space-x-2";
            div.innerHTML = `
                <div class="w-7 h-7 rounded-xl bg-slate-950 flex items-center justify-center text-xs shadow-sm shrink-0">🤖</div>
                <div class="bg-white border border-slate-200 py-3 px-4 rounded-2xl rounded-tl-none shadow-sm flex items-center space-x-1">
                    <span class="typing-dot"></span>
                    <span class="typing-dot"></span>
                    <span class="typing-dot"></span>
                </div>
            `;
            container.appendChild(div);
            container.scrollTop = container.scrollHeight;
            return id;
        }

        function appendChatMsg(text, sender) {
            const container = document.getElementById("chatbot-messages");
            const div = document.createElement("div");
            if (sender === 'user') {
                div.className = "flex justify-end";
                div.innerHTML = `<div class="bg-slate-900 text-white py-2.5 px-4 rounded-2xl rounded-tr-none max-w-[85%] shadow-md">${text}</div>`;
            } else {
                div.className = "flex justify-start items-start space-x-2";
                div.innerHTML = `
                    <div class="w-7 h-7 rounded-xl bg-gradient-to-br from-emerald-400 to-teal-500 flex items-center justify-center text-xs shadow-md shrink-0 text-white font-black">AI</div>
                    <div class="bg-white border border-slate-200 text-slate-700 py-3 px-4 rounded-2xl rounded-tl-none max-w-[85%] shadow-sm leading-relaxed">${text}</div>
                `;
            }
            container.appendChild(div);
            container.scrollTop = container.scrollHeight;
        }

        // NLP BỘ LỌC VÀ CHUYỂN ĐỔI HÀNH ĐỘNG HỆ THỐNG
        function parseCommandAndReply(rawText) {
            const q = rawText.toLowerCase().trim();

            // 1. Chuyển đổi Trang / Tab Hệ Thống
            if (q.includes("báo cáo") || q.includes("doanh thu") || q.includes("lợi nhuận")) {
                if (q.includes("mở") || q.includes("vào") || q.includes("xem")) {
                    switchTab('reports');
                    return "🔄 Đã mở màn hình **Biểu đồ & Báo cáo** thành công.";
                }
            }
            if (q.includes("mở kho") || q.includes("xem kho") || q.includes("vào kho") || q.includes("sản phẩm")) {
                switchTab('products');
                return "🔄 Đã mở màn hình **Danh Mục Kho Hàng** thành công.";
            }
            if (q.includes("mở bán hàng") || q.includes("mở pos") || q.includes("màn hình bán hàng") || q.includes("vào pos")) {
                switchTab('pos');
                return "🔄 Đã chuyển sang màn hình **POS Bán Hàng** thành công.";
            }
            if (q.includes("mở hóa đơn") || q.includes("lịch sử giao dịch")) {
                switchTab('invoices');
                return "🔄 Đã chuyển sang màn hình **Nhật Ký Hóa Đơn**.";
            }

            // 2. Tìm kiếm sản phẩm trực quan trên màn hình POS
            if (q.startsWith("tìm") || q.includes("tìm kiếm")) {
                const searchKey = q.replace("tìm kiếm", "").replace("tìm", "").trim();
                if (searchKey) {
                    switchTab('pos');
                    document.getElementById("pos-search").value = searchKey;
                    renderPosProducts();
                    return `🔍 Đã lọc sản phẩm có từ khóa: **"${searchKey}"** trên kệ hàng POS.`;
                }
            }

            // 3. Thêm nhanh sản phẩm vào giỏ hàng bằng khẩu lệnh
            if (q.startsWith("thêm") || q.startsWith("mua") || q.includes("cho vào giỏ")) {
                let targetName = q.replace("thêm vào giỏ", "").replace("cho vào giỏ", "").replace("thêm", "").replace("mua", "").trim();
                
                // Chuẩn hóa tên viết tắt/phát âm
                if (targetName === "mười lăm" || targetName === "iphone mười lăm" || targetName === "iphone 15") targetName = "iphone 15";
                if (targetName === "samsung" || targetName === "s24") targetName = "samsung";

                const match = products.find(p => p.name.toLowerCase().includes(targetName) && p.stock > 0);
                if (match) {
                    addToCart(match);
                    return `✅ Đã tìm thấy và bỏ **${match.name}** vào giỏ hàng chờ thanh toán của bạn!`;
                } else {
                    return `❌ Thao tác lỗi: Không tìm thấy thiết bị nào có tên tương tự **"${targetName}"** hoặc thiết bị này đã hết hàng tồn kho.`;
                }
            }

            // 4. Hỏi đáp thông tin tồn kho động
            if (q.includes("tồn") || q.includes("còn bao nhiêu")) {
                const totalStock = products.reduce((acc, curr) => acc + curr.stock, 0);
                const lows = products.filter(p => p.stock <= p.minStock);
                
                if (q.includes("hết") || q.includes("cảnh báo")) {
                    if (lows.length === 0) return "Tất cả hàng hoá trong kho đều đạt ngưỡng an toàn tốt!";
                    let res = `⚠️ Có **${lows.length}** sản phẩm dưới định mức:<br>`;
                    lows.forEach(l => res += `- **${l.name}**: Chỉ còn *${l.stock}* máy.<br>`);
                    return res;
                }
                return `📦 Kho hiện đang tồn tổng cộng **${totalStock}** thiết bị thuộc các hãng sản xuất khác nhau.`;
            }

            // 5. Hỏi đáp doanh số / khách hàng
            if (q.includes("khách VIP") || q.includes("vip") || q.includes("chi tiêu")) {
                const sorted = [...customers].sort((a,b) => b.totalSpent - a.totalSpent).filter(c => c.code !== "KHACH_LE");
                if (sorted.length > 0) {
                    return `👑 Khách VIP mua sắm nhiều nhất là **${sorted[0].name}** với tổng số tiền tích lũy đạt: **${formatVND(sorted[0].totalSpent)}**`;
                }
            }

            return "KiotBot chưa khớp được hành động này. Hãy thử nói: *'Mở báo cáo'*, *'Tìm iPhone 15'*, hoặc *'Mua Samsung S24'* xem sao nhé!";
        }

        // KHỞI ĐỘNG HỆ THỐNG TRỰC QUAN
        window.onload = function() {
            currentUser = defaultStaffs[0]; // Auto-Login Admin cho việc tiện trải nghiệm
            document.getElementById("login-screen").classList.add("hidden");
            document.getElementById("header-user-name").innerText = currentUser.name.split(' ').pop();
            document.getElementById("header-user-role").innerText = "Quản lý";
            document.getElementById("sidebar-user-name").innerText = currentUser.name;
            document.getElementById("sidebar-user-role").innerText = "Quản trị cao cấp";
            buildNavigations();
            switchTab('pos');
        }
    </script>
</body>
</html>
