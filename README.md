<!DOCTYPE html>
<html lang="zh-Hant">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>工作行事曆</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Noto+Sans+TC:wght@400;500;600;700&family=Poppins:wght@400;500;600&display=swap" rel="stylesheet">
<style>
  :root{
    --bg:#F6F3ED; --panel:#FFFFFF; --ink:#33312B; --muted:#A29C8F; --muted-2:#C9C2B3; --line:#EAE4D8;
    --sage:#8CA48A; --sage-soft:#E6ECE2; --rose:#D8A29D; --rose-soft:#F6E7E4;
    --sky:#9FB2C4; --sky-soft:#E8EEF3; --done:#B7B0A0; --done-soft:#EFEBE1;
    --radius:18px; --radius-sm:11px; --shadow:0 4px 18px rgba(51,49,43,0.06);
  }
  *{box-sizing:border-box;}
  body{margin:0;background:var(--bg);color:var(--ink);font-family:'Noto Sans TC','Poppins',sans-serif;-webkit-font-smoothing:antialiased;}
  .eyebrow{font-family:'Poppins',sans-serif;font-size:10.5px;letter-spacing:.14em;color:var(--muted);text-transform:uppercase;}

  .app{display:flex;gap:22px;max-width:1500px;margin:0 auto;padding:26px 26px 40px;min-height:100vh;align-items:flex-start;}
  .left{flex:1.7;display:flex;flex-direction:column;gap:20px;min-width:0;}
  .right{flex:1;display:flex;flex-direction:column;min-width:0;position:sticky;top:26px;}

  .topbar{display:flex;align-items:baseline;justify-content:space-between;margin-bottom:2px;padding:0 2px;}
  .topbar h1{font-size:19px;font-weight:700;margin:0;letter-spacing:.02em;}
  .topbar .greet{font-size:12.5px;color:var(--muted);}

  /* ---------- Calendar ---------- */
  .card{background:var(--panel);border-radius:var(--radius);box-shadow:var(--shadow);padding:20px 22px;}
  .cal-head{display:flex;align-items:center;justify-content:space-between;margin-bottom:14px;}
  .cal-title{font-size:16px;font-weight:700;letter-spacing:.02em;}
  .cal-nav{display:flex;align-items:center;gap:6px;}
  .cal-nav button{width:28px;height:28px;border-radius:50%;border:1px solid var(--line);background:#fff;color:var(--ink);cursor:pointer;font-size:13px;display:flex;align-items:center;justify-content:center;transition:.15s;}
  .cal-nav button:hover{background:var(--sage-soft);border-color:var(--sage);}
  .cal-today-btn{border:1px solid var(--line);background:#fff;border-radius:20px;padding:5px 12px;font-size:11.5px;color:var(--muted);cursor:pointer;font-family:'Poppins',sans-serif;margin-right:4px;transition:.15s;}
  .cal-today-btn:hover{border-color:var(--sage);color:var(--sage);}
  .cal-hint{font-size:10.5px;color:var(--muted-2);margin:-8px 0 10px;}

  .cal-wd-row{display:grid;grid-template-columns:repeat(7,1fr);margin-bottom:4px;}
  .cal-wd{text-align:left;padding-left:4px;font-size:11px;color:var(--muted-2);font-family:'Poppins',sans-serif;letter-spacing:.06em;}

  .cal-week{border-top:1px solid var(--line);padding-top:5px;margin-top:5px;}
  .cal-week:first-child{border-top:none;margin-top:0;}
  .week-days{display:grid;grid-template-columns:repeat(7,1fr);}
  .day-num{min-height:24px;padding:2px 4px;font-size:12px;color:var(--muted);cursor:pointer;border-radius:6px;transition:background .15s;user-select:none;}
  .day-num:hover{background:var(--sage-soft);}
  .day-num.muted{color:var(--muted-2);}
  .day-num.selected{background:var(--sage-soft);}
  .day-num .badge{display:inline-flex;align-items:center;justify-content:center;width:19px;height:19px;border-radius:50%;}
  .day-num.today .badge{background:var(--ink);color:#fff;font-weight:700;}

  .week-lanes{display:flex;flex-direction:column;gap:3px;padding-bottom:4px;min-height:6px;}
  .lane-row{display:grid;grid-template-columns:repeat(7,1fr);height:18px;}
  .bar{background:var(--sage);color:#fff;font-size:10.5px;display:flex;align-items:center;justify-content:flex-start;text-align:left;overflow:hidden;white-space:nowrap;text-overflow:ellipsis;cursor:pointer;padding:0 6px;font-family:'Poppins',sans-serif;}
  .bar.inprogress{background:var(--rose);}
  .bar.done{background:var(--done);}
  .bar.round-l{border-radius:8px 0 0 8px;margin-left:2px;}
  .bar.round-r{border-radius:0 8px 8px 0;margin-right:2px;}
  .bar.round-l.round-r{border-radius:8px;margin:0 2px;}
  .day-more{font-size:10px;color:var(--muted);text-align:center;cursor:pointer;font-family:'Poppins',sans-serif;align-self:center;}
  .day-more:hover{color:var(--sage);text-decoration:underline;}

  .quickadd{display:flex;align-items:center;gap:8px;margin-top:16px;padding-top:14px;border-top:1px solid var(--line);flex-wrap:wrap;}
  .quickadd input[type=text]{flex:1;min-width:140px;border:1px solid var(--line);border-radius:var(--radius-sm);padding:9px 12px;font-size:13px;font-family:inherit;background:#FBFAF7;}
  .quickadd input[type=text]:focus{outline:none;border-color:var(--sage);}
  .qa-toggle{border:1px solid var(--line);background:#fff;border-radius:20px;padding:8px 13px;font-size:12px;color:var(--muted);cursor:pointer;font-family:'Poppins',sans-serif;white-space:nowrap;transition:.15s;}
  .qa-toggle:hover{border-color:var(--sage);color:var(--sage);}
  .quickadd > button#qaAdd{border:none;background:var(--ink);color:#fff;border-radius:var(--radius-sm);padding:9px 16px;font-size:13px;cursor:pointer;transition:.15s;}
  .quickadd > button#qaAdd:hover{background:var(--sage);}

  .qa-details{display:none;gap:10px 14px;flex-wrap:wrap;margin-top:12px;padding-top:12px;border-top:1px dashed var(--line);align-items:center;}
  .qa-details.show{display:flex;}
  .qa-details label{font-size:11.5px;color:var(--muted);display:flex;align-items:center;gap:5px;font-family:'Poppins',sans-serif;}
  .qa-details input,.qa-details select{border:1px solid var(--line);border-radius:8px;padding:6px 8px;font-size:12.5px;font-family:inherit;background:#FBFAF7;color:var(--ink);}
  .qa-tilde{color:var(--muted-2);}
  .qa-timerow{display:none;gap:6px;align-items:center;}
  .qa-allday{cursor:pointer;}
  .qa-allday input{width:14px;height:14px;}
  .time-sel{border:1px solid var(--line);border-radius:8px;padding:6px 5px;font-size:12.5px;font-family:'Poppins',sans-serif;background:#FBFAF7;color:var(--ink);}
  .time-colon{color:var(--muted);font-family:'Poppins',sans-serif;}

  /* ---------- Board ---------- */
  .board{display:grid;grid-template-columns:repeat(4,1fr);gap:14px;flex:1;}
  .col{background:var(--panel);border-radius:var(--radius);box-shadow:var(--shadow);padding:16px 14px;display:flex;flex-direction:column;min-height:300px;max-height:480px;}
  .col-head{margin-bottom:12px;padding-bottom:10px;border-bottom:1px solid var(--line);}
  .col-head .dotlabel{display:flex;align-items:center;gap:6px;margin-bottom:2px;}
  .col-head .dotlabel .d{width:7px;height:7px;border-radius:50%;}
  .col-head h3{margin:0;font-size:14.5px;font-weight:700;}
  .col-head .eyebrow{margin-left:13px;}
  .col[data-k="todo"] .d{background:var(--sage);}
  .col[data-k="inprogress"] .d{background:var(--rose);}
  .col[data-k="thisweek"] .d{background:var(--sky);}
  .col[data-k="done"] .d{background:var(--done);}

  .col-list{flex:1;overflow-y:auto;display:flex;flex-direction:column;gap:8px;padding-right:2px;}
  .col-list::-webkit-scrollbar{width:5px;}
  .col-list::-webkit-scrollbar-thumb{background:var(--line);border-radius:10px;}

  .task{position:relative;display:flex;align-items:flex-start;gap:9px;background:#FBFAF7;border-radius:12px;padding:10px 26px 10px 11px;border-left:3px solid var(--sage);font-size:13px;line-height:1.45;}
  .col[data-k="inprogress"] .task{border-left-color:var(--rose);}
  .col[data-k="thisweek"] .task{border-left-color:var(--sky);}
  .col[data-k="done"] .task{border-left-color:var(--done);opacity:.72;}

  .circle{flex:none;width:16px;height:16px;border-radius:50%;border:2px solid var(--sage);margin-top:2px;cursor:pointer;transition:.15s;}
  .col[data-k="inprogress"] .circle{border-color:var(--rose);}
  .col[data-k="thisweek"] .circle{border-color:var(--sky);}
  .circle:hover{transform:scale(1.12);}
  .task-text{flex:1;word-break:break-word;}
  .task-when{display:block;font-size:10.5px;color:var(--muted);margin-top:2px;font-family:'Poppins',sans-serif;}
  .task .x{position:absolute;top:7px;right:7px;border:none;background:none;color:var(--muted-2);font-size:13px;cursor:pointer;opacity:0;transition:.15s;line-height:1;}
  .task:hover .x{opacity:1;}
  .task .x:hover{color:var(--rose);}
  .done-check{flex:none;width:16px;height:16px;border-radius:50%;background:var(--done);margin-top:2px;display:flex;align-items:center;justify-content:center;cursor:pointer;transition:.15s;}
  .done-check:hover{transform:scale(1.12);}
  .done-check:after{content:'';width:5px;height:8px;border:solid #fff;border-width:0 2px 2px 0;transform:rotate(40deg) translate(-1px,-1px);}
  .done-tag{font-size:10.5px;color:var(--muted);font-family:'Poppins',sans-serif;margin-left:6px;white-space:nowrap;}
  .empty{color:var(--muted-2);font-size:12px;text-align:center;margin:auto 0;padding:20px 6px;line-height:1.6;}

  /* ---------- Right / Memo — true freeform notepad ---------- */
  .memo-card{background:var(--panel);border-radius:var(--radius);box-shadow:var(--shadow);padding:24px 24px 18px;display:flex;flex-direction:column;height:calc(100vh - 52px);}
  .memo-head{display:flex;align-items:flex-start;justify-content:space-between;gap:10px;}
  .memo-head h2{margin:0 0 2px;font-size:16px;font-weight:700;}
  .memo-head p{margin:0 0 10px;font-size:11.5px;color:var(--muted);line-height:1.6;}
  .pop-btn{border:1px solid var(--line);background:#fff;border-radius:20px;padding:6px 12px;font-size:11px;color:var(--muted);cursor:pointer;font-family:'Poppins',sans-serif;white-space:nowrap;transition:.15s;flex:none;}
  .pop-btn:hover{border-color:var(--sage);color:var(--sage);}

  .memo-toolbar{display:flex;gap:6px;margin-bottom:8px;}
  .tbtn{width:26px;height:26px;border-radius:7px;border:1px solid var(--line);background:transparent;cursor:pointer;font-size:12px;color:var(--muted);display:flex;align-items:center;justify-content:center;transition:.15s;}
  .tbtn:hover{border-color:var(--sage);color:var(--sage);}
  .tbtn.b{font-weight:800;font-family:'Poppins',sans-serif;}
  .tbtn.c{border-radius:50%;}
  .tbtn.c span{width:9px;height:9px;border-radius:50%;border:2px solid currentColor;display:block;}

  .notepad{
    flex:1;overflow-y:auto;outline:none;font-size:14px;
    background:repeating-linear-gradient(var(--panel) 0px, var(--panel) 26px, var(--line) 27px);
    line-height:27px;position:relative;padding-right:2px;
  }
  .notepad.is-empty::before{ content:attr(data-placeholder); color:var(--muted-2); position:absolute; top:0; left:2px; pointer-events:none; }
  .notepad .line{ min-height:27px; line-height:27px; position:relative; padding-right:2px; }
  .notepad .line.done-line .txt{ text-decoration:line-through; color:var(--muted-2); }
  .notepad .chk{ display:inline-flex; width:13px; height:13px; border-radius:50%; border:2px solid var(--sky); margin-right:6px; vertical-align:middle; cursor:pointer; }
  .notepad .chk.checked{ background:var(--sky); }
  .notepad b{font-weight:700;}
  .notepad::-webkit-scrollbar{width:5px;}
  .notepad::-webkit-scrollbar-thumb{background:var(--line);border-radius:10px;}

  .toast{position:fixed;bottom:26px;right:26px;background:var(--ink);color:#fff;padding:11px 18px;border-radius:12px;font-size:12.5px;box-shadow:var(--shadow);opacity:0;transform:translateY(8px);transition:.25s;pointer-events:none;z-index:50;max-width:320px;}
  .toast.show{opacity:1;transform:translateY(0);}

  /* ---------- Modals ---------- */
  .modal-overlay{position:fixed;inset:0;background:rgba(51,49,43,.35);display:flex;align-items:center;justify-content:center;z-index:200;}
  .modal-box{background:#fff;border-radius:16px;padding:20px 22px;width:320px;max-width:90vw;box-shadow:0 10px 40px rgba(0,0,0,.18);max-height:82vh;overflow-y:auto;}
  .modal-box h3{margin:0 0 14px;font-size:15px;}
  .modal-box label{display:flex;flex-direction:column;gap:4px;font-size:11.5px;color:var(--muted);margin-bottom:10px;font-family:'Poppins',sans-serif;}
  .modal-box input[type=text]{font-size:14px;padding:8px 10px;border:1px solid var(--line);border-radius:8px;font-family:'Noto Sans TC',sans-serif;color:var(--ink);}
  .modal-box input[type=date]{border:1px solid var(--line);border-radius:8px;padding:6px 8px;font-size:12.5px;}
  .modal-row{display:flex;gap:10px;}
  .modal-row label{flex:1;}
  .modal-box .qa-allday{flex-direction:row;align-items:center;gap:6px;}
  .modal-actions{display:flex;align-items:center;gap:8px;margin-top:8px;}
  .modal-danger{border:none;background:var(--rose-soft);color:#a45550;border-radius:8px;padding:8px 12px;font-size:12.5px;cursor:pointer;}
  .modal-ghost{border:1px solid var(--line);background:#fff;color:var(--muted);border-radius:8px;padding:8px 14px;font-size:12.5px;cursor:pointer;}
  .modal-primary{border:none;background:var(--ink);color:#fff;border-radius:8px;padding:8px 16px;font-size:12.5px;cursor:pointer;}
  .day-pop-list{display:flex;flex-direction:column;gap:6px;max-height:340px;overflow-y:auto;}
  .day-pop-row{display:flex;gap:8px;align-items:center;padding:8px 10px;border-radius:9px;background:#FBFAF7;border-left:3px solid var(--sage);cursor:pointer;font-size:13px;}
  .day-pop-row.inprogress{border-left-color:var(--rose);}
  .day-pop-row.done{border-left-color:var(--done);opacity:.7;}
  .dpr-time{font-size:11px;color:var(--muted);font-family:'Poppins',sans-serif;white-space:nowrap;}
  .dpr-title{flex:1;}

  @media(max-width:980px){
    .app{flex-direction:column;}
    .right{position:static;width:100%;}
    .memo-card{height:auto;max-height:480px;}
    .board{grid-template-columns:repeat(2,1fr);}
  }
</style>
</head>
<body>
<div class="app">
  <div class="left">
    <div class="topbar">
      <h1>工作行事曆</h1>
      <span class="greet" id="greet"></span>
    </div>

    <div class="card">
      <div class="cal-head">
        <div class="cal-title" id="calTitle"></div>
        <div class="cal-nav">
          <button class="cal-today-btn" id="btnToday">今天</button>
          <button id="btnPrev">‹</button>
          <button id="btnNext">›</button>
        </div>
      </div>
      <div class="cal-hint">單擊選取日期，雙擊日期快速新增，點選項目可編輯或刪除</div>
      <div class="cal-wd-row" id="calWd"></div>
      <div id="calWeeks"></div>

      <div class="quickadd">
        <input type="text" id="qaInput" placeholder="手動新增行程項目...">
        <button class="qa-toggle" id="qaToggle">詳細設定 ▾</button>
        <button id="qaAdd">新增</button>
      </div>
      <div class="qa-details" id="qaDetails">
        <label>開始 <input type="date" id="qaStart"></label>
        <label>結束 <input type="date" id="qaEnd"></label>
        <label class="qa-allday"><input type="checkbox" id="qaAllDay" checked> 全天</label>
        <span class="qa-timerow" id="qaTimeRow">
          <label>時間 <span id="qaStartTimeWrap"></span></label>
          <span class="qa-tilde">~</span>
          <label><span id="qaEndTimeWrap"></span></label>
        </span>
      </div>
    </div>

    <div class="board" id="board">
      <div class="col" data-k="todo">
        <div class="col-head"><div class="dotlabel"><span class="d"></span><h3>待完成</h3><span class="eyebrow">TO DO</span></div></div>
        <div class="col-list" id="col-todo"></div>
      </div>
      <div class="col" data-k="inprogress">
        <div class="col-head"><div class="dotlabel"><span class="d"></span><h3>進行中</h3><span class="eyebrow">DOING</span></div></div>
        <div class="col-list" id="col-inprogress"></div>
      </div>
      <div class="col" data-k="thisweek">
        <div class="col-head"><div class="dotlabel"><span class="d"></span><h3>可先做</h3><span class="eyebrow">THIS WEEK</span></div></div>
        <div class="col-list" id="col-thisweek"></div>
      </div>
      <div class="col" data-k="done">
        <div class="col-head"><div class="dotlabel"><span class="d"></span><h3>已完成</h3><span class="eyebrow">DONE</span></div></div>
        <div class="col-list" id="col-done"></div>
      </div>
    </div>
  </div>

  <div class="right">
    <div class="memo-card">
      <div class="memo-head">
        <div>
          <h2>隨手記</h2>
          <p>像備忘錄一樣直接打字、換行；含日期或時間的那一行按 Enter 後會自動整理進左側行事曆。</p>
        </div>
        <button class="pop-btn" id="popSticky">📌 拉出便利貼</button>
      </div>
      <div class="memo-toolbar">
        <button class="tbtn b" id="tbBold" title="加粗選取文字">B</button>
        <button class="tbtn c" id="tbCheck" title="在游標所在行加上圈圈勾選"><span></span></button>
      </div>
      <div class="notepad" id="notepad" contenteditable="true" data-placeholder="在這裡打字..."></div>
    </div>
  </div>
</div>

<div class="toast" id="toast"></div>

<script>
/* ---------------- basics ---------------- */
const now = new Date();
const todayDate = new Date(now.getFullYear(), now.getMonth(), now.getDate());
function pad(n){ return n<10 ? '0'+n : ''+n; }
function fmt(d){ return d.getFullYear()+'-'+pad(d.getMonth()+1)+'-'+pad(d.getDate()); }
function parseYMD(str){ const p=str.split('-').map(Number); return new Date(p[0],p[1]-1,p[2]); }
function uid(){ return 'id'+Date.now()+Math.random().toString(36).slice(2,7); }
function addMinutesToTime(t, addH){
  const [hh,mm] = t.split(':').map(Number);
  let total = ((hh*60+mm) + addH*60) % 1440;
  if(total<0) total += 1440;
  return pad(Math.floor(total/60))+':'+pad(total%60);
}
function escapeHtml(s){ return String(s).replace(/&/g,'&amp;').replace(/</g,'&lt;').replace(/>/g,'&gt;').replace(/"/g,'&quot;'); }
function debounce(fn, ms){ let t; return function(...args){ clearTimeout(t); t=setTimeout(()=>fn.apply(this,args), ms); }; }

const todayStr = fmt(todayDate);
let viewYear = todayDate.getFullYear();
let viewMonth = todayDate.getMonth();
let selectedDateStr = todayStr;
let events = []; // {id,title,startDate,endDate,startTime,endTime,allDay,manualInProgress,completed,completedDate}
let stickyWin = null;

/* ---------------- 24h / 15min time select helper ---------------- */
function buildTimeSelectHTML(idPrefix, valueHHMM){
  const parts = (valueHHMM||'09:00').split(':');
  const vh = parts[0], vm = parts[1];
  let hourOpts=''; for(let h=0;h<24;h++){ const hs=pad(h); hourOpts += '<option value="'+hs+'"'+(vh===hs?' selected':'')+'>'+hs+'</option>'; }
  let minOpts=''; ['00','15','30','45'].forEach(mm=>{ minOpts += '<option value="'+mm+'"'+(vm===mm?' selected':'')+'>'+mm+'</option>'; });
  return '<select id="'+idPrefix+'H" class="time-sel">'+hourOpts+'</select><span class="time-colon">:</span><select id="'+idPrefix+'M" class="time-sel">'+minOpts+'</select>';
}
function getTimeSelectValue(idPrefix, doc){
  doc = doc || document;
  const h = doc.getElementById(idPrefix+'H').value;
  const m = doc.getElementById(idPrefix+'M').value;
  return h+':'+m;
}

/* ---------------- persistence ---------------- */
async function loadState(){
  try{
    if(window.storage){
      const e = await window.storage.get('schedule-events-v5').catch(()=>null);
      const np = await window.storage.get('schedule-notepad-v1').catch(()=>null);
      if(e && e.value) events = JSON.parse(e.value);
      if(np && np.value) document.getElementById('notepad').innerHTML = np.value;
    }
  }catch(err){ console.warn('load failed', err); }
  events = events.map(e=>{
    if(e.startDate) return { allDay: (e.allDay!==undefined)? e.allDay : !e.startTime, ...e };
    return { id:e.id, title:e.title, startDate:e.date, endDate:e.date, startTime:null, endTime:null, allDay:true,
             manualInProgress: e.status==='inprogress', completed: e.status==='done', completedDate: e.completedDate||null };
  });
  normalizeNotepad(document, document.getElementById('notepad'));
  refreshNotepadEmptyState(document.getElementById('notepad'));
  renderAll();
}
async function saveEvents(){
  try{ if(window.storage) await window.storage.set('schedule-events-v5', JSON.stringify(events)); }catch(err){ console.warn('save failed', err); }
}
async function saveNotepadHTML(html){
  try{ if(window.storage) await window.storage.set('schedule-notepad-v1', html); }catch(err){ console.warn('save failed', err); }
}

/* ---------------- date helpers (week = Sun~Sat) ---------------- */
function weekRange(dateStr){
  const d = parseYMD(dateStr);
  const dow = d.getDay();
  const sun = new Date(d); sun.setDate(d.getDate()-dow);
  const sat = new Date(sun); sat.setDate(sun.getDate()+6);
  return { start: fmt(sun), end: fmt(sat) };
}
function inRange(dateStr, range){ return dateStr >= range.start && dateStr <= range.end; }

const weekdayMap = {'日':0,'天':0,'一':1,'二':2,'三':3,'四':4,'五':5,'六':6};

function tryParseDateKeyword(text){
  let m = text.match(/(\d{1,2})\s*[\/\-月]\s*(\d{1,2})\s*[日號]?\s*[~\-到至]\s*(\d{1,2})\s*[\/\-月]\s*(\d{1,2})\s*[日號]?/);
  if(m){
    const mo1=parseInt(m[1],10), da1=parseInt(m[2],10), mo2=parseInt(m[3],10), da2=parseInt(m[4],10);
    if(mo1>=1&&mo1<=12&&da1>=1&&da1<=31&&mo2>=1&&mo2<=12&&da2>=1&&da2<=31){
      const d1 = new Date(todayDate.getFullYear(), mo1-1, da1);
      const d2 = new Date(todayDate.getFullYear(), mo2-1, da2);
      return { date:d1, endDate: d2>=d1?d2:d1, matchStr:m[0] };
    }
  }
  const offsets = [['大前天',-3],['大後天',3],['前天',-2],['昨天',-1],['後天',2],['明天',1],['今天',0]];
  for(const [kw,offset] of offsets){
    if(text.includes(kw)){
      const d = new Date(todayDate); d.setDate(todayDate.getDate()+offset);
      return { date:d, matchStr:kw };
    }
  }
  m = text.match(/(下)?[週周星期]([一二三四五六日天])/);
  if(m){
    const targetDow = weekdayMap[m[2]];
    const curDow = todayDate.getDay();
    let diff = targetDow - curDow;
    if(diff < 0) diff += 7;
    if(m[1]) diff += 7;
    const d = new Date(todayDate); d.setDate(todayDate.getDate()+diff);
    return { date:d, matchStr:m[0] };
  }
  m = text.match(/(\d{1,2})\s*[月\/\-](\d{1,2})\s*[日號]?/);
  if(m){
    const mo=parseInt(m[1],10), da=parseInt(m[2],10);
    if(mo>=1 && mo<=12 && da>=1 && da<=31) return { date: new Date(todayDate.getFullYear(), mo-1, da), matchStr:m[0] };
  }
  m = text.match(/(\d{1,2})\s*[日號]/);
  if(m){
    const da=parseInt(m[1],10);
    if(da>=1 && da<=31) return { date: new Date(todayDate.getFullYear(), todayDate.getMonth(), da), matchStr:m[0] };
  }
  return null;
}
function tryParseTime(text){
  let m = text.match(/([01]?\d|2[0-3]):([0-5]\d)/);
  if(m) return { time: pad(parseInt(m[1],10))+':'+m[2], matchStr:m[0] };

  m = text.match(/(上午|下午|中午|晚上|早上|凌晨)?\s*(\d{1,2})\s*點\s*(\d{1,2})?\s*分?/);
  if(m){
    let hour = parseInt(m[2],10);
    let minute = m[3] ? parseInt(m[3],10) : 0;
    const period = m[1];
    if(hour>=0 && hour<=23 && minute>=0 && minute<=59){
      if(period==='下午' || period==='晚上'){ if(hour<12) hour+=12; }
      else if(period==='中午'){ if(hour<12) hour+=12; }
      else if(period==='凌晨'){ if(hour===12) hour=0; }
      if(hour>23) hour = hour%24;
      return { time: pad(hour)+':'+pad(minute), matchStr:m[0] };
    }
  }
  return null;
}
function tryParseDateTime(text){
  const dateResult = tryParseDateKeyword(text);
  const timeResult = tryParseTime(text);
  if(!dateResult && !timeResult) return null;
  const date = dateResult ? dateResult.date : new Date(todayDate);
  const endDate = (dateResult && dateResult.endDate) ? dateResult.endDate : date;
  const time = timeResult ? timeResult.time : null;
  let title = text;
  if(dateResult) title = title.replace(dateResult.matchStr,'');
  if(timeResult) title = title.replace(timeResult.matchStr,'');
  title = title.replace(/^[：:，,、\s]+|[：:，,、\s]+$/g,'').trim();
  if(!title) title = '行程';
  return { date, endDate, time, title };
}

/* ---------------- effective status ---------------- */
function effectiveKind(ev, ref){
  if(ev.completed) return 'done';
  const isRange = ev.endDate !== ev.startDate;
  if(isRange){
    if(ref < ev.startDate) return 'todo';
    if(ref > ev.endDate) return 'done';
    return 'inprogress';
  }
  return ev.manualInProgress ? 'inprogress' : 'todo';
}
function tickExpiredRanges(){
  let changed=false;
  events.forEach(e=>{
    if(!e.completed && e.endDate < todayStr && e.endDate !== e.startDate){ e.completed=true; e.completedDate=e.endDate; changed=true; }
  });
  if(changed) saveEvents();
}

/* ---------------- toast ---------------- */
let toastTimer;
function showToast(msg){
  const t = document.getElementById('toast');
  t.textContent = msg; t.classList.add('show');
  clearTimeout(toastTimer); toastTimer = setTimeout(()=> t.classList.remove('show'), 2800);
}

/* ---------------- consume a notepad line as a calendar event ---------------- */
function tryConsumeLineAsEvent(text){
  text = (text||'').trim();
  if(!text) return false;
  const parsed = tryParseDateTime(text);
  if(!parsed) return false;
  let endTime = null, allDay = true;
  if(parsed.time){ endTime = addMinutesToTime(parsed.time, 1); allDay = false; }
  events.push({
    id: uid(), title: parsed.title, startDate: fmt(parsed.date), endDate: fmt(parsed.endDate),
    startTime: parsed.time, endTime, allDay, manualInProgress:false, completed:false, completedDate:null
  });
  saveEvents();
  const d = parsed.date, ed = parsed.endDate;
  const rangeTxt = fmt(d)!==fmt(ed)
    ? (d.getMonth()+1)+'/'+d.getDate()+' ~ '+(ed.getMonth()+1)+'/'+ed.getDate()
    : (d.getMonth()+1)+'/'+d.getDate()+(parsed.time?' '+parsed.time:'');
  showToast('已加入行事曆：'+rangeTxt+' '+parsed.title);
  renderAll();
  return true;
}

/* ---------------- modals: event editor + day popover ---------------- */
function closeAnyModal(){ const o = document.getElementById('modalOverlay'); if(o) o.remove(); }
function openEventEditor(ev){
  closeAnyModal();
  const overlay = document.createElement('div'); overlay.className='modal-overlay'; overlay.id='modalOverlay';
  overlay.addEventListener('click', e=>{ if(e.target===overlay) closeAnyModal(); });
  const box = document.createElement('div'); box.className='modal-box';
  box.innerHTML =
    '<h3>編輯行程</h3>'
    + '<label>標題<input type="text" id="edTitle" value="'+escapeHtml(ev.title)+'"></label>'
    + '<div class="modal-row"><label>開始<input type="date" id="edStart" value="'+ev.startDate+'"></label>'
    + '<label>結束<input type="date" id="edEnd" value="'+ev.endDate+'"></label></div>'
    + '<label class="qa-allday"><input type="checkbox" id="edAllDay" '+(ev.allDay?'checked':'')+'> 全天</label>'
    + '<div class="modal-row" id="edTimeRow" style="display:'+(ev.allDay?'none':'flex')+';align-items:center;gap:8px;">'
    + '<label>時間<span id="edStartTimeWrap"></span></label>'
    + '<span class="qa-tilde">~</span><label><span id="edEndTimeWrap"></span></label></div>'
    + '<div class="modal-actions"><button id="edDelete" class="modal-danger">刪除</button><div style="flex:1"></div>'
    + '<button id="edCancel" class="modal-ghost">取消</button><button id="edSave" class="modal-primary">儲存</button></div>';
  overlay.appendChild(box);
  document.body.appendChild(overlay);
  document.getElementById('edStartTimeWrap').innerHTML = buildTimeSelectHTML('edStart', ev.startTime||'09:00');
  document.getElementById('edEndTimeWrap').innerHTML = buildTimeSelectHTML('edEnd', ev.endTime||'10:00');
  document.getElementById('edAllDay').addEventListener('change', function(){
    document.getElementById('edTimeRow').style.display = this.checked?'none':'flex';
  });
  document.getElementById('edCancel').addEventListener('click', closeAnyModal);
  document.getElementById('edDelete').addEventListener('click', ()=>{
    if(!confirm('確定要刪除「'+ev.title+'」嗎？')) return;
    events = events.filter(e=>e.id!==ev.id); saveEvents(); closeAnyModal(); renderAll();
  });
  document.getElementById('edSave').addEventListener('click', ()=>{
    const title = document.getElementById('edTitle').value.trim();
    if(!title) return;
    let s = document.getElementById('edStart').value || ev.startDate;
    let e2 = document.getElementById('edEnd').value || s;
    if(e2 < s) e2 = s;
    const allDay = document.getElementById('edAllDay').checked;
    let st=null, et=null;
    if(!allDay){ st = getTimeSelectValue('edStart'); et = getTimeSelectValue('edEnd'); }
    ev.title=title; ev.startDate=s; ev.endDate=e2; ev.allDay=allDay; ev.startTime=st; ev.endTime=et;
    saveEvents(); closeAnyModal(); renderAll();
  });
}
function openDayPopover(dateStr){
  closeAnyModal();
  const list = events.filter(e=> dateStr>=e.startDate && dateStr<=e.endDate)
                      .sort((a,b)=> (a.startTime||'').localeCompare(b.startTime||''));
  const overlay = document.createElement('div'); overlay.className='modal-overlay'; overlay.id='modalOverlay';
  overlay.addEventListener('click', e=>{ if(e.target===overlay) closeAnyModal(); });
  const box = document.createElement('div'); box.className='modal-box';
  const d = parseYMD(dateStr);
  let rows = list.map(ev=>{
    const kind = effectiveKind(ev, todayStr);
    const timeLabel = ev.allDay ? '全天' : (ev.startTime + (ev.endTime? '-'+ev.endTime:''));
    return '<div class="day-pop-row '+kind+'" data-id="'+ev.id+'"><span class="dpr-time">'+timeLabel+'</span><span class="dpr-title">'+escapeHtml(ev.title)+'</span></div>';
  }).join('') || '<div class="empty" style="margin:16px 0;">這天還沒有行程</div>';
  box.innerHTML = '<h3>'+(d.getMonth()+1)+'月'+d.getDate()+'日 行程</h3><div class="day-pop-list">'+rows+'</div>';
  overlay.appendChild(box);
  document.body.appendChild(overlay);
  box.querySelectorAll('.day-pop-row').forEach(r=>{
    r.addEventListener('click', ()=>{
      const id = r.getAttribute('data-id');
      const ev = events.find(e=>e.id===id);
      if(ev) openEventEditor(ev);
    });
  });
}

/* ---------------- render: greeting ---------------- */
function renderGreet(){
  const wd = ['日','一','二','三','四','五','六'][todayDate.getDay()];
  document.getElementById('greet').textContent = '今天是 '+(todayDate.getMonth()+1)+'月'+todayDate.getDate()+'日 星期'+wd;
}

/* ---------------- render: calendar ---------------- */
function syncQaDates(){
  document.getElementById('qaStart').value = selectedDateStr;
  document.getElementById('qaEnd').value = selectedDateStr;
}

function renderCalendar(){
  document.getElementById('calTitle').textContent = viewYear+' 年 '+(viewMonth+1)+' 月';
  const wd = document.getElementById('calWd');
  wd.innerHTML = '';
  ['日','一','二','三','四','五','六'].forEach(w=>{
    const el = document.createElement('div'); el.className='cal-wd'; el.textContent=w; wd.appendChild(el);
  });

  const firstDay = new Date(viewYear, viewMonth, 1);
  const firstIdx = firstDay.getDay();
  const daysInMonth = new Date(viewYear, viewMonth+1, 0).getDate();
  const totalCells = Math.ceil((firstIdx+daysInMonth)/7)*7;
  const gridStart = new Date(viewYear, viewMonth, 1-firstIdx);
  const numWeeks = totalCells/7;
  const maxLanes = 3;

  const weeksWrap = document.getElementById('calWeeks');
  weeksWrap.innerHTML = '';

  for(let w=0; w<numWeeks; w++){
    const weekStartDate = new Date(gridStart); weekStartDate.setDate(gridStart.getDate()+w*7);
    const weekEndDate = new Date(weekStartDate); weekEndDate.setDate(weekStartDate.getDate()+6);
    const weekStartStr = fmt(weekStartDate), weekEndStr = fmt(weekEndDate);
    const weekEl = document.createElement('div'); weekEl.className='cal-week';

    const daysRow = document.createElement('div'); daysRow.className='week-days';
    for(let i=0;i<7;i++){
      const d = new Date(weekStartDate); d.setDate(weekStartDate.getDate()+i);
      const dStr = fmt(d);
      const cell = document.createElement('div'); cell.className='day-num';
      if(d.getMonth()!==viewMonth) cell.classList.add('muted');
      if(dStr===selectedDateStr) cell.classList.add('selected');
      if(dStr===todayStr) cell.classList.add('today');
      const badge = document.createElement('span'); badge.className='badge'; badge.textContent=d.getDate();
      cell.appendChild(badge);
      cell.addEventListener('click', ()=>{ selectedDateStr=dStr; syncQaDates(); renderAll(); });
      cell.addEventListener('dblclick', (e)=>{
        e.stopPropagation();
        selectedDateStr=dStr; syncQaDates(); renderAll();
        const ti = document.getElementById('qaInput');
        ti.scrollIntoView({behavior:'smooth',block:'center'}); ti.focus();
      });
      daysRow.appendChild(cell);
    }
    weekEl.appendChild(daysRow);

    const items = events
      .filter(e => e.endDate >= weekStartStr && e.startDate <= weekEndStr)
      .map(e => {
        const dispStart = e.startDate > weekStartStr ? e.startDate : weekStartStr;
        const dispEnd = e.endDate < weekEndStr ? e.endDate : weekEndStr;
        return { ev:e, dispStart, dispEnd, startCol: parseYMD(dispStart).getDay()+1, endCol: parseYMD(dispEnd).getDay()+1 };
      })
      .sort((a,b)=> a.startCol-b.startCol || (b.endCol-b.startCol)-(a.endCol-a.startCol));

    const lanes = [];
    items.forEach(item=>{
      let placedLane = -1;
      for(let li=0; li<lanes.length; li++){
        if(lanes[li].every(x => item.startCol > x.endCol || item.endCol < x.startCol)){ placedLane = li; break; }
      }
      if(placedLane===-1){ lanes.push([]); placedLane = lanes.length-1; }
      lanes[placedLane].push(item); item.lane = placedLane;
    });

    const lanesWrap = document.createElement('div'); lanesWrap.className='week-lanes';
    const shownLanes = Math.min(lanes.length, maxLanes);
    for(let li=0; li<shownLanes; li++){
      const row = document.createElement('div'); row.className='lane-row';
      lanes[li].forEach(item=>{
        const bar = document.createElement('div');
        const kind = effectiveKind(item.ev, todayStr);
        bar.className = 'bar '+kind;
        if(item.dispStart===item.ev.startDate) bar.classList.add('round-l');
        if(item.dispEnd===item.ev.endDate) bar.classList.add('round-r');
        bar.style.gridColumn = item.startCol+' / '+(item.endCol+1);
        const timePrefix = (!item.ev.allDay && item.ev.startTime) ? item.ev.startTime+' ' : '';
        bar.textContent = timePrefix+item.ev.title;
        bar.title = item.ev.title + (item.ev.startDate!==item.ev.endDate ? ' ('+item.ev.startDate+' ~ '+item.ev.endDate+')' : '');
        bar.addEventListener('click', (e)=>{ e.stopPropagation(); openEventEditor(item.ev); });
        row.appendChild(bar);
      });
      lanesWrap.appendChild(row);
    }

    const dayCounts = new Array(7).fill(0);
    items.forEach(item=>{ for(let c=item.startCol; c<=item.endCol; c++) dayCounts[c-1]++; });
    if(dayCounts.some(c=>c>shownLanes)){
      const orow = document.createElement('div'); orow.className='lane-row';
      for(let i=0;i<7;i++){
        if(dayCounts[i] > shownLanes){
          const dActual = new Date(weekStartDate); dActual.setDate(weekStartDate.getDate()+i);
          const dStrActual = fmt(dActual);
          const more = document.createElement('div'); more.className='day-more';
          more.style.gridColumn = (i+1)+' / '+(i+2);
          more.textContent = '+'+(dayCounts[i]-shownLanes);
          more.addEventListener('click', (e)=>{ e.stopPropagation(); openDayPopover(dStrActual); });
          orow.appendChild(more);
        }
      }
      lanesWrap.appendChild(orow);
    }
    weekEl.appendChild(lanesWrap);
    weeksWrap.appendChild(weekEl);
  }
}

/* ---------------- render: board ---------------- */
function fmtTimeLabel(ev){
  if(ev.allDay) return '';
  if(ev.startTime && ev.endTime) return ev.startTime+'-'+ev.endTime+' ';
  if(ev.startTime) return ev.startTime+' ';
  return '';
}
function taskCard(ev, kind){
  const card = document.createElement('div'); card.className = 'task';
  if(kind === 'done'){
    const chk = document.createElement('div'); chk.className = 'done-check'; chk.title='取消勾選';
    chk.addEventListener('click', ()=>{ ev.completed=false; ev.completedDate=null; saveEvents(); renderAll(); });
    card.appendChild(chk);
  } else {
    const circle = document.createElement('div'); circle.className = 'circle';
    circle.addEventListener('click', ()=>{ ev.completed=true; ev.completedDate=todayStr; saveEvents(); renderAll(); });
    card.appendChild(circle);
  }
  const textWrap = document.createElement('div'); textWrap.className='task-text';
  textWrap.textContent = fmtTimeLabel(ev)+ev.title;
  if(kind === 'thisweek'){
    const when = document.createElement('span'); when.className='task-when';
    const d = parseYMD(ev.startDate); when.textContent = (d.getMonth()+1)+'/'+d.getDate();
    textWrap.appendChild(when);
  }
  if(kind === 'inprogress' && ev.endDate !== ev.startDate){
    const when = document.createElement('span'); when.className='task-when';
    const s=parseYMD(ev.startDate), e2=parseYMD(ev.endDate);
    when.textContent = (s.getMonth()+1)+'/'+s.getDate()+' ~ '+(e2.getMonth()+1)+'/'+e2.getDate();
    textWrap.appendChild(when);
  }
  if(kind === 'done'){
    const d = parseYMD(ev.completedDate);
    const tag = document.createElement('span'); tag.className='done-tag';
    tag.textContent = (d.getMonth()+1)+'/'+d.getDate()+' 完成';
    textWrap.appendChild(tag);
  }
  card.appendChild(textWrap);
  const x = document.createElement('button'); x.className='x'; x.textContent='✕';
  x.addEventListener('click', ()=>{ events = events.filter(e=>e.id!==ev.id); saveEvents(); renderAll(); });
  card.appendChild(x);
  return card;
}
function renderBoard(){
  const week = weekRange(selectedDateStr);
  const todo = events.filter(e => effectiveKind(e,todayStr)==='todo' && e.startDate===selectedDateStr)
                      .sort((a,b)=> (a.startTime||'').localeCompare(b.startTime||''));
  const inprogress = events.filter(e => effectiveKind(e,todayStr)==='inprogress');
  const thisweek = events.filter(e => effectiveKind(e,todayStr)==='todo' && e.startDate!==selectedDateStr && inRange(e.startDate, week))
                          .sort((a,b)=> a.startDate.localeCompare(b.startDate));
  const done = events.filter(e => e.completed && e.completedDate && inRange(e.completedDate, week))
                      .sort((a,b)=> b.completedDate.localeCompare(a.completedDate));
  fillCol('col-todo', todo, 'todo', '這天還沒有待完成的項目');
  fillCol('col-inprogress', inprogress, 'inprogress', '目前沒有進行中的項目');
  fillCol('col-thisweek', thisweek, 'thisweek', '本週沒有可先做的項目');
  fillCol('col-done', done, 'done', '本週還沒有完成的項目');
}
function fillCol(id, list, kind, emptyMsg){
  const el = document.getElementById(id); el.innerHTML = '';
  if(list.length===0){ const e = document.createElement('div'); e.className='empty'; e.textContent = emptyMsg; el.appendChild(e); return; }
  list.forEach(ev => el.appendChild(taskCard(ev, kind)));
}

/* ---------------- render all ---------------- */
function renderAll(){
  tickExpiredRanges();
  renderGreet();
  renderCalendar();
  renderBoard();
}

/* ==================================================================
   NOTEPAD (contenteditable, freeform like a real notes app)
   ================================================================== */
function normalizeNotepad(doc, npEl){
  if(npEl.children.length===0){
    const line = doc.createElement('div'); line.className='line'; line.innerHTML='<br>';
    npEl.appendChild(line);
  }
  Array.from(npEl.children).forEach(child=>{
    if(child.tagName==='DIV' && !child.classList.contains('line')) child.classList.add('line');
  });
}
function refreshNotepadEmptyState(npEl){
  const empty = npEl.textContent.replace(/\u00A0/g,'').trim()==='';
  npEl.classList.toggle('is-empty', empty);
}
function findLineAncestor(node, npEl){
  let cur = node.nodeType===3 ? node.parentElement : node;
  while(cur && cur.parentElement !== npEl) cur = cur.parentElement;
  return cur;
}
function setupNotepad(doc, npEl, boldBtn, checkBtn){
  try{ doc.execCommand('defaultParagraphSeparator', false, 'div'); }catch(err){}

  const onChange = debounce(function(){
    normalizeNotepad(doc, npEl);
    refreshNotepadEmptyState(npEl);
    const html = npEl.innerHTML;
    saveNotepadHTML(html);
    // mirror to the other window if it exists and is not the one being typed in
    if(doc === document){
      if(stickyWin && !stickyWin.closed){
        const sp = stickyWin.document.getElementById('notepad');
        if(sp && stickyWin.document.activeElement !== sp) sp.innerHTML = html;
      }
    } else {
      const mainNp = document.getElementById('notepad');
      if(document.activeElement !== mainNp){ mainNp.innerHTML = html; refreshNotepadEmptyState(mainNp); }
    }
  }, 400);

  npEl.addEventListener('input', onChange);

  npEl.addEventListener('keydown', function(e){
    if(e.key === 'Enter'){
      setTimeout(()=>{
        const sel = doc.getSelection();
        if(!sel || !sel.anchorNode) return;
        const curLine = findLineAncestor(sel.anchorNode, npEl);
        if(curLine){
          const prev = curLine.previousElementSibling;
          if(prev){
            const txt = prev.textContent.trim();
            if(txt && tryConsumeLineAsEvent(txt)){ prev.remove(); }
          }
        }
        onChange();
      }, 0);
    }
  });

  npEl.addEventListener('click', function(e){
    const chk = e.target.closest ? e.target.closest('.chk') : null;
    if(chk){
      chk.classList.toggle('checked');
      const line = findLineAncestor(chk, npEl);
      if(line) line.classList.toggle('done-line', chk.classList.contains('checked'));
      onChange();
    }
  });

  if(boldBtn){
    boldBtn.addEventListener('click', ()=>{ npEl.focus(); doc.execCommand('bold'); onChange(); });
  }
  if(checkBtn){
    checkBtn.addEventListener('click', ()=>{
      npEl.focus();
      const sel = doc.getSelection();
      if(!sel || !sel.anchorNode) return;
      const curLine = findLineAncestor(sel.anchorNode, npEl);
      if(!curLine || curLine.querySelector('.chk')) return;
      const chk = doc.createElement('span'); chk.className='chk'; chk.setAttribute('contenteditable','false');
      curLine.insertBefore(chk, curLine.firstChild);
      const sp = doc.createTextNode('\u00A0');
      chk.after(sp);
      onChange();
    });
  }
}
setupNotepad(document, document.getElementById('notepad'), document.getElementById('tbBold'), document.getElementById('tbCheck'));

/* ---------------- events: calendar nav ---------------- */
document.getElementById('btnPrev').addEventListener('click', ()=>{ viewMonth--; if(viewMonth<0){ viewMonth=11; viewYear--; } renderCalendar(); });
document.getElementById('btnNext').addEventListener('click', ()=>{ viewMonth++; if(viewMonth>11){ viewMonth=0; viewYear++; } renderCalendar(); });
document.getElementById('btnToday').addEventListener('click', ()=>{
  viewYear = todayDate.getFullYear(); viewMonth = todayDate.getMonth();
  selectedDateStr = todayStr; syncQaDates(); renderAll();
});

/* ---------------- events: quick add ---------------- */
const qaDetails = document.getElementById('qaDetails');
document.getElementById('qaStartTimeWrap').innerHTML = buildTimeSelectHTML('qaStart', '09:00');
document.getElementById('qaEndTimeWrap').innerHTML = buildTimeSelectHTML('qaEnd', '10:00');
document.getElementById('qaToggle').addEventListener('click', ()=>{
  qaDetails.classList.toggle('show');
  if(qaDetails.classList.contains('show')) syncQaDates();
});
document.getElementById('qaAllDay').addEventListener('change', function(){
  document.getElementById('qaTimeRow').style.display = this.checked ? 'none' : 'flex';
});
function quickAdd(){
  const titleInput = document.getElementById('qaInput');
  const title = titleInput.value.trim();
  if(!title) return;
  let startDate = selectedDateStr, endDate = selectedDateStr, startTime=null, endTime=null, allDay=true;
  if(qaDetails.classList.contains('show')){
    const s = document.getElementById('qaStart').value;
    const e2 = document.getElementById('qaEnd').value;
    startDate = s || selectedDateStr; endDate = e2 || startDate;
    if(endDate < startDate) endDate = startDate;
    allDay = document.getElementById('qaAllDay').checked;
    if(!allDay){ startTime = getTimeSelectValue('qaStart'); endTime = getTimeSelectValue('qaEnd'); }
  }
  events.push({ id: uid(), title, startDate, endDate, startTime, endTime, allDay, manualInProgress:false, completed:false, completedDate:null });
  titleInput.value = ''; saveEvents(); renderAll();
}
document.getElementById('qaAdd').addEventListener('click', quickAdd);
document.getElementById('qaInput').addEventListener('keydown', e=>{ if(e.key==='Enter') quickAdd(); });

/* ---------------- sticky note popup ---------------- */
function openSticky(){
  if(stickyWin && !stickyWin.closed){ stickyWin.focus(); return; }
  stickyWin = window.open('', 'stickyNote', 'width=320,height=440,resizable=yes');
  if(!stickyWin){ showToast('瀏覽器阻擋了彈出視窗，請允許彈出視窗後再試一次'); return; }
  const doc = stickyWin.document;
  doc.open();
  doc.write('<!DOCTYPE html><html><head><meta charset="UTF-8"><title>隨手記 便利貼</title><style>'
    + "body{margin:0;background:#F9DE7E;font-family:'Noto Sans TC',sans-serif;padding:16px;box-sizing:border-box;height:100vh;display:flex;flex-direction:column;}"
    + "h2{margin:0 0 8px;font-size:14px;color:#5B4A1A;}"
    + ".tools{display:flex;gap:6px;margin-bottom:8px;}"
    + ".tbtn{width:24px;height:24px;border-radius:6px;border:1px solid #C9AE55;background:transparent;cursor:pointer;font-size:11px;color:#5B4A1A;}"
    + ".notepad{flex:1;overflow-y:auto;outline:none;font-size:13.5px;line-height:24px;color:#5B4A1A;position:relative;}"
    + ".notepad.is-empty::before{content:attr(data-placeholder);color:#B8A05A;position:absolute;top:0;left:0;pointer-events:none;}"
    + ".notepad .line{min-height:24px;line-height:24px;}"
    + ".notepad .line.done-line .txt{text-decoration:line-through;opacity:.55;}"
    + ".notepad b{font-weight:700;}"
    + ".notepad .chk{display:inline-flex;width:12px;height:12px;border-radius:50%;border:2px solid #5B4A1A;margin-right:5px;vertical-align:middle;cursor:pointer;}"
    + ".notepad .chk.checked{background:#5B4A1A;}"
    + '</style></head><body>'
    + '<h2>📌 隨手記</h2>'
    + '<div class="tools"><button class="tbtn" id="b">B</button><button class="tbtn" id="c">◯</button></div>'
    + '<div class="notepad" id="notepad" contenteditable="true" data-placeholder="在這裡打字..."></div>'
    + '</body></html>');
  doc.close();

  const spNp = doc.getElementById('notepad');
  spNp.innerHTML = document.getElementById('notepad').innerHTML;
  normalizeNotepad(doc, spNp);
  refreshNotepadEmptyState(spNp);
  setupNotepad(doc, spNp, doc.getElementById('b'), doc.getElementById('c'));
}
document.getElementById('popSticky').addEventListener('click', openSticky);

/* ---------------- init ---------------- */
syncQaDates();
loadState();
</script>
</body>
</html>
