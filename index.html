<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>hoco. Elite Cloud</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Tajawal:wght@400;700;900&display=swap" rel="stylesheet">
    
    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-app.js";
        import { getFirestore, collection, addDoc, onSnapshot, query, orderBy } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js";

        // بياناتك الحقيقية من الصور
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
    </script>

    <style>
        :root { --primary: #0f172a; --maroon: #800000; --bg: #fdfaf5; }
        body { font-family: 'Tajawal', sans-serif; background-color: var(--bg); color: var(--primary); -webkit-tap-highlight-color: transparent; }
        .product-card { background: white; border-radius: 25px; border: 1px solid rgba(0,0,0,0.05); box-shadow: 0 2px 10px rgba(0,0,0,0.02); }
        .qty-btn { width: 32px; height: 32px; background: #f1f5f9; border-radius: 10px; font-weight: bold; display: flex; align-items: center; justify-content: center; }
        #loginScreen, #mainContent { display: none; }
        .active-view { display: flex !important; }
        .active-block { display: block !important; }
    </style>
</head>
<body class="pb-40">

    <div id="loginScreen" class="fixed inset-0 z-[100] bg-[#0f172a] flex flex-col items-center justify-center p-8 active-view">
        <h1 class="text-6xl font-black text-white italic mb-10 tracking-tighter">hoco.</h1>
        <div class="w-full max-w-xs space-y-4">
            <input type="password" id="passInput" placeholder="رمز الدخول" class="w-full p-5 rounded-3xl text-center text-xl font-bold bg-white/10 border border-white/20 text-white outline-none">
            <button onclick="checkLogin()" class="w-full bg-white text-[#0f172a] py-5 rounded-3xl font-black shadow-2xl active:scale-95 transition-all">دخول</button>
        </div>
    </div>

    <div id="mainContent" class="flex-col w-full">
        <header class="bg-white/90 backdrop-blur-md sticky top-0 z-50 p-5 border-b flex justify-between items-center">
            <h1 class="text-3xl font-black text-maroon italic">hoco.</h1>
            <div class="flex gap-2">
                <button id="adminBtn" onclick="toggleAdmin()" class="hidden bg-[#0f172a] text-white px-4 py-2 rounded-xl text-[10px] font-bold">إضافة منتج</button>
                <button onclick="location.reload()" class="bg-rose-50 text-rose-600 px-4 py-2 rounded-xl text-[10px] font-bold">خروج</button>
            </div>
        </header>

        <div id="adminUI" class="hidden m-4 p-6 bg-white rounded-[35px] shadow-2xl border-2 border-slate-100 animate-in fade-in zoom-in duration-300">
            <h3 class="text-xs font-black text-slate-400 mb-4 uppercase text-center tracking-widest">إضافة منتج جديد للسحاب</h3>
            <div class="space-y-3">
                <input type="text" id="addName" placeholder="اسم المنتج" class="w-full p-4 rounded-2xl bg-slate-50 border outline-none font-bold text-sm">
                <div class="grid grid-cols-2 gap-2">
                    <input type="text" id="addId" placeholder="الكود" class="p-4 rounded-2xl bg-slate-50 border outline-none font-bold text-sm">
                    <input type="text" id="addPrice" placeholder="السعر" class="p-4 rounded-2xl bg-slate-50 border outline-none font-bold text-sm">
                </div>
                <input type="text" id="addCat" placeholder="الصنف" class="w-full p-4 rounded-2xl bg-slate-50 border outline-none font-bold text-sm">
                <input type="text" id="addImg" placeholder="رابط صورة المنتج (URL)" class="w-full p-4 rounded-2xl bg-blue-50 border-blue-100 outline-none font-bold text-[10px]">
                <button onclick="uploadToCloud()" class="w-full bg-[#0f172a] text-white py-4 rounded-2xl font-black shadow-lg active:scale-95 transition-all">حفظ ومزامنة ☁️</button>
            </div>
        </div>

        <div id="itemsGrid" class="p-5 grid grid-cols-2 gap-4"></div>
    </div>

    <div id="cartBar" style="display: none;" class="fixed left-6 right-6 bottom-8 bg-[#0f172a] text-white p-5 rounded-[30px] shadow-2xl z-50 flex justify-between items-center max-w-sm mx-auto">
        <span id="cartCount" class="font-black text-sm text-yellow-400">0 مواد مختارة</span>
        <button onclick="openCart()" class="bg-maroon text-white px-8 py-3.5 rounded-2xl text-xs font-black shadow-lg">عرض العربة</button>
    </div>

    <div id="cartModal" class="fixed inset-0 bg-[#0f172a]/70 backdrop-blur-md z-[60] hidden flex items-center justify-center p-4">
        <div class="bg-white rounded-[40px] w-full max-w-sm p-8 flex flex-col max-h-[85vh] shadow-2xl overflow-hidden">
            <h2 class="text-xl font-black text-center mb-6 text-slate-800 italic">قائمة طلبك</h2>
            <div id="cartList" class="overflow-y-auto flex-1 space-y-2 mb-6 pr-1"></div>
            
            <p class="text-[9px] text-center font-bold text-slate-400 uppercase mb-3">اختر مندوباً لإرسال الطلب</p>
            <div class="grid grid-cols-2 gap-2 mb-4">
                <button onclick="sendOrder('9647737774427')" class="bg-[#25D366] text-white py-4 rounded-2xl text-[10px] font-black shadow-md flex flex-col items-center"><span>سمر</span></button>
                <button onclick="sendOrder('9647722897766')" class="bg-[#25D366] text-white py-4 rounded-2xl text-[10px] font-black shadow-md flex flex-col items-center"><span>غادة</span></button>
                <button onclick="sendOrder('9647870037165')" class="bg-[#25D366] text-white py-4 rounded-2xl text-[10px] font-black shadow-md flex flex-col items-center"><span>ودوود</span></button>
                <button onclick="sendOrder('9647770037167')" class="bg-[#25D366] text-white py-4 rounded-2xl text-[10px] font-black shadow-md flex flex-col items-center"><span>مينا</span></button>
            </div>
            <button onclick="document.getElementById('cartModal').classList.add('hidden')" class="text-slate-300 text-[10px] font-bold py-2">رجوع</button>
        </div>
    </div>

    <script type="module">
        import { addDoc, onSnapshot, query, orderBy } from "https://www.gstatic.com/firebasejs/10.7.1/firebase-firestore.js";

        window.checkLogin = function() {
            const p = document.getElementById('passInput').value;
            if(p === "1234") { document.getElementById('adminBtn').classList.remove('hidden'); proceed(); }
            else if(p === "5555") { proceed(); }
            else { alert("رمز الدخول غير صحيح"); }
        }

        function proceed() {
            document.getElementById('loginScreen').classList.remove('active-view');
            document.getElementById('mainContent').classList.add('active-block');
            loadCloudData();
        }

        window.uploadToCloud = async function() {
            const n = document.getElementById('addName').value;
            const p = document.getElementById('addPrice').value;
            const c = document.getElementById('addCat').value;
            const id = document.getElementById('addId').value;
            const im = document.getElementById('addImg').value;

            if(n && p) {
                try {
                    await addDoc(window.dbCol, { name: n, price: p, cat: c, code: id, img: im, time: Date.now() });
                    alert("تم حفظ المنتج بنجاح في السحاب! ☁️");
                    document.getElementById('adminUI').classList.add('hidden');
                } catch(e) { alert("فشل الحفظ: تأكد من تفعيل قواعد Firestore"); }
            }
        }

        function loadCloudData() {
            const q = query(window.dbCol, orderBy("time", "desc"));
            onSnapshot(q, (snap) => {
                const grid = document.getElementById('itemsGrid');
                grid.innerHTML = '';
                snap.forEach((doc) => {
                    const item = doc.data();
                    grid.innerHTML += `
                        <div class="product-card p-4 flex flex-col animate-in fade-in duration-500">
                            <img src="${item.img || 'https://placehold.co/200/f8fafc/0f172a?text=hoco.'}" class="w-full h-28 object-contain mb-3 rounded-2xl bg-slate-50">
                            <h2 class="text-[10px] font-bold text-slate-800 h-8 overflow-hidden leading-tight">${item.name}</h2>
                            <p class="text-maroon font-black text-xs mt-1 mb-3">${item.price} د.ع</p>
                            <div class="flex items-center justify-between bg-slate-50 p-1 rounded-xl mb-3 border border-slate-100">
                                <button onclick="window.changeQty('${doc.id}', -1)" class="qty-btn text-slate-400">−</button>
                                <span id="qty-${doc.id}" class="text-xs font-black">1</span>
                                <button onclick="window.changeQty('${doc.id}', 1)" class="qty-btn text-[#0f172a]">＋</button>
                            </div>
                            <button onclick="window.addToCart('${doc.id}', '${item.name}', '${item.code}')" class="w-full bg-[#0f172a] text-white py-3 rounded-2xl text-[9px] font-black shadow-md active:scale-95 transition-all">إضافة للسلة</button>
                        </div>
                    `;
                });
            });
        }

        let cart = [];
        window.changeQty = (id, d) => {
            let el = document.getElementById(`qty-${id}`);
            let v = parseInt(el.innerText) + d;
            if(v < 1) v = 1;
            el.innerText = v;
        }

        window.addToCart = (id, name, code) => {
            const qty = parseInt(document.getElementById(`qty-${id}`).innerText);
            const exists = cart.find(c => c.code === code);
            if(exists) exists.qty += qty; else cart.push({name, code, qty});
            
            document.getElementById('cartBar').style.display = 'flex';
            document.getElementById('cartCount').innerText = cart.length + " مواد بالسلة";
            document.getElementById(`qty-${id}`).innerText = "1";
        }

        window.openCart = () => {
            const list = document.getElementById('cartList'); list.innerHTML = '';
            cart.forEach(i => {
                list.innerHTML += `
                <div class="p-4 bg-slate-50 rounded-2xl flex justify-between items-center border border-slate-100">
                    <div class="flex flex-col"><span class="text-[10px] font-black leading-tight">${i.name}</span><span class="text-[8px] text-slate-400">ID: ${i.code}</span></div>
                    <span class="bg-[#0f172a] text-yellow-400 px-3 py-1.5 rounded-xl text-[10px] font-black">× ${i.qty}</span>
                </div>`;
            });
            document.getElementById('cartModal').classList.remove('hidden');
        }

        window.sendOrder = (phone) => {
            if(cart.length === 0) return;
            let msg = "📦 *طلب جديد من hoco Elite:*\n\n";
            cart.forEach((i, idx) => msg += `*${idx+1}.* ${i.name}\nالعدد: ( ${i.qty} ) | الكود: ${i.code}\n\n`);
            window.open(`https://wa.me/${phone}?text=${encodeURIComponent(msg)}`);
        }

        window.toggleAdmin = () => document.getElementById('adminUI').classList.toggle('hidden');
    </script>
</body>
</html>
