<!doctype html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>—</title>
  <style>
    :root{
      --bg:#0f1724; --card:#0b1220; --accent:#06b6d4; --accent-2:#7c3aed; --muted:#9aa6bf; --text:#e6eef8;
      --danger:#fb7185; --radius:14px;
    }
    @media (prefers-color-scheme: light){
      :root{
        --bg:#f6f8fb; --card:#ffffff; --accent:#06b6d4; --accent-2:#2563eb; --muted:#6b7280; --text:#0b1220; --danger:#ef4444;
      }
    }

    *{box-sizing:border-box; -webkit-tap-highlight-color: transparent}
    html,body{height:100%;margin:0;font-family:Inter,system-ui,Arial; background:var(--bg); color:var(--text); -webkit-font-smoothing:antialiased}
    .app{max-width:480px;margin:0 auto;height:100vh;display:flex;flex-direction:column}

    .content{flex:1;overflow:auto;padding:12px 12px 120px}

    .card{display:flex;gap:10px;align-items:center;background:var(--card);padding:10px;border-radius:12px;margin-bottom:10px;box-shadow:0 6px 20px rgba(2,6,23,0.35);border:1px solid rgba(255,255,255,0.02);transition:transform .14s}
    .thumb{width:64px;height:64px;border-radius:10px;object-fit:cover;border:1px solid rgba(255,255,255,0.03)}
    .info{flex:1;min-width:0}
    .name{font-weight:700;font-size:15px;white-space:nowrap;overflow:hidden;text-overflow:ellipsis}
    .meta{font-size:13px;color:var(--muted);margin-top:6px}
    .card-actions{display:flex;gap:8px;margin-left:8px}

    .bottom-bar{position:fixed;inset: auto 12px 12px 12px;display:flex;justify-content:space-between;gap:8px;z-index:60}
    .fab-wrap{display:flex;flex-direction:column;align-items:center;gap:6px}
    .fab-label{font-size:12px;color:var(--muted);background:transparent;padding:2px 6px;border-radius:8px}
    .fab{width:56px;height:56px;border-radius:14px;background:linear-gradient(180deg,var(--accent-2),var(--accent));display:flex;align-items:center;justify-content:center;color:white;font-size:26px;box-shadow:0 10px 30px rgba(2,6,23,0.6);border:0;cursor:pointer}
    .mini-btn{height:56px;border-radius:14px;padding:0 14px;background:rgba(255,255,255,0.03);display:flex;gap:8px;align-items:center;color:var(--text);border:1px solid rgba(255,255,255,0.03)}
    .mini-btn svg{width:22px;height:22px}

    .sheet-backdrop{position:fixed;inset:0;background:rgba(0,0,0,0.45);display:none;align-items:flex-end;justify-content:center;z-index:80}
    .sheet{width:100%;max-width:480px;background:var(--card);border-top-left-radius:16px;border-top-right-radius:16px;padding:14px;border:1px solid rgba(255,255,255,0.03);transform:translateY(100%);transition:transform .28s cubic-bezier(.2,.9,.2,1)}
    .sheet.show{transform:translateY(0)}
    .sheet .row{display:flex;gap:8px;margin-bottom:10px}
    .input{flex:1;padding:12px;border-radius:10px;border:1px solid rgba(255,255,255,0.04);background:transparent;color:var(--text)}
    .btn-full{width:100%;padding:12px;border-radius:12px;border:0;background:linear-gradient(90deg,var(--accent),var(--accent-2));color:#fff;font-weight:700}
    .menu-list{display:flex;flex-direction:column;gap:8px}
    .menu-item{padding:12px;border-radius:10px;background:rgba(255,255,255,0.02);display:flex;align-items:center;gap:10px;cursor:pointer;border:1px solid rgba(255,255,255,0.02)}

    .search-row{display:flex;gap:8px;margin-bottom:12px}
    .search-input{flex:1;padding:12px;border-radius:12px;border:1px solid rgba(255,255,255,0.04);background:transparent;color:var(--text)}
    .select{padding:12px;border-radius:12px;border:1px solid rgba(255,255,255,0.04);background:transparent;color:var(--text)}

    .toast{position:fixed;left:50%;transform:translateX(-50%);bottom:140px;background:#111827;color:#fff;padding:10px 14px;border-radius:10px;display:none;z-index:90}

    @keyframes popIn { from{transform:translateY(10px) scale(.98);opacity:0} to{transform:none;opacity:1} }
    .pop{animation:popIn .26s ease both}

    .empty{display:flex;flex-direction:column;gap:10px;align-items:center;justify-content:center;padding:28px;border-radius:12px;background:linear-gradient(180deg,rgba(255,255,255,0.02),transparent);color:var(--muted);text-align:center}
    @media (max-width:420px){ .fab{width:52px;height:52px;border-radius:12px;font-size:22px} .mini-btn{height:52px;border-radius:12px} .thumb{width:56px;height:56px} }
  </style>
</head>
<body>
  <div class="app" id="app">
    <div class="content" id="content">
      <div class="search-row">
        <input id="search" class="search-input" placeholder="ابحث..." aria-label="بحث">
        <select id="filter" class="select" aria-label="فلتر">
          <option value="">كل الأنواع</option>
        </select>
      </div>

      <div id="listArea"></div>
      <div id="empty" class="empty" style="display:none">
        <svg width="88" height="88" viewBox="0 0 24 24" fill="none" style="opacity:.85"><path d="M12 2v20M2 12h20" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/></svg>
        <div>لا توجد منتجات بعد</div>
        <div style="font-size:13px;color:var(--muted)">اضغط زر + لإضافة منتج أو نوع</div>
      </div>
    </div>

    <div class="bottom-bar" role="toolbar" aria-label="أدوات">
      <div class="fab-wrap">
        <div class="fab-label">تصدير</div>
        <button id="btnExport" class="mini-btn" title="تصدير">
          <svg viewBox="0 0 24 24" fill="none"><path d="M12 3v12" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"/><path d="M8 7l4-4 4 4" stroke="currentColor" stroke-width="1.6" stroke-linecap="round" stroke-linejoin="round"/><path d="M21 21H3" stroke="currentColor" stroke-width="1.6" stroke-linecap="round"/></svg>
        </button>
      </div>

      <div class="fab-wrap">
        <div class="fab-label">إضافة نوع</div>
        <button id="btnAddBrand" class="fab" title="أضف نوع (+)">+</button>
      </div>

      <div class="fab-wrap">
        <div class="fab-label">إضافة منتج</div>
        <button id="btnAddProd" class="fab" title="أضف منتج (+)">✚</button>
      </div>

      <div class="fab-wrap">
        <div class="fab-label">المزيد</div>
        <button id="btnMenu" class="mini-btn" title="المزيد">
          <svg viewBox="0 0 24 24" fill="none"><circle cx="12" cy="5" r="1.6" fill="currentColor"/><circle cx="12" cy="12" r="1.6" fill="currentColor"/><circle cx="12" cy="19" r="1.6" fill="currentColor"/></svg>
        </button>
      </div>
    </div>

    <!-- sheets -->
    <div id="sheetBrandBackdrop" class="sheet-backdrop" aria-hidden="true">
      <div class="sheet" role="dialog" aria-modal="true" id="sheetBrand">
        <div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:10px">
          <svg width="28" height="28" viewBox="0 0 24 24" fill="none"><path d="M12 5v14M5 12h14" stroke="currentColor" stroke-width="1.6" stroke-linecap="round"/></svg>
          <button id="closeBrand" class="mini-btn" style="height:36px;padding:6px 8px">إغلاق</button>
        </div>
        <div class="row">
          <input id="brandName" class="input" placeholder="اسم النوع" aria-label="اسم النوع"/>
        </div>
        <div style="display:flex;gap:8px">
          <button id="saveBrand" class="btn-full">حفظ النوع</button>
        </div>
      </div>
    </div>

    <div id="sheetProdBackdrop" class="sheet-backdrop" aria-hidden="true">
      <div class="sheet" role="dialog" aria-modal="true" id="sheetProd">
        <div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:10px">
          <svg width="28" height="28" viewBox="0 0 24 24" fill="none"><rect x="4" y="4" width="16" height="16" rx="2" stroke="currentColor" stroke-width="1.6"/></svg>
          <button id="closeProd" class="mini-btn" style="height:36px;padding:6px 8px">إغلاق</button>
        </div>

        <div class="row"><input id="prdName" class="input" placeholder="اسم المنتج" /></div>
        <div class="row"><input id="prdPrice" class="input" type="number" placeholder="السعر" /></div>
        <div class="row"><select id="prdBrand" class="input"></select></div>
        <div class="row"><input id="prdImg" class="input" placeholder="رابط صورة (اختياري)" /></div>
        <div style="display:flex;gap:8px;margin-top:10px">
          <button id="saveProd" class="btn-full">حفظ</button>
        </div>
      </div>
    </div>

    <!-- more menu sheet -->
    <div id="sheetMoreBackdrop" class="sheet-backdrop" aria-hidden="true">
      <div class="sheet" role="dialog" aria-modal="true" id="sheetMore">
        <div style="display:flex;justify-content:space-between;align-items:center;margin-bottom:10px">
          <svg width="28" height="28" viewBox="0 0 24 24" fill="none"><path d="M4 6h16M4 12h16M4 18h16" stroke="currentColor" stroke-width="1.6" stroke-linecap="round"/></svg>
          <button id="closeMore" class="mini-btn" style="height:36px;padding:6px 8px">إغلاق</button>
        </div>

        <div class="menu-list">
          <div id="menuExportJSON" class="menu-item">تصدير JSON</div>
          <div id="menuExportCSV" class="menu-item">تصدير CSV</div>
          <label class="menu-item" id="menuImportLabel" style="cursor:pointer">استيراد
            <input id="importFile" type="file" accept=".json,.csv" style="display:none" />
          </label>
          <div id="menuClear" class="menu-item" style="color:var(--danger)">مسح التخزين المحلي</div>
          <div id="menuAbout" class="menu-item">حول التطبيق</div>
        </div>
      </div>
    </div>

    <div id="toast" class="toast" role="status" aria-live="polite"></div>
  </div>

  <script>
    // مفاتيح
    const K_BR = 'mobile_brands_mob', K_PR = 'mobile_products_mob', K_LAST = 'mobile_last_del_mob';

    // عناصر DOM
    const listArea = document.getElementById('listArea'), emptyEl = document.getElementById('empty');
    const searchEl = document.getElementById('search'), filterEl = document.getElementById('filter');

    const btnAddBrand = document.getElementById('btnAddBrand'), btnAddProd = document.getElementById('btnAddProd'), btnMenu = document.getElementById('btnMenu'), btnExport = document.getElementById('btnExport');
    const sheetBrandBackdrop = document.getElementById('sheetBrandBackdrop'), brandName = document.getElementById('brandName'), saveBrand = document.getElementById('saveBrand'), closeBrand = document.getElementById('closeBrand');
    const sheetProdBackdrop = document.getElementById('sheetProdBackdrop'), prdName = document.getElementById('prdName'), prdPrice = document.getElementById('prdPrice'), prdBrand = document.getElementById('prdBrand'), prdImg = document.getElementById('prdImg'), saveProd = document.getElementById('saveProd'), closeProd = document.getElementById('closeProd');

    const sheetMoreBackdrop = document.getElementById('sheetMoreBackdrop'), closeMore = document.getElementById('closeMore');
    const menuExportJSON = document.getElementById('menuExportJSON'), menuExportCSV = document.getElementById('menuExportCSV'), menuImportLabel = document.getElementById('menuImportLabel'), importFile = document.getElementById('importFile');
    const menuClear = document.getElementById('menuClear'), menuAbout = document.getElementById('menuAbout');
    const toastEl = document.getElementById('toast');

    // data + defaults
    let brands = [], products = [], editingId = null;
    const defaults = ['سامسونج','آيفون','هواوي','شاومي','انفنكس','تكنو','بوفا'];

    // load/save
    function load(){
      const storedBrands = JSON.parse(localStorage.getItem(K_BR) || 'null');
      // merge stored with defaults, unique, keep stored order then defaults for missing
      if(Array.isArray(storedBrands)){
        const merged = [...storedBrands];
        for(const d of defaults) if(!merged.includes(d)) merged.push(d);
        brands = Array.from(new Set(merged));
      } else brands = defaults.slice();
      products = JSON.parse(localStorage.getItem(K_PR) || 'null') || [];
      renderAll();
    }
    function save(){
      localStorage.setItem(K_BR, JSON.stringify(brands));
      localStorage.setItem(K_PR, JSON.stringify(products));
    }

    // render
    function renderAll(){
      renderBrands();
      renderProducts();
    }

    function renderBrands(){
      filterEl.innerHTML = '<option value="">كل الأنواع</option>';
      prdBrand.innerHTML = '<option value="">اختر نوع</option>';
      for(const b of brands){
        const opt = document.createElement('option'); opt.value = b; opt.textContent = b;
        filterEl.appendChild(opt); prdBrand.appendChild(opt.cloneNode(true));
      }
    }

    function renderProducts(){
      const q = (searchEl.value || '').trim().toLowerCase();
      const f = filterEl.value;
      const filtered = products.filter(p=>{
        if(f && p.brand !== f) return false;
        if(!q) return true;
        return (p.name||'').toLowerCase().includes(q) || (p.note||'').toLowerCase().includes(q);
      });

      listArea.innerHTML = '';
      emptyEl.style.display = filtered.length ? 'none' : (products.length ? 'none' : 'flex');

      filtered.forEach(p => {
        const el = document.createElement('div'); el.className = 'card pop';
        el.innerHTML = `
          <img class="thumb" src="${escapeAttr(p.image || placeholder())}" alt="">
          <div class="info">
            <div class="name">${escape(p.name)}</div>
            <div class="meta">${escape(p.brand||'')} • ${Number(p.price||0).toLocaleString()} ر.س</div>
          </div>
          <div class="card-actions">
            <button data-id="${p.id}" class="mini edit" title="تعديل" style="background:transparent;border:0;color:var(--text)">✎</button>
            <button data-id="${p.id}" class="mini del" title="حذف" style="background:transparent;border:0;color:var(--danger)">🗑</button>
          </div>
        `;
        el.querySelector('.edit').addEventListener('click', ()=> openEdit(p.id));
        el.querySelector('.del').addEventListener('click', ()=> deleteWithUndo(p.id, el));
        listArea.appendChild(el);
      });
    }

    function placeholder(){ return 'data:image/svg+xml;utf8,'+encodeURIComponent('<svg xmlns="http://www.w3.org/2000/svg" width="200" height="200"><rect width="100%" height="100%" fill="#eef2ff"/><text x="50%" y="50%" dominant-baseline="middle" text-anchor="middle" fill="#93c5fd" font-size="14">صورة</text></svg>'); }

    // sheets open/close
    btnAddBrand.addEventListener('click', ()=> openBrandSheet());
    function openBrandSheet(){ sheetBrandBackdrop.style.display='flex'; sheetBrandBackdrop.querySelector('.sheet').classList.add('show'); brandName.focus(); }
    closeBrand.addEventListener('click', ()=> closeBrandSheet());
    function closeBrandSheet(){ sheetBrandBackdrop.style.display='none'; sheetBrandBackdrop.querySelector('.sheet').classList.remove('show'); brandName.value = ''; }

    btnAddProd.addEventListener('click', ()=> openProdSheet());
    function openProdSheet(){ editingId = null; prdName.value=''; prdPrice.value=''; prdImg.value=''; prdBrand.selectedIndex = 0; sheetProdBackdrop.style.display='flex'; sheetProdBackdrop.querySelector('.sheet').classList.add('show'); prdName.focus(); }
    closeProd.addEventListener('click', ()=> closeProdSheet());
    function closeProdSheet(){ sheetProdBackdrop.style.display='none'; sheetProdBackdrop.querySelector('.sheet').classList.remove('show'); }

    // menu ("المزيد")
    btnMenu.addEventListener('click', ()=> openMoreSheet());
    function openMoreSheet(){ sheetMoreBackdrop.style.display='flex'; sheetMoreBackdrop.querySelector('.sheet').classList.add('show'); }
    closeMore.addEventListener('click', ()=> closeMoreSheet());
    function closeMoreSheet(){ sheetMoreBackdrop.style.display='none'; sheetMoreBackdrop.querySelector('.sheet').classList.remove('show'); }

    // brand save
    saveBrand.addEventListener('click', ()=>{
      const v = (brandName.value||'').trim();
      if(!v) return toast('أدخل اسم النوع', true);
      if(brands.includes(v)) return toast('النوع موجود', true);
      brands.unshift(v);
      brands = Array.from(new Set(brands));
      save(); renderBrands(); closeBrandSheet(); toast('تمت الإضافة');
    });

    // product save
    saveProd.addEventListener('click', ()=>{
      const name = (prdName.value||'').trim();
      const price = parseFloat(prdPrice.value) || 0;
      const brand = prdBrand.value;
      const image = (prdImg.value||'').trim();
      if(!name || !brand) return toast('الاسم والنوع مطلوبان', true);
      if(editingId){
        const idx = products.findIndex(p=>p.id===editingId);
        if(idx !== -1){ products[idx] = {...products[idx], name, price, brand, image}; toast('تم التعديل'); }
      } else {
        const id = Date.now().toString(36) + Math.random().toString(36).slice(2,6);
        products.unshift({ id, name, price, brand, image, created: new Date().toISOString() });
        toast('تمت الإضافة');
      }
      save(); renderProducts(); closeProdSheet();
    });

    function openEdit(id){
      const p = products.find(x=>x.id===id); if(!p) return toast('غير موجود', true);
      editingId = id;
      prdName.value = p.name; prdPrice.value = p.price; prdBrand.value = p.brand; prdImg.value = p.image || '';
      openProdSheet();
    }

    // delete with undo
    function deleteWithUndo(id, cardEl){
      cardEl.style.transition = 'opacity .26s, transform .26s'; cardEl.style.opacity = '0'; cardEl.style.transform = 'translateY(-10px) scale(.98)';
      const idx = products.findIndex(p=>p.id===id);
      if(idx === -1) return;
      const removed = products.splice(idx,1)[0];
      localStorage.setItem(K_LAST, JSON.stringify(removed));
      save();
      setTimeout(()=> { renderProducts(); showUndo('تم الحذف', undoDelete); }, 260);
    }
    function undoDelete(){
      const last = localStorage.getItem(K_LAST);
      if(!last) return toast('لا يوجد ما يُرجع', true);
      const obj = JSON.parse(last);
      products.unshift(obj); localStorage.removeItem(K_LAST); save(); renderProducts(); toast('تم الاسترجاع');
    }

    // export/import & clear
    function downloadJSON(obj, filename='data.json'){
      const blob = new Blob([JSON.stringify(obj, null, 2)], {type:'application/json'});
      const url = URL.createObjectURL(blob); const a = document.createElement('a'); a.href = url; a.download = filename; a.click(); URL.revokeObjectURL(url);
    }
    function downloadCSV(items, filename='data.csv'){
      if(!items.length) return toast('لا توجد بيانات', true);
      const headers = ['id','name','price','brand','image','note','created'];
      const lines = [headers.join(',')].concat(items.map(p => headers.map(h => csvSafe(p[h])).join(',')));
      const blob = new Blob([lines.join('\n')], {type:'text/csv;charset=utf-8;'});
      const url = URL.createObjectURL(blob); const a = document.createElement('a'); a.href = url; a.download = filename; a.click(); URL.revokeObjectURL(url);
    }
    function csvSafe(v){ if(v===null||v===undefined) return ''; const s = String(v).replace(/"/g,'""'); return `"${s}"`; }

    menuExportJSON.addEventListener('click', ()=>{
      closeMoreSheet(); if(!products.length) return toast('لا توجد بيانات', true);
      downloadJSON(products, `products_${new Date().toISOString().slice(0,10)}.json`); toast('تم تصدير JSON');
    });
    menuExportCSV.addEventListener('click', ()=>{ closeMoreSheet(); if(!products.length) return toast('لا توجد بيانات', true); downloadCSV(products, `products_${new Date().toISOString().slice(0,10)}.csv`); toast('تم تصدير CSV'); });

    // import
    importFile.addEventListener('change', handleImport);
    menuImportLabel.addEventListener('click', ()=> importFile.click());
    function handleImport(e){
      const file = e.target.files[0]; if(!file) return;
      const reader = new FileReader();
      reader.onload = (ev)=>{
        const txt = ev.target.result;
        if(file.name.endsWith('.json')){
          try{
            const data = JSON.parse(txt);
            if(Array.isArray(data)){
              // merge products, avoid id collisions
              for(const it of data){
                const id = it.id && !products.find(p=>p.id===it.id) ? it.id : Date.now().toString(36)+Math.random().toString(36).slice(2,6);
                products.unshift({ id, name: it.name||'', price: Number(it.price||0), brand: it.brand||defaults[0], image: it.image||'', note: it.note||'', created: it.created||new Date().toISOString() });
              }
              save(); renderProducts(); toast('تم استيراد JSON (مصفوفة)');
            } else if(typeof data === 'object'){
              const id = data.id && !products.find(p=>p.id===data.id) ? data.id : Date.now().toString(36)+Math.random().toString(36).slice(2,6);
              products.unshift({ id, name: data.name||'', price: Number(data.price||0), brand: data.brand||defaults[0], image: data.image||'', note: data.note||'', created: data.created||new Date().toISOString() });
              save(); renderProducts(); toast('تم استيراد عنصر JSON');
            } else toast('تنسيق JSON غير معروف', true);
          } catch(err){ toast('خطأ في قراءة JSON', true); }
        } else if(file.name.endsWith('.csv')){
          const rows = txt.split(/\r?\n/).filter(Boolean);
          const header = rows.shift().split(',');
          const items = rows.map(r=>{
            const cols = parseCSVLine(r);
            const obj = {}; header.forEach((h,i)=> obj[h.trim()] = cols[i] || '');
            const id = obj.id && !products.find(p=>p.id===obj.id) ? obj.id : Date.now().toString(36)+Math.random().toString(36).slice(2,6);
            return { id, name: obj.name||'', price: Number(obj.price||0), brand: obj.brand||defaults[0], image: obj.image||'', note: obj.note||'', created: obj.created || new Date().toISOString() };
          });
          products = items.concat(products); save(); renderProducts(); toast('تم استيراد CSV');
        } else toast('نوع ملف غير مدعوم', true);
      };
      reader.readAsText(file,'utf-8');
      e.target.value = '';
      closeMoreSheet();
    }
    function parseCSVLine(line){ const re = /"(?:[^"]|"")*"|[^,]+/g; const m = line.match(re) || []; return m.map(s=> s.replace(/^"|"$/g,'').replace(/""/g,'"').trim()); }

    // clear storage
    menuClear.addEventListener('click', ()=>{
      if(!confirm('سيؤدي مسح التخزين المحلي إلى فقدان كل البيانات. متابعة؟')) return;
      localStorage.removeItem(K_PR); localStorage.removeItem(K_BR); localStorage.removeItem(K_LAST);
      brands = defaults.slice(); products = []; save(); renderAll(); closeMoreSheet(); toast('تم مسح التخزين المحلي');
    });

    // about
    menuAbout.addEventListener('click', ()=>{
      closeMoreSheet();
      toast('تطبيق تخزين محلي بسيط — يعمل بدون خادم');
    });

    // handlers
    btnExport.addEventListener('click', ()=> { if(!products.length) return toast('لا توجد بيانات', true); downloadJSON(products, `products_${new Date().toISOString().slice(0,10)}.json`); toast('تم التصدير'); });

    searchEl.addEventListener('input', renderProducts);
    filterEl.addEventListener('change', renderProducts);

    // helper functions
    function toast(msg, isError=false){
      toastEl.textContent = msg; toastEl.style.display = 'block'; toastEl.style.background = isError ? 'rgba(220,38,38,0.95)' : '#111827';
      setTimeout(()=>{ toastEl.style.display = 'none'; toastEl.textContent = ''; }, 2000);
    }
    function showUndo(msg, onUndo){
      toastEl.innerHTML = '';
      const txt = document.createElement('span'); txt.textContent = msg;
      const btn = document.createElement('button'); btn.textContent = 'تراجع';
      btn.onclick = ()=> { onUndo(); toastEl.style.display='none'; toastEl.innerHTML=''; };
      toastEl.appendChild(txt); toastEl.appendChild(btn);
      toastEl.style.display = 'block';
      setTimeout(()=>{ if(toastEl.style.display !== 'none'){ toastEl.style.display='none'; toastEl.innerHTML=''; } }, 4500);
    }

    function escape(s){ return String(s||''); }
    function escapeAttr(s){ return String(s||'').replace(/"/g,'&quot;'); }

    // open/close sheet backdrops by tapping outside
    sheetBrandBackdrop.addEventListener('click', (e)=>{ if(e.target === sheetBrandBackdrop) closeBrandSheet(); });
    sheetProdBackdrop.addEventListener('click', (e)=>{ if(e.target === sheetProdBackdrop) closeProdSheet(); });
    sheetMoreBackdrop.addEventListener('click', (e)=>{ if(e.target === sheetMoreBackdrop) closeMoreSheet(); });

    // keyboard shortcuts (desktop)
    window.addEventListener('keydown', (e)=>{
      if(e.key === '/') { e.preventDefault(); searchEl.focus(); }
      if(e.key === 'b') { openBrandSheet(); brandName.focus(); }
      if(e.key === 'n') { openProdSheet(); prdName.focus(); }
      if(e.key === 'u') { const last = localStorage.getItem(K_LAST); if(last) undoDelete(); }
    });

    // initial load
    load();
  </script>
</body>
</html>
