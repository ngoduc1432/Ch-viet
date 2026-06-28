```html
<!DOCTYPE html>
<html lang="vi">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Đức Mobile ERP & POS</title>
    
    <!-- Nhúng Tailwind CSS để làm giao diện đẹp -->
    <script src="https://cdn.tailwindcss.com"></script>
    
    <!-- Nhúng FontAwesome để làm Icon -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    
    <!-- Nhúng React JS -->
    <script src="https://unpkg.com/react@18/umd/react.production.min.js" crossorigin></script>
    <script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js" crossorigin></script>
    
    <!-- Nhúng Babel để trình duyệt hiểu được code React -->
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>

    <style>
        /* Hiệu ứng mượt mà */
        .animate-fade-in { animation: fadeIn 0.3s ease-in-out; }
        .animate-fade-in-down { animation: fadeInDown 0.4s ease-out; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
        @keyframes fadeInDown { from { opacity: 0; transform: translateY(-20px); } to { opacity: 1; transform: translateY(0); } }
        /* Tùy chỉnh thanh cuộn */
        ::-webkit-scrollbar { width: 6px; height: 6px; }
        ::-webkit-scrollbar-track { background: transparent; }
        ::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 10px; }
        ::-webkit-scrollbar-thumb:hover { background: #94a3b8; }
    </style>
</head>
<body class="bg-slate-100 text-slate-800 font-sans">
    
    <!-- Nơi phần mềm sẽ hiển thị -->
    <div id="root"></div>

    <!-- CODE LOGIC PHẦN MỀM -->
    <script type="text/babel">
        const { useState, useEffect } = React;

        // ==========================================
        // 1. DỮ LIỆU HỆ THỐNG (MOCK DATA)
        // ==========================================
        const INITIAL_INVENTORY = [
          { id: 'p1', name: 'iPhone 14 Pro Max 256GB - Tím', price: 25000000, imeis: ['359990123456789', '359990123456790'] },
          { id: 'p2', name: 'Samsung Galaxy S23 Ultra - Đen', price: 21000000, imeis: ['358880123456781'] },
          { id: 'p3', name: 'AirPods Pro 2', price: 5500000, imeis: ['AP2999888777', 'AP2999888778'] },
          { id: 'p4', name: 'MacBook Air M2 8GB/256GB', price: 23500000, imeis: [] },
        ];

        const INITIAL_CUSTOMERS = [
          { id: 'c1', phone: '0901234567', name: 'Nguyễn Văn A', tier: 'VIP_VANG', points: 150000, totalSpent: 45000000, debt: 0, dob: '1990-06-27', lastPurchase: '2026-06-15' },
          { id: 'c2', phone: '0987654321', name: 'Trần Thị B', tier: 'VIP_BAC', points: 50000, totalSpent: 12000000, debt: 5000000, dob: '1995-10-10', lastPurchase: '2025-11-20' },
        ];

        const TIERS = {
          THANH_VIEN: { label: 'Thành Viên', color: 'bg-gray-100 text-gray-700', discountRate: 0 },
          VIP_BAC: { label: 'VIP Bạc', color: 'bg-slate-200 text-slate-800', discountRate: 0.01 }, 
          VIP_VANG: { label: 'VIP Vàng', color: 'bg-yellow-100 text-yellow-700', discountRate: 0.02 }, 
        };

        const ROLES = {
          ADMIN: { id: 'ADMIN', name: 'Chủ Cửa Hàng (Admin)', permissions: ['pos', 'crm', 'inventory', 'hr', 'reports'] },
          MANAGER: { id: 'MANAGER', name: 'Quản Lý Cửa Hàng', permissions: ['pos', 'crm', 'inventory', 'reports'] },
          CASHIER: { id: 'CASHIER', name: 'Thu Ngân', permissions: ['pos', 'crm'] },
          STOCK_KEEPER: { id: 'STOCK_KEEPER', name: 'Quản Lý Kho', permissions: ['inventory'] }
        };

        const EMPLOYEES = [
          { id: 'emp1', username: 'admin', password: '123', name: 'Đức Admin', role: 'ADMIN', branch: 'Toàn hệ thống', baseSalary: 20000000, commissionRate: 0, avatar: 'Đ' },
          { id: 'emp2', username: 'thungan1', password: '123', name: 'Lê Thị Thu', role: 'CASHIER', branch: 'CN1 Quận 10', baseSalary: 8000000, commissionRate: 0.01, avatar: 'T' },
          { id: 'emp3', username: 'kho1', password: '123', name: 'Trần Văn Kho', role: 'STOCK_KEEPER', branch: 'Kho Tổng', baseSalary: 9000000, commissionRate: 0, avatar: 'K' },
        ];

        const MOCK_REPORTS = {
          'ALL': { rev: 125500000, profit: 24500000, orders: 48, stockVal: 450, chartRev: [45, 60, 55, 80, 95, 75, 125.5], chartProfit: [8, 12, 10, 16, 18, 15, 24.5] },
          'CN1': { rev: 83500000, profit: 16000000, orders: 33, stockVal: 280, chartRev: [30, 40, 35, 50, 65, 45, 83.5], chartProfit: [5, 8, 7, 10, 12, 9, 16] },
        };

        const safeGetStorage = (key) => { try { return localStorage.getItem(key); } catch(e) { return null; } };
        const safeSetStorage = (key, val) => { try { localStorage.setItem(key, val); } catch(e) {} };
        const safeRemoveStorage = (key) => { try { localStorage.removeItem(key); } catch(e) {} };

        // ==========================================
        // CÁC COMPONENT GIAO DIỆN
        // ==========================================

        const POSScreen = ({ inventory, cart, setCart, customers, currentCustomer, setCurrentCustomer, showNotification, setShowCheckout }) => {
          const [scanInput, setScanInput] = useState('');
          const [phoneSearch, setPhoneSearch] = useState('');

          const handleScan = (e) => {
            e.preventDefault();
            const inputImei = scanInput.trim();
            if (!inputImei) return;
            let foundProduct = null; let foundImei = null;
            for (const prod of inventory) {
              if (prod.imeis.includes(inputImei)) { foundProduct = prod; foundImei = inputImei; break; }
            }
            if (foundProduct) {
              if (cart.some(item => item.imei === foundImei)) { showNotification('IMEI này đã có trong giỏ hàng!', 'error'); } 
              else {
                setCart(prev => [...prev, { ...foundProduct, imei: foundImei, cartItemId: Date.now() }]);
                setScanInput(''); showNotification(`Đã thêm: ${foundProduct.name}`, 'success');
              }
            } else { showNotification('Không tìm thấy IMEI trong kho!', 'error'); }
          };

          const handleAutoPickProduct = (product) => {
            const availableImeis = product.imeis.filter(imei => !cart.some(c => c.imei === imei));
            if (availableImeis.length > 0) {
              setCart(prev => [...prev, { ...product, imei: availableImeis[0], cartItemId: Date.now() }]);
              showNotification(`Đã thêm: ${product.name}`, 'success');
            } else { showNotification(`Hết hàng khả dụng!`, 'warning'); }
          };

          const searchCustomer = (e) => {
            e.preventDefault();
            const c = customers.find(c => c.phone === phoneSearch.trim());
            if (c) { setCurrentCustomer(c); showNotification(`Đã áp dụng: ${c.name}`, 'success'); setPhoneSearch(''); } 
            else { showNotification('Không tìm thấy SĐT khách hàng!', 'warning'); }
          };

          const cartTotal = cart.reduce((sum, item) => sum + item.price, 0);

          return (
            <div className="flex flex-col lg:flex-row h-full gap-6 animate-fade-in">
              <div className="flex-[7] flex flex-col gap-5 h-full min-h-[500px]">
                <div className="bg-white p-4 rounded-2xl shadow-sm border border-slate-200">
                  <form onSubmit={handleScan} className="relative">
                    <div className="absolute inset-y-0 left-0 pl-4 flex items-center pointer-events-none"><i className="fa-solid fa-barcode text-indigo-500 text-lg"></i></div>
                    <input type="text" value={scanInput} onChange={(e) => setScanInput(e.target.value)} placeholder="Quét mã vạch hoặc nhập số IMEI máy..." className="w-full pl-12 pr-4 py-3.5 bg-slate-50 border border-slate-200 rounded-xl focus:bg-white focus:ring-2 focus:ring-indigo-500 outline-none transition-all text-lg" />
                    <button type="submit" className="absolute right-2 top-2 bottom-2 bg-indigo-600 text-white px-6 rounded-lg hover:bg-indigo-700 font-medium">Thêm</button>
                  </form>
                </div>

                <div className="bg-white p-5 rounded-2xl shadow-sm border border-slate-200 flex-1 overflow-y-auto">
                   <div className="flex justify-between items-center mb-4">
                     <h2 className="text-lg font-bold text-slate-800 flex items-center"><i className="fa-solid fa-store mr-2 text-indigo-600"></i> Chọn nhanh sản phẩm</h2>
                   </div>
                   <div className="grid grid-cols-1 sm:grid-cols-2 xl:grid-cols-3 gap-4">
                    {inventory.map(prod => (
                      <div key={prod.id} className="border border-slate-200 rounded-xl p-4 hover:border-indigo-400 hover:shadow-md transition-all bg-white flex flex-col group">
                        <div className="flex justify-between items-start mb-3 cursor-pointer" onClick={() => handleAutoPickProduct(prod)}>
                          <div>
                            <h3 className="font-semibold text-slate-800 group-hover:text-indigo-600 transition-colors line-clamp-2">{prod.name}</h3>
                            <p className="text-indigo-600 font-bold mt-1">{prod.price.toLocaleString()} ₫</p>
                          </div>
                          <span className={`px-2.5 py-1 rounded-md text-xs font-bold whitespace-nowrap ml-2 ${prod.imeis.length > 0 ? 'bg-green-100 text-green-700' : 'bg-rose-100 text-rose-700'}`}>Kho: {prod.imeis.length}</span>
                        </div>
                        <div className="mt-auto border-t border-slate-100 pt-3">
                          <div className="flex flex-wrap gap-2">
                            {prod.imeis.length > 0 ? prod.imeis.map(imei => {
                              const inCart = cart.some(c => c.imei === imei);
                              return (
                                <button key={imei} disabled={inCart} className={`text-xs px-2.5 py-1.5 rounded-lg border ${inCart ? 'bg-slate-200 text-slate-400' : 'hover:border-indigo-500'}`}>{imei}</button>
                              )
                            }) : <span className="text-sm text-slate-400 italic">Tạm hết hàng</span>}
                          </div>
                        </div>
                      </div>
                    ))}
                  </div>
                </div>
              </div>

              <div className="flex-[3] bg-white rounded-2xl shadow-sm border border-slate-200 flex flex-col h-[600px] lg:h-auto overflow-hidden">
                <div className="p-4 border-b border-slate-200 bg-slate-50">
                  {currentCustomer ? (
                    <div className="flex items-center justify-between bg-indigo-50 border border-indigo-100 p-3 rounded-xl">
                        <div className="flex items-center gap-3">
                            <div className="w-10 h-10 bg-indigo-100 text-indigo-600 rounded-full flex items-center justify-center font-bold">{currentCustomer.name.charAt(0)}</div>
                            <div>
                                <p className="font-bold text-slate-800 text-sm flex items-center gap-1">{currentCustomer.name}</p>
                                <div className="flex gap-2 mt-0.5">
                                    <span className={`text-[10px] px-2 py-0.5 rounded-md font-bold ${TIERS[currentCustomer.tier].color}`}>{TIERS[currentCustomer.tier].label}</span>
                                    <span className="text-[10px] bg-white border border-indigo-100 text-indigo-600 px-2 py-0.5 rounded-md font-bold">{currentCustomer.points.toLocaleString()} pts</span>
                                </div>
                            </div>
                        </div>
                        <button onClick={() => setCurrentCustomer(null)} className="text-slate-400 hover:text-rose-500"><i className="fa-solid fa-xmark text-lg"></i></button>
                    </div>
                  ) : (
                    <form onSubmit={searchCustomer} className="flex gap-2 relative">
                      <div className="relative flex-1">
                        <div className="absolute inset-y-0 left-0 pl-3 flex items-center pointer-events-none"><i className="fa-solid fa-phone text-slate-400"></i></div>
                        <input type="text" value={phoneSearch} onChange={e=>setPhoneSearch(e.target.value)} placeholder="Tìm SĐT khách (VD: 0901234567)" className="w-full pl-9 pr-3 py-2.5 bg-white border border-slate-300 rounded-xl outline-none text-sm" />
                      </div>
                      <button type="submit" className="bg-slate-800 text-white px-4 rounded-xl text-sm font-medium">Tìm</button>
                    </form>
                  )}
                </div>

                <div className="px-5 py-3 border-b border-slate-100 bg-white flex justify-between items-center">
                  <h2 className="font-bold text-slate-800">Giỏ hàng</h2>
                  <span className="bg-slate-100 text-slate-600 text-xs px-2.5 py-1 rounded-full font-bold">{cart.length}</span>
                </div>

                <div className="flex-1 overflow-y-auto p-4 space-y-3 bg-slate-50/50">
                  {cart.length === 0 ? (
                    <div className="h-full flex flex-col items-center justify-center text-slate-400">
                      <i className="fa-solid fa-cart-shopping text-4xl mb-3 text-slate-200"></i>
                      <p className="text-sm font-medium">Chưa có sản phẩm nào</p>
                    </div>
                  ) : (
                    cart.map(item => (
                      <div key={item.cartItemId} className="bg-white p-3 rounded-xl border border-slate-200 shadow-sm flex gap-3 relative group">
                        <div className="flex-1 min-w-0 pr-8">
                          <h4 className="font-semibold text-slate-800 text-sm truncate">{item.name}</h4>
                          <p className="text-xs text-slate-500 mt-1">IMEI: <span className="font-mono bg-slate-100 px-1 rounded">{item.imei}</span></p>
                          <p className="font-bold text-indigo-600 mt-1.5">{item.price.toLocaleString()} ₫</p>
                        </div>
                        <button onClick={() => setCart(cart.filter(i => i.cartItemId !== item.cartItemId))} className="absolute right-3 top-1/2 -translate-y-1/2 text-rose-400 p-2"><i className="fa-solid fa-trash-can"></i></button>
                      </div>
                    ))
                  )}
                </div>

                <div className="p-5 bg-white border-t border-slate-200">
                   <div className="flex justify-between items-center mb-4">
                    <span className="text-slate-500 text-sm font-medium">Tổng tiền:</span>
                    <span className="text-xl font-bold text-slate-800">{cartTotal.toLocaleString()} ₫</span>
                  </div>
                  <button onClick={() => setShowCheckout(true)} disabled={cart.length === 0} className={`w-full py-4 rounded-xl font-bold text-base flex items-center justify-center gap-2 ${cart.length === 0 ? 'bg-slate-100 text-slate-400 cursor-not-allowed' : 'bg-indigo-600 text-white shadow-md hover:bg-indigo-700'}`}>
                    <i className="fa-solid fa-money-bill-wave"></i> XÁC NHẬN THANH TOÁN
                  </button>
                </div>
              </div>
            </div>
          );
        };

        const CheckoutModal = ({ show, onClose, cart, setCart, currentCustomer, setCurrentCustomer, inventory, setInventory, customers, setCustomers, showNotification, isOnline, offlineOrders, setOfflineOrders }) => {
          const [paymentMethod, setPaymentMethod] = useState('QR');
          const [amountGiven, setAmountGiven] = useState(0);
          const [voucherCode, setVoucherCode] = useState('');
          const [usePoints, setUsePoints] = useState(false);

          useEffect(() => { if(show) { setPaymentMethod('QR'); setAmountGiven(0); setVoucherCode(''); setUsePoints(false); } }, [show]);

          if (!show) return null;

          const cartTotal = cart.reduce((sum, item) => sum + item.price, 0);
          const tierDiscount = currentCustomer ? cartTotal * TIERS[currentCustomer.tier].discountRate : 0;
          const pointsDiscount = usePoints && currentCustomer ? currentCustomer.points : 0;
          const voucherDiscount = voucherCode.toUpperCase() === 'DUC500K' ? 500000 : 0;
          const totalDiscount = tierDiscount + pointsDiscount + voucherDiscount;
          const finalTotal = Math.max(0, cartTotal - totalDiscount);
          
          const confirmPayment = () => {
            const cartImeis = cart.map(item => item.imei);
            setInventory(inventory.map(prod => ({...prod, imeis: prod.imeis.filter(imei => !cartImeis.includes(imei))})));
            if (currentCustomer) {
                const pointsEarned = Math.floor(finalTotal * 0.01); 
                setCustomers(customers.map(c => c.id === currentCustomer.id ? { ...c, totalSpent: c.totalSpent + finalTotal, points: (usePoints ? 0 : c.points) + pointsEarned } : c));
            }
            if(!isOnline) {
                setOfflineOrders([...offlineOrders, { id: Date.now(), items: cart, total: finalTotal }]);
                showNotification('Mất mạng! Đã lưu đơn Offline.', 'warning');
            } else { showNotification('Thanh toán & In hóa đơn thành công!', 'success'); }
            setCart([]); setCurrentCustomer(null); onClose();
          };

          return (
            <div className="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-50 flex items-center justify-center p-4">
              <div className="bg-white w-full max-w-4xl rounded-2xl shadow-2xl overflow-hidden flex flex-col md:flex-row animate-fade-in-down">
                <div className="w-full md:w-5/12 bg-slate-50 p-6 border-r border-slate-200 flex flex-col h-full max-h-[90vh] overflow-y-auto">
                  <div className="flex justify-between items-center mb-6">
                      <h2 className="text-xl font-bold text-slate-800">Thanh toán</h2>
                      <button onClick={onClose} className="text-slate-400 hover:text-rose-500"><i className="fa-solid fa-xmark text-xl"></i></button>
                  </div>
                  <div className="space-y-4 text-sm mb-6 flex-1">
                      <div className="flex justify-between text-slate-600"><span>Tổng tiền hàng:</span><span className="font-semibold">{cartTotal.toLocaleString()} đ</span></div>
                      {currentCustomer && (
                          <div className="bg-white p-4 rounded-xl border border-blue-100 shadow-sm relative">
                              <div className="absolute top-0 right-0 bg-blue-100 text-blue-700 text-[10px] px-2 py-1 font-bold rounded-bl-lg">CRM APPLIED</div>
                              <p className="font-bold text-slate-800 flex items-center mb-2"><i className="fa-solid fa-star text-yellow-500 mr-2"></i>{currentCustomer.name}</p>
                              {tierDiscount > 0 && <div className="flex justify-between text-emerald-600 font-medium text-xs mt-1"><span>Giảm hạng {TIERS[currentCustomer.tier].label}:</span><span>- {tierDiscount.toLocaleString()} đ</span></div>}
                              {currentCustomer.points > 0 && (
                                  <div className="mt-3 pt-3 border-t border-slate-100">
                                      <label className="flex items-center cursor-pointer">
                                          <input type="checkbox" checked={usePoints} onChange={() => setUsePoints(!usePoints)} className="rounded text-indigo-600 w-4 h-4 mr-2" />
                                          <span className="text-slate-700 font-medium flex-1">Dùng {currentCustomer.points.toLocaleString()} điểm</span>
                                          {usePoints && <span className="text-emerald-600 font-bold">- {currentCustomer.points.toLocaleString()} đ</span>}
                                      </label>
                                  </div>
                              )}
                          </div>
                      )}
                      <div className="bg-white p-3 rounded-lg border border-slate-200">
                          <label className="text-xs font-semibold text-slate-500 mb-2 flex items-center"><i className="fa-solid fa-ticket mr-1"></i> Mã Voucher</label>
                          <input type="text" value={voucherCode} onChange={e=>setVoucherCode(e.target.value)} placeholder="Nhập DUC500K..." className="w-full border rounded-md px-2 py-1.5 text-sm uppercase focus:ring-indigo-500 outline-none" />
                      </div>
                  </div>
                  <div className="border-t border-slate-200 pt-4 mt-auto">
                      <div className="flex justify-between text-slate-500 text-sm mb-1"><span>Tổng giảm trừ:</span><span className="font-semibold text-emerald-600">- {totalDiscount.toLocaleString()} đ</span></div>
                      <div className="flex justify-between items-end mt-2"><p className="text-slate-700 font-bold text-lg">Khách trả:</p><p className="text-3xl font-black text-indigo-700">{finalTotal.toLocaleString()} ₫</p></div>
                  </div>
                </div>
                
                <div className="w-full md:w-7/12 p-6 bg-white flex flex-col">
                  <h3 className="font-bold text-slate-800 mb-4">Phương thức thanh toán</h3>
                  <div className="flex gap-2 p-1 bg-slate-100 rounded-lg mb-6">
                      {[{ id: 'QR', label: 'Mã QR', icon: 'fa-qrcode' }, { id: 'CASH', label: 'Tiền mặt', icon: 'fa-money-bill-wave' }, { id: 'CARD', label: 'Máy POS', icon: 'fa-credit-card' }].map(method => (
                          <button key={method.id} onClick={() => { setPaymentMethod(method.id); setAmountGiven(method.id === 'CASH' ? finalTotal : 0); }} className={`flex-1 flex flex-col items-center justify-center py-3 rounded-md text-sm font-semibold transition-all ${paymentMethod === method.id ? 'bg-white text-indigo-600 shadow-sm border border-slate-200' : 'text-slate-500 hover:bg-slate-200'}`}>
                              <i className={`fa-solid ${method.icon} text-lg mb-1`}></i> {method.label}
                          </button>
                      ))}
                  </div>
                  <div className="flex-1 flex flex-col items-center justify-center bg-slate-50 rounded-xl border border-slate-200 p-6 mb-6">
                      {paymentMethod === 'QR' && (
                          <div className="text-center">
                              <div className="bg-white p-6 rounded-xl border-2 border-indigo-500 shadow-lg mb-3 mx-auto"><i className="fa-solid fa-qrcode text-6xl text-slate-800"></i></div>
                              <p className="text-sm font-semibold text-slate-700">Khách quét mã thanh toán</p>
                          </div>
                      )}
                      {paymentMethod === 'CASH' && (
                          <div className="w-full">
                              <label className="block text-sm font-semibold text-slate-700 mb-2">Tiền khách đưa (VNĐ)</label>
                              <input type="number" value={amountGiven || ''} onChange={(e) => setAmountGiven(Number(e.target.value))} className="w-full p-4 text-2xl font-bold bg-white border border-slate-300 rounded-xl outline-none" />
                              <div className="mt-6 flex justify-between items-center p-4 bg-white rounded-xl border border-slate-200">
                                  <span className="text-slate-600 font-medium">Tiền thối lại:</span>
                                  <span className={`text-xl font-bold ${(amountGiven - finalTotal) < 0 ? 'text-rose-500' : 'text-emerald-600'}`}>{(amountGiven - finalTotal) < 0 ? 'Khách đưa thiếu!' : `${(amountGiven - finalTotal).toLocaleString()} ₫`}</span>
                              </div>
                          </div>
                      )}
                      {paymentMethod === 'CARD' && (
                          <div className="text-center"><i className="fa-solid fa-credit-card text-5xl text-indigo-500 mb-4"></i><p className="font-semibold text-slate-700">Yêu cầu quẹt thẻ qua POS</p></div>
                      )}
                  </div>
                  <button onClick={confirmPayment} disabled={paymentMethod === 'CASH' && amountGiven < finalTotal} className={`w-full py-4 rounded-xl font-bold text-lg text-white shadow-md transition-all ${(paymentMethod === 'CASH' && amountGiven < finalTotal) ? 'bg-slate-400 cursor-not-allowed' : 'bg-indigo-600 hover:bg-indigo-700'}`}>
                      {isOnline ? 'HOÀN TẤT GIAO DỊCH' : 'LƯU OFFLINE & HOÀN TẤT'}
                  </button>
                </div>
              </div>
            </div>
          );
        };

        const CRMScreen = ({ customers }) => (
          <div className="bg-white p-6 rounded-2xl shadow-sm border border-slate-200 min-h-[70vh] animate-fade-in">
              <div className="flex justify-between items-center mb-6">
                  <h2 className="text-2xl font-bold text-slate-800"><i className="fa-solid fa-users text-indigo-600 mr-2"></i> Khách hàng (CRM)</h2>
              </div>
              <div className="overflow-x-auto border border-slate-200 rounded-xl">
                  <table className="w-full text-left">
                      <thead className="bg-slate-50 border-b border-slate-200">
                          <tr>
                              <th className="py-3 px-4 text-xs font-bold text-slate-500 uppercase">Khách hàng</th>
                              <th className="py-3 px-4 text-xs font-bold text-slate-500 uppercase">SĐT</th>
                              <th className="py-3 px-4 text-xs font-bold text-slate-500 uppercase text-center">Hạng thẻ & Điểm</th>
                              <th className="py-3 px-4 text-xs font-bold text-slate-500 uppercase text-right">Tổng chi tiêu</th>
                          </tr>
                      </thead>
                      <tbody className="divide-y divide-slate-100">
                          {customers.map(c => (
                              <tr key={c.id} className="hover:bg-slate-50">
                                  <td className="py-4 px-4 font-bold text-slate-800">{c.name}</td>
                                  <td className="py-4 px-4 font-mono text-sm text-slate-600">{c.phone}</td>
                                  <td className="py-4 px-4 text-center">
                                      <span className={`inline-block px-2 py-1 rounded text-xs font-bold mb-1 ${TIERS[c.tier].color}`}>{TIERS[c.tier].label}</span>
                                      <div className="text-xs font-semibold text-indigo-600">{c.points.toLocaleString()} pts</div>
                                  </td>
                                  <td className="py-4 px-4 text-right font-semibold text-slate-700">{c.totalSpent.toLocaleString()} ₫</td>
                              </tr>
                          ))}
                      </tbody>
                  </table>
              </div>
          </div>
        );

        const InventoryScreen = ({ inventory }) => (
          <div className="bg-white p-6 rounded-2xl shadow-sm border border-slate-200 min-h-[70vh] animate-fade-in">
            <div className="flex justify-between items-center mb-6">
                <h2 className="text-2xl font-bold text-slate-800"><i className="fa-solid fa-box text-indigo-600 mr-2"></i> Quản lý Kho IMEI</h2>
            </div>
            <div className="overflow-x-auto border border-slate-200 rounded-xl">
                <table className="w-full text-left">
                    <thead className="bg-slate-50 border-b border-slate-200">
                        <tr>
                            <th className="py-3 px-4 text-xs font-bold text-slate-500 uppercase">Sản phẩm</th>
                            <th className="py-3 px-4 text-xs font-bold text-slate-500 uppercase text-center w-24">Tồn</th>
                            <th className="py-3 px-4 text-xs font-bold text-slate-500 uppercase">Danh sách IMEI</th>
                        </tr>
                    </thead>
                    <tbody className="divide-y divide-slate-100">
                        {inventory.map(prod => (
                            <tr key={prod.id} className="hover:bg-slate-50">
                                <td className="py-4 px-4 font-bold text-slate-800">{prod.name}</td>
                                <td className="py-4 px-4 text-center"><span className="px-2 py-1 rounded-md text-sm font-bold bg-green-100 text-green-700">{prod.imeis.length}</span></td>
                                <td className="py-4 px-4">
                                    <div className="flex flex-wrap gap-2">
                                        {prod.imeis.length > 0 ? prod.imeis.map(imei => <div key={imei} className="bg-white border border-slate-200 rounded-md px-2 py-1 text-xs font-mono">{imei}</div>) : <span className="text-sm text-slate-400 italic">Trống</span>}
                                    </div>
                                </td>
                            </tr>
                        ))}
                    </tbody>
                </table>
            </div>
          </div>
        );

        const ReportsScreen = () => {
          const data = MOCK_REPORTS['ALL'];
          const maxVal = Math.max(...data.chartRev) * 1.2; 
          const labels = ['21/06', '22/06', '23/06', '24/06', '25/06', '26/06', 'Hôm nay'];

          return (
            <div className="flex flex-col h-full gap-6 animate-fade-in overflow-y-auto pb-10">
              <div className="flex justify-between items-center bg-white p-6 rounded-2xl shadow-sm border border-slate-200">
                  <h2 className="text-2xl font-bold text-slate-800"><i className="fa-solid fa-chart-column text-indigo-600 mr-2"></i> Báo Cáo & Thống Kê</h2>
              </div>
              <div className="grid grid-cols-1 md:grid-cols-2 xl:grid-cols-4 gap-4">
                  <div className="bg-white p-5 rounded-2xl shadow-sm border border-slate-200">
                      <p className="text-sm font-semibold text-slate-500 mb-1">Doanh thu</p><p className="text-2xl font-black text-indigo-600">{data.rev.toLocaleString()} ₫</p>
                  </div>
                  <div className="bg-white p-5 rounded-2xl shadow-sm border border-slate-200">
                      <p className="text-sm font-semibold text-slate-500 mb-1">Lợi nhuận gộp</p><p className="text-2xl font-black text-emerald-600">{data.profit.toLocaleString()} ₫</p>
                  </div>
                  <div className="bg-white p-5 rounded-2xl shadow-sm border border-slate-200">
                      <p className="text-sm font-semibold text-slate-500 mb-1">Đơn hoàn tất</p><p className="text-2xl font-black text-amber-500">{data.orders} đơn</p>
                  </div>
                  <div className="bg-white p-5 rounded-2xl shadow-sm border border-slate-200">
                      <p className="text-sm font-semibold text-slate-500 mb-1">Tổng Vốn Tồn Kho</p><p className="text-2xl font-black text-purple-600">{data.stockVal} Triệu</p>
                  </div>
              </div>
              <div className="bg-white p-6 rounded-2xl shadow-sm border border-slate-200">
                  <h3 className="font-bold text-slate-800 mb-6">Biểu đồ Kép (Doanh thu & Lợi nhuận)</h3>
                  <div className="flex items-end h-72 gap-2 mt-4 pt-4 border-l border-b border-slate-200 relative pb-1 pl-1">
                      <div className="absolute w-full border-t border-slate-100 border-dashed" style={{ top: '0%' }}></div>
                      <div className="absolute w-full border-t border-slate-100 border-dashed" style={{ top: '25%' }}></div>
                      <div className="absolute w-full border-t border-slate-100 border-dashed" style={{ top: '50%' }}></div>
                      <div className="absolute w-full border-t border-slate-100 border-dashed" style={{ top: '75%' }}></div>
                      {labels.map((label, i) => (
                          <div key={i} className="flex-1 flex flex-col justify-end items-center z-10 h-full pb-6 relative group">
                              <div className="flex items-end gap-1 w-full h-full justify-center">
                                  <div className="w-[35%] bg-indigo-500 rounded-t-md" style={{ height: `${(data.chartRev[i] / maxVal) * 100}%` }}></div>
                                  <div className="w-[35%] bg-emerald-400 rounded-t-md" style={{ height: `${(data.chartProfit[i] / maxVal) * 100}%` }}></div>
                              </div>
                              <span className="text-[10px] sm:text-xs text-slate-500 font-medium absolute bottom-0">{label}</span>
                          </div>
                      ))}
                  </div>
              </div>
            </div>
          );
        };


        // ==========================================
        // MAIN APP COMPONENT
        // ==========================================
        const DucMobileApp = () => {
          const [isAuthenticated, setIsAuthenticated] = useState(false);
          const [currentUser, setCurrentUser] = useState(null);
          const [username, setUsername] = useState('');
          const [password, setPassword] = useState('');
          const [loginError, setLoginError] = useState('');
          const [rememberMe, setRememberMe] = useState(false);
          
          const [activeTab, setActiveTab] = useState('pos');
          const [isOnline, setIsOnline] = useState(true);
          const [inventory, setInventory] = useState(INITIAL_INVENTORY);
          const [customers, setCustomers] = useState(INITIAL_CUSTOMERS);
          const [cart, setCart] = useState([]);
          const [offlineOrders, setOfflineOrders] = useState([]);
          const [notification, setNotification] = useState(null);
          
          const [currentCustomer, setCurrentCustomer] = useState(null);
          const [showCheckout, setShowCheckout] = useState(false);

          const showNotification = (msg, type = 'success') => {
            setNotification({ msg, type });
            setTimeout(() => setNotification(null), 3000);
          };

          useEffect(() => {
            const savedSession = safeGetStorage('duc_mobile_session');
            if (savedSession) {
              try {
                const user = JSON.parse(savedSession);
                const validUser = EMPLOYEES.find(e => e.id === user.id);
                if (validUser) {
                  setCurrentUser(validUser); setIsAuthenticated(true);
                  setActiveTab(validUser.role === 'STOCK_KEEPER' ? 'inventory' : validUser.role === 'ADMIN' ? 'reports' : 'pos');
                }
              } catch (e) { safeRemoveStorage('duc_mobile_session'); }
            }
          }, []);

          const handleLogin = (e) => {
            e.preventDefault(); setLoginError('');
            const user = EMPLOYEES.find(e => e.username === username && e.password === password);
            if (user) {
              setCurrentUser(user); setIsAuthenticated(true);
              if (rememberMe) safeSetStorage('duc_mobile_session', JSON.stringify({ id: user.id, username: user.username, role: user.role }));
              setActiveTab(user.role === 'STOCK_KEEPER' ? 'inventory' : user.role === 'ADMIN' ? 'reports' : 'pos');
              showNotification(`Đăng nhập thành công!`);
            } else { setLoginError('Sai tài khoản hoặc mật khẩu!'); }
          };

          const handleLogout = () => { setIsAuthenticated(false); setCurrentUser(null); safeRemoveStorage('duc_mobile_session'); };
          const hasPermission = (tabId) => currentUser && ROLES[currentUser.role]?.permissions.includes(tabId);

          if (!isAuthenticated) {
            return (
              <div className="min-h-screen flex items-center justify-center p-4 bg-slate-900">
                <div className="w-full max-w-md bg-white rounded-3xl shadow-2xl overflow-hidden animate-fade-in-down">
                  <div className="bg-slate-800 p-8 text-center text-white">
                     <i className="fa-solid fa-mobile-screen-button text-4xl text-indigo-400 mb-3"></i>
                     <h1 className="text-3xl font-black">ĐỨC<span className="text-indigo-400">MOBILE</span></h1>
                     <p className="text-sm mt-2 opacity-70">Hệ thống ERP & POS Bản Offline</p>
                  </div>
                  <div className="p-8">
                    {loginError && <div className="text-rose-600 bg-rose-50 p-3 rounded-lg text-sm mb-4"><i className="fa-solid fa-circle-exclamation"></i> {loginError}</div>}
                    <form onSubmit={handleLogin}>
                      <input type="text" value={username} onChange={e => setUsername(e.target.value)} placeholder="Tài khoản (admin / thungan1 / kho1)" className="w-full mb-4 px-4 py-3 bg-slate-50 border rounded-xl" required />
                      <input type="password" value={password} onChange={e => setPassword(e.target.value)} placeholder="Mật khẩu (123)" className="w-full mb-6 px-4 py-3 bg-slate-50 border rounded-xl" required />
                      <button type="submit" className="w-full bg-indigo-600 text-white font-bold py-3.5 rounded-xl hover:bg-indigo-700"><i className="fa-solid fa-right-to-bracket mr-2"></i> ĐĂNG NHẬP</button>
                    </form>
                  </div>
                </div>
              </div>
            );
          }

          return (
            <div className="min-h-screen flex flex-col pb-16 md:pb-0">
              {/* Header */}
              <header className="bg-slate-900 text-white px-6 py-4 shadow-md flex justify-between items-center z-40">
                <div className="flex items-center gap-8">
                  <h1 className="text-xl font-black">ĐỨC<span className="text-indigo-400">MOBILE</span></h1>
                  <nav className="hidden md:flex bg-slate-800 p-1 rounded-lg">
                    {hasPermission('reports') && <button onClick={() => setActiveTab('reports')} className={`px-4 py-2 rounded-md text-sm font-medium ${activeTab === 'reports' ? 'bg-indigo-600 text-white' : 'text-slate-300 hover:text-white'}`}><i className="fa-solid fa-chart-column mr-2"></i> Báo cáo</button>}
                    {hasPermission('pos') && <button onClick={() => setActiveTab('pos')} className={`px-4 py-2 rounded-md text-sm font-medium ${activeTab === 'pos' ? 'bg-indigo-600 text-white' : 'text-slate-300 hover:text-white'}`}><i className="fa-solid fa-cart-shopping mr-2"></i> Bán hàng</button>}
                    {hasPermission('crm') && <button onClick={() => setActiveTab('crm')} className={`px-4 py-2 rounded-md text-sm font-medium ${activeTab === 'crm' ? 'bg-indigo-600 text-white' : 'text-slate-300 hover:text-white'}`}><i className="fa-solid fa-users mr-2"></i> Khách hàng</button>}
                    {hasPermission('inventory') && <button onClick={() => setActiveTab('inventory')} className={`px-4 py-2 rounded-md text-sm font-medium ${activeTab === 'inventory' ? 'bg-indigo-600 text-white' : 'text-slate-300 hover:text-white'}`}><i className="fa-solid fa-box mr-2"></i> Kho hàng</button>}
                  </nav>
                </div>
                <div className="flex items-center gap-4">
                  <button onClick={() => setIsOnline(!isOnline)} className={`hidden sm:block px-3 py-1 text-xs rounded-full border ${isOnline ? 'text-emerald-400 border-emerald-500' : 'text-rose-400 border-rose-500'}`}>{isOnline ? 'ONLINE' : 'OFFLINE'}</button>
                  <div className="flex items-center gap-3">
                    <span className="text-sm font-semibold">{currentUser.name}</span>
                    <button onClick={handleLogout} className="bg-rose-500 text-white p-2 rounded-lg text-xs" title="Đăng xuất"><i className="fa-solid fa-arrow-right-from-bracket"></i></button>
                  </div>
                </div>
              </header>

              {/* Main Content */}
              <main className="flex-1 p-4 md:p-6 max-w-[1600px] w-full mx-auto h-[calc(100vh-140px)] md:h-[calc(100vh-72px)] overflow-hidden">
                {activeTab === 'reports' && hasPermission('reports') && <ReportsScreen />}
                {activeTab === 'pos' && hasPermission('pos') && <POSScreen inventory={inventory} cart={cart} setCart={setCart} customers={customers} currentCustomer={currentCustomer} setCurrentCustomer={setCurrentCustomer} showNotification={showNotification} setShowCheckout={setShowCheckout} />}
                {activeTab === 'crm' && hasPermission('crm') && <CRMScreen customers={customers} />}
                {activeTab === 'inventory' && hasPermission('inventory') && <InventoryScreen inventory={inventory} />}
              </main>

              {/* Mobile Nav */}
              <div className="md:hidden fixed bottom-0 w-full bg-white border-t border-slate-200 flex z-40">
                  {hasPermission('reports') && <button onClick={() => setActiveTab('reports')} className={`flex-1 py-3 flex flex-col items-center text-[10px] font-bold ${activeTab === 'reports' ? 'text-indigo-600 bg-indigo-50' : 'text-slate-500'}`}><i className="fa-solid fa-chart-column text-lg mb-1"></i> Báo cáo</button>}
                  {hasPermission('pos') && <button onClick={() => setActiveTab('pos')} className={`flex-1 py-3 flex flex-col items-center text-[10px] font-bold ${activeTab === 'pos' ? 'text-indigo-600 bg-indigo-50' : 'text-slate-500'}`}><i className="fa-solid fa-cart-shopping text-lg mb-1"></i> Bán hàng</button>}
                  {hasPermission('crm') && <button onClick={() => setActiveTab('crm')} className={`flex-1 py-3 flex flex-col items-center text-[10px] font-bold ${activeTab === 'crm' ? 'text-indigo-600 bg-indigo-50' : 'text-slate-500'}`}><i className="fa-solid fa-users text-lg mb-1"></i> Khách</button>}
                  {hasPermission('inventory') && <button onClick={() => setActiveTab('inventory')} className={`flex-1 py-3 flex flex-col items-center text-[10px] font-bold ${activeTab === 'inventory' ? 'text-indigo-600 bg-indigo-50' : 'text-slate-500'}`}><i className="fa-solid fa-box text-lg mb-1"></i> Kho</button>}
              </div>

              {/* Modal & Toast */}
              <CheckoutModal show={showCheckout} onClose={() => setShowCheckout(false)} cart={cart} setCart={setCart} currentCustomer={currentCustomer} setCurrentCustomer={setCurrentCustomer} inventory={inventory} setInventory={setInventory} customers={customers} setCustomers={setCustomers} showNotification={showNotification} isOnline={isOnline} offlineOrders={offlineOrders} setOfflineOrders={setOfflineOrders} />
              
              {notification && (
                <div className="fixed top-6 right-6 z-50 animate-fade-in-down bg-slate-800 text-white px-5 py-3 rounded-lg shadow-xl font-medium flex items-center">
                    <i className="fa-solid fa-bell mr-2 text-indigo-400"></i> {notification.msg}
                </div>
              )}
            </div>
          );
        }

        // Render App
        const root = ReactDOM.createRoot(document.getElementById('root'));
        root.render(<DucMobileApp />);
    </script>
</body>
</html>

```
