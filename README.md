<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>🎮 GameAlerts — Jogos Pagos Grátis</title>
<script src="https://cdn.tailwindcss.com"></script>

<link rel="manifest" href="manifest.json">
<meta name="theme-color" content="#00aaff">

<style>
body { background: linear-gradient(135deg,#0f172a,#1e293b); color:white; font-family:"Poppins",sans-serif; min-height:100vh; margin:0; padding:0;}
header{text-align:center; padding:20px 0;}
h1{ font-size:2.2rem; color:#00aaff; font-weight:700;}
.subtitle{ color:#ccc; margin-top:5px;}
.time{ font-weight:bold; margin-top:5px; color:#ffcc00;}
nav{text-align:center; margin:10px 0;}
nav button{ background:#1e40af; color:white; border:none; padding:10px 15px; margin:5px; border-radius:8px; cursor:pointer; font-weight:bold; transition:0.3s;}
nav button.active{ background:#00aaff;}
nav button:hover{ background:#2563eb;}
.games-grid{ display:grid; grid-template-columns:repeat(auto-fit,minmax(260px,1fr)); gap:20px; max-width:1000px; margin:30px auto; padding:0 20px;}
.game-card{ background:#1c1c1c; border-radius:12px; overflow:hidden; box-shadow:0 4px 10px rgba(0,0,0,0.4); transition: transform 0.3s; position:relative;}
.game-card:hover{ transform:translateY(-6px);}
.game-image{ width:100%; height:160px; object-fit:cover; background:#333;}
.game-info{ padding:15px;}
.price{ color:#00ff99; font-weight:bold;}
.original{ text-decoration:line-through; color:#999;}
.btn{ background:#0074e4; border:none; padding:10px 15px; border-radius:8px; color:white; cursor:pointer; transition:0.3s; font-weight:bold; display:block; width:100%; text-align:center;}
.btn:hover{ background:#005bb5;}
.bug-btn{ background:#dc2626; margin-top:10px;}
.bug-btn:hover{ background:#b91c1c;}
.install-btn{ background:#00aa00; margin-top:10px;}
.install-btn:hover{ background:#008800;}
footer{text-align:center; margin-top:30px; color:#aaa; padding-bottom:30px;}
.loading{text-align:center; font-size:1.2rem; color:#bbb; margin-top:40px;}
.notification{ position:fixed; top:20px; right:20px; background:#222; color:white; padding:15px 20px; border-left:4px solid #00ff99; border-radius:6px; box-shadow:0 4px 10px rgba(0,0,0,0.4); transform:translateX(300px); transition:transform 0.5s ease; z-index:9999;}
.notification.show{ transform:translateX(0);}
.countdown{ font-weight:bold; margin-top:5px; color:#ffcc00;}

/* Banner app */
#downloadBanner{ position:fixed; top:0; left:0; width:100%; background:#0074e4; color:white; padding:15px; text-align:center; font-weight:bold; display:flex; justify-content:center; align-items:center; z-index:9999;}
#downloadBanner button{ margin-left:10px; background:#00aa00; border:none; padding:8px 12px; border-radius:6px; cursor:pointer; font-weight:bold;}
#downloadBanner button:hover{ background:#008800;}
#downloadBanner #closeBannerBtn{ background:#dc2626; margin-left:5px; }
</style>
</head>
<body>

<!-- Banner app -->
<div id="downloadBanner">
  <span>📲 Baixe o App GameAlerts para acesso rápido e notificações!</span>
  <button id="installBannerBtn">Instalar</button>
  <button id="closeBannerBtn">✖</button>
</div>

<header>
  <h1>🎁 GameAlerts</h1>
  <p class="subtitle">Jogos pagos que estão ou estarão GRÁTIS na Epic Games Store</p>
  <div class="time" id="brTime">🕒 Carregando horário...</div>
</header>

<nav>
  <button id="tabAtivos" class="active">🎮 Grátis Agora</button>
  <button id="tabFuturos">⏳ Em Breve</button>
</nav>

<div id="status" class="text-center mb-4 text-sm text-gray-400">Carregando lista de jogos...</div>
<div class="games-grid" id="gamesGrid"></div>

<div class="text-center mt-6">
  <button class="btn" id="refreshBtn">🔄 Atualizar Agora</button>
  <button class="btn bug-btn" id="reportBtn">🐞 Reportar Bug</button>
</div>

<footer>
  <p>Dados em tempo real da <a href="https://store.epicgames.com/pt-BR/free-games" style="color:#00aaff;" target="_blank">Epic Games Store</a></p>
</footer>

<div class="notification" id="notification">Novo jogo gratuito detectado!</div>

<script>
// Service Worker PWA
if('serviceWorker' in navigator){navigator.serviceWorker.register('./sw.js').then(()=>console.log('Service Worker registrado'));}

// Horário Brasil
function updateBRTime(){
  const now=new Date();
  const brTime=now.toLocaleString("pt-BR",{timeZone:"America/Sao_Paulo"});
  document.getElementById("brTime").textContent="🕒 Horário Brasil: "+brTime;
}
setInterval(updateBRTime,1000);
updateBRTime();

// PWA instalar app
let deferredPrompt;
const installBannerBtn=document.getElementById("installBannerBtn");
const closeBannerBtn=document.getElementById("closeBannerBtn");
const downloadBanner=document.getElementById("downloadBanner");
window.addEventListener('beforeinstallprompt',e=>{
  e.preventDefault();
  deferredPrompt=e;
  downloadBanner.style.display='flex';
});
installBannerBtn.addEventListener('click',async()=>{
  if(deferredPrompt){
    deferredPrompt.prompt();
    await deferredPrompt.userChoice;
    deferredPrompt=null;
    downloadBanner.style.display='none';
  }
});
closeBannerBtn.addEventListener('click',()=>{downloadBanner.style.display='none';});

// Sistema jogos
const apiURL="https://corsproxy.io/?"+encodeURIComponent("https://store-site-backend-static.ak.epicgames.com/freeGamesPromotions?locale=pt-BR");
const gamesGrid=document.getElementById("gamesGrid");
const statusText=document.getElementById("status");
const refreshBtn=document.getElementById("refreshBtn");
const reportBtn=document.getElementById("reportBtn");
const notification=document.getElementById("notification");
const tabAtivos=document.getElementById("tabAtivos");
const tabFuturos=document.getElementById("tabFuturos");
let lastFreeTitles=[];
let allGames={ativos:[],futuros:[]};
let countdownIntervals=[];

function showNotification(msg){notification.textContent=msg; notification.classList.add("show"); setTimeout(()=>notification.classList.remove("show"),5000);}

async function fetchFreeGames(){
  statusText.textContent="🔍 Atualizando jogos gratuitos...";
  try{
    const response=await fetch(apiURL);
    if(!response.ok) throw new Error("Erro ao acessar API da Epic Games");
    const data=await response.json();
    const elements=data?.data?.Catalog?.searchStore?.elements||[];

    const ativos=elements.filter(g=>g.price?.totalPrice?.discountPrice===0 && g.promotions?.promotionalOffers?.length>0);
    const futuros=elements.filter(g=>g.price?.totalPrice?.discountPrice===0 && g.promotions?.upcomingPromotionalOffers?.length>0);
    allGames={ativos,futuros};
    renderGames(ativos,true);

    const currentTitles=ativos.map(g=>g.title);
    const newTitles=currentTitles.filter(t=>!lastFreeTitles.includes(t));
    if(lastFreeTitles.length>0 && newTitles.length>0){ showNotification("🎉 Novo jogo gratuito: "+newTitles.join(", ")); setTimeout(()=>location.reload(),3000);}
    lastFreeTitles=currentTitles;
    statusText.textContent=`✅ Atualizado às ${new Date().toLocaleTimeString("pt-BR")}`;
  }catch(e){console.error(e); statusText.textContent="❌ Erro ao carregar jogos da Epic Games.";}
}

function renderGames(games,isAtivos){
  gamesGrid.innerHTML=""; countdownIntervals.forEach(i=>clearInterval(i)); countdownIntervals=[];
  if(games.length===0){ gamesGrid.innerHTML="<p class='loading'>Nenhum jogo gratuito disponível 😔</p>"; return; }

  games.forEach(game=>{
    const image=game.keyImages?.find(img=>img.type==="OfferImageWide")?.url || game.keyImages?.[0]?.url || "https://via.placeholder.com/600x400?text=Sem+Capa";
    const originalPrice=(game.price?.totalPrice?.originalPrice/100).toFixed(2);
    const promo=isAtivos? game.promotions?.promotionalOffers?.[0]?.promotionalOffers?.[0] : game.promotions?.upcomingPromotionalOffers?.[0]?.promotionalOffers?.[0];
    const startTime=promo?.startDate?new Date(promo.startDate):null;
    const endTime=promo?.endDate?new Date(promo.endDate):null;
    const pageSlug=game.catalogNs?.mappings?.[0]?.pageSlug || game.productSlug || "";
    const gameUrl="https://store.epicgames.com/"+(pageSlug.startsWith("p/")?pageSlug:("p/"+pageSlug)).replace(/^\/+/,"");

    const card=document.createElement("div");
    card.className="game-card";
    card.innerHTML=`<img src="${image}" alt="${game.title}" class="game-image">
      <div class="game-info">
        <h3>${game.title}</h3>
        <p class="original">De R$${originalPrice}</p>
        <p class="price">💥 GRÁTIS!</p>
        <div class="countdown"></div>
        <a href="${gameUrl}" target="_blank"><button class="btn mt-2">Resgatar</button></a>
      </div>`;
    gamesGrid.appendChild(card);

    const countdownEl=card.querySelector(".countdown");
    const interval=setInterval(()=>{
      const now=new Date();
      let diff;
      if(isAtivos){
        diff=endTime-now;
        if(diff<=0){ countdownEl.textContent="⏰ Promoção terminou!"; clearInterval(interval); window.location.href="promocao-acabou.html";}
        else countdownEl.textContent=formatDiff(diff);
      }else{
        diff=startTime-now;
        if(diff<=0){ countdownEl.textContent="🎮 Promoção disponível agora!"; clearInterval(interval); fetchFreeGames();}
        else{ const days=Math.floor(diff/(1000*60*60*24)); countdownEl.textContent=`⏳ Começa em: ${days} dia(s)`;}
      }
    },1000);
    countdownIntervals.push(interval);
  });
}

function formatDiff(ms){
  const totalSec=Math.floor(ms/1000);
  const d=Math.floor(totalSec/86400);
  const h=Math.floor((totalSec%86400)/3600);
  const m=Math.floor((totalSec%3600)/60);
  const s=totalSec%60;
  return `${d}d ${h}h ${m}m ${s}s`;
}

tabAtivos.addEventListener("click",()=>{ tabAtivos.classList.add("active"); tabFuturos.classList.remove("active"); renderGames(allGames.ativos,true);});
tabFuturos.addEventListener("click",()=>{ tabFuturos.classList.add("active"); tabAtivos.classList.remove("active"); renderGames(allGames.futuros,false);});

refreshBtn.addEventListener("click",fetchFreeGames);

reportBtn.addEventListener("click",()=>{
  const subject=encodeURIComponent("🐞 Reporte de Bug - GameAlerts");
  const body=encodeURIComponent("Descreva o bug que você encontrou:\n\n");
  window.location.href=`mailto:reisjuvenira468@gmail.com?subject=${subject}&body=${body}`;
});

fetchFreeGames();
setInterval(fetchFreeGames,600000);
</script>
</body>
</html>
