<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>🎮 GameAlerts — Jogos Pagos Grátis</title>
<script src="https://cdn.tailwindcss.com"></script>
<style>
  body {
    background: linear-gradient(135deg, #0f172a, #1e293b);
    color: white;
    font-family: "Poppins", sans-serif;
    min-height: 100vh;
    margin: 0;
    padding: 0;
  }

  header { text-align: center; padding: 30px 0; }
  h1 { font-size: 2.2rem; color: #00aaff; font-weight: 700; }
  .subtitle { color: #ccc; margin-top: 5px; }

  nav {
    text-align: center;
    margin-top: 10px;
  }
  nav button {
    background: #1e40af;
    color: white;
    border: none;
    padding: 10px 15px;
    margin: 5px;
    border-radius: 8px;
    cursor: pointer;
    font-weight: bold;
    transition: 0.3s;
  }
  nav button.active { background: #00aaff; }
  nav button:hover { background: #2563eb; }

  .games-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(260px, 1fr));
    gap: 20px;
    max-width: 1000px;
    margin: 30px auto;
    padding: 0 20px;
  }

  .game-card {
    background: #1c1c1c;
    border-radius: 12px;
    overflow: hidden;
    box-shadow: 0 4px 10px rgba(0,0,0,0.4);
    transition: transform 0.3s;
  }
  .game-card:hover { transform: translateY(-6px); }

  .game-image { width: 100%; height: 160px; object-fit: cover; background: #333; }
  .game-info { padding: 15px; }
  .price { color: #00ff99; font-weight: bold; }
  .original { text-decoration: line-through; color: #999; }

  .btn {
    background: #0074e4;
    border: none;
    padding: 10px 15px;
    border-radius: 8px;
    color: white;
    cursor: pointer;
    transition: 0.3s;
    font-weight: bold;
    display: block;
    width: 100%;
    text-align: center;
  }
  .btn:hover { background: #005bb5; }

  footer { text-align: center; margin-top: 30px; color: #aaa; padding-bottom: 30px; }
  .loading { text-align: center; font-size: 1.2rem; color: #bbb; margin-top: 40px; }

  .notification {
    position: fixed; top: 20px; right: 20px;
    background: #222; color: white; padding: 15px 20px;
    border-left: 4px solid #00ff99; border-radius: 6px;
    box-shadow: 0 4px 10px rgba(0,0,0,0.4);
    transform: translateX(300px); transition: transform 0.5s ease;
    z-index: 9999;
  }
  .notification.show { transform: translateX(0); }

  .clock {
    text-align: center;
    font-size: 1.2rem;
    color: #00ffcc;
    margin-top: -10px;
  }
</style>
</head>
<body>
  <header>
    <h1>🎁 GameAlerts</h1>
    <p class="subtitle">Jogos pagos que estão ou estarão GRÁTIS na Epic Games Store</p>
    <div class="clock" id="clock">🕒 Carregando hora...</div>
  </header>

  <nav>
    <button id="tabAtivos" class="active">🎮 Grátis Agora</button>
    <button id="tabFuturos">⏳ Em Breve</button>
  </nav>

  <div id="status" class="text-center mb-4 text-sm text-gray-400">Carregando lista de jogos...</div>
  <div class="games-grid" id="gamesGrid"></div>

  <div class="text-center mt-6">
    <button class="btn" id="refreshBtn">🔄 Atualizar Agora</button>
  </div>

  <footer>
    <p>💬 Reportar bug: <a href="mailto:reisjuvenira468@gmail.com" style="color:#00aaff;">reisjuvenira468@gmail.com</a></p>
    <p>Dados da <a href="https://store.epicgames.com/pt-BR/free-games" style="color:#00aaff;" target="_blank">Epic Games Store</a></p>
  </footer>

  <div class="notification" id="notification">Novo jogo gratuito detectado!</div>

  <script>
    const apiURL = "https://corsproxy.io/?" + encodeURIComponent("https://store-site-backend-static.ak.epicgames.com/freeGamesPromotions?locale=pt-BR");
    const gamesGrid = document.getElementById("gamesGrid");
    const statusText = document.getElementById("status");
    const refreshBtn = document.getElementById("refreshBtn");
    const notification = document.getElementById("notification");
    const tabAtivos = document.getElementById("tabAtivos");
    const tabFuturos = document.getElementById("tabFuturos");
    const clock = document.getElementById("clock");

    let lastFreeTitles = [];
    let allGames = { ativos: [], futuros: [] };

    function showNotification(msg) {
      notification.textContent = msg;
      notification.classList.add("show");
      setTimeout(() => notification.classList.remove("show"), 5000);
    }

    function updateClock() {
      const now = new Date().toLocaleTimeString("pt-BR", { timeZone: "America/Sao_Paulo" });
      clock.textContent = "🕒 Horário de Brasília: " + now;
    }
    setInterval(updateClock, 1000);
    updateClock();

    async function fetchFreeGames() {
      statusText.textContent = "🔍 Atualizando jogos gratuitos...";
      try {
        const response = await fetch(apiURL);
        if (!response.ok) throw new Error("Erro ao acessar API da Epic Games");
        const data = await response.json();
        const elements = data?.data?.Catalog?.searchStore?.elements || [];

        const ativos = elements.filter(game => game.price?.totalPrice?.discountPrice === 0 && game.promotions?.promotionalOffers?.length > 0);
        const futuros = elements.filter(game => game.promotions?.upcomingPromotionalOffers?.length > 0);

        allGames = { ativos, futuros };
        renderGames(ativos);

        const currentTitles = ativos.map(g => g.title);
        const newTitles = currentTitles.filter(t => !lastFreeTitles.includes(t));
        if (lastFreeTitles.length > 0 && newTitles.length > 0) {
          showNotification("🎉 Novo jogo gratuito: " + newTitles.join(", "));
          setTimeout(() => location.reload(), 3000);
        }
        lastFreeTitles = currentTitles;

        const time = new Date().toLocaleTimeString("pt-BR");
        statusText.textContent = `✅ Atualizado às ${time}`;
      } catch (error) {
        console.error("Erro:", error);
        statusText.textContent = "❌ Erro ao carregar jogos da Epic Games.";
      }
    }

    function renderGames(games) {
      gamesGrid.innerHTML = "";
      if (games.length === 0) {
        gamesGrid.innerHTML = "<p class='loading'>Nenhum jogo gratuito disponível 😔</p>";
        return;
      }

      games.forEach(game => {
        const image = game.keyImages?.find(img => img.type === "OfferImageWide")?.url ||
                      game.keyImages?.[0]?.url || "https://via.placeholder.com/600x400?text=Sem+Capa";
        const originalPrice = (game.price?.totalPrice?.originalPrice / 100).toFixed(2);
        const promo = game.promotions?.promotionalOffers?.[0]?.promotionalOffers?.[0] ||
                      game.promotions?.upcomingPromotionalOffers?.[0]?.promotionalOffers?.[0];

        const endTime = promo?.endDate ? new Date(promo.endDate) : null;
        const startTime = promo?.startDate ? new Date(promo.startDate) : null;

        const now = new Date();
        let countdownText = "";

        if (startTime && startTime > now) {
          const diff = Math.floor((startTime - now) / (1000 * 60 * 60 * 24));
          countdownText = `⏳ Disponível em ${diff} dia(s)`;
        } else if (endTime) {
          const diff = Math.floor((endTime - now) / 1000);
          countdownText = diff > 0 ? `⌛ Termina em ${formatCountdown(diff)}` : "❌ Expirado";
        }

        let pageSlug = game.catalogNs?.mappings?.[0]?.pageSlug || game.productSlug || "";
        if (!pageSlug) pageSlug = game.urlSlug || "";
        const gameUrl = `https://store.epicgames.com/pt-BR/p/${pageSlug.replace(/^p\//, "")}`;

        const card = document.createElement("div");
        card.className = "game-card";
        card.innerHTML = `
          <img src="${image}" alt="${game.title}" class="game-image">
          <div class="game-info">
            <h3>${game.title}</h3>
            <p class="original">De R$${originalPrice}</p>
            <p class="price">💥 GRÁTIS!</p>
            <p><small>${countdownText}</small></p>
            <a href="${gameUrl}" target="_blank" rel="noopener">
              <button class="btn mt-2">Resgatar</button>
            </a>
          </div>`;
        gamesGrid.appendChild(card);
      });
    }

    function formatCountdown(seconds) {
      const d = Math.floor(seconds / 86400);
      const h = Math.floor((seconds % 86400) / 3600);
      const m = Math.floor((seconds % 3600) / 60);
      return `${d}d ${h}h ${m}m`;
    }

    refreshBtn.addEventListener("click", fetchFreeGames);
    tabAtivos.addEventListener("click", () => {
      tabAtivos.classList.add("active");
      tabFuturos.classList.remove("active");
      renderGames(allGames.ativos);
    });
    tabFuturos.addEventListener("click", () => {
      tabFuturos.classList.add("active");
      tabAtivos.classList.remove("active");
      renderGames(allGames.futuros);
    });

    fetchFreeGames();
    setInterval(fetchFreeGames, 600000);
  </script>
</body>
</html>