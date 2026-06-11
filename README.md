<!DOCTYPE html>
<html lang="th">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>ระบบตรวจวัดภาวะสมาธิสั้น</title>
<style>
@import url('https://fonts.googleapis.com/css2?family=IBM+Plex+Sans+Thai:wght@300;400;500&family=IBM+Plex+Mono:wght@400;500&display=swap');
*,*::before,*::after{box-sizing:border-box;margin:0;padding:0}
:root{
  --bg:#e8f0fe;--sf:#ffffff;--sf2:#dce8fd;--sf3:#c8d9fb;
  --bd:rgba(66,120,210,0.18);--bd2:rgba(66,120,210,0.30);
  --tx:#1a2540;--tx2:#3d5080;--tx3:#7a90b8;
  --ac:#1a6fc4;--a2:#1558a0;--acl:#e8f0fe;
  --ok:#157a50;--wn:#b83a1a;--am:#8a6000;
  --fn:'IBM Plex Sans Thai',sans-serif;--mo:'IBM Plex Mono',monospace;
}
html,body{height:100%;overflow:hidden}
body{background:var(--bg);color:var(--tx);font-family:var(--fn);font-size:13px;display:flex;flex-direction:column}

/* ── TOPBAR ── */
#topbar{display:flex;align-items:center;gap:10px;padding:7px 16px;
        background:var(--ac);flex-shrink:0;flex-wrap:wrap}
