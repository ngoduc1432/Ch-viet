<!DOCTYPE html>
<html lang="vi">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>PhoneShop Pro - Single HTML</title>
  
  <script crossorigin src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
  <script crossorigin src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
  
  <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>

  <style>
    body {
      margin: 0;
      padding: 0;
      font-family: 'Inter', -apple-system, sans-serif;
      background: #0B1629;
      color: #E8EDF5;
    }
  </style>
</head>
<body>
  <div id="root"></div>

  <script type="text/babel">
    // Lấy các hooks từ biến global React thay vì import
    const { useState, useEffect, useRef, useCallback } = React;

    // ⚠️ QUAN TRỌNG: Nhập API Key của Claude vào đây. 
    // Lưu ý: Đặt Key trong file HTML tĩnh có rủi ro lộ Key nếu bạn đưa lên mạng public.
    const ANTHROPIC_API_KEY = ""; 

    // ─── STORAGE ───────────────────────────────────────────────────────────
    const SKEY = "phoneshop_shared_v2";
    async function saveShared(data) {
      try { localStorage.setItem(SKEY, JSON.stringify(data)); } catch {}
    }
    async function loadShared() {
      try {
        const r = localStorage.getItem(SKEY);
        return r ? JSON.parse(r) : null;
      } catch { return null; }
    }
    async function deleteShared() {
      try { localStorage.removeItem(SKEY); } catch {}
    }

    // ─── INITIAL DATA ────────────────────────────────────────────────────────
    const IP = [
      { id:1,sku:"IPH15PM-256-BLK",name:"iPhone 15 Pro Max 256GB",category:"iPhone",brand:"Apple",color:"Đen Titan",price:34990000,costPrice:28000000,stock:12,minStock:3,sold:47,image:"📱" },
      { id:2,sku:"SAM-S24U-512-WHT",name:"Samsung S24 Ultra 512GB",category:"Samsung",brand:"Samsung",color:"Trắng Bạch Kim",price:31990000,costPrice:25000000,stock:8,minStock:3,sold:35,image:"📱" },
      { id:3,sku:"OPP-FIND7-256-BLK",name:"OPPO Find X7 256GB",category:"OPPO",brand:"OPPO",color:"Đen Bóng",price:22990000,costPrice:17500000,stock:2,minStock:3,sold:28,image:"📱" },
      { id:4,sku:"XIA-14U-256-PUR",name:"Xiaomi 14 Ultra 256GB",category:"Xiaomi",brand:"Xiaomi",color:"Tím Titan",price:23490000,costPrice:18000000,stock:15,minStock:3,sold:22,image:"📱" },
      { id:5,sku:"VIV-X100P-256-BLU",name:"Vivo X100 Pro 256GB",category:"Vivo",brand:"Vivo",color:"Xanh Biển",price:19990000,costPrice:15500000,stock:6,minStock:3,sold:19,image:"📱" },
      { id:6,sku:"IPH15-128-BLU",name:"iPhone 15 128GB",category:"iPhone",brand:"Apple",color:"Xanh Lam",price:22490000,costPrice:17800000,stock:20,minStock:5,sold:62,image:"📱" },
      { id:7,sku:"SAM-A55-256-LVD",name:"Samsung A55 256GB",category:"Samsung",brand:"Samsung",color:"Xanh Tím Oải Hương",price:9990000,costPrice:7200000,stock:25,minStock:5,sold:88,image:"📱" },
      { id:8,sku:"ACC-CASE-MG-BLK",name:"Ốp lưng MagSafe iPhone 15",category:"Phụ kiện",brand:"Apple",color:"Đen",price:890000,costPrice:300000,stock:45,minStock:10,sold:120,image:"🛡️" },
      { id:9,sku:"ACC-CHG-65W-WHT",name:"Sạc nhanh 65W GaN",category:"Phụ kiện",brand:"Anker",color:"Trắng",price:690000,costPrice:220000,stock:38,minStock:10,sold:95,image:"🔌" },
      { id:10,sku:"ACC-EAR-AIRP3-WHT",name:"AirPods 3 (Lightning)",category:"Tai nghe",brand:"Apple",color:"Trắng",price:4490000,costPrice:3200000,stock:18,minStock:5,sold:41,image:"🎧" },
    ];
    const IC = [
      { id:1,name:"Nguyễn Văn An",phone:"0912345678",email:"an.nguyen@gmail.com",group:"VIP",points:2450,totalSpent:68900000,orders:5,lastVisit:"2025-06-20" },
      { id:2,name:"Trần Thị Bình",phone:"0987654321",email:"binh.tran@gmail.com",group:"Thân thiết",points:890,totalSpent:23400000,orders:3,lastVisit:"2025-06-18" },
      { id:3,name:"Lê Minh Cường",phone:"0909123456",email:"cuong.le@gmail.com",group:"Đại lý",points:5200,totalSpent:145000000,orders:18,lastVisit:"2025-06-22" },
      { id:4,name:"Phạm Thị Dung",phone:"0978234567",email:"dung.pham@gmail.com",group:"Khách lẻ",points:120,totalSpent:3200000,orders:1,lastVisit:"2025-06-15" },
      { id:5,name:"Hoàng Văn Em",phone:"0965432109",email:"em.hoang@gmail.com",group:"Thân thiết",points:1340,totalSpent:34500000,orders:4,lastVisit:"2025-06-21" },
    ];
    const IO = [
      { id:"HD001",customer:"Nguyễn Văn An",items:[{name:"iPhone 15 Pro Max 256GB",qty:1,price:34990000}],total:34990000,payment:"Thẻ",status:"Hoàn thành",date:"2025-06-22 09:15" },
      { id:"HD002",customer:"Trần Thị Bình",items:[{name:"Samsung A55 256GB",qty:1,price:9990000},{name:"Ốp lưng MagSafe",qty:1,price:890000}],total:10880000,payment:"QR",status:"Hoàn thành",date:"2025-06-22 10:32" },
      { id:"HD003",customer:"Khách lẻ",items:[{name:"Sạc nhanh 65W GaN",qty:2,price:1380000}],total:1380000,payment:"Tiền mặt",status:"Hoàn thành",date:"2025-06-22 11:05" },
      { id:"HD004",customer:"Lê Minh Cường",items:[{name:"Samsung S24 Ultra 512GB",qty:2,price:63980000}],total:63980000,payment:"Chuyển khoản",status:"Hoàn thành",date:"2025-06-22 13:20" },
      { id:"HD005",customer:"Hoàng Văn Em",items:[{name:"AirPods 3",qty:1,price:4490000}],total:4490000,payment:"QR",status:"Đang xử lý",date:"2025-06-22 14:48" },
    ];
    const STAFF=[
      {id:1,name:"Nguyễn Thị Mai",role:"Quản lý",sales:145000000,commission:2.5,workDays:22,salary:12000000},
      {id:2,name:"Trần Văn Hùng",role:"Nhân viên bán hàng",sales:89000000,commission:1.5,workDays:22,salary:8000000},
      {id:3,name:"Phạm Thị Lan",role:"Nhân viên bán hàng",sales:67000000,commission:1.5,workDays:20,salary:8000000},
      {id:4,name:"Lê Văn Đức",role:"Kế toán",sales:0,commission:0,workDays:22,salary:10000000},
    ];
    const RD=[
      {month:"T1",revenue:245000000,cost:185000000},{month:"T2",revenue:198000000,cost:148000000},
      {month:"T3",revenue:312000000,cost:235000000},{month:"T4",revenue:287000000,cost:215000000},
      {month:"T5",revenue:356000000,cost:268000000},{month:"T6",revenue:298000000,cost:223000000},
    ];

    // ─── HELPERS ───────────────────────────────────────────────────────────
    const fmt=(n)=>new Intl.NumberFormat("vi-VN").format(n)+"đ";
    const fmtS=(n)=>{if(n>=1e9)return(n/1e9).toFixed(1)+"tỷ";if(n>=1e6)return(n/1e6).toFixed(0)+"tr";return new Intl.NumberFormat("vi-VN").format(n);};

    // ─── MINI BAR CHART ──────────────────────────────────────────────────────
    function MiniBar({data}){
      const max=Math.max(...data.map(d=>d.revenue));
      return(
        <div style={{display:"flex",alignItems:"flex-end",gap:6,height:80,padding:"0 4px"}}>
          {data.map((d,i)=>{
            const h=Math.max(8,(d.revenue/max)*70);
            const ph=Math.max(4,(d.cost/max)*70);
            return(
              <div key={i} style={{flex:1,display:"flex",flexDirection:"column",alignItems:"center",gap:2}}>
                <div style={{display:"flex",alignItems:"flex-end",gap:1,height:72}}>
                  <div style={{width:8,height:h,background:"linear-gradient(180deg,#FF6B2B,#FF9A6B)",borderRadius:"3px 3px 0 0"}}/>
                  <div style={{width:8,height:ph,background:"rgba(255,255,255,0.2)",borderRadius:"3px 3px 0 0"}}/>
                </div>
                <span style={{fontSize:9,color:"rgba(255,255,255,0.5)"}}>{d.month}</span>
              </div>
            );
          })}
        </div>
      );
    }

    // ─── CHATBOT ─────────────────────────────────────────────────────────────
    function Chatbot({products,customers,orders,onClose}){
      const [msgs,setMsgs]=useState([{role:"bot",text:"Xin chào! Tôi là trợ lý AI của PhoneShop Pro 📱\nBạn cần hỗ trợ gì?"}]);
      const [input,setInput]=useState("");
      const [loading,setLoading]=useState(false);
      const endRef=useRef(null);
      useEffect(()=>{endRef.current?.scrollIntoView({behavior:"smooth"});},[msgs]);

      const send=async()=>{
        const q=input.trim();
        if(!q||loading)return;
        setInput("");
        setMsgs(p=>[...p,{role:"user",text:q}]);
        setLoading(true);

        const lowStock=products.filter(p=>p.stock<=p.minStock);
        const topProducts=[...products].sort((a,b)=>b.sold-a.sold).slice(0,5);
        const totalRevenue=orders.filter(o=>o.status==="Hoàn thành").reduce((s,o)=>s+o.total,0);
        const context=`Bạn là trợ lý AI cho cửa hàng điện thoại PhoneShop Pro. Trả lời ngắn gọn bằng tiếng Việt.
DỮ LIỆU THỰC TẾ:
- Tổng SP: ${products.length}, tồn: ${products.reduce((s,p)=>s+p.stock,0)} máy
- Hàng sắp hết: ${lowStock.map(p=>p.name+" (còn "+p.stock+")").join(", ")||"Không có"}
- Tổng KH: ${customers.length}, Doanh thu: ${fmt(totalRevenue)}`;

        try{
          if (!ANTHROPIC_API_KEY) {
            setMsgs(p=>[...p,{role:"bot",text:"⚠️ Vui lòng cấu hình biến ANTHROPIC_API_KEY ở đầu code HTML để sử dụng AI."}]);
            setLoading(false);
            return;
          }

          const res=await fetch("https://api.anthropic.com/v1/messages",{
            method:"POST",
            headers:{
              "Content-Type":"application/json",
              "x-api-key": ANTHROPIC_API_KEY,
              "anthropic-version": "2023-06-01",
              "anthropic-dangerously-allow-browser": "true" 
            },
            body:JSON.stringify({
              model:"claude-3-haiku-20240307",
              max_tokens:1000,
              system:context,
              messages:[...msgs.filter(m=>m.role!=="bot"||msgs.indexOf(m)>0).map(m=>({role:m.role==="bot"?"assistant":"user",content:m.text})),{role:"user",content:q}]
            })
          });
          const data=await res.json();
          if (data.error) throw new Error(data.error.message);
          const reply=data.content?.map(c=>c.text||"").join("")||"Xin lỗi, tôi không hiểu câu hỏi này.";
          setMsgs(p=>[...p,{role:"bot",text:reply}]);
        }catch(err){
          setMsgs(p=>[...p,{role:"bot",text:`⚠️ Lỗi: ${err.message || "Không kết nối được."}`}]);
        }
        setLoading(false);
      };

      const quickQ=["Hàng nào sắp hết?","Doanh thu hôm nay?","Tư vấn nhập hàng?"];

      return(
        <div style={{position:"fixed",bottom:20,right:20,width:360,height:520,background:"#0F2547",border:"1px solid rgba(255,107,43,0.3)",borderRadius:20,display:"flex",flexDirection:"column",zIndex:1000,boxShadow:"0 20px 60px rgba(0,0,0,0.5)",overflow:"hidden"}}>
          <div style={{background:"linear-gradient(135deg,#FF6B2B,#FF4500)",padding:"14px 16px",display:"flex",alignItems:"center",justifyContent:"space-between",flexShrink:0}}>
            <div style={{display:"flex",alignItems:"center",gap:10}}>
              <div style={{width:36,height:36,background:"rgba(255,255,255,0.2)",borderRadius:10,display:"flex",alignItems:"center",justifyContent:"center",fontSize:18}}>🤖</div>
              <div>
                <div style={{fontWeight:700,color:"#fff",fontSize:14}}>AI Trợ lý</div>
                <div style={{fontSize:10,color:"rgba(255,255,255,0.75)"}}>Online</div>
              </div>
            </div>
            <button onClick={onClose} style={{background:"rgba(255,255,255,0.2)",border:"none",borderRadius:8,padding:"6px 10px",color:"#fff",cursor:"pointer",fontSize:16}}>✕</button>
          </div>
          <div style={{flex:1,overflow:"auto",padding:14,display:"flex",flexDirection:"column",gap:10}}>
            {msgs.map((m,i)=>(
              <div key={i} style={{display:"flex",justifyContent:m.role==="user"?"flex-end":"flex-start"}}>
                {m.role==="bot"&&<div style={{width:28,height:28,borderRadius:8,background:"rgba(255,107,43,0.2)",display:"flex",alignItems:"center",justifyContent:"center",fontSize:14,flexShrink:0,marginRight:8}}>🤖</div>}
                <div style={{maxWidth:"80%",padding:"10px 13px",borderRadius:m.role==="user"?"14px 14px 4px 14px":"14px 14px 14px 4px",background:m.role==="user"?"linear-gradient(135deg,#FF6B2B,#FF4500)":"rgba(255,255,255,0.08)",color:"#fff",fontSize:13,lineHeight:1.5,whiteSpace:"pre-wrap"}}>{m.text}</div>
              </div>
            ))}
            {loading&&( <div style={{color:"rgba(255,255,255,0.5)", fontSize:12}}>Đang phản hồi...</div> )}
            <div ref={endRef}/>
          </div>
          <div style={{padding:"0 12px 8px",display:"flex",gap:6,flexWrap:"wrap",flexShrink:0}}>
            {quickQ.map((q,i)=>(<button key={i} onClick={()=>{setInput(q);}} style={{padding:"4px 9px",borderRadius:20,border:"1px solid rgba(255,107,43,0.3)",background:"rgba(255,107,43,0.08)",color:"rgba(255,255,255,0.7)",cursor:"pointer",fontSize:11}}>{q}</button>))}
          </div>
          <div style={{padding:"8px 12px 14px",display:"flex",gap:8,flexShrink:0,borderTop:"1px solid rgba(255,255,255,0.06)"}}>
            <input value={input} onChange={e=>setInput(e.target.value)} onKeyDown={e=>e.key==="Enter"&&send()} placeholder="Nhập câu hỏi..." style={{flex:1,background:"rgba(255,255,255,0.07)",border:"1px solid rgba(255,255,255,0.1)",borderRadius:10,padding:"9px 13px",color:"#fff",fontSize:13,outline:"none"}}/>
            <button onClick={send} disabled={loading||!input.trim()} style={{width:40,height:40,borderRadius:10,background:loading||!input.trim()?"rgba(255,255,255,0.08)":"linear-gradient(135deg,#FF6B2B,#FF4500)",border:"none",color:"#fff",cursor:loading||!input.trim()?"not-allowed":"pointer"}}>➤</button>
          </div>
        </div>
      );
    }

    // ─── MAIN APP ────────────────────────────────────────────────────────────
    function App(){
      const [tab,setTab]=useState("dashboard");
      const [products,setProducts]=useState(IP);
      const [customers,setCustomers]=useState(IC);
      const [orders,setOrders]=useState(IO);
      const [cart,setCart]=useState([]);
      const [searchP,setSearchP]=useState("");
      const [searchC,setSearchC]=useState("");
      const [selCust,setSelCust]=useState(null);
      const [payment,setPayment]=useState("Tiền mặt");
      const [discount,setDiscount]=useState(0);
      const [showReceipt,setShowReceipt]=useState(false);
      const [lastOrder,setLastOrder]=useState(null);
      const [catFilter,setCatFilter]=useState("Tất cả");
      const [showAddP,setShowAddP]=useState(false);
      const [newP,setNewP]=useState({sku:"",name:"",category:"iPhone",brand:"",color:"",price:"",costPrice:"",stock:"",minStock:3,image:"📱"});
      const [notif,setNotif]=useState(null);
      const [sidebar,setSidebar]=useState(true);
      const [showChat,setShowChat]=useState(false);
      const [cacheLoaded,setCacheLoaded]=useState(false);
      const [syncStatus,setSyncStatus]=useState(null); 
      const [mobileNav,setMobileNav]=useState(false);
      const saveTimer=useRef(null);
      const isMobile=typeof window!=="undefined"&&window.innerWidth<768;

      const showNotif=(msg,type="success")=>{setNotif({msg,type});setTimeout(()=>setNotif(null),3000);};

      useEffect(()=>{
        (async()=>{
          const cached=await loadShared();
          if(cached){
            if(cached.products)setProducts(cached.products);
            if(cached.customers)setCustomers(cached.customers);
            if(cached.orders)setOrders(cached.orders);
            if(cached.cart?.length>0)setCart(cached.cart);
            if(cached.selCust)setSelCust(cached.selCust);
            if(cached.payment)setPayment(cached.payment);
            if(cached.discount!=null)setDiscount(cached.discount);
            if(cached.newP)setNewP(cached.newP);
            if(cached.tab)setTab(cached.tab);
            setSyncStatus("loaded");
            setTimeout(()=>setSyncStatus(null),2500);
          }
          setCacheLoaded(true);
        })();
      },[]);

      const save=useCallback((data)=>{
        if(!cacheLoaded)return;
        if(saveTimer.current)clearTimeout(saveTimer.current);
        setSyncStatus("saving");
        saveTimer.current=setTimeout(async()=>{
          try{
            await saveShared(data);
            setSyncStatus("saved");
            setTimeout(()=>setSyncStatus(null),2000);
          }catch{
            setSyncStatus("error");
            setTimeout(()=>setSyncStatus(null),3000);
          }
        },1000);
      },[cacheLoaded]);

      useEffect(()=>{
        if(!cacheLoaded)return;
        save({products,customers,orders,cart,selCust,payment,discount,newP,tab});
      },[products,customers,orders,cart,selCust,payment,discount,newP,tab,cacheLoaded]);

      const resetCache=async()=>{
        await deleteShared();
        setProducts(IP);setCustomers(IC);setOrders(IO);setCart([]);
        setSelCust(null);setPayment("Tiền mặt");setDiscount(0);
        setNewP({sku:"",name:"",category:"iPhone",brand:"",color:"",price:"",costPrice:"",stock:"",minStock:3,image:"📱"});
        showNotif("Đã xóa dữ liệu đồng bộ");
      };

      const addToCart=p=>{
        if(p.stock===0){showNotif("Hết hàng!","error");return;}
        setCart(prev=>{
          const ex=prev.find(i=>i.id===p.id);
          if(ex){if(ex.qty>=p.stock){showNotif("Không đủ tồn kho!","error");return prev;}return prev.map(i=>i.id===p.id?{...i,qty:i.qty+1}:i);}
          return[...prev,{...p,qty:1}];
        });
      };
      const removeFromCart=id=>setCart(p=>p.filter(i=>i.id!==id));
      const updateQty=(id,qty)=>{
        if(qty<=0){removeFromCart(id);return;}
        const pr=products.find(p=>p.id===id);
        if(qty>pr.stock){showNotif("Không đủ tồn kho!","error");return;}
        setCart(p=>p.map(i=>i.id===id?{...i,qty}:i));
      };
      
      const cartTotal=cart.reduce((s,i)=>s+i.price*i.qty,0);
      const discAmt=Math.round(cartTotal*(discount/100));
      const finalTotal=cartTotal-discAmt;

      const checkout=()=>{
        if(cart.length===0){showNotif("Giỏ hàng trống!","error");return;}
        const id="HD"+String(orders.length+1).padStart(3,"0");
        const o={id,customer:selCust?selCust.name:"Khách lẻ",items:cart.map(i=>({name:i.name,qty:i.qty,price:i.price*i.qty})),total:finalTotal,payment,status:"Hoàn thành",date:new Date().toLocaleString("vi-VN")};
        setOrders(p=>[o,...p]);
        setProducts(p=>p.map(pr=>{const ci=cart.find(c=>c.id===pr.id);return ci?{...pr,stock:pr.stock-ci.qty,sold:pr.sold+ci.qty}:pr;}));
        if(selCust){const pts=Math.floor(finalTotal/100000);setCustomers(p=>p.map(c=>c.id===selCust.id?{...c,points:c.points+pts,totalSpent:c.totalSpent+finalTotal,orders:c.orders+1}:c));}
        setLastOrder(o);setCart([]);setSelCust(null);setDiscount(0);setShowReceipt(true);
        showNotif(`Thanh toán thành công ${fmt(finalTotal)}!`);
      };

      const addProduct=()=>{
        if(!newP.sku||!newP.name||!newP.price){showNotif("Vui lòng điền đầy đủ!","error");return;}
        const p={...newP,id:products.length+1,price:+newP.price,costPrice:+newP.costPrice,stock:+newP.stock,minStock:+newP.minStock,sold:0};
        setProducts(prev=>[p,...prev]);
        setShowAddP(false);
        setNewP({sku:"",name:"",category:"iPhone",brand:"",color:"",price:"",costPrice:"",stock:"",minStock:3,image:"📱"});
        showNotif("Thêm SP thành công!");
      };

      const cats=["Tất cả",...new Set(products.map(p=>p.category))];
      const filteredP=products.filter(p=>{
        const mc=catFilter==="Tất cả"||p.category===catFilter;
        const ms=p.name.toLowerCase().includes(searchP.toLowerCase())||p.sku.toLowerCase().includes(searchP.toLowerCase());
        return mc&&ms;
      });
      const lowStock=products.filter(p=>p.stock<=p.minStock);
      const todayRev=orders.filter(o=>o.status==="Hoàn thành").reduce((s,o)=>s+o.total,0);
      const totalProfit=RD.reduce((s,d)=>s+(d.revenue-d.cost),0);

      const navItems=[
        {id:"dashboard",icon:"📊",label:"Tổng quan"},
        {id:"pos",icon:"🛒",label:"Bán hàng"},
        {id:"products",icon:"📦",label:"Hàng hóa"},
        {id:"orders",icon:"🧾",label:"Hóa đơn"},
        {id:"customers",icon:"👥",label:"Khách hàng"},
        {id:"staff",icon:"👔",label:"Nhân sự"},
        {id:"reports",icon:"📈",label:"Báo cáo"},
      ];

      const syncCfg={
        saving:{bg:"rgba(255,149,0,0.12)",border:"rgba(255,149,0,0.3)",color:"#FF9500",label:"☁️ Đang lưu…",spin:true},
        saved:{bg:"rgba(52,199,89,0.12)",border:"rgba(52,199,89,0.3)",color:"#34C759",label:"☁️ Đã lưu"},
        loaded:{bg:"rgba(0,122,255,0.12)",border:"rgba(0,122,255,0.3)",color:"#007AFF",label:"📲 Đã khôi phục"},
        error:{bg:"rgba(255,59,48,0.12)",border:"rgba(255,59,48,0.3)",color:"#FF3B30",label:"⚠️ Lỗi lưu"},
      }[syncStatus]||null;

      const C="#0B1629",C2="#0F2547",C3="#132040";
      const inp={background:"rgba(255,255,255,0.06)",border:"1px solid rgba(255,255,255,0.1)",borderRadius:10,padding:"9px 13px",color:"#fff",fontSize:13,outline:"none",width:"100%",boxSizing:"border-box"};
      const card={background:C3,border:"1px solid rgba(255,255,255,0.07)",borderRadius:14,padding:20};
      const tag=(cl)=>({padding:"2px 8px",borderRadius:6,fontSize:11,fontWeight:600,background:cl+"22",color:cl});
      const btnP={background:"linear-gradient(135deg,#FF6B2B,#FF4500)",color:"#fff",border:"none",borderRadius:10,padding:"10px 18px",cursor:"pointer",fontSize:13,fontWeight:700};
      const btnS={background:"rgba(255,255,255,0.06)",color:"rgba(255,255,255,0.7)",border:"1px solid rgba(255,255,255,0.1)",borderRadius:9,padding:"9px 16px",cursor:"pointer",fontSize:13};
      const th={textAlign:"left",padding:"10px 14px",fontSize:11,color:"rgba(255,255,255,0.4)",fontWeight:600,textTransform:"uppercase",borderBottom:"1px solid rgba(255,255,255,0.07)"};
      const td={padding:"12px 14px",fontSize:13,color:"rgba(255,255,255,0.8)",borderBottom:"1px solid rgba(255,255,255,0.04)"};

      const NAV_W=sidebar?220:64;

      if(showReceipt&&lastOrder){
        return(
          <div style={{position:"fixed",inset:0,background:"rgba(0,0,0,0.85)",display:"flex",alignItems:"center",justifyContent:"center",zIndex:9999,padding:16}}>
            <style>{`@keyframes popIn{from{opacity:0;transform:scale(0.85)}to{opacity:1;transform:scale(1)}}`}</style>
            <div style={{background:"#132040",borderRadius:20,padding:28,width:"100%",maxWidth:380,border:"1px solid rgba(255,255,255,0.1)",animation:"popIn 0.3s ease"}}>
              <div style={{textAlign:"center",marginBottom:20}}>
                <div style={{fontSize:52}}>✅</div>
                <div style={{fontSize:20,fontWeight:800,color:"#fff",marginTop:6}}>Thanh toán thành công!</div>
                <div style={{fontSize:13,color:"rgba(255,255,255,0.4)",marginTop:3}}>Hóa đơn #{lastOrder.id}</div>
              </div>
              <button onClick={()=>setShowReceipt(false)} style={{width:"100%",padding:13,background:"linear-gradient(135deg,#FF6B2B,#FF4500)",border:"none",borderRadius:12,color:"#fff",fontWeight:700,fontSize:15,cursor:"pointer"}}>Đóng hóa đơn</button>
            </div>
          </div>
        );
      }

      return(
        <div style={{display:"flex",height:"100vh",background:C,fontFamily:"'Inter',-apple-system,sans-serif",color:"#E8EDF5",overflow:"hidden",position:"relative"}}>
          <style>{`
            *{box-sizing:border-box;}
            ::-webkit-scrollbar{width:4px;height:4px;}
            ::-webkit-scrollbar-track{background:transparent;}
            ::-webkit-scrollbar-thumb{background:rgba(255,255,255,0.1);border-radius:2px;}
            @keyframes spin{from{transform:rotate(0deg)}to{transform:rotate(360deg)}}
            @media(max-width:767px){ .desktop-only{display:none!important;} }
            @media(min-width:768px){ .mobile-only{display:none!important;} }
            button:hover{opacity:0.88;}
          `}</style>

          <aside className="desktop-only" style={{width:NAV_W,background:C2,display:"flex",flexDirection:"column",transition:"width 0.25s ease",flexShrink:0,borderRight:"1px solid rgba(255,255,255,0.06)"}}>
            <div style={{padding:sidebar?"20px 20px 14px":"20px 12px 14px",borderBottom:"1px solid rgba(255,255,255,0.06)",display:"flex",alignItems:"center",gap:10}}>
              <div style={{width:36,height:36,background:"linear-gradient(135deg,#FF6B2B,#FF9A6B)",borderRadius:10,display:"flex",alignItems:"center",justifyContent:"center",fontSize:18,flexShrink:0}}>📱</div>
              {sidebar&&<div><div style={{fontWeight:800,fontSize:15,color:"#fff"}}>PhoneShop</div></div>}
            </div>
            <nav style={{flex:1,padding:"12px 8px",display:"flex",flexDirection:"column",gap:2}}>
              {navItems.map(item=>(
                <button key={item.id} onClick={()=>setTab(item.id)} style={{display:"flex",alignItems:"center",gap:10,padding:sidebar?"10px 12px":"10px",borderRadius:10,border:"none",cursor:"pointer",background:tab===item.id?"rgba(255,107,43,0.15)":"transparent",color:tab===item.id?"#FF6B2B":"rgba(255,255,255,0.55)",fontSize:13.5,fontWeight:tab===item.id?600:400,width:"100%",textAlign:"left",borderLeft:tab===item.id?"3px solid #FF6B2B":"3px solid transparent"}}>
                  <span style={{fontSize:17,flexShrink:0}}>{item.icon}</span>
                  {sidebar&&<span>{item.label}</span>}
                </button>
              ))}
            </nav>
          </aside>

          <main style={{flex:1,display:"flex",flexDirection:"column",overflow:"hidden",minWidth:0}}>
            <header style={{padding:"12px 16px",background:C2,borderBottom:"1px solid rgba(255,255,255,0.06)",display:"flex",alignItems:"center",justifyContent:"space-between",flexShrink:0}}>
              <div style={{fontSize:16,fontWeight:700,color:"#fff"}}>{navItems.find(n=>n.id===tab)?.label}</div>
              <div style={{display:"flex",alignItems:"center",gap:8}}>
                {syncCfg&&<div style={{background:syncCfg.bg,border:`1px solid ${syncCfg.border}`,borderRadius:8,padding:"5px 10px",fontSize:11,color:syncCfg.color}}>{syncCfg.label}</div>}
                <button onClick={resetCache} style={{background:"rgba(255,59,48,0.1)",border:"1px solid rgba(255,59,48,0.2)",borderRadius:8,padding:"7px 10px",cursor:"pointer",color:"rgba(255,100,100,0.7)"}}>🗑️ Reset</button>
              </div>
            </header>

            <div style={{flex:1,overflow:"auto",padding:"16px"}}>
              {tab==="dashboard"&&(
                <div>
                  <div style={{display:"grid",gridTemplateColumns:"repeat(auto-fit,minmax(160px,1fr))",gap:12,marginBottom:16}}>
                    {[{label:"Doanh thu",value:fmtS(todayRev)+"đ",icon:"💰"},{label:"Đơn hàng",value:orders.length,icon:"🧾"},{label:"Tổng tồn kho",value:products.reduce((s,p)=>s+p.stock,0),icon:"📦"}].map((s,i)=>(
                      <div key={i} style={{background:`rgba(255,255,255,0.04)`,borderRadius:14,padding:16}}>
                        <div style={{fontSize:10,color:"rgba(255,255,255,0.4)"}}>{s.label}</div>
                        <div style={{fontSize:22,fontWeight:800,color:"#FF6B2B",marginTop:4}}>{s.value}</div>
                      </div>
                    ))}
                  </div>
                  <div style={card}>
                    <div style={{fontSize:14,fontWeight:700,color:"#fff",marginBottom:12}}>Biểu đồ mini</div>
                    <MiniBar data={RD}/>
                  </div>
                </div>
              )}

              {tab==="pos"&&(
                <div style={{display:"grid",gridTemplateColumns:"minmax(0,1fr) 340px",gap:14,height:"100%"}}>
                  <div style={{display:"flex",flexDirection:"column",gap:10}}>
                    <input style={inp} placeholder="🔍 Tìm SP..." value={searchP} onChange={e=>setSearchP(e.target.value)}/>
                    <div style={{display:"grid",gridTemplateColumns:"repeat(auto-fill,minmax(150px,1fr))",gap:8,overflow:"auto"}}>
                      {filteredP.map(p=>(
                        <button key={p.id} onClick={()=>addToCart(p)} style={{background:C3,border:"1px solid rgba(255,255,255,0.08)",borderRadius:12,padding:12,cursor:"pointer",textAlign:"left"}}>
                          <div style={{fontSize:26,marginBottom:5}}>{p.image}</div>
                          <div style={{fontSize:11,fontWeight:600,color:"#fff"}}>{p.name}</div>
                          <div style={{fontSize:13,fontWeight:800,color:"#FF6B2B"}}>{fmtS(p.price)}đ</div>
                        </button>
                      ))}
                    </div>
                  </div>
                  <div style={{...card,display:"flex",flexDirection:"column"}}>
                    <div style={{fontSize:14,fontWeight:700,color:"#fff",marginBottom:10}}>🛒 Giỏ hàng</div>
                    <div style={{flex:1,overflow:"auto"}}>
                      {cart.map(item=>(
                        <div key={item.id} style={{padding:"9px 0",borderBottom:"1px solid rgba(255,255,255,0.05)"}}>
                          <div style={{fontSize:11,fontWeight:600,color:"#fff"}}>{item.name}</div>
                          <div style={{display:"flex",justifyContent:"space-between",alignItems:"center",marginTop:6}}>
                            <div style={{display:"flex",gap:4}}>
                              <button onClick={()=>updateQty(item.id,item.qty-1)} style={btnS}>-</button>
                              <span style={{width:26,textAlign:"center",color:"#fff"}}>{item.qty}</span>
                              <button onClick={()=>updateQty(item.id,item.qty+1)} style={btnS}>+</button>
                            </div>
                            <span style={{color:"#FF6B2B",fontWeight:700}}>{fmt(item.price*item.qty)}</span>
                          </div>
                        </div>
                      ))}
                    </div>
                    <button onClick={checkout} style={{...btnP,width:"100%",padding:12,marginTop:10}}>💳 Thanh toán {fmt(finalTotal)}</button>
                  </div>
                </div>
              )}

              {/* Các tab khác được giữ nguyên cấu trúc logic, giản lược UI một chút để hiển thị tốt trên 1 file HTML */}
              {tab==="products"&&(
                 <div style={card}>
                    <button onClick={()=>setShowAddP(true)} style={{...btnP, marginBottom:10}}>+ Thêm SP</button>
                    {showAddP && <div style={{background:"rgba(255,255,255,0.05)", padding: 15, borderRadius: 10, marginBottom: 10}}>
                        <input style={{...inp, marginBottom: 10}} placeholder="Tên SP..." value={newP.name} onChange={e=>setNewP({...newP, name: e.target.value})}/>
                        <input style={{...inp, marginBottom: 10}} type="number" placeholder="Giá bán..." value={newP.price} onChange={e=>setNewP({...newP, price: e.target.value})}/>
                        <button onClick={addProduct} style={btnP}>Lưu</button>
                    </div>}
                    <div style={{overflow:"auto"}}>
                        <table style={{width:"100%",borderCollapse:"collapse"}}>
                            <thead><tr><th style={th}>Tên</th><th style={th}>Giá</th><th style={th}>Tồn</th></tr></thead>
                            <tbody>
                                {products.map(p=>(
                                    <tr key={p.id}>
                                        <td style={td}>{p.image} {p.name}</td>
                                        <td style={td}>{fmtS(p.price)}đ</td>
                                        <td style={td}>{p.stock}</td>
                                    </tr>
                                ))}
                            </tbody>
                        </table>
                    </div>
                 </div>
              )}
            </div>

            <div className="mobile-only" style={{background:C2,display:"flex",justifyContent:"space-around",padding:"8px"}}>
              {navItems.map(item=>(
                <button key={item.id} onClick={()=>setTab(item.id)} style={{background:"none",border:"none",color:tab===item.id?"#FF6B2B":"#fff",fontSize:20}}>{item.icon}</button>
              ))}
            </div>
          </main>

          {showChat&&<Chatbot products={products} customers={customers} orders={orders} onClose={()=>setShowChat(false)}/>}
          {!showChat&&<button onClick={()=>setShowChat(true)} style={{position:"fixed",bottom:20,right:20,width:56,height:56,borderRadius:"50%",background:"#FF6B2B",border:"none",color:"#fff",fontSize:24,cursor:"pointer"}}>🤖</button>}
          {notif&&<div style={{position:"fixed",top:14,left:"50%",transform:"translateX(-50%)",background:"#34C759",color:"#fff",padding:"11px 20px",borderRadius:12,zIndex:9999}}>{notif.msg}</div>}
        </div>
      );
    }

    // Kết xuất ứng dụng vào thẻ div#root
    const root = ReactDOM.createRoot(document.getElementById('root'));
    root.render(<App />);
  </script>
