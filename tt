<!doctype html>
<html lang="ar">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>مدير القصة — Story Manager</title>
<style>
  :root{font-family:system-ui,-apple-system,"Segoe UI",Roboto,"Helvetica Neue",Arial;direction:rtl}
  body{margin:0;background:#f5f7fb;color:#0f1724}
  header{background:linear-gradient(90deg,#7c3aed,#06b6d4);color:white;padding:14px 16px}
  .container{max-width:980px;margin:18px auto;padding:12px}
  .grid{display:grid;grid-template-columns:1fr 1fr;gap:12px}
  .card{background:white;border-radius:10px;padding:12px;box-shadow:0 6px 18px rgba(12,19,41,0.06)}
  h2{margin:6px 0 12px;font-size:16px}
  label{display:block;font-size:13px;margin-top:8px}
  input[type=text], textarea, select{width:100%;padding:8px;margin-top:6px;border:1px solid #e6eef8;border-radius:8px;font-size:14px}
  textarea{min-height:110px;resize:vertical}
  button{background:#0ea5a4;color:white;border:0;padding:8px 10px;border-radius:8px;font-weight:600;cursor:pointer;margin-top:8px}
  .btn-ghost{background:transparent;border:1px solid #e6eef8;color:#0f1724}
  .list{max-height:260px;overflow:auto;padding-right:6px}
  .item{padding:8px;border-radius:8px;border:1px solid #f0f4f8;margin-bottom:8px;display:flex;justify-content:space-between;align-items:center}
  small{color:#475569}
  .full{grid-column:1/3}
  .mono{font-family:ui-monospace, SFMono-Regular, Menlo, Monaco, "Roboto Mono", monospace}
  footer{max-width:980px;margin:14px auto;padding:10px;text-align:center;color:#475569}
  @media(max-width:720px){.grid{grid-template-columns:1fr}.full{grid-column:1}}
</style>
</head>
<body>
<header>
  <strong>مدير القصة</strong>
  <div style="font-size:13px;margin-top:6px">احفظ الفصول، الشخصيات، والأركات — وراجع الاتساق قبل أي فصل جديد</div>
</header>

<div class="container">
  <div class="grid">
    <div class="card">
      <h2>1) الشخصيات</h2>
      <div>
        <label>اسم الشخصية</label>
        <input id="charName" type="text" placeholder="مثال: أميرة" />
        <label>دور / ملاحظات</label>
        <input id="charNotes" type="text" placeholder="مثال: البطلة، شجاعة" />
        <button id="addCharBtn">أضف/حدّث الشخصية</button>
      </div>
      <div class="list" id="charsList" style="margin-top:10px"></div>
    </div>

    <div class="card">
      <h2>2) الأركات (Arcs)</h2>
      <label>اسم الأرك</label>
      <input id="arcTitle" type="text" placeholder="مثال: بداية الرحلة" />
      <label>ملخص قصير</label>
      <input id="arcSummary" type="text" placeholder="لمحة عن الأرك" />
      <button id="addArcBtn">أضف/حدّث الأرك</button>
      <div class="list" id="arcsList" style="margin-top:10px"></div>
    </div>

    <div class="card full">
      <h2>3) الفصول</h2>
      <label>رقم/معرّف الفصل</label>
      <input id="chapterId" type="text" placeholder="مثال: ch001" />
      <label>عنوان الفصل</label>
      <input id="chapterTitle" type="text" placeholder="مثال: البوابة" />
      <label>اختر الأرك</label>
      <select id="chapterArc"></select>
      <label>نص/ملخص الفصل</label>
      <textarea id="chapterText" placeholder="انسخ هنا نصّ الفصل أو ملخصه"></textarea>
      <button id="saveChapterBtn">حفظ الفصل</button>
      <div style="margin-top:10px;display:flex;gap:8px;flex-wrap:wrap">
        <button id="listChaptersBtn" class="btn-ghost">عرض كل الفصول</button>
        <button id="consistencyBtn" class="btn-ghost">فحص الاتساق</button>
        <button id="exportBtn" class="btn-ghost">تصدير JSON</button>
        <button id="importBtn" class="btn-ghost">استيراد JSON</button>
      </div>
      <div id="chaptersList" class="list" style="margin-top:10px"></div>
    </div>
  </div>
</div>

<footer>تمّ التطوير بواسطة مُنشئ القالب — يمكنك التصدير والنسخ الاحتياطي. استخدم "فحص الاتساق" قبل أي فصل جديد.</footer>

<input id="fileInput" type="file" style="display:none" accept="application/json" />

<script>
(function(){
  // نموذج بيانات بسيط في localStorage
  const STORAGE_KEY = "story_manager_v1";
  let state = { characters: [], arcs: [], chapters: [] };

  function saveState(){ localStorage.setItem(STORAGE_KEY, JSON.stringify(state)); }
  function loadState(){ const s = localStorage.getItem(STORAGE_KEY); if(s) state = JSON.parse(s); renderAll(); }

  // عناصر DOM
  const charName = document.getElementById("charName");
  const charNotes = document.getElementById("charNotes");
  const addCharBtn = document.getElementById("addCharBtn");
  const charsList = document.getElementById("charsList");

  const arcTitle = document.getElementById("arcTitle");
  const arcSummary = document.getElementById("arcSummary");
  const addArcBtn = document.getElementById("addArcBtn");
  const arcsList = document.getElementById("arcsList");

  const chapterId = document.getElementById("chapterId");
  const chapterTitle = document.getElementById("chapterTitle");
  const chapterArc = document.getElementById("chapterArc");
  const chapterText = document.getElementById("chapterText");
  const saveChapterBtn = document.getElementById("saveChapterBtn");
  const listChaptersBtn = document.getElementById("listChaptersBtn");
  const chaptersList = document.getElementById("chaptersList");
  const consistencyBtn = document.getElementById("consistencyBtn");
  const exportBtn = document.getElementById("exportBtn");
  const importBtn = document.getElementById("importBtn");
  const fileInput = document.getElementById("fileInput");

  // رندر القوائم
  function renderChars(){
    charsList.innerHTML = "";
    state.characters.forEach((c, idx) => {
      const div = document.createElement("div"); div.className="item";
      div.innerHTML = `<div><strong>${c.name}</strong><div><small>${c.notes||''}</small></div></div>
        <div style="display:flex;gap:8px">
          <button class="btn-ghost" data-idx="${idx}" onclick="editChar(${idx})">تحرير</button>
          <button class="btn-ghost" data-idx="${idx}" onclick="deleteChar(${idx})">حذف</button>
        </div>`;
      charsList.appendChild(div);
    });
  }
  function renderArcs(){
    arcsList.innerHTML = "";
    chapterArc.innerHTML = '<option value="">-- بلا أرك --</option>';
    state.arcs.forEach((a, idx) => {
      const div = document.createElement("div"); div.className="item";
      div.innerHTML = `<div><strong>${a.title}</strong><div><small>${a.summary||''}</small></div></div>
        <div style="display:flex;gap:8px">
          <button class="btn-ghost" onclick="editArc(${idx})">تحرير</button>
          <button class="btn-ghost" onclick="deleteArc(${idx})">حذف</button>
        </div>`;
      arcsList.appendChild(div);

      const opt = document.createElement("option"); opt.value = a.id || a.title; opt.textContent = a.title;
      chapterArc.appendChild(opt);
    });
  }
  function renderChapters(){
    chaptersList.innerHTML = "";
    state.chapters.forEach((ch, idx) => {
      const div = document.createElement("div"); div.className="item";
      div.innerHTML = `<div style="max-width:70%"><strong>${ch.id} — ${ch.title||''}</strong>
        <div><small>أرك: ${ch.arc||'-'}</small></div></div>
        <div style="display:flex;gap:8px">
          <button class="btn-ghost" onclick="loadChapter(${idx})">فتح</button>
          <button class="btn-ghost" onclick="deleteChapter(${idx})">حذف</button>
        </div>`;
      chaptersList.appendChild(div);
    });
  }

  // واجهات تحرير مُبسطة (صامِد؛ نعرِّف الدوال على window لتسهيل النداء من innerHTML)
  window.editChar = function(i){
    const c = state.characters[i];
    charName.value = c.name;
    charNotes.value = c.notes || "";
    addCharBtn.dataset.edit = i;
    addCharBtn.textContent = "حفظ التعديل";
  };
  window.deleteChar = function(i){
    if(!confirm("حذف الشخصية نهائيًا؟")) return;
    state.characters.splice(i,1); saveState(); renderAll();
  };
  window.editArc = function(i){
    const a = state.arcs[i];
    arcTitle.value = a.title;
    arcSummary.value = a.summary || "";
    addArcBtn.dataset.edit = i;
    addArcBtn.textContent = "حفظ تعديل الأرك";
  };
  window.deleteArc = function(i){
    if(!confirm("حذف الأرك نهائيًا؟")) return;
    state.arcs.splice(i,1); saveState(); renderAll();
  };
  window.loadChapter = function(i){
    const c = state.chapters[i];
    chapterId.value = c.id; chapterTitle.value = c.title||""; chapterArc.value = c.arc||"";
    chapterText.value = c.text||"";
    saveChapterBtn.dataset.edit = i;
    saveChapterBtn.textContent = "حفظ تعديل الفصل";
    window.scrollTo({top:0,behavior:'smooth'});
  };
  window.deleteChapter = function(i){
    if(!confirm("حذف الفصل نهائيًا؟")) return;
    state.chapters.splice(i,1); saveState(); renderAll();
  };

  // الأحداث
  addCharBtn.addEventListener("click", ()=>{
    const name = charName.value.trim(); if(!name){ alert("أدخل اسم الشخصية"); return; }
    const notes = charNotes.value.trim();
    const edit = addCharBtn.dataset.edit;
    if(edit!==undefined){
      state.characters[edit] = { name, notes };
      addCharBtn.removeAttribute("data-edit"); addCharBtn.textContent = "أضف/حدّث الشخصية";
    } else {
      state.characters.push({ name, notes });
    }
    charName.value=""; charNotes.value=""; saveState(); renderAll();
  });

  addArcBtn.addEventListener("click", ()=>{
    const title = arcTitle.value.trim(); if(!title){ alert("أدخل اسم الأرك"); return; }
    const summary = arcSummary.value.trim();
    const edit = addArcBtn.dataset.edit;
    if(edit!==undefined){
      state.arcs[edit] = { id: state.arcs[edit].id || title, title, summary };
      addArcBtn.removeAttribute("data-edit"); addArcBtn.textContent = "أضف/حدّث الأرك";
    } else {
      state.arcs.push({ id: title, title, summary });
    }
    arcTitle.value=""; arcSummary.value=""; saveState(); renderAll();
  });

  saveChapterBtn.addEventListener("click", ()=>{
    const id = chapterId.value.trim(); if(!id){ alert("أدخل معرّف الفصل (مثل ch001)"); return; }
    const title = chapterTitle.value.trim();
    const arc = chapterArc.value || "";
    const text = chapterText.value.trim();
    const edit = saveChapterBtn.dataset.edit;
    const payload = { id, title, arc, text, updatedAt: new Date().toISOString() };
    if(edit!==undefined){
      state.chapters[edit] = payload;
      saveChapterBtn.removeAttribute("data-edit"); saveChapterBtn.textContent = "حفظ الفصل";
    } else {
      // استبدال إن وجد نفس id
      const idx = state.chapters.findIndex(c=>c.id===id);
      if(idx>=0){ if(!confirm("معرّف موجود. هل تريد الاستبدال؟")) return; state.chapters[idx]=payload; }
      else state.chapters.push(payload);
    }
    chapterId.value=""; chapterTitle.value=""; chapterText.value=""; saveState(); renderAll();
  });

  listChaptersBtn.addEventListener("click", ()=>renderChapters());

  // فحص اتساق بسيط
  consistencyBtn.addEventListener("click", ()=>{
    const report = [];
    const names = state.characters.map(c=>c.name);
    if(names.length===0) report.push("لا توجد شخصيات في القاعدة. أضف شخصيات أولاً.");
    state.chapters.forEach(ch=>{
      const missing = names.filter(n => !ch.text || !ch.text.includes(n));
      if(missing.length>0) report.push(`الفصل ${ch.id} ('${ch.title||'-'}') لا يذكر: ${missing.join(", ")}`);
      // فحوصات أخرى ممكن إضافتها (تواريخ متناقضة، ذكر أحداث متناقضة...)
    });
    if(report.length===0) alert("لا ملاحظات آلية الآن — كل الشخصيات ذُكرت على الأقل في فصولك.");
    else alert("تقرير الفحص:\n\n" + report.join("\n"));
  });

  // تصدير واستيراد
  exportBtn.addEventListener("click", ()=>{
    const blob = new Blob([JSON.stringify(state, null, 2)], {type:"application/json;charset=utf-8"});
    const url = URL.createObjectURL(blob);
    const a = document.createElement("a"); a.href = url; a.download = "story_backup.json"; document.body.appendChild(a); a.click();
    a.remove(); URL.revokeObjectURL(url);
  });
  importBtn.addEventListener("click", ()=> fileInput.click());
  fileInput.addEventListener("change", (e)=>{
    const f = e.target.files[0]; if(!f) return;
    const reader = new FileReader();
    reader.onload = function(){ try{ const j = JSON.parse(reader.result); state = j; saveState(); renderAll(); alert("تم الاستيراد"); } catch(err){ alert("خطأ في ملف JSON"); } }
    reader.readAsText(f);
  });

  // رندر عام
  function renderAll(){ renderChars(); renderArcs(); renderChapters(); saveState(); }
  loadState();
})();
</script>
</body>
</html>
