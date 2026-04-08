<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>hoco. Elite Ultra</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Tajawal:wght@400;700;900&display=swap" rel="stylesheet">
    
    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getFirestore, collection, addDoc, onSnapshot, query, orderBy, doc, deleteDoc, updateDoc } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js";

        const firebaseConfig = {
            apiKey: "AIzaSyAqqQ9j34XDw1_CvfWfuX_VTM66Y6C1C0SRHYJ", 
            authDomain: "hocoprice.firebaseapp.com",
            projectId: "hocoprice",
            storageBucket: "hocoprice.firebasestorage.app",
            messagingSenderId: "367105764415",
            appId: "1:367105764415:web:c2e2d7cb06fa489114757c"
        };

        const app = initializeApp(firebaseConfig);
        const db = getFirestore(app);
        window.dbCol = collection(db, "products");
        window.dbAds = collection(db, "ads");
        window.dbSales = collection(db, "sales"); // كولكشن التقارير
        window.dbDirect = db;
    </script>

    <style>
        :root { --primary: #0f172a; --maroon: #800000; --bg: #f1f5f9; }
        body { font-family: 'Tajawal', sans-serif; background: var(--bg); color: var(--primary); overflow-x: hidden; }
        .glass { background: rgba(255, 255, 255, 0.8); backdrop-filter: blur(12px); -webkit-backdrop-filter: blur(12px); border-bottom: 1px solid rgba(0,0,0,0.05); }
        .product-card { background: white; border-radius: 24px; transition: all 0.3s ease; border: 1px solid rgba(0,0,0,0.02); }
        .admin-only { display: none !important; }
        .is-admin .admin-only { display: flex !important; }
        #imageOverlay { display: none; position: fixed; inset: 0; z-index: 300; background: rgba(0,0,0,0.9); align-items: center; justify-content: center; backdrop-filter: blur(20px); }
        .category-header { background: #e2e8f0; color: #475569; padding: 8px 16px; border-radius: 12px; font-size: 12px; font-weight: 900; margin: 20px 0 10px 0; display: inline-block; scroll-margin-top: 150px; }
        .ads-grid { display: flex; overflow-x: auto; gap: 12px; padding-bottom: 10px; scrollbar-width: none; }
        .ad-item { min-width: 280px; height: 150px; border-radius: 20px; overflow: hidden; flex-shrink: 0; position: relative; }
        .ad-item img { width: 100%; height: 100%; object-fit: cover; }
        .cat-chip { flex-shrink: 0; padding: 8px 16px; border-radius: 50px; background: white; font-size: 11px; font-weight: bold; border: 1px solid #e2e8f0; cursor: pointer; }
        .cat-chip.active { background: #0f172a; color: white; border-color: #0f172a; }
        .no-scrollbar::-webkit-scrollbar { display: none; }
    </style>
</head>
<body class="pb-40">

    <div id="imageOverlay" onclick="this.style.display='none'"><img id="fullImg" src=""></div>

    <div id="loginScreen" class="fixed inset-0 z-[100] bg-[#0f172a] flex flex-col items-center justify-center p-8 text-center">
        <h1 class="text-6xl font-black text-white italic mb-4 tracking-tighter">hoco.</h1>
        <p class="text-white/60 mb-10 font-bold">أهلاً بك في هوكو إليت ألترا</p>
        <div class="w-full max-w-xs space-y-4">
            <input type="password" id="passInput" placeholder="الرمز السري" class="w-full p-6 rounded-[2rem] text-center text-xl font-bold bg-white/5 border border-white/10 text-white outline-none">
            <button onclick="checkLogin()" class="w-full bg-white text-[#0f172a] py-6 rounded-[2rem] font-black shadow-2xl">دخول للنظام</button>
        </div>
    </div>

    <div id="mainContent" class="hidden">
        <header class="glass sticky top-0 z-50">
            <div class="p-4 flex justify-between items-center px-6">
                <div class="w-20"><button onclick="document.getElementById('adminUI').classList.toggle('hidden')" class="admin-only bg-slate-200 text-slate-800 p-2 rounded-xl text-[10px] font-bold">إدارة</button></div>
                <h1 class="text-2xl font-black italic tracking-tighter">hoco.</h1>
                <div class="w-20 text-left"><button onclick="location.reload()" class="bg-red-50 text-red-600 px-4 py-2 rounded-xl text-[10px] font-bold">خروج</button></div>
            </div>
            
            <div class="px-4 pb-3">
                <input type="text" id="searchInput" oninput="filterProducts()" placeholder="ابحث عن منتج أو كود..." class="w-full p-3 bg-slate-100 rounded-2xl text-sm outline-none font-bold">
            </div>
            <div id="catNav" class="flex gap-2 overflow-x-auto px-4 pb-3 no-scrollbar"></div>
        </header>

        <div class="p-4">
            <div id="adsContainer" class="ads-grid mb-4"></div>

            <div id="adminUI" class="hidden mb-10 p-6 glass rounded-[2.5rem] shadow-2xl border-2 border-white">
                <div class="flex bg-slate-100 p-1 rounded-2xl mb-6">
                    <button onclick="switchTab('prod')" class="flex-1 py-3 font-bold text-[10px] rounded-xl transition-all" id="btnP">المنتجات</button>
                    <button onclick="switchTab('ads')" class="flex-1 py-3 font-bold text-[10px] rounded-xl transition-all text-slate-400" id="btnA">الإعلانات</button>
                    <button onclick="switchTab('reports'); loadReports();" class="flex-1 py-3 font-bold text-[10px] rounded-xl transition-all text-slate-400" id="btnR">التقارير</button>
                </div>
                
                <div id="prodTab" class="space-y-3">
                    <input type="text" id="addCat" placeholder="الصنف" class="w-full p-4 rounded-2xl bg-white text-sm font-bold">
                    <input type="text" id="addName" placeholder="اسم المنتج" class="w-full p-4 rounded-2xl bg-white text-sm font-bold">
                    <div class="grid grid-cols-2 gap-2">
                        <input type="text" id="addId" placeholder="كود المنتج" class="p-4 rounded-2xl bg-white text-sm font-bold">
                        <input type="number" id="addPrice" placeholder="السعر" class="p-4 rounded-2xl bg-white text-sm font-bold">
                    </div>
                    <label class="flex items-center gap-2 p-3 bg-white rounded-2xl shadow-sm"><input type="checkbox" id="isHot" class="w-5 h-5 accent-orange-500"><span class="text-xs font-bold text-slate-600">عرض خاص 🔥</span></label>
                    <button onclick="document.getElementById('fileImg').click()" class="w-full py-4 border-2 border-dashed border-slate-300 rounded-2xl text-slate-500 font-bold text-xs bg-white">📸 رفع صورة منتج</button>
                    <input type="file" id="fileImg" accept="image/*" class="hidden" onchange="previewImage(this, 'imgPreview')">
                    <img id="imgPreview" class="hidden w-24 h-24 mx-auto rounded-2xl object-cover mt-2">
                    <button onclick="uploadProduct()" class="w-full bg-[#0f172a] text-white py-5 rounded-2xl font-black mt-4 shadow-xl">حفظ المنتج</button>
                </div>

                <div id="adsTab" class="hidden space-y-3">
                    <button onclick="document.getElementById('fileAd').click()" class="w-full py-10 border-2 border-dashed border-amber-300 rounded-2xl text-amber-600 font-bold text-xs bg-amber-50">🖼️ رفع صورة إعلان</button>
                    <input type="file" id="fileAd" accept="image/*" class="hidden" onchange="previewImage(this, 'adPreview')">
                    <img id="adPreview" class="hidden w-full h-40 rounded-2xl object-cover mt-2 shadow-md">
                    <button onclick="uploadAd()" class="w-full bg-amber-500 text-white py-5 rounded-2xl font-black shadow-xl">نشر الإعلان</button>
                </div>

                <div id="reportsTab" class="hidden space-y-3">
                    <div class="bg-blue-900 text-white p-4 rounded-2xl mb-4">
                        <p class="text-[10px] opacity-70">إجمالي المبيعات المسجلة</p>
                        <h3 id="totalSalesAmount" class="text-xl font-black">0 د.ع</h3>
                    </div>
                    <div id="reportsList" class="space-y-2 max-h-60 overflow-y-auto pr-2"></div>
                </div>
            </div>

            <div id="itemsGrid"></div>
        </div>
    </div>

    <div id="cartBar" style="display: none;" class="fixed bottom-6 left-4 right-4 bg-slate-900 text-white p-6 rounded-[2.5rem] shadow-2xl z-50 flex justify-between items-center max-w-lg mx-auto">
        <div class="flex flex-col"><span id="cartCount" class="font-black text-lg text-yellow-400">0 مواد</span><span id="totalPriceDisplay" class="text-xs text-white/60">0 د.ع</span></div>
        <button onclick="showCart()" class="bg-white text-[#0f172a] px-10 py-4 rounded-2xl text-sm font-black">عرض السلة</button>
    </div>

    <div id="cartModal" class="fixed inset-0 bg-black/40 backdrop-blur-xl z-[60] hidden flex items-center justify-center p-4">
        <div class="bg-white rounded-[3rem] w-full max-w-md p-8 shadow-2xl flex flex-col max-h-[95vh] overflow-y-auto">
            <h2 class="text-2xl font-black text-center mb-6 text-slate-800 tracking-tighter italic">hoco. Order</h2>
            <div id="cartList" class="space-y-3 mb-6"></div>
            <div class="flex justify-between items-center mb-6 px-4 py-4 bg-slate-100 rounded-3xl">
                <span class="font-black text-slate-800">المجموع النهائي:</span>
                <span id="finalTotal" class="text-xl font-black text-maroon">0 د.ع</span>
            </div>
            <div class="space-y-3 mb-6">
                <input type="text" id="customerName" placeholder="اسم المحل أو العميل *" class="w-full p-4 rounded-2xl bg-slate-50 font-bold text-sm outline-none">
                <div class="flex gap-2">
                    <input type="text" id="customerLoc" placeholder="رابط الموقع" class="flex-1 p-4 rounded-2xl bg-slate-50 font-bold text-[10px] outline-none">
                    <button onclick="getLocation()" class="bg-blue-600 text-white px-4 rounded-2xl text-[10px] font-bold">📍</button>
                </div>
                <input type="text" id="customerPoint" placeholder="أقرب نقطة دالة *" class="w-full p-4 rounded-2xl bg-slate-50 font-bold text-sm outline-none">
                <input type="number" id="customerPhone2" placeholder="رقم هاتف احتياطي (اختياري)" class="w-full p-4 rounded-2xl bg-slate-50 font-bold text-sm outline-none">
            </div>
            <div class="grid grid-cols-2 gap-2">
                <button onclick="sendWhatsApp('9647870037165', 'ودود')" class="bg-[#25D366] text-white p-4 rounded-2xl text-[10px] font-black italic">ودود</button>
                <button onclick="sendWhatsApp('9647737774427', 'سمر')" class="bg-[#25D366] text-white p-4 rounded-2xl text-[10px] font-black italic">سمر</button>
                <button onclick="sendWhatsApp('9647722897766', 'غادة')" class="bg-[#25D366] text-white p-4 rounded-2xl text-[10px] font-black italic">غادة</button>
                <button onclick="sendWhatsApp('9647870037167', 'مينا')" class="bg-[#25D366] text-white p-4 rounded-2xl text-[10px] font-black italic">مينا</button>
            </div>
            <button onclick="document.getElementById('cartModal').classList.add('hidden')" class="mt-4 text-slate-400 font-bold text-xs underline">رجوع</button>
        </div>
    </div>

    <script type="module">
        import { addDoc, onSnapshot, query, orderBy, doc, deleteDoc, updateDoc, getDocs } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js";

        let cart = [];
        let base64Img = "";
        let allProducts = [];

        window.checkLogin = () => {
            const pass = document.getElementById('passInput').value;
            if(pass === "1234") { document.body.classList.add('is-admin'); startApp(); }
            else if(pass === "5555") { startApp(); } else { alert("الرمز خطأ"); }
        };

        function startApp() {
            document.getElementById('loginScreen').style.display = 'none';
            document.getElementById('mainContent').style.display = 'block';
            loadAds();
            loadProducts();
            alert("أهلاً بك في نظام hoco. المطور ✅");
        }

        window.getLocation = () => {
            if (navigator.geolocation) {
                navigator.geolocation.getCurrentPosition((pos) => {
                    document.getElementById('customerLoc').value = `https://www.google.com/maps?q=${pos.coords.latitude},${pos.coords.longitude}`;
                    alert("تم تحديد الموقع ✅");
                });
            }
        };

        function loadAds() {
            onSnapshot(query(window.dbAds, orderBy("time", "desc")), (snap) => {
                const cont = document.getElementById('adsContainer'); cont.innerHTML = '';
                snap.forEach(d => {
                    cont.innerHTML += `<div class="ad-item">
                        <img src="${d.data().img}" onclick="zoomImg('${d.data().img}')">
                        <div class="admin-only absolute top-2 left-2"><button onclick="deleteAd('${d.id}')" class="bg-red-600 text-white p-1 rounded-full text-[8px]">✕ حذف</button></div>
                    </div>`;
                });
            });
        }

        function loadProducts() {
            onSnapshot(query(window.dbCol, orderBy("time", "desc")), (snap) => {
                allProducts = [];
                snap.forEach(d => allProducts.push({id: d.id, ...d.data()}));
                renderUI(allProducts);
                renderCatNav();
            });
        }

        function renderUI(data) {
            const grid = document.getElementById('itemsGrid'); grid.innerHTML = '';
            const groups = {};
            data.forEach(itm => { if(!groups[itm.cat]) groups[itm.cat] = []; groups[itm.cat].push(itm); });

            for(const cat in groups) {
                let html = `<div id="section-${cat}" class="category-header">${cat}</div><div class="grid grid-cols-2 gap-3 mb-6">`;
                groups[cat].forEach(item => {
                    html += `<div class="product-card p-3 flex flex-col relative shadow-sm">
                        ${item.hot ? '<div class="absolute top-2 left-2 bg-orange-500 text-white text-[7px] font-black px-2 py-1 rounded-lg z-10">HOT 🔥</div>' : ''}
                        
                        <div class="admin-only absolute top-2 right-2 flex flex-col gap-1 z-20">
                            <button onclick="updateProdImg('${item.id}')" class="bg-blue-600 text-white w-6 h-6 rounded-lg text-[8px]">📷</button>
                            <button onclick="deleteItem('${item.id}')" class="bg-red-600 text-white w-6 h-6 rounded-lg text-[8px]">✕</button>
                        </div>

                        <div class="h-24 flex items-center justify-center mb-2 bg-slate-50 rounded-xl overflow-hidden" onclick="zoomImg('${item.img}')">
                            <img src="${item.img || 'https://placehold.co/200'}" class="max-h-full object-contain">
                        </div>
                        <h2 class="text-[10px] font-bold text-slate-700 h-8 line-clamp-2 leading-tight mb-1">${item.name}</h2>
                        
                        <div class="flex items-center gap-1 mb-2">
                            <span class="text-maroon font-black text-xs">${item.price.toLocaleString()} د.ع</span>
                            <button onclick="editPrice('${item.id}', ${item.price})" class="admin-only text-blue-500 text-[8px]">📝 تعديل</button>
                        </div>

                        <div class="mb-2">
                            <input type="number" id="qty-${item.id}" value="1" min="1" class="w-full p-2 bg-slate-100 rounded-xl text-center font-bold text-xs border-none outline-none focus:ring-1 ring-slate-400">
                        </div>
                        <button onclick="addToCart('${item.id}', '${item.name}', ${item.price}, '${item.code}')" class="bg-slate-700 text-white py-3 rounded-xl text-[9px] font-black">إضافة</button>
                    </div>`;
                });
                grid.innerHTML += html + `</div>`;
            }
        }

        window.addToCart = (id, name, price, code) => {
            const qty = parseInt(document.getElementById(`qty-${id}`).value);
            const exist = cart.find(i => i.id === id);
            if(exist) exist.qty += qty; else cart.push({id, name, price, code, qty});
            updateCartBar();
        };

        function updateCartBar() {
            document.getElementById('cartBar').style.display = 'flex';
            document.getElementById('cartCount').innerText = cart.length + " مواد";
            document.getElementById('totalPriceDisplay').innerText = cart.reduce((s, i) => s + (i.price * i.qty), 0).toLocaleString() + " د.ع";
        }

        window.showCart = () => {
            const list = document.getElementById('cartList'); list.innerHTML = '';
            const total = cart.reduce((s, i) => s + (i.price * i.qty), 0);
            cart.forEach(i => { 
                list.innerHTML += `<div class="flex justify-between items-center bg-slate-50 p-3 rounded-2xl border border-slate-200">
                    <div class="text-[10px] font-bold">${i.name} <br> <span class="text-blue-600">العدد: ${i.qty}</span></div>
                    <div class="text-maroon font-black text-xs">${(i.price * i.qty).toLocaleString()}</div>
                </div>`; 
            });
            document.getElementById('finalTotal').innerText = total.toLocaleString() + " د.ع";
            document.getElementById('cartModal').classList.remove('hidden');
        };

        window.sendWhatsApp = async (phone, adminName) => {
            const name = document.getElementById('customerName').value;
            const point = document.getElementById('customerPoint').value;
            if(!name || !point) return alert("يرجى ملء الاسم ونقطة الدالة");

            let total = 0, msg = `🧾 *طلب جديد - hoco. Elite*\n👤 *المحل:* ${name}\n📍 *نقطة دالة:* ${point}\n`;
            cart.forEach((i, idx) => { 
                let sub = i.price * i.qty; total += sub; 
                msg += `*${idx+1}.* ${i.name} | العدد: ${i.qty} | السعر: ${sub.toLocaleString()}\n`; 
            });
            msg += `💰 *المجموع: ${total.toLocaleString()} د.ع*`;

            // تسجيل التقرير
            await addDoc(window.dbSales, { 
                customer: name, 
                total: total, 
                seller: adminName, 
                time: Date.now(),
                dateStr: new Date().toLocaleDateString('ar-EG')
            });

            window.open(`https://wa.me/${phone}?text=${encodeURIComponent(msg)}`);
        };

        // وظائف الإدارة
        window.switchTab = (tab) => {
            const isP = tab === 'prod', isA = tab === 'ads', isR = tab === 'reports';
            document.getElementById('prodTab').classList.toggle('hidden', !isP);
            document.getElementById('adsTab').classList.toggle('hidden', !isA);
            document.getElementById('reportsTab').classList.toggle('hidden', !isR);
        };

        window.loadReports = async () => {
            const snap = await getDocs(query(window.dbSales, orderBy("time", "desc")));
            const list = document.getElementById('reportsList'); list.innerHTML = '';
            let totalAll = 0;
            snap.forEach(d => {
                const s = d.data(); totalAll += s.total;
                list.innerHTML += `<div class="bg-white p-3 rounded-xl border text-[9px] shadow-sm">
                    <div class="flex justify-between font-black"><span>${s.customer}</span> <span class="text-maroon">${s.total.toLocaleString()}</span></div>
                    <div class="flex justify-between opacity-60"><span>البائع: ${s.seller}</span> <span>${s.dateStr}</span></div>
                </div>`;
            });
            document.getElementById('totalSalesAmount').innerText = totalAll.toLocaleString() + " د.ع";
        };

        window.editPrice = async (id, old) => { const n = prompt("السعر الجديد:", old); if(n) await updateDoc(doc(window.dbDirect, "products", id), { price: Number(n) }); };
        window.updateProdImg = (id) => {
            const input = document.createElement('input'); input.type = 'file'; input.accept = 'image/*';
            input.onchange = (e) => {
                const reader = new FileReader();
                reader.onload = async (re) => { await updateDoc(doc(window.dbDirect, "products", id), { img: re.target.result }); alert("تم تحديث الصورة"); };
                reader.readAsDataURL(e.target.files[0]);
            };
            input.click();
        };

        window.filterProducts = () => {
            const term = document.getElementById('searchInput').value.toLowerCase();
            renderUI(allProducts.filter(p => p.name.toLowerCase().includes(term) || p.code.toLowerCase().includes(term)));
        };

        window.filterProductsByCat = (cat, el) => {
            document.querySelectorAll('.cat-chip').forEach(c => c.classList.remove('active'));
            el.classList.add('active');
            if(cat === 'الكل') renderUI(allProducts);
            else { const sec = document.getElementById(`section-${cat}`); if(sec) sec.scrollIntoView({ behavior: 'smooth' }); }
        };

        window.renderCatNav = () => {
            const nav = document.getElementById('catNav');
            const cats = [...new Set(allProducts.map(p => p.cat))];
            nav.innerHTML = `<div class="cat-chip active" onclick="filterProductsByCat('الكل', this)">الكل</div>`;
            cats.forEach(c => nav.innerHTML += `<div class="cat-chip" onclick="filterProductsByCat('${c}', this)">${c}</div>`);
        };

        window.previewImage = (input, targetId) => {
            const reader = new FileReader();
            reader.onload = (e) => { base64Img = e.target.result; const prev = document.getElementById(targetId); prev.src = base64Img; prev.classList.remove('hidden'); };
            reader.readAsDataURL(input.files[0]);
        };
        window.uploadAd = async () => { if(base64Img) { await addDoc(window.dbAds, { img: base64Img, time: Date.now() }); location.reload(); } };
        window.uploadProduct = async () => {
            const n = document.getElementById('addName').value, p = document.getElementById('addPrice').value, id = document.getElementById('addId').value, c = document.getElementById('addCat').value || "عام", hot = document.getElementById('isHot').checked;
            if(n && p) { await addDoc(window.dbCol, { name: n, price: Number(p), code: id, cat: c, img: base64Img, hot: hot, time: Date.now() }); location.reload(); }
        };
        window.deleteItem = async (id) => { if(confirm("حذف؟")) await deleteDoc(doc(window.dbDirect, "products", id)); };
        window.deleteAd = async (id) => { if(confirm("حذف؟")) await deleteDoc(doc(window.dbDirect, "ads", id)); };
        window.zoomImg = (src) => { if(src) { document.getElementById('fullImg').src = src; document.getElementById('imageOverlay').style.display = 'flex'; } };
    </script>
</body>
</html>