</body>
</html>
  { id:3,sku:"OPP-FIND7-256-BLK",name:"OPPO Find X7 256GB",category:"OPPO",brand:"OPPO",color:"Đen Bóng",price:22990000,costPrice:17500000,stock:2,minStock:3,sold:28,image:"📱" },
  { id:4,sku:"XIA-14U-256-PUR",name:"Xiaomi 14 Ultra 256GB",category:"Xiaomi",brand:"Xiaomi",color:"Tím Titan",price:23490000,costPrice:18000000,stock:15,minStock:3,sold:22,image:"📱" },
  { id:5,sku:"VIV-X100P-256-BLU",name:"Vivo X100 Pro 256GB",category:"Vivo",brand:"Vivo",color:"Xanh Biển",price:19990000,costPrice:15500000,stock:6,minStock:3,sold:19,image:"📱" },
  { id:6,sku:"IPH15-128-BLU",name:"iPhone 15 128GB",category:"iPhone",brand:"Apple",color:"Xanh Lam",price:22490000,costPrice:17800000,stock:20,minStock:5,sold:62,image:"📱" },
  { id:7,sku:"SAM-A55-256-LVD",name:"Samsung A55 256GB",category:"Samsung",brand:"Samsung",color:"Xanh Tím Oải Hương",price:9990000,costPrice:7200000,stock:25,minStock:5,sold:88,image:"📱" },
  { id:8,sku:"ACC-CASE-MG-BLK",name:"Ốp lưng MagSafe iPhone 15",category:"Phụ kiện",brand:"Apple",color:"Đen",price:890000,costPrice:300000,stock:45,minStock:10,sold:120,image:"🛡️" },
  { id:9,sku:"ACC-CHG-65W-WHT",name:"Sạc nhanh 65W GaN",category:"Phụ kiện",brand:"Anker",color:"Trắng",price:690000,costPrice:220000,stock:38,minStock:10,sold:95,image:"🔌" },
  { id:10,sku:"ACC-EAR-AIRP3-WHT",name:"AirPods 3 (Lightning)",category:"Tai nghe",brand:"Apple",color:"Trắng",price:4490000,costPrice:3200000,stock:18,minStock:5,sold:41,image:"🎧" },
];
const IC = [
  { id:1,name:"Nguyễn Văn An",phone:"0912345678",email:"an.nguyen@gmail.com",group:"VIP",points:2450,totalSpent:68900000,orders:5,lastVisit:"2025-06-20" },
  { id:2,name:"Trần Thị Bình",phone:"0987654321",email:"binh.tran@gmail.com",group:"Thân thiết",points:890,totalSpent:23400000,orders:3,lastVisit:"2025-06-18" },
  { id:3,name:"Lê Minh Cường",phone:"0909123456",email:"cuong.le@gmail.com",group:"Đại lý",points:5200,totalSpent:145000000,orders:18,lastVisit:"2025-06-22" },
  { id:4,name:"Phạm Thị Dung",phone:"0978234567",email:"dung.pham@gmail.com",group:"Khách lẻ",points:120,totalSpent:3200000,orders:1,lastVisit:"2025-06-15" },
  { id:5,name:"Hoàng Văn Em",phone:"0965432109",email:"em.hoang@gmail.com",group:"Thân thiết",points:1340,totalSpent:34500000,orders:4,lastVisit:"2025-06-21" },
];
const IO = [
  { id:"HD001",customer:"Nguyễn Văn An",items:[{name:"iPhone 15 Pro Max 256GB",qty:1,price:34990000}],total:34990000,payment:"Thẻ",status:"Hoàn thành",date:"2025-06-22 09:15" },
  { id:"HD002",customer:"Trần Thị Bình",items:[{name:"Samsung A55 256GB",qty:1,price:9990000},{name:"Ốp lưng MagSafe",qty:1,price:890000}],total:10880000,payment:"QR",status:"Hoàn thành",date:"2025-06-22 10:32" },
  { id:"HD003",customer:"Khách lẻ",items:[{name:"Sạc nhanh 65W GaN",qty:2,price:1380000}],total:1380000,payment:"Tiền mặt",status:"Hoàn thành",date:"2025-06-22 11:05" },
  { id:"HD004",customer:"Lê Minh Cường",items:[{name:"Samsung S24 Ultra 512GB",qty:2,price:63980000}],total:63980000,payment:"Chuyển khoản",status:"Hoàn thành",date:"2025-06-22 13:20" },
  { id:"HD005",customer:"Hoàng Văn Em",items:[{name:"AirPods 3",qty:1,price:4490000}],total:4490000,payment:"QR",status:"Đang xử lý",date:"2025-06-22 14:48" },
];
const STAFF=[
  {id:1,name:"Nguyễn Thị Mai",role:"Quản lý",sales:145000000,commission:2.5,workDays:22,salary:12000000},
  {id:2,name:"Trần Văn Hùng",role:"Nhân viên bán hàng",sales:89000000,commission:1.5,workDays:22,salary:8000000},
  {id:3,name:"Phạm Thị Lan",role:"Nhân viên bán hàng",sales:67000000,commission:1.5,workDays:20,salary:8000000},
  {id:4,name:"Lê Văn Đức",role:"Kế toán",sales:0,commission:0,workDays:22,salary:10000000},
];
const RD=[
  {month:"T1",revenue:245000000,cost:185000000},{month:"T2",revenue:198000000,cost:148000000},
  {month:"T3",revenue:312000000,cost:235000000},{month:"T4",revenue:287000000,cost:215000000},
  {month:"T5",revenue:356000000,cost:268000000},{month:"T6",revenue:298000000,cost:223000000},
];

// ─── HELPERS ─────────────────────────────────────────────────────────────────
const fmt=(n)=>new Intl.NumberFormat("vi-VN").format(n)+"đ";
const fmtS=(n)=>{if(n>=1e9)return(n/1e9).toFixed(1)+"tỷ";if(n>=1e6)return(n/1e6).toFixed(0)+"tr";return new Intl.NumberFormat("vi-VN").format(n);};

// ─── MINI BAR CHART ──────────────────────────────────────────────────────────
function MiniBar({data}){
  const max=Math.max(...data.map(d=>d.revenue));
  return(
    <div style={{display:"flex",alignItems:"flex-end",gap:6,height:80,padding:"0 4px"}}>
      {data.map((d,i)=>{
        const h=Math.max(8,(d.revenue/max)*70);
        const ph=Math.max(4,(d.cost/max)*70);
        return(
          <div key={i} style={{flex:1,display:"flex",flexDirection:"column",alignItems:"center",gap:2}}>
            <div style={{display:"flex",alignItems:"flex-end",gap:1,height:72}}>
              <div style={{width:8,height:h,background:"linear-gradient(180deg,#FF6B2B,#FF9A6B)",borderRadius:"3px 3px 0 0"}}/>
              <div style={{width:8,height:ph,background:"rgba(255,255,255,0.2)",borderRadius:"3px 3px 0 0"}}/>
            </div>
            <span style={{fontSize:9,color:"rgba(255,255,255,0.5)"}}>{d.month}</span>
          </div>
        );
      })}
    </div>
  );
}

// ─── CHATBOT ─────────────────────────────────────────────────────────────────
function Chatbot({products,customers,orders,onClose}){
  const [msgs,setMsgs]=useState([{role:"bot",text:"Xin chào! Tôi là trợ lý AI của PhoneShop Pro 📱\nBạn cần hỗ trợ gì? Tôi có thể tư vấn về tồn kho, doanh thu, khách hàng, và hơn thế nữa!"}]);
  const [input,setInput]=useState("");
  const [loading,setLoading]=useState(false);
  const endRef=useRef(null);
  useEffect(()=>{endRef.current?.scrollIntoView({behavior:"smooth"});},[msgs]);

  const send=async()=>{
    const q=input.trim();
    if(!q||loading)return;
    setInput("");
    setMsgs(p=>[...p,{role:"user",text:q}]);
    setLoading(true);

    const lowStock=products.filter(p=>p.stock<=p.minStock);
    const topProducts=[...products].sort((a,b)=>b.sold-a.sold).slice(0,5);
    const totalRevenue=orders.filter(o=>o.status==="Hoàn thành").reduce((s,o)=>s+o.total,0);
    const context=`Bạn là trợ lý AI cho cửa hàng điện thoại PhoneShop Pro. Trả lời ngắn gọn bằng tiếng Việt.
DỮ LIỆU THỰC TẾ:
- Tổng sản phẩm: ${products.length} loại, tổng tồn kho: ${products.reduce((s,p)=>s+p.stock,0)} máy
- Hàng sắp hết (${lowStock.length} mặt hàng): ${lowStock.map(p=>p.name+" (còn "+p.stock+")").join(", ")||"Không có"}
- Top 5 bán chạy: ${topProducts.map((p,i)=>(i+1)+". "+p.name+" (đã bán "+p.sold+")").join(", ")}
- Tổng khách hàng: ${customers.length}, VIP: ${customers.filter(c=>c.group==="VIP").length}
- Tổng đơn hàng: ${orders.length}, doanh thu: ${fmt(totalRevenue)}
- Sản phẩm: ${products.slice(0,5).map(p=>p.name+" giá "+fmt(p.price)+" còn "+p.stock).join("; ")}`;

    try{
      // Lưu ý: Gọi API trực tiếp từ Client side.
      // Cần có file .env với VITE_ANTHROPIC_API_KEY
      const apiKey = import.meta.env.VITE_ANTHROPIC_API_KEY;
      
      if (!apiKey) {
        setMsgs(p=>[...p,{role:"bot",text:"⚠️ Vui lòng cấu hình VITE_ANTHROPIC_API_KEY trong file .env để sử dụng AI."}]);
        setLoading(false);
        return;
      }

      const res=await fetch("https://api.anthropic.com/v1/messages",{
        method:"POST",
        headers:{
          "Content-Type":"application/json",
          "x-api-key": apiKey,
          "anthropic-version": "2023-06-01",
          "anthropic-dangerously-allow-browser": "true" 
        },
        body:JSON.stringify({
          model:"claude-3-haiku-20240307", // Đã đổi sang haiku để chạy nhẹ/rẻ hơn
          max_tokens:1000,
          system:context,
          messages:[...msgs.filter(m=>m.role!=="bot"||msgs.indexOf(m)>0).map(m=>({role:m.role==="bot"?"assistant":"user",content:m.text})),{role:"user",content:q}]
        })
      });
      const data=await res.json();
      if (data.error) throw new Error(data.error.message);
      const reply=data.content?.map(c=>c.text||"").join("")||"Xin lỗi, tôi không hiểu câu hỏi này.";
      setMsgs(p=>[...p,{role:"bot",text:reply}]);
    }catch(err){
      setMsgs(p=>[...p,{role:"bot",text:`⚠️ Lỗi: ${err.message || "Không thể kết nối AI. Vui lòng thử lại."}`}]);
    }
    setLoading(false);
  };

  const quickQ=["Hàng nào sắp hết?","Sản phẩm bán chạy nhất?","Doanh thu hôm nay?","Tư vấn nhập hàng?","Khách hàng VIP nào?"];

  return(
    <div style={{position:"fixed",bottom:20,right:20,width:360,height:520,background:"#0F2547",border:"1px solid rgba(255,107,43,0.3)",borderRadius:20,display:"flex",flexDirection:"column",zIndex:1000,boxShadow:"0 20px 60px rgba(0,0,0,0.5)",overflow:"hidden"}}>
      {/* Header */}
      <div style={{background:"linear-gradient(135deg,#FF6B2B,#FF4500)",padding:"14px 16px",display:"flex",alignItems:"center",justifyContent:"space-between",flexShrink:0}}>
        <div style={{display:"flex",alignItems:"center",gap:10}}>
          <div style={{width:36,height:36,background:"rgba(255,255,255,0.2)",borderRadius:10,display:"flex",alignItems:"center",justifyContent:"center",fontSize:18}}>🤖</div>
          <div>
            <div style={{fontWeight:700,color:"#fff",fontSize:14}}>AI Trợ lý PhoneShop</div>
            <div style={{fontSize:10,color:"rgba(255,255,255,0.75)",display:"flex",alignItems:"center",gap:4}}>
              <span style={{width:6,height:6,borderRadius:"50%",background:"#7FFF00",display:"inline-block"}}/>Online
            </div>
          </div>
        </div>
        <button onClick={onClose} style={{background:"rgba(255,255,255,0.2)",border:"none",borderRadius:8,padding:"6px 10px",color:"#fff",cursor:"pointer",fontSize:16}}>✕</button>
      </div>

      {/* Messages */}
      <div style={{flex:1,overflow:"auto",padding:14,display:"flex",flexDirection:"column",gap:10}}>
        {msgs.map((m,i)=>(
          <div key={i} style={{display:"flex",justifyContent:m.role==="user"?"flex-end":"flex-start"}}>
            {m.role==="bot"&&<div style={{width:28,height:28,borderRadius:8,background:"rgba(255,107,43,0.2)",display:"flex",alignItems:"center",justifyContent:"center",fontSize:14,flexShrink:0,marginRight:8}}>🤖</div>}
            <div style={{maxWidth:"80%",padding:"10px 13px",borderRadius:m.role==="user"?"14px 14px 4px 14px":"14px 14px 14px 4px",background:m.role==="user"?"linear-gradient(135deg,#FF6B2B,#FF4500)":"rgba(255,255,255,0.08)",color:"#fff",fontSize:13,lineHeight:1.5,whiteSpace:"pre-wrap",wordBreak:"break-word"}}>
              {m.text}
            </div>
          </div>
        ))}
        {loading&&(
          <div style={{display:"flex",alignItems:"center",gap:8}}>
            <div style={{width:28,height:28,borderRadius:8,background:"rgba(255,107,43,0.2)",display:"flex",alignItems:"center",justifyContent:"center",fontSize:14}}>🤖</div>
            <div style={{background:"rgba(255,255,255,0.08)",borderRadius:"14px 14px 14px 4px",padding:"10px 14px",display:"flex",gap:4}}>
              {[0,1,2].map(i=><span key={i} style={{width:6,height:6,borderRadius:"50%",background:"#FF6B2B",display:"inline-block",animation:`bounce 1.2s ease-in-out ${i*0.2}s infinite`}}/>)}
            </div>
          </div>
        )}
        <div ref={endRef}/>
      </div>

      {/* Quick questions */}
      <div style={{padding:"0 12px 8px",display:"flex",gap:6,flexWrap:"wrap",flexShrink:0}}>
        {quickQ.map((q,i)=>(
          <button key={i} onClick={()=>{setInput(q);}} style={{padding:"4px 9px",borderRadius:20,border:"1px solid rgba(255,107,43,0.3)",background:"rgba(255,107,43,0.08)",color:"rgba(255,255,255,0.7)",cursor:"pointer",fontSize:11,whiteSpace:"nowrap"}}>
            {q}
          </button>
        ))}
      </div>

      {/* Input */}
      <div style={{padding:"8px 12px 14px",display:"flex",gap:8,flexShrink:0,borderTop:"1px solid rgba(255,255,255,0.06)"}}>
        <input
          value={input}
          onChange={e=>setInput(e.target.value)}
          onKeyDown={e=>e.key==="Enter"&&send()}
          placeholder="Nhập câu hỏi..."
          style={{flex:1,background:"rgba(255,255,255,0.07)",border:"1px solid rgba(255,255,255,0.1)",borderRadius:10,padding:"9px 13px",color:"#fff",fontSize:13,outline:"none"}}
        />
        <button onClick={send} disabled={loading||!input.trim()} style={{width:40,height:40,borderRadius:10,background:loading||!input.trim()?"rgba(255,255,255,0.08)":"linear-gradient(135deg,#FF6B2B,#FF4500)",border:"none",color:"#fff",cursor:loading||!input.trim()?"not-allowed":"pointer",fontSize:18,display:"flex",alignItems:"center",justifyContent:"center",flexShrink:0}}>
          ➤
        </button>
      </div>
    </div>
  );
}

