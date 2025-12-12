<!doctype html>
<html lang="ar" dir="rtl">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>بطولة E-Football — موقع تجريبي</title>
  <style>
    :root{
      --bg:#0b0f15;
      --card:#0f1720;
      --muted:#94a3b8;
      --accent:#1fb6a4;
      --glass: rgba(255,255,255,0.02);
      --accent-2:#16a34a;
      --danger:#ff6b6b;
    }
    *{box-sizing:border-box;font-family:Inter, "Noto Sans Arabic", system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;}
    html,body{height:100%;margin:0;background:linear-gradient(180deg,#071024 0%,var(--bg)100%);color:#e6eef6}
    a{color:var(--accent)}
    .container{max-width:1100px;margin:18px auto;padding:16px}
    header{display:flex;align-items:center;gap:14px}
    .logo{width:56px;height:56px;border-radius:10px;background:linear-gradient(135deg,var(--accent),#7c3aed);display:flex;align-items:center;justify-content:center;font-weight:800}
    h1{margin:0;font-size:20px}
    .tabs{display:flex;gap:18px;margin-top:12px}
    .tab{padding:8px 12px;border-radius:10px;background:transparent;color:var(--muted);cursor:pointer}
    .tab.active{background:linear-gradient(90deg,var(--accent),#34d399);color:#022; font-weight:700}

    .card{background:linear-gradient(180deg, rgba(255,255,255,0.02), rgba(255,255,255,0.01));padding:14px;border-radius:12px;margin-top:14px}
    .muted{color:var(--muted);font-size:13px}

    /* Matches list */
    .match{display:flex;align-items:center;justify-content:space-between;padding:10px;border-radius:10px;margin-bottom:10px;background:rgba(255,255,255,0.01)}
    .team{display:flex;align-items:center;gap:8px}
    .thumb{width:40px;height:40px;border-radius:8px;background:#123;display:flex;align-items:center;justify-content:center;font-weight:700}
    .match-date{background:rgba(255,255,255,0.03);padding:6px 10px;border-radius:8px;color:var(--muted);font-size:13px}

    /* Standings */
    table{width:100%;border-collapse:collapse}
    th,td{padding:10px;text-align:left}
    th{color:var(--muted);font-size:13px}
    tbody tr{border-top:1px solid rgba(255,255,255,0.02)}

    /* Knockout simple */
    .bracket{display:flex;gap:14px;flex-wrap:wrap;margin-top:12px}
    .slot{background:rgba(255,255,255,0.02);padding:8px;border-radius:10px;min-width:180px;text-align:center}

    /* Scorers */
    .scorer{display:flex;justify-content:space-between;padding:8px;border-radius:8px;background:rgba(255,255,255,0.01);margin-bottom:8px}

    /* Admin button fixed bottom */
    .admin-btn{
      position:fixed;left:18px;right:18px;bottom:18px;background:linear-gradient(90deg,var(--accent),#06b6d4);color:#042027;padding:12px;border-radius:14px;text-align:center;font-weight:700;cursor:pointer;box-shadow:0 8px 20px rgba(6,182,212,0.12)
    }

    /* Modal */
    .modal-back{position:fixed;inset:0;background:rgba(0,0,0,0.6);display:none;align-items:center;justify-content:center;padding:18px}
    .modal{background:var(--card);padding:16px;border-radius:12px;max-width:1000px;width:100%;color:#e6eef6;display:flex;gap:12px}
    .admin-col{flex:1;min-width:280px;max-height:80vh;overflow:auto}
    .field{display:flex;gap:8px;align-items:center;margin-bottom:10px}
    .field label{min-width:110px;color:var(--muted)}
    input[type="text"], select, input[type="date"], input[type="number"]{flex:1;padding:8px;border-radius:8px;border:1px solid rgba(255,255,255,0.03);background:transparent;color:inherit}
    button.primary{background:var(--accent);border:none;padding:8px 10px;border-radius:8px;color:#042027;cursor:pointer}
    button.ghost{background:transparent;border:1px solid rgba(255,255,255,0.04);color:var(--muted);padding:8px;border-radius:8px;cursor:pointer}
    .small{font-size:13px;color:var(--muted)}
    .logo-grid{display:grid;grid-template-columns:repeat(6,1fr);gap:8px;padding:6px;background:var(--glass);border-radius:8px}
    .logo-item{padding:8px;border-radius:8px;display:flex;flex-direction:column;align-items:center;gap:6px;cursor:pointer}
    .logo-item.selected{outline:3px solid rgba(6,182,212,0.14)}
    .logo-preview{width:60px;height:60px;border-radius:8px;background-size:cover;background-position:center}

    @media(max-width:900px){
      .logo-grid{grid-template-columns:repeat(4,1fr)}
      .modal{flex-direction:column}
    }
  </style>
</head>
<body>
  <div class="container">
    <header>
      <div class="logo">EF</div>
      <div>
        <h1>بطولة E-Football — العرض العام</h1>
        <div class="muted">واجهة عرض المباريات، الترتيب، وأقوى الهدافين — انزل لأسفل لفتح لوحة المشرف</div>
      </div>
    </header>

    <div class="tabs" id="navTabs">
      <div class="tab active" data-view="matches">Matches</div>
      <div class="tab" data-view="standings">Standings</div>
      <div class="tab" data-view="knockout">Knockout</div>
      <div class="tab" data-view="scorers">Scorers</div>
    </div>

    <div id="viewArea" class="card">
      <!-- dynamic content -->
    </div>
  </div>

  <div class="admin-btn" id="openAdminBtn">فتح لوحة المشرف — Admin Panel</div>

  <!-- Modal / Admin -->
  <div class="modal-back" id="modalBack">
    <div class="modal" role="dialog" aria-modal="true">
      <div class="admin-col" style="flex:0.7">
        <h3>لوحة المشرف</h3>
        <div id="adminAuthArea">
          <div class="field"><label>أدخل كلمة المرور</label><input id="adminPass" type="text" placeholder="أدخل كلمة المرور"/></div>
          <div style="display:flex;gap:8px"><button id="doLogin" class="primary">دخول</button><button id="closeModal" class="ghost">إلغاء</button></div>
          <div class="small" style="margin-top:8px">كلمة المرور الافتراضية: <b>admin123</b> — يمكنك تغييرها من Settings لاحقًا.</div>
        </div>

        <div id="adminPanel" style="display:none">
          <div style="display:flex;gap:8px;margin-top:10px">
            <button class="primary" id="tabTeams">فرق (Teams)</button>
            <button class="ghost" id="tabParticipants">المتبارون</button>
            <button class="ghost" id="tabMatches">المباريات</button>
            <button class="ghost" id="tabSettings">الإعدادات</button>
          </div>

          <div id="adminContent" style="margin-top:12px"></div>
        </div>
      </div>

      <div class="admin-col" style="flex:0.6">
        <h3>معاينة مباشرة</h3>
        <div id="livePreview" class="card small">لا يوجد شيء حتى الآن.</div>
      </div>
    </div>
  </div>

<script>
/* -------------------------
  Data model (localStorage)
----------------------------*/
const LS_TEAMS = "ef_teams_v1";
const LS_PARTS = "ef_parts_v1";
const LS_MATCHES = "ef_matches_v1";
const LS_SETTINGS = "ef_settings_v1";

// default teams (merged list you provided)
const DEFAULT_TEAMS = [
  "inter_milan","ac_milan","as_roma","napoli","atalanta","lazio","como",
  "fc_barcelona","real_madrid","celtic",
  "man_city","liverpool","arsenal","chelsea","man_united",
  "lille","psg","ol_lyon","as_monaco","olympique_marseille",
  "botafogo","fluminense","palmeiras","sao_paulo","santos","flamengo",
  "borussia_dortmund","bayer_leverkusen","bayern_munich","eintracht_frankfurt","fc_porto","sporting_cp"
];

// helper
const $ = id => document.getElementById(id);

// initialize data if missing
function initData(){
  if(!localStorage.getItem(LS_TEAMS)){
    const teams = DEFAULT_TEAMS.map(id=>({
      id, name: prettyName(id), iconUrl: null, group: null, stats: {played:0,wins:0,draws:0,losses:0,gf:0,ga:0,points:0}
    }));
    localStorage.setItem(LS_TEAMS, JSON.stringify(teams));
  }
  if(!localStorage.getItem(LS_PARTS)) localStorage.setItem(LS_PARTS, JSON.stringify([]));
  if(!localStorage.getItem(LS_MATCHES)) localStorage.setItem(LS_MATCHES, JSON.stringify([]));
  if(!localStorage.getItem(LS_SETTINGS)) localStorage.setItem(LS_SETTINGS, JSON.stringify({adminPass:"admin123"}));
}
initData();

function loadTeams(){ return JSON.parse(localStorage.getItem(LS_TEAMS) || "[]"); }
function saveTeams(t){ localStorage.setItem(LS_TEAMS, JSON.stringify(t)); }
function loadParts(){ return JSON.parse(localStorage.getItem(LS_PARTS) || "[]"); }
function saveParts(p){ localStorage.setItem(LS_PARTS, JSON.stringify(p)); }
function loadMatches(){ return JSON.parse(localStorage.getItem(LS_MATCHES) || "[]"); }
function saveMatches(m){ localStorage.setItem(LS_MATCHES, JSON.stringify(m)); }
function loadSettings(){ return JSON.parse(localStorage.getItem(LS_SETTINGS) || "{}"); }
function saveSettings(s){ localStorage.setItem(LS_SETTINGS, JSON.stringify(s)); }

function prettyName(id){
  return id.replace(/_/g," ").replace(/\b\w/g,ch=>ch.toUpperCase());
}

/* -------------------------
  UI: Tabs & Views
----------------------------*/
const tabs = document.querySelectorAll(".tab");
const viewArea = $("viewArea");
tabs.forEach(t => t.addEventListener("click", ()=> {
  tabs.forEach(x=>x.classList.remove("active"));
  t.classList.add("active");
  renderView(t.dataset.view);
}));
renderView("matches");

function renderView(view){
  if(view==="matches") renderMatchesView();
  else if(view==="standings") renderStandingsView();
  else if(view==="knockout") renderKnockoutView();
  else if(view==="scorers") renderScorersView();
}

/* -------------------------
  Matches view
----------------------------*/
function renderMatchesView(){
  const matches = loadMatches();
  const teams = loadTeams();
  let html = `<h2>Matches</h2><div class="muted">قائمة المباريات (يوم فقط) — سيتم تحديث النتائج من لوحة المشرف</div><div style="margin-top:12px">`;
  if(matches.length===0) html += `<div class="muted" style="padding:8px">لا توجد مباريات بعد.</div>`;
  matches.forEach(m=>{
    const home = teams.find(t=>t.id===m.homeTeamId);
    const away = teams.find(t=>t.id===m.awayTeamId);
    html += `<div class="match">
      <div class="team"><div class="thumb" style="background-image:url('${home? (home.iconUrl || placeholderFor(home.id)) : placeholderFor(null)}');background-size:cover"></div><div>${home?home.name:'-'}</div></div>
      <div class="match-date">${m.date || '-'}</div>
      <div class="team"><div>${m.homeGoals==null? '-' : m.homeGoals}</div><div style="margin:0 8px">:</div><div>${m.awayGoals==null? '-' : m.awayGoals}</div></div>
      <div class="team"><div style="margin-left:8px">${away?away.name:'-'}</div><div class="thumb" style="background-image:url('${away? (away.iconUrl || placeholderFor(away.id)) : placeholderFor(null)}');background-size:cover;margin-left:8px"></div></div>
    </div>`;
  });
  html += `</div>`;
  viewArea.innerHTML = html;
}

/* -------------------------
  Standings view (grouped by team.group)
----------------------------*/
function computeStandings(){
  // reset stats
  const teams = loadTeams();
  teams.forEach(t => t.stats = {played:0,wins:0,draws:0,losses:0,gf:0,ga:0,points:0});
  const matches = loadMatches();
  matches.forEach(m=>{
    if(m.homeGoals==null || m.awayGoals==null) return;
    const ht = teams.find(t=>t.id===m.homeTeamId);
    const at = teams.find(t=>t.id===m.awayTeamId);
    if(!ht || !at) return;
    ht.stats.played++; at.stats.played++;
    ht.stats.gf += m.homeGoals; ht.stats.ga += m.awayGoals;
    at.stats.gf += m.awayGoals; at.stats.ga += m.homeGoals;
    if(m.homeGoals > m.awayGoals){ ht.stats.wins++; at.stats.losses++; ht.stats.points += 3; }
    else if(m.homeGoals < m.awayGoals){ at.stats.wins++; ht.stats.losses++; at.stats.points += 3; }
    else { ht.stats.draws++; at.stats.draws++; ht.stats.points +=1; at.stats.points +=1; }
  });
  saveTeams(teams);
  return teams;
}

function renderStandingsView(){
  const teams = computeStandings();
  // group by team.group (if null put 'Ungrouped')
  const groups = {};
  teams.forEach(t=>{
    const g = t.group || "Ungrouped";
    groups[g] = groups[g] || [];
    groups[g].push(t);
  });
  // sort each group by points desc, then gd, then gf
  for(const g in groups){
    groups[g].sort((a,b)=>{
      if(b.stats.points !== a.stats.points) return b.stats.points - a.stats.points;
      const gda = a.stats.gf - a.stats.ga; const gdb = b.stats.gf - b.stats.ga;
      if(gdb !== gda) return gdb - gda;
      return b.stats.gf - a.stats.gf;
    });
  }
  let html = `<h2>Standings</h2><div class="muted">الترتيب حسب المجموعات (أو بدون مجموعات)</div>`;
  for(const g in groups){
    html += `<div style="margin-top:12px" class="card"><h3 style="margin:0 0 8px 0">${g}</h3>
      <table><thead><tr><th>#</th><th>Team</th><th>P</th><th>W</th><th>D</th><th>L</th><th>GF</th><th>GA</th><th>GD</th><th>Pts</th></tr></thead><tbody>`;
    groups[g].forEach((t,idx)=>{
      const gd = t.stats.gf - t.stats.ga;
      html += `<tr><td>${idx+1}</td><td><div style="display:flex;align-items:center;gap:8px"><div class="thumb" style="width:36px;height:36px;background-image:url('${t.iconUrl || placeholderFor(t.id)}');background-size:cover"></div>${t.name}</div></td><td>${t.stats.played}</td><td>${t.stats.wins}</td><td>${t.stats.draws}</td><td>${t.stats.losses}</td><td>${t.stats.gf}</td><td>${t.stats.ga}</td><td>${gd}</td><td>${t.stats.points}</td></tr>`;
    });
    html += `</tbody></table></div>`;
  }
  viewArea.innerHTML = html;
}

/* -------------------------
  Knockout view (simple)
----------------------------*/
function renderKnockoutView(){
  // We'll just show matches with stage containing "Quarter" / "Semi" / "Final"
  const matches = loadMatches();
  const teams = loadTeams();
  let html = `<h2>Knockout</h2><div class="muted">شجرة الأدوار الإقصائية (عرض مبسط)</div><div class="bracket">`;
  const stages = ["Quarter Final","Semi Final","Final","3rd Place"];
  stages.forEach(stage=>{
    const ms = matches.filter(m=>m.stage===stage);
    if(ms.length===0) return;
    html += `<div style="min-width:220px"><h4 style="margin:8px 0">${stage}</h4>`;
    ms.forEach(m=>{
      const ht = teams.find(t=>t.id===m.homeTeamId);
      const at = teams.find(t=>t.id===m.awayTeamId);
      html += `<div class="slot"><div style="display:flex;gap:8px;justify-content:center;align-items:center"><div style="width:36px;height:36px;border-radius:8px;background-image:url('${ht? (ht.iconUrl||placeholderFor(ht.id)) : placeholderFor(null)}');background-size:cover"></div><div>${ht?ht.name:'-'}</div></div>
        <div style="margin:6px 0">${m.homeGoals==null?'-':m.homeGoals} : ${m.awayGoals==null?'-':m.awayGoals}</div>
        <div style="display:flex;gap:8px;justify-content:center;align-items:center"><div style="width:36px;height:36px;border-radius:8px;background-image:url('${at? (at.iconUrl||placeholderFor(at.id)) : placeholderFor(null)}');background-size:cover"></div><div>${at?at.name:'-'}</div></div></div>`;
    });
    html += `</div>`;
  });
  html += `</div>`;
  viewArea.innerHTML = html;
}

/* -------------------------
  Scorers view (simple from participants stats)
----------------------------*/
function renderScorersView(){
  // For demo we use participants and their goals stored in parts.goals (if any)
  const parts = loadParts();
  let sorted = parts.slice().sort((a,b)=> (b.goals||0) - (a.goals||0));
  let html = `<h2>Top Scorers</h2><div class="muted">ترتيب الهدافين حسب الأهداف المسجلة في ملف المشاركين</div>`;
  if(sorted.length===0) html += `<div class="muted" style="padding:8px">لا يوجد بيانات للاعبين حتى الآن.</div>`;
  sorted.forEach(p=>{
    html += `<div class="scorer"><div>${p.name} <span class="small" style="margin-right:8px">${p.teamName || ''}</span></div><div>${p.goals || 0}</div></div>`;
  });
  viewArea.innerHTML = html;
}

/* -------------------------
  Admin modal behavior
----------------------------*/
const modalBack = $("modalBack");
$("openAdminBtn").addEventListener("click", ()=> modalBack.style.display = "flex");
$("closeModal").addEventListener("click", ()=> { modalBack.style.display="none"; resetAdminAuth(); });

function resetAdminAuth(){
  $("adminAuthArea").style.display = ""; $("adminPanel").style.display = "none"; $("adminPass").value="";
}

// login
$("doLogin").addEventListener("click", ()=>{
  const pass = $("adminPass").value;
  const settings = loadSettings();
  if(pass === settings.adminPass){
    $("adminAuthArea").style.display="none"; $("adminPanel").style.display="block";
    showAdminSection("teams");
    updateLivePreview("مرحباً بالمشرف — يمكنك التعديل الآن");
  } else alert("كلمة المرور خاطئة");
});

// admin tabs
$("tabTeams").addEventListener("click", ()=> showAdminSection("teams"));
$("tabParticipants").addEventListener("click", ()=> showAdminSection("parts"));
$("tabMatches").addEventListener("click", ()=> showAdminSection("matches"));
$("tabSettings").addEventListener("click", ()=> showAdminSection("settings"));

function showAdminSection(section){
  // reset button styles
  ["tabTeams","tabParticipants","tabMatches","tabSettings"].forEach(id=> $(id).className = id==="tabTeams" && section==="teams" ? "primary": "ghost");
  const content = $("adminContent");
  if(section==="teams"){
    renderAdminTeams(content);
  } else if(section==="parts"){
    renderAdminParts(content);
  } else if(section==="matches"){
    renderAdminMatches(content);
  } else if(section==="settings"){
    renderAdminSettings(content);
  }
}

/* -------------------------
  Admin: Teams
----------------------------*/
function renderAdminTeams(container){
  const teams = loadTeams();
  let html = `<h4>قائمة الفرق</h4><div class="small muted">اضغط على شعار لاختياره وتغييره، أو ارفع شعار جديد</div>
    <div style="margin-top:10px" class="logo-grid">`;
  teams.forEach(t=>{
    html += `<div class="logo-item" data-id="${t.id}">
      <div class="logo-preview" style="background-image:url('${t.iconUrl || placeholderFor(t.id)}')"></div>
      <div class="small" style="text-align:center">${t.name}</div>
    </div>`;
  });
  html += `</div><div style="margin-top:10px" class="small">أدخل اسم فريق جديد:</div>
    <div class="field"><label>اسم الفريق</label><input id="newTeamName" type="text" placeholder="مثال: My Team" /></div>
    <div style="display:flex;gap:8px"><button id="addTeamBtn" class="primary">إضافة فريق</button><label class="ghost" style="padding:8px;border-radius:8px;cursor:pointer">رفع شعار<input id="uploadTeamLogo" type="file" accept="image/*" style="display:none" /></label></div>`;
  container.innerHTML = html;

  // attach events
  container.querySelectorAll(".logo-item").forEach(el=>{
    el.addEventListener("click", ()=>{
      const id = el.dataset.id;
      const teams = loadTeams();
      const t = teams.find(tt=>tt.id===id);
      const name = prompt("تعديل اسم الفريق:", t.name);
      if(name!==null){ t.name = name; saveTeams(teams); renderAdminTeams(container); updateLivePreview("تم تعديل اسم الفريق"); }
    });
  });

  $("addTeamBtn").addEventListener("click", ()=>{
    const name = $("newTeamName").value.trim();
    if(!name) return alert("أدخل اسم الفريق");
    const id = name.toLowerCase().replace(/\s+/g,"_").replace(/[^\w\-]/g,"");
    const teams = loadTeams();
    if(teams.find(t=>t.id===id)) return alert("يوجد فريق بنفس المعرف. غيّر الاسم.");
    teams.push({id,name,iconUrl:null,group:null,stats:{played:0,wins:0,draws:0,losses:0,gf:0,ga:0,points:0}});
    saveTeams(teams); renderAdminTeams(container);
    updateLivePreview("أُضيف الفريق "+name);
    $("newTeamName").value = "";
  });

  $("uploadTeamLogo").addEventListener("change", (ev)=>{
    const f = ev.target.files[0];
    if(!f) return;
    const reader = new FileReader();
    reader.onload = ()=> {
      // let user choose team to assign this logo to
      const teams = loadTeams();
      const selId = prompt("ضع معرف الفريق الذي تريد ربط الشعار به (مثال: inter_milan)","");
      if(!selId) return;
      const team = teams.find(t=>t.id===selId);
      if(!team) return alert("معرف غير موجود");
      team.iconUrl = reader.result;
      saveTeams(teams); renderAdminTeams(container); updateLivePreview("تم رفع الشعار لـ "+team.name);
    };
    reader.readAsDataURL(f);
    ev.target.value = "";
  });
}

/* -------------------------
  Admin: Participants
----------------------------*/
function renderAdminParts(container){
  const teams = loadTeams();
  let html = `<h4>المتبارون</h4>
    <div class="field"><label>اسم المتبارٍ</label><input id="partName" type="text" placeholder="مثال: Mohammed"/></div>
    <div class="field"><label>اختر فريق</label><select id="partTeam"><option value="">-- بدون فريق --</option>`;
  teams.forEach(t=> html += `<option value="${t.id}">${t.name}</option>`);
  html += `</select></div>
    <div class="field"><label>أهداف حالياً</label><input id="partGoals" type="number" min="0" value="0" /></div>
    <div style="display:flex;gap:8px"><button id="addPartBtn" class="primary">أضف متبارٍ</button><button id="clearParts" class="ghost">حذف الكل</button></div>
    <h4 style="margin-top:12px">قائمة المتبارين</h4><div id="partsList" class="small"></div>`;
  container.innerHTML = html;

  function refreshList(){
    const list = loadParts();
    const el = $("partsList");
    if(list.length===0){ el.innerHTML = `<div class="muted">لا يوجد متبارين</div>`; return; }
    el.innerHTML = "";
    list.forEach((p,idx)=>{
      const team = teams.find(t=>t.id===p.teamId);
      const div = document.createElement("div");
      div.style.display="flex"; div.style.alignItems="center"; div.style.justifyContent="space-between"; div.style.marginBottom="6px";
      div.innerHTML = `<div>${p.name} <span class="small muted">${team?team.name:''}</span></div><div><button data-idx="${idx}" class="ghost">حذف</button></div>`;
      el.appendChild(div);
    });
    el.querySelectorAll("button").forEach(b=>{
      b.addEventListener("click",(ev)=>{
        const idx=Number(b.dataset.idx);
        const arr = loadParts(); arr.splice(idx,1); saveParts(arr); refreshList(); updateLivePreview("تم حذف متبارٍ"); renderScorersView();
      });
    });
  }
  refreshList();

  $("addPartBtn").addEventListener("click", ()=>{
    const name = $("partName").value.trim();
    const teamId = $("partTeam").value || null;
    const goals = Number($("partGoals").value) || 0;
    if(!name) return alert("ادخل اسم المتبارٍ");
    const parts = loadParts();
    parts.push({id:"p_"+Date.now(), name, teamId, teamName: teamId? (loadTeams().find(t=>t.id===teamId)||{}).name : null, goals});
    saveParts(parts); refreshList(); $("partName").value=""; $("partGoals").value=0; updateLivePreview("تم إضافة متبارٍ"); renderScorersView();
  });

  $("clearParts").addEventListener("click", ()=>{ if(confirm("حذف كل المتبارين؟")){ saveParts([]); refreshList(); updateLivePreview("تم حذف الكل"); }});
}

/* -------------------------
  Admin: Matches
----------------------------*/
function renderAdminMatches(container){
  const teams = loadTeams();
  let html = `<h4>إدارة المباريات</h4>
    <div class="field"><label>المرحلة</label><select id="matchStage"><option value="Group">Group</option><option>Quarter Final</option><option>Semi Final</option><option>Final</option><option>3rd Place</option></select></div>
    <div class="field"><label>تاريخ (يوم)</label><input id="matchDate" type="date" /></div>
    <div class="field"><label>الفريق المضيف</label><select id="homeTeam"><option value="">-- فارغ --</option>`;
  teams.forEach(t=> html += `<option value="${t.id}">${t.name}</option>`);
  html += `</select></div>
    <div class="field"><label>الفريق الضيف</label><select id="awayTeam"><option value="">-- فارغ --</option>`;
  teams.forEach(t=> html += `<option value="${t.id}">${t.name}</option>`);
  html += `</select></div>
    <div class="field"><label>نتيجة مضيف</label><input id="homeGoals" type="number" min="0" /></div>
    <div class="field"><label>نتيجة ضيف</label><input id="awayGoals" type="number" min="0" /></div>
    <div style="display:flex;gap:8px"><button id="addMatchBtn" class="primary">أضف مباراة</button><button id="recalcBtn" class="ghost">إعادة حساب الترتيبات</button></div>
    <h4 style="margin-top:12px">قائمة المباريات</h4><div id="matchesList" class="small"></div>`;
  container.innerHTML = html;

  function refreshMatches(){
    const ms = loadMatches();
    const el = $("matchesList");
    if(ms.length===0){ el.innerHTML = `<div class="muted">لا توجد مباريات</div>`; return; }
    el.innerHTML = "";
    ms.forEach((m,idx)=>{
      const ht = (loadTeams().find(t=>t.id===m.homeTeamId)||{}).name || '-';
      const at = (loadTeams().find(t=>t.id===m.awayTeamId)||{}).name || '-';
      const div = document.createElement("div");
      div.style.display="flex";div.style.alignItems="center";div.style.justifyContent="space-between";div.style.marginBottom="6px";
      div.innerHTML = `<div>${m.stage} — ${m.date || ''} — ${ht} vs ${at}</div><div><button data-idx="${idx}" class="ghost">تحرير</button> <button data-del="${idx}" class="ghost">حذف</button></div>`;
      el.appendChild(div);
    });
    el.querySelectorAll("button[data-del]").forEach(b=>{
      b.addEventListener("click", ()=>{ if(confirm("حذف المباراة؟")){ const ms=loadMatches(); ms.splice(Number(b.dataset.del),1); saveMatches(ms); refreshMatches(); renderMatchesView(); renderKnockoutView(); renderStandingsView(); }});
    });
    el.querySelectorAll("button[data-idx]").forEach(b=>{
      b.addEventListener("click", ()=>{
        const idx=Number(b.dataset.idx);
        const ms = loadMatches()[idx];
        $("matchStage").value = ms.stage;
        $("matchDate").value = ms.date || "";
        $("homeTeam").value = ms.homeTeamId || "";
        $("awayTeam").value = ms.awayTeamId || "";
        $("homeGoals").value = ms.homeGoals==null? "": ms.homeGoals;
        $("awayGoals").value = ms.awayGoals==null? "": ms.awayGoals;
        // replace existing
        if(confirm("هل تريد تعديل هذه المباراة (سيتم إزالة القديمة بعد الحفظ)؟")){
          const all = loadMatches();
          all.splice(idx,1);
          saveMatches(all);
          refreshMatches();
        }
      });
    });
  }
  refreshMatches();

  $("addMatchBtn").addEventListener("click", ()=>{
    const stage = $("matchStage").value;
    const date = $("matchDate").value;
    const home = $("homeTeam").value || null;
    const away = $("awayTeam").value || null;
    const hg = $("homeGoals").value; const ag = $("awayGoals").value;
    const homeGoals = hg===""? null: Number(hg);
    const awayGoals = ag===""? null: Number(ag);
    const ms = loadMatches();
    ms.push({id:"m_"+Date.now(), stage, date, homeTeamId:home, awayTeamId:away, homeGoals, awayGoals});
    saveMatches(ms);
    renderMatchesView(); renderKnockoutView(); renderStandingsView();
    updateLivePreview("تم إضافة مباراة");
    renderAdminMatches($("adminContent"));
  });

  $("recalcBtn").addEventListener("click", ()=>{ computeStandings(); renderStandingsView(); updateLivePreview("إعادة الحساب - تم"); });
}

/* -------------------------
  Admin: Settings
----------------------------*/
function renderAdminSettings(container){
  const settings = loadSettings();
  container.innerHTML = `<h4>الإعدادات</h4>
    <div class="field"><label>كلمة مرور المشرف</label><input id="setPass" type="text" value="${settings.adminPass || ''}" /></div>
    <div style="display:flex;gap:8px"><button id="saveSettings" class="primary">حفظ</button><button id="resetAll" class="ghost">إعادة ضبط كل البيانات</button></div>
    <div class="small muted" style="margin-top:8px">تنبيه: إعادة الضبط تحذف كل ما في localStorage من فرق ومباريات ومشاركين.</div>`;
  $("saveSettings").addEventListener("click", ()=>{
    const newp = $("setPass").value || "admin123";
    const s = loadSettings(); s.adminPass = newp; saveSettings(s); alert("تم حفظ الإعدادات");
  });
  $("resetAll").addEventListener("click", ()=>{
    if(!confirm("هل أنت متأكد من إعادة الضبط؟ سيُحذف كل شيء!")) return;
    localStorage.removeItem(LS_TEAMS); localStorage.removeItem(LS_MATCHES); localStorage.removeItem(LS_PARTS); localStorage.removeItem(LS_SETTINGS);
    initData(); alert("تمت إعادة الضبط. أعد فتح لوحة المشرف"); location.reload();
  });
}

/* -------------------------
  Helpers & placeholders
----------------------------*/
function placeholderFor(id){
  // tiny colorful SVG dataURL based on id or generic
  const label = id? id.split("_").map(x=>x[0].toUpperCase()).slice(0,2).join("") : "?";
  const hue = Math.abs(hashStr(id||"x"))%360;
  const bg = `hsl(${hue} 70% 45%)`;
  const svg = `<svg xmlns='http://www.w3.org/2000/svg' width='220' height='220'><rect rx='14' width='100%' height='100%' fill='${bg}'/><text x='50%' y='54%' font-family='Arial' font-size='64' fill='#fff' text-anchor='middle' dominant-baseline='middle' font-weight='700'>${label}</text></svg>`;
  return `data:image/svg+xml;base64,${btoa(svg)}`;
}
function hashStr(s){ let h=0; for(let i=0;i<s.length;i++){ h=(h<<5)-h + s.charCodeAt(i); h=h&h; } return h; }

function updateLivePreview(msg){
  const el = $("livePreview");
  el.innerHTML = `<div style="padding:8px">${msg}</div>`;
  setTimeout(()=>{ computeStandings(); renderStandingsView(); renderMatchesView(); renderKnockoutView(); renderScorersView(); },200);
}

/* -------------------------
  Init: render admin default and public views
----------------------------*/
renderMatchesView();
renderStandingsView();
renderKnockoutView();
renderScorersView();

/* -------------------------
  Expose some handy functions for debugging in console
----------------------------*/
window.ef = {
  loadTeams, saveTeams, loadMatches, saveMatches, loadParts, saveParts, computeStandings, placeholderFor
};
</script>
</body>
</html>
