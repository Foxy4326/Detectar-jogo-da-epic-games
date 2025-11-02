<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>🎮 GameAlerts — Jogos Pagos Grátis</title>
<script src="https://cdn.tailwindcss.com"></script>
<link rel="manifest" href="manifest.json">
<style>
  body { background: linear-gradient(135deg,#0f172a,#1e293b);color:white;font-family:"Poppins",sans-serif;min-height:100vh;margin:0;padding:0; }
  header { text-align:center;padding:20px 0; }
  h1 { font-size:2rem;color:#00aaff;font-weight:700; }
  .subtitle { color:#ccc;margin-top:5px; }
  #brTime { font-weight:bold;margin-top:5px;color:#ffcc00; }
  nav { text-align:center;margin-top:10px; }
  nav button { background:#1e40af;color:white;border:none;padding:10px 15px;margin:5px;border-radius:8px;cursor:pointer;font-weight:bold;transition:0.3s; }
  nav button.active { background:#00aaff; }
  nav button:hover { background:#2563eb; }
  .games-grid { display:grid;grid-template-columns:repeat(auto-fit,minmax(260px,1fr));gap:20px;max-width:1000px;margin:20px auto;padding:0 20px; }
  .game-card { background:#1c1c1c;border-radius:12px;overflow:hidden;box-shadow:0 4px 10px rgba(0,0,0,0.4);transition:transform 0.3s;position:relative; }
  .game-card:hover { transform:translateY(-6px); }
  .game-image { width:100%;height:160px;object-fit:cover;background:#333; }
  .game-info { padding:15px; }
  .price { color:#00ff99;font-weight:bold; }
  .original { text-decoration:line-through;color:#999; }
  .btn { background:#0074e4;border:none;padding:10px 15px;border-radius:8px;color:white;cursor:pointer;transition:0.3s;font-weight:bold;display:block;width:100%;text-align:center; }
  .btn:hover { background:#005bb5; }
  .bug-btn { background:#dc2626;margin-top:10px; }
  .bug-btn:hover { background:#b91c1c; }
  footer { text-align:center;margin-top:30px;color:#aaa;padding-bottom:30px; }
  .notification { position:fixed;top:20px;right:20px;background:#222;color:white;padding:15px 20px;border-left:4px solid #00ff99;border-radius:6px;box-shadow:0 4px 10px rgba(0,0,0,0.4);transform:translateX(300px);transition:transform 0.5s ease;z-index:9999; }
  .notification.show { transform:translateX(0); }
  .countdown { font-weight:bold;margin-top:5px;color:#ffcc00; }
  #installPrompt { display:none;position:fixed;bottom:20px;left:50%;transform:translateX(-50%);background:#1e40af;color:white;padding:10px 20px;border-radius:8px;cursor:pointer;font-weight:bold; }
</style>
</head>
<body>
  <header>
    <h1>🎁 GameAlerts</h1>
    <p class="subtitle">Jogos pagos que estão ou estarão GRÁTIS na Epic Games Store</p>
    <div id="brTime">🕒 Carregando hora...</div>
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
    <p>Dados em tempo real da 
      <a href="https://store.epicgames.com/pt-BR/free-games" style="color:#00aaff;" target="_blank">Epic Games Store</a>
    </p>
  </footer>

  <div class="notification" id="notification"></div>
  <div id="installPrompt">📲 Instalar App GameAlerts</div>

  <script>
    const apiList = [
      "https://api.allorigins.win/raw?url=https://store-site-backend-static.ak.epicgames.com/freeGamesPromotions?locale=pt-BR",
      "https://api.codetabs.com/v1/proxy?quest=https://store-site-backend-static.ak.epicgames.com/freeGamesPromotions?locale=pt-BR"
    ];
    const gamesGrid = document.getElementById("gamesGrid");
    const statusText = document.getElementById("status");
    const refreshBtn = document.getElementById("refreshBtn");
    const reportBtn = document.getElementById("reportBtn");
    const notification = document.getElementById("notification");
    const tabAtivos = document.getElementById("tabAtivos");
    const tabFuturos = document.getElementById("tabFuturos");
    const brTimeEl = document.getElementById("brTime");
    const installPrompt = document.getElementById("installPrompt");
    let deferredPrompt;
    let lastFreeTitles = [];
    let allGames = { ativos: [], futuros: [] };
    let countdowns = [];

    // Hora Brasil
    function updateBrazilTime() {
      const now = new Date();
      brTimeEl.textContent = "🕒 Hora Brasil: " + now.toLocaleString("pt-BR", { timeZone: "America/Sao_Paulo" });
    }
    setInterval(updateBrazilTime, 1000);
    updateBrazilTime();

    // Notificação
    function showNotification(msg) {
      notification.textContent = msg;
      notification.classList.add("show");
      setTimeout(() => notification.classList.remove("show"), 5000);
    }

    // Busca API com fallback
    async function fetchData() {
      for (let url of apiList) {
        try {
          const r = await fetch(url);
          if (!r.ok) continue;
          return await r.json();
        } catch (e) { console.warn("Erro com API:", url); }
      }
      return null;
    }

    async function fetchGames() {
      statusText.textContent = "🔍 Buscando promoções...";
      const data = await fetchData();
      if (!data) {
        gamesGrid.innerHTML = "<p class='loading'>⚠️ Não foi possível carregar os jogos no momento.</p>";
        statusText.textContent = "❌ Falha ao buscar dados.";
        return;
      }
      const elements = data?.data?.Catalog?.searchStore?.elements || [];
      const ativos = elements.filter(g => g.price?.totalPrice?.discountPrice === 0 && g.promotions?.promotionalOffers?.length);
      const futuros = elements.filter(g => g.promotions?.upcomingPromotionalOffers?.length);

      allGames = { ativos, futuros };
      renderGames(ativos, true);
      const novos = ativos.map(g => g.title).filter(t => !lastFreeTitles.includes(t));
      if (novos.length) showNotification("🎉 Novos jogos gratuitos: " + novos.join(", "));
      lastFreeTitles = ativos.map(g => g.title);
      statusText.textContent = "✅ Atualizado: " + new Date().toLocaleTimeString("pt-BR");
    }

    function renderGames(games, ativos) {
      gamesGrid.innerHTML = "";
      countdowns.forEach(clearInterval);
      countdowns = [];
      if (!games.length) {
        gamesGrid.innerHTML = "<p class='loading'>Nenhum jogo disponível 😔</p>";
        return;
      }

      games.forEach(game => {
        const img = game.keyImages?.find(i => i.type === "OfferImageWide")?.url || game.keyImages?.[0]?.url || "https://via.placeholder.com/600x400?text=Sem+Capa";
        const promo = ativos ? game.promotions.promotionalOffers[0].promotionalOffers[0] : game.promotions.upcomingPromotionalOffers[0].promotionalOffers[0];
        const start = promo?.startDate ? new Date(promo.startDate) : null;
        const end = promo?.endDate ? new Date(promo.endDate) : null;
        const slug = (game.catalogNs?.mappings?.[0]?.pageSlug || game.productSlug || "").replace(/^\/+/, "");
        const url = "https://store.epicgames.com/p/" + slug;

        const div = document.createElement("div");
        div.className = "game-card";
        div.innerHTML = `
          <img src="${img}" alt="${game.title}" class="game-image">
          <div class="game-info">
            <h3>${game.title}</h3>
            <p class="price">💥 GRÁTIS!</p>
            <div class="countdown"></div>
            <a href="${url}" target="_blank"><button class="btn mt-2">Resgatar</button></a>
          </div>`;
        gamesGrid.appendChild(div);

        const countdown = div.querySelector(".countdown");
        const i = setInterval(() => {
          const now = new Date();
          if (ativos && end) {
            const diff = end - now;
            countdown.textContent = diff <= 0 ? "⏰ Promoção Encerrada" : formatDiff(diff);
          } else if (!ativos && start) {
            const diff = start - now;
            countdown.textContent = diff <= 0 ? "🎮 Disponível Agora!" : `⏳ Em ${Math.ceil(diff/86400000)} dia(s)`;
          }
        }, 1000);
        countdowns.push(i);
      });
    }

    function formatDiff(ms) {
      const s = Math.floor(ms/1000), d = Math.floor(s/86400), h = Math.floor(s%86400/3600), m = Math.floor(s%3600/60);
      return `${d}d ${h}h ${m}m`;
    }

    tabAtivos.onclick = () => { tabAtivos.classList.add("active"); tabFuturos.classList.remove("active"); renderGames(allGames.ativos,true); };
    tabFuturos.onclick = () => { tabFuturos.classList.add("active"); tabAtivos.classList.remove("active"); renderGames(allGames.futuros,false); };
    refreshBtn.onclick = fetchGames;
    reportBtn.onclick = () => { window.location.href = "mailto:reisjuvenira468@gmail.com?subject=🐞 Reporte de Bug - GameAlerts"; };

    // PWA instalação
    window.addEventListener("beforeinstallprompt", e => {
      e.preventDefault();
      deferredPrompt = e;
      installPrompt.style.display = "block";
    });
    installPrompt.onclick = async () => {
      installPrompt.style.display = "none";
      deferredPrompt.prompt();
      await deferredPrompt.userChoice;
      deferredPrompt = null;
    };

    fetchGames();
    setInterval(fetchGames, 600000);
  </script>
</body>
</html>