// ─── MAIN APP ────────────────────────────────────────────────────────────────
export default function App(){
  const [tab,setTab]=useState("dashboard");
  const [products,setProducts]=useState(IP);
  const [customers,setCustomers]=useState(IC);
  const [orders,setOrders]=useState(IO);
  const [cart,setCart]=useState([]);
  const [searchP,setSearchP]=useState("");
  const [searchC,setSearchC]=useState("");
  const [selCust,setSelCust]=useState(null);
  const [payment,setPayment]=useState("Tiền mặt");
  const [discount,setDiscount]=useState(0);
  const [showReceipt,setShowReceipt]=useState(false);
  const [lastOrder,setLastOrder]=useState(null);
  const [catFilter,setCatFilter]=useState("Tất cả");
  const [showAddP,setShowAddP]=useState(false);
  const [newP,setNewP]=useState({sku:"",name:"",category:"iPhone",brand:"",color:"",price:"",costPrice:"",stock:"",minStock:3,image:"📱"});
  const [notif,setNotif]=useState(null);
  const [sidebar,setSidebar]=useState(true);
  const [showChat,setShowChat]=useState(false);
  const [cacheLoaded,setCacheLoaded]=useState(false);
  const [syncStatus,setSyncStatus]=useState(null); 
  const [mobileNav,setMobileNav]=useState(false);
  const saveTimer=useRef(null);
  const isMobile=typeof window!=="undefined"&&window.innerWidth<768;

  const showNotif=(msg,type="success")=>{setNotif({msg,type});setTimeout(()=>setNotif(null),3000);};

  // ── LOAD SHARED CACHE ──
  useEffect(()=>{
    (async()=>{
      const cached=await loadShared();
      if(cached){
        if(cached.products)setProducts(cached.products);
        if(cached.customers)setCustomers(cached.customers);
        if(cached.orders)setOrders(cached.orders);
        if(cached.cart?.length>0)setCart(cached.cart);
        if(cached.selCust)setSelCust(cached.selCust);
        if(cached.payment)setPayment(cached.payment);
        if(cached.discount!=null)setDiscount(cached.discount);
        if(cached.newP)setNewP(cached.newP);
        if(cached.tab)setTab(cached.tab);
        setSyncStatus("loaded");
        setTimeout(()=>setSyncStatus(null),2500);
      }
      setCacheLoaded(true);
    })();
  },[]);

  // ── AUTO-SAVE SHARED ──
  const save=useCallback((data)=>{
    if(!cacheLoaded)return;
    if(saveTimer.current)clearTimeout(saveTimer.current);
    setSyncStatus("saving");
    saveTimer.current=setTimeout(async()=>{
      try{
        await saveShared(data);
        setSyncStatus("saved");
        setTimeout(()=>setSyncStatus(null),2000);
      }catch{
        setSyncStatus("error");
        setTimeout(()=>setSyncStatus(null),3000);
      }
    },1000);
  },[cacheLoaded]);

  useEffect(()=>{
    if(!cacheLoaded)return;
    save({products,customers,orders,cart,selCust,payment,discount,newP,tab});
  },[products,customers,orders,cart,selCust,payment,discount,newP,tab,cacheLoaded]);

  const resetCache=async()=>{
    await deleteShared();
    setProducts(IP);setCustomers(IC);setOrders(IO);setCart([]);
    setSelCust(null);setPayment("Tiền mặt");setDiscount(0);
    setNewP({sku:"",name:"",category:"iPhone",brand:"",color:"",price:"",costPrice:"",stock:"",minStock:3,image:"📱"});
    showNotif("Đã xóa dữ liệu đồng bộ & khôi phục mặc định");
  };

  // ── CART ──
  const addToCart=p=>{
    if(p.stock===0){showNotif("Hết hàng!","error");return;}
    setCart(prev=>{
      const ex=prev.find(i=>i.id===p.id);
      if(ex){if(ex.qty>=p.stock){showNotif("Không đủ tồn kho!","error");return prev;}return prev.map(i=>i.id===p.id?{...i,qty:i.qty+1}:i);}
      return[...prev,{...p,qty:1}];
    });
  };
  const removeFromCart=id=>setCart(p=>p.filter(i=>i.id!==id));
  const updateQty=(id,qty)=>{
    if(qty<=0){removeFromCart(id);return;}
    const pr=products.find(p=>p.id===id);
    if(qty>pr.stock){showNotif("Không đủ tồn kho!","error");return;}
    setCart(p=>p.map(i=>i.id===id?{...i,qty}:i));
  };
  const cartTotal=cart.reduce((s,i)=>s+i.price*i.qty,0);
  const discAmt=Math.round(cartTotal*(discount/100));
  const finalTotal=cartTotal-discAmt;

  const checkout=()=>{
    if(cart.length===0){showNotif("Giỏ hàng trống!","error");return;}
    const id="HD"+String(orders.length+1).padStart(3,"0");
    const o={id,customer:selCust?selCust.name:"Khách lẻ",items:cart.map(i=>({name:i.name,qty:i.qty,price:i.price*i.qty})),total:finalTotal,payment,status:"Hoàn thành",date:new Date().toLocaleString("vi-VN")};
    setOrders(p=>[o,...p]);
    setProducts(p=>p.map(pr=>{const ci=cart.find(c=>c.id===pr.id);return ci?{...pr,stock:pr.stock-ci.qty,sold:pr.sold+ci.qty}:pr;}));
    if(selCust){const pts=Math.floor(finalTotal/100000);setCustomers(p=>p.map(c=>c.id===selCust.id?{...c,points:c.points+pts,totalSpent:c.totalSpent+finalTotal,orders:c.orders+1}:c));}
    setLastOrder(o);setCart([]);setSelCust(null);setDiscount(0);setShowReceipt(true);
    showNotif(`Thanh toán thành công ${fmt(finalTotal)}!`);
  };

  const addProduct=()=>{
    if(!newP.sku||!newP.name||!newP.price){showNotif("Vui lòng điền đầy đủ!","error");return;}
    const p={...newP,id:products.length+1,price:+newP.price,costPrice:+newP.costPrice,stock:+newP.stock,minStock:+newP.minStock,sold:0};
    setProducts(prev=>[p,...prev]);
    setShowAddP(false);
    setNewP({sku:"",name:"",category:"iPhone",brand:"",color:"",price:"",costPrice:"",stock:"",minStock:3,image:"📱"});
    showNotif("Thêm sản phẩm thành công!");
  };

  const cats=["Tất cả",...new Set(products.map(p=>p.category))];
  const filteredP=products.filter(p=>{
    const mc=catFilter==="Tất cả"||p.category===catFilter;
    const ms=p.name.toLowerCase().includes(searchP.toLowerCase())||p.sku.toLowerCase().includes(searchP.toLowerCase());
    return mc&&ms;
  });
  const lowStock=products.filter(p=>p.stock<=p.minStock);
  const todayRev=orders.filter(o=>o.status==="Hoàn thành").reduce((s,o)=>s+o.total,0);
  const totalProfit=RD.reduce((s,d)=>s+(d.revenue-d.cost),0);

  const navItems=[
    {id:"dashboard",icon:"📊",label:"Tổng quan"},
    {id:"pos",icon:"🛒",label:"Bán hàng"},
    {id:"products",icon:"📦",label:"Hàng hóa"},
    {id:"orders",icon:"🧾",label:"Hóa đơn"},
    {id:"customers",icon:"👥",label:"Khách hàng"},
    {id:"staff",icon:"👔",label:"Nhân sự"},
    {id:"reports",icon:"📈",label:"Báo cáo"},
  ];

  const syncCfg={
    saving:{bg:"rgba(255,149,0,0.12)",border:"rgba(255,149,0,0.3)",color:"#FF9500",label:"☁️ Đang lưu…",spin:true},
    saved:{bg:"rgba(52,199,89,0.12)",border:"rgba(52,199,89,0.3)",color:"#34C759",label:"☁️ Đã lưu"},
    loaded:{bg:"rgba(0,122,255,0.12)",border:"rgba(0,122,255,0.3)",color:"#007AFF",label:"📲 Đã khôi phục"},
    error:{bg:"rgba(255,59,48,0.12)",border:"rgba(255,59,48,0.3)",color:"#FF3B30",label:"⚠️ Lỗi lưu"},
  }[syncStatus]||null;

  if(showReceipt&&lastOrder){
    return(
      <div style={{position:"fixed",inset:0,background:"rgba(0,0,0,0.85)",display:"flex",alignItems:"center",justifyContent:"center",zIndex:9999,padding:16}}>
        <style>{`@keyframes popIn{from{opacity:0;transform:scale(0.85)}to{opacity:1;transform:scale(1)}}`}</style>
        <div style={{background:"#132040",borderRadius:20,padding:28,width:"100%",maxWidth:380,border:"1px solid rgba(255,255,255,0.1)",boxShadow:"0 20px 60px rgba(0,0,0,0.5)",animation:"popIn 0.3s ease"}}>
          <div style={{textAlign:"center",marginBottom:20}}>
            <div style={{fontSize:52}}>✅</div>
            <div style={{fontSize:20,fontWeight:800,color:"#fff",marginTop:6}}>Thanh toán thành công!</div>
            <div style={{fontSize:13,color:"rgba(255,255,255,0.4)",marginTop:3}}>Hóa đơn #{lastOrder.id}</div>
          </div>
          <div style={{background:"rgba(255,255,255,0.04)",borderRadius:12,padding:14,marginBottom:16}}>
            {lastOrder.items.map((item,i)=>(
              <div key={i} style={{display:"flex",justifyContent:"space-between",marginBottom:6,fontSize:13}}>
                <span style={{color:"rgba(255,255,255,0.7)"}}>{item.name}</span>
                <span style={{color:"#fff",fontWeight:600}}>{fmt(item.price)}</span>
              </div>
            ))}
            <div style={{height:1,background:"rgba(255,255,255,0.08)",margin:"10px 0"}}/>
            <div style={{display:"flex",justifyContent:"space-between",fontWeight:800,fontSize:16}}>
              <span style={{color:"#fff"}}>Tổng cộng</span>
              <span style={{color:"#FF6B2B"}}>{fmt(lastOrder.total)}</span>
            </div>
            <div style={{marginTop:6,fontSize:11,color:"rgba(255,255,255,0.35)"}}>Thanh toán: {lastOrder.payment} • {lastOrder.date}</div>
          </div>
          <button onClick={()=>setShowReceipt(false)} style={{width:"100%",padding:13,background:"linear-gradient(135deg,#FF6B2B,#FF4500)",border:"none",borderRadius:12,color:"#fff",fontWeight:700,fontSize:15,cursor:"pointer"}}>
            Hóa đơn mới
          </button>
        </div>
      </div>
    );
  }

  const C="#0B1629",C2="#0F2547",C3="#132040";
  const inp={background:"rgba(255,255,255,0.06)",border:"1px solid rgba(255,255,255,0.1)",borderRadius:10,padding:"9px 13px",color:"#fff",fontSize:13,outline:"none",width:"100%",boxSizing:"border-box"};
  const card={background:C3,border:"1px solid rgba(255,255,255,0.07)",borderRadius:14,padding:20};
  const tag=(cl)=>({padding:"2px 8px",borderRadius:6,fontSize:11,fontWeight:600,background:cl+"22",color:cl});
  const btnP={background:"linear-gradient(135deg,#FF6B2B,#FF4500)",color:"#fff",border:"none",borderRadius:10,padding:"10px 18px",cursor:"pointer",fontSize:13,fontWeight:700,boxShadow:"0 4px 14px rgba(255,107,43,0.35)"};
  const btnS={background:"rgba(255,255,255,0.06)",color:"rgba(255,255,255,0.7)",border:"1px solid rgba(255,255,255,0.1)",borderRadius:9,padding:"9px 16px",cursor:"pointer",fontSize:13,fontWeight:500};
  const th={textAlign:"left",padding:"10px 14px",fontSize:11,color:"rgba(255,255,255,0.4)",fontWeight:600,textTransform:"uppercase",letterSpacing:0.6,borderBottom:"1px solid rgba(255,255,255,0.07)"};
  const td={padding:"12px 14px",fontSize:13,color:"rgba(255,255,255,0.8)",borderBottom:"1px solid rgba(255,255,255,0.04)"};

  const NAV_W=sidebar?220:64;

  return(
    <div style={{display:"flex",height:"100vh",background:C,fontFamily:"'Inter',-apple-system,sans-serif",color:"#E8EDF5",overflow:"hidden",position:"relative"}}>
      <style>{`
        *{box-sizing:border-box;}
        ::-webkit-scrollbar{width:4px;height:4px;}
        ::-webkit-scrollbar-track{background:transparent;}
        ::-webkit-scrollbar-thumb{background:rgba(255,255,255,0.1);border-radius:2px;}
        @keyframes spin{from{transform:rotate(0deg)}to{transform:rotate(360deg)}}
        @keyframes fadeUp{from{opacity:0;transform:translateY(8px)}to{opacity:1;transform:translateY(0)}}
        @keyframes bounce{0%,100%{transform:translateY(0)}50%{transform:translateY(-5px)}}
        @keyframes slideIn{from{transform:translateX(-100%)}to{transform:translateX(0)}}
        @media(max-width:767px){
          .desktop-only{display:none!important;}
          .mobile-table{display:block!important;}
        }
        @media(min-width:768px){
          .mobile-only{display:none!important;}
        }
        button:hover{opacity:0.88;}
      `}</style>

      {mobileNav&&(
        <div style={{position:"fixed",inset:0,zIndex:200}}>
          <div onClick={()=>setMobileNav(false)} style={{position:"absolute",inset:0,background:"rgba(0,0,0,0.6)"}}/>
          <aside style={{position:"absolute",left:0,top:0,bottom:0,width:240,background:C2,borderRight:"1px solid rgba(255,255,255,0.06)",display:"flex",flexDirection:"column",animation:"slideIn 0.2s ease",zIndex:201}}>
            <div style={{padding:"20px 16px 14px",borderBottom:"1px solid rgba(255,255,255,0.06)",display:"flex",alignItems:"center",gap:10}}>
              <div style={{width:36,height:36,background:"linear-gradient(135deg,#FF6B2B,#FF9A6B)",borderRadius:10,display:"flex",alignItems:"center",justifyContent:"center",fontSize:18}}>📱</div>
              <div><div style={{fontWeight:800,fontSize:15,color:"#fff"}}>PhoneShop Pro</div><div style={{fontSize:10,color:"rgba(255,255,255,0.4)"}}>Quản lý bán hàng</div></div>
            </div>
            <nav style={{flex:1,padding:"12px 8px",display:"flex",flexDirection:"column",gap:2}}>
              {navItems.map(item=>(
                <button key={item.id} onClick={()=>{setTab(item.id);setMobileNav(false);}} style={{display:"flex",alignItems:"center",gap:10,padding:"11px 14px",borderRadius:10,border:"none",cursor:"pointer",background:tab===item.id?"rgba(255,107,43,0.15)":"transparent",color:tab===item.id?"#FF6B2B":"rgba(255,255,255,0.55)",fontSize:14,fontWeight:tab===item.id?600:400,width:"100%",textAlign:"left",borderLeft:tab===item.id?"3px solid #FF6B2B":"3px solid transparent"}}>
                  <span style={{fontSize:18}}>{item.icon}</span>{item.label}
                  {item.id==="products"&&lowStock.length>0&&<span style={{marginLeft:"auto",background:"#FF3B30",color:"#fff",borderRadius:10,padding:"1px 7px",fontSize:10,fontWeight:700}}>{lowStock.length}</span>}
                </button>
              ))}
            </nav>
            <div style={{padding:"12px 16px",borderTop:"1px solid rgba(255,255,255,0.06)"}}>
              <button onClick={()=>{resetCache();setMobileNav(false);}} style={{...btnS,width:"100%",fontSize:12,padding:"8px 12px"}}>🗑️ Xóa dữ liệu đồng bộ</button>
            </div>
          </aside>
        </div>
      )}

      <aside className="desktop-only" style={{width:NAV_W,background:C2,display:"flex",flexDirection:"column",transition:"width 0.25s ease",flexShrink:0,borderRight:"1px solid rgba(255,255,255,0.06)"}}>
        <div style={{padding:sidebar?"20px 20px 14px":"20px 12px 14px",borderBottom:"1px solid rgba(255,255,255,0.06)",display:"flex",alignItems:"center",gap:10}}>
          <div style={{width:36,height:36,background:"linear-gradient(135deg,#FF6B2B,#FF9A6B)",borderRadius:10,display:"flex",alignItems:"center",justifyContent:"center",fontSize:18,flexShrink:0}}>📱</div>
          {sidebar&&<div><div style={{fontWeight:800,fontSize:15,color:"#fff",whiteSpace:"nowrap"}}>PhoneShop Pro</div><div style={{fontSize:10,color:"rgba(255,255,255,0.4)"}}>Quản lý bán hàng</div></div>}
        </div>
        <nav style={{flex:1,padding:"12px 8px",display:"flex",flexDirection:"column",gap:2}}>
          {navItems.map(item=>(
            <button key={item.id} onClick={()=>setTab(item.id)} style={{display:"flex",alignItems:"center",gap:10,padding:sidebar?"10px 12px":"10px",borderRadius:10,border:"none",cursor:"pointer",background:tab===item.id?"rgba(255,107,43,0.15)":"transparent",color:tab===item.id?"#FF6B2B":"rgba(255,255,255,0.55)",fontSize:13.5,fontWeight:tab===item.id?600:400,width:"100%",textAlign:"left",borderLeft:tab===item.id?"3px solid #FF6B2B":"3px solid transparent",transition:"all 0.15s"}}>
              <span style={{fontSize:17,flexShrink:0}}>{item.icon}</span>
              {sidebar&&<><span style={{whiteSpace:"nowrap"}}>{item.label}</span>{item.id==="products"&&lowStock.length>0&&<span style={{marginLeft:"auto",background:"#FF3B30",color:"#fff",borderRadius:10,padding:"1px 7px",fontSize:10,fontWeight:700}}>{lowStock.length}</span>}</>}
            </button>
          ))}
        </nav>
        <button onClick={()=>setSidebar(!sidebar)} style={{margin:"0 8px 12px",padding:"8px",background:"rgba(255,255,255,0.04)",border:"1px solid rgba(255,255,255,0.08)",borderRadius:8,cursor:"pointer",color:"rgba(255,255,255,0.4)",fontSize:13,textAlign:"center"}}>
          {sidebar?"◀":"▶"}
        </button>
        {sidebar&&<div style={{padding:"0 12px 14px",fontSize:11,color:"rgba(255,255,255,0.25)",textAlign:"center"}}>Chi nhánh: Cầu Giấy, HN</div>}
      </aside>

      <main style={{flex:1,display:"flex",flexDirection:"column",overflow:"hidden",minWidth:0}}>
        <header style={{padding:"12px 16px",background:C2,borderBottom:"1px solid rgba(255,255,255,0.06)",display:"flex",alignItems:"center",justifyContent:"space-between",gap:10,flexShrink:0}}>
          <div style={{display:"flex",alignItems:"center",gap:12}}>
            <button className="mobile-only" onClick={()=>setMobileNav(true)} style={{background:"rgba(255,255,255,0.07)",border:"1px solid rgba(255,255,255,0.1)",borderRadius:9,padding:"8px 11px",cursor:"pointer",color:"#fff",fontSize:16}}>☰</button>
            <div>
              <div style={{fontSize:16,fontWeight:700,color:"#fff"}}>{navItems.find(n=>n.id===tab)?.label}</div>
              <div style={{fontSize:10,color:"rgba(255,255,255,0.35)"}}>{new Date().toLocaleDateString("vi-VN",{weekday:"long",year:"numeric",month:"long",day:"numeric"})}</div>
            </div>
          </div>
          <div style={{display:"flex",alignItems:"center",gap:8,flexShrink:0}}>
            {syncCfg&&(
              <div style={{background:syncCfg.bg,border:`1px solid ${syncCfg.border}`,borderRadius:8,padding:"5px 10px",fontSize:11,color:syncCfg.color,display:"flex",alignItems:"center",gap:5,whiteSpace:"nowrap"}}>
                {syncCfg.spin&&<span style={{display:"inline-block",animation:"spin 1s linear infinite"}}>⟳</span>}
                {syncCfg.label}
              </div>
            )}
            {lowStock.length>0&&(
              <div className="desktop-only" style={{background:"rgba(255,59,48,0.12)",border:"1px solid rgba(255,59,48,0.3)",borderRadius:8,padding:"5px 10px",fontSize:11,color:"#FF3B30",whiteSpace:"nowrap"}}>
                ⚠️ {lowStock.length} sắp hết
              </div>
            )}
            <button className="desktop-only" onClick={resetCache} title="Xóa dữ liệu" style={{background:"rgba(255,59,48,0.1)",border:"1px solid rgba(255,59,48,0.2)",borderRadius:8,padding:"7px 10px",cursor:"pointer",color:"rgba(255,100,100,0.7)",fontSize:13}}>🗑️</button>
            <div style={{display:"flex",alignItems:"center",gap:7,background:"rgba(255,255,255,0.05)",padding:"7px 10px",borderRadius:10,border:"1px solid rgba(255,255,255,0.08)"}}>
              <div style={{width:28,height:28,background:"linear-gradient(135deg,#FF6B2B,#FF9A6B)",borderRadius:"50%",display:"flex",alignItems:"center",justifyContent:"center",fontSize:13}}>👤</div>
              <div className="desktop-only">
                <div style={{fontSize:11,fontWeight:600,color:"#fff"}}>Nguyễn Thị Mai</div>
                <div style={{fontSize:9,color:"rgba(255,255,255,0.4)"}}>Quản lý</div>
              </div>
            </div>
          </div>
        </header>

        <div style={{flex:1,overflow:"auto",padding:"16px"}}>
          {tab==="dashboard"&&(
            <div>
              <div style={{display:"grid",gridTemplateColumns:"repeat(auto-fit,minmax(160px,1fr))",gap:12,marginBottom:16}}>
                {[
                  {label:"Doanh thu",value:fmtS(todayRev)+"đ",icon:"💰",accent:"#FF6B2B",sub:`${orders.filter(o=>o.status==="Hoàn thành").length} đơn hoàn thành`},
                  {label:"Đơn hàng",value:orders.length,icon:"🧾",accent:"#007AFF",sub:"Hôm nay"},
                  {label:"Tổng tồn kho",value:products.reduce((s,p)=>s+p.stock,0),icon:"📦",accent:"#34C759",sub:`${lowStock.length} cần nhập`},
                  {label:"Lợi nhuận T6",value:fmtS(totalProfit)+"đ",icon:"📈",accent:"#AF52DE",sub:"Tỷ suất 25.3%"},
                ].map((s,i)=>(
                  <div key={i} style={{background:`linear-gradient(135deg,${s.accent}22,${s.accent}08)`,border:`1px solid ${s.accent}30`,borderRadius:14,padding:16}}>
                    <div style={{display:"flex",justifyContent:"space-between",alignItems:"flex-start"}}>
                      <div>
                        <div style={{fontSize:10,color:"rgba(255,255,255,0.4)",textTransform:"uppercase",letterSpacing:0.7,fontWeight:600}}>{s.label}</div>
                        <div style={{fontSize:22,fontWeight:800,color:s.accent,lineHeight:1.2,marginTop:4}}>{s.value}</div>
                        <div style={{fontSize:10,color:"rgba(255,255,255,0.4)",marginTop:3}}>{s.sub}</div>
                      </div>
                      <div style={{fontSize:24}}>{s.icon}</div>
                    </div>
                  </div>
                ))}
              </div>
              <div style={{display:"grid",gridTemplateColumns:"minmax(0,2fr) minmax(0,1fr)",gap:12,marginBottom:12}}>
                <div style={card}>
                  <div style={{display:"flex",justifyContent:"space-between",alignItems:"center",marginBottom:14}}>
                    <div style={{fontSize:14,fontWeight:700,color:"#fff"}}>Doanh thu 6 tháng</div>
                    <div style={{display:"flex",gap:8,fontSize:10}}><span style={tag("#FF6B2B")}>■ Doanh thu</span><span style={tag("rgba(255,255,255,0.4)")}>■ Chi phí</span></div>
                  </div>
                  <MiniBar data={RD}/>
                  <div style={{height:1,background:"rgba(255,255,255,0.06)",margin:"12px 0"}}/>
                  <div style={{display:"grid",gridTemplateColumns:"repeat(3,1fr)",gap:8}}>
                    {[{l:"Tổng DT",v:fmtS(RD.reduce((s,d)=>s+d.revenue,0))+"đ"},{l:"Chi phí",v:fmtS(RD.reduce((s,d)=>s+d.cost,0))+"đ"},{l:"LN",v:fmtS(totalProfit)+"đ"}].map((m,i)=>(
                      <div key={i} style={{textAlign:"center",padding:8,background:"rgba(255,255,255,0.03)",borderRadius:8}}>
                        <div style={{fontSize:9,color:"rgba(255,255,255,0.4)",marginBottom:2}}>{m.l}</div>
                        <div style={{fontSize:13,fontWeight:700,color:"#FF6B2B"}}>{m.v}</div>
                      </div>
                    ))}
                  </div>
                </div>
                <div style={card}>
                  <div style={{fontSize:14,fontWeight:700,color:"#fff",marginBottom:12}}>Top bán chạy</div>
                  {[...products].sort((a,b)=>b.sold-a.sold).slice(0,5).map((p,i)=>{
                    const mx=Math.max(...products.map(x=>x.sold));
                    return(
                      <div key={p.id} style={{marginBottom:10}}>
                        <div style={{display:"flex",justifyContent:"space-between",marginBottom:3}}>
                          <div style={{fontSize:11,color:"rgba(255,255,255,0.7)",display:"flex",alignItems:"center",gap:5}}>
                            <span style={{fontSize:9,fontWeight:700,color:i<3?"#FF6B2B":"rgba(255,255,255,0.3)",width:14}}>#{i+1}</span>
                            <span style={{overflow:"hidden",textOverflow:"ellipsis",whiteSpace:"nowrap",maxWidth:110}}>{p.name}</span>
                          </div>
                          <span style={{fontSize:10,color:"#FF6B2B",fontWeight:700,flexShrink:0}}>{p.sold}</span>
                        </div>
                        <div style={{height:3,background:"rgba(255,255,255,0.06)",borderRadius:2}}>
                          <div style={{height:"100%",width:`${(p.sold/mx)*100}%`,background:i<3?"linear-gradient(90deg,#FF6B2B,#FF9A6B)":"rgba(255,255,255,0.2)",borderRadius:2}}/>
                        </div>
                      </div>
                    );
                  })}
                </div>
              </div>
              <div style={{display:"grid",gridTemplateColumns:"minmax(0,1fr) minmax(0,1fr)",gap:12}}>
                <div style={card}>
                  <div style={{fontSize:14,fontWeight:700,color:"#fff",marginBottom:12}}>Hóa đơn gần nhất</div>
                  {orders.slice(0,4).map(o=>(
                    <div key={o.id} style={{display:"flex",justifyContent:"space-between",alignItems:"center",padding:"9px 0",borderBottom:"1px solid rgba(255,255,255,0.04)"}}>
                      <div>
                        <div style={{fontSize:12,fontWeight:700,color:"#FF6B2B"}}>{o.id}</div>
                        <div style={{fontSize:11,color:"rgba(255,255,255,0.45)"}}>{o.customer}</div>
                      </div>
                      <div style={{textAlign:"right"}}>
                        <div style={{fontSize:12,fontWeight:700,color:"#fff"}}>{fmtS(o.total)}đ</div>
                        <span style={tag(o.status==="Hoàn thành"?"#34C759":"#FF9500")}>{o.status}</span>
                      </div>
                    </div>
                  ))}
                </div>
                <div style={card}>
                  <div style={{fontSize:14,fontWeight:700,color:"#fff",marginBottom:12}}>⚠️ Sắp hết hàng</div>
                  {lowStock.length===0?<div style={{textAlign:"center",padding:20,color:"rgba(255,255,255,0.3)",fontSize:13}}>✅ Tồn kho ổn định</div>:
                    lowStock.map(p=>(
                      <div key={p.id} style={{display:"flex",justifyContent:"space-between",alignItems:"center",padding:"9px 0",borderBottom:"1px solid rgba(255,255,255,0.04)"}}>
                        <div style={{fontSize:12,color:"#fff",fontWeight:500,maxWidth:"70%",overflow:"hidden",textOverflow:"ellipsis",whiteSpace:"nowrap"}}>{p.name}</div>
                        <div style={{textAlign:"right",flexShrink:0}}>
                          <div style={{fontSize:16,fontWeight:800,color:p.stock===0?"#FF3B30":"#FF9500"}}>{p.stock}</div>
                          <div style={{fontSize:9,color:"rgba(255,255,255,0.3)"}}>tối thiểu {p.minStock}</div>
                        </div>
                      </div>
                    ))
                  }
                </div>
              </div>
            </div>
          )}

          {tab==="pos"&&(
            <div style={{display:"grid",gridTemplateColumns:"minmax(0,1fr) 340px",gap:14,height:"calc(100vh - 120px)"}}>
              <div style={{display:"flex",flexDirection:"column",gap:10,overflow:"hidden",minWidth:0}}>
                <div style={{display:"flex",gap:8}}>
                  <input style={{...inp,flex:1}} placeholder="🔍 Tìm sản phẩm, mã SKU…" value={searchP} onChange={e=>setSearchP(e.target.value)}/>
                  <select style={{...inp,width:130}} value={catFilter} onChange={e=>setCatFilter(e.target.value)}>
                    {cats.map(c=><option key={c} value={c}>{c}</option>)}
                  </select>
                </div>
                <div style={{display:"grid",gridTemplateColumns:"repeat(auto-fill,minmax(150px,1fr))",gap:8,overflow:"auto",paddingBottom:4}}>
                  {filteredP.map(p=>(
                    <button key={p.id} onClick={()=>addToCart(p)} style={{background:p.stock===0?"rgba(255,255,255,0.02)":C3,border:p.stock===0?"1px solid rgba(255,255,255,0.04)":"1px solid rgba(255,255,255,0.08)",borderRadius:12,padding:12,cursor:p.stock===0?"not-allowed":"textAlign","left",opacity:p.stock===0?0.4:1,transition:"all 0.15s"}}>
                      <div style={{fontSize:26,marginBottom:5}}>{p.image}</div>
                      <div style={{fontSize:11,fontWeight:600,color:"#fff",lineHeight:1.3,marginBottom:3}}>{p.name.length>28?p.name.slice(0,28)+"…":p.name}</div>
                      <div style={{fontSize:13,fontWeight:800,color:"#FF6B2B",marginBottom:4}}>{fmtS(p.price)}đ</div>
                      <span style={tag(p.stock===0?"#FF3B30":p.stock<=p.minStock?"#FF9500":"#34C759")}>{p.stock===0?"Hết":`${p.stock} còn`}</span>
                    </button>
                  ))}
                </div>
              </div>
              <div style={{...card,display:"flex",flexDirection:"column",overflow:"hidden",minWidth:0}}>
                <div style={{fontSize:14,fontWeight:700,color:"#fff",marginBottom:10}}>🛒 Hóa đơn</div>
                <select style={{...inp,marginBottom:10}} value={selCust?.id||""} onChange={e=>setSelCust(customers.find(c=>c.id===+e.target.value)||null)}>
                  <option value="">👤 Khách lẻ</option>
                  {customers.map(c=><option key={c.id} value={c.id}>{c.name} — {c.points} điểm</option>)}
                </select>
                <div style={{flex:1,overflow:"auto"}}>
                  {cart.length===0?<div style={{textAlign:"center",padding:"30px 10px",color:"rgba(255,255,255,0.2)"}}>
                    <div style={{fontSize:36,marginBottom:6}}>🛒</div>
                    <div style={{fontSize:12}}>Chọn sản phẩm bên trái</div>
                  </div>:cart.map(item=>(
                    <div key={item.id} style={{padding:"9px 0",borderBottom:"1px solid rgba(255,255,255,0.05)"}}>
                      <div style={{display:"flex",justifyContent:"space-between",alignItems:"flex-start"}}>
                        <div style={{flex:1,marginRight:6}}>
                          <div style={{fontSize:11,fontWeight:600,color:"#fff",lineHeight:1.3}}>{item.name}</div>
                          <div style={{fontSize:11,color:"#FF6B2B",marginTop:2}}>{fmt(item.price)}</div>
                        </div>
                        <button onClick={()=>removeFromCart(item.id)} style={{background:"none",border:"none",color:"rgba(255,255,255,0.3)",cursor:"pointer",fontSize:16,padding:2}}>×</button>
                      </div>
                      <div style={{display:"flex",justifyContent:"space-between",alignItems:"center",marginTop:6}}>
                        <div style={{display:"flex",alignItems:"center",gap:4}}>
                          <button onClick={()=>updateQty(item.id,item.qty-1)} style={{width:26,height:26,borderRadius:6,background:"rgba(255,255,255,0.08)",border:"none",color:"#fff",cursor:"pointer",fontSize:15}}>−</button>
                          <span style={{width:26,textAlign:"center",fontSize:13,fontWeight:700,color:"#fff"}}>{item.qty}</span>
                          <button onClick={()=>updateQty(item.id,item.qty+1)} style={{width:26,height:26,borderRadius:6,background:"rgba(255,255,255,0.08)",border:"none",color:"#fff",cursor:"pointer",fontSize:15}}>+</button>
                        </div>
                        <span style={{fontSize:13,fontWeight:800,color:"#fff"}}>{fmt(item.price*item.qty)}</span>
                      </div>
                    </div>
                  ))}
                </div>
                <div style={{marginTop:10}}>
                  <div style={{height:1,background:"rgba(255,255,255,0.06)",marginBottom:10}}/>
                  <div style={{display:"flex",gap:5,marginBottom:10}}>
                    {["Tiền mặt","Thẻ","QR","CK"].map(m=>(
                      <button key={m} onClick={()=>setPayment(m==="CK"?"Chuyển khoản":m)} style={{flex:1,padding:"6px 2px",borderRadius:8,border:`1px solid ${payment===(m==="CK"?"Chuyển khoản":m)?"#FF6B2B":"rgba(255,255,255,0.1)"}`,background:payment===(m==="CK"?"Chuyển khoản":m)?"rgba(255,107,43,0.15)":"transparent",color:payment===(m==="CK"?"Chuyển khoản":m)?"#FF6B2B":"rgba(255,255,255,0.4)",cursor:"pointer",fontSize:10,fontWeight:600}}>
                        {m==="Tiền mặt"?"💵":m==="Thẻ"?"💳":m==="QR"?"📲":"🏦"} {m}
                      </button>
                    ))}
                  </div>
                  <div style={{display:"flex",alignItems:"center",gap:6,marginBottom:8}}>
                    <span style={{fontSize:11,color:"rgba(255,255,255,0.4)",flexShrink:0}}>Chiết khấu %</span>
                    <input type="number" style={{...inp,width:60}} value={discount} onChange={e=>setDiscount(Math.min(100,Math.max(0,+e.target.value)))} min="0" max="100"/>
                    <div style={{display:"flex",gap:4,flex:1}}>
                      {[5,10,15].map(d=><button key={d} onClick={()=>setDiscount(d)} style={{flex:1,padding:"5px 2px",borderRadius:6,border:"1px solid rgba(255,255,255,0.08)",background:"rgba(255,255,255,0.04)",color:"rgba(255,255,255,0.5)",cursor:"pointer",fontSize:10}}>{d}%</button>)}
                    </div>
                  </div>
                  <div style={{background:"rgba(255,255,255,0.04)",borderRadius:10,padding:10,marginBottom:10}}>
                    <div style={{display:"flex",justifyContent:"space-between",fontSize:11,marginBottom:3}}>
                      <span style={{color:"rgba(255,255,255,0.45)"}}>Tạm tính</span><span>{fmt(cartTotal)}</span>
                    </div>
                    {discount>0&&<div style={{display:"flex",justifyContent:"space-between",fontSize:11,marginBottom:3}}>
                      <span style={{color:"rgba(255,255,255,0.45)"}}>Chiết khấu</span><span style={{color:"#34C759"}}>−{fmt(discAmt)}</span>
                    </div>}
                    <div style={{display:"flex",justifyContent:"space-between",fontWeight:800,fontSize:15,paddingTop:6,borderTop:"1px solid rgba(255,255,255,0.08)",marginTop:4}}>
                      <span style={{color:"#fff"}}>Tổng cộng</span><span style={{color:"#FF6B2B"}}>{fmt(finalTotal)}</span>
                    </div>
                  </div>
                  <button onClick={checkout} style={{...btnP,width:"100%",padding:12,fontSize:14,textAlign:"center"}}>
                    💳 Thanh toán{cart.length>0?` (${cart.length})`:""}
                  </button>
                </div>
              </div>
            </div>
          )}

          {tab==="products"&&(
            <div>
              <div style={{display:"flex",flexWrap:"wrap",gap:8,justifyContent:"space-between",alignItems:"center",marginBottom:14}}>
                <div style={{display:"flex",gap:8,flex:1,minWidth:0}}>
                  <input style={{...inp,maxWidth:240}} placeholder="🔍 Tìm tên, SKU…" value={searchP} onChange={e=>setSearchP(e.target.value)}/>
                  <select style={{...inp,width:130}} value={catFilter} onChange={e=>setCatFilter(e.target.value)}>
                    {cats.map(c=><option key={c} value={c}>{c}</option>)}
                  </select>
                </div>
                <button onClick={()=>setShowAddP(true)} style={btnP}>+ Thêm SP</button>
              </div>
              {showAddP&&(
                <div style={{...card,marginBottom:14,border:"1px solid rgba(255,107,43,0.3)"}}>
                  <div style={{fontSize:14,fontWeight:700,color:"#FF6B2B",marginBottom:12,display:"flex",justifyContent:"space-between",alignItems:"center"}}>
                    <span>➕ Thêm sản phẩm mới</span>
                  </div>
                  <div style={{display:"grid",gridTemplateColumns:"repeat(auto-fit,minmax(180px,1fr))",gap:10,marginBottom:12}}>
                    {[{l:"Mã SKU *",k:"sku",ph:"VD: IPH15-BLK"},{l:"Tên sản phẩm *",k:"name",ph:"VD: iPhone 15"},{l:"Thương hiệu",k:"brand",ph:"Apple"},{l:"Màu sắc",k:"color",ph:"Đen Titan"},{l:"Giá bán *",k:"price",ph:"22490000",t:"number"},{l:"Giá vốn",k:"costPrice",ph:"17800000",t:"number"},{l:"Tồn kho",k:"stock",ph:"10",t:"number"},{l:"Tối thiểu",k:"minStock",ph:"3",t:"number"}].map(f=>(
                      <div key={f.k}>
                        <div style={{fontSize:10,color:"rgba(255,255,255,0.4)",marginBottom:3}}>{f.l}</div>
                        <input type={f.t||"text"} style={inp} placeholder={f.ph} value={newP[f.k]} onChange={e=>setNewP(p=>({...p,[f.k]:e.target.value}))}/>
                      </div>
                    ))}
                  </div>
                  <div style={{display:"flex",alignItems:"center",gap:8,marginBottom:12}}>
                    <div style={{fontSize:10,color:"rgba(255,255,255,0.4)"}}>Danh mục:</div>
                    <select style={{...inp,width:140}} value={newP.category} onChange={e=>setNewP(p=>({...p,category:e.target.value}))}>
                      {["iPhone","Samsung","OPPO","Xiaomi","Vivo","Realme","Phụ kiện","Tai nghe","Khác"].map(c=><option key={c} value={c}>{c}</option>)}
                    </select>
                    <div style={{fontSize:10,color:"rgba(255,255,255,0.4)"}}>Icon:</div>
                    <select style={{...inp,width:90}} value={newP.image} onChange={e=>setNewP(p=>({...p,image:e.target.value}))}>
                      {["📱","🎧","🔌","🛡️","⌚","💻"].map(ic=><option key={ic} value={ic}>{ic}</option>)}
                    </select>
                  </div>
                  <div style={{display:"flex",gap:8}}>
                    <button onClick={addProduct} style={btnP}>✅ Lưu sản phẩm</button>
                    <button onClick={()=>setShowAddP(false)} style={btnS}>Hủy</button>
                  </div>
                </div>
              )}
              <div style={{...card,overflow:"auto"}}>
                <table style={{width:"100%",borderCollapse:"collapse"}}>
                  <thead>
                    <tr>
                      <th style={th}>Sản phẩm</th>
                      <th style={{...th}} className="desktop-only">SKU</th>
                      <th style={th}>Giá bán</th>
                      <th style={th}>Biên lợi</th>
                      <th style={th}>Tồn kho</th>
                      <th style={th}>Đã bán</th>
                    </tr>
                  </thead>
                  <tbody>
                    {filteredP.map(p=>{
                      const mg=Math.round(((p.price-p.costPrice)/p.price)*100);
                      const sc=p.stock===0?"#FF3B30":p.stock<=p.minStock?"#FF9500":"#34C759";
                      return(
                        <tr key={p.id}>
                          <td style={td}>
                            <div style={{display:"flex",alignItems:"center",gap:8}}>
                              <span style={{fontSize:20}}>{p.image}</span>
                              <div>
                                <div style={{fontWeight:600,color:"#fff",fontSize:12}}>{p.name}</div>
                                <div style={{fontSize:10,color:"rgba(255,255,255,0.4)"}}>{p.color}</div>
                              </div>
                            </div>
                          </td>
                          <td style={{...td,fontFamily:"monospace",fontSize:10,color:"rgba(255,255,255,0.4)"}} className="desktop-only">{p.sku}</td>
                          <td style={{...td,fontWeight:700,color:"#fff"}}>{fmtS(p.price)}đ</td>
                          <td style={td}><span style={tag(mg>=25?"#34C759":mg>=15?"#FF9500":"#FF3B30")}>{mg}%</span></td>
                          <td style={td}><span style={{fontWeight:800,color:sc,fontSize:15}}>{p.stock}</span>{p.stock<=p.minStock&&<span style={{marginLeft:4}}>{p.stock===0?"🔴":"🟡"}</span>}</td>
                          <td style={{...td,fontWeight:600,color:"#FF6B2B"}}>{p.sold}</td>
                        </tr>
                      );
                    })}
                  </tbody>
                </table>
              </div>
            </div>
          )}

          {tab==="orders"&&(
            <div style={card}>
              <div style={{display:"flex",justifyContent:"space-between",alignItems:"center",marginBottom:14,flexWrap:"wrap",gap:8}}>
                <div style={{fontSize:14,fontWeight:700,color:"#fff"}}>Danh sách hóa đơn ({orders.length})</div>
              </div>
              <div style={{overflow:"auto"}}>
                <table style={{width:"100%",borderCollapse:"collapse"}}>
                  <thead><tr>
                    <th style={th}>Mã HĐ</th>
                    <th style={{...th}} className="desktop-only">Thời gian</th>
                    <th style={th}>Khách</th>
                    <th style={{...th}} className="desktop-only">TT</th>
                    <th style={th}>Tổng tiền</th>
                    <th style={th}>Trạng thái</th>
                  </tr></thead>
                  <tbody>
                    {orders.map(o=>(
                      <tr key={o.id}>
                        <td style={{...td,fontWeight:800,color:"#FF6B2B",fontFamily:"monospace"}}>{o.id}</td>
                        <td style={{...td,fontSize:10,color:"rgba(255,255,255,0.4)"}} className="desktop-only">{o.date}</td>
                        <td style={td}>{o.customer}</td>
                        <td style={td} className="desktop-only"><span style={{fontSize:12}}>{o.payment==="Tiền mặt"?"💵":o.payment==="Thẻ"?"💳":o.payment==="QR"?"📲":"🏦"}</span> {o.payment}</td>
                        <td style={{...td,fontWeight:800,color:"#fff"}}>{fmtS(o.total)}đ</td>
                        <td style={td}><span style={tag(o.status==="Hoàn thành"?"#34C759":"#FF9500")}>{o.status}</span></td>
                      </tr>
                    ))}
                  </tbody>
                </table>
              </div>
            </div>
          )}

          {tab==="customers"&&(
            <div>
              <div style={{display:"grid",gridTemplateColumns:"repeat(auto-fit,minmax(130px,1fr))",gap:10,marginBottom:14}}>
                {[{l:"Tổng KH",v:customers.length,i:"👥"},{l:"VIP",v:customers.filter(c=>c.group==="VIP").length,i:"⭐"},{l:"Đại lý",v:customers.filter(c=>c.group==="Đại lý").length,i:"🏪"},{l:"DT từ KH",v:fmtS(customers.reduce((s,c)=>s+c.totalSpent,0))+"đ",i:"💰"}].map((s,i)=>(
                  <div key={i} style={{...card,textAlign:"center"}}>
                    <div style={{fontSize:22,marginBottom:4}}>{s.i}</div>
                    <div style={{fontSize:18,fontWeight:800,color:"#FF6B2B"}}>{s.v}</div>
                    <div style={{fontSize:10,color:"rgba(255,255,255,0.4)"}}>{s.l}</div>
                  </div>
                ))}
              </div>
              <div style={card}>
                <div style={{display:"flex",justifyContent:"space-between",alignItems:"center",marginBottom:12,flexWrap:"wrap",gap:8}}>
                  <div style={{fontSize:14,fontWeight:700,color:"#fff"}}>Danh sách khách hàng</div>
                  <input style={{...inp,width:200}} placeholder="🔍 Tìm khách…" value={searchC} onChange={e=>setSearchC(e.target.value)}/>
                </div>
                <div style={{overflow:"auto"}}>
                  <table style={{width:"100%",borderCollapse:"collapse"}}>
                    <thead><tr>
                      <th style={th}>Khách hàng</th>
                      <th style={{...th}} className="desktop-only">SĐT</th>
                      <th style={th}>Nhóm</th>
                      <th style={th}>Điểm</th>
                      <th style={th}>Chi tiêu</th>
                      <th style={{...th}} className="desktop-only">Đơn</th>
                    </tr></thead>
                    <tbody>
                      {customers.filter(c=>c.name.toLowerCase().includes(searchC.toLowerCase())||c.phone.includes(searchC)).map(c=>{
                        const gc={"VIP":"#FFD700","Thân thiết":"#007AFF","Đại lý":"#AF52DE","Khách lẻ":"rgba(255,255,255,0.4)"};
                        return(
                          <tr key={c.id}>
                            <td style={td}>
                              <div style={{display:"flex",alignItems:"center",gap:8}}>
                                <div style={{width:30,height:30,borderRadius:"50%",background:"linear-gradient(135deg,#FF6B2B44,#007AFF44)",display:"flex",alignItems:"center",justifyContent:"center",fontWeight:700,fontSize:12,color:"#fff",flexShrink:0}}>{c.name[0]}</div>
                                <div>
                                  <div style={{fontWeight:600,color:"#fff",fontSize:12}}>{c.name}</div>
                                  <div style={{fontSize:10,color:"rgba(255,255,255,0.4)"}} className="desktop-only">{c.email}</div>
                                </div>
                              </div>
                            </td>
                            <td style={{...td,fontFamily:"monospace",fontSize:11}} className="desktop-only">{c.phone}</td>
                            <td style={td}><span style={tag(gc[c.group]||"#888")}>{c.group}</span></td>
                            <td style={{...td,fontWeight:700,color:"#FFD700"}}>⭐ {c.points.toLocaleString()}</td>
                            <td style={{...td,fontWeight:700,color:"#FF6B2B"}}>{fmtS(c.totalSpent)}đ</td>
                            <td style={{...td,textAlign:"center"}} className="desktop-only">{c.orders}</td>
                          </tr>
                        );
                      })}
                    </tbody>
                  </table>
                </div>
              </div>
            </div>
          )}

          {tab==="staff"&&(
            <div>
              <div style={{display:"grid",gridTemplateColumns:"repeat(auto-fit,minmax(140px,1fr))",gap:10,marginBottom:14}}>
                {[{l:"Tổng NV",v:STAFF.length,i:"👔"},{l:"Quỹ lương",v:fmtS(STAFF.reduce((s,e)=>s+e.salary,0))+"đ",i:"💵"},{l:"DS trung bình",v:fmtS(STAFF.filter(e=>e.sales>0).reduce((s,e)=>s+e.sales,0)/STAFF.filter(e=>e.sales>0).length)+"đ",i:"📊"},{l:"Hoa hồng ước tính",v:fmtS(STAFF.reduce((s,e)=>s+(e.sales*e.commission/100),0))+"đ",i:"🎯"}].map((s,i)=>(
                  <div key={i} style={card}>
                    <div style={{fontSize:22,marginBottom:5}}>{s.i}</div>
                    <div style={{fontSize:18,fontWeight:800,color:"#FF6B2B"}}>{s.v}</div>
                    <div style={{fontSize:10,color:"rgba(255,255,255,0.4)",marginTop:2}}>{s.l}</div>
                  </div>
                ))}
              </div>
              <div style={card}>
                <div style={{fontSize:14,fontWeight:700,color:"#fff",marginBottom:12}}>Bảng lương tháng 6/2025</div>
                <div style={{overflow:"auto"}}>
                  <table style={{width:"100%",borderCollapse:"collapse"}}>
                    <thead><tr>
                      <th style={th}>Nhân viên</th>
                      <th style={th}>Chức vụ</th>
                      <th style={{...th}} className="desktop-only">Ngày công</th>
                      <th style={{...th}} className="desktop-only">Doanh số</th>
                      <th style={th}>Lương CB</th>
                      <th style={th}>Hoa hồng</th>
                      <th style={th}>Tổng</th>
                    </tr></thead>
                    <tbody>
                      {STAFF.map((e,i)=>{
                        const comm=Math.round(e.sales*e.commission/100);
                        return(
                          <tr key={i}>
                            <td style={td}>
                              <div style={{display:"flex",alignItems:"center",gap:8}}>
                                <div style={{width:30,height:30,borderRadius:"50%",background:"linear-gradient(135deg,#007AFF44,#AF52DE44)",display:"flex",alignItems:"center",justifyContent:"center",fontWeight:700,fontSize:12,color:"#fff",flexShrink:0}}>{e.name[0]}</div>
                                <span style={{fontWeight:600,color:"#fff",fontSize:12}}>{e.name}</span>
                              </div>
                            </td>
                            <td style={td}><span style={tag(e.role==="Quản lý"?"#FF6B2B":e.role==="Kế toán"?"#AF52DE":"#007AFF")}>{e.role}</span></td>
                            <td style={{...td,textAlign:"center"}} className="desktop-only">{e.workDays}/22</td>
                            <td style={{...td,color:"#FF6B2B",fontWeight:600}} className="desktop-only">{e.sales>0?fmtS(e.sales)+"đ":"—"}</td>
                            <td style={td}>{fmtS(e.salary)}đ</td>
                            <td style={{...td,color:"#34C759"}}>{comm>0?"+"+fmtS(comm)+"đ":"—"}</td>
                            <td style={{...td,fontWeight:800,color:"#fff"}}>{fmtS(e.salary+comm)}đ</td>
                          </tr>
                        );
                      })}
                    </tbody>
                  </table>
                </div>
              </div>
            </div>
          )}

          {tab==="reports"&&(
            <div>
              <div style={{display:"grid",gridTemplateColumns:"repeat(auto-fit,minmax(160px,1fr))",gap:10,marginBottom:14}}>
                {RD.slice(-3).map((d,i)=>(
                  <div key={i} style={{background:"linear-gradient(135deg,#FF6B2B22,#FF6B2B08)",border:"1px solid #FF6B2B30",borderRadius:14,padding:16}}>
                    <div style={{fontSize:10,color:"rgba(255,255,255,0.4)",textTransform:"uppercase",letterSpacing:0.7}}>Tháng {4+i}/2025</div>
                    <div style={{fontSize:20,fontWeight:800,color:"#FF6B2B",marginTop:4}}>{fmtS(d.revenue)}đ</div>
                    <div style={{fontSize:10,color:"rgba(255,255,255,0.4)",marginTop:3}}>LN: {fmtS(d.revenue-d.cost)}đ ({Math.round(((d.revenue-d.cost)/d.revenue)*100)}%)</div>
                  </div>
                )).concat([
                  <div key="total" style={{background:"linear-gradient(135deg,#34C75922,#34C75908)",border:"1px solid #34C75930",borderRadius:14,padding:16}}>
                    <div style={{fontSize:10,color:"rgba(255,255,255,0.4)",textTransform:"uppercase",letterSpacing:0.7}}>Tổng 6 tháng</div>
                    <div style={{fontSize:20,fontWeight:800,color:"#34C759",marginTop:4}}>{fmtS(RD.reduce((s,d)=>s+d.revenue,0))}đ</div>
                    <div style={{fontSize:10,color:"rgba(255,255,255,0.4)",marginTop:3}}>LN ròng: {fmtS(totalProfit)}đ</div>
                  </div>
                ])}
              </div>
              <div style={{display:"grid",gridTemplateColumns:"minmax(0,1fr) minmax(0,1fr)",gap:12,marginBottom:12}}>
                <div style={card}>
                  <div style={{fontSize:14,fontWeight:700,color:"#fff",marginBottom:12}}>Doanh thu & Lợi nhuận</div>
                  <div style={{overflow:"auto"}}>
                    <table style={{width:"100%",borderCollapse:"collapse"}}>
                      <thead><tr>
                        <th style={th}>Tháng</th><th style={th}>Doanh thu</th><th style={{...th}} className="desktop-only">Chi phí</th><th style={th}>Lợi nhuận</th><th style={th}>Biên</th>
                      </tr></thead>
                      <tbody>
                        {RD.map((d,i)=>{
                          const pr=d.revenue-d.cost;
                          const mg=Math.round((pr/d.revenue)*100);
                          return(
                            <tr key={i}>
                              <td style={{...td,fontWeight:700}}>T{i+1}</td>
                              <td style={{...td,color:"#FF6B2B",fontWeight:700}}>{fmtS(d.revenue)}đ</td>
                              <td style={{...td,color:"rgba(255,255,255,0.5)"}} className="desktop-only">{fmtS(d.cost)}đ</td>
                              <td style={{...td,color:"#34C759",fontWeight:700}}>{fmtS(pr)}đ</td>
                              <td style={td}><span style={tag(mg>=25?"#34C759":"#FF9500")}>{mg}%</span></td>
                            </tr>
                          );
                        })}
                      </tbody>
                    </table>
                  </div>
                </div>
                <div style={card}>
                  <div style={{fontSize:14,fontWeight:700,color:"#fff",marginBottom:12}}>Top 8 sản phẩm</div>
                  {[...products].sort((a,b)=>b.sold-a.sold).slice(0,8).map((p,i)=>(
                    <div key={p.id} style={{display:"flex",alignItems:"center",gap:8,padding:"7px 0",borderBottom:"1px solid rgba(255,255,255,0.04)"}}>
                      <div style={{width:20,height:20,borderRadius:5,background:i<3?"rgba(255,107,43,0.2)":"rgba(255,255,255,0.06)",display:"flex",alignItems:"center",justifyContent:"center",fontSize:10,fontWeight:700,color:i<3?"#FF6B2B":"rgba(255,255,255,0.3)",flexShrink:0}}>#{i+1}</div>
                      <span style={{fontSize:14}}>{p.image}</span>
                      <div style={{flex:1,minWidth:0}}>
                        <div style={{fontSize:11,fontWeight:600,color:"#fff",overflow:"hidden",textOverflow:"ellipsis",whiteSpace:"nowrap"}}>{p.name}</div>
                        <div style={{fontSize:9,color:"rgba(255,255,255,0.35)"}}>{p.sold} đã bán</div>
                      </div>
                      <div style={{fontSize:11,fontWeight:700,color:"#FF6B2B",flexShrink:0}}>{fmtS(p.sold*p.price)}đ</div>
                    </div>
                  ))}
                </div>
              </div>
            </div>
          )}
        </div>

        <div className="mobile-only" style={{background:C2,borderTop:"1px solid rgba(255,255,255,0.07)",display:"flex",justifyContent:"space-around",padding:"8px 4px",flexShrink:0}}>
          {navItems.map(item=>(
            <button key={item.id} onClick={()=>setTab(item.id)} style={{display:"flex",flexDirection:"column",alignItems:"center",gap:2,background:"none",border:"none",cursor:"pointer",padding:"4px 8px",borderRadius:8,color:tab===item.id?"#FF6B2B":"rgba(255,255,255,0.4)",minWidth:0,flex:1}}>
              <span style={{fontSize:20}}>{item.icon}</span>
              <span style={{fontSize:9,fontWeight:tab===item.id?700:400,whiteSpace:"nowrap"}}>{item.label}</span>
            </button>
          ))}
        </div>
      </main>

      {showChat&&<Chatbot products={products} customers={customers} orders={orders} onClose={()=>setShowChat(false)}/>}
      {!showChat&&(
        <button onClick={()=>setShowChat(true)} style={{position:"fixed",bottom:20,right:20,width:56,height:56,borderRadius:"50%",background:"linear-gradient(135deg,#FF6B2B,#FF4500)",border:"none",cursor:"pointer",boxShadow:"0 6px 24px rgba(255,107,43,0.5)",display:"flex",alignItems:"center",justifyContent:"center",fontSize:24,zIndex:500,transition:"all 0.2s"}}>
          🤖
        </button>
      )}

      {notif&&(
        <div style={{position:"fixed",top:14,left:"50%",transform:"translateX(-50%)",zIndex:9998,background:notif.type==="error"?"#FF3B30":"#34C759",color:"#fff",padding:"11px 20px",borderRadius:12,fontSize:13,fontWeight:600,boxShadow:"0 8px 24px rgba(0,0,0,0.3)",animation:"fadeUp 0.2s ease",whiteSpace:"nowrap",maxWidth:"90vw"}}>
          {notif.type==="error"?"⚠️":"✅"} {notif.msg}
        </div>
      )}
    </div>
  );
}
</body>
</html>
  { id:6,sku:"IPH15-128-BLU",name:"iPhone 15 128GB",category:"iPhone",brand:"Apple",color:"Xanh Lam",price:22490000,costPrice:17800000,stock:20,minStock:5,sold:62,image:"📱" },
  { id:7,sku:"SAM-A55-256-LVD",name:"Samsung A55 256GB",category:"Samsung",brand:"Samsung",color:"Xanh Tím Oải Hương",price:9990000,costPrice:7200000,stock:25,minStock:5,sold:88,image:"📱" },
  { id:8,sku:"ACC-CASE-MG-BLK",name:"Ốp lưng MagSafe iPhone 15",category:"Phụ kiện",brand:"Apple",color:"Đen",price:890000,costPrice:300000,stock:45,minStock:10,sold:120,image:"🛡️" },
  { id:9,sku:"ACC-CHG-65W-WHT",name:"Sạc nhanh 65W GaN",category:"Phụ kiện",brand:"Anker",color:"Trắng",price:690000,costPrice:220000,stock:38,minStock:10,sold:95,image:"🔌" },
  { id:10,sku:"ACC-EAR-AIRP3-WHT",name:"AirPods 3 (Lightning)",category:"Tai nghe",brand:"Apple",color:"Trắng",price:4490000,costPrice:3200000,stock:18,minStock:5,sold:41,image:"🎧" },
];
const IC = [
  { id:1,name:"Nguyễn Văn An",phone:"0912345678",email:"an.nguyen@gmail.com",group:"VIP",points:2450,totalSpent:68900000,orders:5,lastVisit:"2025-06-20" },
  { id:2,name:"Trần Thị Bình",phone:"0987654321",email:"binh.tran@gmail.com",group:"Thân thiết",points:890,totalSpent:23400000,orders:3,lastVisit:"2025-06-18" },
  { id:3,name:"Lê Minh Cường",phone:"0909123456",email:"cuong.le@gmail.com",group:"Đại lý",points:5200,totalSpent:145000000,orders:18,lastVisit:"2025-06-22" },
  { id:4,name:"Phạm Thị Dung",phone:"0978234567",email:"dung.pham@gmail.com",group:"Khách lẻ",points:120,totalSpent:3200000,orders:1,lastVisit:"2025-06-15" },
  { id:5,name:"Hoàng Văn Em",phone:"0965432109",email:"em.hoang@gmail.com",group:"Thân thiết",points:1340,totalSpent:34500000,orders:4,lastVisit:"2025-06-21" },
];
const IO = [
  { id:"HD001",customer:"Nguyễn Văn An",items:[{name:"iPhone 15 Pro Max 256GB",qty:1,price:34990000}],total:34990000,payment:"Thẻ",status:"Hoàn thành",date:"2025-06-22 09:15" },
  { id:"HD002",customer:"Trần Thị Bình",items:[{name:"Samsung A55 256GB",qty:1,price:9990000},{name:"Ốp lưng MagSafe",qty:1,price:890000}],total:10880000,payment:"QR",status:"Hoàn thành",date:"2025-06-22 10:32" },
  { id:"HD003",customer:"Khách lẻ",items:[{name:"Sạc nhanh 65W GaN",qty:2,price:1380000}],total:1380000,payment:"Tiền mặt",status:"Hoàn thành",date:"2025-06-22 11:05" },
  { id:"HD004",customer:"Lê Minh Cường",items:[{name:"Samsung S24 Ultra 512GB",qty:2,price:63980000}],total:63980000,payment:"Chuyển khoản",status:"Hoàn thành",date:"2025-06-22 13:20" },
  { id:"HD005",customer:"Hoàng Văn Em",items:[{name:"AirPods 3",qty:1,price:4490000}],total:4490000,payment:"QR",status:"Đang xử lý",date:"2025-06-22 14:48" },
];
const STAFF=[
  {id:1,name:"Nguyễn Thị Mai",role:"Quản lý",sales:145000000,commission:2.5,workDays:22,salary:12000000},
  {id:2,name:"Trần Văn Hùng",role:"Nhân viên bán hàng",sales:89000000,commission:1.5,workDays:22,salary:8000000},
  {id:3,name:"Phạm Thị Lan",role:"Nhân viên bán hàng",sales:67000000,commission:1.5,workDays:20,salary:8000000},
  {id:4,name:"Lê Văn Đức",role:"Kế toán",sales:0,commission:0,workDays:22,salary:10000000},
];
const RD=[
  {month:"T1",revenue:245000000,cost:185000000},{month:"T2",revenue:198000000,cost:148000000},
  {month:"T3",revenue:312000000,cost:235000000},{month:"T4",revenue:287000000,cost:215000000},
  {month:"T5",revenue:356000000,cost:268000000},{month:"T6",revenue:298000000,cost:223000000},
];

