<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>J.A.R.V.I.S.</title>
<style>
  :root{
    --c:#00e5ff; --c2:#0a7a8c; --bg:#02080d; --panel:rgba(0,229,255,.06); --line:rgba(0,229,255,.35);
    --txt:#bff6ff; --dim:#5b8a94;
  }
  *{box-sizing:border-box;margin:0;padding:0}
  html,body{height:100%;background:var(--bg);color:var(--txt);font-family:"Segoe UI",Consolas,monospace;overflow:hidden}
  body::before{content:"";position:fixed;inset:0;pointer-events:none;z-index:0;
    background-image:linear-gradient(rgba(0,229,255,.05) 1px,transparent 1px),linear-gradient(90deg,rgba(0,229,255,.05) 1px,transparent 1px);
    background-size:44px 44px;animation:grid 12s linear infinite}
  @keyframes grid{to{background-position:44px 44px}}
  body::after{content:"";position:fixed;inset:0;pointer-events:none;z-index:50;
    background:repeating-linear-gradient(0deg,rgba(0,0,0,.18) 0 1px,transparent 1px 3px);mix-blend-mode:multiply}
  .scan{position:fixed;left:0;right:0;height:120px;top:-120px;pointer-events:none;z-index:2;
    background:linear-gradient(transparent,rgba(0,229,255,.08),transparent);animation:scan 6s linear infinite}
  @keyframes scan{to{top:100%}}

  #app{position:relative;z-index:3;height:100%;display:grid;
    grid-template-columns:260px 1fr 330px;grid-template-rows:56px 1fr 74px;gap:12px;padding:12px}
  header{grid-column:1/4;display:flex;align-items:center;justify-content:space-between;
    border:1px solid var(--line);background:var(--panel);padding:0 18px;clip-path:polygon(0 0,calc(100% - 18px) 0,100% 18px,100% 100%,18px 100%,0 calc(100% - 18px))}
  .logo{font-size:20px;letter-spacing:10px;color:var(--c);text-shadow:0 0 12px var(--c);font-weight:700}
  .hdr-r{display:flex;gap:16px;align-items:center;font-size:12px;color:var(--dim)}
  #clock{color:var(--c);font-size:16px;letter-spacing:2px}
  .panel{border:1px solid var(--line);background:var(--panel);padding:12px;position:relative;overflow:hidden;
    clip-path:polygon(0 0,calc(100% - 14px) 0,100% 14px,100% 100%,14px 100%,0 calc(100% - 14px))}
  .panel h3{font-size:11px;letter-spacing:4px;color:var(--c);margin-bottom:10px;display:flex;align-items:center;gap:8px}
  .panel h3::after{content:"";flex:1;height:1px;background:linear-gradient(90deg,var(--line),transparent)}
  .left{display:flex;flex-direction:column;gap:12px;overflow:auto}
  .mode{width:100%;text-align:left;background:transparent;border:1px solid var(--line);color:var(--txt);
    padding:9px 10px;margin-bottom:6px;cursor:pointer;font-size:13px;transition:.2s;position:relative}
  .mode small{display:block;color:var(--dim);font-size:10px;margin-top:2px}
  .mode:hover{background:rgba(0,229,255,.12);transform:translateX(4px)}
  .mode.on{background:rgba(0,229,255,.2);box-shadow:0 0 14px rgba(0,229,255,.4) inset;border-color:var(--c)}
  .mode.on::before{content:"▶";position:absolute;right:10px;top:10px;color:var(--c);font-size:10px;animation:blink 1s infinite}
  @keyframes blink{50%{opacity:.2}}
  .row{display:flex;justify-content:space-between;font-size:12px;padding:5px 0;border-bottom:1px dashed rgba(0,229,255,.15)}
  .row b{color:var(--c);font-weight:600}
  .dot{display:inline-block;width:8px;height:8px;border-radius:50%;background:#555;margin-right:6px}
  .dot.ok{background:#4dff9d;box-shadow:0 0 8px #4dff9d}.dot.bad{background:#ff4d6d;box-shadow:0 0 8px #ff4d6d}
  .meter{height:6px;background:rgba(0,229,255,.12);margin-top:8px;overflow:hidden}
  .meter i{display:block;height:100%;width:30%;background:var(--c);box-shadow:0 0 8px var(--c);transition:width .15s}

  .center{display:flex;flex-direction:column;align-items:center;justify-content:center;position:relative;min-height:0}
  #reactor{width:100%;height:100%;max-height:100%;display:block}
  #stateLabel{position:absolute;bottom:14px;font-size:13px;letter-spacing:8px;color:var(--c);text-shadow:0 0 10px var(--c)}
  #subtitle{position:absolute;top:10px;max-width:80%;text-align:center;font-size:15px;color:var(--txt);min-height:22px;text-shadow:0 0 8px rgba(0,229,255,.6)}

  .right{display:flex;flex-direction:column;gap:12px;min-height:0}
  .right .panel{display:flex;flex-direction:column;min-height:0}
  #chat{flex:1.4}#acts{flex:1}
  .log{flex:1;overflow:auto;font-size:13px;line-height:1.45;padding-right:4px}
  .log::-webkit-scrollbar{width:4px}.log::-webkit-scrollbar-thumb{background:var(--line)}
  .msg{margin-bottom:9px;animation:in .3s}
  @keyframes in{from{opacity:0;transform:translateY(6px)}}
  .msg .who{font-size:10px;letter-spacing:3px;color:var(--dim)}
  .msg.u .who{color:#8affc1}.msg.j .who{color:var(--c)}
  .msg.j .t{color:var(--txt)}.msg.u .t{color:#d8ffe9}
  .act{font-size:12px;border-left:2px solid var(--c);padding:3px 8px;margin-bottom:6px;background:rgba(0,229,255,.05);white-space:pre-wrap;word-break:break-word;animation:in .3s}
  .act.err{border-color:#ff4d6d;color:#ffb3c0}.act.okk{border-color:#4dff9d}

  footer{grid-column:1/4;display:flex;gap:10px;align-items:center;border:1px solid var(--line);background:var(--panel);padding:0 12px;
    clip-path:polygon(18px 0,100% 0,100% calc(100% - 18px),calc(100% - 18px) 100%,0 100%,0 18px)}
  #inp{flex:1;background:rgba(0,0,0,.4);border:1px solid var(--line);color:var(--txt);padding:12px;font-size:15px;outline:none;font-family:inherit}
  #inp:focus{box-shadow:0 0 12px rgba(0,229,255,.4)}
  .btn{background:transparent;border:1px solid var(--c);color:var(--c);padding:11px 16px;cursor:pointer;letter-spacing:2px;font-size:12px;transition:.2s}
  .btn:hover{background:var(--c);color:#001a20;box-shadow:0 0 16px var(--c)}
  #mic{width:52px;height:52px;border-radius:50%;padding:0;font-size:20px;position:relative}
  #mic.on{background:#4dff9d;border-color:#4dff9d;color:#00251a;animation:pulse 1.2s infinite}
  @keyframes pulse{0%{box-shadow:0 0 0 0 rgba(77,255,157,.7)}100%{box-shadow:0 0 0 22px rgba(77,255,157,0)}}
  label.chk{font-size:11px;color:var(--dim);display:flex;align-items:center;gap:6px;cursor:pointer;white-space:nowrap}

  #power{position:absolute;bottom:42px;left:50%;transform:translateX(-50%);width:64px;height:64px;border-radius:50%;
    border:3px solid #ff3b4d;color:#ff3b4d;background:radial-gradient(circle,rgba(255,59,77,.28),rgba(0,0,0,.65));
    cursor:pointer;display:flex;align-items:center;justify-content:center;z-index:6;
    box-shadow:0 0 20px rgba(255,59,77,.75),inset 0 0 14px rgba(255,59,77,.4);transition:.35s;animation:offpulse 2s infinite}
  #power svg{width:30px;height:30px;stroke:currentColor;fill:none;stroke-width:2.6;stroke-linecap:round;filter:drop-shadow(0 0 6px currentColor)}
  #power:hover{transform:translateX(-50%) scale(1.1)}
  #power.on{border-color:#4dff9d;color:#4dff9d;background:radial-gradient(circle,rgba(77,255,157,.3),rgba(0,0,0,.65));
    box-shadow:0 0 24px rgba(77,255,157,.8),inset 0 0 14px rgba(77,255,157,.45);animation:onpulse 2.4s infinite}
  @keyframes offpulse{50%{box-shadow:0 0 6px rgba(255,59,77,.4),inset 0 0 8px rgba(255,59,77,.2)}}
  @keyframes onpulse{50%{box-shadow:0 0 38px rgba(77,255,157,.95),inset 0 0 18px rgba(77,255,157,.6)}}
  #powerTxt{position:absolute;bottom:112px;left:50%;transform:translateX(-50%);font-size:10px;letter-spacing:4px;color:var(--dim);z-index:6;white-space:nowrap}

  #boot{position:fixed;inset:0;background:var(--bg);z-index:100;display:flex;flex-direction:column;align-items:center;justify-content:center;gap:14px;transition:opacity .8s}
  #boot pre{color:var(--c);font-size:13px;min-height:150px;text-shadow:0 0 8px var(--c)}
  #boot .bar{width:320px;height:4px;background:rgba(0,229,255,.15)}#boot .bar i{display:block;height:100%;width:0;background:var(--c);box-shadow:0 0 10px var(--c)}
  #boot .big{font-size:30px;letter-spacing:16px;color:var(--c);text-shadow:0 0 20px var(--c)}

  #modal{position:fixed;inset:0;background:rgba(0,0,0,.7);z-index:90;display:none;align-items:center;justify-content:center}
  #modal.show{display:flex}
  .card{width:min(520px,92vw);max-height:90vh;overflow:auto;background:#031017;border:1px solid var(--c);padding:20px;box-shadow:0 0 30px rgba(0,229,255,.3)}
  .card h2{letter-spacing:6px;font-size:15px;color:var(--c);margin-bottom:14px}
  .card label{display:block;font-size:11px;letter-spacing:2px;color:var(--dim);margin:12px 0 4px}
  .card input,.card select{width:100%;background:rgba(0,0,0,.5);border:1px solid var(--line);color:var(--txt);padding:9px;font-size:14px;font-family:inherit}
  .card .hint{font-size:11px;color:var(--dim);margin-top:4px}
  .card .btns{display:flex;gap:8px;justify-content:flex-end;margin-top:18px}

  #confirm{position:fixed;inset:0;background:rgba(0,0,0,.75);z-index:95;display:none;align-items:center;justify-content:center}
  #confirm.show{display:flex}
  #confirm pre{background:#000;border:1px solid var(--line);padding:10px;max-height:220px;overflow:auto;font-size:12px;white-space:pre-wrap;color:#ffd78a}

  @media(max-width:900px){
    html,body{overflow:auto}
    #app{height:auto;grid-template-columns:1fr;grid-template-rows:auto}
    header,footer{grid-column:1}.center{height:340px}.right .panel{height:260px}
    footer{flex-wrap:wrap;padding:10px}
  }
</style>
</head>
<body>
<div class="scan"></div>

<div id="boot">
  <div class="big">J.A.R.V.I.S.</div>
  <pre id="bootlog"></pre>
  <div class="bar"><i id="bootbar"></i></div>
</div>

<div id="app">
  <header>
    <div class="logo">J.A.R.V.I.S.</div>
    <div class="hdr-r">
      <span><span class="dot" id="dotNet"></span><span id="netTxt">PC desconectado</span></span>
      <span id="clock">--:--:--</span>
      <button class="btn" id="btnCfg" style="padding:6px 12px">⚙ CONFIG</button>
    </div>
  </header>

  <aside class="left">
    <div class="panel">
      <h3>MODOS</h3>
      <button class="mode on" data-mode="conversa">💬 Conversa<small>Papo livre e perguntas</small></button>
      <button class="mode" data-mode="pc">🖥 Comandos do PC<small>Terminal, abrir sites, listar</small></button>
      <button class="mode" data-mode="blender">🧊 Blender 3D<small>Criar cenas e objetos</small></button>
      <button class="mode" data-mode="arquivos">📁 Arquivos<small>Criar pastas e arquivos</small></button>
      <button class="mode" data-mode="video">🎬 Edição de vídeo<small>Agitada, calma ou fácil</small></button>
      <button class="mode" data-mode="auto">⚡ Autônomo<small>Usa tudo sozinho</small></button>
    </div>
    <div class="panel">
      <h3>PAINEL DA CONVERSA</h3>
      <div class="row"><span>Estado</span><b id="pState">EM ESPERA</b></div>
      <div class="row"><span>Modo</span><b id="pMode">CONVERSA</b></div>
      <div class="row"><span>IA</span><b id="pAI">—</b></div>
      <div class="row"><span>Mensagens</span><b id="pCount">0</b></div>
      <div class="row"><span>Ações feitas</span><b id="pActs">0</b></div>
      <div class="row"><span>Última resposta</span><b id="pLat">—</b></div>
      <div class="meter"><i id="vu"></i></div>
    </div>
  </aside>

  <main class="center">
    <div id="subtitle"></div>
    <canvas id="reactor"></canvas>
    <div id="powerTxt">IA DESATIVADA</div>
    <button id="power" title="Ligar / desligar a IA"><svg viewBox="0 0 24 24"><path d="M12 3v8"/><path d="M6.4 6.6a8 8 0 1 0 11.2 0"/></svg></button>
    <div id="stateLabel">EM ESPERA</div>
  </main>

  <aside class="right">
    <div class="panel" id="chat"><h3>CONVERSA</h3><div class="log" id="log"></div></div>
    <div class="panel" id="acts"><h3>AÇÕES NO PC</h3><div class="log" id="actlog"></div></div>
  </aside>

  <footer>
    <button class="btn" id="mic" title="Falar">🎤</button>
    <input id="inp" placeholder="Digite um comando ou fale com o Jarvis..." autocomplete="off">
    <button class="btn" id="send">ENVIAR</button>
    <label class="chk"><input type="checkbox" id="wake"> palavra "Jarvis"</label>
    <label class="chk"><input type="checkbox" id="voiceOn" checked> voz</label>
  </footer>
</div>

<div id="modal">
  <div class="card">
    <h2>CONFIGURAÇÕES</h2>
    <label>IA</label>
    <select id="cProv">
      <option value="claude">Claude (Anthropic)</option>
      <option value="openai">ChatGPT (OpenAI)</option>
      <option value="gemini">Gemini (Google)</option>
    </select>
    <label>CHAVE DE API</label>
    <input id="cKey" type="password" placeholder="cole sua chave aqui">
    <div class="hint">Fica salva só no seu navegador (localStorage). Nunca vai pro GitHub.</div>
    <label>MODELO (opcional)</label>
    <input id="cModel" placeholder="deixe vazio para o padrão">
    <label>ENDEREÇO DO SERVIDOR LOCAL</label>
    <input id="cUrl" value="http://localhost:8765">
    <label>TOKEN DO SERVIDOR LOCAL</label>
    <input id="cTok" placeholder="aparece no terminal quando você roda jarvis_server.py">
    <label class="chk" style="margin-top:14px;font-size:12px"><input type="checkbox" id="cAuto"> Executar comandos do terminal SEM pedir confirmação</label>
    <div class="hint">Desligado é o recomendado: o Jarvis sempre mostra o comando e você aprova.</div>
    <div class="btns"><button class="btn" id="cCancel">FECHAR</button><button class="btn" id="cSave">SALVAR</button></div>
  </div>
</div>

<div id="confirm">
  <div class="card">
    <h2>CONFIRMAR EXECUÇÃO</h2>
    <p style="font-size:13px;margin-bottom:8px">O Jarvis quer rodar isto no seu PC:</p>
    <pre id="confTxt"></pre>
    <div class="btns"><button class="btn" id="confNo">NEGAR</button><button class="btn" id="confYes">PERMITIR</button></div>
  </div>
</div>

<script>
/* ============================== estado / config ============================== */
const $ = id => document.getElementById(id);
const DEF_MODELS = {claude:"claude-sonnet-5-5", openai:"gpt-4o-mini", gemini:"gemini-2.0-flash"};
const cfg = Object.assign({prov:"claude",key:"",model:"",url:"http://localhost:8765",tok:"",auto:false},
  JSON.parse(localStorage.getItem("jarvis_cfg")||"{}"));
const saveCfg = () => localStorage.setItem("jarvis_cfg", JSON.stringify(cfg));

let mode = "conversa", state = "off", history = [], nMsg = 0, nActs = 0, pcOnline = false, aiOn = false, pcInfo = {}, battery = null;
const STATE = {
  off:{t:"DESATIVADA", col:[255,59,77]},
  idle:{t:"EM ESPERA", col:[0,229,255]},
  listening:{t:"OUVINDO", col:[77,255,157]},
  thinking:{t:"PENSANDO", col:[255,196,0]},
  speaking:{t:"FALANDO", col:[80,170,255]},
  executing:{t:"EXECUTANDO", col:[255,77,157]}
};
function setState(s){
  state = s;
  $("stateLabel").textContent = $("pState").textContent = STATE[s].t;
  const c = STATE[s].col, css = `rgb(${c})`;
  document.documentElement.style.setProperty("--c", css);
}

const MODE_INFO = {
  conversa:["CONVERSA","Modo conversa: responda de forma natural. Use ações só se o usuário pedir claramente."],
  pc:["COMANDOS DO PC","Modo comandos do PC: priorize ações 'comando', 'abrir_url' e 'listar'."],
  blender:["BLENDER 3D","Modo Blender: priorize a ação 'blender' com código Python bpy completo e bonito."],
  arquivos:["ARQUIVOS","Modo arquivos: priorize 'criar_pasta' e 'criar_arquivo'."],
  video:["EDIÇÃO DE VÍDEO","Modo edição de vídeo: priorize buscar_midia, info_midia, video, ffmpeg e abrir_programa. Pergunte rápido o estilo (agitado, calmo ou fácil) só se o usuário não disse."],
  auto:["AUTÔNOMO","Modo autônomo: escolha sozinho as melhores ações e combine várias se precisar."]
};
document.querySelectorAll(".mode").forEach(b=>b.onclick=()=>{
  document.querySelectorAll(".mode").forEach(x=>x.classList.remove("on"));
  b.classList.add("on"); mode = b.dataset.mode; $("pMode").textContent = MODE_INFO[mode][0];
  addMsg("j","Modo "+MODE_INFO[mode][0].toLowerCase()+" ativado.", true);
});

/* ============================== relógio / UI ============================== */
setInterval(()=>{ $("clock").textContent = new Date().toLocaleTimeString("pt-BR"); },500);
function addMsg(who, text, quiet){
  const d = document.createElement("div"); d.className = "msg "+who;
  d.innerHTML = `<div class="who">${who==="u"?"VOCÊ":"JARVIS"}</div><div class="t"></div>`;
  $("log").appendChild(d); const t = d.querySelector(".t");
  if(who==="j"){ let i=0; const iv=setInterval(()=>{ t.textContent=text.slice(0,++i); $("log").scrollTop=1e9; if(i>=text.length)clearInterval(iv); },12); }
  else t.textContent = text;
  $("log").scrollTop = 1e9;
  if(!quiet){ nMsg++; $("pCount").textContent = nMsg; }
}
function addAct(text, cls){
  const d = document.createElement("div"); d.className = "act "+(cls||""); d.textContent = text;
  $("actlog").appendChild(d); $("actlog").scrollTop = 1e9;
}

/* ============================== boot ============================== */
(function boot(){
  const lines = ["> iniciando núcleo...","> carregando módulos de voz...","> calibrando reator de arco...","> verificando protocolos de segurança...","> conectando interfaces...","> sistemas online."];
  let i=0; const pre=$("bootlog");
  const iv=setInterval(()=>{
    pre.textContent += lines[i]+"\n"; $("bootbar").style.width=((i+1)/lines.length*100)+"%"; i++;
    if(i>=lines.length){ clearInterval(iv); setTimeout(()=>{ $("boot").style.opacity=0; setTimeout(()=>$("boot").remove(),800);
      greet(); },500); }
  },380);
})();
function greet(){
  setState("off");
  addMsg("j","Interface carregada. Toque no botão de energia para ativar a IA.", true);
  $("subtitle").textContent = "Toque no botão de energia para ativar";
  pingPC(); setInterval(pingPC, 8000);
}

/* ============================== reator (canvas) ============================== */
const cv = $("reactor"), ctx = cv.getContext("2d");
let W,H,DPR; function resize(){ DPR=devicePixelRatio||1; const r=cv.getBoundingClientRect(); W=r.width;H=r.height; cv.width=W*DPR;cv.height=H*DPR; ctx.setTransform(DPR,0,0,DPR,0,0); }
addEventListener("resize",resize); setTimeout(resize,50);
let amp=0, ampT=0, tt=0, curCol=[0,229,255];
const parts = Array.from({length:70},()=>({a:Math.random()*6.28,r:.55+Math.random()*.5,s:(Math.random()-.5)*.01,z:Math.random()*2+.5}));
function frame(){
  requestAnimationFrame(frame); if(!W) return;
  tt += .016;
  const target = STATE[state].col;
  for(let i=0;i<3;i++) curCol[i] += (target[i]-curCol[i])*.06;
  const col = `${curCol[0]|0},${curCol[1]|0},${curCol[2]|0}`;
  ampT = state==="off" ? .04 : state==="speaking" ? .5+Math.random()*.5 : state==="listening" ? (micLevel||.2+Math.random()*.2) : state==="thinking" ? .5+Math.sin(tt*6)*.3 : state==="executing" ? .6+Math.sin(tt*10)*.3 : .15+Math.sin(tt*1.5)*.08;
  amp += (ampT-amp)*.15;
  $("vu").style.width = Math.min(100,amp*100)+"%";
  ctx.clearRect(0,0,W,H);
  const cx=W/2, cy=(H-70)/2+8, R=Math.min(W,H-110)*.34;
  const speed = state==="off"?.25:state==="thinking"?3:state==="executing"?4:1;

  // brilho
  const g = ctx.createRadialGradient(cx,cy,0,cx,cy,R*1.6);
  g.addColorStop(0,`rgba(${col},${.25+amp*.35})`); g.addColorStop(.5,`rgba(${col},.06)`); g.addColorStop(1,"transparent");
  ctx.fillStyle=g; ctx.fillRect(0,0,W,H);

  ctx.save(); ctx.translate(cx,cy);
  // anéis
  const ring=(r,w,dash,rot,alpha)=>{ ctx.save(); ctx.rotate(rot); ctx.strokeStyle=`rgba(${col},${alpha})`; ctx.lineWidth=w; ctx.setLineDash(dash);
    ctx.beginPath(); ctx.arc(0,0,r,0,6.283); ctx.stroke(); ctx.restore(); };
  ring(R*1.45,1,[4,10],tt*.2*speed,.4);
  ring(R*1.3,2,[60,30,10,30],-tt*.35*speed,.6);
  ring(R*1.15,1,[2,6],tt*.6*speed,.5);
  // ticks
  ctx.save(); ctx.rotate(tt*.15*speed); ctx.strokeStyle=`rgba(${col},.7)`; ctx.lineWidth=2; ctx.setLineDash([]);
  for(let i=0;i<60;i++){ const a=i/60*6.283, l=i%5===0?14:7; ctx.beginPath(); ctx.moveTo(Math.cos(a)*R*1.0,Math.sin(a)*R*1.0); ctx.lineTo(Math.cos(a)*(R*1.0+l),Math.sin(a)*(R*1.0+l)); ctx.stroke(); }
  ctx.restore();
  // arcos giratórios (estilo reator)
  for(let k=0;k<3;k++){ ctx.save(); ctx.rotate(tt*(.7+k*.3)*(k%2?-1:1)*speed+k*2); ctx.strokeStyle=`rgba(${col},${.9-k*.2})`; ctx.lineWidth=5-k; ctx.setLineDash([]);
    ctx.shadowColor=`rgb(${col})`; ctx.shadowBlur=14; ctx.beginPath(); ctx.arc(0,0,R*(.88-k*.12),0,1.6); ctx.stroke(); ctx.restore(); }
  // triângulo núcleo
  ctx.save(); ctx.rotate(-tt*.4*speed); ctx.strokeStyle=`rgba(${col},.8)`; ctx.lineWidth=2; ctx.setLineDash([]);
  for(let s=0;s<2;s++){ ctx.beginPath(); for(let i=0;i<3;i++){ const a=i/3*6.283+s*3.1416; const rr=R*.5*(1+amp*.25); ctx[i?"lineTo":"moveTo"](Math.cos(a)*rr,Math.sin(a)*rr);} ctx.closePath(); ctx.stroke(); }
  ctx.restore();
  // ondas de voz
  ctx.strokeStyle=`rgba(${col},.9)`; ctx.lineWidth=2; ctx.beginPath();
  for(let i=0;i<=120;i++){ const a=i/120*6.283; const rr=R*.3 + Math.sin(a*8+tt*8)*amp*R*.12 + Math.sin(a*3-tt*5)*amp*R*.08;
    ctx[i?"lineTo":"moveTo"](Math.cos(a)*rr,Math.sin(a)*rr); } ctx.closePath(); ctx.stroke();
  // núcleo
  const cg=ctx.createRadialGradient(0,0,0,0,0,R*.28); cg.addColorStop(0,"rgba(255,255,255,.95)"); cg.addColorStop(.4,`rgba(${col},.8)`); cg.addColorStop(1,"transparent");
  ctx.fillStyle=cg; ctx.beginPath(); ctx.arc(0,0,R*(.26+amp*.06),0,6.283); ctx.fill();
  // partículas
  parts.forEach(p=>{ p.a+=p.s*speed; const rr=R*(1.55+ (p.r-.55)*.7); ctx.fillStyle=`rgba(${col},${.3+p.z*.2})`;
    ctx.beginPath(); ctx.arc(Math.cos(p.a)*rr,Math.sin(p.a)*rr,p.z,0,6.283); ctx.fill(); });
  ctx.restore();
}
frame();

/* ============================== voz: ouvir e falar ============================== */
const SR = window.SpeechRecognition || window.webkitSpeechRecognition;
let rec=null, listening=false, micLevel=0;
function setupRec(){
  if(!SR){ addMsg("j","Seu navegador não suporta reconhecimento de voz. Use o Chrome ou o Edge.",true); return null; }
  const r = new SR(); r.lang="pt-BR"; r.continuous=true; r.interimResults=true;
  r.onresult = e=>{ if(!aiOn) return;
    let fin="", tmp="";
    for(let i=e.resultIndex;i<e.results.length;i++){ const t=e.results[i][0].transcript; e.results[i].isFinal ? fin+=t : tmp+=t; }
    micLevel = .3+Math.random()*.5;
    if(tmp) $("subtitle").textContent = tmp;
    if(fin){ $("subtitle").textContent = fin; handleVoice(fin.trim()); }
  };
  r.onend = ()=>{ if(listening){ try{r.start();}catch(e){} } };
  r.onerror = e=>{ if(e.error==="not-allowed"){ listening=false; $("mic").classList.remove("on"); addMsg("j","Preciso de permissão para usar o microfone.",true); } };
  return r;
}
function handleVoice(text){
  if($("wake").checked){
    const m = text.toLowerCase().match(/j[aá]rvis[,\s]*(.*)/);
    if(!m) return; text = m[1].trim(); if(!text){ reply("Pois não, senhor?"); return; }
  }
  stopSpeaking(); ask(text);
}
$("mic").onclick = ()=>{
  if(!aiOn){ offHint(); return; }
  if(!rec) rec = setupRec(); if(!rec) return;
  listening = !listening; $("mic").classList.toggle("on", listening);
  if(listening){ try{rec.start();}catch(e){} if(state==="idle") setState("listening"); }
  else { rec.stop(); if(state==="listening") setState("idle"); $("subtitle").textContent=""; micLevel=0; }
};

let ptVoice=null;
function pickVoice(){ const v=speechSynthesis.getVoices(); ptVoice = v.find(x=>/pt-BR/i.test(x.lang)&&/(male|masc|daniel|antonio|google)/i.test(x.name)) || v.find(x=>/pt-BR/i.test(x.lang)) || v.find(x=>/^pt/i.test(x.lang)); }
if("speechSynthesis" in window){ speechSynthesis.onvoiceschanged=pickVoice; pickVoice(); }
function stopSpeaking(){ if("speechSynthesis" in window) speechSynthesis.cancel(); }
function speak(text, done){
  if(!$("voiceOn").checked || !("speechSynthesis" in window)){ done&&done(); return; }
  const u = new SpeechSynthesisUtterance(text.replace(/[*_`#]/g,""));
  u.lang="pt-BR"; if(ptVoice) u.voice=ptVoice; u.rate=1.02; u.pitch=.9;
  u.onstart = ()=>{ if(aiOn) setState("speaking"); };
  u.onend = u.onerror = ()=>{ done&&done(); };
  speechSynthesis.speak(u);
}
function backToIdle(){ setState(!aiOn?"off":listening?"listening":"idle"); $("subtitle").textContent = aiOn ? "" : "Toque no botão de energia para ativar"; }
function reply(text){
  addMsg("j", text); $("subtitle").textContent = text.length>140 ? text.slice(0,140)+"…" : text;
  speak(text, backToIdle); if(!$("voiceOn").checked) setTimeout(backToIdle, 1500);
}

/* ============================== servidor local ============================== */
async function pingPC(){
  if(!cfg.tok){ netStatus(false,"configure o token"); return; }
  try{
    const r = await fetch(cfg.url+"/ping",{headers:{"X-Jarvis-Token":cfg.tok}});
    const j = await r.json(); pcOnline = !!j.ok; if(j.ok) pcInfo = j;
    netStatus(pcOnline, pcOnline ? "PC conectado"+(j.blender?" · Blender ✓":" · sem Blender") : "token inválido");
  }catch(e){ pcOnline=false; netStatus(false,"PC desconectado"); }
}
function netStatus(ok,t){ $("dotNet").className="dot "+(ok?"ok":"bad"); $("netTxt").textContent=t; }
async function runActions(acoes){
  if(!acoes||!acoes.length) return [];
  if(!pcOnline){ addAct("Servidor local offline. Rode: python jarvis_server.py","err"); reply("Não consigo executar, senhor. O servidor do computador não está ligado."); return []; }
  setState("executing");
  const list=[]; let results=[];
  for(const a of acoes){
    if((a.tipo==="comando"||a.tipo==="ffmpeg") && !cfg.auto){
      const txt = a.tipo==="comando" ? a.cmd : "ffmpeg "+(a.args||[]).join(" ");
      const ok = await confirmDlg(txt);
      if(!ok){ addAct("Negado: "+txt,"err"); continue; }
    }
    list.push(a);
  }
  if(!list.length){ backToIdle(); return []; }
  list.forEach(a=>addAct("→ "+a.tipo+": "+(a.caminho||a.cmd||a.url||a.nome||"")));
  try{
    const r = await fetch(cfg.url+"/exec",{method:"POST",headers:{"Content-Type":"application/json","X-Jarvis-Token":cfg.tok},body:JSON.stringify({acoes:list})});
    const j = await r.json();
    results = j.resultados||[];
    results.forEach(x=>{
      addAct((x.ok?"✔ ":"✖ ")+x.saida, x.ok?"okk":"err"); nActs++;
      if(x.job) pollJob(x.job);
    });
    $("pActs").textContent = nActs;
  }catch(e){ addAct("Erro de conexão: "+e.message,"err"); }
  backToIdle();
  return results;
}
function pollJob(id){
  const el = document.createElement("div"); el.className="act"; el.textContent="🎬 0%";
  $("actlog").appendChild(el);
  setState("executing");
  const iv = setInterval(async()=>{
    try{
      const r = await fetch(cfg.url+"/job?id="+id,{headers:{"X-Jarvis-Token":cfg.tok}});
      const j = await r.json();
      el.textContent = "🎬 "+Math.round((j.prog||0)*100)+"% — "+(j.msg||"");
      $("actlog").scrollTop = 1e9;
      if(j.status!=="rodando"){
        clearInterval(iv);
        const ok = j.status==="pronto";
        el.textContent = (ok?"✔ ":"✖ ")+j.saida; el.className = "act "+(ok?"okk":"err");
        nActs++; $("pActs").textContent = nActs;
        backToIdle();
        if(aiOn) reply(ok ? "Edição concluída, senhor. O vídeo está na pasta Jarvis, em vídeos." : "A edição falhou, senhor. "+String(j.saida).slice(0,140));
      }
    }catch(e){ clearInterval(iv); el.textContent="Perdi a conexão com o servidor durante a edição."; el.className="act err"; backToIdle(); }
  },1500);
}
function confirmDlg(txt){
  return new Promise(res=>{
    $("confTxt").textContent = txt; $("confirm").classList.add("show");
    $("confYes").onclick=()=>{ $("confirm").classList.remove("show"); res(true); };
    $("confNo").onclick =()=>{ $("confirm").classList.remove("show"); res(false); };
  });
}

/* ============================== IA ============================== */
const SYSTEM = `Você é o J.A.R.V.I.S., assistente pessoal em português do Brasil, educado, inteligente, com um leve humor britânico. Trate o usuário por "senhor" (ou "senhora" se ele indicar). Respostas curtas e naturais, pois serão FALADAS em voz alta: sem markdown, sem listas, sem emojis.
Você controla o computador do usuário por meio de ações. Responda SEMPRE e SOMENTE com um JSON válido neste formato:
{"fala":"o que você diz em voz alta","acoes":[ ... ]}
Tipos de ação disponíveis ("acoes" pode ser []):
{"tipo":"criar_pasta","caminho":"Projetos/MeuJogo"}  (caminho relativo à pasta de trabalho)
{"tipo":"criar_arquivo","caminho":"Projetos/nota.txt","conteudo":"texto"}
{"tipo":"listar","caminho":"."}
{"tipo":"abrir_url","url":"https://..."}
{"tipo":"comando","cmd":"comando de terminal"}  (o usuário confirma antes de rodar)
{"tipo":"blender","nome":"nome_da_cena","codigo":"código Python bpy completo"}
Regras do Blender: o código roda dentro do Blender com "import bpy". Comece limpando a cena (bpy.ops.object.select_all(action='SELECT'); bpy.ops.object.delete()), crie os objetos, materiais coloridos, uma câmera e luzes. Use apenas bpy e math. Não use input() nem caminhos fora da pasta.
Ações de vídeo:
{"tipo":"buscar_midia","midia":"video|audio|imagem|todos","filtro":"parte do nome (opcional)","pasta":"(opcional)"}  (lista arquivos do PC; o resultado volta para você)
{"tipo":"info_midia","caminho":"arquivo"}  (duração, resolução, áudio; o resultado volta para você)
{"tipo":"video","nome":"edicao1","estilo":"agitado|calmo|facil","entradas":["caminho1.mp4","foto.jpg"],"musica":"caminho.mp3 (opcional)","duracao_alvo":30,"formato":"vertical|horizontal|quadrado","bpm":128,"legendas":[{"texto":"...","inicio":0,"fim":2,"posicao":"baixo|meio|topo"}],"abrir":true}
{"tipo":"ffmpeg","args":["-i","a.mp4","-vf","...","saida.mp4"]}  (edição livre com FFmpeg, o usuário confirma; roda na pasta Jarvis/videos)
{"tipo":"abrir_programa","programa":"capcut|davinci|premiere|filmora|vegas|shotcut|kdenlive","arquivo":"opcional"}
Regras de vídeo: você edita de verdade com FFmpeg e entrega um .mp4 pronto em Jarvis/videos (mais um .edl de cortes que o DaVinci e o Premiere importam). CapCut e outros editores NÃO podem ser controlados por dentro: edite com a ação "video" e depois use "abrir_programa" para abrir o editor e mostrar o arquivo pronto para o usuário continuar lá. Seja honesto sobre isso.
Se o usuário não disse quais arquivos usar, use primeiro "buscar_midia" sozinha no turno (o resultado volta automaticamente) e só então monte a edição com os arquivos mais recentes ou mais relevantes.
Estilos: "agitado" = cortes rápidos no ritmo da música (use o bpm da música se souber), zoom na batida, tremida, glitch, flash, cores fortes, acelerações; é o estilo de edits de TikTok e Reels. "calmo" = takes longos, zoom lento, transições suaves, cor suave, música baixa. "facil" = cortes simples em sequência, sem efeitos pesados. Escolha pelo pedido: "complicado", "cheio de efeito" ou "dinâmico" = agitado; "relaxante", "suave" ou "cinematográfico" = calmo; "simples" ou "rápido de fazer" = facil.
Nunca invente que fez algo: a fala deve dizer o que você VAI fazer. Se o pedido for perigoso (apagar arquivos do sistema, formatar disco, etc.), recuse na fala e não envie ação.
`;
async function callAI(msgs){
  const model = cfg.model || DEF_MODELS[cfg.prov], sys = SYSTEM + "\n" + MODE_INFO[mode][1] + "\n" + buildContext();
  if(cfg.prov==="claude"){
    const r = await fetch("https://api.anthropic.com/v1/messages",{method:"POST",headers:{"content-type":"application/json","x-api-key":cfg.key,"anthropic-version":"2023-06-01","anthropic-dangerous-direct-browser-access":"true"},
      body:JSON.stringify({model,max_tokens:4000,system:sys,messages:msgs})});
    const j = await r.json(); if(!r.ok) throw new Error(j.error?.message||r.status); return j.content.map(c=>c.text||"").join("");
  }
  if(cfg.prov==="openai"){
    const r = await fetch("https://api.openai.com/v1/chat/completions",{method:"POST",headers:{"content-type":"application/json","authorization":"Bearer "+cfg.key},
      body:JSON.stringify({model,messages:[{role:"system",content:sys},...msgs]})});
    const j = await r.json(); if(!r.ok) throw new Error(j.error?.message||r.status); return j.choices[0].message.content;
  }
  const r = await fetch(`https://generativelanguage.googleapis.com/v1beta/models/${model}:generateContent?key=${cfg.key}`,{method:"POST",headers:{"content-type":"application/json"},
    body:JSON.stringify({systemInstruction:{parts:[{text:sys}]},contents:msgs.map(m=>({role:m.role==="assistant"?"model":"user",parts:[{text:m.content}]}))})});
  const j = await r.json(); if(!r.ok) throw new Error(j.error?.message||r.status); return j.candidates[0].content.parts.map(p=>p.text||"").join("");
}
function parseReply(txt){
  try{ const a=txt.indexOf("{"), b=txt.lastIndexOf("}"); const o=JSON.parse(txt.slice(a,b+1)); return {fala:o.fala||"", acoes:Array.isArray(o.acoes)?o.acoes:[]}; }
  catch(e){ return {fala:txt.replace(/[{}"]/g,""), acoes:[]}; }
}
let busy=false;
async function ask(text){
  if(!text||busy) return;
  if(!aiOn){ offHint(); return; }
  if(!cfg.key){ addMsg("u",text); reply("Preciso da sua chave de API, senhor. Abra as configurações."); $("modal").classList.add("show"); return; }
  busy=true; addMsg("u",text); setState("thinking"); $("subtitle").textContent="Processando...";
  history.push({role:"user",content:text}); history = history.slice(-16);
  const t0 = performance.now();
  try{
    let round = 0;
    while(true){
      const raw = await callAI(history);
      $("pLat").textContent = ((performance.now()-t0)/1000).toFixed(1)+"s";
      history.push({role:"assistant",content:raw});
      const {fala,acoes} = parseReply(raw);
      if(fala) reply(fala); else backToIdle();
      const res = await runActions(acoes);
      const fb = (res||[]).filter(x=>x.info || !x.ok);
      if(!fb.length || ++round>3) break;
      history.push({role:"user",content:"[RESULTADO DAS AÇÕES — dados do sistema, não é o usuário falando]\n"+
        fb.map(x=>x.tipo+" "+(x.ok?"OK":"ERRO")+": "+String(x.saida).slice(0,3000)).join("\n---\n")+
        "\nContinue a tarefa do usuário com base nisso."});
      setState("thinking");
    }
  }catch(e){
    history.pop(); setState("idle"); reply("Tive um problema, senhor: "+e.message);
  }
  busy=false;
}
$("send").onclick = ()=>{ const v=$("inp").value.trim(); $("inp").value=""; stopSpeaking(); ask(v); };
$("inp").addEventListener("keydown",e=>{ if(e.key==="Enter") $("send").click(); });

/* ============================== contexto em tempo real ============================== */
if(navigator.getBattery){ navigator.getBattery().then(b=>{
  const up=()=>{ battery={nivel:Math.round(b.level*100),carregando:b.charging}; };
  up(); b.onlevelchange=up; b.onchargingchange=up; }).catch(()=>{}); }
function buildContext(){
  const d = new Date(), tz = Intl.DateTimeFormat().resolvedOptions().timeZone;
  const off = -d.getTimezoneOffset()/60, offTxt = "UTC"+(off>=0?"+":"")+off;
  const ini = new Date(d.getFullYear(),0,0), diaAno = Math.floor((d-ini)/864e5);
  const L = [
    "CONTEXTO ATUAL (dados reais do dispositivo do usuário, atualizados a cada mensagem):",
    "- Data: "+d.toLocaleDateString("pt-BR",{weekday:"long",day:"numeric",month:"long",year:"numeric"}),
    "- Hora: "+d.toLocaleTimeString("pt-BR")+" ("+tz+", "+offTxt+")",
    "- ISO: "+d.toISOString()+" | dia "+diaAno+" do ano | semana "+Math.ceil(diaAno/7),
    "- Navegador: "+navigator.userAgent+" | idioma "+navigator.language,
    "- Tela: "+screen.width+"x"+screen.height+" | internet: "+(navigator.onLine?"online":"offline"),
    battery ? "- Bateria: "+battery.nivel+"% "+(battery.carregando?"(carregando)":"(descarregando)") : "",
    pcOnline ? "- PC conectado: sistema "+(pcInfo.sistema||"?")+", usuário "+(pcInfo.usuario||"?")+", máquina "+(pcInfo.maquina||"?")+", pasta de trabalho "+(pcInfo.pasta||"?")+", Blender "+(pcInfo.blender?"instalado":"não encontrado")+", FFmpeg (edição de vídeo) "+(pcInfo.ffmpeg?"instalado":"NÃO instalado: avise o usuário para instalar com 'winget install ffmpeg'") : "- PC: servidor local desconectado (você não consegue executar ações agora)",
    "- Modo atual: "+MODE_INFO[mode][0]+" | mensagens nesta sessão: "+nMsg+" | ações executadas: "+nActs,
    "Use SEMPRE estes dados para responder sobre hora, data, dia da semana, prazos e o estado do computador. Nunca diga que não tem acesso à hora ou à data: calcule diferenças de datas a partir daqui. Não invente a cidade do usuário; o fuso horário é a única pista de localização."
  ];
  return L.filter(Boolean).join("\n");
}

/* ============================== botão de energia ============================== */
function offHint(){
  $("subtitle").textContent = "IA desativada. Toque no botão de energia.";
  $("power").animate([{transform:"translateX(-50%) scale(1)"},{transform:"translateX(-50%) scale(1.25)"},{transform:"translateX(-50%) scale(1)"}],{duration:450});
}
function setPower(on){
  aiOn = on;
  $("power").classList.toggle("on", on);
  $("powerTxt").textContent = on ? "IA ATIVA" : "IA DESATIVADA";
  if(on){
    setState("idle");
    const d = new Date(), h = d.getHours();
    const g = h<12?"Bom dia":h<18?"Boa tarde":"Boa noite";
    const quando = d.toLocaleDateString("pt-BR",{weekday:"long",day:"numeric",month:"long"});
    const hora = d.toLocaleTimeString("pt-BR",{hour:"2-digit",minute:"2-digit"});
    const extra = cfg.key ? "" : " Abra as configurações e cole sua chave de API para eu poder pensar.";
    reply(g+", senhor. Sistemas ativados. São "+hora+" de "+quando+"."+extra);
  } else {
    listening = false; $("mic").classList.remove("on"); micLevel = 0;
    try{ rec && rec.stop(); }catch(e){}
    stopSpeaking(); setState("off");
    $("subtitle").textContent = "Toque no botão de energia para ativar";
    addMsg("j","Sistemas desativados.", true);
  }
}
$("power").onclick = ()=>setPower(!aiOn);

/* ============================== configurações ============================== */
function loadCfgUI(){ $("cProv").value=cfg.prov; $("cKey").value=cfg.key; $("cModel").value=cfg.model; $("cUrl").value=cfg.url; $("cTok").value=cfg.tok; $("cAuto").checked=cfg.auto; }
function aiLabel(){ $("pAI").textContent = ({claude:"CLAUDE",openai:"CHATGPT",gemini:"GEMINI"})[cfg.prov]; }
$("btnCfg").onclick=()=>{ loadCfgUI(); $("modal").classList.add("show"); };
$("cCancel").onclick=()=>$("modal").classList.remove("show");
$("cSave").onclick=()=>{
  cfg.prov=$("cProv").value; cfg.key=$("cKey").value.trim(); cfg.model=$("cModel").value.trim();
  cfg.url=$("cUrl").value.trim().replace(/\/$/,""); cfg.tok=$("cTok").value.trim(); cfg.auto=$("cAuto").checked;
  saveCfg(); aiLabel(); $("modal").classList.remove("show"); pingPC(); reply("Configurações salvas.");
};
aiLabel();
</script>
</body>
</html>
#!/usr/bin/env python3
"""
JARVIS - Servidor local (ponte entre o site e o seu computador).

Roda no SEU PC. O site (GitHub Pages) manda ações pra cá e este programa executa:
  - criar pastas e arquivos (dentro da pasta de trabalho)
  - rodar comandos no terminal (o site pede sua confirmação antes)
  - abrir sites
  - criar cenas 3D no Blender (via script Python do Blender)

Só usa a biblioteca padrão do Python. Python 3.8+.
Uso:  python jarvis_server.py
"""
import getpass
import glob
import json
import platform
import random
import textwrap
import threading
import uuid
from urllib.parse import urlparse, parse_qs
import os
import secrets
import shutil
import subprocess
import sys
import webbrowser
from http.server import BaseHTTPRequestHandler, ThreadingHTTPServer
from pathlib import Path

PORT = int(os.environ.get("JARVIS_PORT", "8765"))
WORKSPACE = Path(os.environ.get("JARVIS_DIR", Path.home() / "Jarvis")).resolve()
TOKEN_FILE = Path(__file__).with_name("jarvis_token.txt")

WORKSPACE.mkdir(parents=True, exist_ok=True)
(WORKSPACE / "blender").mkdir(exist_ok=True)

if TOKEN_FILE.exists():
    TOKEN = TOKEN_FILE.read_text().strip()
else:
    TOKEN = secrets.token_urlsafe(16)
    TOKEN_FILE.write_text(TOKEN)


def find_blender():
    env = os.environ.get("BLENDER_PATH")
    if env and Path(env).exists():
        return env
    found = shutil.which("blender")
    if found:
        return found
    patterns = [
        r"C:\Program Files\Blender Foundation\Blender*\blender.exe",
        r"C:\Program Files (x86)\Blender Foundation\Blender*\blender.exe",
        "/Applications/Blender.app/Contents/MacOS/Blender",
        "/usr/bin/blender",
        "/snap/bin/blender",
    ]
    for p in patterns:
        hits = sorted(glob.glob(p))
        if hits:
            return hits[-1]
    return None


BLENDER = find_blender()
FFMPEG = os.environ.get("FFMPEG_PATH") or shutil.which("ffmpeg")
FFPROBE = os.environ.get("FFPROBE_PATH") or shutil.which("ffprobe")
(WORKSPACE / "videos").mkdir(exist_ok=True)


def safe_path(rel):
    """Só permite caminhos dentro da pasta de trabalho."""
    p = (WORKSPACE / str(rel).lstrip("/\\")).resolve()
    if WORKSPACE != p and WORKSPACE not in p.parents:
        raise ValueError("Caminho fora da pasta de trabalho: " + str(rel))
    return p


def safe_name(name):
    keep = "".join(c if c.isalnum() or c in "-_" else "_" for c in str(name or "cena"))
    return keep[:50] or "cena"


# ---------------------------------------------------------------- ações
def act_criar_pasta(a):
    p = safe_path(a["caminho"])
    p.mkdir(parents=True, exist_ok=True)
    return f"Pasta criada: {p}"


def act_criar_arquivo(a):
    p = safe_path(a["caminho"])
    p.parent.mkdir(parents=True, exist_ok=True)
    p.write_text(a.get("conteudo", ""), encoding="utf-8")
    return f"Arquivo criado: {p}"


def act_listar(a):
    p = safe_path(a.get("caminho", "."))
    itens = sorted(os.listdir(p))[:100]
    return "Conteúdo de " + str(p) + ":\n" + "\n".join(itens)


def act_abrir_url(a):
    url = str(a["url"])
    if not url.startswith(("http://", "https://")):
        raise ValueError("Só abro links http/https.")
    webbrowser.open(url)
    return "Abri " + url


def act_comando(a):
    r = subprocess.run(
        a["cmd"], shell=True, cwd=str(WORKSPACE),
        capture_output=True, text=True, timeout=int(a.get("timeout", 60)),
    )
    out = (r.stdout or "") + (r.stderr or "")
    return f"[código {r.returncode}]\n{out[-1500:]}".strip()


BLENDER_WRAPPER = '''
import bpy, traceback
try:
    exec(compile(open(r"{user}", encoding="utf-8").read(), "jarvis_user", "exec"))
except Exception:
    traceback.print_exc()
try:
    bpy.ops.wm.save_as_mainfile(filepath=r"{blend}")
except Exception:
    traceback.print_exc()
'''


def act_blender(a):
    if not BLENDER:
        raise RuntimeError(
            "Não achei o Blender. Defina a variável BLENDER_PATH com o caminho do executável."
        )
    nome = safe_name(a.get("nome"))
    pasta = WORKSPACE / "blender"
    user = pasta / f"{nome}.py"
    blend = pasta / f"{nome}.blend"
    wrapper = pasta / f"_run_{nome}.py"
    user.write_text(a["codigo"], encoding="utf-8")
    wrapper.write_text(BLENDER_WRAPPER.format(user=str(user), blend=str(blend)), encoding="utf-8")
    subprocess.Popen([BLENDER, "--python", str(wrapper)])
    return f"Abrindo o Blender com a cena '{nome}'. Arquivo: {blend}"


# ================================================================ VÍDEO
VIDEO_EXT = {".mp4", ".mov", ".mkv", ".avi", ".webm", ".m4v"}
AUDIO_EXT = {".mp3", ".wav", ".m4a", ".aac", ".ogg", ".flac"}
IMG_EXT = {".jpg", ".jpeg", ".png", ".webp"}
HOME = Path.home()
MEDIA_ROOTS = [HOME / "Videos", HOME / "Movies", HOME / "Downloads", HOME / "Desktop",
               HOME / "Documents", HOME / "Music", HOME / "Pictures", WORKSPACE]
FORMATS = {"vertical": (1080, 1920), "horizontal": (1920, 1080), "quadrado": (1080, 1080)}
FONT_CANDIDATES = [
    r"C:\Windows\Fonts\arialbd.ttf", r"C:\Windows\Fonts\arial.ttf",
    "/System/Library/Fonts/Supplemental/Arial Bold.ttf", "/Library/Fonts/Arial.ttf",
    "/usr/share/fonts/truetype/dejavu/DejaVuSans-Bold.ttf", "/usr/share/fonts/TTF/DejaVuSans-Bold.ttf",
]
JOBS = {}


def need_ffmpeg():
    if not FFMPEG or not FFPROBE:
        raise RuntimeError("FFmpeg não encontrado. Instale (Windows: winget install ffmpeg) e reinicie o servidor.")


def media_path(p):
    """Acha um arquivo de mídia: caminho absoluto, relativo à pasta Jarvis/Home, ou pelo nome."""
    raw = os.path.expanduser(str(p))
    for c in (Path(raw), WORKSPACE / raw, HOME / raw):
        if c.is_file():
            return c.resolve()
    name = Path(raw).name
    for root in MEDIA_ROOTS:
        if not root.exists():
            continue
        for dp, dn, fn in os.walk(root):
            if len(Path(dp).relative_to(root).parts) > 3:
                dn[:] = []
                continue
            if name in fn:
                return (Path(dp) / name).resolve()
    raise FileNotFoundError("Não achei o arquivo: " + str(p))


def probe(path):
    r = subprocess.run(
        [FFPROBE, "-v", "error", "-show_entries",
         "format=duration:stream=codec_type,width,height", "-of", "json", str(path)],
        capture_output=True, text=True, timeout=60)
    j = json.loads(r.stdout or "{}")
    streams = j.get("streams", [])
    v = next((s for s in streams if s.get("codec_type") == "video"), None)
    try:
        dur = float(j.get("format", {}).get("duration") or 0)
    except ValueError:
        dur = 0.0
    return {"dur": dur, "video": bool(v), "w": (v or {}).get("width", 0), "h": (v or {}).get("height", 0),
            "audio": any(s.get("codec_type") == "audio" for s in streams)}


def run_ff(args, cwd, timeout=1800):
    r = subprocess.run([FFMPEG, "-y", "-hide_banner", "-loglevel", "error"] + args,
                       cwd=str(cwd), capture_output=True, text=True, timeout=timeout)
    if r.returncode != 0:
        raise RuntimeError("FFmpeg falhou: " + (r.stderr or "")[-700:])


def open_default(p):
    if sys.platform.startswith("win"):
        os.startfile(str(p))  # noqa
    elif sys.platform == "darwin":
        subprocess.Popen(["open", str(p)])
    else:
        subprocess.Popen(["xdg-open", str(p)])


def reveal(p):
    p = Path(p)
    if sys.platform.startswith("win"):
        subprocess.Popen(["explorer", "/select,", str(p)])
    elif sys.platform == "darwin":
        subprocess.Popen(["open", "-R", str(p)])
    else:
        subprocess.Popen(["xdg-open", str(p.parent)])


def act_buscar_midia(a):
    kinds = {"video": VIDEO_EXT, "audio": AUDIO_EXT, "imagem": IMG_EXT}
    exts = kinds.get(a.get("midia", "video"), VIDEO_EXT | AUDIO_EXT | IMG_EXT)
    filtro = str(a.get("filtro", "")).lower()
    roots = [Path(os.path.expanduser(a["pasta"]))] if a.get("pasta") else MEDIA_ROOTS
    achados, vistos = [], set()
    for root in roots:
        if not root.exists():
            continue
        for dp, dn, fn in os.walk(root):
            if len(Path(dp).relative_to(root).parts) > 3:
                dn[:] = []
                continue
            dn[:] = [d for d in dn if not d.startswith(".") and d not in ("node_modules", "AppData", "_tmp_")]
            for f in fn:
                if Path(f).suffix.lower() in exts and filtro in f.lower():
                    p = Path(dp) / f
                    if str(p) in vistos or "_tmp_" in str(p):
                        continue
                    vistos.add(str(p))
                    try:
                        st = p.stat()
                    except OSError:
                        continue
                    achados.append((st.st_mtime, str(p), st.st_size))
    achados.sort(reverse=True)
    linhas = [f"{p}  ({s / 1e6:.1f} MB)" for _, p, s in achados[:40]]
    out = ("Mídias encontradas (mais recentes primeiro):\n" + "\n".join(linhas)) if linhas else "Nenhuma mídia encontrada."
    return out


def act_info_midia(a):
    need_ffmpeg()
    p = media_path(a["caminho"])
    i = probe(p)
    return f"{p}\nduração {i['dur']:.1f}s, {i['w']}x{i['h']}, áudio: {'sim' if i['audio'] else 'não'}"


def upd(job, prog, msg):
    job["prog"], job["msg"] = prog, msg


def plan_segments(clips, style, target, bpm, rnd):
    segs, total, n = [], 0.0, len(clips)
    beat = 60.0 / max(60.0, min(200.0, bpm))
    if style == "agitado":
        k = 0
        while total < target and len(segs) < 150:
            ci = k % n
            k += 1
            c = clips[ci]
            want = max(0.35, rnd.choice([0.5, 1, 1, 2]) * beat)
            speed = 1 if c["img"] else rnd.choice([1, 1, 1, 1.5, 2])
            src = want * speed
            if c["img"]:
                start = 0.0
            else:
                src = min(src, c["dur"])
                start = rnd.uniform(0, max(0.0, c["dur"] - src))
            ln = src / speed
            if total + ln > target:
                ln = target - total
                src = ln * speed
            if ln < 0.15:
                break
            segs.append(dict(ci=ci, start=start, src=src, ln=ln, speed=speed))
            total += ln
    else:
        cursor, done, k = [0.0] * n, [False] * n, 0
        while not all(done) and total < target and len(segs) < 80:
            ci = k % n
            k += 1
            if done[ci]:
                continue
            c = clips[ci]
            want = rnd.uniform(3.5, 6.5) if style == "calmo" else rnd.uniform(3, 5)
            if c["img"]:
                src, start = want, 0.0
                done[ci] = True
            else:
                src = min(want, c["dur"] - cursor[ci])
                start = cursor[ci]
                cursor[ci] += src
                if c["dur"] - cursor[ci] < 0.8:
                    done[ci] = True
            if total + src > target:
                src = target - total
            if src < 0.3:
                done[ci] = True
                continue
            segs.append(dict(ci=ci, start=start, src=src, ln=src, speed=1))
            total += src
    return segs


def seg_filter(style, W, H, fps, ln, speed, rnd):
    ln = max(ln, 0.1)
    f = [f"setpts=(PTS-STARTPTS)/{speed}", f"fps={fps}",
         f"scale={W}:{H}:force_original_aspect_ratio=increase", f"crop={W}:{H}", "setsar=1"]

    def zoom(expr):
        return (f"scale=w='trunc({W}*({expr})/2)*2':h='trunc({H}*({expr})/2)*2':eval=frame,crop={W}:{H}")

    if style == "agitado":
        fx = rnd.choice(["kick", "kick", "kick", "zin", "zout", "shake", "glitch"])
        if fx == "kick":
            f.append(zoom("1+0.16*exp(-7*t)"))
        elif fx == "zin":
            f.append(zoom(f"1+0.22*t/{ln:.3f}"))
        elif fx == "zout":
            f.append(zoom(f"1.22-0.22*t/{ln:.3f}"))
        elif fx == "shake":
            m = int(W * 0.012)
            f.append(f"scale={int(W * 1.08) // 2 * 2}:{int(H * 1.08) // 2 * 2},"
                     f"crop={W}:{H}:x='(iw-{W})/2+sin(t*55)*{m}':y='(ih-{H})/2+cos(t*47)*{m}'")
        else:
            f.append("rgbashift=rh=-8:bh=8:gv=3")
        f.append("eq=contrast=1.12:saturation=1.4:brightness=0.02")
        if rnd.random() < 0.4:
            f.append("fade=t=in:st=0:d=0.1:color=white")
    elif style == "calmo":
        if rnd.random() < 0.5:
            f.append(zoom(f"1+0.07*t/{ln:.3f}"))
        else:
            f.append(zoom(f"1.07-0.07*t/{ln:.3f}"))
        f.append("eq=saturation=0.95:contrast=1.03")
        f.append("colorbalance=rs=.03:bs=-.03")
    else:
        f.append("eq=contrast=1.04:saturation=1.05")
    f.append("format=yuv420p")
    return f


def render_segment(seg, clips, idx, work, style, W, H, fps, music, rnd):
    c = clips[seg["ci"]]
    out = f"seg{idx:03d}.mp4"
    vf = ",".join(seg_filter(style, W, H, fps, seg["ln"], seg["speed"], rnd))
    args = []
    if c["img"]:
        args += ["-loop", "1", "-framerate", str(fps), "-t", f"{seg['src']:.3f}", "-i", str(c["path"])]
    else:
        args += ["-ss", f"{seg['start']:.3f}", "-t", f"{seg['src']:.3f}", "-i", str(c["path"])]
    args += ["-f", "lavfi", "-t", f"{seg['ln']:.3f}", "-i", "anullsrc=r=48000:cl=stereo"]
    use_audio = (not music) and c["audio"] and not c["img"]
    af = "aresample=48000"
    if use_audio and seg["speed"] != 1:
        af = f"atempo={seg['speed']},aresample=48000"
    args += ["-vf", vf, "-af", af, "-map", "0:v", "-map", "0:a" if use_audio else "1:a",
             "-t", f"{seg['ln']:.3f}", "-r", str(fps), "-c:v", "libx264", "-preset", "veryfast", "-crf", "17",
             "-pix_fmt", "yuv420p", "-c:a", "aac", "-b:a", "160k", "-ar", "48000", "-ac", "2", out]
    run_ff(args, work)
    return out


def join_concat(files, work):
    (work / "list.txt").write_text("".join(f"file '{f}'\n" for f in files), encoding="utf-8")
    run_ff(["-f", "concat", "-safe", "0", "-i", "list.txt", "-c", "copy", "joined.mp4"], work)


def join_xfade(files, work):
    durs = [probe(work / f)["dur"] for f in files]
    if len(files) == 1:
        shutil.copy(work / files[0], work / "joined.mp4")
        return
    xd = max(0.15, min(0.8, 0.4 * min(durs)))
    args, fc = [], []
    for f in files:
        args += ["-i", f]
    prev, preva, acc = "[0:v]", "[0:a]", durs[0]
    for i in range(1, len(files)):
        tr = "fade" if i % 2 else "dissolve"
        fc.append(f"{prev}[{i}:v]xfade=transition={tr}:duration={xd:.3f}:offset={acc - xd:.3f}[v{i}]")
        fc.append(f"{preva}[{i}:a]acrossfade=d={xd:.3f}[a{i}]")
        prev, preva, acc = f"[v{i}]", f"[a{i}]", acc + durs[i] - xd
    args += ["-filter_complex", ";".join(fc), "-map", prev, "-map", preva,
             "-c:v", "libx264", "-preset", "veryfast", "-crf", "17", "-pix_fmt", "yuv420p",
             "-c:a", "aac", "-b:a", "192k", "joined.mp4"]
    run_ff(args, work)


def final_pass(work, out_path, style, W, H, music, music_vol, legendas, fps=30):
    T = probe(work / "joined.mp4")["dur"]
    vf = []
    font_ok = False
    for fp in FONT_CANDIDATES:
        if Path(fp).exists():
            shutil.copy(fp, work / "font.ttf")
            font_ok = True
            break
    if legendas and font_ok:
        vertical = H > W
        for i, l in enumerate(legendas):
            (work / f"t{i}.txt").write_text(textwrap.fill(str(l.get("texto", "")), 22 if vertical else 38), encoding="utf-8")
            ini = float(l.get("inicio", 0))
            fim = float(l.get("fim", ini + 2))
            pos = {"topo": "h*0.12", "meio": "(h-text_h)/2"}.get(l.get("posicao", "baixo"), "h*0.74")
            size = int(H / (22 if vertical else 15))
            vf.append(f"drawtext=fontfile=font.ttf:textfile=t{i}.txt:fontsize={size}:fontcolor=white:"
                      f"borderw=5:bordercolor=black:x=(w-text_w)/2:y={pos}:enable='between(t,{ini},{fim})'")
    fin = 0.25 if style == "agitado" else 0.5
    vf.append(f"fade=t=in:st=0:d={fin}")
    vf.append(f"fade=t=out:st={max(0, T - 0.6):.2f}:d=0.6")
    args = ["-i", "joined.mp4"]
    if music:
        args += ["-stream_loop", "-1", "-i", str(music)]
    af = f"afade=t=in:st=0:d=0.5,afade=t=out:st={max(0, T - 1.5):.2f}:d=1.5"
    if music:
        af += f",volume={music_vol}"
    args += ["-vf", ",".join(vf), "-af", af, "-map", "0:v", "-map", "1:a" if music else "0:a",
             "-t", f"{T:.3f}", "-r", str(fps), "-c:v", "libx264", "-preset", "medium", "-crf", "18", "-pix_fmt", "yuv420p",
             "-c:a", "aac", "-b:a", "192k", "-movflags", "+faststart", str(out_path)]
    run_ff(args, work)
    return T, (bool(legendas) and not font_ok)


def tc(sec, fps):
    f = int(round(sec * fps))
    return f"{f // (fps * 3600):02d}:{f // (fps * 60) % 60:02d}:{f // fps % 60:02d}:{f % fps:02d}"


def write_edl(path, nome, segs, clips, fps):
    L = [f"TITLE: {nome}", "FCM: NON-DROP FRAME", ""]
    rec = 0.0
    for i, s in enumerate(segs, 1):
        L.append(f"{i:03d}  AX       V     C        {tc(s['start'], fps)} {tc(s['start'] + s['src'], fps)} "
                 f"{tc(rec, fps)} {tc(rec + s['ln'], fps)}")
        L.append(f"* FROM CLIP NAME: {clips[s['ci']]['name']}")
        rec += s["ln"]
    Path(path).write_text("\n".join(L) + "\n", encoding="utf-8")


def video_job(jid, a):
    job = JOBS[jid]
    work = None
    try:
        need_ffmpeg()
        style = str(a.get("estilo", "facil")).lower()
        if style not in ("agitado", "calmo", "facil"):
            style = "facil"
        W, H = FORMATS.get(a.get("formato", "vertical"), FORMATS["vertical"])
        fps = int(a.get("fps", 30))
        nome = safe_name(a.get("nome", "edicao"))
        outdir = WORKSPACE / "videos"
        out = outdir / f"{nome}.mp4"
        work = outdir / f"_tmp_{nome}"
        shutil.rmtree(work, ignore_errors=True)
        work.mkdir(parents=True)
        rnd = random.Random(str(a.get("semente") or nome))

        upd(job, 0.02, "analisando arquivos")
        clips = []
        for e in a.get("entradas", []):
            p = media_path(e)
            if p.suffix.lower() in IMG_EXT:
                clips.append(dict(path=p, img=True, dur=0, audio=False, name=p.name))
            else:
                i = probe(p)
                if i["video"] and i["dur"] > 0.3:
                    clips.append(dict(path=p, img=False, dur=i["dur"], audio=i["audio"], name=p.name))
        if not clips:
            raise RuntimeError("Nenhum vídeo ou imagem válido em 'entradas'.")
        music = media_path(a["musica"]) if a.get("musica") else None
        default_target = 30.0 if style == "agitado" else 1e9
        target = float(a.get("duracao_alvo") or default_target)
        vol = float(a.get("volume_musica") or {"agitado": 1.0, "calmo": 0.8}.get(style, 0.9))

        segs = plan_segments(clips, style, target, float(a.get("bpm", 128)), rnd)
        if not segs:
            raise RuntimeError("Não consegui montar cortes com esses arquivos.")
        steps = len(segs) + 3
        files = []
        for i, s in enumerate(segs):
            upd(job, (i + 1) / steps, f"cortando e aplicando efeitos {i + 1}/{len(segs)}")
            files.append(render_segment(s, clips, i, work, style, W, H, fps, music, rnd))
        upd(job, (len(segs) + 1) / steps, "juntando os cortes")
        if style == "calmo":
            join_xfade(files, work)
        else:
            join_concat(files, work)
        upd(job, (len(segs) + 2) / steps, "legendas, música e acabamento")
        T, sem_fonte = final_pass(work, out, style, W, H, music, vol, a.get("legendas") or [], fps)
        msg = f"Vídeo pronto: {out} ({T:.0f}s, estilo {style}, {W}x{H})"
        if a.get("exportar_edl", True):
            edl = outdir / f"{nome}.edl"
            write_edl(edl, nome, segs, clips, fps)
            msg += f"\nLista de cortes (DaVinci/Premiere): {edl}"
        if sem_fonte:
            msg += "\nAviso: não achei uma fonte para as legendas, então ficaram de fora."
        if a.get("abrir", True):
            try:
                open_default(out)
            except Exception:
                pass
        job.update(status="pronto", saida=msg, prog=1.0, msg="pronto")
    except Exception as e:
        job.update(status="erro", saida=f"{type(e).__name__}: {e}", msg="erro")
    finally:
        if work:
            shutil.rmtree(work, ignore_errors=True)


def act_video(a):
    need_ffmpeg()
    jid = uuid.uuid4().hex[:8]
    JOBS[jid] = {"status": "rodando", "prog": 0.0, "msg": "iniciando", "saida": ""}
    threading.Thread(target=video_job, args=(jid, a), daemon=True).start()
    return "Edição iniciada em segundo plano.", {"job": jid}


def act_ffmpeg(a):
    need_ffmpeg()
    args = [str(x) for x in a["args"]]
    r = subprocess.run([FFMPEG, "-y", "-hide_banner"] + args, cwd=str(WORKSPACE / "videos"),
                       capture_output=True, text=True, timeout=int(a.get("timeout", 1800)))
    if r.returncode != 0:
        raise RuntimeError("FFmpeg falhou: " + (r.stderr or "")[-700:])
    return "FFmpeg concluído. " + (r.stderr or "")[-300:]


WIN_APPS = {
    "capcut": [r"%LOCALAPPDATA%\CapCut\Apps\CapCut.exe", r"%LOCALAPPDATA%\CapCut\Apps\*\CapCut.exe"],
    "davinci": [r"C:\Program Files\Blackmagic Design\DaVinci Resolve\Resolve.exe"],
    "premiere": [r"C:\Program Files\Adobe\Adobe Premiere Pro *\Adobe Premiere Pro.exe"],
    "filmora": [r"C:\Program Files\Wondershare\*Filmora*\*Filmora*.exe"],
    "vegas": [r"C:\Program Files\VEGAS\VEGAS Pro *\vegas*.exe"],
    "shotcut": [r"C:\Program Files\Shotcut\shotcut.exe"],
    "kdenlive": [r"C:\Program Files\kdenlive\bin\kdenlive.exe"],
}
MAC_APPS = {"capcut": "CapCut", "davinci": "DaVinci Resolve", "premiere": "Adobe Premiere Pro",
            "filmora": "Wondershare Filmora", "shotcut": "Shotcut", "kdenlive": "kdenlive",
            "imovie": "iMovie", "finalcut": "Final Cut Pro"}
LIN_APPS = {"kdenlive": "kdenlive", "shotcut": "shotcut", "davinci": "resolve", "openshot": "openshot-qt"}
OPEN_WITH_FILE = {"shotcut", "kdenlive", "vegas"}


def act_abrir_programa(a):
    key = str(a.get("programa", "")).lower().replace(" ", "").replace("-", "")
    for alias, real in (("resolve", "davinci"), ("final", "finalcut"), ("cap", "capcut"), ("pr", "premiere")):
        if key.startswith(alias):
            key = real
            break
    arq = media_path(a["arquivo"]) if a.get("arquivo") else None
    com_arq = [str(arq)] if arq and key in OPEN_WITH_FILE else []
    if sys.platform.startswith("win"):
        exe = None
        for pat in WIN_APPS.get(key, []):
            hits = sorted(glob.glob(os.path.expandvars(pat)))
            if hits:
                exe = hits[-1]
                break
        if not exe:
            raise RuntimeError(f"Não encontrei o programa '{a.get('programa')}' instalado.")
        subprocess.Popen([exe] + com_arq)
    elif sys.platform == "darwin":
        app = MAC_APPS.get(key)
        if not app:
            raise RuntimeError(f"Não conheço o programa '{a.get('programa')}'.")
        subprocess.Popen(["open", "-a", app] + com_arq)
    else:
        exe = shutil.which(LIN_APPS.get(key, key))
        if not exe:
            raise RuntimeError(f"Não encontrei o programa '{a.get('programa')}'.")
        subprocess.Popen([exe] + com_arq)
    msg = f"Abri o {a.get('programa')}."
    if arq and not com_arq:
        reveal(arq)
        msg += f" Mostrei o arquivo {arq.name} na pasta: arraste para a linha do tempo do editor."
    return msg


INFO_ACTIONS = {"listar", "buscar_midia", "info_midia"}

ACTIONS = {
    "criar_pasta": act_criar_pasta,
    "criar_arquivo": act_criar_arquivo,
    "listar": act_listar,
    "abrir_url": act_abrir_url,
    "comando": act_comando,
    "blender": act_blender,
    "buscar_midia": act_buscar_midia,
    "info_midia": act_info_midia,
    "video": act_video,
    "ffmpeg": act_ffmpeg,
    "abrir_programa": act_abrir_programa,
}


# ---------------------------------------------------------------- HTTP
class Handler(BaseHTTPRequestHandler):
    def log_message(self, fmt, *args):
        print("  [http]", fmt % args)

    def _cors(self):
        self.send_header("Access-Control-Allow-Origin", "*")
        self.send_header("Access-Control-Allow-Headers", "Content-Type, X-Jarvis-Token")
        self.send_header("Access-Control-Allow-Methods", "GET, POST, OPTIONS")
        self.send_header("Access-Control-Allow-Private-Network", "true")

    def _json(self, code, obj):
        data = json.dumps(obj, ensure_ascii=False).encode("utf-8")
        self.send_response(code)
        self._cors()
        self.send_header("Content-Type", "application/json; charset=utf-8")
        self.send_header("Content-Length", str(len(data)))
        self.end_headers()
        self.wfile.write(data)

    def do_OPTIONS(self):
        self.send_response(204)
        self._cors()
        self.end_headers()

    def _auth(self):
        return self.headers.get("X-Jarvis-Token") == TOKEN

    def do_GET(self):
        if self.path.startswith("/ping"):
            if not self._auth():
                return self._json(401, {"ok": False, "erro": "Token inválido"})
            return self._json(200, {
                "ok": True,
                "pasta": str(WORKSPACE),
                "blender": bool(BLENDER),
                "sistema": platform.system() + " " + platform.release(),
                "usuario": getpass.getuser(),
                "maquina": platform.node(),
                "ffmpeg": bool(FFMPEG and FFPROBE),
            })
        if self.path.startswith("/job"):
            if not self._auth():
                return self._json(401, {"ok": False, "erro": "Token inválido"})
            jid = (parse_qs(urlparse(self.path).query).get("id") or [""])[0]
            return self._json(200, JOBS.get(jid, {"status": "erro", "saida": "Job não encontrado", "prog": 0}))
        self._json(404, {"ok": False})

    def do_POST(self):
        if not self._auth():
            return self._json(401, {"ok": False, "erro": "Token inválido"})
        if self.path != "/exec":
            return self._json(404, {"ok": False})
        try:
            n = int(self.headers.get("Content-Length", 0))
            body = json.loads(self.rfile.read(n) or b"{}")
        except Exception as e:
            return self._json(400, {"ok": False, "erro": str(e)})
        resultados = []
        for a in body.get("acoes", []):
            tipo = a.get("tipo")
            fn = ACTIONS.get(tipo)
            if not fn:
                resultados.append({"tipo": tipo, "ok": False, "saida": "Ação desconhecida"})
                continue
            try:
                print(f"  >> {tipo}")
                saida = fn(a)
                extra = {}
                if isinstance(saida, tuple):
                    saida, extra = saida
                resultados.append({"tipo": tipo, "ok": True, "saida": saida,
                                   "info": tipo in INFO_ACTIONS, **extra})
            except Exception as e:
                resultados.append({"tipo": tipo, "ok": False, "saida": f"{type(e).__name__}: {e}"})
        self._json(200, {"ok": True, "resultados": resultados})


def main():
    print("=" * 56)
    print("  J.A.R.V.I.S. - servidor local ativo")
    print("=" * 56)
    print(f"  Endereço : http://localhost:{PORT}")
    print(f"  Pasta    : {WORKSPACE}")
    print(f"  Blender  : {BLENDER or 'NÃO ENCONTRADO (defina BLENDER_PATH)'}")
    print(f"  FFmpeg   : {FFMPEG or 'NÃO ENCONTRADO (winget install ffmpeg)'}")
    print(f"  TOKEN    : {TOKEN}")
    print("  Cole o token nas Configurações do site. Ctrl+C para sair.")
    print("=" * 56)
    ThreadingHTTPServer(("127.0.0.1", PORT), Handler).serve_forever()


if __name__ == "__main__":
    try:
        main()
    except KeyboardInterrupt:
        print("\nJarvis desligado.")
        # J.A.R.V.I.S.

Assistente estilo Jarvis: você fala ou digita, ele responde em voz e executa coisas no seu PC (pastas, arquivos, comandos, Blender).

São duas partes:

| Parte | Onde roda | Arquivo |
|---|---|---|
| Interface (HUD animado, voz, modos) | GitHub Pages / navegador | `index.html` |
| Ponte com o computador | No seu PC | `jarvis_server.py` |

> Um site sozinho não pode mexer no seu computador (é uma proteção do navegador). Por isso existe o `jarvis_server.py`: ele roda na sua máquina e executa o que o Jarvis pede.

## Publicar no GitHub Pages
1. Crie um repositório e suba `index.html`, `jarvis_server.py` e este README.
2. **Settings → Pages → Deploy from a branch → main / (root)**.
3. Abra o link `https://SEU-USUARIO.github.io/NOME-DO-REPO/` no **Chrome ou Edge** (voz funciona melhor neles).

> Não suba o arquivo `jarvis_token.txt` (ele é criado ao rodar o servidor). Adicione no `.gitignore`.

## Usar
1. Instale o [Python 3](https://www.python.org/downloads/) e, se quiser 3D, o [Blender](https://www.blender.org/download/).
2. No terminal: `python jarvis_server.py`
   Ele mostra um **TOKEN** e cria a pasta `~/Jarvis` (é onde ele cria tudo).
3. No site, clique em **⚙ CONFIG** e preencha:
   - IA (Claude, ChatGPT ou Gemini) e a **chave de API**
   - o **token** do servidor
4. Clique no 🎤 e fale, ou digite. Marque "palavra Jarvis" para ele só responder quando você disser "Jarvis, ...".

## Exemplos
- "Jarvis, cria uma pasta chamada Projetos com uma subpasta Jogo"
- "Jarvis, cria no Blender uma cidade futurista com prédios azuis"
- "Jarvis, abre o YouTube"
- "Jarvis, roda `dir` e me diz o que tem na pasta"

## Edição de vídeo
Instale o **FFmpeg** (Windows: `winget install ffmpeg`, Mac: `brew install ffmpeg`) e reinicie o servidor.

O Jarvis edita de verdade: ele procura seus vídeos, fotos e músicas, corta, aplica efeitos e entrega um `.mp4` pronto em `~/Jarvis/videos`, mais um arquivo `.edl` com a lista de cortes (DaVinci Resolve e Premiere importam).

| Estilo | O que ele faz |
|---|---|
| **agitado** | cortes curtos no ritmo da música (diga o BPM), zoom na batida, tremida, glitch, flash, cores fortes, acelerações |
| **calmo** | takes longos, zoom lento, transições suaves, cor suave, música baixa |
| **fácil** | cortes simples em sequência, sem efeitos pesados |

Exemplos:
- "Jarvis, faz uma edição agitada com meus vídeos mais recentes, 20 segundos, vertical"
- "Jarvis, monta um vídeo calmo com essas fotos e a música relaxante.mp3, com a legenda 'Verão 2026'"
- "Jarvis, abre o CapCut com o vídeo que você fez"

**CapCut e outros editores:** o CapCut não tem como ser controlado por programas externos. Por isso o Jarvis faz a edição com o FFmpeg e depois **abre o CapCut e mostra o arquivo na pasta** para você arrastar pra linha do tempo e continuar lá. DaVinci Resolve e Premiere ainda aceitam o `.edl` pelo menu de importar timeline.

## Modos
Conversa · Comandos do PC · Blender 3D · Arquivos · Edição de vídeo · Autônomo

## Segurança
- A chave de API fica só no seu navegador.
- O servidor só aceita pedidos com o token e só escuta em `localhost`.
- Arquivos e pastas só são criados dentro de `~/Jarvis`.
- Comandos de terminal **sempre pedem sua confirmação**, a menos que você ative o modo sem confirmação.
- Se o Blender não for achado, defina a variável `BLENDER_PATH` com o caminho do executável.
