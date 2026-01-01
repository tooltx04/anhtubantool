<html lang="vi">
<head>
<meta charset="UTF-8">
<title>anhtubantool</title>

<style>
*{box-sizing:border-box;margin:0;padding:0}
body{
    min-height:100vh;
    font-family:Arial;
    background:linear-gradient(270deg,red,orange,yellow,green,cyan,blue,violet);
    background-size:1600% 1600%;
    animation:rainbow 15s ease infinite;
    display:flex;
    justify-content:center;
    align-items:center;
    color:#fff;
}
@keyframes rainbow{
    0%{background-position:0% 50%}
    50%{background-position:100% 50%}
    100%{background-position:0% 50%}
}
.main{
    width:540px;
    background:rgba(0,0,0,.7);
    border-radius:22px;
    padding:24px;
}
h1{
    text-align:center;
    font-size:34px;
    margin-bottom:16px;
    text-shadow:-2px -2px 0 #fff,2px -2px 0 #fff,-2px 2px 0 #fff,2px 2px 0 #fff,0 0 14px gold;
}
.tabs{display:flex;gap:10px;margin-bottom:16px}
.tab{
    flex:1;
    padding:12px;
    text-align:center;
    border-radius:14px;
    border:2px solid #fff;
    cursor:pointer;
    font-weight:bold;
}
.tab.active{background:rgba(255,255,255,.25)}
.content{display:none}
.content.active{display:block}
.box{
    background:#020617;
    border-radius:16px;
    padding:16px;
}
input,button{
    width:100%;
    padding:12px;
    border-radius:8px;
    border:none;
    margin-top:8px;
}
button{background:#2563eb;color:#fff;font-weight:bold;cursor:pointer}
button.reset{background:#b91c1c;margin-top:6px}
.row{
    display:flex;
    justify-content:space-between;
    padding:6px 0;
    border-bottom:1px solid rgba(255,255,255,.15);
}
.row:last-child{border-bottom:none}
.history{
    margin-top:12px;
    background:#020617;
    padding:10px;
    border-radius:10px;
    font-size:13px;
}
.history-item{
    border-bottom:1px dashed #334155;
    padding:4px 0;
}
</style>
</head>

<body>
<div class="main">

<h1>anhtubantool</h1>

<div class="tabs">
    <div class="tab active" onclick="openTab('sicbo',this)">🎲 SICBO</div>
    <div class="tab" onclick="openTab('sunwin',this)">🔥 SUNWIN</div>
</div>

<!-- ===== SICBO ===== -->
<div id="sicbo" class="content active">
<div class="box">
<h3>🎲 SICBO AI</h3>
<input id="md5Input" placeholder="Nhập MD5 32 ký tự">
<button onclick="analyzeSicbo()">PHÂN TÍCH</button>
<button class="reset" onclick="resetSicbo()">RESET LỊCH SỬ</button>
<div id="sicboOut"></div>
<div class="history">
<b>📜 Lịch sử SICBO</b>
<div id="sicboHistory"></div>
</div>
</div>
</div>

<!-- ===== SUNWIN ===== -->
<div id="sunwin" class="content">
<div class="box">
<h3>🔥 SUNWIN AI</h3>
<p style="text-align:center">⏳ <span id="countdown">60</span>s</p>
<div class="row"><span>Phiên</span><span id="phien">--</span></div>
<div class="row"><span>Xúc xắc</span><span id="xx">--</span></div>
<div class="row"><span>Tổng</span><span id="tong">--</span></div>
<div class="row"><span>Kết quả</span><span id="ketqua">--</span></div>
<div class="row"><span>Dự đoán</span><span id="dudoan">--</span></div>
<button class="reset" onclick="resetSun()">RESET LỊCH SỬ</button>
<div class="history">
<b>📜 Lịch sử SUNWIN</b>
<div id="sunHistory"></div>
</div>
</div>
</div>

</div>

<script>
function openTab(id,e){
    document.querySelectorAll('.tab').forEach(t=>t.classList.remove('active'));
    document.querySelectorAll('.content').forEach(c=>c.classList.remove('active'));
    e.classList.add('active');
    document.getElementById(id).classList.add('active');
}

/* ===== SICBO ===== */
function analyzeSicbo(){
    const md5=md5Input.value.trim().toLowerCase();
    if(!/^[0-9a-f]{32}$/.test(md5)){sicboOut.innerHTML="❌ MD5 lỗi";return;}
    let s=parseInt(md5.slice(0,16),16);
    let r=()=>1+Math.floor((Math.sin(s++)*10000%1)*6);
    let d1=r(),d2=r(),d3=r();
    let total=d1+d2+d3;
    sicboOut.innerHTML=`🎲 ${d1}-${d2}-${d3} | Tổng: <b>${total}</b>`;
    let h=JSON.parse(localStorage.getItem("sicbo_h")||"[]");
    h.unshift(`🎲 ${d1}-${d2}-${d3} | ${total}`);
    if(h.length>10)h.pop();
    localStorage.setItem("sicbo_h",JSON.stringify(h));
    renderSicbo();
}
function renderSicbo(){
    sicboHistory.innerHTML=(JSON.parse(localStorage.getItem("sicbo_h")||"[]"))
        .map(i=>`<div class="history-item">${i}</div>`).join("");
}
function resetSicbo(){
    localStorage.removeItem("sicbo_h");
    renderSicbo();
}
renderSicbo();

/* ===== SUNWIN ===== */
let cd=60;
async function loadSun(){
    try{
        let r=await fetch("https://sunwinsaygex-tzz9.onrender.com/api/sun");
        let d=await r.json();
        phien.textContent=d.phien;
        xx.textContent=`${d.xuc_xac_1}-${d.xuc_xac_2}-${d.xuc_xac_3}`;
        tong.textContent=d.tong;
        ketqua.textContent=d.ket_qua;
        dudoan.textContent=d.du_doan;

        let h=JSON.parse(localStorage.getItem("sun_h")||"[]");
        h.unshift(`Phiên ${d.phien} | Tổng ${d.tong} | ${d.ket_qua} | ${d.du_doan}`);
        if(h.length>10)h.pop();
        localStorage.setItem("sun_h",JSON.stringify(h));
        renderSun();
    }catch{}
}
function renderSun(){
    sunHistory.innerHTML=(JSON.parse(localStorage.getItem("sun_h")||"[]"))
        .map(i=>`<div class="history-item">${i}</div>`).join("");
}
function resetSun(){
    localStorage.removeItem("sun_h");
    renderSun();
}
setInterval(()=>{countdown.textContent=cd--;if(cd<0){cd=60;loadSun()}},1000);
loadSun();renderSun();
</script>
</body>
</html>