// ─── HELPERS ─────────────────────────────────────────────────────────────────
const fmt=(n)=>new Intl.NumberFormat("vi-VN").format(n)+"đ";
const fmtS=(n)=>{if(n>=1e9)return(n/1e9).toFixed(1)+"tỷ";if(n>=1e6)return(n/1e6).toFixed(0)+"tr";return new Intl.NumberFormat("vi-VN").format(n);};

// ─── MINI BAR CHART ──────────────────────────────────────────────────────────
function MiniBar({data}){
  const max=Math.max(...data.map(d=>d.revenue));
  return(
    <div style={{display:"flex",alignItems:"flex-end",gap:6,height:80,padding:"0 4px"}}>
      {data.map((d,i)=>{
        const h=Math.max(8,(d.revenue/max)*70);
        const ph=Math.max(4,(d.cost/max)*70);
        return(
          <div key={i} style={{flex:1,display:"flex",flexDirection:"column",alignItems:"center",gap:2}}>
            <div style={{display:"flex",alignItems:"flex-end",gap:1,height:72}}>
              <div style={{width:8,height:h,background:"linear-gradient(180deg,#FF6B2B,#FF9A6B)",borderRadius:"3px 3px 0 0"}}/>
              <div style={{width:8,height:ph,background:"rgba(255,255,255,0.2)",borderRadius:"3px 3px 0 0"}}/>
            </div>
            <span style={{fontSize:9,color:"rgba(255,255,255,0.5)"}}>{d.month}</span>
          </div>
        );
      })}
    </div>
  );
}

// ─── CHATBOT ─────────────────────────────────────────────────────────────────
function Chatbot({products,customers,orders,onClose}){
  const [msgs,setMsgs]=useState([{role:"bot",text:"Xin chào! Tôi là trợ lý AI của PhoneShop Pro 📱\nBạn cần hỗ trợ gì? Tôi có thể tư vấn về tồn kho, doanh thu, khách hàng..."}]);
  const [input,setInput]=useState("");
  const [loading,setLoading]=useState(false);
  const endRef=useRef(null);
  useEffect(()=>{endRef.current?.scrollIntoView({behavior:"smooth"});},[msgs]);

  const send=async()=>{
    const q=input.trim();
    if(!q||loading)return;
    setInput("");
    setMsgs(p=>[...p,{role:"user",text:q}]);
    setLoading(true);

    const lowStock=products.filter(p=>p.stock<=p.minStock);
    const topProducts=[...products].sort((a,b)=>b.sold-a.sold).slice(0,5);
    const totalRevenue=orders.filter(o=>o.status==="Hoàn thành").reduce((s,o)=>s+o.total,0);
    const context=`Bạn là trợ lý AI cho cửa hàng điện thoại PhoneShop Pro. Trả lời ngắn gọn bằng tiếng Việt.
DỮ LIỆU THỰC TẾ:
- Tổng sản phẩm: ${products.length} loại, tổng tồn kho: ${products.reduce((s,p)=>s+p.stock,0)} máy
- Hàng sắp hết: ${lowStock.length} mặt hàng
- Top 5 bán chạy: ${topProducts.map((p,i)=>(i+1)+". "+p.name).join(", ")}
- Tổng khách hàng: ${customers.length}
- Tổng đơn hàng: ${orders.length}, doanh thu: ${fmt(totalRevenue)}`;

    try{
      const res=await fetch("https://api.anthropic.com/v1/messages",{
        method:"POST",
        headers:{"Content-Type":"application/json"},
        body:JSON.stringify({
          model:"claude-3-haiku-20240307",
          max_tokens:500,
          system:context,
          messages:[...msgs.filter(m=>m.role!=="bot"||msgs.indexOf(m)>0).map(m=>({role:m.role==="bot"?"assistant":"user",content:m.text})),{role:"user",content:q}]
        })
      });
      const data=await res.json();
      const reply=data.content?.map(c=>c.text||"").join("")||"Xin lỗi, tính năng AI hiện cần cài đặt API Key để hoạt động.";
      setMsgs(p=>[...p,{role:"bot",text:reply}]);
    }catch{
      setMsgs(p=>[...p,{role:"bot",text:"⚠️ AI đang chạy ở chế độ offline. Vui lòng thêm cấu hình API để trò chuyện."}]);
    }
    setLoading(false);
  };

  const quickQ=["Hàng nào sắp hết?","Sản phẩm bán chạy nhất?","Doanh thu hôm nay?"];

  return(
    <div style={{position:"fixed",bottom:80,right:20,width:340,height:480,background:"#0F2547",border:"1px solid rgba(255,107,43,0.3)",borderRadius:20,display:"flex",flexDirection:"column",zIndex:1000,boxShadow:"0 20px 60px rgba(0,0,0,0.5)",overflow:"hidden"}}>
      <div style={{background:"linear-gradient(135deg,#FF6B2B,#FF4500)",padding:"14px 16px",display:"flex",alignItems:"center",justifyContent:"space-between",flexShrink:0}}>
        <div style={{display:"flex",alignItems:"center",gap:10}}>
          <div style={{width:36,height:36,background:"rgba(255,255,255,0.2)",borderRadius:10,display:"flex",alignItems:"center",justifyContent:"center",fontSize:18}}>🤖</div>
          <div>
            <div style={{fontWeight:700,color:"#fff",fontSize:14}}>AI Trợ lý PhoneShop</div>
          </div>
        </div>
        <button onClick={onClose} style={{background:"rgba(255,255,255,0.2)",border:"none",borderRadius:8,padding:"6px 10px",color:"#fff",cursor:"pointer",fontSize:16}}>✕</button>
      </div>
      <div style={{flex:1,overflow:"auto",padding:14,display:"flex",flexDirection:"column",gap:10}}>
        {msgs.map((m,i)=>(
          <div key={i} style={{display:"flex",justifyContent:m.role==="user"?"flex-end":"flex-start"}}>
            <div style={{maxWidth:"85%",padding:"10px 13px",borderRadius:m.role==="user"?"14px 14px 4px 14px":"14px 14px 14px 4px",background:m.role==="user"?"linear-gradient(135deg,#FF6B2B,#FF4500)":"rgba(255,255,255,0.08)",color:"#fff",fontSize:13,lineHeight:1.5,whiteSpace:"pre-wrap",wordBreak:"break-word"}}>{m.text}</div>
          </div>
        ))}
        {loading&&<div style={{color:"#FF6B2B",fontSize:12,paddingLeft:10}}>Đang trả lời...</div>}
        <div ref={endRef}/>
      </div>
      <div style={{padding:"0 12px 8px",display:"flex",gap:6,flexWrap:"wrap",flexShrink:0}}>
        {quickQ.map((q,i)=>(<button key={i} onClick={()=>{setInput(q);}} style={{padding:"4px 9px",borderRadius:20,border:"1px solid rgba(255,107,43,0.3)",background:"rgba(255,107,43,0.08)",color:"rgba(255,255,255,0.7)",cursor:"pointer",fontSize:11,whiteSpace:"nowrap"}}>{q}</button>))}
      </div>
      <div style={{padding:"8px 12px 14px",display:"flex",gap:8,flexShrink:0,borderTop:"1px solid rgba(255,255,255,0.06)"}}>
        <input value={input} onChange={e=>setInput(e.target.value)} onKeyDown={e=>e.key==="Enter"&&send()} placeholder="Nhập câu hỏi..." style={{flex:1,background:"rgba(255,255,255,0.07)",border:"1px solid rgba(255,255,255,0.1)",borderRadius:10,padding:"9px 13px",color:"#fff",fontSize:13,outline:"none"}}/>
        <button onClick={send} disabled={loading||!input.trim()} style={{width:40,height:40,borderRadius:10,background:loading||!input.trim()?"rgba(255,255,255,0.08)":"linear-gradient(135deg,#FF6B2B,#FF4500)",border:"none",color:"#fff",cursor:loading||!input.trim()?"not-allowed":"pointer",display:"flex",alignItems:"center",justifyContent:"center"}}>➤</button>
      </div>
    </div>
  );
}

// ─── LOGIN SCREEN ────────────────────────────────────────────────────────────
function LoginScreen({ onLogin }) {
  const [user, setUser] = useState("");
  const [pass, setPass] = useState("");
  const [error, setError] = useState("");

  const handleSubmit = (e) => {
    e.preventDefault();
    if (!user || !pass) {
      setError("Vui lòng nhập tài khoản và mật khẩu!");
      return;
    }
    // Mật khẩu demo đơn giản
    if (pass !== "123456") {
      setError("Sai mật khẩu! (Gợi ý: 123456)");
      return;
    }
    onLogin(user);
  };

  return (
    <div style={{ height: "100vh", display: "flex", alignItems: "center", justifyContent: "center", background: "#0B1629", color: "#fff", fontFamily: "'Inter', sans-serif", padding: 16 }}>
      <div style={{ background: "#132040", padding: "34px 28px", borderRadius: 20, width: "100%", maxWidth: 360, border: "1px solid rgba(255,255,255,0.1)", boxShadow: "0 20px 60px rgba(0,0,0,0.5)" }}>
        <div style={{ textAlign: "center", marginBottom: 30 }}>
          <div style={{ width: 60, height: 60, background: "linear-gradient(135deg,#FF6B2B,#FF9A6B)", borderRadius: 16, display: "flex", alignItems: "center", justifyContent: "center", fontSize: 30, margin: "0 auto 16px", boxShadow: "0 8px 24px rgba(255,107,43,0.3)" }}>📱</div>
          <h1 style={{ fontSize: 24, fontWeight: 800, margin: 0, color: "#fff" }}>PhoneShop Pro</h1>
          <p style={{ fontSize: 13, color: "rgba(255,255,255,0.4)", marginTop: 6 }}>Đăng nhập hệ thống quản lý</p>
        </div>
        
        <form onSubmit={handleSubmit} style={{ display: "flex", flexDirection: "column", gap: 16 }}>
          {error && <div style={{ background: "rgba(255,59,48,0.1)", color: "#FF3B30", padding: "10px", borderRadius: 8, fontSize: 13, textAlign: "center", border: "1px solid rgba(255,59,48,0.2)" }}>{error}</div>}
          
          <div>
            <label style={{ display: "block", fontSize: 12, color: "rgba(255,255,255,0.6)", marginBottom: 6 }}>Tên đăng nhập</label>
            <input value={user} onChange={e=>setUser(e.target.value)} placeholder="VD: admin" style={{ width: "100%", background: "rgba(255,255,255,0.05)", border: "1px solid rgba(255,255,255,0.1)", padding: "12px 16px", borderRadius: 10, color: "#fff", outline: "none", fontSize: 14, boxSizing: "border-box", transition: "all 0.2s" }} autoFocus />
          </div>
          
          <div>
            <label style={{ display: "block", fontSize: 12, color: "rgba(255,255,255,0.6)", marginBottom: 6 }}>Mật khẩu</label>
            <input type="password" value={pass} onChange={e=>setPass(e.target.value)} placeholder="••••••" style={{ width: "100%", background: "rgba(255,255,255,0.05)", border: "1px solid rgba(255,255,255,0.1)", padding: "12px 16px", borderRadius: 10, color: "#fff", outline: "none", fontSize: 14, boxSizing: "border-box", transition: "all 0.2s" }} />
            <div style={{ fontSize: 11, color: "rgba(255,255,255,0.3)", marginTop: 6, textAlign: "right" }}>*Mật khẩu demo: 123456</div>
          </div>
          
          <button type="submit" style={{ background: "linear-gradient(135deg,#FF6B2B,#FF4500)", color: "#fff", border: "none", padding: "14px", borderRadius: 10, fontSize: 15, fontWeight: 700, cursor: "pointer", marginTop: 10, boxShadow: "0 4px 15px rgba(255,107,43,0.3)", transition: "opacity 0.2s" }}>
            Đăng nhập ngay
          </button>
        </form>
      </div>
    </div>
  );
}