#topbar h1{font-size:13px;font-weight:500;color:#fff;letter-spacing:.1px;margin-right:4px}
.badge{font-family:var(--mo);font-size:10px;padding:1px 7px;border-radius:3px;border:1px solid}
.bs{color:#fff;background:rgba(255,255,255,.18);border-color:rgba(255,255,255,.35)}
.bo{color:#ffe4a0;background:rgba(255,200,0,.18);border-color:rgba(255,200,0,.4)}
.br{color:#ffb0a0;background:rgba(255,80,60,.18);border-color:rgba(255,80,60,.4)}
.bg{color:#a0ffcc;background:rgba(0,200,120,.18);border-color:rgba(0,200,120,.4)}
#topbar-right{margin-left:auto;display:flex;align-items:center;gap:6px}

/* ── TABS ── */
#tabs{display:flex;gap:0;border-bottom:2px solid var(--bd);flex-shrink:0;padding:0 14px;background:var(--sf)}
.tb{font-family:var(--fn);font-size:12px;padding:7px 16px;border:none;background:none;
    color:var(--tx2);cursor:pointer;border-bottom:2px solid transparent;margin-bottom:-2px;
    transition:color .15s,border-color .15s;font-weight:400}
.tb:hover{color:var(--ac)}.tb.active{color:var(--ac);border-bottom-color:var(--ac);font-weight:500}
.tp{display:none;flex:1;overflow:hidden}.tp.active{display:flex}

/* ── BUTTONS ── */
button.ctrl{font-family:var(--fn);font-size:12px;padding:5px 12px;border-radius:5px;
            border:1px solid var(--bd2);background:var(--sf);color:var(--tx);cursor:pointer;
            transition:background .15s,border-color .15s;white-space:nowrap}
button.ctrl:hover:not(:disabled){background:var(--sf2);border-color:var(--ac)}
button.ctrl.cp{border-color:var(--ac);color:#fff;background:var(--ac)}
button.ctrl.cp:hover:not(:disabled){background:var(--a2)}
button.ctrl.cs{border-color:var(--ok);color:var(--ok)}
button.ctrl.cs:hover:not(:disabled){background:rgba(21,122,80,.08)}
button.ctrl.cw{border-color:var(--am);color:var(--am)}
button.ctrl.cw:hover:not(:disabled){background:rgba(138,96,0,.08)}
button.ctrl:disabled{opacity:.35;cursor:not-allowed}

/* ── STAT CARD ── */
.sc{background:var(--sf);border:1px solid var(--bd);border-radius:8px;padding:10px 12px}
.sc-label{font-size:10px;color:var(--tx3);margin-bottom:4px;font-weight:500;letter-spacing:.3px;text-transform:uppercase}
.sc-val{font-family:var(--mo);font-size:22px;font-weight:500;line-height:1;color:var(--tx)}
.sc-unit{font-size:10px;color:var(--tx3);margin-top:3px}

/* ── PANELS ── */
.panel{background:var(--sf);border:1px solid var(--bd);border-radius:8px;padding:10px 12px;display:flex;flex-direction:column;gap:6px;overflow:hidden}
.pl{font-size:10px;color:var(--tx3);letter-spacing:.4px;text-transform:uppercase;font-weight:500}
canvas.ch{width:100%;border-radius:4px;background:var(--sf2);display:block}
.hl{display:flex;justify-content:space-between;font-family:var(--mo);font-size:9px;color:var(--tx3)}

/* ═══════════════════════
   TAB: ติดตามสายตา
   3 คอลัมน์: ค่า | วิดีโอ 50% | กราฟ
═══════════════════════ */
#tab-tracking{flex-direction:row;padding:10px 14px;gap:10px;overflow:hidden}
#trk-left{width:22%;flex-shrink:0;display:flex;flex-direction:column;gap:8px;overflow:hidden}
#trk-center{width:50%;flex-shrink:0;display:flex;flex-direction:column;gap:8px}
#trk-right{flex:1;min-width:0;display:flex;flex-direction:column;gap:8px;overflow:hidden}

/* video */
#video-box{position:relative;background:#0a0e1a;border:2px solid var(--bd2);border-radius:10px;overflow:hidden;flex:1;min-height:0}
#wg-video-container{position:relative;background:#0a0e1a;height:100%}
#wg-video-container video{width:100%!important;height:100%!important;object-fit:cover;transform:scaleX(-1);display:block}
#webgazerFaceOverlay{position:absolute!important;top:0!important;left:0!important;transform:scaleX(-1)!important;display:block!important;z-index:5;pointer-events:none}
#webgazerFaceFeedbackBox{display:none!important}
#sbar{position:absolute;bottom:0;left:0;right:0;background:rgba(10,14,26,.80);padding:5px 12px;
      font-family:var(--mo);font-size:10px;color:#9ab0d8;display:flex;align-items:center;gap:6px;z-index:10}
#sdot{width:7px;height:7px;border-radius:50%;background:rgba(154,176,216,.4);flex-shrink:0}
#sdot.active{background:#4edc9a;animation:pulse 1.5s infinite}
#sdot.warn{background:#f07050}
@keyframes pulse{0%,100%{opacity:1}50%{opacity:.35}}

/* ═══════════════════════
   TABS: ทดสอบจุด / อ่านข้อความ
═══════════════════════ */
#tab-task,#tab-reading{padding:12px 16px;flex-direction:row;gap:16px;overflow:hidden}
.task-left{flex:1;display:flex;flex-direction:column;gap:10px;overflow:auto}
.task-right{width:240px;flex-shrink:0;display:flex;flex-direction:column;gap:8px}
.cr{display:flex;align-items:center;gap:10px;font-size:12px;color:var(--tx2)}
.cr label{min-width:140px;flex-shrink:0}.cr input[type=range]{flex:1;min-width:0}
.cr span{font-family:var(--mo);font-size:11px;min-width:44px;text-align:right;color:var(--tx)}
select{font-family:var(--fn);font-size:12px;padding:4px 8px;border-radius:5px;
       border:1px solid var(--bd2);background:var(--sf);color:var(--tx);flex:1}
.ibox{background:var(--sf2);border:1px solid var(--bd);border-radius:7px;padding:10px 12px;font-size:11px;color:var(--tx2);line-height:2}
.ibox .il{font-size:10px;color:var(--tx3);letter-spacing:.4px;text-transform:uppercase;margin-bottom:5px;font-weight:500}

/* ═══════════════════════
   TAB: ผลการทดสอบ
═══════════════════════ */
#tab-results{padding:10px 14px;flex-direction:row;gap:12px;overflow:hidden}
#res-left{flex:1;min-width:0;display:flex;flex-direction:column;gap:10px;overflow:auto}
#res-right{width:260px;flex-shrink:0;display:flex;flex-direction:column;gap:8px;overflow:auto}
.as3{display:grid;grid-template-columns:repeat(3,1fr);gap:8px}
.as4{display:grid;grid-template-columns:repeat(4,1fr);gap:8px}
.as2{display:grid;grid-template-columns:1fr 1fr;gap:8px}
.tw{overflow:auto;flex:1}
table{width:100%;border-collapse:collapse;font-size:11px}
th{font-family:var(--mo);font-size:9px;color:var(--tx3);font-weight:500;text-align:left;
   padding:6px 8px;border-bottom:2px solid var(--bd2);letter-spacing:.3px;background:var(--sf2)}
td{padding:5px 8px;border-bottom:1px solid var(--bd);color:var(--tx2);font-family:var(--mo);font-size:11px}
td.hit{color:var(--ok);font-weight:500}td.miss{color:var(--wn)}
.ex{padding-top:8px;border-top:1px solid var(--bd);display:flex;gap:6px;flex-wrap:wrap;align-items:center;flex-shrink:0}
.ex span{font-size:11px;color:var(--tx2)}

/* ── ระดับสมาธิ badge ── */
.attn-badge{display:inline-block;padding:4px 14px;border-radius:20px;font-size:13px;font-weight:500;margin:2px 0}
.attn-high{background:#d4f5e6;color:#0c6e3e;border:1px solid #7dd8ac}
.attn-med{background:#fff3cc;color:#7a5200;border:1px solid #f0c040}
.attn-low{background:#fde0d8;color:#932010;border:1px solid #f09070}

/* ══ OVERLAYS ══ */
#cal-overlay{display:none;position:fixed;inset:0;z-index:9500;background:#030814;cursor:crosshair}
#cal-overlay.active{display:block}
#cal-title{position:absolute;top:22px;left:50%;transform:translateX(-50%);font-family:var(--mo);font-size:12px;color:rgba(180,210,255,.55);white-space:nowrap;pointer-events:none}
#cal-prog{position:absolute;top:46px;left:50%;transform:translateX(-50%);font-family:var(--mo);font-size:11px;color:rgba(180,210,255,.3);pointer-events:none}
.cal-pt{position:absolute;width:22px;height:22px;border-radius:50%;transform:translate(-50%,-50%);cursor:pointer;transition:transform .15s,background .15s}
.cal-pt::before{content:'';position:absolute;inset:7px;border-radius:50%;background:rgba(255,255,255,.92)}
.cal-pt.waiting{background:rgba(180,210,255,.18);border:1.5px solid rgba(180,210,255,.35)}
.cal-pt.active-pt{background:rgba(78,156,255,.4);border:2px solid #4e9cff;transform:translate(-50%,-50%) scale(1.4)}
.cal-pt.done{background:rgba(78,220,154,.3);border:1.5px solid #4edc9a;transform:translate(-50%,-50%) scale(.8)}
#cal-esc{position:absolute;top:18px;right:20px;font-family:var(--mo);font-size:11px;color:rgba(180,210,255,.3);cursor:pointer}
#cal-esc:hover{color:rgba(180,210,255,.6)}
#stim-overlay{display:none;position:fixed;inset:0;z-index:9000;background:#050a18;cursor:none}
#stim-overlay.active{display:block}
#stim-dot{position:absolute;width:26px;height:26px;border-radius:50%;background:#fff;transform:translate(-50%,-50%);pointer-events:none;transition:left .12s,top .12s}
#stim-dot::after{content:'';position:absolute;inset:7px;border-radius:50%;background:#4e9cff}
#stim-dot.flash{background:#ffe070}
#stim-ctr{position:absolute;top:22px;left:50%;transform:translateX(-50%);font-family:var(--mo);font-size:12px;color:rgba(180,210,255,.38);pointer-events:none}
#stim-res{position:absolute;bottom:30px;left:50%;transform:translateX(-50%);font-family:var(--mo);font-size:11px;color:rgba(180,210,255,.45);white-space:nowrap;pointer-events:none}
#stim-prog{position:absolute;bottom:0;left:0;height:3px;background:#4e9cff;transition:width .12s linear}
#stim-esc{position:absolute;top:18px;right:20px;font-family:var(--mo);font-size:11px;color:rgba(180,210,255,.28);cursor:pointer}
#stim-esc:hover{color:rgba(180,210,255,.6)}
#read-overlay{display:none;position:fixed;inset:0;z-index:9000;background:#050a18;overflow:hidden}
#read-overlay.active{display:flex;flex-direction:column}
#rh{padding:14px 40px 10px;border-bottom:1px solid rgba(180,210,255,.1);display:flex;justify-content:space-between;align-items:center;flex-shrink:0}
#rh span{font-family:var(--mo);font-size:11px;color:rgba(180,210,255,.35)}
#read-esc{font-family:var(--mo);font-size:11px;color:rgba(180,210,255,.3);cursor:pointer}
#read-esc:hover{color:rgba(180,210,255,.6)}
#rb{flex:1;overflow:auto;padding:36px 10%;max-width:820px;margin:0 auto;width:100%}
#read-passage{line-height:2.2;color:rgba(220,235,255,.9);letter-spacing:.01em;font-family:'IBM Plex Sans Thai',sans-serif;font-weight:300}
.read-line{position:relative;margin-bottom:2px;cursor:default;border-radius:3px}
.read-line.gazed{background:rgba(78,156,255,.14)}
#rf{padding:10px 40px 14px;border-top:1px solid rgba(180,210,255,.1);display:flex;gap:10px;align-items:center;flex-shrink:0}
#rprog{flex:1;height:3px;background:rgba(180,210,255,.1);border-radius:2px}
#rprog-bar{height:100%;background:#4e9cff;border-radius:2px;transition:width .3s}
#rtimer{font-family:var(--mo);font-size:11px;color:rgba(180,210,255,.35);min-width:36px;text-align:right}
#rnext{font-family:var(--fn);font-size:12px;padding:5px 14px;border-radius:5px;border:1px solid rgba(180,210,255,.2);background:transparent;color:rgba(220,235,255,.65);cursor:pointer}
#rnext:hover{background:rgba(180,210,255,.1)}
#read-gaze-dot{position:fixed;width:12px;height:12px;border-radius:50%;background:rgba(78,156,255,.5);border:1.5px solid #4e9cff;transform:translate(-50%,-50%);pointer-events:none;z-index:9100;display:none}
</style>
</head>
<body>

<!-- OVERLAYS -->
<div id="cal-overlay">
  <div id="cal-title">คลิกจุดสีขาวทีละจุด ตามลำดับที่ไฮไลต์สีฟ้า</div>
  <div id="cal-prog">จุดที่ <span id="cal-cur">0</span> / 9</div>
  <div id="cal-esc" onclick="endCalibration(false)">ESC / ยกเลิก</div>
</div>
<div id="stim-overlay">
  <div id="stim-dot"></div>
  <div id="stim-ctr">จุดที่ <span id="sc-cur">0</span>/<span id="sc-tot">0</span></div>
  <div id="stim-res"></div>
  <div id="stim-prog"></div>
  <div id="stim-esc" onclick="endTask()">ESC / ออก</div>
</div>
<div id="read-overlay">
  <div id="rh">
    <span id="read-info">ตอนที่ <span id="rp-cur">1</span>/<span id="rp-tot">1</span></span>
    <div id="read-esc" onclick="endReading()">ESC / ออก</div>
  </div>
  <div id="rb"><div id="read-passage"></div></div>
  <div id="read-gaze-dot"></div>
  <div id="rf">
    <div id="rprog"><div id="rprog-bar" style="width:0%"></div></div>
    <div id="rtimer">0s</div>
    <button id="rnext" onclick="endReading()">เสร็จ / ถัดไป →</button>
  </div>
</div>

<!-- TOPBAR -->
<div id="topbar">
  <h1>ระบบตรวจวัดภาวะสมาธิสั้นในนักเรียนระดับมัธยมศึกษาตอนต้น</h1>
  <span class="badge bs" id="lib-badge">กำลังโหลด...</span>
  <span class="badge bg" id="cal-badge" style="display:none">ปรับเทียบแล้ว</span>
  <div id="topbar-right">
    <button class="ctrl cp" id="btn-start" onclick="startET()" disabled>เริ่มต้น</button>
    <button class="ctrl cs" id="btn-cal"   onclick="startCalibration()" disabled>ปรับเทียบ (9 จุด)</button>
    <button class="ctrl"    id="btn-stop"  onclick="stopET()" disabled>หยุด</button>
    <button class="ctrl"    onclick="resetStats()">รีเซ็ต</button>
  </div>
</div>

<!-- TABS -->
<div id="tabs">
  <button class="tb active" onclick="switchTab('tracking',this)">ติดตามสายตา</button>
  <button class="tb" onclick="switchTab('task',this)">ทดสอบจุด</button>
  <button class="tb" onclick="switchTab('reading',this)">อ่านข้อความ</button>
  <button class="tb" onclick="switchTab('results',this)">ผลการทดสอบ</button>
</div>

<!-- TAB: ติดตามสายตา -->
<div id="tab-tracking" class="tp active">

  <!-- ซ้าย: ค่าสถิติ -->
  <div id="trk-left">
    <div class="sc">
      <div class="sc-label">ตำแหน่งสายตา</div>
      <div class="sc-val" style="font-size:16px"><span id="s-gx">—</span>, <span id="s-gy">—</span></div>
      <div class="sc-unit"><span id="s-gaze" style="color:var(--ac);font-weight:500">—</span></div>
    </div>
    <div class="sc"><div class="sc-label">จำนวนตัวอย่าง</div><div class="sc-val" id="s-samples">0</div><div class="sc-unit"><span id="s-time">0s</span></div></div>
    <div class="sc"><div class="sc-label">ครั้งที่จ้องนิ่ง</div><div class="sc-val" id="m-fix">0</div><div class="sc-unit">Fixation &gt;200ms</div></div>
    <div class="sc"><div class="sc-label">ครั้งที่กระตุก</div><div class="sc-val" id="m-sac">0</div><div class="sc-unit">Saccade &gt;30px</div></div>
    <div class="sc"><div class="sc-label">ระยะจ้องเฉลี่ย</div><div class="sc-val" id="m-fdur">—</div><div class="sc-unit">ms</div></div>
    <div class="sc"><div class="sc-label">พื้นที่กระจายสายตา</div><div class="sc-val" id="m-bcea" style="font-size:16px">—</div><div class="sc-unit">BCEA (px²)</div></div>
    <div style="margin-top:auto">
      <button class="ctrl" style="width:100%" onclick="exportCSV('gaze')">ส่งออก CSV</button>
    </div>
  </div>

  <!-- กลาง: วิดีโอ 50% -->
  <div id="trk-center">
    <div id="video-box">
      <div id="wg-video-container"></div>
      <div id="sbar"><div id="sdot"></div><span id="stxt">กำลังโหลด...</span></div>
    </div>
  </div>

  <!-- ขวา: กราฟ -->
  <div id="trk-right">
    <div class="panel" style="flex-shrink:0">
      <div class="pl">แผนที่ความร้อน — แนวนอน (X)</div>
      <canvas class="ch" id="heatmap" style="height:58px"></canvas>
      <div class="hl"><span>ซ้าย</span><span>กลาง</span><span>ขวา</span></div>
    </div>
    <div class="panel" style="flex:1;min-height:60px">
      <div class="pl">แนวสายตา — แนวตั้ง (Y)</div>
      <canvas class="ch" id="timeline" style="flex:1;min-height:0"></canvas>
    </div>
    <div class="panel" style="flex-shrink:0">
      <div style="font-size:11px;color:var(--tx2);line-height:2">
        <span style="color:var(--ok);font-weight:500">จ้องนิ่ง</span> = สายตาหยุด &gt;200ms &nbsp;·&nbsp;
        <span style="color:var(--wn);font-weight:500">กระตุก</span> = เกิน 30px &nbsp;·&nbsp;
        <span style="color:var(--ac);font-weight:500">BCEA สูง</span> = สายตากระจาย (สัมพันธ์กับ ADHD)
      </div>
    </div>
  </div>
</div>

<!-- TAB: ทดสอบจุด -->
<div id="tab-task" class="tp">
  <div class="task-left">
    <div class="ibox" style="font-size:12px;color:var(--tx2)">จุดสีขาวปรากฏทีละจุดบนหน้าจอมืด — จ้องมองจุดนั้นโดยไม่ขยับหัว ระบบวัดความแม่นยำของสายตา</div>
    <div class="cr"><label>จำนวนจุด</label><input type="range" id="cfg-dots" min="5" max="25" step="1" value="9" oninput="document.getElementById('cfg-dots-v').textContent=this.value"><span id="cfg-dots-v">9</span></div>
    <div class="cr"><label>เวลาต่อจุด (วินาที)</label><input type="range" id="cfg-dur" min="1" max="5" step="0.5" value="2" oninput="document.getElementById('cfg-dur-v').textContent=this.value"><span id="cfg-dur-v">2</span></div>
    <div class="cr"><label>รูปแบบจุด</label><select id="cfg-pat"><option value="grid">ตาราง</option><option value="random">สุ่ม</option><option value="center-out">กลางออกขอบ</option></select></div>
    <div class="cr"><label>ระยะคลาดเคลื่อนที่ยอมรับ</label><input type="range" id="cfg-thr" min="50" max="300" step="10" value="120" oninput="document.getElementById('cfg-thr-v').textContent=this.value+'px'"><span id="cfg-thr-v">120px</span></div>
    <div style="display:flex;gap:8px;align-items:center;margin-top:6px">
      <button class="ctrl cp" id="btn-task" onclick="startTask()" disabled>เริ่มทดสอบจุด</button>
      <span style="font-size:11px;color:var(--tx3)">* ต้องเปิดติดตามสายตาและปรับเทียบก่อน</span>
    </div>
  </div>
  <div class="task-right">
    <div class="ibox">
      <div class="il">วิธีทดสอบ</div>
      1. เปิดติดตามสายตา<br>
      2. กด "ปรับเทียบ (9 จุด)"<br>
      3. เริ่มทดสอบจุด<br>
      4. จ้องมองจุดสีขาวแต่ละจุด<br>
      5. ดูผลในหน้าผลการทดสอบ
    </div>
    <div class="ibox">
      <div class="il">ค่าที่วัดได้</div>
      ความคลาดเคลื่อน (px)<br>
      อัตราโดน threshold<br>
      การกระจายสายตา
    </div>
  </div>
</div>

<!-- TAB: อ่านข้อความ -->
<div id="tab-reading" class="tp">
  <div class="task-left">
    <div class="ibox" style="font-size:12px;color:var(--tx2)">ข้อความแสดงบนหน้าจอมืด — อ่านตามปกติ ระบบวัดรูปแบบสายตา การอ่านซ้ำ และความเร็วอ่าน</div>
    <div class="cr"><label>ข้อความ</label><select id="cfg-passage"><option value="0">บทที่ 1 — ดาวอังคาร</option><option value="1">บทที่ 2 — คณิตศาสตร์</option><option value="2">บทที่ 3 — ประวัติศาสตร์ไทย</option></select></div>
    <div class="cr"><label>ขนาดตัวอักษร</label><input type="range" id="cfg-fontsize" min="16" max="28" step="1" value="20" oninput="document.getElementById('cfg-fs-v').textContent=this.value+'px'"><span id="cfg-fs-v">20px</span></div>
    <div class="cr"><label>เวลาอ่าน (วินาที)</label><input type="range" id="cfg-readtime" min="30" max="180" step="10" value="60" oninput="document.getElementById('cfg-rt-v').textContent=this.value+'s'"><span id="cfg-rt-v">60s</span></div>
    <div style="display:flex;gap:8px;align-items:center;margin-top:6px">
      <button class="ctrl cw" id="btn-read" onclick="startReading()" disabled>เริ่มอ่านข้อความ</button>
      <span style="font-size:11px;color:var(--tx3)">* ต้องเปิดติดตามสายตาก่อน</span>
    </div>
  </div>
  <div class="task-right">
    <div class="ibox">
      <div class="il">ค่าที่วัดได้</div>
      การจ้องต่อบรรทัด<br>
      ครั้งที่ย้อนกลับ (อ่านซ้ำ)<br>
      ความเร็วอ่าน (คำ/นาที)<br>
      ครอบคลุมบรรทัด (%)<br>
      แผนที่ความร้อนขณะอ่าน
    </div>
    <div class="ibox">
      <div class="il">สัมพันธ์กับ ADHD</div>
      อ่านซ้ำมาก → วอกแวก<br>
      อ่านช้า → สมาธิสั้น<br>
      ข้ามบรรทัด → ไม่โฟกัส<br>
      จ้องมากต่อบรรทัด → ยาก
    </div>
  </div>
</div>

<!-- TAB: ผลการทดสอบ -->
<div id="tab-results" class="tp">
  <div id="res-left">
    <div id="no-results" style="color:var(--tx3);font-size:12px;padding:16px 0">ยังไม่มีผล — ทำทดสอบจุด หรือ อ่านข้อความ ก่อน</div>
    <div id="has-results" style="display:none">

      <!-- วิเคราะห์ระดับสมาธิ -->
      <div id="attn-analysis" class="panel" style="margin-bottom:10px;flex-shrink:0">
        <div class="pl">ผลวิเคราะห์ระดับสมาธิในการทำงาน</div>
        <div style="display:flex;align-items:center;gap:14px;flex-wrap:wrap;margin-top:4px">
          <div>
            <div style="font-size:11px;color:var(--tx3);margin-bottom:4px">ระดับสมาธิโดยรวม</div>
            <span id="attn-level-badge" class="attn-badge attn-high">—</span>
          </div>
          <div style="flex:1;min-width:180px">
            <div style="font-size:11px;color:var(--tx3);margin-bottom:4px">คะแนนสมาธิ (0–100)</div>
            <div style="background:var(--sf2);border-radius:20px;height:16px;overflow:hidden;border:1px solid var(--bd)">
              <div id="attn-bar" style="height:100%;width:0%;background:var(--ac);border-radius:20px;transition:width .8s ease"></div>
            </div>
            <div style="display:flex;justify-content:space-between;font-size:9px;color:var(--tx3);margin-top:2px"><span>ต่ำ</span><span>ปานกลาง</span><span>สูง</span></div>
          </div>
          <div style="text-align:right">
            <div id="attn-score" style="font-family:var(--mo);font-size:28px;font-weight:500;color:var(--ac)">—</div>
            <div style="font-size:10px;color:var(--tx3)">คะแนน</div>
          </div>
        </div>
        <div id="attn-detail" style="font-size:11px;color:var(--tx2);line-height:1.9;margin-top:6px;padding-top:6px;border-top:1px solid var(--bd)">—</div>
      </div>

      <!-- ผล Stimulus -->
      <div id="stim-res-sec">
        <div style="font-size:12px;font-weight:500;color:var(--tx);margin-bottom:8px">ผลทดสอบจุด (Sustained Attention)</div>
        <div class="as3">
          <div class="sc"><div class="sc-label">ความคลาดเคลื่อนเฉลี่ย</div><div class="sc-val" id="r-avg">—</div><div class="sc-unit">px</div></div>
          <div class="sc"><div class="sc-label">อัตราโดนเป้า</div><div class="sc-val" id="r-hit" style="color:var(--ok)">—</div><div class="sc-unit">ใน threshold</div></div>
          <div class="sc"><div class="sc-label">คลาดเคลื่อนสูงสุด</div><div class="sc-val" id="r-max" style="color:var(--wn)">—</div><div class="sc-unit">px</div></div>
        </div>
        <div style="font-size:10px;color:var(--tx3);letter-spacing:.3px;text-transform:uppercase;margin:10px 0 5px">แผนที่ความแม่นยำสายตา</div>
        <canvas id="accuracy-map" style="width:100%;height:180px;border-radius:6px;border:1px solid var(--bd);background:#050a18;display:block"></canvas>
      </div>

      <!-- ผล Reading -->
      <div id="read-res-sec" style="margin-top:14px;display:none">
        <div style="font-size:12px;font-weight:500;color:var(--tx);margin-bottom:8px">ผลทดสอบอ่านข้อความ (Reading)</div>
        <div class="as3">
          <div class="sc"><div class="sc-label">ความเร็วอ่าน</div><div class="sc-val" id="r-wpm">—</div><div class="sc-unit">คำ/นาที</div></div>
          <div class="sc"><div class="sc-label">ครั้งที่อ่านซ้ำ</div><div class="sc-val" id="r-reg" style="color:var(--wn)">—</div><div class="sc-unit">ครั้ง</div></div>
          <div class="sc"><div class="sc-label">ครอบคลุมบรรทัด</div><div class="sc-val" id="r-cov" style="color:var(--ok)">—</div><div class="sc-unit">%</div></div>
        </div>
        <div style="font-size:10px;color:var(--tx3);letter-spacing:.3px;text-transform:uppercase;margin:10px 0 5px">แผนที่ความร้อนขณะอ่าน</div>
        <canvas id="read-heatmap" style="width:100%;height:60px;border-radius:5px;border:1px solid var(--bd);background:var(--sf);display:block"></canvas>
        <div style="font-size:10px;color:var(--tx3);letter-spacing:.3px;text-transform:uppercase;margin:10px 0 5px">การจ้องต่อบรรทัด</div>
        <canvas id="line-chart" style="width:100%;height:100px;border-radius:5px;border:1px solid var(--bd);background:var(--sf);display:block"></canvas>
      </div>

      <div class="ex" style="margin-top:12px">
        <span>ส่งออก:</span>
        <button class="ctrl" onclick="exportCSV('task')">ทดสอบจุด CSV</button>
        <button class="ctrl" onclick="exportCSV('reading')">อ่านข้อความ CSV</button>
        <button class="ctrl" onclick="exportCSV('gaze')">สายตา CSV</button>
      </div>
    </div>
  </div>
  <div id="res-right">
    <div id="no-table" style="color:var(--tx3);font-size:11px;padding:10px 0">ผลทดสอบจะแสดงที่นี่</div>
    <div id="has-table" style="display:none;flex-direction:column;gap:8px;height:100%">
      <div style="font-size:10px;color:var(--tx3);letter-spacing:.3px;text-transform:uppercase;font-weight:500">รายละเอียดแต่ละจุด</div>
      <div class="tw">
        <table><thead><tr><th>จุด</th><th>ความคลาดเคลื่อน</th><th>ผล</th></tr></thead><tbody id="acc-tbody"></tbody></table>
      </div>
    </div>
  </div>
</div>

<script>
let running=false,startTime=null;
let gazeHistory=[],fixCount=0,sacCount=0;
let fixDurs=[],fixStart=null,inFix=false;
let gazeYHistory=[],gazeLog=[];
const heatBins=new Array(40).fill(0);
const FIX_PX=30,FIX_MS=200;
let currentGX=null,currentGY=null,calibrated=false;
let taskActive=false,taskDots=[],taskIdx=0,taskResults=[],taskTimer=null,taskDur=2000,dotSamples=[];
let readActive=false,readPassageIdx=0,readTimer=null,readDur=60000,readStartTime=null;
let readGazeLog=[],readLines=[],prevGX=null;

const PASSAGES=[
  {title:'ดาวอังคาร',words:120,text:`ดาวอังคารเป็นดาวเคราะห์ลำดับที่สี่จากดวงอาทิตย์ในระบบสุริยะของเรา มันมีขนาดเล็กกว่าโลกประมาณครึ่งหนึ่ง และมีบรรยากาศที่เบาบางมากจนเกือบไม่มีออกซิเจน

พื้นผิวของดาวอังคารปกคลุมด้วยฝุ่นและหินสีแดง เกิดจากออกไซด์ของเหล็กที่สะสมมาเป็นเวลานับล้านปี จึงทำให้ดาวอังคารได้รับฉายาว่า "ดาวแดง"

นักวิทยาศาสตร์ส่งยานสำรวจหลายลำไปยังดาวอังคาร เพื่อศึกษาความเป็นไปได้ที่จะพบสิ่งมีชีวิต หรือแม้แต่ตั้งถิ่นฐานของมนุษย์ในอนาคต`},
  {title:'คณิตศาสตร์ในชีวิต',words:110,text:`คณิตศาสตร์ไม่ได้มีอยู่แค่ในตำราเรียนเท่านั้น แต่ยังปรากฏอยู่รอบตัวเราในชีวิตประจำวัน ตั้งแต่การคิดเงินทอน การทำอาหาร ไปจนถึงการออกแบบอาคาร

เมื่อเราซื้อสินค้าลดราคา เราต้องคำนวณเปอร์เซ็นต์ส่วนลด เมื่อทำอาหาร เราต้องปรับสัดส่วนวัตถุดิบ สิ่งเหล่านี้ล้วนใช้ทักษะคณิตศาสตร์ทั้งสิ้น

การฝึกคิดเลขในชีวิตประจำวันช่วยพัฒนาทักษะการคิดวิเคราะห์ และทำให้เราตัดสินใจได้อย่างมีเหตุผลมากขึ้น`},
  {title:'ประวัติศาสตร์ไทย',words:115,text:`กรุงสุโขทัยถือเป็นราชธานีแห่งแรกของชาติไทย ก่อตั้งขึ้นราวปี พ.ศ. ๑๗๙๒ โดยมีพ่อขุนศรีอินทราทิตย์เป็นปฐมกษัตริย์ ยุคสมัยนี้เจริญรุ่งเรืองทั้งด้านศิลปะและวัฒนธรรม

พ่อขุนรามคำแหงมหาราชทรงประดิษฐ์อักษรไทยขึ้นเมื่อ พ.ศ. ๑๘๒๖ อักษรนี้ถือเป็นรากฐานของภาษาไทยที่ใช้สืบทอดมาจนถึงปัจจุบัน

ต่อมาราชอาณาจักรอยุธยาได้รับการสถาปนาขึ้นในปี พ.ศ. ๑๘๙๓ และมีความเจริญรุ่งเรืองยาวนานกว่า ๔๑๗ ปี`}
];

function switchTab(id,btn){
  document.querySelectorAll('.tp').forEach(p=>p.classList.remove('active'));
  document.querySelectorAll('.tb').forEach(b=>b.classList.remove('active'));
  document.getElementById('tab-'+id).classList.add('active');
  btn.classList.add('active');
}
function setStatus(t,s){document.getElementById('stxt').textContent=t;document.getElementById('sdot').className=s||'';}
function setBadge(t,c){const b=document.getElementById('lib-badge');b.textContent=t;b.className='badge '+c;}
function onLibLoaded(){setBadge('พร้อมใช้งาน','badge bg');document.getElementById('btn-start').disabled=false;setStatus('กด "เริ่มต้น" เพื่อเปิดกล้อง','');}
function onLibError(){setBadge('โหลดล้มเหลว','badge br');setStatus('โหลด library ล้มเหลว','warn');}

function processGaze(data){
  if(!data||!running)return;
  const x=data.x,y=data.y,W=window.innerWidth,H=window.innerHeight,now=Date.now();
  currentGX=x;currentGY=y;
  if(taskActive)dotSamples.push({x,y,t:now});
  if(readActive)processReadGaze(x,y,now);
  if(gazeHistory.length>0){
    const p=gazeHistory[gazeHistory.length-1],d=Math.hypot(x-p.x,y-p.y);
    if(d>FIX_PX){sacCount++;if(inFix&&fixStart)fixDurs.push(now-fixStart);inFix=false;fixStart=null;}
    else{if(!inFix){inFix=true;fixStart=now;}else if(now-fixStart>FIX_MS){fixCount++;fixStart=now;}}
  }
  gazeHistory.push({x,y,t:now});if(gazeHistory.length>600)gazeHistory.shift();
  gazeYHistory.push(Math.min(1,Math.max(0,y/(H||800))));if(gazeYHistory.length>200)gazeYHistory.shift();
  heatBins[Math.min(39,Math.max(0,Math.floor((x/(W||1280))*40)))]++;
  gazeLog.push({t:now,x:Math.round(x),y:Math.round(y),fixCount,sacCount});
  document.getElementById('s-gx').textContent=Math.round(x);
  document.getElementById('s-gy').textContent=Math.round(y);
  const xr=x/(W||1280);
  document.getElementById('s-gaze').textContent=xr<.35?'ซ้าย':xr>.65?'ขวา':'กลาง';
  document.getElementById('s-samples').textContent=gazeHistory.length;
  document.getElementById('m-fix').textContent=fixCount;
  document.getElementById('m-sac').textContent=sacCount;
  if(fixDurs.length>0)document.getElementById('m-fdur').textContent=Math.round(fixDurs.reduce((a,b)=>a+b,0)/fixDurs.length);
  if(gazeHistory.length>10){
    const xs=gazeHistory.map(g=>g.x),ys=gazeHistory.map(g=>g.y);
    const mx=xs.reduce((a,b)=>a+b,0)/xs.length,my=ys.reduce((a,b)=>a+b,0)/ys.length;
    document.getElementById('m-bcea').textContent=Math.round(xs.reduce((a,b)=>a+(b-mx)**2,0)/xs.length+ys.reduce((a,b)=>a+(b-my)**2,0)/ys.length);
  }
  if(startTime)document.getElementById('s-time').textContent=Math.floor((now-startTime)/1000)+'s';
  drawHeatmap();drawTimeline();
  setStatus('กำลังติดตาม ('+Math.round(x)+', '+Math.round(y)+')','active');
}

async function startET(){
  if(running||typeof webgazer==='undefined'){setStatus('ยังไม่พร้อม','warn');return;}
  setStatus('กำลังขอสิทธิ์กล้อง...','');
  document.getElementById('btn-start').disabled=true;
  document.getElementById('btn-stop').disabled=false;
  startTime=Date.now();
  try{
    webgazer.setGazeListener(processGaze);
    webgazer.showPredictionPoints(false);
    await webgazer.begin();
    ['webgazerVideoFeed','webgazerFaceOverlay','webgazerFaceFeedbackBox','webgazerVideoContainer'].forEach(id=>{
      const el=document.getElementById(id);
      if(el)el.style.cssText+=';position:absolute!important;top:-9999px!important;left:-9999px!important;pointer-events:none!important;';
    });
    setTimeout(()=>{
      const wv=document.getElementById('webgazerVideoFeed');
      const wo=document.getElementById('webgazerFaceOverlay');
      const ct=document.getElementById('wg-video-container');
      if(wv&&ct){
        ct.appendChild(wv);
        wv.style.cssText='position:absolute!important;top:0!important;left:0!important;width:100%!important;height:100%!important;object-fit:cover;transform:scaleX(-1);display:block;';
      }
      if(wo&&ct){
        ct.appendChild(wo);
        const sync=()=>{if(!wv)return;wo.style.cssText='position:absolute!important;top:0!important;left:0!important;width:'+wv.offsetWidth+'px!important;height:'+wv.offsetHeight+'px!important;transform:scaleX(-1)!important;display:block!important;z-index:5;pointer-events:none;';};
        sync();new ResizeObserver(sync).observe(wv);patchOverlayColors(wo);
      }
      const fb=document.getElementById('webgazerFaceFeedbackBox');if(fb)fb.style.display='none';
    },1200);
    running=true;
    document.getElementById('btn-cal').disabled=false;
    document.getElementById('btn-task').disabled=false;
    document.getElementById('btn-read').disabled=false;
    setStatus('กำลังติดตามสายตา...','active');
  }catch(e){
    setStatus('เปิดกล้องไม่ได้: '+e.message,'warn');
    document.getElementById('btn-start').disabled=false;
    document.getElementById('btn-stop').disabled=true;
  }
}
function stopET(){
  running=false;taskActive=false;readActive=false;
  if(typeof webgazer!=='undefined')webgazer.pause();
  endCalibration(false);endTask();endReading();
  setStatus('หยุดแล้ว','');
  document.getElementById('btn-start').disabled=false;
  document.getElementById('btn-stop').disabled=true;
  document.getElementById('btn-cal').disabled=true;
  document.getElementById('btn-task').disabled=true;
  document.getElementById('btn-read').disabled=true;
}
function resetStats(){
  fixCount=0;sacCount=0;gazeHistory=[];fixDurs=[];fixStart=null;inFix=false;
  gazeYHistory=[];gazeLog=[];heatBins.fill(0);startTime=running?Date.now():null;
  ['m-fix','m-sac','s-samples'].forEach(id=>document.getElementById(id).textContent='0');
  ['m-fdur','m-bcea'].forEach(id=>document.getElementById(id).textContent='—');
  ['s-gx','s-gy'].forEach(id=>document.getElementById(id).textContent='—');
  document.getElementById('s-gaze').textContent='—';
  document.getElementById('s-time').textContent='0s';
  drawHeatmap();drawTimeline();
}

/* CALIBRATION */
const CAL_POS=[[.1,.1],[.5,.1],[.9,.1],[.1,.5],[.5,.5],[.9,.5],[.1,.9],[.5,.9],[.9,.9]];
let calIdx=0;
function startCalibration(){
  if(!running){alert('กรุณาเปิดติดตามสายตาก่อน');return;}
  calIdx=0;
  const ov=document.getElementById('cal-overlay');
  ov.querySelectorAll('.cal-pt').forEach(e=>e.remove());
  document.getElementById('cal-cur').textContent='0';
  const W=window.innerWidth,H=window.innerHeight;
  CAL_POS.forEach(([rx,ry],i)=>{
    const d=document.createElement('div');
    d.className='cal-pt waiting';d.style.left=Math.round(rx*W)+'px';d.style.top=Math.round(ry*H)+'px';d.dataset.idx=i;
    d.onclick=()=>clickCalDot(i,d,rx*W,ry*H);ov.appendChild(d);
  });
  activateCalDot(0);ov.classList.add('active');
}
function activateCalDot(i){document.querySelectorAll('.cal-pt').forEach(d=>{d.classList.remove('active-pt');if(parseInt(d.dataset.idx)===i)d.classList.add('active-pt');});}
function clickCalDot(i,dot,x,y){
  if(i!==calIdx)return;
  for(let c=0;c<5;c++){if(typeof webgazer!=='undefined')webgazer.recordScreenPosition(x,y,'click');}
  dot.classList.remove('active-pt');dot.classList.add('done');
  calIdx++;document.getElementById('cal-cur').textContent=calIdx;
  if(calIdx<9)activateCalDot(calIdx);
  else setTimeout(()=>endCalibration(true),400);
}
function endCalibration(ok){
  document.getElementById('cal-overlay').classList.remove('active');
  if(ok){calibrated=true;document.getElementById('cal-badge').style.display='inline-block';setStatus('ปรับเทียบเสร็จแล้ว ✓','active');}
}

/* STIMULUS TASK */
function genDots(pat,n){
  const W=window.innerWidth,H=window.innerHeight,m=80,dots=[];
  if(pat==='grid'){
    const cols=Math.ceil(Math.sqrt(n*(W/H))),rows=Math.ceil(n/cols);
    const gw=(W-m*2)/(cols-1||1),gh=(H-m*2)/(rows-1||1);
    for(let r=0;r<rows&&dots.length<n;r++)for(let c=0;c<cols&&dots.length<n;c++)dots.push({x:m+c*gw,y:m+r*gh});
  }else if(pat==='center-out'){
    dots.push({x:W/2,y:H/2});
    for(let i=0;i<n-1;i++){const a=(i/(n-1))*Math.PI*2-Math.PI/2,r=Math.min(W,H)*.38;dots.push({x:W/2+Math.cos(a)*r,y:H/2+Math.sin(a)*r});}
  }else{
    for(let i=0;i<n;i++)dots.push({x:m+Math.random()*(W-m*2),y:m+Math.random()*(H-m*2)});
  }
  return dots;
}
function startTask(){
  if(!running){alert('กรุณาเปิดติดตามสายตาก่อน');return;}
  taskDur=parseFloat(document.getElementById('cfg-dur').value)*1000;
  taskDots=genDots(document.getElementById('cfg-pat').value,parseInt(document.getElementById('cfg-dots').value));
  taskResults=[];taskIdx=0;taskActive=true;dotSamples=[];
  document.getElementById('sc-tot').textContent=taskDots.length;
  document.getElementById('stim-res').textContent='';
  document.getElementById('stim-overlay').classList.add('active');
  showNextDot();
}
function showNextDot(){
  if(taskIdx>=taskDots.length){endTask();return;}
  dotSamples=[];
  const d=taskDots[taskIdx],el=document.getElementById('stim-dot');
  el.style.left=d.x+'px';el.style.top=d.y+'px';el.classList.remove('flash');
  document.getElementById('sc-cur').textContent=taskIdx+1;
  const pr=document.getElementById('stim-prog');
  pr.style.transition='none';pr.style.width='0%';
  requestAnimationFrame(()=>requestAnimationFrame(()=>{pr.style.transition='width '+taskDur+'ms linear';pr.style.width='100%';}));
  taskTimer=setTimeout(()=>{recordDot(d);el.classList.add('flash');setTimeout(()=>{taskIdx++;showNextDot();},200);},taskDur);
}
function recordDot(dot){
  const thr=parseInt(document.getElementById('cfg-thr').value);
  let gx=null,gy=null;
  if(dotSamples.length>0){const h=Math.max(1,Math.floor(dotSamples.length/2)),r=dotSamples.slice(-h);gx=r.reduce((a,s)=>a+s.x,0)/r.length;gy=r.reduce((a,s)=>a+s.y,0)/r.length;}
  else if(currentGX!==null){gx=currentGX;gy=currentGY;}
  const err=gx!==null?Math.round(Math.hypot(gx-dot.x,gy-dot.y)):null;
  const hit=err!==null&&err<=thr;
  taskResults.push({idx:taskIdx+1,tx:Math.round(dot.x),ty:Math.round(dot.y),gx:gx!==null?Math.round(gx):null,gy:gy!==null?Math.round(gy):null,err,hit});
  const hc=taskResults.filter(r=>r.hit).length;
  document.getElementById('stim-res').textContent='ผ่าน: '+hc+'/'+taskResults.length+' — คลาด: '+(err!==null?err+'px':'N/A');
}
function endTask(){
  taskActive=false;clearTimeout(taskTimer);
  document.getElementById('stim-overlay').classList.remove('active');
  document.getElementById('stim-prog').style.width='0%';
  if(taskResults.length>0)renderResults();
}

/* READING TASK */
function buildPassage(passage){
  const c=document.getElementById('read-passage');c.innerHTML='';readLines=[];
  const fs=parseInt(document.getElementById('cfg-fontsize').value);c.style.fontSize=fs+'px';
  passage.text.split('\n\n').forEach((para,pi,arr)=>{
    para.split('\n').forEach(line=>{
      if(!line.trim())return;
      const div=document.createElement('div');div.className='read-line';div.textContent=line;div.dataset.lineIdx=readLines.length;
      c.appendChild(div);readLines.push({el:div,fixations:0,regressions:0,gazed:false});
    });
    if(pi<arr.length-1){const br=document.createElement('div');br.style.height='1em';c.appendChild(br);}
  });
}
function startReading(){
  if(!running){alert('กรุณาเปิดติดตามสายตาก่อน');return;}
  readPassageIdx=parseInt(document.getElementById('cfg-passage').value);
  readDur=parseInt(document.getElementById('cfg-readtime').value)*1000;
  readGazeLog=[];prevGX=null;readStartTime=Date.now();
  buildPassage(PASSAGES[readPassageIdx]);
  document.getElementById('rp-cur').textContent=readPassageIdx+1;
  document.getElementById('rp-tot').textContent=PASSAGES.length;
  document.getElementById('rprog-bar').style.width='0%';
  document.getElementById('rtimer').textContent='0s';
  readActive=true;
  document.getElementById('read-overlay').classList.add('active');
  document.getElementById('read-gaze-dot').style.display='block';
  readTimer=setInterval(()=>{
    const el=Date.now()-readStartTime;
    document.getElementById('rprog-bar').style.width=Math.min(100,(el/readDur)*100)+'%';
    document.getElementById('rtimer').textContent=Math.floor(el/1000)+'s';
    if(el>=readDur)endReading();
  },200);
}
function processReadGaze(x,y,now){
  document.getElementById('read-gaze-dot').style.left=x+'px';
  document.getElementById('read-gaze-dot').style.top=y+'px';
  readGazeLog.push({t:now,x:Math.round(x),y:Math.round(y)});
  const el=document.elementFromPoint(x,y);
  if(el&&el.classList.contains('read-line')){
    const li=parseInt(el.dataset.lineIdx);
    if(!isNaN(li)&&readLines[li]){
      const line=readLines[li];
      if(!line.gazed){line.gazed=true;el.classList.add('gazed');}
      line.fixations++;
      if(prevGX!==null&&x<prevGX-40)line.regressions++;
    }
  }
  prevGX=x;
}
function endReading(){
  readActive=false;clearInterval(readTimer);
  document.getElementById('read-overlay').classList.remove('active');
  document.getElementById('read-gaze-dot').style.display='none';
  if(readGazeLog.length>0)renderReadingResults();
}

/* ── วิเคราะห์ระดับสมาธิ ── */
function analyzeAttention(stimData, readData){
  let score=0, factors=[], maxScore=0;

  if(stimData){
    const {hitPct, avgErr, maxErr} = stimData;
    const hitScore = Math.round(hitPct * 0.35);
    const errScore = avgErr !== '—' ? Math.round(Math.max(0, 35 - (avgErr/10))) : 0;
    score += hitScore + errScore;
    maxScore += 70;
    factors.push('ความแม่นยำสายตา: '+(hitPct)+'% โดน ('+(hitPct>=70?'ดี':'ต้องปรับปรุง')+')');
    if(avgErr!=='—')factors.push('ความคลาดเคลื่อนเฉลี่ย: '+avgErr+'px ('+(avgErr<150?'ดี':'ค่อนข้างสูง')+')');
  }
  if(readData){
    const {wpm, reg, cov} = readData;
    const wpmScore = Math.round(Math.min(30, wpm / 4));
    const regScore = Math.round(Math.max(0, 15 - reg * 1.5));
    const covScore = Math.round(cov * 0.05);
    score += wpmScore + regScore + covScore;
    maxScore += 50;
    factors.push('ความเร็วอ่าน: '+wpm+' คำ/นาที ('+(wpm>=80?'ดี':'ช้ากว่าปกติ')+')');
    factors.push('การอ่านซ้ำ: '+reg+' ครั้ง ('+(reg<=5?'ปกติ':'บ่งชี้วอกแวก')+')');
    factors.push('ครอบคลุมบรรทัด: '+cov+'% ('+(cov>=80?'ดี':'ข้ามบรรทัดบ่อย')+')');
  }
  if(maxScore===0)return;

  const pct = Math.round((score/maxScore)*100);
  const scoreEl = document.getElementById('attn-score');
  const barEl = document.getElementById('attn-bar');
  const badgeEl = document.getElementById('attn-level-badge');
  const detailEl = document.getElementById('attn-detail');

  scoreEl.textContent = pct;
  barEl.style.width = pct+'%';

  let level, cls, color;
  if(pct>=70){level='สมาธิดี';cls='attn-high';color='#157a50';}
  else if(pct>=45){level='สมาธิปานกลาง';cls='attn-med';color='#7a5200';}
  else{level='สมาธิต่ำ — ควรตรวจสอบเพิ่มเติม';cls='attn-low';color='#932010';}

  badgeEl.textContent=level;
  badgeEl.className='attn-badge '+cls;
  scoreEl.style.color=color;
  barEl.style.background=color;
  detailEl.innerHTML='<strong>ปัจจัยที่วิเคราะห์:</strong> '+factors.join(' &nbsp;·&nbsp; ')+'<br><span style="font-size:10px;color:var(--tx3)">* ผลนี้เป็นการคัดกรองเบื้องต้น ไม่ใช่การวินิจฉัยทางการแพทย์</span>';
}

/* RENDER RESULTS */
function renderResults(){
  document.getElementById('no-results').style.display='none';
  document.getElementById('has-results').style.display='block';
  document.getElementById('no-table').style.display='none';
  document.getElementById('has-table').style.display='flex';
  const res=taskResults;if(!res.length)return;
  const valid=res.filter(r=>r.err!==null);
  const avg=valid.length?Math.round(valid.reduce((a,r)=>a+r.err,0)/valid.length):'—';
  const max=valid.length?Math.max(...valid.map(r=>r.err)):'—';
  const hitPct=Math.round((res.filter(r=>r.hit).length/res.length)*100);
  document.getElementById('r-avg').textContent=avg+(avg!=='—'?'px':'');
  document.getElementById('r-hit').textContent=hitPct+'%';
  document.getElementById('r-max').textContent=max+(max!=='—'?'px':'');
  analyzeAttention({hitPct,avgErr:avg,maxErr:max},null);
  const mc=document.getElementById('accuracy-map');
  mc.width=mc.offsetWidth||400;mc.height=180;
  const sx=mc.width/window.innerWidth,sy=mc.height/window.innerHeight;
  const ctx=mc.getContext('2d');ctx.clearRect(0,0,mc.width,mc.height);
  const thr=parseInt(document.getElementById('cfg-thr').value);
  res.forEach(r=>{
    const tx=r.tx*sx,ty=r.ty*sy;
    ctx.beginPath();ctx.arc(tx,ty,thr*Math.min(sx,sy),0,Math.PI*2);ctx.strokeStyle='rgba(78,156,255,.15)';ctx.lineWidth=1;ctx.stroke();
    if(r.gx!==null){
      const gx=r.gx*sx,gy=r.gy*sy;
      ctx.beginPath();ctx.moveTo(tx,ty);ctx.lineTo(gx,gy);ctx.strokeStyle=r.hit?'rgba(78,220,120,.6)':'rgba(240,112,80,.6)';ctx.lineWidth=1.5;ctx.stroke();
      ctx.beginPath();ctx.arc(gx,gy,4,0,Math.PI*2);ctx.fillStyle=r.hit?'rgba(78,220,120,.9)':'rgba(240,112,80,.9)';ctx.fill();
    }
    ctx.beginPath();ctx.arc(tx,ty,7,0,Math.PI*2);ctx.fillStyle='#fff';ctx.fill();
    ctx.fillStyle='#050a18';ctx.font='8px monospace';ctx.textAlign='center';ctx.textBaseline='middle';ctx.fillText(r.idx,tx,ty);
  });
  const tbody=document.getElementById('acc-tbody');tbody.innerHTML='';
  res.forEach(r=>{
    const tr=document.createElement('tr');
    tr.innerHTML=`<td>${r.idx}</td><td class="${r.hit?'hit':'miss'}">${r.err!==null?r.err+'px':'—'}</td><td class="${r.hit?'hit':'miss'}">${r.hit?'โดน':'พลาด'}</td>`;
    tbody.appendChild(tr);
  });
  switchTab('results',document.querySelectorAll('.tb')[3]);
}
function renderReadingResults(){
  document.getElementById('no-results').style.display='none';
  document.getElementById('has-results').style.display='block';
  document.getElementById('read-res-sec').style.display='block';
  const p=PASSAGES[readPassageIdx];
  const elapsed=(Date.now()-readStartTime)/1000/60;
  const wpm=Math.round(p.words/Math.max(elapsed,.1));
  const totalReg=readLines.reduce((a,l)=>a+l.regressions,0);
  const cov=readLines.length?Math.round((readLines.filter(l=>l.gazed).length/readLines.length)*100):0;
  document.getElementById('r-wpm').textContent=wpm;
  document.getElementById('r-reg').textContent=totalReg;
  document.getElementById('r-cov').textContent=cov+'%';
  analyzeAttention(null,{wpm,reg:totalReg,cov});
  const hc=document.getElementById('read-heatmap');hc.width=hc.offsetWidth||400;hc.height=60;
  const hctx=hc.getContext('2d');hctx.clearRect(0,0,hc.width,hc.height);
  const rb=new Array(40).fill(0);
  readGazeLog.forEach(g=>rb[Math.min(39,Math.max(0,Math.floor((g.x/window.innerWidth)*40)))]++);
  const rm=Math.max(...rb,1);
  for(let i=0;i<40;i++){const h=(rb[i]/rm)*52,a=(0.2+(rb[i]/rm)*.72).toFixed(2);hctx.fillStyle=`rgba(78,156,255,${a})`;hctx.beginPath();if(hctx.roundRect)hctx.roundRect(i*(hc.width/40),60-h,hc.width/40-1,h,2);else hctx.rect(i*(hc.width/40),60-h,hc.width/40-1,h);hctx.fill();}
  const lc=document.getElementById('line-chart');lc.width=lc.offsetWidth||400;lc.height=100;
  const lctx=lc.getContext('2d');lctx.clearRect(0,0,lc.width,lc.height);
  const mf=Math.max(...readLines.map(l=>l.fixations),1),bw=lc.width/Math.max(readLines.length,1);
  readLines.forEach((line,i)=>{
    const h=(line.fixations/mf)*85;
    lctx.fillStyle=line.regressions>0?'rgba(240,112,80,.7)':'rgba(78,156,255,.65)';
    lctx.beginPath();if(lctx.roundRect)lctx.roundRect(i*bw+1,90-h,bw-2,h,2);else lctx.rect(i*bw+1,90-h,bw-2,h);lctx.fill();
    if(i%3===0){lctx.fillStyle='rgba(60,90,160,.5)';lctx.font='8px monospace';lctx.textAlign='center';lctx.fillText(i+1,i*bw+bw/2,98);}
  });
  switchTab('results',document.querySelectorAll('.tb')[3]);
}

function drawHeatmap(){
  const c=document.getElementById('heatmap'),ctx=c.getContext('2d');
  c.width=c.offsetWidth||400;c.height=58;ctx.clearRect(0,0,c.width,c.height);
  const bw=c.width/40,max=Math.max(...heatBins,1);
  for(let i=0;i<40;i++){const h=(heatBins[i]/max)*50,a=(0.18+(heatBins[i]/max)*.72).toFixed(2);ctx.fillStyle=`rgba(26,111,196,${a})`;ctx.beginPath();if(ctx.roundRect)ctx.roundRect(i*bw,58-h,bw-1,h,2);else ctx.rect(i*bw,58-h,bw-1,h);ctx.fill();}
}
function drawTimeline(){
  const c=document.getElementById('timeline'),ctx=c.getContext('2d');
  c.width=c.offsetWidth||400;c.height=c.offsetHeight||50;ctx.clearRect(0,0,c.width,c.height);
  if(gazeYHistory.length<2)return;
  ctx.beginPath();
  gazeYHistory.forEach((v,i)=>{const x=(i/(gazeYHistory.length-1))*c.width,y=v*c.height;i===0?ctx.moveTo(x,y):ctx.lineTo(x,y);});
  ctx.strokeStyle='rgba(26,111,196,.7)';ctx.lineWidth=1.5;ctx.stroke();
}
function patchOverlayColors(canvas){
  if(!canvas)return;const ctx=canvas.getContext('2d');if(!ctx||ctx.__patched)return;ctx.__patched=true;
  Object.defineProperty(ctx,'fillStyle',{set(v){this.__fs=(typeof v==='string'&&(v.includes('255,0,0')||v.toLowerCase()==='red'||v.includes('rgb(255')))?'rgba(78,220,154,.95)':v;},get(){return this.__fs||'rgba(78,220,154,.95)';}});
  Object.defineProperty(ctx,'strokeStyle',{set(v){this.__ss=(typeof v==='string'&&(v.includes('255,0,0')||v.toLowerCase()==='red'||v.includes('rgb(255')))?'rgba(78,220,154,.85)':v;},get(){return this.__ss||'rgba(78,220,154,.85)';}});
}
function exportCSV(type){
  let content,filename;
  if(type==='task'){if(!taskResults.length){alert('ยังไม่มีผล');return;}content='trial,target_x,target_y,gaze_x,gaze_y,error_px,hit\n'+taskResults.map(r=>`${r.idx},${r.tx},${r.ty},${r.gx??''},${r.gy??''},${r.err??''},${r.hit?1:0}`).join('\n');filename='stimulus_results.csv';}
  else if(type==='reading'){if(!readGazeLog.length){alert('ยังไม่มีผล');return;}content='timestamp,gaze_x,gaze_y\n'+readGazeLog.map(r=>`${r.t},${r.x},${r.y}`).join('\n');filename='reading_gaze.csv';}
  else{if(!gazeLog.length){alert('ยังไม่มีข้อมูล');return;}content='timestamp,gaze_x,gaze_y,fix_count,sac_count\n'+gazeLog.map(r=>`${r.t},${r.x},${r.y},${r.fixCount},${r.sacCount}`).join('\n');filename='gaze_data.csv';}
  const blob=new Blob([content],{type:'text/csv'}),url=URL.createObjectURL(blob),a=document.createElement('a');
  a.href=url;a.download=filename;a.click();URL.revokeObjectURL(url);
}
document.addEventListener('keydown',e=>{
  if(e.key==='Escape'){
    if(document.getElementById('cal-overlay').classList.contains('active'))endCalibration(false);
    else if(taskActive)endTask();
    else if(readActive)endReading();
  }
});
drawHeatmap();drawTimeline();
(function(){const s=document.createElement('script');s.src='https://unpkg.com/webgazer@2.1.0/dist/webgazer.min.js';s.onload=onLibLoaded;s.onerror=onLibError;document.body.appendChild(s);})();
</script>
</body>
</html>
