<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>KiotPro V2 - Hệ Thống Quản Lý Bán Hàng Đa Nền Tảng</title>
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
        .no-scrollbar::-webkit-scrollbar { display: none; }
        .no-scrollbar { -ms-overflow-style: none; scrollbar-width: none; }
        
        /* Smooth animations */
        .slide-up { animation: slideUp 0.35s cubic-bezier(0.16, 1, 0.3, 1) forwards; }
        .fade-in { animation: fadeIn 0.2s ease-out forwards; }
        
        @keyframes slideUp {
            from { transform: translateY(100%); opacity: 0; }
            to { transform: translateY(0); opacity: 1; }
        }
        @keyframes fadeIn {
            from { opacity: 0; }
            to { opacity: 1; }
        }

        /* In ấn hóa đơn chuyên nghiệp */
        @media print {
            body * { visibility: hidden; }
            #printable-receipt-content, #printable-receipt-content * { visibility: visible; }
            #printable-receipt-content { 
                position: absolute; left: 0; top: 0; width: 100%; margin: 0; padding: 15px; 
                box-shadow: none !important; border: none !important;
            }
            .no-print { display: none !important; }
        }

        /* Thẩm mỹ Glassmorphism cho phần Header */
        .glass { background: rgba(255, 255, 255, 0.85); backdrop-filter: blur(12px); -webkit-backdrop-filter: blur(12px); }
    </style>