// ─── MAIN APP ────────────────────────────────────────────────────────────────
export default function App(){
  const [tab,setTab]=useState("dashboard");
  const [products,setProducts]=useState(IP);
  const [customers,setCustomers]=useState(IC);
  const [orders,setOrders]=useState(IO);
  const [cart,setCart]=useState([]);
  const [searchP,setSearchP]=useState("");
  const [searchC,setSearchC]=useState("");
  const [selCust,setSelCust]=useState(null);
  const [payment,setPayment]=useState("Tiền mặt");
  const [discount,setDiscount]=useState(0);
  const [showReceipt,setShowReceipt]=useState(false);
  const [lastOrder,setLastOrder]=useState(null);
  const [catFilter,setCatFilter]=useState("Tất cả");
  const [showAddP,setShowAddP]=useState(false);
  const [newP,setNewP]=useState({sku:"",name:"",category:"iPhone",brand:"",color:"",price:"",costPrice:"",stock:"",minStock:3,image:"📱"});
  const [notif,setNotif]=useState(null);
  const [sidebar,setSidebar]=useState(true);
  const [showChat,setShowChat]=useState(false);
  const [mobileNav,setMobileNav]=useState(false);

  // Sync state
  const [user, setUser] = useState(null);
  const [syncMode, setSyncMode] = useState("local"); // "local" | "firebase"
  const [cacheLoaded,setCacheLoaded]=useState(false);
  const [syncStatus,setSyncStatus]=useState(null); // "saving"|"saved"|"error"
  const saveTimer=useRef(null);
  const fileInputRef = useRef(null);

  // ── AUTH STATE (MỚI) ──
  const [authUser, setAuthUser] = useState(() => {
    if (typeof window !== "undefined") {
      const saved = localStorage.getItem("phoneshop_auth_v1");
      return saved ? JSON.parse(saved) : null;
    }
    return null;
  });

  const handleLogin = (username) => {
    const userObj = { name: username || "Quản lý" };
    localStorage.setItem("phoneshop_auth_v1", JSON.stringify(userObj));
    setAuthUser(userObj);
  };

  const handleLogout = () => {
    localStorage.removeItem("phoneshop_auth_v1");
    setAuthUser(null);
  };

  const showNotif=(msg,type="success")=>{setNotif({msg,type});setTimeout(()=>setNotif(null),3000);};

  // ── INIT AUTH & DATA LOAD ──
  useEffect(() => {
    // Nếu chạy trên Github Pages (không có Firebase config), chỉ dùng LocalStorage
    if (!auth) {
      setSyncMode("local");
      const localData = localStorage.getItem("phoneshop_data_v3");
      if (localData) {
        try {
          const d = JSON.parse(localData);
          if(d.products) setProducts(d.products);
          if(d.customers) setCustomers(d.customers);
          if(d.orders) setOrders(d.orders);
        } catch(e) {}
      }
      setCacheLoaded(true);
      return;
    }

    // Nếu có Firebase (Cloud Sync)
    setSyncMode("firebase");
    const initAuth = async () => {
      try {
        if (typeof __initial_auth_token !== 'undefined' && __initial_auth_token) {
          await signInWithCustomToken(auth, __initial_auth_token);
        } else {
          await signInAnonymously(auth);
        }
      } catch(e) { console.error(e); }
    };
    initAuth();

    const unsubscribe = onAuthStateChanged(auth, (u) => {
      setUser(u);
    });
    return () => unsubscribe();
  }, []);

  // ── LOAD FIREBASE DATA ONCE LOGIN ──
  useEffect(() => {
    if (syncMode === "firebase" && user && db) {
      const loadCloudData = async () => {
        try {
          const docSnap = await getDoc(doc(db, 'artifacts', appId, 'users', user.uid, 'storeData', 'v1'));
          if (docSnap.exists()) {
            const d = docSnap.data();
            if(d.products) setProducts(d.products);
            if(d.customers) setCustomers(d.customers);
            if(d.orders) setOrders(d.orders);
            showNotif("Đã tải dữ liệu từ Đám mây!");
          }
        } catch (e) {
          console.warn("Could not load from cloud", e);
        }
        setCacheLoaded(true);
      };
      loadCloudData();
    }
  }, [user, syncMode]);

  // ── AUTO-SAVE TO LOCAL & FIREBASE ──
  useEffect(()=>{
    if(!cacheLoaded) return;
    const dataToSave = { products, customers, orders };
    
    // Luôn lưu local làm dự phòng
    localStorage.setItem("phoneshop_data_v3", JSON.stringify(dataToSave));

    // Lưu Firebase nếu có
    if (syncMode === "firebase" && user && db) {
      if(saveTimer.current) clearTimeout(saveTimer.current);
      setSyncStatus("saving");
      saveTimer.current = setTimeout(async()=>{
        try{
          await setDoc(doc(db, 'artifacts', appId, 'users', user.uid, 'storeData', 'v1'), dataToSave);
          setSyncStatus("saved");
          setTimeout(()=>setSyncStatus(null), 2000);
        }catch{
          setSyncStatus("error");
          setTimeout(()=>setSyncStatus(null), 3000);
        }
      }, 1500);
    } else {
      // Fake saving status for UI when LocalStorage
      if(saveTimer.current) clearTimeout(saveTimer.current);
      setSyncStatus("saving");
      saveTimer.current = setTimeout(()=>{
        setSyncStatus("saved");
        setTimeout(()=>setSyncStatus(null), 1000);
      }, 500);
    }
  },[products, customers, orders, cacheLoaded, user, syncMode]);

  // ── IMPORT / EXPORT TẬP TIN CHO GITHUB PAGES ──
  const handleExportBackup = () => {
    const data = { products, customers, orders, timestamp: new Date().toISOString() };
    const blob = new Blob([JSON.stringify(data, null, 2)], { type: "application/json" });
    const url = URL.createObjectURL(blob);
    const a = document.createElement("a");
    a.href = url;
    a.download = `PhoneShop_Backup_${new Date().toISOString().slice(0,10)}.json`;
    a.click();
    URL.revokeObjectURL(url);
    showNotif("Đã tải file dữ liệu Backup!");
  };

  const handleImportBackup = (e) => {
    const file = e.target.files[0];
    if (!file) return;
    const reader = new FileReader();
    reader.onload = (evt) => {
      try {
        const d = JSON.parse(evt.target.result);
        if(d.products) setProducts(d.products);
        if(d.customers) setCustomers(d.customers);
        if(d.orders) setOrders(d.orders);
        showNotif("Đã phục hồi dữ liệu thành công!");
      } catch (err) {
        showNotif("File dữ liệu không hợp lệ!", "error");
      }
    };
    reader.readAsText(file);
    if(fileInputRef.current) fileInputRef.current.value = '';
  };

  const resetCache=()=>{
    localStorage.removeItem("phoneshop_data_v3");
    setProducts(IP);setCustomers(IC);setOrders(IO);setCart([]);
    showNotif("Đã khôi phục dữ liệu gốc");
  };

  // ── CART LOGIC ──
  const addToCart=p=>{
    if(p.stock===0){showNotif("Hết hàng!","error");return;}
    setCart(prev=>{
      const ex=prev.find(i=>i.id===p.id);
      if(ex){if(ex.qty>=p.stock){showNotif("Không đủ tồn kho!","error");return prev;}return prev.map(i=>i.id===p.id?{...i,qty:i.qty+1}:i);}
      return[...prev,{...p,qty:1}];
    });
  };
  const removeFromCart=id=>setCart(p=>p.filter(i=>i.id!==id));
  const updateQty=(id,qty)=>{
    if(qty<=0){removeFromCart(id);return;}
    const pr=products.find(p=>p.id===id);
    if(qty>pr.stock){showNotif("Không đủ tồn kho!","error");return;}
    setCart(p=>p.map(i=>i.id===id?{...i,qty}:i));
  };
  const cartTotal=cart.reduce((s,i)=>s+i.price*i.qty,0);
  const discAmt=Math.round(cartTotal*(discount/100));
  const finalTotal=cartTotal-discAmt;

  const checkout=()=>{
    if(cart.length===0){showNotif("Giỏ hàng trống!","error");return;}
    const id="HD"+String(orders.length+1).padStart(3,"0");
    const o={id,customer:selCust?selCust.name:"Khách lẻ",items:cart.map(i=>({name:i.name,qty:i.qty,price:i.price*i.qty})),total:finalTotal,payment,status:"Hoàn thành",date:new Date().toLocaleString("vi-VN")};
    setOrders(p=>[o,...p]);
    setProducts(p=>p.map(pr=>{const ci=cart.find(c=>c.id===pr.id);return ci?{...pr,stock:pr.stock-ci.qty,sold:pr.sold+ci.qty}:pr;}));
    if(selCust){const pts=Math.floor(finalTotal/100000);setCustomers(p=>p.map(c=>c.id===selCust.id?{...c,points:c.points+pts,totalSpent:c.totalSpent+finalTotal,orders:c.orders+1}:c));}
    setLastOrder(o);setCart([]);setSelCust(null);setDiscount(0);setShowReceipt(true);
    showNotif(`Thanh toán thành công ${fmt(finalTotal)}!`);
  };

  const addProduct=()=>{
    if(!newP.sku||!newP.name||!newP.price){showNotif("Vui lòng điền đầy đủ!","error");return;}
    const p={...newP,id:products.length+1,price:+newP.price,costPrice:+newP.costPrice,stock:+newP.stock,minStock:+newP.minStock,sold:0};
    setProducts(prev=>[p,...prev]);
    setShowAddP(false);
    setNewP({sku:"",name:"",category:"iPhone",brand:"",color:"",price:"",costPrice:"",stock:"",minStock:3,image:"📱"});
    showNotif("Thêm sản phẩm thành công!");
  };

  const cats=["Tất cả",...new Set(products.map(p=>p.category))];
  const filteredP=products.filter(p=>{
    const mc=catFilter==="Tất cả"||p.category===catFilter;
    const ms=p.name.toLowerCase().includes(searchP.toLowerCase())||p.sku.toLowerCase().includes(searchP.toLowerCase());
    return mc&&ms;
  });
  const lowStock=products.filter(p=>p.stock<=p.minStock);
  const todayRev=orders.filter(o=>o.status==="Hoàn thành").reduce((s,o)=>s+o.total,0);
  const totalProfit=RD.reduce((s,d)=>s+(d.revenue-d.cost),0);

  const navItems=[
    {id:"dashboard",icon:"📊",label:"Tổng quan"},
    {id:"pos",icon:"🛒",label:"Bán hàng"},
    {id:"products",icon:"📦",label:"Hàng hóa"},
    {id:"orders",icon:"🧾",label:"Hóa đơn"},
    {id:"customers",icon:"👥",label:"Khách hàng"},
    {id:"staff",icon:"👔",label:"Nhân sự"},
    {id:"reports",icon:"📈",label:"Báo cáo"},
  ];

  // KHIẾN TOÀN BỘ APP BỊ KHOÁ NẾU CHƯA ĐĂNG NHẬP
  if (!authUser) {
    return <LoginScreen onLogin={handleLogin} />;
  }

  const syncCfg={
    saving:{bg:"rgba(255,149,0,0.12)",border:"rgba(255,149,0,0.3)",color:"#FF9500",label:syncMode==="firebase"?"☁️ Đang đồng bộ...":"💾 Đang lưu...",spin:true},
    saved:{bg:"rgba(52,199,89,0.12)",border:"rgba(52,199,89,0.3)",color:"#34C759",label:syncMode==="firebase"?"☁️ Đã đồng bộ":"💾 Đã lưu Offline"},
    error:{bg:"rgba(255,59,48,0.12)",border:"rgba(255,59,48,0.3)",color:"#FF3B30",label:"⚠️ Lỗi đồng bộ"},
  }[syncStatus]||null;

  // ── RECEIPT MODAL ──
  if(showReceipt&&lastOrder){
    return(
      <div style={{position:"fixed",inset:0,background:"rgba(0,0,0,0.85)",display:"flex",alignItems:"center",justifyContent:"center",zIndex:9999,padding:16}}>
        <style>{`@keyframes popIn{from{opacity:0;transform:scale(0.85)}to{opacity:1;transform:scale(1)}}`}</style>
        <div style={{background:"#132040",borderRadius:20,padding:28,width:"100%",maxWidth:380,border:"1px solid rgba(255,255,255,0.1)",boxShadow:"0 20px 60px rgba(0,0,0,0.5)",animation:"popIn 0.3s ease"}}>
          <div style={{textAlign:"center",marginBottom:20}}>
            <div style={{fontSize:52}}>✅</div>
            <div style={{fontSize:20,fontWeight:800,color:"#fff",marginTop:6}}>Thanh toán thành công!</div>
            <div style={{fontSize:13,color:"rgba(255,255,255,0.4)",marginTop:3}}>Hóa đơn #{lastOrder.id}</div>
          </div>
          <div style={{background:"rgba(255,255,255,0.04)",borderRadius:12,padding:14,marginBottom:16}}>
            {lastOrder.items.map((item,i)=>(
              <div key={i} style={{display:"flex",justifyContent:"space-between",marginBottom:6,fontSize:13}}>
                <span style={{color:"rgba(255,255,255,0.7)"}}>{item.name}</span>
                <span style={{color:"#fff",fontWeight:600}}>{fmt(item.price)}</span>
              </div>
            ))}
            <div style={{height:1,background:"rgba(255,255,255,0.08)",margin:"10px 0"}}/>
            <div style={{display:"flex",justifyContent:"space-between",fontWeight:800,fontSize:16}}>
              <span style={{color:"#fff"}}>Tổng cộng</span>
              <span style={{color:"#FF6B2B"}}>{fmt(lastOrder.total)}</span>
            </div>
            <div style={{marginTop:6,fontSize:11,color:"rgba(255,255,255,0.35)"}}>Thanh toán: {lastOrder.payment} • {lastOrder.date}</div>
          </div>
          <button onClick={()=>setShowReceipt(false)} style={{width:"100%",padding:13,background:"linear-gradient(135deg,#FF6B2B,#FF4500)",border:"none",borderRadius:12,color:"#fff",fontWeight:700,fontSize:15,cursor:"pointer"}}>
            Hóa đơn mới
          </button>
        </div>
      </div>
    );
  }

  // ── STYLES ──
  const C="#0B1629",C2="#0F2547",C3="#132040";
  const inp={background:"rgba(255,255,255,0.06)",border:"1px solid rgba(255,255,255,0.1)",borderRadius:10,padding:"9px 13px",color:"#fff",fontSize:13,outline:"none",width:"100%",boxSizing:"border-box"};
  const card={background:C3,border:"1px solid rgba(255,255,255,0.07)",borderRadius:14,padding:20};
  const tag=(cl)=>({padding:"2px 8px",borderRadius:6,fontSize:11,fontWeight:600,background:cl+"22",color:cl,whiteSpace:"nowrap"});
  const btnP={background:"linear-gradient(135deg,#FF6B2B,#FF4500)",color:"#fff",border:"none",borderRadius:10,padding:"10px 18px",cursor:"pointer",fontSize:13,fontWeight:700,boxShadow:"0 4px 14px rgba(255,107,43,0.35)",whiteSpace:"nowrap"};
  const btnS={background:"rgba(255,255,255,0.06)",color:"rgba(255,255,255,0.7)",border:"1px solid rgba(255,255,255,0.1)",borderRadius:9,padding:"9px 16px",cursor:"pointer",fontSize:13,fontWeight:500,whiteSpace:"nowrap"};
  const th={textAlign:"left",padding:"10px 14px",fontSize:11,color:"rgba(255,255,255,0.4)",fontWeight:600,textTransform:"uppercase",letterSpacing:0.6,borderBottom:"1px solid rgba(255,255,255,0.07)",whiteSpace:"nowrap"};
  const td={padding:"12px 14px",fontSize:13,color:"rgba(255,255,255,0.8)",borderBottom:"1px solid rgba(255,255,255,0.04)",whiteSpace:"nowrap"};

  const NAV_W=sidebar?220:64;

  return(
    <div style={{display:"flex",height:"100vh",background:C,fontFamily:"'Inter',-apple-system,sans-serif",color:"#E8EDF5",overflow:"hidden",position:"relative"}}>
      <style>{`
        *{box-sizing:border-box;}
        ::-webkit-scrollbar{width:4px;height:4px;}
        ::-webkit-scrollbar-track{background:transparent;}
        ::-webkit-scrollbar-thumb{background:rgba(255,255,255,0.1);border-radius:2px;}
        @keyframes spin{from{transform:rotate(0deg)}to{transform:rotate(360deg)}}
        @keyframes fadeUp{from{opacity:0;transform:translateY(8px)}to{opacity:1;transform:translateY(0)}}
        @keyframes bounce{0%,100%{transform:translateY(0)}50%{transform:translateY(-5px)}}
        @keyframes slideIn{from{transform:translateX(-100%)}to{transform:translateX(0)}}
        
        .pos-grid { display: grid; grid-template-columns: minmax(0,1fr) 340px; gap: 14px; height: calc(100vh - 120px); }
        .table-wrap { overflow-x: auto; width: 100%; -webkit-overflow-scrolling: touch; }

        @media(max-width:767px){
          .desktop-only{display:none!important;}
          .pos-grid { grid-template-columns: 1fr; height: auto; display: flex; flex-direction: column; }
          .pos-cart-panel { flex: none !important; margin-bottom: 20px; }
          .pos-products-panel { flex: none !important; height: 60vh; }
        }
        @media(min-width:768px){
          .mobile-only{display:none!important;}
        }
        button:hover{opacity:0.88;}
      `}</style>

      {/* ── MOBILE NAV OVERLAY ── */}
      {mobileNav&&(
        <div style={{position:"fixed",inset:0,zIndex:200}}>
          <div onClick={()=>setMobileNav(false)} style={{position:"absolute",inset:0,background:"rgba(0,0,0,0.6)"}}/>
          <aside style={{position:"absolute",left:0,top:0,bottom:0,width:240,background:C2,borderRight:"1px solid rgba(255,255,255,0.06)",display:"flex",flexDirection:"column",animation:"slideIn 0.2s ease",zIndex:201}}>
            <div style={{padding:"20px 16px 14px",borderBottom:"1px solid rgba(255,255,255,0.06)",display:"flex",alignItems:"center",gap:10}}>
              <div style={{width:36,height:36,background:"linear-gradient(135deg,#FF6B2B,#FF9A6B)",borderRadius:10,display:"flex",alignItems:"center",justifyContent:"center",fontSize:18}}>📱</div>
              <div><div style={{fontWeight:800,fontSize:15,color:"#fff"}}>PhoneShop Pro</div><div style={{fontSize:10,color:"rgba(255,255,255,0.4)"}}>Quản lý bán hàng</div></div>
            </div>
            <nav style={{flex:1,padding:"12px 8px",display:"flex",flexDirection:"column",gap:2,overflowY:"auto"}}>
              {navItems.map(item=>(
                <button key={item.id} onClick={()=>{setTab(item.id);setMobileNav(false);}} style={{display:"flex",alignItems:"center",gap:10,padding:"11px 14px",borderRadius:10,border:"none",cursor:"pointer",background:tab===item.id?"rgba(255,107,43,0.15)":"transparent",color:tab===item.id?"#FF6B2B":"rgba(255,255,255,0.55)",fontSize:14,fontWeight:tab===item.id?600:400,width:"100%",textAlign:"left",borderLeft:tab===item.id?"3px solid #FF6B2B":"3px solid transparent"}}>
                  <span style={{fontSize:18}}>{item.icon}</span>{item.label}
                  {item.id==="products"&&lowStock.length>0&&<span style={{marginLeft:"auto",background:"#FF3B30",color:"#fff",borderRadius:10,padding:"1px 7px",fontSize:10,fontWeight:700}}>{lowStock.length}</span>}
                </button>
              ))}
            </nav>
            <div style={{padding:"12px 16px",borderTop:"1px solid rgba(255,255,255,0.06)",display:"flex",flexDirection:"column",gap:8}}>
              <button onClick={handleExportBackup} style={{...btnS,width:"100%",fontSize:12,padding:"8px 12px",background:"rgba(0,122,255,0.1)",color:"#007AFF",border:"1px solid rgba(0,122,255,0.2)"}}>📥 Xuất Backup File</button>
              <label style={{...btnS,width:"100%",fontSize:12,padding:"8px 12px",textAlign:"center",background:"rgba(52,199,89,0.1)",color:"#34C759",border:"1px solid rgba(52,199,89,0.2)",cursor:"pointer"}}>
                📤 Nhập Backup File
                <input type="file" accept=".json" style={{display:"none"}} onChange={handleImportBackup} ref={fileInputRef} />
              </label>
            </div>
          </aside>
        </div>
      )}

      {/* ── DESKTOP SIDEBAR ── */}
      <aside className="desktop-only" style={{width:NAV_W,background:C2,display:"flex",flexDirection:"column",transition:"width 0.25s ease",flexShrink:0,borderRight:"1px solid rgba(255,255,255,0.06)"}}>
        <div style={{padding:sidebar?"20px 20px 14px":"20px 12px 14px",borderBottom:"1px solid rgba(255,255,255,0.06)",display:"flex",alignItems:"center",gap:10}}>
          <div style={{width:36,height:36,background:"linear-gradient(135deg,#FF6B2B,#FF9A6B)",borderRadius:10,display:"flex",alignItems:"center",justifyContent:"center",fontSize:18,flexShrink:0}}>📱</div>
          {sidebar&&<div><div style={{fontWeight:800,fontSize:15,color:"#fff",whiteSpace:"nowrap"}}>PhoneShop Pro</div><div style={{fontSize:10,color:"rgba(255,255,255,0.4)"}}>Quản lý bán hàng</div></div>}
        </div>
        <nav style={{flex:1,padding:"12px 8px",display:"flex",flexDirection:"column",gap:2,overflowY:"auto"}}>
          {navItems.map(item=>(
            <button key={item.id} onClick={()=>setTab(item.id)} style={{display:"flex",alignItems:"center",gap:10,padding:sidebar?"10px 12px":"10px",borderRadius:10,border:"none",cursor:"pointer",background:tab===item.id?"rgba(255,107,43,0.15)":"transparent",color:tab===item.id?"#FF6B2B":"rgba(255,255,255,0.55)",fontSize:13.5,fontWeight:tab===item.id?600:400,width:"100%",textAlign:"left",borderLeft:tab===item.id?"3px solid #FF6B2B":"3px solid transparent",transition:"all 0.15s"}}>
              <span style={{fontSize:17,flexShrink:0}}>{item.icon}</span>
              {sidebar&&<><span style={{whiteSpace:"nowrap"}}>{item.label}</span>{item.id==="products"&&lowStock.length>0&&<span style={{marginLeft:"auto",background:"#FF3B30",color:"#fff",borderRadius:10,padding:"1px 7px",fontSize:10,fontWeight:700}}>{lowStock.length}</span>}</>}
            </button>
          ))}
        </nav>
        {sidebar&&<div style={{padding:"12px", borderTop:"1px solid rgba(255,255,255,0.06)", display:"flex", flexDirection:"column", gap:8}}>
           <button onClick={handleExportBackup} title="Xuất dữ liệu Json tải về máy" style={{background:"transparent",border:"1px solid rgba(0,122,255,0.3)",color:"#007AFF",padding:"6px",borderRadius:8,fontSize:11,cursor:"pointer"}}>📥 Export Backup</button>
           <label style={{background:"transparent",border:"1px solid rgba(52,199,89,0.3)",color:"#34C759",padding:"6px",borderRadius:8,fontSize:11,cursor:"pointer",textAlign:"center"}}>
              📤 Import Backup
              <input type="file" accept=".json" style={{display:"none"}} onChange={handleImportBackup} />
           </label>
        </div>}
        <button onClick={()=>setSidebar(!sidebar)} style={{margin:"0 8px 12px",padding:"8px",background:"rgba(255,255,255,0.04)",border:"1px solid rgba(255,255,255,0.08)",borderRadius:8,cursor:"pointer",color:"rgba(255,255,255,0.4)",fontSize:13,textAlign:"center"}}>
          {sidebar?"◀":"▶"}
        </button>
      </aside>

      {/* ── MAIN ── */}
      <main style={{flex:1,display:"flex",flexDirection:"column",overflow:"hidden",minWidth:0}}>
        {/* HEADER */}
        <header style={{padding:"12px 16px",background:C2,borderBottom:"1px solid rgba(255,255,255,0.06)",display:"flex",alignItems:"center",justifyContent:"space-between",gap:10,flexShrink:0}}>
          <div style={{display:"flex",alignItems:"center",gap:12}}>
            <button className="mobile-only" onClick={()=>setMobileNav(true)} style={{background:"rgba(255,255,255,0.07)",border:"1px solid rgba(255,255,255,0.1)",borderRadius:9,padding:"8px 11px",cursor:"pointer",color:"#fff",fontSize:16}}>☰</button>
            <div>
              <div style={{fontSize:16,fontWeight:700,color:"#fff"}}>{navItems.find(n=>n.id===tab)?.label}</div>
              <div style={{fontSize:10,color:"rgba(255,255,255,0.35)"}}>{new Date().toLocaleDateString("vi-VN",{weekday:"long",year:"numeric",month:"long",day:"numeric"})}</div>
            </div>
          </div>
          <div style={{display:"flex",alignItems:"center",gap:8,flexShrink:0}}>
            {/* SYNC STATUS */}
            {syncCfg&&(
              <div style={{background:syncCfg.bg,border:`1px solid ${syncCfg.border}`,borderRadius:8,padding:"5px 10px",fontSize:11,color:syncCfg.color,display:"flex",alignItems:"center",gap:5,whiteSpace:"nowrap"}}>
                {syncCfg.spin&&<span style={{display:"inline-block",animation:"spin 1s linear infinite"}}>⟳</span>}
                <span className="desktop-only">{syncCfg.label}</span>
              </div>
            )}
            <div style={{display:"flex",alignItems:"center",gap:7,background:"rgba(255,255,255,0.05)",padding:"4px 10px",borderRadius:10,border:"1px solid rgba(255,255,255,0.08)"}}>
              <div style={{width:28,height:28,background:"linear-gradient(135deg,#FF6B2B,#FF9A6B)",borderRadius:"50%",display:"flex",alignItems:"center",justifyContent:"center",fontSize:13}}>👤</div>
              <div className="desktop-only">
                <div style={{fontSize:11,fontWeight:600,color:"#fff",maxWidth: 100, overflow:"hidden", textOverflow:"ellipsis", whiteSpace:"nowrap"}}>{authUser.name}</div>
              </div>
              <div style={{width: 1, height: 20, background: "rgba(255,255,255,0.1)", margin: "0 4px"}} />
              <button onClick={handleLogout} title="Đăng xuất" style={{background:"transparent", border:"none", color:"rgba(255,255,255,0.6)", cursor:"pointer", fontSize: 16, padding: "4px", display:"flex", alignItems:"center"}}>🚪</button>
            </div>
          </div>
        </header>

        {/* CONTENT */}
        <div style={{flex:1,overflow:"auto",padding:"16px", WebkitOverflowScrolling: "touch"}}>

          {/* ── DASHBOARD ── */}
          {tab==="dashboard"&&(
            <div>
              <div style={{display:"grid",gridTemplateColumns:"repeat(auto-fit,minmax(140px,1fr))",gap:12,marginBottom:16}}>
                {[
                  {label:"Doanh thu",value:fmtS(todayRev)+"đ",icon:"💰",accent:"#FF6B2B",sub:`${orders.filter(o=>o.status==="Hoàn thành").length} đơn`},
                  {label:"Đơn hàng",value:orders.length,icon:"🧾",accent:"#007AFF",sub:"Hôm nay"},
                  {label:"Tổng tồn kho",value:products.reduce((s,p)=>s+p.stock,0),icon:"📦",accent:"#34C759",sub:`${lowStock.length} cần nhập`},
                  {label:"Lợi nhuận T6",value:fmtS(totalProfit)+"đ",icon:"📈",accent:"#AF52DE",sub:"25.3%"},
                ].map((s,i)=>(
                  <div key={i} style={{background:`linear-gradient(135deg,${s.accent}22,${s.accent}08)`,border:`1px solid ${s.accent}30`,borderRadius:14,padding:"14px 12px"}}>
                    <div style={{display:"flex",justifyContent:"space-between",alignItems:"flex-start"}}>
                      <div>
                        <div style={{fontSize:10,color:"rgba(255,255,255,0.4)",textTransform:"uppercase",letterSpacing:0.7,fontWeight:600}}>{s.label}</div>
                        <div style={{fontSize:18,fontWeight:800,color:s.accent,lineHeight:1.2,marginTop:4}}>{s.value}</div>
                        <div style={{fontSize:10,color:"rgba(255,255,255,0.4)",marginTop:3}}>{s.sub}</div>
                      </div>
                      <div style={{fontSize:22}}>{s.icon}</div>
                    </div>
                  </div>
                ))}
              </div>
              <div style={{display:"grid",gridTemplateColumns:"repeat(auto-fit,minmax(300px,1fr))",gap:12,marginBottom:12}}>
                <div style={card}>
                  <div style={{display:"flex",justifyContent:"space-between",alignItems:"center",marginBottom:14}}>
                    <div style={{fontSize:14,fontWeight:700,color:"#fff"}}>Doanh thu 6 tháng</div>
                    <div style={{display:"flex",gap:8,fontSize:10}}><span style={tag("#FF6B2B")}>■ DT</span><span style={tag("rgba(255,255,255,0.4)")}>■ Chi</span></div>
                  </div>
                  <MiniBar data={RD}/>
                  <div style={{height:1,background:"rgba(255,255,255,0.06)",margin:"12px 0"}}/>
                  <div style={{display:"grid",gridTemplateColumns:"repeat(3,1fr)",gap:8}}>
                    {[{l:"Tổng DT",v:fmtS(RD.reduce((s,d)=>s+d.revenue,0))+"đ"},{l:"Chi phí",v:fmtS(RD.reduce((s,d)=>s+d.cost,0))+"đ"},{l:"LN",v:fmtS(totalProfit)+"đ"}].map((m,i)=>(
                      <div key={i} style={{textAlign:"center",padding:8,background:"rgba(255,255,255,0.03)",borderRadius:8}}>
                        <div style={{fontSize:9,color:"rgba(255,255,255,0.4)",marginBottom:2}}>{m.l}</div>
                        <div style={{fontSize:13,fontWeight:700,color:"#FF6B2B"}}>{m.v}</div>
                      </div>
                    ))}
                  </div>
                </div>
                <div style={card}>
                  <div style={{fontSize:14,fontWeight:700,color:"#fff",marginBottom:12}}>Top bán chạy</div>
                  {[...products].sort((a,b)=>b.sold-a.sold).slice(0,5).map((p,i)=>{
                    const mx=Math.max(...products.map(x=>x.sold));
                    return(
                      <div key={p.id} style={{marginBottom:10}}>
                        <div style={{display:"flex",justifyContent:"space-between",marginBottom:3}}>
                          <div style={{fontSize:11,color:"rgba(255,255,255,0.7)",display:"flex",alignItems:"center",gap:5}}>
                            <span style={{fontSize:9,fontWeight:700,color:i<3?"#FF6B2B":"rgba(255,255,255,0.3)",width:14}}>#{i+1}</span>
                            <span style={{overflow:"hidden",textOverflow:"ellipsis",whiteSpace:"nowrap",maxWidth:160}}>{p.name}</span>
                          </div>
                          <span style={{fontSize:10,color:"#FF6B2B",fontWeight:700,flexShrink:0}}>{p.sold}</span>
                        </div>
                        <div style={{height:3,background:"rgba(255,255,255,0.06)",borderRadius:2}}>
                          <div style={{height:"100%",width:`${(p.sold/mx)*100}%`,background:i<3?"linear-gradient(90deg,#FF6B2B,#FF9A6B)":"rgba(255,255,255,0.2)",borderRadius:2}}/>
                        </div>
                      </div>
                    );
                  })}
                </div>
              </div>
              <div style={{display:"grid",gridTemplateColumns:"repeat(auto-fit,minmax(280px,1fr))",gap:12}}>
                <div style={card}>
                  <div style={{fontSize:14,fontWeight:700,color:"#fff",marginBottom:12}}>Hóa đơn gần nhất</div>
                  {orders.slice(0,4).map(o=>(
                    <div key={o.id} style={{display:"flex",justifyContent:"space-between",alignItems:"center",padding:"9px 0",borderBottom:"1px solid rgba(255,255,255,0.04)"}}>
                      <div>
                        <div style={{fontSize:12,fontWeight:700,color:"#FF6B2B"}}>{o.id}</div>
                        <div style={{fontSize:11,color:"rgba(255,255,255,0.45)"}}>{o.customer}</div>
                      </div>
                      <div style={{textAlign:"right"}}>
                        <div style={{fontSize:12,fontWeight:700,color:"#fff"}}>{fmtS(o.total)}đ</div>
                        <span style={tag(o.status==="Hoàn thành"?"#34C759":"#FF9500")}>{o.status}</span>
                      </div>
                    </div>
                  ))}
                </div>
                <div style={card}>
                  <div style={{fontSize:14,fontWeight:700,color:"#fff",marginBottom:12}}>⚠️ Sắp hết hàng</div>
                  {lowStock.length===0?<div style={{textAlign:"center",padding:20,color:"rgba(255,255,255,0.3)",fontSize:13}}>✅ Tồn kho ổn định</div>:
                    lowStock.map(p=>(
                      <div key={p.id} style={{display:"flex",justifyContent:"space-between",alignItems:"center",padding:"9px 0",borderBottom:"1px solid rgba(255,255,255,0.04)"}}>
                        <div style={{fontSize:12,color:"#fff",fontWeight:500,maxWidth:"65%",overflow:"hidden",textOverflow:"ellipsis",whiteSpace:"nowrap"}}>{p.name}</div>
                        <div style={{textAlign:"right",flexShrink:0}}>
                          <div style={{fontSize:16,fontWeight:800,color:p.stock===0?"#FF3B30":"#FF9500"}}>{p.stock}</div>
                          <div style={{fontSize:9,color:"rgba(255,255,255,0.3)"}}>tối thiểu {p.minStock}</div>
                        </div>
                      </div>
                    ))
                  }
                </div>
              </div>
            </div>
          )}

          {/* ── POS ── */}
          {tab==="pos"&&(
            <div className="pos-grid">
              <div className="pos-products-panel" style={{display:"flex",flexDirection:"column",gap:10,overflow:"hidden",minWidth:0}}>
                <div style={{display:"flex",gap:8, flexWrap: "wrap"}}>
                  <input style={{...inp,flex:1, minWidth: "150px"}} placeholder="🔍 Tìm SP, SKU…" value={searchP} onChange={e=>setSearchP(e.target.value)}/>
                  <select style={{...inp,width:110}} value={catFilter} onChange={e=>setCatFilter(e.target.value)}>
                    {cats.map(c=><option key={c} value={c}>{c}</option>)}
                  </select>
                </div>
                <div style={{display:"grid",gridTemplateColumns:"repeat(auto-fill,minmax(140px,1fr))",gap:8,overflow:"auto",paddingBottom:10}}>
                  {filteredP.map(p=>(
                    <button key={p.id} onClick={()=>addToCart(p)} style={{background:p.stock===0?"rgba(255,255,255,0.02)":C3,border:p.stock===0?"1px solid rgba(255,255,255,0.04)":"1px solid rgba(255,255,255,0.08)",borderRadius:12,padding:"10px 8px",cursor:p.stock===0?"not-allowed":"pointer",textAlign:"left",opacity:p.stock===0?0.4:1,transition:"all 0.15s"}}>
                      <div style={{fontSize:24,marginBottom:5}}>{p.image}</div>
                      <div style={{fontSize:11,fontWeight:600,color:"#fff",lineHeight:1.3,marginBottom:3}}>{p.name.length>28?p.name.slice(0,28)+"…":p.name}</div>
                      <div style={{fontSize:13,fontWeight:800,color:"#FF6B2B",marginBottom:4}}>{fmtS(p.price)}đ</div>
                      <span style={tag(p.stock===0?"#FF3B30":p.stock<=p.minStock?"#FF9500":"#34C759")}>{p.stock===0?"Hết":`${p.stock} còn`}</span>
                    </button>
                  ))}
                </div>
              </div>
              {/* CART */}
              <div className="pos-cart-panel" style={{...card,display:"flex",flexDirection:"column",overflow:"hidden",minWidth:0}}>
                <div style={{fontSize:14,fontWeight:700,color:"#fff",marginBottom:10}}>🛒 Hóa đơn</div>
                <select style={{...inp,marginBottom:10}} value={selCust?.id||""} onChange={e=>setSelCust(customers.find(c=>c.id===+e.target.value)||null)}>
                  <option value="">👤 Khách lẻ</option>
                  {customers.map(c=><option key={c.id} value={c.id}>{c.name} — {c.points} điểm</option>)}
                </select>
                <div style={{flex:1,overflow:"auto", marginBottom:10}}>
                  {cart.length===0?<div style={{textAlign:"center",padding:"30px 10px",color:"rgba(255,255,255,0.2)"}}>
                    <div style={{fontSize:36,marginBottom:6}}>🛒</div>
                    <div style={{fontSize:12}}>Chọn sản phẩm bên trái</div>
                  </div>:cart.map(item=>(
                    <div key={item.id} style={{padding:"9px 0",borderBottom:"1px solid rgba(255,255,255,0.05)"}}>
                      <div style={{display:"flex",justifyContent:"space-between",alignItems:"flex-start"}}>
                        <div style={{flex:1,marginRight:6}}>
                          <div style={{fontSize:11,fontWeight:600,color:"#fff",lineHeight:1.3}}>{item.name}</div>
                          <div style={{fontSize:11,color:"#FF6B2B",marginTop:2}}>{fmt(item.price)}</div>
                        </div>
                        <button onClick={()=>removeFromCart(item.id)} style={{background:"none",border:"none",color:"rgba(255,255,255,0.3)",cursor:"pointer",fontSize:20,padding:"0 6px"}}>×</button>
                      </div>
                      <div style={{display:"flex",justifyContent:"space-between",alignItems:"center",marginTop:6}}>
                        <div style={{display:"flex",alignItems:"center",gap:4}}>
                          <button onClick={()=>updateQty(item.id,item.qty-1)} style={{width:28,height:28,borderRadius:6,background:"rgba(255,255,255,0.08)",border:"none",color:"#fff",cursor:"pointer",fontSize:16}}>−</button>
                          <span style={{width:24,textAlign:"center",fontSize:13,fontWeight:700,color:"#fff"}}>{item.qty}</span>
                          <button onClick={()=>updateQty(item.id,item.qty+1)} style={{width:28,height:28,borderRadius:6,background:"rgba(255,255,255,0.08)",border:"none",color:"#fff",cursor:"pointer",fontSize:16}}>+</button>
                        </div>
                        <span style={{fontSize:13,fontWeight:800,color:"#fff"}}>{fmt(item.price*item.qty)}</span>
                      </div>
                    </div>
                  ))}
                </div>
                <div>
                  <div style={{height:1,background:"rgba(255,255,255,0.06)",marginBottom:10}}/>
                  <div style={{display:"flex",gap:5,marginBottom:10}}>
                    {["Tiền mặt","Thẻ","QR","CK"].map(m=>(
                      <button key={m} onClick={()=>setPayment(m==="CK"?"Chuyển khoản":m)} style={{flex:1,padding:"8px 2px",borderRadius:8,border:`1px solid ${payment===(m==="CK"?"Chuyển khoản":m)?"#FF6B2B":"rgba(255,255,255,0.1)"}`,background:payment===(m==="CK"?"Chuyển khoản":m)?"rgba(255,107,43,0.15)":"transparent",color:payment===(m==="CK"?"Chuyển khoản":m)?"#FF6B2B":"rgba(255,255,255,0.4)",cursor:"pointer",fontSize:11,fontWeight:600}}>
                        {m==="Tiền mặt"?"💵":m==="Thẻ"?"💳":m==="QR"?"📲":"🏦"}<br className="mobile-only"/> {m}
                      </button>
                    ))}
                  </div>
                  <div style={{display:"flex",alignItems:"center",gap:6,marginBottom:8}}>
                    <span style={{fontSize:11,color:"rgba(255,255,255,0.4)",flexShrink:0}}>C.khấu %</span>
                    <input type="number" style={{...inp,width:60}} value={discount} onChange={e=>setDiscount(Math.min(100,Math.max(0,+e.target.value)))} min="0" max="100"/>
                    <div style={{display:"flex",gap:4,flex:1}}>
                      {[5,10,15].map(d=><button key={d} onClick={()=>setDiscount(d)} style={{flex:1,padding:"5px 2px",borderRadius:6,border:"1px solid rgba(255,255,255,0.08)",background:"rgba(255,255,255,0.04)",color:"rgba(255,255,255,0.5)",cursor:"pointer",fontSize:10}}>{d}%</button>)}
                    </div>
                  </div>
                  <div style={{background:"rgba(255,255,255,0.04)",borderRadius:10,padding:10,marginBottom:10}}>
                    <div style={{display:"flex",justifyContent:"space-between",fontSize:11,marginBottom:3}}>
                      <span style={{color:"rgba(255,255,255,0.45)"}}>Tạm tính</span><span>{fmt(cartTotal)}</span>
                    </div>
                    {discount>0&&<div style={{display:"flex",justifyContent:"space-between",fontSize:11,marginBottom:3}}>
                      <span style={{color:"rgba(255,255,255,0.45)"}}>Chiết khấu</span><span style={{color:"#34C759"}}>−{fmt(discAmt)}</span>
                    </div>}
                    <div style={{display:"flex",justifyContent:"space-between",fontWeight:800,fontSize:15,paddingTop:6,borderTop:"1px solid rgba(255,255,255,0.08)",marginTop:4}}>
                      <span style={{color:"#fff"}}>Tổng cộng</span><span style={{color:"#FF6B2B"}}>{fmt(finalTotal)}</span>
                    </div>
                  </div>
                  <button onClick={checkout} style={{...btnP,width:"100%",padding:14,fontSize:14,textAlign:"center"}}>
                    💳 Thanh toán{cart.length>0?` (${cart.length})`:""}
                  </button>
                </div>
              </div>
            </div>
          )}

          {/* ── PRODUCTS ── */}
          {tab==="products"&&(
            <div>
              <div style={{display:"flex",flexWrap:"wrap",gap:8,justifyContent:"space-between",alignItems:"center",marginBottom:14}}>
                <div style={{display:"flex",gap:8,flex:1,minWidth:0}}>
                  <input style={{...inp,maxWidth:240}} placeholder="🔍 Tìm tên, SKU…" value={searchP} onChange={e=>setSearchP(e.target.value)}/>
                  <select style={{...inp,width:110}} value={catFilter} onChange={e=>setCatFilter(e.target.value)}>
                    {cats.map(c=><option key={c} value={c}>{c}</option>)}
                  </select>
                </div>
                <button onClick={()=>setShowAddP(true)} style={btnP}>+ Thêm SP</button>
              </div>
              {showAddP&&(
                <div style={{...card,marginBottom:14,border:"1px solid rgba(255,107,43,0.3)"}}>
                  <div style={{fontSize:14,fontWeight:700,color:"#FF6B2B",marginBottom:12,display:"flex",justifyContent:"space-between",alignItems:"center"}}>
                    <span>➕ Thêm sản phẩm mới</span>
                  </div>
                  <div style={{display:"grid",gridTemplateColumns:"repeat(auto-fit,minmax(140px,1fr))",gap:10,marginBottom:12}}>
                    {[{l:"Mã SKU *",k:"sku",ph:"VD: IPH15-BLK"},{l:"Tên sản phẩm *",k:"name",ph:"VD: iPhone 15"},{l:"Thương hiệu",k:"brand",ph:"Apple"},{l:"Màu sắc",k:"color",ph:"Đen Titan"},{l:"Giá bán *",k:"price",ph:"22490000",t:"number"},{l:"Giá vốn",k:"costPrice",ph:"17800000",t:"number"},{l:"Tồn kho",k:"stock",ph:"10",t:"number"},{l:"Tối thiểu",k:"minStock",ph:"3",t:"number"}].map(f=>(
                      <div key={f.k}>
                        <div style={{fontSize:10,color:"rgba(255,255,255,0.4)",marginBottom:3}}>{f.l}</div>
                        <input type={f.t||"text"} style={inp} placeholder={f.ph} value={newP[f.k]} onChange={e=>setNewP(p=>({...p,[f.k]:e.target.value}))}/>
                      </div>
                    ))}
                  </div>
                  <div style={{display:"flex",alignItems:"center",flexWrap:"wrap",gap:8,marginBottom:12}}>
                    <div style={{fontSize:10,color:"rgba(255,255,255,0.4)"}}>Danh mục:</div>
                    <select style={{...inp,width:120}} value={newP.category} onChange={e=>setNewP(p=>({...p,category:e.target.value}))}>
                      {["iPhone","Samsung","OPPO","Xiaomi","Vivo","Realme","Phụ kiện","Tai nghe","Khác"].map(c=><option key={c} value={c}>{c}</option>)}
                    </select>
                    <div style={{fontSize:10,color:"rgba(255,255,255,0.4)"}}>Icon:</div>
                    <select style={{...inp,width:80}} value={newP.image} onChange={e=>setNewP(p=>({...p,image:e.target.value}))}>
                      {["📱","🎧","🔌","🛡️","⌚","💻"].map(ic=><option key={ic} value={ic}>{ic}</option>)}
                    </select>
                  </div>
                  <div style={{display:"flex",gap:8}}>
                    <button onClick={addProduct} style={btnP}>✅ Lưu sản phẩm</button>
                    <button onClick={()=>setShowAddP(false)} style={btnS}>Hủy</button>
                  </div>
                </div>
              )}
              <div style={card}>
                <div className="table-wrap">
                  <table style={{width:"100%",borderCollapse:"collapse"}}>
                    <thead>
                      <tr>
                        <th style={th}>Sản phẩm</th>
                        <th style={{...th}} className="desktop-only">SKU</th>
                        <th style={th}>Giá bán</th>
                        <th style={th}>Biên LN</th>
                        <th style={th}>Tồn kho</th>
                        <th style={th}>Đã bán</th>
                      </tr>
                    </thead>
                    <tbody>
                      {filteredP.map(p=>{
                        const mg=Math.round(((p.price-p.costPrice)/p.price)*100);
                        const sc=p.stock===0?"#FF3B30":p.stock<=p.minStock?"#FF9500":"#34C759";
                        return(
                          <tr key={p.id}>
                            <td style={td}>
                              <div style={{display:"flex",alignItems:"center",gap:8}}>
                                <span style={{fontSize:20}}>{p.image}</span>
                                <div>
                                  <div style={{fontWeight:600,color:"#fff",fontSize:12, whiteSpace:"normal"}}>{p.name}</div>
                                  <div style={{fontSize:10,color:"rgba(255,255,255,0.4)"}}>{p.color}</div>
                                </div>
                              </div>
                            </td>
                            <td style={{...td,fontFamily:"monospace",fontSize:10,color:"rgba(255,255,255,0.4)"}} className="desktop-only">{p.sku}</td>
                            <td style={{...td,fontWeight:700,color:"#fff"}}>{fmtS(p.price)}đ</td>
                            <td style={td}><span style={tag(mg>=25?"#34C759":mg>=15?"#FF9500":"#FF3B30")}>{mg}%</span></td>
                            <td style={td}><span style={{fontWeight:800,color:sc,fontSize:15}}>{p.stock}</span>{p.stock<=p.minStock&&<span style={{marginLeft:4}}>{p.stock===0?"🔴":"🟡"}</span>}</td>
                            <td style={{...td,fontWeight:600,color:"#FF6B2B"}}>{p.sold}</td>
                          </tr>
                        );
                      })}
                    </tbody>
                  </table>
                </div>
              </div>
            </div>
          )}

          {/* ── ORDERS ── */}
          {tab==="orders"&&(
            <div style={card}>
              <div style={{display:"flex",justifyContent:"space-between",alignItems:"center",marginBottom:14,flexWrap:"wrap",gap:8}}>
                <div style={{fontSize:14,fontWeight:700,color:"#fff"}}>Hóa đơn ({orders.length})</div>
                <div style={{display:"flex",gap:8}}>
                  <span style={tag("#34C759")}>✅ {orders.filter(o=>o.status==="Hoàn thành").length}</span>
                  <span style={tag("#FF9500")}>⏳ {orders.filter(o=>o.status==="Đang xử lý").length}</span>
                </div>
              </div>
              <div className="table-wrap">
                <table style={{width:"100%",borderCollapse:"collapse"}}>
                  <thead><tr>
                    <th style={th}>Mã HĐ</th>
                    <th style={{...th}} className="desktop-only">Thời gian</th>
                    <th style={th}>Khách</th>
                    <th style={{...th}} className="desktop-only">Thanh toán</th>
                    <th style={th}>Tổng tiền</th>
                    <th style={th}>Trạng thái</th>
                  </tr></thead>
                  <tbody>
                    {orders.map(o=>(
                      <tr key={o.id}>
                        <td style={{...td,fontWeight:800,color:"#FF6B2B",fontFamily:"monospace"}}>{o.id}</td>
                        <td style={{...td,fontSize:10,color:"rgba(255,255,255,0.4)"}} className="desktop-only">{o.date}</td>
                        <td style={td}>{o.customer}</td>
                        <td style={td} className="desktop-only"><span style={{fontSize:12}}>{o.payment==="Tiền mặt"?"💵":o.payment==="Thẻ"?"💳":o.payment==="QR"?"📲":"🏦"}</span> {o.payment}</td>
                        <td style={{...td,fontWeight:800,color:"#fff"}}>{fmtS(o.total)}đ</td>
                        <td style={td}><span style={tag(o.status==="Hoàn thành"?"#34C759":"#FF9500")}>{o.status}</span></td>
                      </tr>
                    ))}
                  </tbody>
                </table>
              </div>
            </div>
          )}

          {/* ── CUSTOMERS ── */}
          {tab==="customers"&&(
            <div>
              <div style={{display:"grid",gridTemplateColumns:"repeat(auto-fit,minmax(120px,1fr))",gap:10,marginBottom:14}}>
                {[{l:"Tổng KH",v:customers.length,i:"👥"},{l:"VIP",v:customers.filter(c=>c.group==="VIP").length,i:"⭐"},{l:"Đại lý",v:customers.filter(c=>c.group==="Đại lý").length,i:"🏪"},{l:"DT từ KH",v:fmtS(customers.reduce((s,c)=>s+c.totalSpent,0))+"đ",i:"💰"}].map((s,i)=>(
                  <div key={i} style={{...card,textAlign:"center", padding:"16px 10px"}}>
                    <div style={{fontSize:22,marginBottom:4}}>{s.i}</div>
                    <div style={{fontSize:18,fontWeight:800,color:"#FF6B2B"}}>{s.v}</div>
                    <div style={{fontSize:10,color:"rgba(255,255,255,0.4)"}}>{s.l}</div>
                  </div>
                ))}
              </div>
              <div style={card}>
                <div style={{display:"flex",justifyContent:"space-between",alignItems:"center",marginBottom:12,flexWrap:"wrap",gap:8}}>
                  <div style={{fontSize:14,fontWeight:700,color:"#fff"}}>Khách hàng</div>
                  <input style={{...inp,maxWidth:180}} placeholder="🔍 Tìm khách…" value={searchC} onChange={e=>setSearchC(e.target.value)}/>
                </div>
                <div className="table-wrap">
                  <table style={{width:"100%",borderCollapse:"collapse"}}>
                    <thead><tr>
                      <th style={th}>Khách hàng</th>
                      <th style={{...th}} className="desktop-only">SĐT</th>
                      <th style={th}>Nhóm</th>
                      <th style={th}>Điểm</th>
                      <th style={th}>Chi tiêu</th>
                      <th style={{...th}} className="desktop-only">Đơn</th>
                    </tr></thead>
                    <tbody>
                      {customers.filter(c=>c.name.toLowerCase().includes(searchC.toLowerCase())||c.phone.includes(searchC)).map(c=>{
                        const gc={"VIP":"#FFD700","Thân thiết":"#007AFF","Đại lý":"#AF52DE","Khách lẻ":"rgba(255,255,255,0.4)"};
                        return(
                          <tr key={c.id}>
                            <td style={td}>
                              <div style={{display:"flex",alignItems:"center",gap:8}}>
                                <div style={{width:30,height:30,borderRadius:"50%",background:"linear-gradient(135deg,#FF6B2B44,#007AFF44)",display:"flex",alignItems:"center",justifyContent:"center",fontWeight:700,fontSize:12,color:"#fff",flexShrink:0}}>{c.name[0]}</div>
                                <div>
                                  <div style={{fontWeight:600,color:"#fff",fontSize:12}}>{c.name}</div>
                                  <div style={{fontSize:10,color:"rgba(255,255,255,0.4)"}} className="desktop-only">{c.email}</div>
                                </div>
                              </div>
                            </td>
                            <td style={{...td,fontFamily:"monospace",fontSize:11}} className="desktop-only">{c.phone}</td>
                            <td style={td}><span style={tag(gc[c.group]||"#888")}>{c.group}</span></td>
                            <td style={{...td,fontWeight:700,color:"#FFD700"}}>⭐ {c.points.toLocaleString()}</td>
                            <td style={{...td,fontWeight:700,color:"#FF6B2B"}}>{fmtS(c.totalSpent)}đ</td>
                            <td style={{...td,textAlign:"center"}} className="desktop-only">{c.orders}</td>
                          </tr>
                        );
                      })}
                    </tbody>
                  </table>
                </div>
              </div>
            </div>
          )}

          {/* ── STAFF ── */}
          {tab==="staff"&&(
            <div>
              <div style={{display:"grid",gridTemplateColumns:"repeat(auto-fit,minmax(130px,1fr))",gap:10,marginBottom:14}}>
                {[{l:"Tổng NV",v:STAFF.length,i:"👔"},{l:"Quỹ lương",v:fmtS(STAFF.reduce((s,e)=>s+e.salary,0))+"đ",i:"💵"},{l:"DS trung bình",v:fmtS(STAFF.filter(e=>e.sales>0).reduce((s,e)=>s+e.sales,0)/STAFF.filter(e=>e.sales>0).length)+"đ",i:"📊"},{l:"Hoa hồng (ƯT)",v:fmtS(STAFF.reduce((s,e)=>s+(e.sales*e.commission/100),0))+"đ",i:"🎯"}].map((s,i)=>(
                  <div key={i} style={{...card, padding:"16px 10px", textAlign:"center"}}>
                    <div style={{fontSize:22,marginBottom:5}}>{s.i}</div>
                    <div style={{fontSize:18,fontWeight:800,color:"#FF6B2B"}}>{s.v}</div>
                    <div style={{fontSize:10,color:"rgba(255,255,255,0.4)",marginTop:2}}>{s.l}</div>
                  </div>
                ))}
              </div>
              <div style={card}>
                <div style={{fontSize:14,fontWeight:700,color:"#fff",marginBottom:12}}>Bảng lương tháng 6/2025</div>
                <div className="table-wrap">
                  <table style={{width:"100%",borderCollapse:"collapse"}}>
                    <thead><tr>
                      <th style={th}>Nhân viên</th>
                      <th style={th}>Chức vụ</th>
                      <th style={{...th}} className="desktop-only">Ngày</th>
                      <th style={{...th}} className="desktop-only">Doanh số</th>
                      <th style={th}>Lương CB</th>
                      <th style={th}>Hoa hồng</th>
                      <th style={th}>Tổng</th>
                    </tr></thead>
                    <tbody>
                      {STAFF.map((e,i)=>{
                        const comm=Math.round(e.sales*e.commission/100);
                        return(
                          <tr key={i}>
                            <td style={td}>
                              <div style={{display:"flex",alignItems:"center",gap:8}}>
                                <div style={{width:30,height:30,borderRadius:"50%",background:"linear-gradient(135deg,#007AFF44,#AF52DE44)",display:"flex",alignItems:"center",justifyContent:"center",fontWeight:700,fontSize:12,color:"#fff",flexShrink:0}}>{e.name[0]}</div>
                                <span style={{fontWeight:600,color:"#fff",fontSize:12}}>{e.name}</span>
                              </div>
                            </td>
                            <td style={td}><span style={tag(e.role==="Quản lý"?"#FF6B2B":e.role==="Kế toán"?"#AF52DE":"#007AFF")}>{e.role}</span></td>
                            <td style={{...td,textAlign:"center"}} className="desktop-only">{e.workDays}/22</td>
                            <td style={{...td,color:"#FF6B2B",fontWeight:600}} className="desktop-only">{e.sales>0?fmtS(e.sales)+"đ":"—"}</td>
                            <td style={td}>{fmtS(e.salary)}đ</td>
                            <td style={{...td,color:"#34C759"}}>{comm>0?"+"+fmtS(comm)+"đ":"—"}</td>
                            <td style={{...td,fontWeight:800,color:"#fff"}}>{fmtS(e.salary+comm)}đ</td>
                          </tr>
                        );
                      })}
                    </tbody>
                  </table>
                </div>
              </div>
            </div>
          )}

          {/* ── REPORTS ── */}
          {tab==="reports"&&(
            <div>
              <div style={{display:"grid",gridTemplateColumns:"repeat(auto-fit,minmax(140px,1fr))",gap:10,marginBottom:14}}>
                {RD.slice(-3).map((d,i)=>(
                  <div key={i} style={{background:"linear-gradient(135deg,#FF6B2B22,#FF6B2B08)",border:"1px solid #FF6B2B30",borderRadius:14,padding:16}}>
                    <div style={{fontSize:10,color:"rgba(255,255,255,0.4)",textTransform:"uppercase",letterSpacing:0.7}}>Tháng {4+i}/2025</div>
                    <div style={{fontSize:20,fontWeight:800,color:"#FF6B2B",marginTop:4}}>{fmtS(d.revenue)}đ</div>
                    <div style={{fontSize:10,color:"rgba(255,255,255,0.4)",marginTop:3}}>LN: {fmtS(d.revenue-d.cost)}đ</div>
                  </div>
                )).concat([
                  <div key="total" style={{background:"linear-gradient(135deg,#34C75922,#34C75908)",border:"1px solid #34C75930",borderRadius:14,padding:16}}>
                    <div style={{fontSize:10,color:"rgba(255,255,255,0.4)",textTransform:"uppercase",letterSpacing:0.7}}>Tổng 6 tháng</div>
                    <div style={{fontSize:20,fontWeight:800,color:"#34C759",marginTop:4}}>{fmtS(RD.reduce((s,d)=>s+d.revenue,0))}đ</div>
                    <div style={{fontSize:10,color:"rgba(255,255,255,0.4)",marginTop:3}}>LN: {fmtS(totalProfit)}đ</div>
                  </div>
                ])}
              </div>
              <div style={{display:"grid",gridTemplateColumns:"repeat(auto-fit,minmax(280px,1fr))",gap:12,marginBottom:12}}>
                <div style={card}>
                  <div style={{fontSize:14,fontWeight:700,color:"#fff",marginBottom:12}}>Doanh thu & Lợi nhuận</div>
                  <div className="table-wrap">
                    <table style={{width:"100%",borderCollapse:"collapse"}}>
                      <thead><tr>
                        <th style={th}>Tháng</th><th style={th}>Doanh thu</th><th style={{...th}} className="desktop-only">Chi phí</th><th style={th}>Lợi nhuận</th><th style={th}>Biên</th>
                      </tr></thead>
                      <tbody>
                        {RD.map((d,i)=>{
                          const pr=d.revenue-d.cost;
                          const mg=Math.round((pr/d.revenue)*100);
                          return(
                            <tr key={i}>
                              <td style={{...td,fontWeight:700}}>T{i+1}</td>
                              <td style={{...td,color:"#FF6B2B",fontWeight:700}}>{fmtS(d.revenue)}đ</td>
                              <td style={{...td,color:"rgba(255,255,255,0.5)"}} className="desktop-only">{fmtS(d.cost)}đ</td>
                              <td style={{...td,color:"#34C759",fontWeight:700}}>{fmtS(pr)}đ</td>
                              <td style={td}><span style={tag(mg>=25?"#34C759":"#FF9500")}>{mg}%</span></td>
                            </tr>
                          );
                        })}
                      </tbody>
                    </table>
                  </div>
                </div>
                <div style={card}>
                  <div style={{fontSize:14,fontWeight:700,color:"#fff",marginBottom:12}}>Top 8 sản phẩm</div>
                  {[...products].sort((a,b)=>b.sold-a.sold).slice(0,8).map((p,i)=>(
                    <div key={p.id} style={{display:"flex",alignItems:"center",gap:8,padding:"7px 0",borderBottom:"1px solid rgba(255,255,255,0.04)"}}>
                      <div style={{width:20,height:20,borderRadius:5,background:i<3?"rgba(255,107,43,0.2)":"rgba(255,255,255,0.06)",display:"flex",alignItems:"center",justifyContent:"center",fontSize:10,fontWeight:700,color:i<3?"#FF6B2B":"rgba(255,255,255,0.3)",flexShrink:0}}>#{i+1}</div>
                      <span style={{fontSize:14}}>{p.image}</span>
                      <div style={{flex:1,minWidth:0}}>
                        <div style={{fontSize:11,fontWeight:600,color:"#fff",overflow:"hidden",textOverflow:"ellipsis",whiteSpace:"nowrap"}}>{p.name}</div>
                        <div style={{fontSize:9,color:"rgba(255,255,255,0.35)"}}>{p.sold} đã bán</div>
                      </div>
                      <div style={{fontSize:11,fontWeight:700,color:"#FF6B2B",flexShrink:0}}>{fmtS(p.sold*p.price)}đ</div>
                    </div>
                  ))}
                </div>
              </div>
              <div style={card}>
                <div style={{fontSize:14,fontWeight:700,color:"#fff",marginBottom:12}}>Chi phí vận hành T6/2025</div>
                <div style={{display:"grid",gridTemplateColumns:"repeat(auto-fit,minmax(140px,1fr))",gap:10}}>
                  {[{l:"Mặt bằng",a:15000000,i:"🏬"},{l:"Điện nước",a:3200000,i:"💡"},{l:"Nhân sự",a:STAFF.reduce((s,e)=>s+e.salary,0),i:"👔"},{l:"Marketing",a:5000000,i:"📣"},{l:"Bảo trì",a:1500000,i:"🔧"},{l:"Vận chuyển",a:2800000,i:"🚚"},{l:"Khấu hao",a:3000000,i:"📉"},{l:"Chi phí khác",a:1200000,i:"📎"}].map((c,i)=>(
                    <div key={i} style={{background:"rgba(255,255,255,0.04)",borderRadius:10,padding:12,display:"flex",alignItems:"center",gap:8}}>
                      <span style={{fontSize:20}}>{c.i}</span>
                      <div>
                        <div style={{fontSize:10,color:"rgba(255,255,255,0.4)"}}>{c.l}</div>
                        <div style={{fontSize:13,fontWeight:700,color:"#fff"}}>{fmtS(c.a)}đ</div>
                      </div>
                    </div>
                  ))}
                </div>
              </div>
            </div>
          )}
        </div>

        {/* ── MOBILE BOTTOM NAV ── */}
        <div className="mobile-only" style={{background:C2,borderTop:"1px solid rgba(255,255,255,0.07)",display:"flex",justifyContent:"space-around",padding:"8px 4px",flexShrink:0, paddingBottom: "max(8px, env(safe-area-inset-bottom))"}}>
          {navItems.map(item=>(
            <button key={item.id} onClick={()=>setTab(item.id)} style={{display:"flex",flexDirection:"column",alignItems:"center",gap:2,background:"none",border:"none",cursor:"pointer",padding:"4px 8px",borderRadius:8,color:tab===item.id?"#FF6B2B":"rgba(255,255,255,0.4)",minWidth:0,flex:1}}>
              <span style={{fontSize:20}}>{item.icon}</span>
              <span style={{fontSize:9,fontWeight:tab===item.id?700:400,whiteSpace:"nowrap"}}>{item.label}</span>
            </button>
          ))}
        </div>
      </main>

      {/* ── CHATBOT ── */}
      {showChat&&<Chatbot products={products} customers={customers} orders={orders} onClose={()=>setShowChat(false)}/>}

      {/* ── CHAT TOGGLE BUTTON ── */}
      {!showChat&&(
        <button onClick={()=>setShowChat(true)} style={{position:"fixed",bottom:70,right:20,width:50,height:50,borderRadius:"50%",background:"linear-gradient(135deg,#FF6B2B,#FF4500)",border:"none",cursor:"pointer",boxShadow:"0 6px 24px rgba(255,107,43,0.5)",display:"flex",alignItems:"center",justifyContent:"center",fontSize:22,zIndex:500,transition:"all 0.2s"}}>
          🤖
        </button>
      )}

      {/* ── GLOBAL NOTIFICATION ── */}
      {notif&&(
        <div style={{position:"fixed",top:14,left:"50%",transform:"translateX(-50%)",zIndex:9998,background:notif.type==="error"?"#FF3B30":"#34C759",color:"#fff",padding:"11px 20px",borderRadius:12,fontSize:13,fontWeight:600,boxShadow:"0 8px 24px rgba(0,0,0,0.3)",animation:"fadeUp 0.2s ease",whiteSpace:"nowrap",maxWidth:"90vw"}}>
          {notif.type==="error"?"⚠️":"✅"} {notif.msg}
        </div>
      )}
    </div>
  );
}


```
            scrollbar-width: none;
        }
        /* Custom slide up animation for sheet */
        .slide-up {
            animation: slideUp 0.3s cubic-bezier(0.16, 1, 0.3, 1) forwards;
        }
        @keyframes slideUp {
            from { transform: translateY(100%); }
            to { transform: translateY(0); }
        }
        
        /* Chat bubble animations */
        .fade-in-up {
            animation: fadeInUp 0.3s cubic-bezier(0.16, 1, 0.3, 1) forwards;
        }
        @keyframes fadeInUp {
            from { opacity: 0; transform: translateY(16px); }
            to { opacity: 1; transform: translateY(0); }
        }
        .typing-dot {
            animation: typing 1.4s infinite ease-in-out both;
        }
        .typing-dot:nth-child(1) { animation-delay: -0.32s; }
        .typing-dot:nth-child(2) { animation-delay: -0.16s; }
        @keyframes typing {
            0%, 80%, 100% { transform: scale(0); }
            40% { transform: scale(1); }
        }
    </style>
</head>
<body class="bg-slate-50 text-slate-800 min-h-screen flex flex-col pb-16 overflow-x-hidden">

    <!-- ==================== SCREEN 1: LOGIN SCREEN ==================== -->
    <div id="login-screen" class="fixed inset-0 bg-gradient-to-br from-slate-900 via-emerald-950 to-emerald-900 z-50 flex items-center justify-center p-4">
        <div class="bg-white/95 backdrop-blur-md w-full max-w-sm rounded-3xl shadow-2xl p-6 space-y-6 border border-white/20">
            <div class="text-center space-y-2">
                <div class="inline-block bg-emerald-50 text-emerald-600 px-4 py-2 rounded-2xl font-black text-2xl tracking-wider shadow-inner">
                    Kiot<span class="text-amber-500">Pro</span><span class="text-indigo-500 text-sm ml-1">AI 3.0</span>
                </div>
                <h2 class="text-lg font-bold text-slate-800">Hệ thống Đăng Nhập</h2>
                <p class="text-xs text-slate-400">Ứng dụng POS & Quản lý kho thông minh tích hợp AI Actions</p>
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
                <p class="text-[10px] text-slate-400">Tài khoản dùng thử:</p>
                <p class="text-[10px] font-bold text-slate-500">Admin: <span class="text-emerald-600">admin / 123</span></p>
                <p class="text-[10px] font-bold text-slate-500">Nhân viên: <span class="text-emerald-600">nhanvien / 123</span></p>
            </div>
        </div>
    </div>

    <!-- ==================== HEADER MOBILE ==================== -->
    <header class="bg-gradient-to-r from-emerald-600 to-teal-700 text-white px-4 py-3 flex justify-between items-center shadow-lg sticky top-0 z-30 shrink-0">
        <div class="flex items-center space-x-2">
            <div class="bg-white text-emerald-600 px-2 py-0.5 rounded-lg font-black text-sm tracking-wider shadow-inner">
                Kiot<span class="text-amber-500">Pro</span>
            </div>
            <div class="flex flex-col">
                <span class="text-[10px] font-bold text-white leading-none" id="header-user-name">Đang tải...</span>
                <span class="text-[8px] bg-emerald-800 text-emerald-100 px-1.5 py-0.2 rounded-full font-medium inline-block mt-0.5" id="header-user-role">Đang tải...</span>
            </div>
        </div>
        
        <div class="flex items-center space-x-2.5">
            <span class="text-[9px] bg-white/20 text-white px-2 py-0.5 rounded-full font-bold">CN Quận 1</span>
            <button onclick="handleLogout()" class="bg-white/10 hover:bg-white/20 p-2 rounded-xl text-xs transition-colors">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2.5" d="M17 16l4-4m0 0l-4-4m4 4H7m6 4v1a3 3 0 01-3 3H6a3 3 0 01-3-3V7a3 3 0 013-3h4a3 3 0 013 3v1" />
                </svg>
            </button>
        </div>
    </header>

    <!-- ==================== MAIN SCROLLER ==================== -->
    <main class="flex-1 overflow-y-auto px-3.5 py-4 space-y-4 max-w-md mx-auto w-full">

        <!-- ==================== TAB 1: POS (BÁN HÀNG) ==================== -->
        <section id="section-pos" class="tab-content block space-y-3">
            <div class="bg-white p-3 rounded-2xl shadow-sm border border-slate-150 space-y-2.5">
                <div class="relative">
                    <span class="absolute inset-y-0 left-0 flex items-center pl-3 text-slate-400">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4" viewBox="0 0 20 20" fill="currentColor">
                            <path fill-rule="evenodd" d="M8 4a4 4 0 100 8 4 4 0 000-8zM2 8a6 6 0 1110.89 3.476l4.817 4.817a1 1 0 01-1.414 1.414l-4.816-4.816A6 6 0 012 8z" clip-rule="evenodd" />
                        </svg>
                    </span>
                    <input type="text" id="pos-search" oninput="renderPosProducts()" placeholder="Tìm tên điện thoại, hãng sản xuất..." class="w-full pl-9 pr-3 py-2 border border-slate-200 rounded-xl focus:outline-none focus:ring-2 focus:ring-emerald-500 text-sm">
                </div>
                <div class="flex space-x-2 overflow-x-auto no-scrollbar py-0.5" id="pos-category-filters">
                    <!-- Dynamic loaded -->
                </div>
            </div>

            <div class="grid grid-cols-2 gap-2.5" id="pos-products-grid">
                <!-- Dynamic cards -->
            </div>
        </section>

        <!-- ==================== TAB 2: PRODUCTS (KHO HÀNG) ==================== -->
        <section id="section-products" class="tab-content hidden space-y-3">
            <div class="flex justify-between items-center">
                <div>
                    <h2 class="text-lg font-bold text-slate-800">Danh mục sản phẩm</h2>
                    <p class="text-[11px] text-slate-400">Theo dõi định mức kho hàng</p>
                </div>
                <button onclick="openProductModal(false)" id="btn-add-product" class="bg-emerald-600 hover:bg-emerald-700 text-white font-bold p-2.5 rounded-xl shadow-md">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
                        <path fill-rule="evenodd" d="M10 5a1 1 0 011 1v3h3a1 1 0 110 2h-3v3a1 1 0 11-2 0v-3H6a1 1 0 110-2h3V6a1 1 0 011-1z" clip-rule="evenodd" />
                    </svg>
                </button>
            </div>

            <div class="bg-white p-3 rounded-2xl shadow-sm border border-slate-100 flex flex-col gap-2">
                <input type="text" id="product-list-search" oninput="renderProductTable()" placeholder="Tìm nhanh theo tên, mã máy..." class="w-full px-3 py-2 border border-slate-200 rounded-xl text-sm focus:outline-none focus:ring-2 focus:ring-emerald-500">
                <div class="grid grid-cols-2 gap-2">
                    <select id="product-list-category" onchange="renderProductTable()" class="py-1.5 px-2 border border-slate-200 rounded-lg text-xs bg-white focus:outline-none">
                        <option value="all">Tất cả hãng</option>
                        <option value="iPhone">iPhone</option>
                        <option value="Samsung">Samsung</option>
                        <option value="Oppo">Oppo</option>
                        <option value="Xiaomi">Xiaomi</option>
                        <option value="realme">realme</option>
                        <option value="Phụ kiện">Phụ kiện</option>
                    </select>
                    <select id="product-list-status" onchange="renderProductTable()" class="py-1.5 px-2 border border-slate-200 rounded-lg text-xs bg-white focus:outline-none">
                        <option value="all">Tất cả tồn kho</option>
                        <option value="low-stock">Sắp hết hàng</option>
                        <option value="out-of-stock">Hết hàng (Tồn = 0)</option>
                    </select>
                </div>
            </div>

            <div class="space-y-2.5" id="product-cards-container">
                <!-- Dynamic cards -->
            </div>
        </section>

        <!-- ==================== TAB 3: INVOICES (ĐƠN HÀNG) ==================== -->
        <section id="section-invoices" class="tab-content hidden space-y-3">
            <div>
                <h2 class="text-lg font-bold text-slate-800">Nhật ký bán hàng</h2>
                <p class="text-[11px] text-slate-400">Xem lại hóa đơn và thông tin thanh toán</p>
            </div>

            <div class="bg-white p-3 rounded-2xl shadow-sm border border-slate-100">
                <input type="text" id="invoice-search" oninput="renderInvoiceTable()" placeholder="Tìm theo mã hóa đơn, tên khách..." class="w-full px-3 py-2 border border-slate-200 rounded-xl text-sm focus:outline-none focus:ring-2 focus:ring-emerald-500">
            </div>

            <div class="space-y-2.5" id="invoice-cards-container">
                <!-- Dynamic cards -->
            </div>
        </section>

        <!-- ==================== TAB 4: CUSTOMERS (KHÁCH HÀNG) ==================== -->
        <section id="section-customers" class="tab-content hidden space-y-3">
            <div class="flex justify-between items-center">
                <div>
                    <h2 class="text-lg font-bold text-slate-800">Danh mục khách hàng</h2>
                    <p class="text-[11px] text-slate-400">Bảo mật thông tin đối tác mua sắm</p>
                </div>
                <button onclick="openAddCustomerModal(false)" class="bg-emerald-600 hover:bg-emerald-700 text-white font-bold p-2.5 rounded-xl shadow-md">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
                        <path d="M8 9a3 3 0 100-6 3 3 0 000 6zM8 11a6 6 0 016 6H2a6 6 0 016-6zM16 7a1 1 0 10-2 0v1h-1a1 1 0 100 2h1v1a1 1 0 102 0v-1h1a1 1 0 100-2h-1V7z" />
                    </svg>
                </button>
            </div>

            <div class="bg-white p-3 rounded-2xl shadow-sm border border-slate-100">
                <input type="text" id="customer-search" oninput="renderCustomerTable()" placeholder="Nhập tên, số điện thoại..." class="w-full px-3 py-2 border border-slate-200 rounded-xl text-sm focus:outline-none focus:ring-2 focus:ring-emerald-500">
            </div>

            <div class="space-y-2.5" id="customer-cards-container">
                <!-- Dynamic cards -->
            </div>
        </section>

        <!-- ==================== TAB 5: STAFFS (NHÂN VIÊN) ==================== -->
        <section id="section-staffs" class="tab-content hidden space-y-3">
            <div class="flex justify-between items-center">
                <div>
                    <h2 class="text-lg font-bold text-slate-800">Danh sách nhân sự</h2>
                    <p class="text-[11px] text-slate-400">Quản lý phân quyền tài khoản</p>
                </div>
                <button onclick="openStaffModal(false)" class="bg-emerald-600 hover:bg-emerald-700 text-white font-bold p-2.5 rounded-xl shadow-md">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
                        <path fill-rule="evenodd" d="M10 9a3 3 0 100-6 3 3 0 000 6zm-7 9a7 7 0 1114 0H3z" clip-rule="evenodd" />
                    </svg>
                </button>
            </div>

            <div class="space-y-2.5" id="staff-cards-container">
                <!-- Dynamic cards -->
            </div>
        </section>

        <!-- ==================== TAB 6: REPORTS & ADVANCED ANALYTICS (BÁO CÁO) ==================== -->
        <section id="section-reports" class="tab-content hidden space-y-4">
            <div>
                <h2 class="text-lg font-bold text-slate-800">Phân tích kinh doanh</h2>
                <p class="text-[11px] text-slate-400">Xem doanh thu, lợi nhuận đa chiều</p>
            </div>

            <!-- TIME FILTER TABS (Doanh thu ngày, tuần, tháng, năm) -->
            <div class="bg-slate-200/80 p-1 rounded-2xl flex gap-1 text-center shrink-0 overflow-x-auto no-scrollbar">
                <button onclick="setReportPeriod('day')" id="btn-period-day" class="flex-1 py-2 px-2 text-xs font-bold rounded-xl transition-all bg-white text-emerald-700 shadow-sm whitespace-nowrap">Hôm nay</button>
                <button onclick="setReportPeriod('week')" id="btn-period-week" class="flex-1 py-2 px-2 text-xs font-bold rounded-xl transition-all text-slate-600 whitespace-nowrap">Tuần này</button>
                <button onclick="setReportPeriod('month')" id="btn-period-month" class="flex-1 py-2 px-2 text-xs font-bold rounded-xl transition-all text-slate-600 whitespace-nowrap">Theo tháng</button>
                <button onclick="setReportPeriod('year')" id="btn-period-year" class="flex-1 py-2 px-2 text-xs font-bold rounded-xl transition-all text-slate-600 whitespace-nowrap">Năm nay</button>
            </div>

            <!-- Custom Month Picker (Hidden by default) -->
            <div id="month-picker-container" class="hidden flex justify-between items-center bg-emerald-50/50 p-2.5 rounded-xl border border-emerald-100">
                <label for="custom-month-selector" class="text-xs font-extrabold text-emerald-800 uppercase tracking-wide">Chọn tháng báo cáo:</label>
                <input type="month" id="custom-month-selector" value="2026-06" onchange="renderReportDashboard()" class="px-3 py-1.5 border border-emerald-200 rounded-lg text-sm font-bold text-slate-800 bg-white focus:outline-none focus:ring-2 focus:ring-emerald-500 shadow-sm">
            </div>

            <!-- Stats Grid -->
            <div class="grid grid-cols-2 gap-2.5">
                <div class="bg-white p-3.5 rounded-2xl border border-slate-100 shadow-sm space-y-1">
                    <span class="text-[9px] text-slate-400 font-extrabold uppercase tracking-wide block">Doanh Thu</span>
                    <span class="text-base font-extrabold text-slate-850" id="stat-revenue">0 ₫</span>
                </div>
                <div class="bg-white p-3.5 rounded-2xl border border-slate-100 shadow-sm space-y-1">
                    <span class="text-[9px] text-slate-400 font-extrabold uppercase tracking-wide block">Lợi Nhuận Gộp</span>
                    <span class="text-base font-extrabold text-emerald-600" id="stat-profit">0 ₫</span>
                </div>
                <div class="bg-white p-3.5 rounded-2xl border border-slate-100 shadow-sm space-y-1">
                    <span class="text-[9px] text-slate-400 font-extrabold uppercase tracking-wide block">Tổng Đơn Hàng</span>
                    <span class="text-base font-extrabold text-slate-800" id="stat-orders">0 Đơn</span>
                </div>
                <div class="bg-white p-3.5 rounded-2xl border border-slate-100 shadow-sm space-y-1">
                    <span class="text-[9px] text-slate-400 font-extrabold uppercase tracking-wide block">Sản Phẩm Đã Bán</span>
                    <span class="text-base font-extrabold text-blue-600" id="stat-qty-sold">0 Máy</span>
                </div>
            </div>

            <!-- Chart Card -->
            <div class="bg-white p-4 rounded-2xl border border-slate-100 shadow-sm">
                <div class="flex justify-between items-center mb-3">
                    <h3 class="text-xs font-extrabold text-slate-800 uppercase tracking-wide">Xu hướng doanh thu</h3>
                    <span class="text-[9px] bg-emerald-50 text-emerald-600 px-2 py-0.5 rounded-full font-bold" id="chart-period-lbl">Hôm nay</span>
                </div>
                <div class="h-48 w-full relative">
                    <canvas id="revenueChart"></canvas>
                </div>
            </div>

            <!-- Top Products Performance Card -->
            <div class="bg-white p-4 rounded-2xl border border-slate-100 shadow-sm space-y-3">
                <div class="flex justify-between items-center pb-2 border-b border-slate-50">
                    <h3 class="text-xs font-extrabold text-slate-800 uppercase tracking-wide">Sản phẩm bán chạy nhất</h3>
                    <span class="text-[10px] text-slate-400 font-bold">Số lượng máy</span>
                </div>
                <!-- Dynamic progress bars showing top products -->
                <div class="space-y-3" id="top-products-progress-container">
                    <!-- Loaded dynamically -->
                </div>
            </div>
        </section>

    </main>

    <!-- ==================== FLOATING MOBILE CART BAR ==================== -->
    <div id="floating-cart-bar" class="fixed bottom-[80px] left-4 right-4 z-40 hidden">
        <button onclick="toggleCartSheet(true)" class="w-full bg-amber-500 hover:bg-amber-600 active:scale-[0.98] text-white py-3 px-4 rounded-2xl shadow-xl flex justify-between items-center transition-all">
            <div class="flex items-center space-x-2">
                <div class="bg-amber-600 text-white w-6 h-6 rounded-full flex items-center justify-center text-xs font-black" id="cart-floating-count">0</div>
                <span class="text-sm font-bold">Xem giỏ hàng chờ thanh toán</span>
            </div>
            <div class="flex items-center space-x-1 font-black text-sm">
                <span id="cart-floating-total">0 ₫</span>
                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5l7 7-7 7" />
                </svg>
            </div>
        </button>
    </div>

    <!-- ==================== GEMINI AI FLOATING BUTTON ==================== -->
    <div id="gemini-ai-fab" class="fixed bottom-20 right-4 z-40 hidden transition-all duration-300">
        <button onclick="toggleAiChatSheet(true)" class="w-14 h-14 bg-gradient-to-tr from-indigo-500 via-purple-600 to-pink-500 rounded-full shadow-lg hover:shadow-2xl active:scale-90 flex items-center justify-center text-white relative group border-2 border-white">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-7 w-7 animate-pulse" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="2.5">
                <path stroke-linecap="round" stroke-linejoin="round" d="M9.813 15.904L9 21l3.586-3.586m0 0a9 9 0 114.242-4.242l-4.242 4.242z" />
            </svg>
            <span class="absolute -top-1 -right-1 flex h-4.5 w-4.5">
                <span class="animate-ping absolute inline-flex h-full w-full rounded-full bg-pink-400 opacity-75"></span>
                <span class="relative inline-flex rounded-full h-4.5 w-4.5 bg-pink-500 border border-white text-[9px] font-bold text-white items-center justify-center">AI</span>
            </span>
        </button>
    </div>

    <!-- ==================== BOTTOM NAVIGATION BAR ==================== -->
    <nav class="fixed bottom-0 left-0 right-0 bg-white border-t border-slate-150 shadow-lg px-1 py-1 flex justify-around items-center z-45 max-w-md mx-auto" id="bottom-navigation-bar">
        <!-- Default buttons populated dynamically based on Roles -->
    </nav>

    <!-- ==================== MOBILE CART BOTTOM SHEET ==================== -->
    <div id="cart-bottom-sheet" class="fixed inset-0 bg-slate-950/60 backdrop-blur-sm z-50 flex flex-col justify-end hidden">
        <div class="flex-1" onclick="toggleCartSheet(false)"></div>
        <div class="bg-white rounded-t-3xl max-h-[85vh] flex flex-col shadow-2xl w-full max-w-md mx-auto slide-up">
            <div class="w-12 h-1 bg-slate-200 rounded-full mx-auto my-3 shrink-0"></div>
            
            <div class="px-5 pb-3 border-b border-slate-100 flex justify-between items-center shrink-0">
                <h3 class="font-extrabold text-base text-slate-800">
                    Giỏ hàng bán lẻ (<span id="pos-cart-count">0</span>)
                </h3>
                <button onclick="clearCart()" class="text-xs font-bold text-red-500">Xóa giỏ</button>
            </div>

            <div class="flex-1 overflow-y-auto px-5 py-2 divide-y divide-slate-100" id="pos-cart-container">
                <!-- Dynamically loaded -->
            </div>

            <div class="bg-slate-50 p-5 border-t border-slate-100 space-y-3 shrink-0 text-sm">
                <div>
                    <label class="block text-[10px] font-bold text-slate-400 uppercase tracking-wide mb-1">Khách hàng nhận hàng</label>
                    <div class="flex items-center space-x-2">
                        <select id="pos-customer-select" class="flex-1 py-2 px-3 rounded-xl border border-slate-200 bg-white text-xs font-bold focus:outline-none">
                            <!-- Dynamic customers options -->
                        </select>
                        <button onclick="toggleCartSheet(false); openAddCustomerModal(true);" class="bg-emerald-50 text-emerald-600 p-2 rounded-xl border border-emerald-100">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
                                <path d="M8 9a3 3 0 100-6 3 3 0 000 6zM8 11a6 6 0 016 6H2a6 6 0 016-6zM16 7a1 1 0 10-2 0v1h-1a1 1 0 100 2h1v1a1 1 0 102 0v-1h1a1 1 0 100-2h-1V7z" />
                            </svg>
                        </button>
                    </div>
                </div>

                <div class="grid grid-cols-2 gap-3">
                    <div>
                        <label class="block text-[10px] font-bold text-slate-400 uppercase tracking-wide mb-1">Chiết khấu (%)</label>
                        <input type="number" id="pos-discount" min="0" max="100" value="0" oninput="calcCartTotals()" class="w-full border border-slate-200 rounded-xl py-1.5 px-3 focus:outline-none text-center font-bold text-slate-800">
                    </div>
                    <div>
                        <label class="block text-[10px] font-bold text-slate-400 uppercase tracking-wide mb-1">Phương thức</label>
                        <select id="pos-payment" class="w-full border border-slate-200 rounded-xl py-1.5 px-3 bg-white font-bold text-slate-800">
                            <option value="Tiền mặt">Tiền mặt</option>
                            <option value="Chuyển khoản">Chuyển khoản</option>
                            <option value="Quẹt thẻ">Quẹt thẻ</option>
                        </select>
                    </div>
                </div>

                <div class="flex justify-between items-center pt-3 border-t border-dashed border-slate-200">
                    <div>
                        <span class="text-[10px] text-slate-400 font-bold block">Tổng tiền</span>
                        <span id="pos-subtotal" class="text-xs text-slate-400 line-through">0 ₫</span>
                    </div>
                    <span id="pos-total-amount" class="text-xl font-black text-emerald-600">0 ₫</span>
                </div>

                <button onclick="checkoutCart()" class="w-full bg-emerald-600 hover:bg-emerald-700 text-white font-bold py-3.5 px-4 rounded-2xl shadow-lg transition-all flex justify-center items-center space-x-2">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
                        <path fill-rule="evenodd" d="M16.707 5.293a1 1 0 010 1.414l-8 8a1 1 0 01-1.414 0l-4-4a1 1 0 011.414-1.414L8 12.586l7.293-7.293a1 1 0 011.414 0z" clip-rule="evenodd" />
                    </svg>
                    <span>Thanh Toán & Xuất Phiếu</span>
                </button>
            </div>
        </div>
    </div>

    <!-- ==================== GEMINI AI CHAT BOTTOM SHEET ==================== -->
    <div id="ai-chat-sheet" class="fixed inset-0 bg-slate-950/60 backdrop-blur-sm z-50 flex flex-col justify-end hidden">
        <div class="flex-1" onclick="toggleAiChatSheet(false)"></div>
        <div class="bg-slate-50 rounded-t-3xl h-[88vh] flex flex-col shadow-2xl w-full max-w-md mx-auto slide-up overflow-hidden border border-slate-200">
            <!-- AI Header -->
            <div class="bg-gradient-to-r from-indigo-600 via-purple-600 to-pink-600 px-5 py-3.5 flex justify-between items-center shrink-0 shadow-md">
                <div class="flex items-center space-x-3">
                    <div class="bg-white/10 p-2 rounded-2xl border border-white/20">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5 text-white animate-pulse" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2.5" d="M13 10V3L4 14h7v7l9-11h-7z" />
                        </svg>
                    </div>
                    <div>
                        <h3 class="font-extrabold text-sm text-white leading-tight">KiotPro AI Assistant 3.0</h3>
                        <p class="text-[9px] text-pink-100 font-medium">Auto-Cart & Phân Tích Dữ Liệu Thời Gian Thực</p>
                    </div>
                </div>
                <div class="flex items-center space-x-2">
                    <button onclick="clearAiChatHistory()" class="text-[10px] text-white/75 hover:text-white bg-white/10 px-2 py-1 rounded-lg transition-colors font-semibold">
                        Xóa lịch sử
                    </button>
                    <button onclick="toggleAiChatSheet(false)" class="text-white/80 hover:text-white bg-white/10 p-1.5 rounded-full transition-colors">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-4.5 w-4.5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2.5" d="M6 18L18 6M6 6l12 12" />
                        </svg>
                    </button>
                </div>
            </div>

            <!-- Chat Messages Area -->
            <div class="flex-1 overflow-y-auto px-4 py-4 space-y-4" id="ai-chat-messages">
                <!-- Welcome Message -->
                <div class="flex items-start space-x-2.5">
                    <div class="w-8 h-8 rounded-xl bg-gradient-to-br from-indigo-500 to-purple-600 flex items-center justify-center shrink-0 shadow-sm text-white text-xs font-bold">
                        AI
                    </div>
                    <div class="bg-white border border-slate-200/80 p-3.5 rounded-2xl rounded-tl-none shadow-sm text-xs text-slate-700 max-w-[85%] space-y-2">
                        <p>Xin chào! Mình là phiên bản <strong>AI 3.0</strong> được nâng cấp mạnh mẽ.</p>
                        <p class="font-semibold text-slate-800">Các tính năng mới nhất:</p>
                        <ul class="list-disc pl-4 space-y-1 text-slate-600 text-[11px]">
                            <li><strong>Phân tích báo cáo:</strong> "Hôm nay bán được bao nhiêu?", "Mở báo cáo tháng này".</li>
                            <li><strong>Quản lý kho:</strong> "Có máy nào bán chạy không?", "Còn máy nào sắp hết?".</li>
                            <li><strong>Tự động bán hàng:</strong> "Thêm iPhone 15 Pro Max vào giỏ hàng giúp mình".</li>
                        </ul>
                    </div>
                </div>
            </div>

            <!-- Suggestion Prompts (Quick Click) -->
            <div class="px-4 py-2 shrink-0 bg-slate-100/50 border-t border-slate-200 overflow-x-auto no-scrollbar flex space-x-1.5">
                <button onclick="sendQuickPrompt('Hôm nay bán được bao nhiêu doanh thu?')" class="px-3 py-1.5 bg-white border border-slate-200 hover:border-indigo-400 text-[10px] font-bold text-slate-700 rounded-full shrink-0 shadow-xs transition-colors">📊 Hỏi doanh thu nay</button>
                <button onclick="sendQuickPrompt('Thêm iPhone 15 Pro Max vào giỏ hàng')" class="px-3 py-1.5 bg-white border border-slate-200 hover:border-indigo-400 text-[10px] font-bold text-slate-700 rounded-full shrink-0 shadow-xs transition-colors">🛒 Bán 1 iPhone 15 PM</button>
                <button onclick="sendQuickPrompt('Mở báo cáo tháng này')" class="px-3 py-1.5 bg-white border border-slate-200 hover:border-indigo-400 text-[10px] font-bold text-slate-700 rounded-full shrink-0 shadow-xs transition-colors">📈 Mở Báo cáo Tháng</button>
                <button onclick="sendQuickPrompt('Hiện tại cửa hàng đang có máy nào bán chạy nhất?')" class="px-3 py-1.5 bg-white border border-slate-200 hover:border-indigo-400 text-[10px] font-bold text-slate-700 rounded-full shrink-0 shadow-xs transition-colors">🔥 Hỏi máy bán chạy</button>
                <button onclick="sendQuickPrompt('Tìm các điện thoại dòng Samsung')" class="px-3 py-1.5 bg-white border border-slate-200 hover:border-indigo-400 text-[10px] font-bold text-slate-700 rounded-full shrink-0 shadow-xs transition-colors">📱 Lọc dòng Samsung</button>
            </div>

            <!-- Chat Input Area -->
            <div class="bg-white p-3 border-t border-slate-200 shrink-0">
                <form id="ai-chat-form" onsubmit="handleSendAiMessage(event)" class="flex items-center space-x-2 relative">
                    <input type="text" id="ai-chat-input" placeholder="Nhập yêu cầu hoặc câu hỏi cho AI..." autocomplete="off" class="flex-1 bg-slate-100 border-none rounded-full py-2.5 px-4 text-xs focus:outline-none focus:ring-2 focus:ring-indigo-500 text-slate-800 placeholder-slate-400">
                    <button type="submit" id="ai-send-btn" class="w-9 h-9 bg-indigo-600 text-white rounded-full flex items-center justify-center shadow-md hover:bg-indigo-700 active:scale-95 transition-all disabled:opacity-50">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-4.5 w-4.5 ml-0.5" viewBox="0 0 20 20" fill="currentColor">
                            <path d="M10.894 2.553a1 1 0 00-1.788 0l-7 14a1 1 0 001.169 1.409l5-1.429A1 1 0 009 15.571V11a1 1 0 112 0v4.571a1 1 0 00.725.962l5 1.428a1 1 0 001.17-1.408l-7-14z" />
                        </svg>
                    </button>
                </form>
            </div>
        </div>
    </div>

    <!-- ==================== MODAL 1: ADD/EDIT PRODUCT ==================== -->
    <div id="modal-product" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-50 flex items-end justify-center hidden">
        <div class="bg-white w-full max-w-md rounded-t-3xl shadow-xl overflow-hidden flex flex-col max-h-[90vh]">
            <div class="bg-emerald-600 text-white px-5 py-4 flex justify-between items-center shrink-0">
                <h3 class="font-bold text-base" id="product-modal-title">Thêm Sản Phẩm Mới</h3>
                <button onclick="closeProductModal()" class="text-white hover:text-slate-200">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
                    </svg>
                </button>
            </div>
            
            <form id="product-form" onsubmit="saveProduct(event)" class="p-5 space-y-4 overflow-y-auto">
                <input type="hidden" id="edit-product-id">
                
                <div class="grid grid-cols-2 gap-3">
                    <div>
                        <label class="block text-xs font-bold text-slate-500 mb-1">Mã sản phẩm *</label>
                        <input type="text" id="prod-code" required class="w-full p-2.5 border border-slate-200 rounded-xl focus:ring-1 focus:ring-emerald-500 text-sm focus:outline-none">
                    </div>
                    <div>
                        <label class="block text-xs font-bold text-slate-500 mb-1">Hãng / Nhóm *</label>
                        <select id="prod-category" required class="w-full p-2.5 border border-slate-200 rounded-xl bg-white text-sm focus:outline-none">
                            <option value="iPhone">iPhone (Apple)</option>
                            <option value="Samsung">Samsung</option>
                            <option value="Oppo">Oppo</option>
                            <option value="Xiaomi">Xiaomi</option>
                            <option value="realme">realme</option>
                            <option value="Phụ kiện">Phụ kiện</option>
                        </select>
                    </div>
                </div>

                <div>
                    <label class="block text-xs font-bold text-slate-500 mb-1">Tên điện thoại *</label>
                    <input type="text" id="prod-name" required placeholder="Ví dụ: iPhone 15 Pro Max 256GB" class="w-full p-2.5 border border-slate-200 rounded-xl focus:ring-1 focus:ring-emerald-500 text-sm focus:outline-none">
                </div>

                <div class="grid grid-cols-2 gap-3">
                    <div>
                        <label class="block text-xs font-bold text-slate-500 mb-1">Giá Vốn (Nhập) *</label>
                        <input type="number" id="prod-cost" required min="0" class="w-full p-2.5 border border-slate-200 rounded-xl text-sm focus:outline-none">
                    </div>
                    <div>
                        <label class="block text-xs font-bold text-slate-500 mb-1">Giá Bán Lẻ *</label>
                        <input type="number" id="prod-selling" required min="0" class="w-full p-2.5 border border-slate-200 rounded-xl text-sm focus:outline-none">
                    </div>
                </div>

                <div class="grid grid-cols-2 gap-3">
                    <div>
                        <label class="block text-xs font-bold text-slate-500 mb-1">Tồn kho ban đầu *</label>
                        <input type="number" id="prod-stock" required min="0" class="w-full p-2.5 border border-slate-200 rounded-xl text-sm focus:outline-none">
                    </div>
                    <div>
                        <label class="block text-xs font-bold text-slate-500 mb-1">Mức tồn báo tối thiểu *</label>
                        <input type="number" id="prod-minStock" required min="1" class="w-full p-2.5 border border-slate-200 rounded-xl text-sm focus:outline-none">
                    </div>
                </div>

                <div class="pt-2 flex space-x-2">
                    <button type="button" onclick="closeProductModal()" class="w-1/2 bg-slate-100 text-slate-700 py-3 rounded-xl font-bold text-sm">Hủy bỏ</button>
                    <button type="submit" class="w-1/2 bg-emerald-600 text-white py-3 rounded-xl font-bold text-sm shadow">Lưu sản phẩm</button>
                </div>
            </form>
        </div>
    </div>

    <!-- ==================== MODAL 2: ADD CUSTOMER ==================== -->
    <div id="modal-customer" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-50 flex items-end justify-center hidden">
        <div class="bg-white w-full max-w-md rounded-t-3xl shadow-xl overflow-hidden">
            <div class="bg-emerald-600 text-white px-5 py-4 flex justify-between items-center">
                <h3 class="font-bold text-base" id="customer-modal-title">Thêm Khách Hàng</h3>
                <button onclick="closeCustomerModal()" class="text-white hover:text-slate-200">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
                    </svg>
                </button>
            </div>
            
            <form id="customer-form" onsubmit="saveCustomer(event)" class="p-5 space-y-4">
                <input type="hidden" id="edit-customer-id">
                <input type="hidden" id="is-pos-adding" value="false">

                <div>
                    <label class="block text-xs font-bold text-slate-500 mb-1">Mã khách hàng *</label>
                    <input type="text" id="cust-code" required class="w-full p-2.5 border border-slate-200 rounded-xl text-sm focus:outline-none">
                </div>

                <div>
                    <label class="block text-xs font-bold text-slate-500 mb-1">Tên khách hàng *</label>
                    <input type="text" id="cust-name" required class="w-full p-2.5 border border-slate-200 rounded-xl text-sm focus:outline-none">
                </div>

                <div>
                    <label class="block text-xs font-bold text-slate-500 mb-1">Số điện thoại *</label>
                    <input type="tel" id="cust-phone" required class="w-full p-2.5 border border-slate-200 rounded-xl text-sm focus:outline-none">
                </div>

                <div>
                    <label class="block text-xs font-bold text-slate-500 mb-1">Địa chỉ giao dịch</label>
                    <input type="text" id="cust-address" class="w-full p-2.5 border border-slate-200 rounded-xl text-sm focus:outline-none">
                </div>

                <div class="pt-2 flex space-x-2">
                    <button type="button" onclick="closeCustomerModal()" class="w-1/2 bg-slate-100 text-slate-700 py-3 rounded-xl font-bold text-sm">Hủy bỏ</button>
                    <button type="submit" class="w-1/2 bg-emerald-600 text-white py-3 rounded-xl font-bold text-sm shadow">Lưu lại</button>
                </div>
            </form>
        </div>
    </div>

    <!-- ==================== MODAL 3: ADD/EDIT STAFF ==================== -->
    <div id="modal-staff" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-50 flex items-end justify-center hidden">
        <div class="bg-white w-full max-w-md rounded-t-3xl shadow-xl overflow-hidden">
            <div class="bg-emerald-600 text-white px-5 py-4 flex justify-between items-center">
                <h3 class="font-bold text-base" id="staff-modal-title">Thêm Nhân Viên Mới</h3>
                <button onclick="closeStaffModal()" class="text-white hover:text-slate-200">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
                    </svg>
                </button>
            </div>
            
            <form id="staff-form" onsubmit="saveStaff(event)" class="p-5 space-y-4">
                <input type="hidden" id="edit-staff-id">

                <div>
                    <label class="block text-xs font-bold text-slate-500 mb-1">Mã nhân sự *</label>
                    <input type="text" id="staff-code" required class="w-full p-2.5 border border-slate-200 rounded-xl text-sm focus:outline-none">
                </div>

                <div>
                    <label class="block text-xs font-bold text-slate-500 mb-1">Họ và Tên nhân viên *</label>
                    <input type="text" id="staff-name" required class="w-full p-2.5 border border-slate-200 rounded-xl text-sm focus:outline-none">
                </div>

                <div class="grid grid-cols-2 gap-3">
                    <div>
                        <label class="block text-xs font-bold text-slate-500 mb-1">Tài khoản đăng nhập *</label>
                        <input type="text" id="staff-username" required class="w-full p-2.5 border border-slate-200 rounded-xl text-sm focus:outline-none">
                    </div>
                    <div>
                        <label class="block text-xs font-bold text-slate-500 mb-1">Mật khẩu đăng nhập *</label>
                        <input type="password" id="staff-password" required class="w-full p-2.5 border border-slate-200 rounded-xl text-sm focus:outline-none">
                    </div>
                </div>

                <div>
                    <label class="block text-xs font-bold text-slate-500 mb-1">Quyền hạn truy cập *</label>
                    <select id="staff-role" required class="w-full p-2.5 border border-slate-200 rounded-xl bg-white text-sm focus:outline-none">
                        <option value="Staff">Staff (Nhân viên bán lẻ)</option>
                        <option value="Admin">Admin (Quản trị viên hệ thống)</option>
                    </select>
                </div>

                <div class="pt-2 flex space-x-2">
                    <button type="button" onclick="closeStaffModal()" class="w-1/2 bg-slate-100 text-slate-700 py-3 rounded-xl font-bold text-sm">Hủy</button>
                    <button type="submit" class="w-1/2 bg-emerald-600 text-white py-3 rounded-xl font-bold text-sm shadow">Lưu nhân sự</button>
                </div>
            </form>
        </div>
    </div>

    <!-- ==================== MODAL 4: RECEIPT VIEWER ==================== -->
    <div id="modal-receipt" class="fixed inset-0 bg-slate-900/60 backdrop-blur-sm z-50 flex items-center justify-center p-3.5 hidden">
        <div class="bg-white w-full max-w-sm rounded-2xl shadow-xl overflow-hidden flex flex-col max-h-[90vh]">
            <div class="bg-slate-800 text-white px-4 py-3 flex justify-between items-center shrink-0">
                <h3 class="font-bold text-sm flex items-center">
                    Hóa Đơn Bán Hàng #<span id="receipt-invoice-code">HD000</span>
                </h3>
                <button onclick="closeReceiptModal()" class="text-slate-400 hover:text-white">
                    <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                        <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12" />
                    </svg>
                </button>
            </div>

            <!-- Scroll content -->
            <div class="flex-1 overflow-y-auto p-4 bg-amber-50/20">
                <div class="bg-white border border-slate-200 rounded-xl p-4 shadow-sm space-y-3 text-xs" id="printable-receipt-content">
                    <div class="text-center space-y-0.5">
                        <h4 class="font-bold text-sm text-emerald-600 uppercase">KiotPro Mobile Store</h4>
                        <p class="text-[10px] text-slate-400">120 Hai Bà Trưng, Quận 1, TP. HCM</p>
                    </div>

                    <div class="border-t border-dashed border-slate-300"></div>

                    <div class="space-y-1 text-slate-600 text-[11px]">
                        <div class="flex justify-between">
                            <span>Mã hóa đơn:</span>
                            <span class="font-bold text-slate-800" id="receipt-id">HD0000</span>
                        </div>
                        <div class="flex justify-between">
                            <span>Thời gian:</span>
                            <span id="receipt-date">--:--</span>
                        </div>
                        <div class="flex justify-between">
                            <span>Khách hàng:</span>
                            <span class="font-bold text-slate-800" id="receipt-customer-name">Khách lẻ</span>
                        </div>
                        <div class="flex justify-between">
                            <span>Người lập phiếu:</span>
                            <span class="font-bold text-emerald-700" id="receipt-seller">Admin</span>
                        </div>
                    </div>

                    <div class="border-t border-dashed border-slate-300"></div>

                    <div class="space-y-2" id="receipt-items-container">
                        <!-- Loaded dynamically -->
                    </div>

                    <div class="border-t border-dashed border-slate-300"></div>

                    <div class="space-y-1 text-[11px]">
                        <div class="flex justify-between text-slate-500">
                            <span>Tổng tiền hàng:</span>
                            <span id="receipt-subtotal">0 ₫</span>
                        </div>
                        <div class="flex justify-between text-slate-500">
                            <span>Chiết khấu:</span>
                            <span id="receipt-discount">-0 ₫</span>
                        </div>
                        <div class="flex justify-between font-bold text-sm text-slate-800 pt-1.5 border-t border-slate-100">
                            <span>Thanh toán thực tế:</span>
                            <span class="text-emerald-600" id="receipt-total-amount">0 ₫</span>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Footer actions -->
            <div class="bg-slate-50 px-4 py-3 border-t border-slate-100 flex space-x-2 shrink-0">
                <button onclick="closeReceiptModal()" class="w-full bg-slate-200 text-slate-700 font-bold py-2.5 rounded-xl text-xs">Đóng lại</button>
            </div>
        </div>
    </div>

    <!-- TOAST CONTAINER -->
    <div id="toast-container" class="fixed bottom-[110px] left-4 right-4 space-y-2 z-50 pointer-events-none max-w-sm mx-auto"></div>

    <script>
        // MOCK USERS & DATA (REALISTIC June 2026 SETUPS)
        const defaultStaffs = [
            { id: "s1", code: "NV001", name: "Nguyễn Minh Quân", username: "nhanvien", password: "123", role: "Staff" },
            { id: "admin", code: "ADMIN", name: "Trần Hoàng Long (Quản lý)", username: "admin", password: "123", role: "Admin" }
        ];

        const defaultProducts = [
            { id: "p1", code: "DT001", name: "iPhone 15 Pro Max 256GB", category: "iPhone", costPrice: 27000000, sellingPrice: 29490000, stock: 12, minStock: 3 },
            { id: "p2", code: "DT002", name: "Samsung Galaxy S24 Ultra 512GB", category: "Samsung", costPrice: 25000000, sellingPrice: 27890000, stock: 8, minStock: 2 },
            { id: "p3", code: "DT003", name: "iPhone 13 128GB", category: "iPhone", costPrice: 12500000, sellingPrice: 13990000, stock: 18, minStock: 4 },
            { id: "p4", code: "DT004", name: "Oppo Reno11 5G", category: "Oppo", costPrice: 8000000, sellingPrice: 9190000, stock: 5, minStock: 3 },
            { id: "p5", code: "DT005", name: "Xiaomi Redmi Note 13", category: "Xiaomi", costPrice: 4000000, sellingPrice: 4690000, stock: 22, minStock: 5 },
            { id: "p6", code: "DT006", name: "iPhone 15 128GB", category: "iPhone", costPrice: 18000000, sellingPrice: 19990000, stock: 2, minStock: 3 }
        ];

        const defaultCustomers = [
            { id: "c1", code: "KH001", name: "Phan Anh Tuấn", phone: "0901234567", address: "Quận 3, TP. Hồ Chí Minh", totalSpent: 43480000 },
            { id: "c2", code: "KH002", name: "Trần Thị Ánh Tuyết", phone: "0987654321", address: "Hoàn Kiếm, Hà Nội", totalSpent: 13990000 },
            { id: "c_guest", code: "KHACH_LE", name: "Khách lẻ (Khách vãng lai)", phone: "-", address: "-", totalSpent: 0 }
        ];

        // Default Invoices with historical spread in 2026
        const defaultInvoices = [
            // Today June 3, 2026
            { 
                id: "inv1", code: "HD5320", date: "2026-06-03 10:30", customerId: "c1", customerName: "Phan Anh Tuấn", customerPhone: "0901234567", paymentMethod: "Chuyển khoản",
                items: [{ id: "p1", code: "DT001", name: "iPhone 15 Pro Max 256GB", sellingPrice: 29490000, quantity: 1, costPrice: 27000000 }],
                subtotal: 29490000, discount: 0, totalAmount: 29490000, costTotal: 27000000, profit: 2490000, seller: "Trần Hoàng Long (Quản lý)"
            },
            { 
                id: "inv2", code: "HD5321", date: "2026-06-03 14:15", customerId: "c_guest", customerName: "Khách lẻ", customerPhone: "-", paymentMethod: "Tiền mặt",
                items: [{ id: "p5", code: "DT005", name: "Xiaomi Redmi Note 13", sellingPrice: 4690000, quantity: 2, costPrice: 4000000 }],
                subtotal: 9380000, discount: 0, totalAmount: 9380000, costTotal: 8000000, profit: 1380000, seller: "Nguyễn Minh Quân (NV001)"
            },
            // Earlier this week
            { 
                id: "inv3", code: "HD5318", date: "2026-06-01 11:20", customerId: "c2", customerName: "Trần Thị Ánh Tuyết", customerPhone: "0987654321", paymentMethod: "Quẹt thẻ",
                items: [{ id: "p3", code: "DT003", name: "iPhone 13 128GB", sellingPrice: 13990000, quantity: 1, costPrice: 12500000 }],
                subtotal: 13990000, discount: 0, totalAmount: 13990000, costTotal: 12500000, profit: 1490000, seller: "Nguyễn Minh Quân (NV001)"
            },
            // Last Month (May 2026)
            { 
                id: "inv4", code: "HD5299", date: "2026-05-18 16:45", customerId: "c1", customerName: "Phan Anh Tuấn", customerPhone: "0901234567", paymentMethod: "Chuyển khoản",
                items: [{ id: "p2", code: "DT002", name: "Samsung Galaxy S24 Ultra 512GB", sellingPrice: 27890000, quantity: 1, costPrice: 25000000 }],
                subtotal: 27890000, discount: 0, totalAmount: 27890000, costTotal: 25000000, profit: 2890000, seller: "Trần Hoàng Long (Quản lý)"
            },
            { 
                id: "inv5", code: "HD5288", date: "2026-05-02 09:30", customerId: "c_guest", customerName: "Khách lẻ", customerPhone: "-", paymentMethod: "Tiền mặt",
                items: [
                    { id: "p5", code: "DT005", name: "Xiaomi Redmi Note 13", sellingPrice: 4690000, quantity: 3, costPrice: 4000000 },
                    { id: "p4", code: "DT004", name: "Oppo Reno11 5G", sellingPrice: 9190000, quantity: 2, costPrice: 8000000 }
                ],
                subtotal: 32450000, discount: 5, totalAmount: 30827500, costTotal: 28000000, profit: 2827500, seller: "Trần Hoàng Long (Quản lý)"
            },
            // Earlier this year (Feb 2026)
            { 
                id: "inv6", code: "HD5104", date: "2026-02-14 19:15", customerId: "c1", customerName: "Phan Anh Tuấn", customerPhone: "0901234567", paymentMethod: "Quẹt thẻ",
                items: [{ id: "p1", code: "DT001", name: "iPhone 15 Pro Max 256GB", sellingPrice: 29490000, quantity: 1, costPrice: 27000000 }],
                subtotal: 29490000, discount: 0, totalAmount: 29490000, costTotal: 27000000, profit: 2490000, seller: "Nguyễn Minh Quân (NV001)"
            }
        ];

        let staffs = JSON.parse(localStorage.getItem("kiot_an_staffs")) || defaultStaffs;
        let products = JSON.parse(localStorage.getItem("kiot_an_products")) || defaultProducts;
        let customers = JSON.parse(localStorage.getItem("kiot_an_customers")) || defaultCustomers;
        let invoices = JSON.parse(localStorage.getItem("kiot_an_invoices")) || defaultInvoices;

        let currentUser = null;
        let cart = [];
        let currentSelectedCategory = "all";
        let reportPeriod = "day"; 
        
        // Chart object hooks
        let revenueChartObj = null;

        function saveState() {
            localStorage.setItem("kiot_an_staffs", JSON.stringify(staffs));
            localStorage.setItem("kiot_an_products", JSON.stringify(products));
            localStorage.setItem("kiot_an_customers", JSON.stringify(customers));
            localStorage.setItem("kiot_an_invoices", JSON.stringify(invoices));
        }

        // ==================== AUTH ENGINE ====================
        function handleLogin(event) {
            event.preventDefault();
            const u = document.getElementById("login-username").value.trim().toLowerCase();
            const p = document.getElementById("login-password").value.trim();

            const matched = staffs.find(s => s.username === u && s.password === p);
            if (matched) {
                currentUser = matched;
                document.getElementById("login-screen").classList.add("hidden");
                showToast(`Xin chào, ${matched.name}!`);

                document.getElementById("header-user-name").innerText = matched.name;
                document.getElementById("header-user-role").innerText = matched.role === "Admin" ? "Quản lý" : "Nhân viên";

                // Show AI Floating button
                document.getElementById("gemini-ai-fab").classList.remove("hidden");

                buildBottomNavigationBar();
                switchTab('pos');
            } else {
                showToast("Sai tài khoản hoặc mật khẩu!", "error");
            }
        }

        function handleLogout() {
            if (confirm("Xác nhận đăng xuất khỏi hệ thống?")) {
                currentUser = null;
                cart = [];
                aiChatHistory = []; // clear chat history
                renderCart();
                document.getElementById("login-username").value = "";
                document.getElementById("login-password").value = "";
                document.getElementById("login-screen").classList.remove("hidden");
                
                // Hide AI Floating button
                document.getElementById("gemini-ai-fab").classList.add("hidden");
                document.getElementById("ai-chat-sheet").classList.add("hidden");
                
                showToast("Đã đăng xuất.");
            }
        }

        // DYNAMIC ACCESS MANAGEMENT
        function buildBottomNavigationBar() {
            const nav = document.getElementById("bottom-navigation-bar");
            nav.innerHTML = "";

            let tabs = [
                { id: "pos", name: "Bán hàng", icon: `<svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor"><path d="M3 1a1 1 0 000 2h1.22l.305 1.222a.997.997 0 00.01.042l1.358 5.43-.893.892C3.74 11.846 4.532 13 5.619 13H9c1.105 0 2-.895 2-2V8h2v3c0 1.105.895 2 2 2h1.381c1.087 0 1.878-1.154 1.319-2.116l-2-3.46A1 1 0 0014 7h-3V3a1 1 0 00-2 0v4H7V3.586L5.707 2.293A1 1 0 005 2H3.82L3.516 1.18A1 1 0 003 1z" /><path d="M6 16a2 2 0 114 0 2 2 0 01-4 0zM14 16a2 2 0 114 0 2 2 0 01-4 0z" /></svg>` },
                { id: "products", name: "Kho", icon: `<svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor"><path fill-rule="evenodd" d="M10 2a8 8 0 100 16 8 8 0 000-16zM7 9a1 1 0 000 2h6a1 1 0 100-2H7z" clip-rule="evenodd" /></svg>` },
                { id: "invoices", name: "Hóa đơn", icon: `<svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor"><path fill-rule="evenodd" d="M4 4a2 2 0 012-2h4.586A1 1 0 0113 2.586L15.414 5A1 1 0 0116 5.586V16a2 2 0 01-2 2H6a2 2 0 01-2-2V4zm2 6a1 1 0 011-1h6a1 1 0 110 2H7a1 1 0 01-1-1zm1 3a1 1 0 100 2h6a1 1 0 100-2H7z" clip-rule="evenodd" /></svg>` },
                { id: "customers", name: "Khách", icon: `<svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor"><path d="M9 6a3 3 0 11-6 0 3 3 0 016 0zM17 6a3 3 0 11-6 0 3 3 0 006 0zM12.93 17c.046-.327.07-.66.07-1a6.97 6.97 0 00-1.5-4.33A5 5 0 0119 16v1h-6.07zM6 11a5 5 0 015 5v1H1v-1a5 5 0 015-5z" /></svg>` }
            ];

            if (currentUser && currentUser.role === "Admin") {
                tabs.push(
                    { id: "staffs", name: "Nhân viên", icon: `<svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor"><path d="M13 6a3 3 0 11-6 0 3 3 0 016 0zM18 8a2 2 0 11-4 0 2 2 0 014 0zM14 15a4 4 0 00-8 0v3h8v-3zM6 8a2 2 0 11-4 0 2 2 0 014 0zM16 18v-3a5.972 5.972 0 00-.75-2.906A3.005 3.005 0 0119 15v3h-3zM4.75 12.094A5.973 5.973 0 004 15v3H1v-3a3 3 0 013.75-2.906z" /></svg>` },
                    { id: "reports", name: "Báo cáo", icon: `<svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor"><path d="M2 10a8 8 0 018-8v8h8a8 8 0 11-16 0z" /><path d="M12 2.252A8.014 8.014 0 0117.748 8H12V2.252z" /></svg>` }
                );
            }

            tabs.forEach(t => {
                const btn = document.createElement("button");
                btn.id = `btn-tab-${t.id}`;
                btn.className = "mobile-nav-btn flex flex-col items-center justify-center w-12 py-1 text-slate-400 font-medium transition-all";
                btn.onclick = () => switchTab(t.id);
                btn.innerHTML = `
                    ${t.icon}
                    <span class="text-[9px] mt-0.5">${t.name}</span>
                `;
                nav.appendChild(btn);
            });
        }

        function switchTab(tabId) {
            if (!currentUser) return;
            if ((tabId === "staffs" || tabId === "reports") && currentUser.role !== "Admin") {
                showToast("Bạn không có quyền truy cập!", "warning");
                return;
            }

            document.querySelectorAll(".tab-content").forEach(el => el.classList.add("hidden"));
            const targetSec = document.getElementById(`section-${tabId}`);
            if (targetSec) { targetSec.classList.remove("hidden"); }

            document.querySelectorAll(".mobile-nav-btn").forEach(btn => {
                btn.classList.remove("text-emerald-600", "font-extrabold");
                btn.classList.add("text-slate-400", "font-medium");
            });
            const activeBtn = document.getElementById(`btn-tab-${tabId}`);
            if (activeBtn) {
                activeBtn.classList.remove("text-slate-400", "font-medium");
                activeBtn.classList.add("text-emerald-600", "font-extrabold");
            }

            // Hide add-product button for staff
            const addProductBtn = document.getElementById("btn-add-product");
            if (addProductBtn) {
                if (currentUser.role === "Admin") addProductBtn.classList.remove("hidden");
                else addProductBtn.classList.add("hidden");
            }

            // Reload data
            if (tabId === 'pos') {
                initPosScreen();
            } else if (tabId === 'products') {
                renderProductTable();
            } else if (tabId === 'invoices') {
                renderInvoiceTable();
            } else if (tabId === 'customers') {
                renderCustomerTable();
            } else if (tabId === 'staffs') {
                renderStaffTable();
            } else if (tabId === 'reports') {
                renderReportDashboard();
            }
        }

        // TOAST SYSTEM
        function showToast(message, type = "success") {
            const container = document.getElementById("toast-container");
            const toast = document.createElement("div");
            toast.className = "flex items-center space-x-2 px-4 py-3 rounded-2xl shadow-xl text-white font-semibold text-xs transition-all duration-300 transform translate-y-2 opacity-0 pointer-events-auto justify-center";
            
            if (type === "success") {
                toast.classList.add("bg-emerald-600");
                toast.innerHTML = `<svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 shrink-0" viewBox="0 0 20 20" fill="currentColor"><path fill-rule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zm3.707-9.293a1 1 0 00-1.414-1.414L9 10.586 7.707 9.293a1 1 0 00-1.414 1.414l2 2a1 1 0 001.414 0l4-4z" clip-rule="evenodd" /></svg><span>${message}</span>`;
            } else if (type === "warning") {
                toast.classList.add("bg-amber-500");
                toast.innerHTML = `<svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 shrink-0" viewBox="0 0 20 20" fill="currentColor"><path fill-rule="evenodd" d="M8.257 3.099c.765-1.36 2.722-1.36 3.486 0l5.58 9.92c.75 1.334-.213 2.98-1.742 2.98H4.42c-1.53 0-2.493-1.646-1.743-2.98l5.58-9.92zM11 13a1 1 0 11-2 0 1 1 0 012 0zm-1-8a1 1 0 00-1 1v3a1 1 0 002 0V6a1 1 0 00-1-1z" clip-rule="evenodd" /></svg><span>${message}</span>`;
            } else {
                toast.classList.add("bg-red-600");
                toast.innerHTML = `<svg xmlns="http://www.w3.org/2000/svg" class="h-4 w-4 shrink-0" viewBox="0 0 20 20" fill="currentColor"><path fill-rule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zM8.707 7.293a1 1 0 00-1.414 1.414L8.586 10l-1.293 1.293a1 1 0 101.414 1.414L10 11.414l1.293 1.293a1 1 0 001.414-1.414L11.414 10l1.293-1.293a1 1 0 00-1.414-1.414L10 8.586 8.707 7.293z" clip-rule="evenodd" /></svg><span>${message}</span>`;
            }

            container.appendChild(toast);
            setTimeout(() => { toast.classList.remove("translate-y-2", "opacity-0"); }, 10);
            setTimeout(() => {
                toast.classList.add("translate-y-2", "opacity-0");
                setTimeout(() => { toast.remove(); }, 300);
            }, 2500);
        }

        // MONEY CONVERSION
        function formatVND(amount) {
            return new Intl.NumberFormat('vi-VN', { style: 'currency', currency: 'VND' }).format(amount);
        }

        // ==================== TAB 1: POS SCREEN (BÁN HÀNG) ====================
        function initPosScreen() {
            renderPosCategories();
            renderPosProducts();
            populatePosCustomerDropdown();
            calcCartTotals();
        }

        function renderPosCategories() {
            const categories = ["all", "iPhone", "Samsung", "Oppo", "Xiaomi", "realme", "Phụ kiện"];
            const catContainer = document.getElementById("pos-category-filters");
            catContainer.innerHTML = "";

            categories.forEach(cat => {
                const isActive = currentSelectedCategory === cat;
                const display = cat === "all" ? "Tất cả" : cat;
                const btn = document.createElement("button");
                btn.className = `px-4 py-1.5 rounded-xl text-xs font-bold whitespace-nowrap transition-all border ${
                    isActive ? "bg-emerald-600 text-white border-emerald-600 shadow-sm" : "bg-slate-50 text-slate-600 border-slate-200"
                }`;
                btn.innerText = display;
                btn.onclick = () => {
                    currentSelectedCategory = cat;
                    renderPosCategories();
                    renderPosProducts();
                };
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
                grid.innerHTML = `<div class="col-span-full py-8 text-center text-slate-400 text-xs font-semibold">Không tìm thấy máy phù hợp</div>`;
                return;
            }

            filtered.forEach(p => {
                const isOutOfStock = p.stock <= 0;
                const card = document.createElement("div");
                card.className = `bg-white p-3 rounded-2xl border border-slate-200 flex flex-col justify-between space-y-2 relative ${
                    isOutOfStock ? "opacity-60 bg-slate-50 cursor-not-allowed" : "active:scale-[0.98] transition-all cursor-pointer hover:border-emerald-500"
                }`;
                
                if (!isOutOfStock) {
                    card.onclick = () => addToCart(p);
                }

                const badge = isOutOfStock 
                    ? `<span class="absolute top-1.5 right-1.5 bg-red-600 text-white text-[8px] px-1.5 py-0.5 rounded-full font-bold shadow-sm">HẾT VÀO</span>`
                    : `<span class="absolute top-1.5 right-1.5 bg-slate-100 text-slate-500 text-[8px] px-1.5 py-0.5 rounded-full font-bold">Còn ${p.stock}</span>`;

                card.innerHTML = `
                    ${badge}
                    <div class="space-y-0.5 pt-1.5">
                        <span class="text-[9px] text-emerald-600 font-bold block uppercase">${p.category}</span>
                        <h4 class="font-bold text-xs text-slate-800 line-clamp-2 leading-tight">${p.name}</h4>
                    </div>
                    <div class="flex justify-between items-center pt-2 border-t border-slate-50">
                        <span class="font-extrabold text-xs text-slate-900">${formatVND(p.sellingPrice)}</span>
                        <div class="w-5.5 h-5.5 rounded-full bg-emerald-50 text-emerald-600 flex items-center justify-center font-bold text-xs hover:bg-emerald-600 hover:text-white transition-all">
                            +
                        </div>
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
                if (c.code === "KHACH_LE") { opt.selected = true; }
                dropdown.appendChild(opt);
            });
        }

        function addToCart(product) {
            const existing = cart.find(item => item.id === product.id);
            const currentQty = existing ? existing.quantity : 0;

            if (currentQty + 1 > product.stock) {
                showToast(`Không đủ số lượng máy trong kho. Còn ${product.stock} máy.`, "warning");
                return;
            }

            if (existing) {
                existing.quantity++;
            } else {
                cart.push({
                    id: product.id,
                    code: product.code,
                    name: product.name,
                    sellingPrice: product.sellingPrice,
                    costPrice: product.costPrice,
                    quantity: 1
                });
            }

            renderCart();
            calcCartTotals();
            showToast("Đã thêm vào giỏ hàng");
        }

        function toggleCartSheet(show) {
            const sheet = document.getElementById("cart-bottom-sheet");
            const fab = document.getElementById("gemini-ai-fab");
            if (show) { 
                sheet.classList.remove("hidden"); 
                fab.classList.add("hidden"); 
            } 
            else { 
                sheet.classList.add("hidden"); 
                fab.classList.remove("hidden"); 
            }
        }

        function renderCart() {
            const container = document.getElementById("pos-cart-container");
            const floatBar = document.getElementById("floating-cart-bar");
            container.innerHTML = "";

            if (cart.length === 0) {
                container.innerHTML = `<div class="py-10 text-center text-slate-400 text-xs font-semibold">Giỏ hàng rỗng. Hãy chọn vài chiếc điện thoại.</div>`;
                floatBar.classList.add("hidden");
                return;
            }

            floatBar.classList.remove("hidden");

            let count = 0;
            cart.forEach((item, index) => {
                count += item.quantity;
                const row = document.createElement("div");
                row.className = "py-3 flex items-center justify-between space-x-1.5";
                row.innerHTML = `
                    <div class="flex-1 min-w-0 pr-2">
                        <h5 class="font-bold text-xs text-slate-800 truncate">${item.name}</h5>
                        <span class="text-[10px] text-slate-400 font-bold block">${formatVND(item.sellingPrice)}</span>
                    </div>
                    <div class="flex items-center space-x-2 shrink-0">
                        <button onclick="updateCartQty(${index}, -1)" class="w-6.5 h-6.5 rounded-full border border-slate-200 bg-white flex items-center justify-center font-bold text-xs">-</button>
                        <span class="w-4 text-center font-bold text-xs">${item.quantity}</span>
                        <button onclick="updateCartQty(${index}, 1)" class="w-6.5 h-6.5 rounded-full border border-slate-200 bg-white flex items-center justify-center font-bold text-xs">+</button>
                    </div>
                    <div class="w-20 text-right shrink-0">
                        <span class="font-extrabold text-xs text-slate-800">${formatVND(item.sellingPrice * item.quantity)}</span>
                    </div>
                `;
                container.appendChild(row);
            });

            document.getElementById("pos-cart-count").innerText = count;
            document.getElementById("cart-floating-count").innerText = count;
        }

        function updateCartQty(index, offset) {
            const item = cart[index];
            const original = products.find(p => p.id === item.id);

            if (item.quantity + offset <= 0) {
                cart.splice(index, 1);
                showToast("Đã xóa máy khỏi giỏ");
            } else if (item.quantity + offset > original.stock) {
                showToast(`Không đủ số lượng máy trong kho!`, "warning");
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
            cart.forEach(item => { subtotal += item.sellingPrice * item.quantity; });

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
            const payMethod = document.getElementById("pos-payment").value;

            let subtotal = 0;
            let costTotal = 0;
            cart.forEach(item => {
                subtotal += item.sellingPrice * item.quantity;
                costTotal += item.costPrice * item.quantity;
            });

            const discountAmt = Math.round(subtotal * (discountPct / 100));
            const totalAmount = subtotal - discountAmt;
            const profit = totalAmount - costTotal;

            const invoiceId = "HD" + Math.floor(1000 + Math.random() * 9000);
            
            // Current datetime in 2026
            const formattedDate = "2026-06-03 11:33"; 

            const sellerInfo = currentUser ? `${currentUser.name} (${currentUser.code})` : "Hệ thống";

            const invoiceObj = {
                id: "inv_" + Date.now(),
                code: invoiceId,
                date: formattedDate,
                customerId: customer.id,
                customerName: customer.name,
                customerPhone: customer.phone,
                paymentMethod: payMethod,
                items: [...cart],
                subtotal: subtotal,
                discount: discountPct,
                totalAmount: totalAmount,
                costTotal: costTotal,
                profit: profit,
                seller: sellerInfo
            };

            // Deduct stock
            cart.forEach(item => {
                const original = products.find(p => p.id === item.id);
                if (original) { original.stock = Math.max(0, original.stock - item.quantity); }
            });

            if (customer.code !== "KHACH_LE") { customer.totalSpent += totalAmount; }

            invoices.unshift(invoiceObj);
            saveState();

            // Reset sales panel
            cart = [];
            renderCart();
            document.getElementById("pos-discount").value = 0;
            calcCartTotals();
            toggleCartSheet(false);
            renderPosProducts();

            showToast(`Giao dịch thành công #${invoiceId}`);
            showSimulatedReceipt(invoiceObj);
        }

        // ==================== RECEIPT VIEWER ====================
        function showSimulatedReceipt(invoice) {
            document.getElementById("receipt-invoice-code").innerText = invoice.code;
            document.getElementById("receipt-id").innerText = invoice.code;
            document.getElementById("receipt-date").innerText = invoice.date;
            document.getElementById("receipt-customer-name").innerText = invoice.customerName;
            document.getElementById("receipt-payment-method").innerText = invoice.paymentMethod;
            document.getElementById("receipt-seller").innerText = invoice.seller || "Quản trị viên";

            const itemContainer = document.getElementById("receipt-items-container");
            itemContainer.innerHTML = "";

            invoice.items.forEach(item => {
                const row = document.createElement("div");
                row.className = "flex justify-between text-[11px] font-semibold text-slate-700 py-1";
                row.innerHTML = `
                    <div class="pr-2 truncate">
                        <span class="block text-slate-800 font-bold">${item.name}</span>
                        <span class="text-[9px] text-slate-400">SL: ${item.quantity} x ${formatVND(item.sellingPrice)}</span>
                    </div>
                    <span class="font-extrabold text-slate-900 shrink-0">${formatVND(item.sellingPrice * item.quantity)}</span>
                `;
                itemContainer.appendChild(row);
            });

            const discAmt = Math.round(invoice.subtotal * (invoice.discount / 100));
            document.getElementById("receipt-subtotal").innerText = formatVND(invoice.subtotal);
            document.getElementById("receipt-discount").innerText = discAmt > 0 ? `-${formatVND(discAmt)} (${invoice.discount}%)` : "0 ₫";
            document.getElementById("receipt-total-amount").innerText = formatVND(invoice.totalAmount);

            document.getElementById("modal-receipt").classList.remove("hidden");
        }

        function closeReceiptModal() {
            document.getElementById("modal-receipt").classList.add("hidden");
        }

        // ==================== TAB 2: INVENTORY ====================
        function renderProductTable() {
            const container = document.getElementById("product-cards-container");
            container.innerHTML = "";

            const searchKey = document.getElementById("product-list-search").value.toLowerCase().trim();
            const catFilter = document.getElementById("product-list-category").value;
            const statusFilter = document.getElementById("product-list-status").value;

            const filtered = products.filter(p => {
                const matchSearch = p.name.toLowerCase().includes(searchKey) || p.code.toLowerCase().includes(searchKey);
                const matchCat = catFilter === "all" || p.category === catFilter;
                
                let matchStatus = true;
                if (statusFilter === "low-stock") {
                    matchStatus = p.stock <= p.minStock && p.stock > 0;
                } else if (statusFilter === "out-of-stock") {
                    matchStatus = p.stock === 0;
                }
                return matchSearch && matchCat && matchStatus;
            });

            if (filtered.length === 0) {
                container.innerHTML = `<div class="py-10 text-center text-slate-400 text-xs font-semibold">Chưa tìm thấy thiết bị nào</div>`;
                return;
            }

            filtered.forEach(p => {
                const isOutOfStock = p.stock === 0;
                const isLowStock = p.stock <= p.minStock && p.stock > 0;

                let badge = "";
                if (isOutOfStock) {
                    badge = `<span class="bg-red-100 text-red-700 px-2.5 py-0.5 rounded-lg text-[9px] font-bold">HẾT HÀNG</span>`;
                } else if (isLowStock) {
                    badge = `<span class="bg-amber-100 text-amber-700 px-2.5 py-0.5 rounded-lg text-[9px] font-bold">CẦN NHẬP GẤP</span>`;
                } else {
                    badge = `<span class="bg-green-100 text-green-700 px-2.5 py-0.5 rounded-lg text-[9px] font-bold">AN TOÀN</span>`;
                }

                const actionsHtml = (currentUser && currentUser.role === "Admin") 
                    ? `<div class="flex justify-end space-x-4 text-xs pt-2 border-t border-slate-50 font-bold">
                        <button onclick="openProductModal(true, '${p.id}')" class="text-blue-600">Sửa thông tin</button>
                        <button onclick="deleteProduct('${p.id}')" class="text-red-500">Xóa khỏi kho</button>
                       </div>`
                    : `<div class="text-[10px] text-slate-400 italic text-right pt-2 border-t border-slate-50 font-medium">Chỉ Quản trị viên mới được thao tác kho</div>`;

                const card = document.createElement("div");
                card.className = "bg-white p-3.5 rounded-2xl border border-slate-100 shadow-sm space-y-3.5";
                card.innerHTML = `
                    <div class="flex justify-between items-start">
                        <div class="space-y-0.5">
                            <span class="text-[9px] text-slate-400 font-extrabold block uppercase tracking-wide">${p.code} | Hãng: ${p.category}</span>
                            <h4 class="font-bold text-sm text-slate-800 leading-tight">${p.name}</h4>
                        </div>
                        ${badge}
                    </div>
                    <div class="grid grid-cols-3 gap-2 py-2.5 bg-slate-50 rounded-xl px-3 text-[11px] font-semibold text-slate-500">
                        <div>
                            <span class="text-[9px] block text-slate-400 font-normal">Giá Vốn</span>
                            <span class="text-slate-800 font-bold">${formatVND(p.costPrice)}</span>
                        </div>
                        <div>
                            <span class="text-[9px] block text-slate-400 font-normal">Giá Bán Lẻ</span>
                            <span class="text-emerald-600 font-bold">${formatVND(p.sellingPrice)}</span>
                        </div>
                        <div class="text-right">
                            <span class="text-[9px] block text-slate-400 font-normal">Tồn Hiện Tại</span>
                            <span class="text-slate-900 font-extrabold text-sm">${p.stock} máy</span>
                        </div>
                    </div>
                    ${actionsHtml}
                `;
                container.appendChild(card);
            });
        }

        function openProductModal(isEdit, id = "") {
            const modal = document.getElementById("modal-product");
            const title = document.getElementById("product-modal-title");
            const form = document.getElementById("product-form");
            form.reset();

            if (isEdit) {
                title.innerText = "Chỉnh sửa sản phẩm";
                const p = products.find(item => item.id === id);
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

        function closeProductModal() {
            document.getElementById("modal-product").classList.add("hidden");
        }

        function saveProduct(event) {
            event.preventDefault();
            const editId = document.getElementById("edit-product-id").value;
            const code = document.getElementById("prod-code").value.trim().toUpperCase();
            const category = document.getElementById("prod-category").value;
            const name = document.getElementById("prod-name").value.trim();
            const costPrice = parseInt(document.getElementById("prod-cost").value) || 0;
            const sellingPrice = parseInt(document.getElementById("prod-selling").value) || 0;
            const stock = parseInt(document.getElementById("prod-stock").value) || 0;
            const minStock = parseInt(document.getElementById("prod-minStock").value) || 0;

            if (editId) {
                const idx = products.findIndex(p => p.id === editId);
                if (idx !== -1) {
                    products[idx] = { id: editId, code, category, name, costPrice, sellingPrice, stock, minStock };
                    showToast("Cập nhật thông tin thành công");
                }
            } else {
                if (products.some(p => p.code === code)) {
                    showToast(`Mã "${code}" bị trùng!`, "error");
                    return;
                }
                const newProd = { id: "prod_" + Date.now(), code, category, name, costPrice, sellingPrice, stock, minStock };
                products.push(newProd);
                showToast("Thêm mới kho hàng thành công");
            }

            saveState();
            closeProductModal();
            renderProductTable();
        }

        function deleteProduct(id) {
            if (confirm("Hành động này sẽ xóa sản phẩm vĩnh viễn khỏi kho. Đồng ý?")) {
                products = products.filter(p => p.id !== id);
                saveState();
                renderProductTable();
                showToast("Đã xóa sản phẩm");
            }
        }

        // ==================== TAB 3: INVOICES HISTORY ====================
        function renderInvoiceTable() {
            const container = document.getElementById("invoice-cards-container");
            container.innerHTML = "";

            const searchKey = document.getElementById("invoice-search").value.toLowerCase().trim();

            const filtered = invoices.filter(inv => {
                return inv.code.toLowerCase().includes(searchKey) || 
                       inv.customerName.toLowerCase().includes(searchKey) || 
                       inv.seller.toLowerCase().includes(searchKey);
            });

            if (filtered.length === 0) {
                container.innerHTML = `<div class="py-10 text-center text-slate-400 text-xs font-semibold">Chưa phát sinh hóa đơn nào</div>`;
                return;
            }

            filtered.forEach(inv => {
                const card = document.createElement("div");
                card.className = "bg-white p-3.5 rounded-2xl border border-slate-100 shadow-sm space-y-2";
                card.innerHTML = `
                    <div class="flex justify-between items-center text-xs">
                        <span class="font-bold text-emerald-600 font-mono">#${inv.code}</span>
                        <span class="text-slate-400 font-bold">${inv.date}</span>
                    </div>
                    <div class="flex justify-between items-end">
                        <div>
                            <span class="text-[9px] block text-slate-400 font-bold uppercase">Khách Hàng</span>
                            <span class="text-xs font-extrabold text-slate-800">${inv.customerName}</span>
                        </div>
                        <div class="text-right">
                            <span class="text-[9px] block text-slate-400 font-bold uppercase">Thanh Toán</span>
                            <span class="text-sm font-black text-slate-900">${formatVND(inv.totalAmount)}</span>
                        </div>
                    </div>
                    <div class="pt-2.5 border-t border-slate-50 flex justify-between items-center text-[10px] text-slate-400 font-bold">
                        <div>
                            <span class="bg-slate-100 text-slate-600 px-1.5 py-0.5 rounded mr-1.5">${inv.paymentMethod}</span>
                            <span>Người bán: <span class="text-slate-600">${inv.seller ? inv.seller.split(' ')[0] : 'Admin'}</span></span>
                        </div>
                        <button onclick="viewInvoiceDetail('${inv.id}')" class="text-emerald-600 font-bold hover:underline">Chi tiết</button>
                    </div>
                `;
                container.appendChild(card);
            });
        }

        function viewInvoiceDetail(id) {
            const inv = invoices.find(item => item.id === id);
            if (inv) { showSimulatedReceipt(inv); }
        }

        // ==================== TAB 4: CUSTOMERS ====================
        function renderCustomerTable() {
            const container = document.getElementById("customer-cards-container");
            container.innerHTML = "";

            const searchKey = document.getElementById("customer-search").value.toLowerCase().trim();

            const filtered = customers.filter(c => {
                return c.name.toLowerCase().includes(searchKey) || c.phone.includes(searchKey);
            });

            if (filtered.length === 0) {
                container.innerHTML = `<div class="py-10 text-center text-slate-400 text-xs font-semibold">Không tìm thấy khách hàng</div>`;
                return;
            }

            filtered.forEach(c => {
                const isGuest = c.code === "KHACH_LE";
                const card = document.createElement("div");
                card.className = "bg-white p-3.5 rounded-2xl border border-slate-100 shadow-sm space-y-2";
                
                const actions = isGuest ? "" : `
                    <div class="flex justify-end space-x-3 text-xs pt-2 border-t border-slate-50 font-bold">
                        <button onclick="openAddCustomerModal(false, '${c.id}')" class="text-blue-600">Sửa thông tin</button>
                        <button onclick="deleteCustomer('${c.id}')" class="text-red-500">Xóa tài khoản</button>
                    </div>
                `;

                card.innerHTML = `
                    <div class="flex justify-between items-start">
                        <div class="space-y-1">
                            <span class="text-[9px] text-slate-400 font-bold block">${c.code}</span>
                            <h4 class="font-extrabold text-sm text-slate-800">${c.name}</h4>
                            <span class="text-[11px] font-bold text-slate-500 block">SĐT: ${c.phone}</span>
                            <span class="text-[11px] text-slate-400 truncate block max-w-[200px]">${c.address}</span>
                        </div>
                        <div class="text-right">
                            <span class="text-[9px] text-slate-400 font-bold block uppercase">Chi tiêu lũy kế</span>
                            <span class="text-xs font-extrabold text-emerald-600">${formatVND(c.totalSpent)}</span>
                        </div>
                    </div>
                    ${actions}
                `;
                container.appendChild(card);
            });
        }

        function openAddCustomerModal(isPosAdding = false, id = "") {
            const modal = document.getElementById("modal-customer");
            const title = document.getElementById("customer-modal-title");
            const form = document.getElementById("customer-form");
            form.reset();
            document.getElementById("is-pos-adding").value = isPosAdding ? "true" : "false";

            if (id) {
                title.innerText = "Chỉnh sửa khách hàng";
                const c = customers.find(item => item.id === id);
                if (c) {
                    document.getElementById("edit-customer-id").value = c.id;
                    document.getElementById("cust-code").value = c.code;
                    document.getElementById("cust-name").value = c.name;
                    document.getElementById("cust-phone").value = c.phone;
                    document.getElementById("cust-address").value = c.address;
                }
            } else {
                title.innerText = "Thêm khách hàng";
                document.getElementById("edit-customer-id").value = "";
                document.getElementById("cust-code").value = "KH00" + customers.length;
            }
            modal.classList.remove("hidden");
        }

        function closeCustomerModal() {
            document.getElementById("modal-customer").classList.add("hidden");
        }

        function saveCustomer(event) {
            event.preventDefault();
            const editId = document.getElementById("edit-customer-id").value;
            const code = document.getElementById("cust-code").value.trim().toUpperCase();
            const name = document.getElementById("cust-name").value.trim();
            const phone = document.getElementById("cust-phone").value.trim();
            const address = document.getElementById("cust-address").value.trim() || "-";
            const isPos = document.getElementById("is-pos-adding").value === "true";

            if (editId) {
                const idx = customers.findIndex(c => c.id === editId);
                if (idx !== -1) {
                    customers[idx] = { ...customers[idx], code, name, phone, address };
                    showToast("Cập nhật thông tin khách hàng thành công");
                }
            } else {
                if (customers.some(c => c.code === code || (c.phone === phone && phone !== "-"))) {
                    showToast("Mã hoặc SĐT đã tồn tại trên hệ thống!", "error");
                    return;
                }
                const newCust = { id: "cust_" + Date.now(), code, name, phone, address, totalSpent: 0 };
                customers.push(newCust);
                showToast("Thêm khách hàng thành công");

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
            
            if (isPos) {
                switchTab('pos');
            } else {
                renderCustomerTable();
            }
        }

        function deleteCustomer(id) {
            if (confirm("Bạn có đồng ý xóa dữ liệu tích lũy khách hàng này?")) {
                customers = customers.filter(c => c.id !== id);
                saveState();
                renderCustomerTable();
                showToast("Đã xóa khách hàng");
            }
        }

        // ==================== TAB 5: STAFFS MANAGEMENT (ADMIN ONLY) ====================
        function renderStaffTable() {
            const container = document.getElementById("staff-cards-container");
            container.innerHTML = "";

            staffs.forEach(s => {
                const isSelf = s.username === currentUser.username;
                const card = document.createElement("div");
                card.className = "bg-white p-3.5 rounded-2xl border border-slate-100 shadow-sm space-y-2";
                
                const actions = isSelf 
                    ? `<span class="text-[10px] text-emerald-600 font-bold italic block text-right">Tài khoản đang đăng nhập hiện tại</span>`
                    : `<div class="flex justify-end space-x-3 text-xs pt-1.5 border-t border-slate-50 font-bold">
                        <button onclick="openStaffModal(true, '${s.id}')" class="text-blue-600">Sửa quyền</button>
                        <button onclick="deleteStaff('${s.id}')" class="text-red-500">Xóa tài khoản</button>
                       </div>`;

                card.innerHTML = `
                    <div class="flex justify-between items-start">
                        <div>
                            <span class="text-[9px] text-slate-400 font-bold block">${s.code}</span>
                            <h4 class="font-extrabold text-sm text-slate-800">${s.name}</h4>
                            <span class="text-xs text-slate-500 font-bold block">Tài khoản: <span class="text-emerald-600 font-mono">${s.username}</span></span>
                        </div>
                        <span class="px-2.5 py-0.5 rounded-lg text-[10px] font-bold ${
                            s.role === "Admin" ? "bg-amber-100 text-amber-700" : "bg-blue-100 text-blue-700"
                        }">${s.role === "Admin" ? "Quản lý" : "Nhân viên"}</span>
                    </div>
                    ${actions}
                `;
                container.appendChild(card);
            });
        }

        function openStaffModal(isEdit, id = "") {
            const modal = document.getElementById("modal-staff");
            const title = document.getElementById("staff-modal-title");
            const form = document.getElementById("staff-form");
            form.reset();

            if (isEdit) {
                title.innerText = "Cập nhật nhân viên";
                const s = staffs.find(item => item.id === id);
                if (s) {
                    document.getElementById("edit-staff-id").value = s.id;
                    document.getElementById("staff-code").value = s.code;
                    document.getElementById("staff-name").value = s.name;
                    document.getElementById("staff-username").value = s.username;
                    document.getElementById("staff-password").value = s.password;
                    document.getElementById("staff-role").value = s.role;
                }
            } else {
                title.innerText = "Thêm nhân viên mới";
                document.getElementById("edit-staff-id").value = "";
                document.getElementById("staff-code").value = "NV00" + (staffs.length + 1);
            }
            modal.classList.remove("hidden");
        }

        function closeStaffModal() {
            document.getElementById("modal-staff").classList.add("hidden");
        }

        function saveStaff(event) {
            event.preventDefault();
            const editId = document.getElementById("edit-staff-id").value;
            const code = document.getElementById("staff-code").value.trim().toUpperCase();
            const name = document.getElementById("staff-name").value.trim();
            const username = document.getElementById("staff-username").value.trim().toLowerCase();
            const password = document.getElementById("staff-password").value.trim();
            const role = document.getElementById("staff-role").value;

            if (editId) {
                const idx = staffs.findIndex(s => s.id === editId);
                if (idx !== -1) {
                    staffs[idx] = { id: editId, code, name, username, password, role };
                    showToast("Cập nhật thông tin nhân viên thành công");
                }
            } else {
                if (staffs.some(s => s.username === username)) {
                    showToast("Tên đăng nhập đã trùng!", "error");
                    return;
                }
                const newStaff = { id: "staff_" + Date.now(), code, name, username, password, role };
                staffs.push(newStaff);
                showToast("Thêm nhân viên mới thành công");
            }

            saveState();
            closeStaffModal();
            renderStaffTable();
        }

        function deleteStaff(id) {
            if (confirm("Hành động này sẽ xóa tài khoản nhân viên. Đồng ý?")) {
                staffs = staffs.filter(s => s.id !== id);
                saveState();
                renderStaffTable();
                showToast("Đã xóa nhân viên.");
            }
        }

        // ==================== TAB 6: REPORTS & ADVANCED ANALYTICS ====================
        function setReportPeriod(period) {
            reportPeriod = period;
            
            // Toggle filter button styles
            const periods = ['day', 'week', 'month', 'year'];
            periods.forEach(p => {
                const btn = document.getElementById(`btn-period-${p}`);
                if (p === period) {
                    btn.className = "flex-1 py-2 px-2 text-xs font-bold rounded-xl transition-all bg-white text-emerald-700 shadow-sm whitespace-nowrap";
                } else {
                    btn.className = "flex-1 py-2 px-2 text-xs font-bold rounded-xl transition-all text-slate-600 whitespace-nowrap";
                }
            });

            if (period === 'month') {
                document.getElementById('month-picker-container').classList.remove('hidden');
            } else {
                document.getElementById('month-picker-container').classList.add('hidden');
            }

            renderReportDashboard();
        }

        function renderReportDashboard() {
            // Hardcode base date for 2026: Wednesday, June 3rd, 2026.
            const todayStr = "2026-06-03";
            const currentYearStr = "2026";

            // Filter invoices dynamically based on the active reportPeriod
            let filteredInvoices = [];

            if (reportPeriod === 'day') {
                document.getElementById("chart-period-lbl").innerText = "Hôm nay";
                filteredInvoices = invoices.filter(inv => inv.date.startsWith(todayStr));
            } else if (reportPeriod === 'week') {
                document.getElementById("chart-period-lbl").innerText = "Tuần này";
                filteredInvoices = invoices.filter(inv => {
                    const datePart = inv.date.split(' ')[0];
                    return datePart >= "2026-06-01" && datePart <= "2026-06-07";
                });
            } else if (reportPeriod === 'month') {
                const selectedMonth = document.getElementById("custom-month-selector").value; // e.g. "2026-06"
                if (!selectedMonth) return;
                const [year, month] = selectedMonth.split('-');
                document.getElementById("chart-period-lbl").innerText = `Tháng ${month}/${year}`;
                filteredInvoices = invoices.filter(inv => inv.date.startsWith(selectedMonth));
            } else if (reportPeriod === 'year') {
                document.getElementById("chart-period-lbl").innerText = "Năm nay";
                filteredInvoices = invoices.filter(inv => inv.date.startsWith(currentYearStr));
            }

            // Calculation metrics
            let totalRevenue = 0;
            let totalProfit = 0;
            let totalQtySold = 0;
            let totalOrders = filteredInvoices.length;

            // Compute metrics & identify Top Selling Products
            const productSalesMap = {}; 

            filteredInvoices.forEach(inv => {
                totalRevenue += inv.totalAmount;
                totalProfit += inv.profit;
                
                inv.items.forEach(item => {
                    totalQtySold += item.quantity;
                    productSalesMap[item.name] = (productSalesMap[item.name] || 0) + item.quantity;
                });
            });

            // Update stats cards
            document.getElementById("stat-revenue").innerText = formatVND(totalRevenue);
            document.getElementById("stat-profit").innerText = formatVND(totalProfit);
            document.getElementById("stat-orders").innerText = `${totalOrders} Đơn`;
            document.getElementById("stat-qty-sold").innerText = `${totalQtySold} Máy`;

            // Display "Top Selling Products" visual progress list
            renderTopProductsProgress(productSalesMap);

            // Re-render the charts with dynamic coordinates
            renderAnalyticsChart(filteredInvoices);
        }

        function renderTopProductsProgress(salesMap) {
            const container = document.getElementById("top-products-progress-container");
            container.innerHTML = "";

            // Convert map to array and sort descending
            const sortedProducts = Object.keys(salesMap).map(name => {
                return { name: name, qty: salesMap[name] };
            }).sort((a,b) => b.qty - a.qty).slice(0, 4);

            if (sortedProducts.length === 0) {
                container.innerHTML = `<p class="text-center text-xs text-slate-400 py-4 font-semibold">Chưa bán ra thiết bị nào trong giai đoạn này</p>`;
                return;
            }

            // Find max quantity to determine 100% width benchmark
            const maxQty = Math.max(...sortedProducts.map(p => p.qty));

            sortedProducts.forEach((p, index) => {
                const pct = maxQty > 0 ? (p.qty / maxQty) * 100 : 0;
                
                // Color codes for top ranks
                const colors = ['bg-emerald-500', 'bg-blue-500', 'bg-amber-500', 'bg-purple-500'];
                const badgeColors = ['bg-emerald-50 text-emerald-600', 'bg-blue-50 text-blue-600', 'bg-amber-50 text-amber-600', 'bg-purple-50 text-purple-600'];
                const activeColor = colors[index] || 'bg-slate-400';
                const activeBadge = badgeColors[index] || 'bg-slate-100 text-slate-500';

                const itemDiv = document.createElement("div");
                itemDiv.className = "space-y-1.5";
                itemDiv.innerHTML = `
                    <div class="flex justify-between items-center text-xs font-bold text-slate-700">
                        <div class="flex items-center space-x-2 truncate">
                            <span class="w-5 h-5 rounded-full flex items-center justify-center font-bold text-[10px] ${activeBadge}">${index+1}</span>
                            <span class="truncate max-w-[200px] text-slate-850">${p.name}</span>
                        </div>
                        <span class="text-slate-800 font-extrabold shrink-0">${p.qty} máy</span>
                    </div>
                    <div class="w-full bg-slate-100 h-2.5 rounded-full overflow-hidden">
                        <div class="${activeColor} h-full rounded-full transition-all duration-500" style="width: ${pct}%"></div>
                    </div>
                `;
                container.appendChild(itemDiv);
            });
        }

        function renderAnalyticsChart(invoicesGroup) {
            if (revenueChartObj) { revenueChartObj.destroy(); }

            let labels = [];
            let revenueDataPoints = [];
            let profitDataPoints = [];

            if (reportPeriod === 'day') {
                labels = ['08h-10h', '10h-12h', '12h-14h', '14h-16h', '16h-18h', '18h-20h', '20h-22h'];
                revenueDataPoints = [4000000, 12000000, 5000000, 8000000, 0, 0, 0];
                profitDataPoints = [450000, 1500000, 550000, 850000, 0, 0, 0];

                invoicesGroup.forEach(inv => {
                    const hour = parseInt(inv.date.split(' ')[1].split(':')[0]);
                    if (hour >= 8 && hour < 10) { revenueDataPoints[0] += inv.totalAmount; profitDataPoints[0] += inv.profit; }
                    else if (hour >= 10 && hour < 12) { revenueDataPoints[1] += inv.totalAmount; profitDataPoints[1] += inv.profit; }
                    else if (hour >= 12 && hour < 14) { revenueDataPoints[2] += inv.totalAmount; profitDataPoints[2] += inv.profit; }
                    else if (hour >= 14 && hour < 16) { revenueDataPoints[3] += inv.totalAmount; profitDataPoints[3] += inv.profit; }
                    else if (hour >= 16 && hour < 18) { revenueDataPoints[4] += inv.totalAmount; profitDataPoints[4] += inv.profit; }
                    else if (hour >= 18 && hour < 20) { revenueDataPoints[5] += inv.totalAmount; profitDataPoints[5] += inv.profit; }
                    else if (hour >= 20 && hour <= 22) { revenueDataPoints[6] += inv.totalAmount; profitDataPoints[6] += inv.profit; }
                });

            } else if (reportPeriod === 'week') {
                labels = ['Thứ 2', 'Thứ 3', 'Thứ 4', 'Thứ 5', 'Thứ 6', 'Thứ 7', 'Chủ Nhật'];
                revenueDataPoints = [0, 0, 0, 0, 0, 0, 0];
                profitDataPoints = [0, 0, 0, 0, 0, 0, 0];

                invoicesGroup.forEach(inv => {
                    const datePart = inv.date.split(' ')[0];
                    if (datePart === "2026-06-01") { revenueDataPoints[0] += inv.totalAmount; profitDataPoints[0] += inv.profit; }
                    else if (datePart === "2026-06-02") { revenueDataPoints[1] += inv.totalAmount; profitDataPoints[1] += inv.profit; }
                    else if (datePart === "2026-06-03") { revenueDataPoints[2] += inv.totalAmount; profitDataPoints[2] += inv.profit; }
                    else if (datePart === "2026-06-04") { revenueDataPoints[3] += inv.totalAmount; profitDataPoints[3] += inv.profit; }
                    else if (datePart === "2026-06-05") { revenueDataPoints[4] += inv.totalAmount; profitDataPoints[4] += inv.profit; }
                    else if (datePart === "2026-06-06") { revenueDataPoints[5] += inv.totalAmount; profitDataPoints[5] += inv.profit; }
                    else if (datePart === "2026-06-07") { revenueDataPoints[6] += inv.totalAmount; profitDataPoints[6] += inv.profit; }
                });

            } else if (reportPeriod === 'month') {
                // Break the month dynamically into 5 viewable sections
                labels = ['Tuần 1 (1-7)', 'Tuần 2 (8-14)', 'Tuần 3 (15-21)', 'Tuần 4 (22-28)', 'Cuối tháng'];
                revenueDataPoints = [0, 0, 0, 0, 0];
                profitDataPoints = [0, 0, 0, 0, 0];

                invoicesGroup.forEach(inv => {
                    const dayVal = parseInt(inv.date.split(' ')[0].split('-')[2]);
                    if (dayVal <= 7) { revenueDataPoints[0] += inv.totalAmount; profitDataPoints[0] += inv.profit; }
                    else if (dayVal <= 14) { revenueDataPoints[1] += inv.totalAmount; profitDataPoints[1] += inv.profit; }
                    else if (dayVal <= 21) { revenueDataPoints[2] += inv.totalAmount; profitDataPoints[2] += inv.profit; }
                    else if (dayVal <= 28) { revenueDataPoints[3] += inv.totalAmount; profitDataPoints[3] += inv.profit; }
                    else { revenueDataPoints[4] += inv.totalAmount; profitDataPoints[4] += inv.profit; }
                });

            } else if (reportPeriod === 'year') {
                labels = ['T1', 'T2', 'T3', 'T4', 'T5', 'T6', 'T7', 'T8', 'T9', 'T10', 'T11', 'T12'];
                revenueDataPoints = Array(12).fill(0);
                profitDataPoints = Array(12).fill(0);

                invoicesGroup.forEach(inv => {
                    const monthIdx = parseInt(inv.date.split(' ')[0].split('-')[1]) - 1;
                    if (monthIdx >= 0 && monthIdx < 12) {
                        revenueDataPoints[monthIdx] += inv.totalAmount;
                        profitDataPoints[monthIdx] += inv.profit;
                    }
                });
            }

            const ctx = document.getElementById('revenueChart').getContext('2d');
            revenueChartObj = new Chart(ctx, {
                type: 'line',
                data: {
                    labels: labels,
                    datasets: [
                        {
                            label: 'Doanh Thu',
                            data: revenueDataPoints,
                            borderColor: '#10b981',
                            backgroundColor: 'rgba(16, 185, 129, 0.08)',
                            borderWidth: 2.5,
                            fill: true,
                            tension: 0.35,
                            pointRadius: 4,
                            pointBackgroundColor: '#10b981'
                        },
                        {
                            label: 'Lợi Nhuận',
                            data: profitDataPoints,
                            borderColor: '#3b82f6',
                            backgroundColor: 'rgba(59, 130, 246, 0.08)',
                            borderWidth: 2,
                            fill: true,
                            tension: 0.35,
                            pointRadius: 3,
                            pointBackgroundColor: '#3b82f6'
                        }
                    ]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    plugins: {
                        legend: { display: false }
                    },
                    scales: {
                        x: { grid: { display: false }, ticks: { font: { size: 9, weight: '600' } } },
                        y: {
                            ticks: {
                                font: { size: 9, weight: '600' },
                                callback: function(v) { return v >= 1000000 ? (v / 1000000) + 'M' : v >= 1000 ? (v / 1000) + 'k' : v; }
                            }
                        }
                    }
                }
            });
        }

        // ==================== GEMINI AI INTEGRATION & ACTIONS ====================
        const apiKey = ""; 
        let aiChatHistory = [];

        function toggleAiChatSheet(show) {
            const sheet = document.getElementById("ai-chat-sheet");
            if (show) {
                sheet.classList.remove("hidden");
            } else {
                sheet.classList.add("hidden");
            }
        }

        function clearAiChatHistory() {
            aiChatHistory = [];
            const container = document.getElementById("ai-chat-messages");
            container.innerHTML = `
                <!-- Welcome Message -->
                <div class="flex items-start space-x-2.5">
                    <div class="w-8 h-8 rounded-xl bg-gradient-to-br from-indigo-500 to-purple-600 flex items-center justify-center shrink-0 shadow-sm text-white text-xs font-bold">
                        AI
                    </div>
                    <div class="bg-white border border-slate-200/80 p-3.5 rounded-2xl rounded-tl-none shadow-sm text-xs text-slate-700 max-w-[85%] space-y-2">
                        <p>Đã làm sạch lịch sử hội thoại! Mình đã sẵn sàng hỗ trợ tiếp.</p>
                    </div>
                </div>
            `;
            showToast("Đã xóa lịch sử chat AI.");
        }

        async function fetchWithRetry(url, options, maxRetries = 5) {
            let delays = [1000, 2000, 4000, 8000, 16000];
            for (let i = 0; i < maxRetries; i++) {
                try {
                    const response = await fetch(url, options);
                    if (!response.ok) throw new Error(`HTTP error! status: ${response.status}`);
                    return await response.json();
                } catch (e) {
                    if (i === maxRetries - 1) throw e;
                    await new Promise(r => setTimeout(r, delays[i]));
                }
            }
        }

        // Advanced AI System Context Data gathering
        function getAiSystemContext() {
            // Inventory metrics
            const totalProducts = products.length;
            const lowStockProducts = products.filter(p => p.stock <= p.minStock);
            const lowStockList = lowStockProducts.length > 0 ? lowStockProducts.map(p => `${p.name} (Mã: ${p.code}, Tồn: ${p.stock})`).join(', ') : "Không có";
            
            // Financials of the simulated today (2026-06-03)
            const todayStr = "2026-06-03";
            const todayInvoices = invoices.filter(i => i.date.includes(todayStr));
            const todayRevenue = todayInvoices.reduce((sum, inv) => sum + inv.totalAmount, 0);
            const todayProfit = todayInvoices.reduce((sum, inv) => sum + inv.profit, 0);
            const totalOrders = todayInvoices.length;

            // Product Best Sellers map
            const salesMap = {};
            invoices.forEach(inv => inv.items.forEach(item => salesMap[item.name] = (salesMap[item.name] || 0) + item.quantity));
            const bestSellers = Object.entries(salesMap)
                                .sort((a,b) => b[1]-a[1])
                                .slice(0, 3)
                                .map(arr => `${arr[0]} (đã bán ${arr[1]} máy)`).join(', ');

            return `Bạn là "KiotPro AI Assistant 3.0" - trợ lý thông minh của hệ thống quản lý KiotPro Mobile.
Ngày hôm nay: Thứ Sáu, 05/06/2026 (dữ liệu ca làm việc chuẩn hiện tại tập trung ngày 03/06/2026).
Người đang chat: ${currentUser ? currentUser.name : "Khách"} (Quyền: ${currentUser ? currentUser.role : "Nhân viên"}).

📊 DỮ LIỆU THỰC TẾ CỦA CỬA HÀNG (REAL-TIME DATA):
- Doanh thu hôm nay: ${formatVND(todayRevenue)}. Lợi nhuận: ${formatVND(todayProfit)}. Số đơn: ${totalOrders}.
- Kho hàng có: ${totalProducts} dòng thiết bị.
- Hàng sắp hết: [${lowStockList}].
- Sản phẩm đang bán chạy nhất: [${bestSellers}].

🛠️ ĐIỀU KHIỂN HỆ THỐNG (AI ACTIONS):
Bạn có khả năng tự động thao tác thay cho người dùng! Khi muốn ra lệnh hệ thống, bạn CHỈ CẦN chèn ĐÚNG MỘT đoạn mã JSON ở cuối tin nhắn trả lời.
Cấu trúc bắt buộc: [ACTION:{"type":"...", "..."}]

CÁC LỆNH HỖ TRỢ:
1. Mở các trang quản lý: [ACTION:{"type":"switch_tab","tab":"pos|products|invoices|customers|reports"}]
   (Ví dụ người dùng bảo: "Cho mình xem báo cáo" -> [ACTION:{"type":"switch_tab","tab":"reports"}])
2. Lọc nhanh/Tìm kiếm: [ACTION:{"type":"search","tab":"pos|products","query":"TỪ KHÓA"}]
   (Ví dụ: "Tìm iPhone" -> [ACTION:{"type":"search","tab":"pos","query":"iPhone"}])
3. Mở báo cáo kinh doanh theo thời gian: [ACTION:{"type":"open_report","period":"day|week|month|year"}]
   (Ví dụ: "Báo cáo tháng này" -> [ACTION:{"type":"open_report","period":"month"}])
4. Thêm sản phẩm vào giỏ hàng TỰ ĐỘNG: [ACTION:{"type":"add_to_cart","productCode":"MÃ SẢN PHẨM"}]
   (Quan trọng: Bạn phải lấy đúng MÃ SẢN PHẨM trong hệ thống để điền vào. Ví dụ: DT001, DT002, DT003... Ví dụ người dùng nói "Bán 1 cái iPhone 15 Pro Max" -> Bạn đáp: "Mình đã thêm iPhone 15 Pro Max vào giỏ hàng nhé." + [ACTION:{"type":"add_to_cart","productCode":"DT001"}])
   Bảng mã hàng:
   - iPhone 15 Pro Max 256GB: DT001
   - Samsung Galaxy S24 Ultra 512GB: DT002
   - iPhone 13 128GB: DT003
   - Oppo Reno11 5G: DT004
   - Xiaomi Redmi Note 13: DT005
   - iPhone 15 128GB: DT006

QUY TẮC TRẢ LỜI:
- Thân thiện, gọn gàng, phù hợp màn hình điện thoại. Dùng "Mình" và "Bạn".
- Dùng dấu (*) hoặc (-) để tạo danh sách bullet point.
- Dùng dấu (**) để **in đậm** text quan trọng.`;
        }

        function formatMarkdownLike(text) {
            // Xóa phần action ẩn đi
            let cleanText = text.replace(/\[ACTION:.*?\]/g, '').trim();
            
            // Xử lý Bold **
            cleanText = cleanText.replace(/\*\*(.*?)\*\*/g, '<strong class="text-slate-800 font-extrabold">$1</strong>');
            
            // Xử lý list (bullet points dạng - hoặc *)
            let lines = cleanText.split('\n');
            let inList = false;
            let htmlLines = [];
            
            lines.forEach(line => {
                let trimmed = line.trim();
                if (trimmed.startsWith('- ') || trimmed.startsWith('* ')) {
                    if (!inList) {
                        htmlLines.push('<ul class="list-disc ml-4 space-y-1 my-2">');
                        inList = true;
                    }
                    htmlLines.push(`<li>${trimmed.substring(2)}</li>`);
                } else {
                    if (inList) {
                        htmlLines.push('</ul>');
                        inList = false;
                    }
                    htmlLines.push(`${line}<br>`);
                }
            });
            
            if (inList) htmlLines.push('</ul>');
            
            return htmlLines.join('').replace(/(<br>)+$/g, ''); // Xóa <br> dư ở cuối
        }

        // Parse and Dispatch Action detected from Gemini's response
        function parseAndDispatchAction(rawText) {
            const regex = /\[ACTION:(.*?)\]/g;
            let match;
            while ((match = regex.exec(rawText)) !== null) {
                try {
                    const action = JSON.parse(match[1]);
                    console.log("AI Action Dispatched:", action);
                    
                    if (action.type === "switch_tab") {
                        switchTab(action.tab);
                        showToast(`AI: Đang mở trang ${getTabDisplayName(action.tab)}`);
                    } 
                    else if (action.type === "search") {
                        switchTab(action.tab);
                        let inputId = action.tab === "pos" ? "pos-search" : "product-list-search";
                        const input = document.getElementById(inputId);
                        if (input) {
                            input.value = action.query;
                            if(action.tab === "pos") renderPosProducts();
                            else renderProductTable();
                            showToast(`AI: Đã lọc từ khóa "${action.query}"`);
                        }
                    }
                    else if (action.type === "open_report") {
                        switchTab('reports');
                        setReportPeriod(action.period);
                        showToast(`AI: Đã cập nhật số liệu bảng báo cáo.`);
                    }
                    else if (action.type === "add_to_cart") {
                        switchTab('pos');
                        const product = products.find(p => p.code === action.productCode);
                        if (product) {
                            addToCart(product);
                            toggleCartSheet(true); // Tự động bật sheet giỏ hàng cho user xem
                        } else {
                            showToast(`AI lỗi: Không tìm thấy mã sản phẩm ${action.productCode}`, "error");
                        }
                    }
                } catch (e) {
                    console.error("Lỗi parse AI Action:", e);
                }
            }
        }

        function getTabDisplayName(tab) {
            const names = {
                pos: "Bán hàng",
                products: "Kho hàng",
                invoices: "Hóa đơn",
                customers: "Khách hàng",
                reports: "Báo cáo kinh doanh",
                staffs: "Nhân viên"
            };
            return names[tab] || tab;
        }

        function appendMessage(text, isUser = false) {
            const container = document.getElementById("ai-chat-messages");
            const msgDiv = document.createElement("div");
            msgDiv.className = `flex items-start space-x-2.5 fade-in-up ${isUser ? 'justify-end space-x-reverse' : 'justify-start'}`;
            
            if (isUser) {
                msgDiv.innerHTML = `
                    <div class="bg-indigo-600 text-white p-3 rounded-2xl rounded-tr-none shadow-sm text-xs max-w-[85%] font-medium">
                        ${text}
                    </div>
                    <div class="w-8 h-8 rounded-xl bg-slate-200 flex items-center justify-center shrink-0 text-[10px] font-bold text-slate-600">
                        Me
                    </div>
                `;
            } else {
                msgDiv.innerHTML = `
                    <div class="w-8 h-8 rounded-xl bg-gradient-to-br from-indigo-500 to-purple-600 flex items-center justify-center shrink-0 shadow-sm text-white text-xs font-bold">
                        AI
                    </div>
                    <div class="bg-white border border-slate-200/80 p-3.5 rounded-2xl rounded-tl-none shadow-sm text-xs text-slate-700 max-w-[85%] leading-relaxed">
                        ${formatMarkdownLike(text)}
                    </div>
                `;
            }
            
            container.appendChild(msgDiv);
            container.scrollTop = container.scrollHeight;
        }

        function appendLoadingIndicator() {
            const container = document.getElementById("ai-chat-messages");
            const msgDiv = document.createElement("div");
            msgDiv.id = "ai-typing-indicator";
            msgDiv.className = `flex items-end space-x-2 fade-in-up justify-start`;
            msgDiv.innerHTML = `
                <div class="w-8 h-8 rounded-xl bg-gradient-to-br from-indigo-500 to-purple-600 flex items-center justify-center shrink-0 shadow-sm text-white text-xs font-bold">
                    AI
                </div>
                <div class="bg-white border border-slate-200 py-3.5 px-4 rounded-2xl rounded-tl-none shadow-sm flex space-x-1 items-center h-8">
                    <div class="w-1.5 h-1.5 bg-indigo-400 rounded-full typing-dot"></div>
                    <div class="w-1.5 h-1.5 bg-indigo-400 rounded-full typing-dot"></div>
                    <div class="w-1.5 h-1.5 bg-indigo-400 rounded-full typing-dot"></div>
                </div>
            `;
            container.appendChild(msgDiv);
            container.scrollTop = container.scrollHeight;
        }

        function removeLoadingIndicator() {
            const el = document.getElementById("ai-typing-indicator");
            if (el) el.remove();
        }

        // Quick Suggestion Prompts Click Handler
        function sendQuickPrompt(promptText) {
            const inputEl = document.getElementById("ai-chat-input");
            inputEl.value = promptText;
            document.getElementById("ai-chat-form").dispatchEvent(new Event('submit'));
        }

        async function handleSendAiMessage(event) {
            if (event) event.preventDefault();
            
            const inputEl = document.getElementById("ai-chat-input");
            const btnEl = document.getElementById("ai-send-btn");
            const text = inputEl.value.trim();
            
            if (!text) return;
            
            // UI Updates
            inputEl.value = "";
            inputEl.disabled = true;
            btnEl.disabled = true;
            appendMessage(text, true);
            appendLoadingIndicator();

            aiChatHistory.push({ role: "user", parts: [{ text: text }] });
            
            const systemContext = getAiSystemContext();
            
            const payload = {
                contents: aiChatHistory,
                systemInstruction: { parts: [{ text: systemContext }] }
            };

            const url = `https://generativelanguage.googleapis.com/v1beta/models/gemini-2.5-flash-preview-09-2025:generateContent?key=${apiKey}`;
            
            try {
                const response = await fetchWithRetry(url, {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify(payload)
                });

                const replyText = response.candidates?.[0]?.content?.parts?.[0]?.text || "Xin lỗi, tôi không thể xử lý câu hỏi lúc này.";
                
                aiChatHistory.push({ role: "model", parts: [{ text: replyText }] });
                
                removeLoadingIndicator();
                appendMessage(replyText, false);
                
                // Process dynamic action if present
                parseAndDispatchAction(replyText);

            } catch (error) {
                console.error("AI Chat Error:", error);
                removeLoadingIndicator();
                appendMessage("Hệ thống kết nối AI đang gián đoạn, vui lòng thử lại sau.", false);
                aiChatHistory.pop();
            } finally {
                inputEl.disabled = false;
                btnEl.disabled = false;
                inputEl.focus();
            }
        }
    </script>
</body>
</html>