</head>
<body class="text-slate-800 min-h-screen flex flex-col md:flex-row pb-16 md:pb-0 overflow-x-hidden">

    <!-- ==================== SCREEN 1: LOGIN ==================== -->
    <div id="login-screen" class="fixed inset-0 bg-slate-950 z-50 flex items-center justify-center p-4">
        <!-- Nền mờ nghệ thuật -->
        <div class="absolute inset-0 overflow-hidden pointer-events-none">
            <div class="absolute -top-1/4 -right-1/4 w-96 h-96 bg-emerald-600 rounded-full mix-blend-multiply filter blur-[100px] opacity-35"></div>
            <div class="absolute -bottom-1/4 -left-1/4 w-96 h-96 bg-teal-600 rounded-full mix-blend-multiply filter blur-[100px] opacity-35"></div>
        </div>

        <div class="bg-white/10 backdrop-blur-xl w-full max-w-sm rounded-3xl shadow-2xl p-8 space-y-6 border border-white/15 relative z-10 fade-in">
            <div class="text-center space-y-2">
                <div class="inline-flex items-center justify-center space-x-2 bg-white/20 text-white px-5 py-2.5 rounded-2xl font-black text-2xl tracking-wider shadow-inner backdrop-blur-md">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6 text-amber-400" viewBox="0 0 20 20" fill="currentColor"><path d="M11 17a1 1 0 001.447.894l4-2A1 1 0 0017 15V9.236a1 1 0 00-1.447-.894l-4 2a1 1 0 00-.553.894V17zM15.211 6.276a1 1 0 000-1.788l-4.764-2.382a1 1 0 00-.894 0L4.789 4.488a1 1 0 000 1.788l4.764 2.382a1 1 0 00.894 0l4.764-2.382zM4.447 8.342A1 1 0 003 9.236V15a1 1 0 00.553.894l4 2A1 1 0 009 17v-5.764a1 1 0 00-.553-.894l-4-2z" /></svg>
                    <span>Kiot<span class="text-amber-400">Pro</span></span>
                </div>
                <h2 class="text-lg font-bold text-white mt-4">Hệ thống Đăng Nhập</h2>
                <p class="text-xs text-slate-300">Quản lý kho hàng & Bán hàng đa kênh</p>
            </div>

            <form id="login-form" onsubmit="handleLogin(event)" class="space-y-4">
                <div>
                    <input type="text" id="login-username" required placeholder="Tài khoản (admin/nhanvien)" class="w-full px-4 py-3.5 bg-white/10 border border-white/20 text-white placeholder-slate-300 rounded-2xl focus:outline-none focus:ring-2 focus:ring-emerald-400 focus:bg-white/15 transition-all text-sm">
                </div>
                <div>
                    <input type="password" id="login-password" required placeholder="Mật khẩu (123)" class="w-full px-4 py-3.5 bg-white/10 border border-white/20 text-white placeholder-slate-300 rounded-2xl focus:outline-none focus:ring-2 focus:ring-emerald-400 focus:bg-white/15 transition-all text-sm">
                </div>
                <button type="submit" class="w-full bg-gradient-to-r from-emerald-500 to-teal-500 hover:from-emerald-400 hover:to-teal-400 active:scale-[0.98] text-white py-3.5 rounded-2xl font-bold text-sm shadow-lg transition-all flex justify-center items-center mt-2">
                    <span>ĐĂNG NHẬP</span>
                </button>
            </form>
            
            <div class="border-t border-white/10 pt-4 text-center">
                <p class="text-[10px] text-slate-400">Tài khoản dùng thử:</p>
                <p class="text-[10px] font-bold text-slate-300">Admin: <span class="text-emerald-400">admin / 123</span></p>
                <p class="text-[10px] font-bold text-slate-300">Nhân viên: <span class="text-emerald-400">nhanvien / 123</span></p>
            </div>
        </div>
    </div>


    <!-- ==================== SIDEBAR (HIỂN THỊ TRÊN MÁY TÍNH) ==================== -->
    <aside class="hidden md:flex flex-col w-64 bg-slate-900 text-slate-300 shrink-0 border-r border-slate-800 relative z-20">
        <!-- Logo -->
        <div class="p-6 flex items-center space-x-3 border-b border-slate-800">
            <div class="bg-gradient-to-br from-emerald-500 to-teal-600 text-white p-2 rounded-xl shadow-md">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M20 7l-8-4-8 4m16 0l-8 4m8-4v10l-8 4m0-10L4 7m8 4v10M4 7v10l8 4" /></svg>
            </div>
            <div class="flex flex-col">
                <span class="text-base font-black text-white tracking-wider">Kiot<span class="text-amber-400">Pro</span></span>
                <span class="text-[9px] text-emerald-400 font-bold tracking-widest uppercase">Workspace v2</span>
            </div>
        </div>

        <!-- Thông tin User -->
        <div class="p-5 bg-slate-950/40 border-b border-slate-800 flex items-center space-x-3">
            <div class="w-10 h-10 rounded-full bg-emerald-500 text-white flex items-center justify-center font-bold text-sm shadow-inner" id="sidebar-user-avatar">
                U
            </div>
            <div class="flex-1 min-w-0">
                <span class="text-sm font-bold text-white block truncate" id="sidebar-user-name">Đang tải...</span>
                <span class="text-[10px] text-emerald-400 font-semibold block mt-0.5" id="sidebar-user-role">Đang tải...</span>
            </div>
        </div>

        <!-- Navigation Menu -->
        <nav class="flex-1 px-4 py-6 space-y-1 overflow-y-auto no-scrollbar" id="sidebar-navigation">
            <!-- Được tải tự động dựa trên quyền đăng nhập -->
        </nav>

        <!-- Footer hành động -->
        <div class="p-4 border-t border-slate-800 flex flex-col space-y-2">
            <button onclick="openSettingsModal()" class="flex items-center space-x-3 px-4 py-3 rounded-xl text-xs font-bold text-slate-400 hover:text-white hover:bg-slate-800 transition-colors">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10.325 4.317c.426-1.756 2.924-1.756 3.35 0a1.724 1.724 0 002.573 1.066c1.543-.94 3.31.826 2.37 2.37a1.724 1.724 0 001.065 2.572c1.756.426 1.756 2.924 0 3.35a1.724 1.724 0 00-1.066 2.573c.94 1.543-.826 3.31-2.37 2.37a1.724 1.724 0 00-2.572 1.065c-.426 1.756-2.924 1.756-3.35 0a1.724 1.724 0 00-2.573-1.066c-1.543.94-3.31-.826-2.37-2.37a1.724 1.724 0 00-1.065-2.572c-1.756-.426-1.756-2.924 0-3.35a1.724 1.724 0 001.066-2.573c-.94-1.543.826-3.31 2.37-2.37.996.608 2.296.07 2.572-1.065z" /><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z" /></svg>
                <span>Cài Đặt Hệ Thống</span>
            </button>
            <button onclick="handleLogout()" class="flex items-center space-x-3 px-4 py-3 rounded-xl text-xs font-bold text-red-400 hover:text-white hover:bg-red-950/45 transition-colors">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 16l4-4m0 0l-4-4m4 4H7m6 4v1a3 3 0 01-3 3H6a3 3 0 01-3-3V7a3 3 0 013-3h4a3 3 0 013 3v1" /></svg>
                <span>Đăng Xuất</span>
            </button>
        </div>
    </aside>


    <!-- ==================== MAIN CONTENT AREA ==================== -->
    <div class="flex-1 flex flex-col min-w-0">

        <!-- ==================== HEADER MOBILE (ẨN TRÊN MÁY TÍNH) ==================== -->
        <header class="md:hidden glass text-slate-800 px-4 py-3.5 flex justify-between items-center shadow-sm sticky top-0 z-30 shrink-0 border-b border-slate-200">
            <div class="flex items-center space-x-3">
                <div class="bg-gradient-to-br from-emerald-500 to-teal-600 text-white p-2 rounded-xl shadow">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" viewBox="0 0 20 20" fill="currentColor"><path d="M11 17a1 1 0 001.447.894l4-2A1 1 0 0017 15V9.236a1 1 0 00-1.447-.894l-4 2a1 1 0 00-.553.894V17zM15.211 6.276a1 1 0 000-1.788l-4.764-2.382a1 1 0 00-.894 0L4.789 4.488a1 1 0 000 1.788l4.764 2.382a1 1 0 00.894 0l4.764-2.382zM4.447 8.342A1 1 0 003 9.236V15a1 1 0 00.553.894l4 2A1 1 0 009 17v-5.764a1 1 0 00-.553-.894l-4-2z" /></svg>
                </div>
                <div class="flex flex-col">
                    <span class="text-xs font-extrabold text-slate-850 leading-none" id="header-user-name">Đang tải...</span>
                    <span class="text-[9px] text-emerald-600 font-bold mt-0.5" id="header-user-role">Đang tải...</span>
                </div>
            </div>
            
            <div class="flex items-center space-x-2">
                <span class="text-[9px] bg-slate-100 text-slate-600 px-2 py-1 rounded-lg font-bold border border-slate-200">CN Q.1</span>
                <button onclick="openSettingsModal()" class="bg-slate-100 hover:bg-slate-200 text-slate-600 p-2 rounded-xl transition-colors">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10.325 4.317c.426-1.756 2.924-1.756 3.35 0a1.724 1.724 0 002.573 1.066c1.543-.94 3.31.826 2.37 2.37a1.724 1.724 0 001.065 2.572c1.756.426 1.756 2.924 0 3.35a1.724 1.724 0 00-1.066 2.573c.94 1.543-.826 3.31-2.37 2.37a1.724 1.724 0 00-2.572 1.065c-.426 1.756-2.924 1.756-3.35 0a1.724 1.724 0 00-2.573-1.066c-1.543.94-3.31-.826-2.37-2.37a1.724 1.724 0 00-1.065-2.572c-1.756-.426-1.756-2.924 0-3.35a1.724 1.724 0 001.066-2.573c-.94-1.543.826-3.31 2.37-2.37.996.608 2.296.07 2.572-1.065z" /><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z" /></svg>
                </button>
            </div>
        </header>

        <!-- ==================== MAIN CONTENT WRAPPER ==================== -->
        <main class="flex-1 overflow-y-auto px-4 py-5 md:px-8 md:py-6 w-full max-w-7xl mx-auto">

            <!-- ==================== TAB 1: POS (LAYOUT ĐA DẠNG) ==================== -->
            <section id="section-pos" class="tab-content block space-y-4 fade-in">
                <!-- Grid phân chia linh hoạt cho Desktop / Mobile -->
                <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
                    
                    <!-- Nhóm 1: Sản phẩm & Lọc (Rộng 2/3 trên Desktop) -->
                    <div class="lg:col-span-2 space-y-4">
                        <div class="bg-white p-4 rounded-2xl shadow-sm border border-slate-200/70 space-y-3">
                            <div class="flex flex-col sm:flex-row gap-3">
                                <div class="relative flex-1">
                                    <span class="absolute inset-y-0 left-0 flex items-center pl-3.5 text-slate-400">
                                        <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" viewBox="0 0 20 20" fill="currentColor"><path fill-rule="evenodd" d="M8 4a4 4 0 100 8 4 4 0 000-8zM2 8a6 6 0 1110.89 3.476l4.817 4.817a1 1 0 01-1.414 1.414l-4.816-4.816A6 6 0 012 8z" clip-rule="evenodd" /></svg>
                                    </span>
                                    <input type="text" id="pos-search" oninput="renderPosProducts()" placeholder="Nhập tên sản phẩm, mã thiết bị để tìm..." class="w-full pl-10 pr-3 py-2.5 bg-slate-50 border border-slate-200 rounded-xl focus:outline-none focus:ring-2 focus:ring-emerald-500 focus:bg-white text-sm transition-all font-medium">
                                </div>
                                <div class="flex space-x-1.5 overflow-x-auto no-scrollbar py-0.5" id="pos-category-filters">
                                    <!-- Dynamic -->
                                </div>
                            </div>
                        </div>

                        <!-- Lưới sản phẩm POS -->
                        <div class="grid grid-cols-2 sm:grid-cols-3 gap-4" id="pos-products-grid">
                            <!-- Dynamic cards -->
                        </div>
                    </div>

                    <!-- Nhóm 2: Giỏ hàng hiển thị song song (CHỈ HIỂN THỊ TRÊN DESKTOP) -->
                    <div class="hidden lg:block">
                        <div class="bg-white rounded-2xl border border-slate-200/70 shadow-sm sticky top-6 flex flex-col max-h-[calc(100vh-100px)]">
                            <div class="p-5 border-b border-slate-100 flex justify-between items-center">
                                <h3 class="font-black text-base text-slate-800">Giỏ hàng chờ thanh toán</h3>
                                <button onclick="clearCart()" class="text-xs font-bold text-red-500 hover:underline">Xóa hết</button>
                            </div>
                            
                            <!-- Thân giỏ hàng -->
                            <div class="flex-1 overflow-y-auto px-5 py-3 divide-y divide-slate-100 min-h-[250px]" id="pos-cart-desktop-container">
                                <!-- Dynamic -->
                            </div>

                            <!-- Panel tính tiền & Chốt đơn -->
                            <div class="bg-slate-50 p-5 border-t border-slate-100 rounded-b-2xl space-y-4">
                                <div>
                                    <label class="text-[10px] font-bold text-slate-400 uppercase tracking-wider mb-1 block">Khách nhận hàng</label>
                                    <div class="flex items-center space-x-2">
                                        <select id="pos-customer-select-desktop" class="flex-1 py-2.5 px-3 rounded-xl border border-slate-200 bg-white text-xs font-bold text-slate-700 focus:outline-none focus:ring-2 focus:ring-emerald-500"></select>
                                        <button onclick="openAddCustomerModal(true);" class="bg-emerald-50 text-emerald-600 p-2.5 rounded-xl border border-emerald-100 hover:bg-emerald-100 transition-colors">
                                            <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" viewBox="0 0 20 20" fill="currentColor"><path d="M8 9a3 3 0 100-6 3 3 0 000 6zM8 11a6 6 0 016 6H2a6 6 0 016-6zM16 7a1 1 0 10-2 0v1h-1a1 1 0 100 2h1v1a1 1 0 102 0v-1h1a1 1 0 100-2h-1V7z" /></svg>
                                        </button>
                                    </div>
                                </div>

                                <div>
                                    <label class="text-[10px] font-bold text-slate-400 uppercase tracking-wider mb-1 block">Ghi chú</label>
                                    <input type="text" id="pos-note-desktop" placeholder="Địa chỉ ship, lưu ý bảo hành..." class="w-full py-2 px-3 border border-slate-200 rounded-xl bg-white text-xs font-medium focus:outline-none focus:ring-2 focus:ring-emerald-500">
                                </div>

                                <div class="grid grid-cols-2 gap-3">
                                    <div>
                                        <label class="text-[10px] font-bold text-slate-400 uppercase tracking-wider mb-1.5 block">Giảm giá (%)</label>
                                        <input type="number" id="pos-discount-desktop" min="0" max="100" value="0" oninput="syncDiscount('desktop')" class="w-full border border-slate-200 rounded-xl py-1.5 px-3 focus:outline-none text-center font-bold text-slate-800 bg-white">
                                    </div>
                                    <div>
                                        <label class="text-[10px] font-bold text-slate-400 uppercase tracking-wider mb-1.5 block">Thanh toán</label>
                                        <select id="pos-payment-desktop" class="w-full border border-slate-200 rounded-xl py-1.5 px-3 bg-white font-bold text-slate-800 focus:outline-none">
                                            <option value="Chuyển khoản" selected>🏦 Chuyển khoản</option>
                                            <option value="Tiền mặt">💵 Tiền mặt</option>
                                            <option value="Quẹt thẻ">💳 Quẹt thẻ</option>
                                        </select>
                                    </div>
                                </div>

                                <div class="flex justify-between items-end pt-3 border-t border-dashed border-slate-200">
                                    <div>
                                        <span class="text-[10px] text-slate-400 font-bold block">Tạm tính</span>
                                        <span id="pos-subtotal-desktop" class="text-xs text-slate-400 line-through">0 ₫</span>
                                    </div>
                                    <div class="text-right">
                                        <span class="text-[9px] text-emerald-600 font-bold block uppercase tracking-wide">Cần thanh toán</span>
                                        <span id="pos-total-amount-desktop" class="text-xl font-black text-slate-900 tracking-tight">0 ₫</span>
                                    </div>
                                </div>

                                <button onclick="checkoutCartDirect('desktop')" class="w-full bg-emerald-600 hover:bg-emerald-700 text-white font-bold py-3 px-4 rounded-xl shadow-md transition-all flex justify-center items-center space-x-2">
                                    <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" viewBox="0 0 20 20" fill="currentColor"><path fill-rule="evenodd" d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z" clip-rule="evenodd" /></svg>
                                    <span class="text-xs uppercase tracking-wide">Thanh toán (F9)</span>
                                </button>
                            </div>
                        </div>
                    </div>

                </div>
            </section>

            <!-- ==================== TAB 2: PRODUCTS (DANH SÁCH BẢNG) ==================== -->
            <section id="section-products" class="tab-content hidden space-y-4 fade-in">
                <div class="flex justify-between items-center">
                    <div>
                        <h2 class="text-xl font-black text-slate-900 tracking-tight">Quản lý kho thiết bị</h2>
                        <p class="text-xs font-semibold text-slate-500">Cập nhật, sửa đổi thông tin định mức kho hàng</p>
                    </div>
                    <button onclick="openProductModal(false)" id="btn-add-product-main" class="bg-slate-900 hover:bg-slate-800 text-white font-bold px-4 py-2.5 rounded-xl shadow transition-all flex items-center space-x-2">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" viewBox="0 0 20 20" fill="currentColor"><path fill-rule="evenodd" d="M10 5a1 1 0 011 1v3h3a1 1 0 110 2h-3v3a1 1 0 11-2 0v-3H6a1 1 0 110-2h3V6a1 1 0 011-1z" clip-rule="evenodd" /></svg>
                        <span class="text-xs">Thêm Sản Phẩm</span>
                    </button>
                </div>

                <!-- Tìm kiếm và chọn lọc dữ liệu -->
                <div class="bg-white p-4 rounded-2xl shadow-sm border border-slate-200/70 flex flex-col sm:flex-row gap-3">
                    <input type="text" id="product-list-search" oninput="renderProductTable()" placeholder="Nhập tên điện thoại, mã vạch để tìm kiếm..." class="flex-1 px-4 py-2.5 bg-slate-50 border border-slate-200 rounded-xl text-sm focus:outline-none focus:ring-2 focus:ring-emerald-500 font-medium">
                    <div class="grid grid-cols-2 gap-2 sm:w-80">
                        <select id="product-list-category" onchange="renderProductTable()" class="py-2 px-3 border border-slate-200 rounded-xl text-xs font-bold bg-white focus:outline-none">
                            <option value="all">Tất cả danh mục</option>
                            <option value="iPhone">Apple (iPhone)</option>
                            <option value="Samsung">Samsung</option>
                            <option value="Oppo">Oppo</option>
                            <option value="Xiaomi">Xiaomi</option>
                            <option value="Phụ kiện">Phụ kiện</option>
                        </select>
                        <select id="product-list-status" onchange="renderProductTable()" class="py-2 px-3 border border-slate-200 rounded-xl text-xs font-bold bg-white focus:outline-none">
                            <option value="all">Tất cả tồn kho</option>
                            <option value="low-stock">Sắp hết hàng</option>
                            <option value="out-of-stock">Đã hết hàng</option>
                        </select>
                    </div>
                </div>

                <!-- Bảng sản phẩm trên Desktop / Thẻ trên Mobile -->
                <div class="bg-white rounded-2xl border border-slate-200/70 shadow-sm overflow-hidden">
                    <div class="hidden md:block overflow-x-auto">
                        <table class="w-full text-left border-collapse">
                            <thead>
                                <tr class="bg-slate-50 text-slate-500 text-[10px] font-black uppercase tracking-wider border-b border-slate-100">
                                    <th class="py-4 px-6">Mã máy</th>
                                    <th class="py-4 px-6">Tên sản phẩm</th>
                                    <th class="py-4 px-6">Phân loại</th>
                                    <th class="py-4 px-6 text-right">Giá nhập</th>
                                    <th class="py-4 px-6 text-right">Giá bán lẻ</th>
                                    <th class="py-4 px-6 text-center">Tồn hiện tại</th>
                                    <th class="py-4 px-6 text-center">Trạng thái</th>
                                    <th class="py-4 px-6 text-center">Hành động</th>
                                </tr>
                            </thead>
                            <tbody class="divide-y divide-slate-100 text-xs font-bold text-slate-700" id="product-table-body">
                                <!-- Dynamic rows -->
                            </tbody>
                        </table>
                    </div>

                    <!-- Layout thẻ gọn gàng trên Mobile -->
                    <div class="md:hidden p-4 space-y-3" id="product-cards-container">
                        <!-- Dynamic -->
                    </div>
                </div>
            </section>

            <!-- ==================== TAB 3: INVOICES (ĐƠN HÀNG) ==================== -->
            <section id="section-invoices" class="tab-content hidden space-y-4 fade-in">
                <div>
                    <h2 class="text-xl font-black text-slate-900 tracking-tight">Nhật ký bán hàng</h2>
                    <p class="text-xs font-semibold text-slate-500">Tra cứu nhanh hóa đơn, biên nhận và thời gian bán lẻ</p>
                </div>

                <div class="bg-white p-4 rounded-2xl shadow-sm border border-slate-200/70">
                    <input type="text" id="invoice-search" oninput="renderInvoiceTable()" placeholder="Nhập mã hóa đơn hoặc tên khách hàng..." class="w-full px-4 py-2.5 bg-slate-50 border border-slate-200 rounded-xl text-sm focus:outline-none focus:ring-2 focus:ring-emerald-500 font-medium">
                </div>

                <!-- Khung hiển thị bảng/thẻ -->
                <div class="bg-white rounded-2xl border border-slate-200/70 shadow-sm overflow-hidden">
                    <div class="hidden md:block overflow-x-auto">
                        <table class="w-full text-left border-collapse">
                            <thead>
                                <tr class="bg-slate-50 text-slate-500 text-[10px] font-black uppercase tracking-wider border-b border-slate-100">
                                    <th class="py-4 px-6">Mã HD</th>
                                    <th class="py-4 px-6">Thời gian</th>
                                    <th class="py-4 px-6">Khách hàng</th>
                                    <th class="py-4 px-6 text-right">Tổng thanh toán</th>
                                    <th class="py-4 px-6 text-center">Phương thức</th>
                                    <th class="py-4 px-6">Thu ngân</th>
                                    <th class="py-4 px-6 text-center">Chi tiết</th>
                                </tr>
                            </thead>
                            <tbody class="divide-y divide-slate-100 text-xs font-bold text-slate-700" id="invoice-table-body">
                                <!-- Dynamic rows -->
                            </tbody>
                        </table>
                    </div>

                    <div class="md:hidden p-4 space-y-3" id="invoice-cards-container">
                        <!-- Dynamic -->
                    </div>
                </div>
            </section>

            <!-- ==================== TAB 4: CUSTOMERS ==================== -->
            <section id="section-customers" class="tab-content hidden space-y-4 fade-in">
                <div class="flex justify-between items-center">
                    <div>
                        <h2 class="text-xl font-black text-slate-900 tracking-tight">Đối tác khách hàng</h2>
                        <p class="text-xs font-semibold text-slate-500">Thông tin liên hệ và doanh số tích lũy</p>
                    </div>
                    <button onclick="openAddCustomerModal(false)" class="bg-emerald-600 hover:bg-emerald-700 text-white font-bold px-4 py-2.5 rounded-xl shadow flex items-center space-x-2">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" viewBox="0 0 20 20" fill="currentColor"><path d="M8 9a3 3 0 100-6 3 3 0 000 6zM8 11a6 6 0 016 6H2a6 6 0 016-6zM16 7a1 1 0 10-2 0v1h-1a1 1 0 100 2h1v1a1 1 0 102 0v-1h1a1 1 0 100-2h-1V7z" /></svg>
                        <span class="text-xs">Thêm khách hàng</span>
                    </button>
                </div>

                <div class="bg-white p-4 rounded-2xl border border-slate-200/70 shadow-sm">
                    <input type="text" id="customer-search" oninput="renderCustomerTable()" placeholder="Nhập tên hoặc số điện thoại khách hàng cần tìm..." class="w-full px-4 py-2.5 bg-slate-50 border border-slate-200 rounded-xl text-sm focus:outline-none focus:ring-2 focus:ring-emerald-500 font-medium">
                </div>

                <div class="bg-white rounded-2xl border border-slate-200/70 shadow-sm overflow-hidden">
                    <div class="hidden md:block overflow-x-auto">
                        <table class="w-full text-left border-collapse">
                            <thead>
                                <tr class="bg-slate-50 text-slate-500 text-[10px] font-black uppercase tracking-wider border-b border-slate-100">
                                    <th class="py-4 px-6">Mã KH</th>
                                    <th class="py-4 px-6">Tên khách hàng</th>
                                    <th class="py-4 px-6">Số điện thoại</th>
                                    <th class="py-4 px-6">Địa chỉ</th>
                                    <th class="py-4 px-6 text-right">Chi tiêu lũy kế</th>
                                    <th class="py-4 px-6 text-center">Hành động</th>
                                </tr>
                            </thead>
                            <tbody class="divide-y divide-slate-100 text-xs font-bold text-slate-700" id="customer-table-body">
                                <!-- Dynamic -->
                            </tbody>
                        </table>
                    </div>

                    <div class="md:hidden p-4 space-y-3" id="customer-cards-container">
                        <!-- Dynamic -->
                    </div>
                </div>
            </section>

            <!-- ==================== TAB 5: STAFFS ==================== -->
            <section id="section-staffs" class="tab-content hidden space-y-4 fade-in">
                <div class="flex justify-between items-center">
                    <div>
                        <h2 class="text-xl font-black text-slate-900 tracking-tight">Danh sách nhân sự</h2>
                        <p class="text-xs font-semibold text-slate-500">Phân quyền đăng nhập, mật khẩu truy cập hệ thống</p>
                    </div>
                    <button onclick="openStaffModal(false)" class="bg-blue-600 hover:bg-blue-700 text-white font-bold px-4 py-2.5 rounded-xl shadow flex items-center space-x-2">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" viewBox="0 0 20 20" fill="currentColor"><path fill-rule="evenodd" d="M10 9a3 3 0 100-6 3 3 0 000 6zm-7 9a7 7 0 1114 0H3z" clip-rule="evenodd" /></svg>
                        <span class="text-xs">Thêm nhân viên</span>
                    </button>
                </div>

                <div class="bg-white rounded-2xl border border-slate-200/70 shadow-sm overflow-hidden">
                    <div class="hidden md:block overflow-x-auto">
                        <table class="w-full text-left border-collapse">
                            <thead>
                                <tr class="bg-slate-50 text-slate-500 text-[10px] font-black uppercase tracking-wider border-b border-slate-100">
                                    <th class="py-4 px-6">Mã NV</th>
                                    <th class="py-4 px-6">Họ và tên</th>
                                    <th class="py-4 px-6">Tài khoản</th>
                                    <th class="py-4 px-6">Quyền hạn</th>
                                    <th class="py-4 px-6 text-center">Hành động</th>
                                </tr>
                            </thead>
                            <tbody class="divide-y divide-slate-100 text-xs font-bold text-slate-700" id="staff-table-body">
                                <!-- Dynamic -->
                            </tbody>
                        </table>
                    </div>

                    <div class="md:hidden p-4 space-y-3" id="staff-cards-container">
                        <!-- Dynamic -->
                    </div>
                </div>
            </section>

            <!-- ==================== TAB 6: REPORTS ==================== -->
            <section id="section-reports" class="tab-content hidden space-y-5 fade-in">
                <div>
                    <h2 class="text-xl font-black text-slate-900 tracking-tight">Báo cáo hiệu quả kinh doanh</h2>
                    <p class="text-xs font-semibold text-slate-500">Tổng quát doanh số, tiền lời và sản phẩm bán chạy nhất</p>
                </div>

                <!-- Lọc thời gian -->
                <div class="bg-slate-200/60 p-1 rounded-2xl grid grid-cols-4 gap-1 text-center max-w-md shadow-inner">
                    <button onclick="setReportPeriod('day')" id="btn-period-day" class="py-2.5 text-xs font-bold rounded-xl transition-all bg-white text-emerald-700 shadow-sm">Hôm nay</button>
                    <button onclick="setReportPeriod('week')" id="btn-period-week" class="py-2.5 text-xs font-bold rounded-xl transition-all text-slate-600">Tuần</button>
                    <button onclick="setReportPeriod('month')" id="btn-period-month" class="py-2.5 text-xs font-bold rounded-xl transition-all text-slate-600">Tháng</button>
                    <button onclick="setReportPeriod('year')" id="btn-period-year" class="py-2.5 text-xs font-bold rounded-xl transition-all text-slate-600">Năm</button>
                </div>

                <!-- Thẻ thống kê cốt lõi -->
                <div class="grid grid-cols-2 lg:grid-cols-4 gap-4">
                    <div class="bg-gradient-to-br from-emerald-500 to-teal-600 text-white p-5 rounded-3xl shadow-lg space-y-1">
                        <span class="text-[10px] font-semibold text-emerald-50 block uppercase tracking-wider opacity-90">Tổng Doanh Thu</span>
                        <span class="text-xl lg:text-2xl font-black tracking-tight block" id="stat-revenue">0 ₫</span>
                    </div>
                    <div class="bg-white p-5 rounded-3xl border border-slate-200/70 shadow-sm space-y-1">
                        <span class="text-[10px] font-bold text-slate-400 block uppercase tracking-wider">Lợi Nhuận Gộp</span>
                        <span class="text-xl lg:text-2xl font-black text-blue-600 tracking-tight block" id="stat-profit">0 ₫</span>
                    </div>
                    <div class="bg-white p-5 rounded-3xl border border-slate-200/70 shadow-sm space-y-1">
                        <span class="text-[10px] font-bold text-slate-400 block uppercase tracking-wider">Tổng Đơn Hàng</span>
                        <span class="text-xl lg:text-2xl font-black text-slate-800 tracking-tight block" id="stat-orders">0 Đơn</span>
                    </div>
                    <div class="bg-white p-5 rounded-3xl border border-slate-200/70 shadow-sm space-y-1">
                        <span class="text-[10px] font-bold text-slate-400 block uppercase tracking-wider">Thiết Bị Đã Bán</span>
                        <span class="text-xl lg:text-2xl font-black text-amber-500 tracking-tight block" id="stat-qty-sold">0 Máy</span>
                    </div>
                </div>

                <!-- Biểu đồ và Top sản phẩm bán chạy -->
                <div class="grid grid-cols-1 lg:grid-cols-3 gap-6">
                    <div class="lg:col-span-2 bg-white p-5 rounded-3xl border border-slate-200/70 shadow-sm">
                        <div class="flex justify-between items-center mb-4">
                            <h3 class="text-xs font-black text-slate-800 uppercase tracking-wide">Đường xu thế bán lẻ</h3>
                            <span class="text-[10px] bg-slate-100 text-slate-600 px-2.5 py-1 rounded-lg font-bold" id="chart-period-lbl">Hôm nay</span>
                        </div>
                        <div class="h-64 w-full relative">
                            <canvas id="revenueChart"></canvas>
                        </div>
                    </div>

                    <div class="bg-white p-5 rounded-3xl border border-slate-200/70 shadow-sm space-y-4">
                        <div class="flex justify-between items-center pb-3 border-b border-slate-100">
                            <h3 class="text-xs font-black text-slate-800 uppercase tracking-wide">Top 3 Bán Chạy Nhất</h3>
                            <span class="text-[10px] text-slate-400 font-bold">Số máy đã bán</span>
                        </div>
                        <div class="space-y-4" id="top-products-progress-container">
                            <!-- Loaded Dynamically -->
                        </div>
                    </div>
                </div>
            </section>

        </main>
    </div>

    <!-- ==================== FLOATING CART BAR (CHỈ HIỂN THỊ TRÊN MOBILE) ==================== -->
    <div id="floating-cart-bar" class="fixed bottom-20 left-4 right-4 z-40 lg:hidden hidden fade-in">
        <button onclick="toggleCartSheet(true)" class="w-full bg-slate-900 hover:bg-slate-800 active:scale-95 text-white py-4 px-5 rounded-2xl shadow-xl flex justify-between items-center transition-all">
            <div class="flex items-center space-x-3">
                <div class="bg-emerald-500 text-white w-7 h-7 rounded-full flex items-center justify-center text-xs font-black shadow" id="cart-floating-count">0</div>
                <span class="text-sm font-bold tracking-wide">Xem giỏ hàng</span>
            </div>
            <div class="flex items-center space-x-1.5 font-black text-emerald-400">
                <span id="cart-floating-total" class="text-sm">0 ₫</span>
                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor"><path fill-rule="evenodd" d="M7.293 14.707a1 1 0 010-1.414L10.586 10 7.293 6.707a1 1 0 011.414-1.414l4 4a1 1 0 010 1.414l-4 4a1 1 0 01-1.414 0z" clip-rule="evenodd" /></svg>
            </div>
        </button>
    </div>

    <!-- ==================== BOTTOM NAVIGATION (CHỈ HIỂN THỊ TRÊN MOBILE) ==================== -->
    <nav class="fixed bottom-0 left-0 right-0 glass border-t border-slate-200 shadow-md px-2 py-1.5 flex justify-around items-center z-45 md:hidden max-w-md mx-auto" id="bottom-navigation-bar">
        <!-- Default buttons populated dynamically -->
    </nav>


    <!-- ==================== CART BOTTOM SHEET (CHỈ HIỂN THỊ TRÊN MOBILE) ==================== -->
    <div id="cart-bottom-sheet" class="fixed inset-0 bg-slate-900/50 backdrop-blur-sm z-50 flex flex-col justify-end lg:hidden hidden">
        <div class="flex-1" onclick="toggleCartSheet(false)"></div>
        <div class="bg-white rounded-t-[32px] max-h-[85vh] flex flex-col shadow-2xl w-full max-w-md mx-auto slide-up">
            <div class="w-12 h-1.5 bg-slate-200 rounded-full mx-auto my-3 shrink-0"></div>
            
            <div class="px-5 pb-3 border-b border-slate-100 flex justify-between items-center shrink-0">
                <h3 class="font-black text-base text-slate-800">
                    Giỏ hàng di động (<span id="pos-cart-count" class="text-emerald-600">0</span>)
                </h3>
                <button onclick="clearCart()" class="text-xs font-bold text-red-500 bg-red-50 px-3 py-1.5 rounded-lg">Dọn sạch</button>
            </div>

            <div class="flex-1 overflow-y-auto px-5 py-2 divide-y divide-slate-100" id="pos-cart-mobile-container">
                <!-- Dynamically loaded -->
            </div>

            <div class="bg-slate-50 p-5 border-t border-slate-200 space-y-4 shrink-0 rounded-t-3xl">
                <div>
                    <label class="block text-[10px] font-bold text-slate-400 uppercase tracking-wide mb-1.5">Khách nhận hàng</label>
                    <div class="flex items-center space-x-2">
                        <select id="pos-customer-select-mobile" class="flex-1 py-2.5 px-3 rounded-xl border border-slate-200 bg-white text-xs font-bold text-slate-700 focus:outline-none focus:ring-2 focus:ring-emerald-500"></select>
                        <button onclick="toggleCartSheet(false); openAddCustomerModal(true);" class="bg-white text-emerald-600 p-2.5 rounded-xl border border-emerald-200">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" viewBox="0 0 20 20" fill="currentColor"><path d="M8 9a3 3 0 100-6 3 3 0 000 6zM8 11a6 6 0 016 6H2a6 6 0 016-6zM16 7a1 1 0 10-2 0v1h-1a1 1 0 100 2h1v1a1 1 0 102 0v-1h1a1 1 0 100-2h-1V7z" /></svg>
                        </button>
                    </div>
                </div>

                <div>
                    <label class="block text-[10px] font-bold text-slate-400 uppercase tracking-wide mb-1.5">Ghi chú</label>
                    <input type="text" id="pos-note-mobile" placeholder="Ghi chú người ship, bảo hành..." class="w-full py-2 px-3 border border-slate-200 rounded-xl bg-white text-xs font-medium focus:outline-none">
                </div>

                <div class="grid grid-cols-2 gap-3">
                    <div>
                        <label class="block text-[10px] font-bold text-slate-400 uppercase tracking-wide mb-1.5">Giảm giá (%)</label>
                        <input type="number" id="pos-discount-mobile" min="0" max="100" value="0" oninput="syncDiscount('mobile')" class="w-full border border-slate-200 rounded-xl py-2 px-3 focus:outline-none text-center font-bold text-slate-800 bg-white">
                    </div>
                    <div>
                        <label class="block text-[10px] font-bold text-slate-400 uppercase tracking-wide mb-1.5">Hình thức</label>
                        <select id="pos-payment-mobile" class="w-full border border-slate-200 rounded-xl py-2 px-3 bg-white font-bold text-slate-800">
                            <option value="Chuyển khoản" selected>🏦 Chuyển khoản</option>
                            <option value="Tiền mặt">💵 Tiền mặt</option>
                            <option value="Quẹt thẻ">💳 Quẹt thẻ</option>
                        </select>
                    </div>
                </div>

                <div class="flex justify-between items-end pt-3 border-t border-dashed border-slate-200">
                    <div>
                        <span class="text-[10px] text-slate-400 font-bold block">Tạm tính</span>
                        <span id="pos-subtotal-mobile" class="text-xs text-slate-400 line-through">0 ₫</span>
                    </div>
                    <div class="text-right">
                        <span class="text-[9px] text-emerald-600 font-bold block uppercase tracking-wide">Cần thanh toán</span>
                        <span id="pos-total-amount-mobile" class="text-xl font-black text-slate-900">0 ₫</span>
                    </div>
                </div>

                <button onclick="checkoutCartDirect('mobile')" class="w-full bg-emerald-600 hover:bg-emerald-700 text-white font-bold py-3 px-4 rounded-xl shadow-lg transition-all flex justify-center items-center space-x-2">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor"><path fill-rule="evenodd" d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z" clip-rule="evenodd" /></svg>
                    <span>Thanh Toán & Xuất Phiếu</span>
                </button>
            </div>
        </div>
    </div>


    <!-- ==================== MODALS (HỆ THỐNG BIỂU MẪU) ==================== -->
    <!-- 1. PRODUCT MODAL -->
    <div id="modal-product" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-50 flex items-center justify-center p-4 hidden">
        <div class="bg-white w-full max-w-md rounded-2xl shadow-2xl overflow-hidden flex flex-col max-h-[90vh] fade-in">
            <div class="bg-slate-900 text-white px-5 py-4 flex justify-between items-center shrink-0">
                <h3 class="font-bold text-sm tracking-wide" id="product-modal-title">Sản Phẩm</h3>
                <button onclick="closeProductModal()" class="text-slate-400 hover:text-white">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" /></svg>
                </button>
            </div>
            <form id="product-form" onsubmit="saveProduct(event)" class="p-5 space-y-4 overflow-y-auto">
                <input type="hidden" id="edit-product-id">
                <div class="grid grid-cols-2 gap-3">
                    <div>
                        <label class="block text-xs font-bold text-slate-500 mb-1">Mã sản phẩm *</label>
                        <input type="text" id="prod-code" required class="w-full p-2.5 border border-slate-200 rounded-xl text-xs font-bold focus:outline-none">
                    </div>
                    <div>
                        <label class="block text-xs font-bold text-slate-500 mb-1">Nhóm máy *</label>
                        <select id="prod-category" required class="w-full p-2.5 border border-slate-200 rounded-xl text-xs font-bold bg-white focus:outline-none">
                            <option value="iPhone">Apple (iPhone)</option>
                            <option value="Samsung">Samsung</option>
                            <option value="Oppo">Oppo</option>
                            <option value="Xiaomi">Xiaomi</option>
                            <option value="Phụ kiện">Phụ kiện</option>
                        </select>
                    </div>
                </div>
                <div>
                    <label class="block text-xs font-bold text-slate-500 mb-1">Tên thiết bị *</label>
                    <input type="text" id="prod-name" required placeholder="Ví dụ: iPhone 15 Pro Max 256GB" class="w-full p-2.5 border border-slate-200 rounded-xl text-xs font-bold focus:outline-none">
                </div>
                <div class="grid grid-cols-2 gap-3">
                    <div>
                        <label class="block text-xs font-bold text-slate-500 mb-1">Giá vốn (Nhập) *</label>
                        <input type="number" id="prod-cost" required min="0" class="w-full p-2.5 border border-slate-200 rounded-xl text-xs font-bold focus:outline-none">
                    </div>
                    <div>
                        <label class="block text-xs font-bold text-slate-500 mb-1">Giá bán lẻ *</label>
                        <input type="number" id="prod-selling" required min="0" class="w-full p-2.5 border border-slate-200 rounded-xl text-xs font-bold focus:outline-none">
                    </div>
                </div>
                <div class="grid grid-cols-2 gap-3">
                    <div>
                        <label class="block text-xs font-bold text-slate-500 mb-1">Số lượng tồn kho *</label>
                        <input type="number" id="prod-stock" required min="0" class="w-full p-2.5 border border-slate-200 rounded-xl text-xs font-bold focus:outline-none">
                    </div>
                    <div>
                        <label class="block text-xs font-bold text-slate-500 mb-1">Báo động tối thiểu *</label>
                        <input type="number" id="prod-minStock" required min="1" class="w-full p-2.5 border border-slate-200 rounded-xl text-xs font-bold focus:outline-none">
                    </div>
                </div>
                <div class="pt-2 flex space-x-2">
                    <button type="button" onclick="closeProductModal()" class="w-1/3 bg-slate-100 text-slate-700 py-3 rounded-xl font-bold text-xs">Hủy</button>
                    <button type="submit" class="w-2/3 bg-emerald-600 text-white py-3 rounded-xl font-bold text-xs shadow-md">Lưu sản phẩm</button>
                </div>
            </form>
        </div>
    </div>

    <!-- 2. CUSTOMER MODAL -->
    <div id="modal-customer" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-50 flex items-center justify-center p-4 hidden">
        <div class="bg-white w-full max-w-sm rounded-2xl shadow-2xl overflow-hidden flex flex-col fade-in">
            <div class="bg-emerald-600 text-white px-5 py-4 flex justify-between items-center">
                <h3 class="font-bold text-sm tracking-wide" id="customer-modal-title">Khách Hàng</h3>
                <button onclick="closeCustomerModal()" class="text-white hover:text-slate-200">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" /></svg>
                </button>
            </div>
            <form id="customer-form" onsubmit="saveCustomer(event)" class="p-5 space-y-4">
                <input type="hidden" id="edit-customer-id">
                <input type="hidden" id="is-pos-adding" value="false">
                <div>
                    <label class="block text-xs font-bold text-slate-500 mb-1">Mã khách hàng *</label>
                    <input type="text" id="cust-code" required class="w-full p-2.5 border border-slate-200 rounded-xl text-xs font-bold focus:outline-none">
                </div>
                <div>
                    <label class="block text-xs font-bold text-slate-500 mb-1">Họ tên *</label>
                    <input type="text" id="cust-name" required class="w-full p-2.5 border border-slate-200 rounded-xl text-xs font-bold focus:outline-none">
                </div>
                <div>
                    <label class="block text-xs font-bold text-slate-500 mb-1">Điện thoại *</label>
                    <input type="tel" id="cust-phone" required class="w-full p-2.5 border border-slate-200 rounded-xl text-xs font-bold focus:outline-none">
                </div>
                <div>
                    <label class="block text-xs font-bold text-slate-500 mb-1">Địa chỉ</label>
                    <input type="text" id="cust-address" class="w-full p-2.5 border border-slate-200 rounded-xl text-xs font-bold focus:outline-none">
                </div>
                <div class="pt-2 flex space-x-2">
                    <button type="button" onclick="closeCustomerModal()" class="w-1/3 bg-slate-100 text-slate-700 py-3 rounded-xl font-bold text-xs">Hủy</button>
                    <button type="submit" class="w-2/3 bg-emerald-600 text-white py-3 rounded-xl font-bold text-xs shadow-md">Lưu thông tin</button>
                </div>
            </form>
        </div>
    </div>

    <!-- 3. STAFF MODAL -->
    <div id="modal-staff" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-50 flex items-center justify-center p-4 hidden">
        <div class="bg-white w-full max-w-sm rounded-2xl shadow-2xl overflow-hidden flex flex-col fade-in">
            <div class="bg-blue-600 text-white px-5 py-4 flex justify-between items-center">
                <h3 class="font-bold text-sm tracking-wide" id="staff-modal-title">Nhân sự</h3>
                <button onclick="closeStaffModal()" class="text-white hover:text-slate-200">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" /></svg>
                </button>
            </div>
            <form id="staff-form" onsubmit="saveStaff(event)" class="p-5 space-y-4">
                <input type="hidden" id="edit-staff-id">
                <div class="grid grid-cols-2 gap-3">
                    <div>
                        <label class="block text-xs font-bold text-slate-500 mb-1">Mã nhân viên *</label>
                        <input type="text" id="staff-code" required class="w-full p-2.5 border border-slate-200 rounded-xl text-xs font-bold focus:outline-none">
                    </div>
                    <div>
                        <label class="block text-xs font-bold text-slate-500 mb-1">Quyền hạn *</label>
                        <select id="staff-role" required class="w-full p-2.5 border border-slate-200 rounded-xl text-xs font-bold bg-white focus:outline-none">
                            <option value="Staff">Nhân viên</option>
                            <option value="Admin">Quản lý</option>
                        </select>
                    </div>
                </div>
                <div>
                    <label class="block text-xs font-bold text-slate-500 mb-1">Họ tên *</label>
                    <input type="text" id="staff-name" required class="w-full p-2.5 border border-slate-200 rounded-xl text-xs font-bold focus:outline-none">
                </div>
                <div class="grid grid-cols-2 gap-3">
                    <div>
                        <label class="block text-xs font-bold text-slate-500 mb-1">Tài khoản đăng nhập *</label>
                        <input type="text" id="staff-username" required class="w-full p-2.5 border border-slate-200 rounded-xl text-xs font-bold focus:outline-none">
                    </div>
                    <div>
                        <label class="block text-xs font-bold text-slate-500 mb-1">Mật khẩu *</label>
                        <input type="password" id="staff-password" required class="w-full p-2.5 border border-slate-200 rounded-xl text-xs font-bold focus:outline-none">
                    </div>
                </div>
                <div class="pt-2 flex space-x-2">
                    <button type="button" onclick="closeStaffModal()" class="w-1/3 bg-slate-100 text-slate-700 py-3 rounded-xl font-bold text-xs">Hủy</button>
                    <button type="submit" class="w-2/3 bg-blue-600 text-white py-3 rounded-xl font-bold text-xs shadow-md">Lưu</button>
                </div>
            </form>
        </div>
    </div>

    <!-- 4. RECEIPT VIEWER (HOÁ ĐƠN IN) -->
    <div id="modal-receipt" class="fixed inset-0 bg-slate-900/80 backdrop-blur-sm z-50 flex items-center justify-center p-4 hidden fade-in">
        <div class="bg-white w-full max-w-sm rounded-2xl shadow-2xl overflow-hidden flex flex-col max-h-[95vh]">
            <div class="bg-slate-900 text-white px-5 py-4 flex justify-between items-center shrink-0 no-print">
                <h3 class="font-bold text-xs tracking-wide">
                    Hóa Đơn #<span id="receipt-invoice-code">HD000</span>
                </h3>
                <button onclick="closeReceiptModal()" class="text-slate-400 hover:text-white">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" /></svg>
                </button>
            </div>

            <div class="flex-1 overflow-y-auto p-5 bg-slate-50">
                <div class="bg-white border border-slate-200 rounded-2xl p-5 shadow-sm space-y-4 text-xs relative" id="printable-receipt-content">
                    
                    <!-- Watermark Logo -->
                    <div class="absolute inset-0 flex items-center justify-center opacity-[0.03] pointer-events-none">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-32 w-32" viewBox="0 0 20 20" fill="currentColor"><path d="M11 17a1 1 0 001.447.894l4-2A1 1 0 0017 15V9.236a1 1 0 00-1.447-.894l-4 2a1 1 0 00-.553.894V17zM15.211 6.276a1 1 0 000-1.788l-4.764-2.382a1 1 0 00-.894 0L4.789 4.488a1 1 0 000 1.788l4.764 2.382a1 1 0 00.894 0l4.764-2.382zM4.447 8.342A1 1 0 003 9.236V15a1 1 0 00.553.894l4 2A1 1 0 009 17v-5.764a1 1 0 00-.553-.894l-4-2z" /></svg>
                    </div>

                    <div class="text-center space-y-1 relative z-10">
                        <h4 class="font-black text-sm text-slate-800 tracking-tight">KIOTPRO MOBILE</h4>
                        <p class="text-[10px] text-slate-500 font-medium">120 Hai Bà Trưng, Quận 1, TP. HCM</p>
                        <p class="text-[10px] text-slate-500 font-medium">SĐT hỗ trợ: 0909.123.456</p>
                    </div>

                    <div class="border-t border-dashed border-slate-300"></div>

                    <div class="space-y-1 text-slate-600 text-[10px] font-medium relative z-10">
                        <div class="flex justify-between">
                            <span>Mã hoá đơn:</span><span class="font-bold text-slate-900" id="receipt-id">HD0000</span>
                        </div>
                        <div class="flex justify-between">
                            <span>Thời gian:</span><span id="receipt-date">--:--</span>
                        </div>
                        <div class="flex justify-between">
                            <span>Khách hàng:</span><span class="font-bold text-slate-900" id="receipt-customer-name">Khách lẻ</span>
                        </div>
                        <div class="flex justify-between">
                            <span>Nhân viên:</span><span class="font-bold text-slate-900" id="receipt-seller">Admin</span>
                        </div>
                    </div>

                    <div class="border-t border-dashed border-slate-300"></div>

                    <div class="space-y-2 relative z-10" id="receipt-items-container">
                        <!-- Loaded dynamically -->
                    </div>

                    <div class="border-t border-dashed border-slate-300"></div>

                    <div class="space-y-1.5 text-[10px] font-semibold relative z-10">
                        <div class="flex justify-between text-slate-500">
                            <span>Tiền hàng tạm tính:</span><span id="receipt-subtotal">0 ₫</span>
                        </div>
                        <div class="flex justify-between text-slate-500">
                            <span>Chiết khấu giảm giá:</span><span id="receipt-discount">-0 ₫</span>
                        </div>
                        <div class="flex justify-between font-black text-xs text-slate-900 pt-2 border-t border-slate-100">
                            <span>TỔNG THỰC THU:</span><span id="receipt-total-amount">0 ₫</span>
                        </div>
                    </div>

                    <!-- Ghi chú hiển thị trên bill -->
                    <div id="receipt-note-wrapper" class="bg-slate-50 p-2.5 rounded-xl border border-slate-200 hidden relative z-10">
                        <span class="text-[9px] font-bold text-slate-400 uppercase block">Ghi chú:</span>
                        <p class="text-[10px] font-bold text-slate-700 italic" id="receipt-note-text"></p>
                    </div>

                    <div class="text-center pt-3 relative z-10">
                        <p class="text-[9px] font-bold text-slate-400 uppercase">Cảm ơn quý khách và hẹn gặp lại!</p>
                    </div>
                </div>
            </div>

            <div class="bg-white px-5 py-3 border-t border-slate-100 flex space-x-2 shrink-0 no-print">
                <button onclick="closeReceiptModal()" class="w-1/2 bg-slate-150 hover:bg-slate-200 text-slate-700 font-bold py-2.5 rounded-xl text-xs transition-colors">Đóng</button>
                <button onclick="printReceipt()" class="w-1/2 bg-slate-900 hover:bg-slate-800 text-white font-bold py-2.5 rounded-xl text-xs shadow-md flex items-center justify-center space-x-1.5 transition-colors">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 17h2a2 2 0 002-2v-4a2 2 0 00-2-2H5a2 2 0 00-2 2v4a2 2 0 002 2h2m2 4h6a2 2 0 002-2v-4a2 2 0 00-2-2H9a2 2 0 00-2 2v4a2 2 0 002 2zm8-12V5a2 2 0 00-2-2H9a2 2 0 00-2 2v4h10z" /></svg>
                    <span>In hóa đơn</span>
                </button>
            </div>
        </div>
    </div>

    <!-- 5. SETTINGS MODAL -->
    <div id="modal-settings" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-50 flex items-center justify-center p-4 hidden">
        <div class="bg-white w-full max-w-sm rounded-[24px] shadow-2xl overflow-hidden fade-in">
            <div class="bg-slate-100 px-6 py-4 flex justify-between items-center border-b border-slate-200">
                <h3 class="font-black text-sm tracking-tight text-slate-800">Cài đặt hệ thống</h3>
                <button onclick="closeSettingsModal()" class="bg-white text-slate-400 hover:text-slate-600 p-1.5 rounded-full shadow-sm">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" /></svg>
                </button>
            </div>
            
            <div class="p-6 space-y-4">
                <div>
                    <h4 class="text-[10px] font-bold text-slate-400 uppercase tracking-wider mb-2">Dữ liệu sao lưu (JSON)</h4>
                    <div class="space-y-2">
                        <button onclick="exportData()" class="w-full bg-slate-50 border border-slate-200 hover:border-emerald-500 hover:text-emerald-700 text-slate-700 font-bold py-3 px-4 rounded-xl text-xs flex items-center justify-between transition-colors">
                            <span>Xuất dữ liệu dự phòng</span>
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 16v1a3 3 0 003 3h10a3 3 0 003-3v-1m-4-4l-4 4m0 0l-4-4m4 4V4" /></svg>
                        </button>
                        <button onclick="wipeData()" class="w-full bg-red-50 border border-red-100 hover:bg-red-100 text-red-600 font-bold py-3 px-4 rounded-xl text-xs flex items-center justify-between transition-colors">
                            <span>Xoá sạch dữ liệu</span>
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M19 7l-.867 12.142A2 2 0 0116.138 21H7.862a2 2 0 01-1.995-1.858L5 7m5 4v6m4-6v6m1-10V4a1 1 0 00-1-1h-4a1 1 0 00-1 1v3M4 7h16" /></svg>
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>

    <!-- TOAST NOTIFICATION CONTAINER -->
    <div id="toast-container" class="fixed bottom-24 left-4 right-4 md:bottom-6 md:right-6 md:left-auto space-y-2 z-50 pointer-events-none max-w-sm w-full mx-auto"></div>

    <!-- ==================== LOGIC CHƯƠNG TRÌNH ==================== -->
    <script>
        // MOCK USERS & DATA
        const defaultStaffs = [
            { id: "s1", code: "NV001", name: "Nguyễn Minh Quân", username: "nhanvien", password: "123", role: "Staff" },
            { id: "admin", code: "ADMIN", name: "Trần Hoàng Long", username: "admin", password: "123", role: "Admin" }
        ];

        const defaultProducts = [
            { id: "p1", code: "DT001", name: "iPhone 15 Pro Max 256GB", category: "iPhone", costPrice: 27000000, sellingPrice: 29490000, stock: 12, minStock: 3 },
            { id: "p2", code: "DT002", name: "Samsung Galaxy S24 Ultra 512GB", category: "Samsung", costPrice: 25000000, sellingPrice: 27890000, stock: 8, minStock: 2 },
            { id: "p3", code: "DT003", name: "iPhone 13 128GB", category: "iPhone", costPrice: 12500000, sellingPrice: 13990000, stock: 18, minStock: 4 },
            { id: "p4", code: "DT004", name: "Oppo Reno11 5G", category: "Oppo", costPrice: 8000000, sellingPrice: 9190000, stock: 5, minStock: 3 },
            { id: "p5", code: "DT005", name: "Xiaomi Redmi Note 13", category: "Xiaomi", costPrice: 4000000, sellingPrice: 4690000, stock: 22, minStock: 5 },
            { id: "p6", code: "PK001", name: "Cáp sạc Type-C 20W", category: "Phụ kiện", costPrice: 100000, sellingPrice: 250000, stock: 50, minStock: 10 }
        ];

        const defaultCustomers = [
            { id: "c1", code: "KH001", name: "Phan Anh Tuấn", phone: "0901234567", address: "Quận 3, HCM", totalSpent: 43480000 },
            { id: "c_guest", code: "KHACH_LE", name: "Khách lẻ", phone: "-", address: "-", totalSpent: 0 }
        ];

        const defaultInvoices = [
            { 
                id: "inv1", code: "HD5320", date: "2026-06-03 10:30", customerId: "c1", customerName: "Phan Anh Tuấn", paymentMethod: "Chuyển khoản",
                items: [{ id: "p1", code: "DT001", name: "iPhone 15 Pro Max 256GB", sellingPrice: 29490000, quantity: 1, costPrice: 27000000 }],
                subtotal: 29490000, discount: 0, totalAmount: 29490000, costTotal: 27000000, profit: 2490000, seller: "Trần Hoàng Long", note: "Khách VIP mua trực tiếp tại quầy"
            }
        ];

        let staffs = JSON.parse(localStorage.getItem("kiot_v2_staffs")) || defaultStaffs;
        let products = JSON.parse(localStorage.getItem("kiot_v2_products")) || defaultProducts;
        let customers = JSON.parse(localStorage.getItem("kiot_v2_customers")) || defaultCustomers;
        let invoices = JSON.parse(localStorage.getItem("kiot_v2_invoices")) || defaultInvoices;

        let currentUser = null;
        let cart = [];
        let currentSelectedCategory = "all";
        let reportPeriod = "day"; 
        let revenueChartObj = null;

        function saveState() {
            localStorage.setItem("kiot_v2_staffs", JSON.stringify(staffs));
            localStorage.setItem("kiot_v2_products", JSON.stringify(products));
            localStorage.setItem("kiot_v2_customers", JSON.stringify(customers));
            localStorage.setItem("kiot_v2_invoices", JSON.stringify(invoices));
        }

        // ==================== ĐĂNG NHẬP / PHÂN QUYỀN ====================
        function handleLogin(event) {
            event.preventDefault();
            const u = document.getElementById("login-username").value.trim().toLowerCase();
            const p = document.getElementById("login-password").value.trim();

            const matched = staffs.find(s => s.username === u && s.password === p);
            if (matched) {
                currentUser = matched;
                document.getElementById("login-screen").classList.add("hidden");
                showToast(`Xin chào, ${matched.name}! Chúc một ngày làm việc hiệu quả.`);
                
                // Cập nhật thông tin giao diện Header và Sidebar
                document.getElementById("header-user-name").innerText = matched.name;
                document.getElementById("header-user-role").innerText = matched.role === "Admin" ? "Quản lý" : "Nhân viên";
                document.getElementById("sidebar-user-name").innerText = matched.name;
                document.getElementById("sidebar-user-role").innerText = matched.role === "Admin" ? "Quản lý hệ thống" : "Nhân viên bán lẻ";
                document.getElementById("sidebar-user-avatar").innerText = matched.name.charAt(0).toUpperCase();

                buildBottomNavigationBar();
                buildSidebarNavigation();
                switchTab('pos');
            } else {
                showToast("Sai tài khoản hoặc mật khẩu đăng nhập!", "error");
            }
        }

        function handleLogout() {
            currentUser = null; cart = []; renderCart();
            document.getElementById("login-username").value = "";
            document.getElementById("login-password").value = "";
            document.getElementById("login-screen").classList.remove("hidden");
            closeSettingsModal();
            showToast("Đã đăng xuất tài khoản.");
        }

        // ==================== NAVIGATION ENGINE (SIDEBAR & BOTTOM BAR) ====================
        const NAV_ITEMS = [
            { id: "pos", name: "Bán hàng POS", icon: `<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 3h2l.4 2M7 13h10l4-8H5.4M7 13L5.4 5M7 13l-2.293 2.293c-.63.63-.184 1.707.707 1.707H17m0 0a2 2 0 100 4 2 2 0 000-4zm-8 2a2 2 0 11-4 0 2 2 0 014 0z" />` },
            { id: "products", name: "Kho thiết bị", icon: `<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M20 7l-8-4-8 4m16 0l-8 4m8-4v10l-8 4m0-10L4 7m8 4v10M4 7v10l8 4" />` },
            { id: "invoices", name: "Lịch sử hóa đơn", icon: `<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 12h6m-6 4h6m2 5H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z" />` },
            { id: "customers", name: "Khách hàng", icon: `<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 4.354a4 4 0 110 5.292M15 21H3v-1a6 6 0 0112 0v1zm0 0h6v-1a6 6 0 00-9-5.197M13 7a4 4 0 11-8 0 4 4 0 018 0z" />` },
            { id: "staffs", name: "Nhân viên", icon: `<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M21 13.255A23.931 23.931 0 0112 15c-3.183 0-6.22-.62-9-1.745M16 6V4a2 2 0 00-2-2h-4a2 2 0 00-2 2v2m4 6h.01M5 20h14a2 2 0 002-2V8a2 2 0 00-2-2H5a2 2 0 00-2 2v10a2 2 0 002 2z" />`, adminOnly: true },
            { id: "reports", name: "Biểu đồ báo cáo", icon: `<path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 19v-6a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2a2 2 0 002-2zm0 0V9a2 2 0 012-2h2a2 2 0 012 2v10m-6 0a2 2 0 002 2h2a2 2 0 002-2m0 0V5a2 2 0 012-2h2a2 2 0 012 2v14a2 2 0 01-2 2h-2a2 2 0 01-2-2z" />`, adminOnly: true }
        ];

        function buildBottomNavigationBar() {
            const nav = document.getElementById("bottom-navigation-bar");
            nav.innerHTML = "";

            NAV_ITEMS.forEach(t => {
                if(t.adminOnly && currentUser?.role !== "Admin") return;
                const btn = document.createElement("button");
                btn.id = `btn-tab-mobile-${t.id}`;
                btn.className = "mobile-nav-btn flex flex-col items-center justify-center w-12 py-1 text-slate-450 font-bold transition-all active:scale-95";
                btn.onclick = () => switchTab(t.id);
                btn.innerHTML = `<svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 mb-0.5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="${t.icon}"/></svg><span class="text-[8.5px]">${t.name.split(" ")[0]}</span>`;
                nav.appendChild(btn);
            });
        }

        function buildSidebarNavigation() {
            const nav = document.getElementById("sidebar-navigation");
            nav.innerHTML = "";

            NAV_ITEMS.forEach(t => {
                if(t.adminOnly && currentUser?.role !== "Admin") return;
                const btn = document.createElement("button");
                btn.id = `btn-tab-sidebar-${t.id}`;
                btn.className = "w-full flex items-center space-x-3.5 px-4 py-3 rounded-xl text-xs font-bold text-slate-400 hover:text-white hover:bg-slate-800 transition-all active:scale-95";
                btn.onclick = () => switchTab(t.id);
                btn.innerHTML = `<svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="${t.icon}"/></svg><span>${t.name}</span>`;
                nav.appendChild(btn);
            });
        }

        function switchTab(tabId) {
            if (!currentUser) return;
            if ((tabId === "staffs" || tabId === "reports") && currentUser.role !== "Admin") {
                showToast("Bạn không có quyền quản lý chức năng này!", "warning"); return;
            }

            document.querySelectorAll(".tab-content").forEach(el => el.classList.add("hidden"));
            const targetSec = document.getElementById(`section-${tabId}`);
            if (targetSec) { targetSec.classList.remove("hidden"); }

            // Reset trạng thái nút Mobile
            document.querySelectorAll(".mobile-nav-btn").forEach(btn => btn.classList.remove("text-emerald-500"));
            const activeMobileBtn = document.getElementById(`btn-tab-mobile-${tabId}`);
            if (activeMobileBtn) activeMobileBtn.classList.add("text-emerald-500");

            // Reset trạng thái nút Sidebar (PC)
            document.querySelectorAll("#sidebar-navigation button").forEach(btn => {
                btn.classList.remove("bg-emerald-600", "text-white", "hover:bg-emerald-600");
                btn.classList.add("text-slate-400", "hover:bg-slate-800", "hover:text-white");
            });
            const activeSidebarBtn = document.getElementById(`btn-tab-sidebar-${tabId}`);
            if (activeSidebarBtn) {
                activeSidebarBtn.classList.remove("text-slate-400", "hover:bg-slate-800", "hover:text-white");
                activeSidebarBtn.classList.add("bg-emerald-600", "text-white", "hover:bg-emerald-600");
            }

            // Tải dữ liệu từng phân mục
            if (tabId === 'pos') initPosScreen();
            else if (tabId === 'products') renderProductTable();
            else if (tabId === 'invoices') renderInvoiceTable();
            else if (tabId === 'customers') renderCustomerTable();
            else if (tabId === 'staffs') renderStaffTable();
            else if (tabId === 'reports') renderReportDashboard();
        }

        // ==================== TOAST & UTILITIES ====================
        function showToast(message, type = "success") {
            const container = document.getElementById("toast-container");
            const toast = document.createElement("div");
            toast.className = "flex items-center space-x-2 px-4 py-3.5 rounded-2xl shadow-xl text-white font-bold text-xs transition-all duration-300 transform translate-y-2 opacity-0 pointer-events-auto justify-center";
            
            if (type === "success") {
                toast.classList.add("bg-slate-900");
                toast.innerHTML = `<svg class="h-4 w-4 text-emerald-400 shrink-0" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="3" d="M5 13l4 4L19 7" /></svg><span>${message}</span>`;
            } else if (type === "warning") {
                toast.classList.add("bg-amber-500");
                toast.innerHTML = `<span>⚠️ ${message}</span>`;
            } else {
                toast.classList.add("bg-red-500");
                toast.innerHTML = `<span>❌ ${message}</span>`;
            }

            container.appendChild(toast);
            setTimeout(() => { toast.classList.remove("translate-y-2", "opacity-0"); }, 10);
            setTimeout(() => {
                toast.classList.add("translate-y-2", "opacity-0");
                setTimeout(() => { toast.remove(); }, 300);
            }, 3000);
        }

        function formatVND(amount) {
            return new Intl.NumberFormat('vi-VN', { style: 'currency', currency: 'VND' }).format(amount).replace('₫', '').trim() + ' ₫';
        }

        // ==================== POS SYSTEM (BÁN HÀNG ĐA PHƯƠNG THỨC) ====================
        function getBrandVisuals(category) {
            const visual = { color: 'bg-slate-100 text-slate-500', icon: '📱' };
            if(category === 'iPhone') { visual.color = 'bg-slate-100 text-slate-800'; visual.icon = `<svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24"><path d="M17.05 20.28c-.98.95-2.05.8-3.08.35-1.09-.46-2.09-.48-3.24 0-1.44.62-2.2.44-3.06-.35C2.79 15.25 3.51 7.59 9.05 7.31c1.35.07 2.29.74 3.08.8 1.18-.24 2.31-.93 3.57-.84 1.51.05 2.95.72 3.8 1.94-3.41 2.06-2.83 6.64.55 7.9-1.03 1.25-1.99 2.45-2.9 3.17zM12.03 7.25c-.15-2.23 1.66-4.07 3.74-4.25.29 2.58-2.34 4.5-3.74 4.25z"/></svg>`; }
            else if(category === 'Samsung') { visual.color = 'bg-blue-50 text-blue-600'; visual.icon = `<svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24"><text x="50%" y="60%" font-size="12" font-weight="900" font-family="Arial" text-anchor="middle">S</text></svg>`; }
            else if(category === 'Xiaomi') { visual.color = 'bg-orange-50 text-orange-600'; visual.icon = `<svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24"><text x="50%" y="60%" font-size="12" font-weight="900" font-family="Arial" text-anchor="middle">mi</text></svg>`; }
            else if(category === 'Phụ kiện') { visual.color = 'bg-purple-50 text-purple-600'; visual.icon = `<svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 10V3L4 14h7v7l9-11h-7z"/></svg>`; }
            return visual;
        }

        function initPosScreen() {
            renderPosCategories();
            renderPosProducts();
            populatePosCustomerDropdowns();
            calcCartTotals();
        }

        function renderPosCategories() {
            const categories = ["all", "iPhone", "Samsung", "Oppo", "Xiaomi", "Phụ kiện"];
            const catContainer = document.getElementById("pos-category-filters");
            catContainer.innerHTML = "";

            categories.forEach(cat => {
                const isActive = currentSelectedCategory === cat;
                const display = cat === "all" ? "Tất cả" : (cat === "iPhone" ? "Apple" : cat);
                const btn = document.createElement("button");
                btn.className = `px-3.5 py-2 rounded-xl text-xs font-extrabold whitespace-nowrap transition-all border ${
                    isActive ? "bg-slate-900 text-white border-slate-900 shadow-sm" : "bg-white text-slate-600 border-slate-200 hover:bg-slate-50"
                }`;
                btn.innerText = display;
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

            if (filtered.length === 0) {
                grid.innerHTML = `<div class="col-span-full py-10 text-center text-slate-400 text-xs font-bold">Không tìm thấy thiết bị phù hợp trong kho</div>`;
                return;
            }

            filtered.forEach(p => {
                const isOutOfStock = p.stock <= 0;
                const visual = getBrandVisuals(p.category);

                const card = document.createElement("div");
                card.className = `bg-white p-4 rounded-2xl border border-slate-200 flex flex-col justify-between space-y-3 relative shadow-sm ${
                    isOutOfStock ? "opacity-50 grayscale bg-slate-50 cursor-not-allowed" : "active:scale-95 transition-transform cursor-pointer hover:border-emerald-400"
                }`;
                
                if (!isOutOfStock) card.onclick = () => addToCart(p);

                const badge = isOutOfStock 
                    ? `<span class="absolute top-2.5 right-2.5 bg-red-100 text-red-600 text-[9px] px-2 py-0.5 rounded font-black">HẾT HÀNG</span>`
                    : `<span class="absolute top-2.5 right-2.5 bg-slate-100 text-slate-500 text-[9px] px-2 py-0.5 rounded font-bold">Còn: ${p.stock}</span>`;

                card.innerHTML = `
                    ${badge}
                    <div class="w-9.5 h-9.5 rounded-xl ${visual.color} flex items-center justify-center shadow-inner">
                        ${visual.icon}
                    </div>
                    <div>
                        <span class="text-[9px] text-slate-400 font-bold block uppercase tracking-wider mb-0.5">${p.category}</span>
                        <h4 class="font-bold text-xs text-slate-800 line-clamp-2 leading-tight">${p.name}</h4>
                    </div>
                    <div class="flex justify-between items-center pt-2.5 border-t border-slate-100">
                        <span class="font-black text-xs text-emerald-600">${formatVND(p.sellingPrice)}</span>
                        <div class="w-6 h-6 rounded-full bg-slate-100 text-slate-600 flex items-center justify-center font-bold text-xs">
                            +
                        </div>
                    </div>
                `;
                grid.appendChild(card);
            });
        }

        function populatePosCustomerDropdowns() {
            const mSelect = document.getElementById("pos-customer-select-mobile");
            const dSelect = document.getElementById("pos-customer-select-desktop");
            
            [mSelect, dSelect].forEach(select => {
                if(!select) return;
                select.innerHTML = "";
                customers.forEach(c => {
                    const opt = document.createElement("option");
                    opt.value = c.id;
                    opt.innerText = c.name + (c.phone !== "-" ? ` (${c.phone})` : "");
                    if (c.code === "KHACH_LE") opt.selected = true;
                    select.appendChild(opt);
                });
            });
        }

        function addToCart(product) {
            const existing = cart.find(item => item.id === product.id);
            const currentQty = existing ? existing.quantity : 0;

            if (currentQty + 1 > product.stock) {
                showToast(`Chỉ còn đúng ${product.stock} sản phẩm có sẵn trong kho hàng!`, "warning"); return;
            }

            if (existing) existing.quantity++;
            else cart.push({ ...product, quantity: 1 });

            renderCart(); calcCartTotals();
            showToast("Đã thêm thiết bị vào giỏ hàng!");
        }

        function toggleCartSheet(show) {
            const sheet = document.getElementById("cart-bottom-sheet");
            if (show) sheet.classList.remove("hidden");
            else sheet.classList.add("hidden");
        }

        function renderCart() {
            // Render song song cả 2 giỏ hàng (Cho Mobile & Desktop)
            const mContainer = document.getElementById("pos-cart-mobile-container");
            const dContainer = document.getElementById("pos-cart-desktop-container");
            const floatBar = document.getElementById("floating-cart-bar");

            [mContainer, dContainer].forEach(container => {
                if(!container) return;
                container.innerHTML = "";
                
                if (cart.length === 0) {
                    container.innerHTML = `<div class="py-12 flex flex-col items-center justify-center opacity-40"><svg xmlns="http://www.w3.org/2000/svg" class="h-10 w-10 mb-2" fill="none" viewBox="0 0 24 24" stroke="currentColor"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.5" d="M16 11V7a4 4 0 00-8 0v4M5 9h14l1 12H4L5 9z" /></svg><span class="text-xs font-bold">Giỏ hàng rỗng</span></div>`;
                    return;
                }

                cart.forEach((item, index) => {
                    const row = document.createElement("div");
                    row.className = "py-3.5 flex items-center justify-between space-x-2";
                    row.innerHTML = `
                        <div class="flex-1 min-w-0 pr-1">
                            <h5 class="font-bold text-xs text-slate-800 truncate" title="${item.name}">${item.name}</h5>
                            <span class="text-[10px] text-emerald-600 font-extrabold block mt-0.5">${formatVND(item.sellingPrice)}</span>
                        </div>
                        <div class="flex items-center bg-slate-100 rounded-lg p-0.5 shrink-0">
                            <button onclick="updateCartQty(${index}, -1)" class="w-6.5 h-6.5 rounded bg-white shadow-sm flex items-center justify-center font-bold text-slate-600 text-xs">-</button>
                            <span class="w-6 text-center font-black text-xs">${item.quantity}</span>
                            <button onclick="updateCartQty(${index}, 1)" class="w-6.5 h-6.5 rounded bg-white shadow-sm flex items-center justify-center font-bold text-slate-600 text-xs">+</button>
                        </div>
                    `;
                    container.appendChild(row);
                });
            });

            // Toggle hiển thị thanh nổi giỏ hàng (chỉ trên Mobile)
            let count = cart.reduce((acc, curr) => acc + curr.quantity, 0);
            document.getElementById("pos-cart-count").innerText = count;
            document.getElementById("cart-floating-count").innerText = count;

            if (cart.length === 0) floatBar.classList.add("hidden");
            else floatBar.classList.remove("hidden");
        }

        function updateCartQty(index, offset) {
            const item = cart[index];
            const original = products.find(p => p.id === item.id);

            if (item.quantity + offset <= 0) {
                cart.splice(index, 1);
                showToast("Đã loại bỏ máy khỏi đơn hàng.");
            } else if (item.quantity + offset > original.stock) {
                showToast(`Không đủ số lượng có sẵn trong kho hàng!`, "warning");
                return;
            } else {
                item.quantity += offset;
            }

            renderCart(); calcCartTotals();
        }

        function clearCart() {
            cart = []; renderCart(); calcCartTotals(); toggleCartSheet(false);
            document.getElementById("pos-note-mobile").value = "";
            document.getElementById("pos-note-desktop").value = "";
            showToast("Giỏ hàng đã sạch.");
        }

        function syncDiscount(from) {
            const mDisc = document.getElementById("pos-discount-mobile");
            const dDisc = document.getElementById("pos-discount-desktop");
            
            if(from === 'mobile') dDisc.value = mDisc.value;
            else mDisc.value = dDisc.value;

            calcCartTotals();
        }

        function calcCartTotals() {
            let subtotal = 0;
            cart.forEach(item => subtotal += item.sellingPrice * item.quantity);

            const discountPct = parseInt(document.getElementById("pos-discount-desktop").value) || 0;
            const discountAmt = Math.round(subtotal * (discountPct / 100));
            const total = subtotal - discountAmt;

            // Update cho phiên bản desktop
            document.getElementById("pos-subtotal-desktop").innerText = formatVND(subtotal);
            document.getElementById("pos-total-amount-desktop").innerText = formatVND(total);
            document.getElementById("cart-floating-total").innerText = formatVND(total);

            // Update cho phiên bản mobile
            document.getElementById("pos-subtotal-mobile").innerText = formatVND(subtotal);
            document.getElementById("pos-total-amount-mobile").innerText = formatVND(total);
        }

        function checkoutCartDirect(from) {
            if (cart.length === 0) return;

            const suffix = from === 'desktop' ? '-desktop' : '-mobile';
            const custId = document.getElementById("pos-customer-select" + suffix).value;
            const customer = customers.find(c => c.id === custId);
            const discountPct = parseInt(document.getElementById("pos-discount" + suffix).value) || 0;
            const payMethod = document.getElementById("pos-payment" + suffix).value;
            const note = document.getElementById("pos-note" + suffix).value.trim();

            let subtotal = 0; let costTotal = 0;
            cart.forEach(item => {
                subtotal += item.sellingPrice * item.quantity;
                costTotal += item.costPrice * item.quantity;
            });

            const discountAmt = Math.round(subtotal * (discountPct / 100));
            const totalAmount = subtotal - discountAmt;
            const profit = totalAmount - costTotal;

            const invoiceId = "HD" + Math.floor(10000 + Math.random() * 90000);
            
            // Format datetime: YYYY-MM-DD HH:mm
            const now = new Date();
            const pad = n => n < 10 ? '0' + n : n;
            const formattedDate = `${now.getFullYear()}-${pad(now.getMonth()+1)}-${pad(now.getDate())} ${pad(now.getHours())}:${pad(now.getMinutes())}`;

            const sellerInfo = currentUser ? currentUser.name : "Hệ thống";

            const invoiceObj = {
                id: "inv_" + Date.now(), code: invoiceId, date: formattedDate,
                customerId: customer.id, customerName: customer.name,
                paymentMethod: payMethod, items: [...cart],
                subtotal, discount: discountPct, totalAmount, costTotal, profit, seller: sellerInfo, note: note
            };

            // Trừ tồn kho
            cart.forEach(item => {
                const original = products.find(p => p.id === item.id);
                if (original) original.stock = Math.max(0, original.stock - item.quantity);
            });

            if (customer.code !== "KHACH_LE") customer.totalSpent += totalAmount;

            invoices.unshift(invoiceObj);
            saveState();

            // Clear giỏ hàng và reset trạng thái nhập
            cart = []; renderCart();
            document.getElementById("pos-discount-desktop").value = 0;
            document.getElementById("pos-discount-mobile").value = 0;
            document.getElementById("pos-note-desktop").value = "";
            document.getElementById("pos-note-mobile").value = "";
            
            calcCartTotals(); toggleCartSheet(false); renderPosProducts();

            showToast(`Giao dịch thành công hóa đơn #${invoiceId}`);
            showSimulatedReceipt(invoiceObj);
        }

        // ==================== RECEIPT VIEWER AND PRINT ACTION ====================
        function showSimulatedReceipt(invoice) {
            document.getElementById("receipt-invoice-code").innerText = invoice.code;
            document.getElementById("receipt-id").innerText = invoice.code;
            document.getElementById("receipt-date").innerText = invoice.date;
            document.getElementById("receipt-customer-name").innerText = invoice.customerName;
            document.getElementById("receipt-seller").innerText = invoice.seller || "Hệ thống";

            const itemContainer = document.getElementById("receipt-items-container");
            itemContainer.innerHTML = "";

            invoice.items.forEach(item => {
                const row = document.createElement("div");
                row.className = "flex justify-between items-start text-[10px] font-bold text-slate-800 py-1 border-b border-dashed border-slate-100 last:border-0";
                row.innerHTML = `
                    <div class="pr-2 flex-1">
                        <span class="block">${item.name}</span>
                        <span class="text-[8.5px] text-slate-550 font-medium">SL: ${item.quantity} x ${formatVND(item.sellingPrice)}</span>
                    </div>
                    <span class="shrink-0 pt-0.5">${formatVND(item.sellingPrice * item.quantity)}</span>
                `;
                itemContainer.appendChild(row);
            });

            const discAmt = Math.round(invoice.subtotal * (invoice.discount / 100));
            document.getElementById("receipt-subtotal").innerText = formatVND(invoice.subtotal);
            document.getElementById("receipt-discount").innerText = discAmt > 0 ? `-${formatVND(discAmt)} (${invoice.discount}%)` : "0 ₫";
            document.getElementById("receipt-total-amount").innerText = formatVND(invoice.totalAmount);

            // Ghi chú hiển thị
            const noteWrapper = document.getElementById("receipt-note-wrapper");
            const noteText = document.getElementById("receipt-note-text");
            if (invoice.note) {
                noteText.innerText = invoice.note;
                noteWrapper.classList.remove("hidden");
            } else {
                noteWrapper.classList.add("hidden");
            }

            document.getElementById("modal-receipt").classList.remove("hidden");
        }

        function closeReceiptModal() { document.getElementById("modal-receipt").classList.add("hidden"); }
        function printReceipt() { window.print(); }

        // ==================== SETTINGS (EXPORT / IMPORT / WIPE) ====================
        function openSettingsModal() { document.getElementById("modal-settings").classList.remove("hidden"); }
        function closeSettingsModal() { document.getElementById("modal-settings").classList.add("hidden"); }

        function exportData() {
            const data = { staffs, products, customers, invoices };
            const dataStr = "data:text/json;charset=utf-8," + encodeURIComponent(JSON.stringify(data));
            const dlNode = document.createElement('a');
            dlNode.setAttribute("href", dataStr);
            dlNode.setAttribute("download", "kiotpro_system_backup_" + Date.now() + ".json");
            document.body.appendChild(dlNode);
            dlNode.click();
            dlNode.remove();
            showToast("Đã xuất tệp dữ liệu JSON thành công.");
        }

        function wipeData() {
            if(confirm("⚠️ LƯU Ý: Hành động này sẽ xoá sạch mọi thiết lập, hóa đơn, khách hàng mới và phục hồi về thông số mặc định! Đồng ý?")) {
                localStorage.removeItem("kiot_v2_staffs");
                localStorage.removeItem("kiot_v2_products");
                localStorage.removeItem("kiot_v2_customers");
                localStorage.removeItem("kiot_v2_invoices");
                location.reload();
            }
        }

        // ==================== RENDER KHO HÀNG (TABLE & CARD) ====================
        function renderProductTable() {
            const tBody = document.getElementById("product-table-body");
            const mContainer = document.getElementById("product-cards-container");
            tBody.innerHTML = "";
            mContainer.innerHTML = "";

            const searchKey = document.getElementById("product-list-search").value.toLowerCase().trim();
            const catFilter = document.getElementById("product-list-category").value;
            const statusFilter = document.getElementById("product-list-status").value;

            const filtered = products.filter(p => {
                const matchSearch = p.name.toLowerCase().includes(searchKey) || p.code.toLowerCase().includes(searchKey);
                const matchCat = catFilter === "all" || p.category === catFilter;
                let matchStatus = true;
                if (statusFilter === "low-stock") matchStatus = p.stock <= p.minStock && p.stock > 0;
                else if (statusFilter === "out-of-stock") matchStatus = p.stock === 0;
                return matchSearch && matchCat && matchStatus;
            });

            if(filtered.length === 0) {
                const emptyMsg = `<tr><td colspan="8" class="text-center py-10 text-slate-400 font-bold">Không tồn tại máy nào khớp với bộ lọc</td></tr>`;
                tBody.innerHTML = emptyMsg;
                mContainer.innerHTML = `<div class="text-center py-10 text-slate-400 font-bold text-xs">Không tìm thấy máy phù hợp</div>`;
                return;
            }

            filtered.forEach(p => {
                const isOutOfStock = p.stock === 0;
                const isLowStock = p.stock <= p.minStock && p.stock > 0;
                let badge = isOutOfStock ? `<span class="bg-red-50 text-red-600 px-2.5 py-1 rounded-md text-[9px] font-black tracking-wide border border-red-100">HẾT HÀNG</span>` 
                          : isLowStock ? `<span class="bg-amber-50 text-amber-600 px-2.5 py-1 rounded-md text-[9px] font-black tracking-wide border border-amber-100">SẮP HẾT</span>`
                          : `<span class="bg-emerald-50 text-emerald-600 px-2.5 py-1 rounded-md text-[9px] font-black tracking-wide border border-emerald-100">AN TOÀN</span>`;

                const actions = (currentUser?.role === "Admin") 
                    ? `<div class="flex items-center justify-center space-x-2">
                        <button onclick="openProductModal(true, '${p.id}')" class="text-slate-600 bg-slate-100 border border-slate-200 px-2.5 py-1 rounded-md hover:bg-slate-200">Sửa</button>
                        <button onclick="deleteProduct('${p.id}')" class="text-red-500 bg-red-50 border border-red-100 px-2.5 py-1 rounded-md hover:bg-red-100">Xóa</button>
                       </div>` : `<span class="text-slate-400 italic font-medium">Chỉ Admin</span>`;

                // Render bản PC (Table row)
                const tr = document.createElement("tr");
                tr.className = "hover:bg-slate-50 transition-colors border-b border-slate-100";
                tr.innerHTML = `
                    <td class="py-3.5 px-6 font-mono text-slate-500">${p.code}</td>
                    <td class="py-3.5 px-6 text-slate-900">${p.name}</td>
                    <td class="py-3.5 px-6 text-slate-500">${p.category}</td>
                    <td class="py-3.5 px-6 text-right">${formatVND(p.costPrice)}</td>
                    <td class="py-3.5 px-6 text-right text-emerald-600 font-extrabold">${formatVND(p.sellingPrice)}</td>
                    <td class="py-3.5 px-6 text-center text-slate-800 font-extrabold">${p.stock} máy</td>
                    <td class="py-3.5 px-6 text-center">${badge}</td>
                    <td class="py-3.5 px-6 text-center">${actions}</td>
                `;
                tBody.appendChild(tr);

                // Render bản Mobile (Card)
                const card = document.createElement("div");
                card.className = "bg-white p-4 rounded-3xl border border-slate-200/70 shadow-sm space-y-3";
                card.innerHTML = `
                    <div class="flex justify-between items-start">
                        <div>
                            <span class="text-[9px] text-slate-400 font-bold block uppercase tracking-wider mb-0.5">${p.code}</span>
                            <h4 class="font-black text-xs text-slate-800 leading-snug">${p.name}</h4>
                        </div>
                        ${badge}
                    </div>
                    <div class="grid grid-cols-3 gap-2 bg-slate-50 p-2.5 rounded-2xl text-[10px]">
                        <div><span class="text-[9px] text-slate-400 block mb-0.5">Giá vốn</span><span class="text-slate-700 font-bold">${formatVND(p.costPrice)}</span></div>
                        <div><span class="text-[9px] text-slate-400 block mb-0.5">Giá bán</span><span class="text-emerald-600 font-black">${formatVND(p.sellingPrice)}</span></div>
                        <div class="text-right"><span class="text-[9px] text-slate-400 block mb-0.5">Kho</span><span class="text-slate-900 font-black text-xs">${p.stock}</span></div>
                    </div>
                    ${currentUser?.role === "Admin" ? `
                    <div class="flex justify-end space-x-2 pt-2.5 border-t border-slate-100 font-bold text-xs">
                        <button onclick="openProductModal(true, '${p.id}')" class="text-slate-600 bg-slate-100 px-3 py-1.5 rounded-lg">Sửa</button>
                        <button onclick="deleteProduct('${p.id}')" class="text-red-500 bg-red-50 px-3 py-1.5 rounded-lg">Xoá</button>
                    </div>` : ""}
                `;
                mContainer.appendChild(card);
            });
        }

        // ==================== RENDER HOÁ ĐƠN (TABLE & CARD) ====================
        function renderInvoiceTable() {
            const tBody = document.getElementById("invoice-table-body");
            const mContainer = document.getElementById("invoice-cards-container");
            tBody.innerHTML = "";
            mContainer.innerHTML = "";

            const searchKey = document.getElementById("invoice-search").value.toLowerCase().trim();
            const filtered = invoices.filter(inv => inv.code.toLowerCase().includes(searchKey) || inv.customerName.toLowerCase().includes(searchKey));

            if(filtered.length === 0) {
                tBody.innerHTML = `<tr><td colspan="7" class="text-center py-10 text-slate-400 font-bold">Chưa có thông tin đơn hàng trùng khớp</td></tr>`;
                mContainer.innerHTML = `<div class="text-center py-10 text-slate-400 font-bold text-xs">Không tìm thấy hóa đơn nào</div>`;
                return;
            }

            filtered.forEach(inv => {
                // PC (Row)
                const tr = document.createElement("tr");
                tr.className = "hover:bg-slate-50 border-b border-slate-100 transition-colors";
                tr.innerHTML = `
                    <td class="py-3.5 px-6 font-mono text-emerald-600 font-black">#${inv.code}</td>
                    <td class="py-3.5 px-6 text-slate-450">${inv.date}</td>
                    <td class="py-3.5 px-6 text-slate-800 font-bold">${inv.customerName}</td>
                    <td class="py-3.5 px-6 text-right font-black text-slate-900">${formatVND(inv.totalAmount)}</td>
                    <td class="py-3.5 px-6 text-center"><span class="bg-slate-100 text-slate-700 font-bold px-2 py-0.5 rounded">${inv.paymentMethod}</span></td>
                    <td class="py-3.5 px-6 text-slate-500">${inv.seller}</td>
                    <td class="py-3.5 px-6 text-center">
                        <button onclick="viewInvoiceDetail('${inv.id}')" class="text-emerald-600 bg-emerald-50 hover:bg-emerald-100 border border-emerald-100 px-3 py-1 rounded-md">Xem</button>
                    </td>
                `;
                tBody.appendChild(tr);

                // Mobile (Card)
                const card = document.createElement("div");
                card.className = "bg-white p-4 rounded-3xl border border-slate-200/60 shadow-sm space-y-2.5 active:scale-95 transition-transform cursor-pointer hover:border-emerald-300";
                card.onclick = () => viewInvoiceDetail(inv.id);
                card.innerHTML = `
                    <div class="flex justify-between items-center">
                        <span class="font-black text-emerald-600 text-xs">#${inv.code}</span>
                        <span class="text-[9px] text-slate-400 font-bold bg-slate-50 px-2 py-0.5 rounded">${inv.date}</span>
                    </div>
                    <div class="flex justify-between items-end">
                        <div>
                            <span class="text-[9px] text-slate-400 font-bold block">Khách hàng</span>
                            <span class="text-xs font-black text-slate-800">${inv.customerName}</span>
                        </div>
                        <div class="text-right">
                            <span class="text-xs font-black text-slate-900 block">${formatVND(inv.totalAmount)}</span>
                            <span class="text-[9.5px] text-slate-400 font-semibold">${inv.paymentMethod}</span>
                        </div>
                    </div>
                `;
                mContainer.appendChild(card);
            });
        }

        function viewInvoiceDetail(id) {
            const inv = invoices.find(item => item.id === id);
            if (inv) showSimulatedReceipt(inv);
        }

        // ==================== RENDER KHÁCH HÀNG (TABLE & CARD) ====================
        function renderCustomerTable() {
            const tBody = document.getElementById("customer-table-body");
            const mContainer = document.getElementById("customer-cards-container");
            tBody.innerHTML = "";
            mContainer.innerHTML = "";

            const searchKey = document.getElementById("customer-search").value.toLowerCase().trim();
            const filtered = customers.filter(c => c.name.toLowerCase().includes(searchKey) || c.phone.includes(searchKey));

            filtered.forEach(c => {
                const isGuest = c.code === "KHACH_LE";
                const actionsHtml = isGuest ? `<span class="text-slate-400 italic">Hệ thống</span>` 
                    : `<div class="flex justify-center space-x-2">
                        <button onclick="openAddCustomerModal(false, '${c.id}')" class="text-slate-600 bg-slate-100 border border-slate-200 px-2.5 py-1 rounded-md">Sửa</button>
                        <button onclick="deleteCustomer('${c.id}')" class="text-red-500 bg-red-50 border border-red-100 px-2.5 py-1 rounded-md">Xóa</button>
                       </div>`;

                // PC (Row)
                const tr = document.createElement("tr");
                tr.className = "hover:bg-slate-50 border-b border-slate-100 transition-colors";
                tr.innerHTML = `
                    <td class="py-3.5 px-6 font-mono text-slate-500">${c.code}</td>
                    <td class="py-3.5 px-6 text-slate-800 font-bold">${c.name}</td>
                    <td class="py-3.5 px-6 text-slate-500">${c.phone}</td>
                    <td class="py-3.5 px-6 text-slate-500 truncate max-w-[200px]">${c.address}</td>
                    <td class="py-3.5 px-6 text-right font-black text-emerald-600">${formatVND(c.totalSpent)}</td>
                    <td class="py-3.5 px-6 text-center">${actionsHtml}</td>
                `;
                tBody.appendChild(tr);

                // Mobile (Card)
                const card = document.createElement("div");
                card.className = "bg-white p-4 rounded-3xl border border-slate-200/60 shadow-sm space-y-2";
                card.innerHTML = `
                    <div class="flex justify-between items-start">
                        <div>
                            <span class="text-[9px] text-slate-400 font-bold block uppercase tracking-wider mb-0.5">${c.code}</span>
                            <h4 class="font-black text-xs text-slate-800">${c.name}</h4>
                            <span class="text-[11px] font-bold text-slate-500 block mt-1">SĐT: ${c.phone}</span>
                        </div>
                        <div class="text-right bg-emerald-50 px-2.5 py-1 rounded-lg border border-emerald-100">
                            <span class="text-[9px] text-emerald-600 block uppercase font-bold">Lũy kế</span>
                            <span class="text-xs font-black text-emerald-700">${formatVND(c.totalSpent)}</span>
                        </div>
                    </div>
                    ${!isGuest ? `
                    <div class="flex justify-end space-x-2 pt-2 border-t border-slate-100 font-bold text-xs mt-2">
                        <button onclick="openAddCustomerModal(false, '${c.id}')" class="text-slate-600 bg-slate-100 px-3 py-1.5 rounded-lg">Sửa</button>
                        <button onclick="deleteCustomer('${c.id}')" class="text-red-500 bg-red-50 px-3 py-1.5 rounded-lg">Xóa</button>
                    </div>` : ""}
                `;
                mContainer.appendChild(card);
            });
        }

        // ==================== RENDER NHÂN SỰ (TABLE & CARD) ====================
        function renderStaffTable() {
            const tBody = document.getElementById("staff-table-body");
            const mContainer = document.getElementById("staff-cards-container");
            tBody.innerHTML = "";
            mContainer.innerHTML = "";

            staffs.forEach(s => {
                const isSelf = s.username === currentUser?.username;
                const actionsHtml = isSelf ? `<span class="text-emerald-600 italic">Đang hoạt động</span>`
                    : `<div class="flex justify-center space-x-2">
                        <button onclick="openStaffModal(true, '${s.id}')" class="text-slate-600 bg-slate-100 border border-slate-200 px-2.5 py-1 rounded-md">Sửa</button>
                        <button onclick="deleteStaff('${s.id}')" class="text-red-500 bg-red-50 border border-red-100 px-2.5 py-1 rounded-md">Xóa</button>
                       </div>`;

                // PC (Row)
                const tr = document.createElement("tr");
                tr.className = "hover:bg-slate-50 border-b border-slate-100 transition-colors";
                tr.innerHTML = `
                    <td class="py-3.5 px-6 font-mono text-slate-500">${s.code}</td>
                    <td class="py-3.5 px-6 text-slate-800 font-bold">${s.name}</td>
                    <td class="py-3.5 px-6 font-mono text-slate-500">${s.username}</td>
                    <td class="py-3.5 px-6"><span class="px-2 py-0.5 rounded text-[10px] font-bold ${s.role === 'Admin' ? 'bg-amber-100 text-amber-700' : 'bg-blue-100 text-blue-700'}">${s.role}</span></td>
                    <td class="py-3.5 px-6 text-center">${actionsHtml}</td>
                `;
                tBody.appendChild(tr);

                // Mobile (Card)
                const card = document.createElement("div");
                card.className = "bg-white p-4 rounded-3xl border border-slate-200/60 shadow-sm";
                card.innerHTML = `
                    <div class="flex justify-between items-start">
                        <div>
                            <h4 class="font-black text-xs text-slate-800">${s.name}</h4>
                            <span class="text-[10px] font-bold text-slate-500 mt-0.5 block">Tài khoản: <span class="font-mono text-blue-600">${s.username}</span></span>
                        </div>
                        <span class="px-2.5 py-1 rounded-lg text-[9px] font-black border ${s.role === "Admin" ? "bg-amber-50 text-amber-600 border-amber-100" : "bg-blue-50 text-blue-600 border-blue-100"}">${s.role}</span>
                    </div>
                    ${!isSelf ? `
                    <div class="flex justify-end space-x-2 pt-2 border-t border-slate-100 font-bold text-xs mt-2">
                        <button onclick="openStaffModal(true, '${s.id}')" class="text-slate-600 bg-slate-100 px-3 py-1.5 rounded-lg">Sửa</button>
                        <button onclick="deleteStaff('${s.id}')" class="text-red-500 bg-red-50 px-3 py-1.5 rounded-lg">Xóa</button>
                    </div>` : ""}
                `;
                mContainer.appendChild(card);
            });
        }

        // ==================== BẢN ĐỒ BIỂU MẪU ĐĂNG KÝ CRUD ====================
        // CRUD SẢN PHẨM
        function openProductModal(isEdit, id="") {
            const form = document.getElementById("product-form"); form.reset();
            document.getElementById("product-modal-title").innerText = isEdit ? "Sửa thông tin sản phẩm" : "Thêm thiết bị mới";
            if (isEdit) {
                const p = products.find(item => item.id === id);
                document.getElementById("edit-product-id").value = p.id; document.getElementById("prod-code").value = p.code;
                document.getElementById("prod-category").value = p.category; document.getElementById("prod-name").value = p.name;
                document.getElementById("prod-cost").value = p.costPrice; document.getElementById("prod-selling").value = p.sellingPrice;
                document.getElementById("prod-stock").value = p.stock; document.getElementById("prod-minStock").value = p.minStock;
            } else { document.getElementById("edit-product-id").value = ""; document.getElementById("prod-code").value = "SP" + Date.now().toString().slice(-4); }
            document.getElementById("modal-product").classList.remove("hidden");
        }
        function closeProductModal() { document.getElementById("modal-product").classList.add("hidden"); }
        function saveProduct(e) {
            e.preventDefault();
            const id = document.getElementById("edit-product-id").value;
            const newProd = {
                id: id || "prod_" + Date.now(),
                code: document.getElementById("prod-code").value.toUpperCase(),
                category: document.getElementById("prod-category").value,
                name: document.getElementById("prod-name").value,
                costPrice: parseInt(document.getElementById("prod-cost").value)||0,
                sellingPrice: parseInt(document.getElementById("prod-selling").value)||0,
                stock: parseInt(document.getElementById("prod-stock").value)||0,
                minStock: parseInt(document.getElementById("prod-minStock").value)||0
            };
            if (id) products[products.findIndex(p => p.id === id)] = newProd; else products.unshift(newProd);
            saveState(); closeProductModal(); renderProductTable(); showToast("Đã lưu dữ liệu sản phẩm thành công!");
        }
        function deleteProduct(id) { if(confirm("Bạn có đồng ý xóa máy này khỏi kho hàng?")) { products = products.filter(p=>p.id!==id); saveState(); renderProductTable(); showToast("Đã xóa sản phẩm."); } }

        // CRUD KHÁCH HÀNG
        function openAddCustomerModal(isPos, id="") {
            const form = document.getElementById("customer-form"); form.reset();
            document.getElementById("is-pos-adding").value = isPos;
            document.getElementById("customer-modal-title").innerText = id ? "Chỉnh sửa đối tác" : "Thêm mới đối tác khách hàng";
            if(id){
                const c = customers.find(x=>x.id===id);
                document.getElementById("edit-customer-id").value = c.id; document.getElementById("cust-code").value = c.code;
                document.getElementById("cust-name").value = c.name; document.getElementById("cust-phone").value = c.phone; document.getElementById("cust-address").value = c.address;
            } else { document.getElementById("edit-customer-id").value = ""; document.getElementById("cust-code").value = "KH" + Date.now().toString().slice(-4); }
            document.getElementById("modal-customer").classList.remove("hidden");
        }
        function closeCustomerModal() { document.getElementById("modal-customer").classList.add("hidden"); }
        function saveCustomer(e) {
            e.preventDefault();
            const id = document.getElementById("edit-customer-id").value;
            const isPos = document.getElementById("is-pos-adding").value === "true";
            const newCust = {
                id: id || "cust_" + Date.now(),
                code: document.getElementById("cust-code").value.toUpperCase(), name: document.getElementById("cust-name").value,
                phone: document.getElementById("cust-phone").value, address: document.getElementById("cust-address").value,
                totalSpent: id ? customers.find(c=>c.id===id).totalSpent : 0
            };
            if(id) customers[customers.findIndex(c=>c.id===id)] = newCust; else customers.unshift(newCust);
            saveState(); closeCustomerModal();
            if(isPos) { populatePosCustomerDropdowns(); document.getElementById("pos-customer-select-desktop").value = newCust.id; document.getElementById("pos-customer-select-mobile").value = newCust.id; } 
            else renderCustomerTable();
            showToast("Đã cập nhật thông tin đối tác.");
        }
        function deleteCustomer(id) { if(confirm("Xóa thông tin khách hàng này?")) { customers=customers.filter(c=>c.id!==id); saveState(); renderCustomerTable(); showToast("Đã xóa."); } }

        // CRUD NHÂN VIÊN
        function openStaffModal(isEdit, id="") {
            const form = document.getElementById("staff-form"); form.reset();
            document.getElementById("staff-modal-title").innerText = id ? "Thay đổi phân quyền nhân sự" : "Thêm nhân viên mới";
            if(id){
                const s = staffs.find(x=>x.id===id);
                document.getElementById("edit-staff-id").value = s.id; document.getElementById("staff-code").value = s.code;
                document.getElementById("staff-role").value = s.role; document.getElementById("staff-name").value = s.name;
                document.getElementById("staff-username").value = s.username; document.getElementById("staff-password").value = s.password;
            } else { document.getElementById("edit-staff-id").value = ""; document.getElementById("staff-code").value = "NV" + Date.now().toString().slice(-3); }
            document.getElementById("modal-staff").classList.remove("hidden");
        }
        function closeStaffModal() { document.getElementById("modal-staff").classList.add("hidden"); }
        function saveStaff(e) {
            e.preventDefault(); const id = document.getElementById("edit-staff-id").value;
            const newStaff = {
                id: id || "staff_" + Date.now(), code: document.getElementById("staff-code").value.toUpperCase(),
                role: document.getElementById("staff-role").value, name: document.getElementById("staff-name").value,
                username: document.getElementById("staff-username").value, password: document.getElementById("staff-password").value
            };
            if(id) staffs[staffs.findIndex(s=>s.id===id)] = newStaff; else staffs.push(newStaff);
            saveState(); closeStaffModal(); renderStaffTable(); showToast("Đã lưu nhân sự.");
        }
        function deleteStaff(id) { if(confirm("Xóa nhân sự này khỏi hệ thống?")) { staffs=staffs.filter(s=>s.id!==id); saveState(); renderStaffTable(); showToast("Đã xóa."); } }

        // ==================== BÁO CÁO THỐNG KÊ (CHART JS BIỂU ĐỒ) ====================
        function setReportPeriod(p) {
            reportPeriod = p;
            ['day', 'week', 'month', 'year'].forEach(x => {
                const btn = document.getElementById(`btn-period-${x}`);
                btn.className = x === p ? "py-2.5 text-xs font-bold rounded-xl transition-all bg-white text-emerald-700 shadow-sm" : "py-2.5 text-xs font-bold rounded-xl transition-all text-slate-600 hover:text-slate-700";
            });
            document.getElementById("chart-period-lbl").innerText = {day:"Hôm nay", week:"Tuần này", month:"Tháng này", year:"Năm nay"}[p];
            renderReportDashboard();
        }

        function renderReportDashboard() {
            let totalRev = 0, totalProf = 0, totalQty = 0; const productSales = {};
            invoices.forEach(inv => {
                totalRev += inv.totalAmount; totalProf += inv.profit;
                inv.items.forEach(item => { totalQty += item.quantity; productSales[item.name] = (productSales[item.name] || 0) + item.quantity; });
            });

            document.getElementById("stat-revenue").innerText = formatVND(totalRev);
            document.getElementById("stat-profit").innerText = formatVND(totalProf);
            document.getElementById("stat-orders").innerText = `${invoices.length} Đơn`;
            document.getElementById("stat-qty-sold").innerText = `${totalQty} Máy`;

            // Render Top Selling Products
            const topCtn = document.getElementById("top-products-progress-container"); topCtn.innerHTML = "";
            const sorted = Object.keys(productSales).map(k => ({name: k, qty: productSales[k]})).sort((a,b)=>b.qty-a.qty).slice(0,3);
            const max = sorted.length ? Math.max(...sorted.map(s=>s.qty)) : 0;
            
            if(sorted.length === 0) {
                topCtn.innerHTML = `<div class="text-center py-6 text-slate-400 font-bold text-xs">Chưa phát sinh doanh thu</div>`;
            } else {
                sorted.forEach((p, i) => {
                    const colors = ['bg-emerald-500', 'bg-blue-500', 'bg-amber-500'];
                    topCtn.innerHTML += `
                        <div>
                            <div class="flex justify-between items-center text-[11px] font-bold text-slate-700 mb-1.5"><span class="truncate pr-2">${i+1}. ${p.name}</span><span>${p.qty} máy</span></div>
                            <div class="w-full bg-slate-100 h-2 rounded-full overflow-hidden"><div class="${colors[i]} h-full rounded-full" style="width: ${(p.qty/max)*100}%"></div></div>
                        </div>`;
                });
            }

            // Render ChartJS
            if (revenueChartObj) revenueChartObj.destroy();
            const ctx = document.getElementById('revenueChart').getContext('2d');
            revenueChartObj = new Chart(ctx, {
                type: 'line',
                data: {
                    labels: ['Sáng (8h-12h)', 'Trưa (12h-15h)', 'Chiều (15h-18h)', 'Tối (18h-22h)'],
                    datasets: [
                        { 
                            label: 'Doanh thu', 
                            data: [totalRev*0.25, totalRev*0.35, totalRev*0.20, totalRev*0.20], 
                            borderColor: '#10b981', 
                            backgroundColor: 'rgba(16, 185, 129, 0.05)',
                            tension: 0.3,
                            fill: true,
                            borderWidth: 2
                        }
                    ]
                },
                options: { 
                    responsive: true, 
                    maintainAspectRatio: false, 
                    plugins: { legend: { display: false } }, 
                    scales: { 
                        x: { grid: { display: false } },
                        y: { ticks: { callback: v => v >= 1000000 ? (v / 1000000) + 'M' : v } }
                    } 
                }
            });
        }

        // ==================== KEYBOARD SHORTCUTS FOR POS (TIỆN ÍCH PC) ====================
        window.addEventListener('keydown', function(e) {
            // Nhấn F9 để thanh toán nhanh trên Desktop
            if (e.key === 'F9') {
                e.preventDefault();
                const activeTab = document.querySelector('.tab-content:not(.hidden)');
                if (activeTab && activeTab.id === 'section-pos') {
                    checkoutCartDirect('desktop');
                }
            }
        });
    </script>
</body>
</html>

