<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>🎮 GameAlerts - Jogos Grátis da Epic Games</title>
<script src="https://cdn.tailwindcss.com"></script>
<style>
  body {
    background: #121212;
    color: white;
    font-family: "Segoe UI", sans-serif;
    transition: background 0.8s;
  }

  header {
    text-align: center;
    padding: 30px 0;
    background: linear-gradient(135deg, #1e1e1e, #2c2c2c);
    border-radius: 12px;
    box-shadow: 0 4px 16px rgba(0,0,0,0.4);
    margin-bottom: 30px;
  }

  h1 { font-size: 2.2rem; color: #00aaff; }
  .subtitle { color: #ccc; }

  .games-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(260px, 1fr));
    gap: 20px;
  }

  .game-card {
    background: #1c1c1c;
    border-radius: 12px;
    overflow: hidden;
    box-shadow: 0 4px 10px rgba(0,0,0,0.4);
    transition: transform 0.3s;
  }

  .game-card:hover {
    transform: translateY(-6px);
  }

  .game-image {
    width: 100%;
    height: 160px;
    object-fit: cover;
  }

  .game-info {
    padding: 15px;
  }

  .price {
    color: #00ff99;
    font-weight: bold;
  }

  .original {
    text-decoration: line-through;
    color: #999;
  }

  .btn {
    background: #0074e4;
    border: none;
    padding: 10px 15px;
    border-radius: 8px;
    color: white;
    cursor: pointer;
    transition: 0.3s;
    font-weight: bold;
  }

  .btn:hover { background: #005bb5; }

  footer {
    text-align: center;
    margin-top: 30px;
    color: #aaa;
  }

  .loading {
    text-align: center;
    font-size: 1.2rem;
    color: #bbb;
    margin-top: 40px;
  }

  .notification {
    position: fixed;
    top: 20px;
    right: 20px;
    background: #222;
    color: white;
    padding: 15px 20px;
    border-left: 4px solid #00ff99;
    border-radius: 6px;
    box-shadow: 0 4px 10px rgba(0,0,0,0.4);
    transform: translateX(300px);
    transition: transform 0.5s ease;
    z-index: 9999;
  }
  .notification.show {
    transform: translateX(0);
  }
</style>
</head>
<body>
  <div class="container mx-auto px-4">
    <header>
      <h1>🎁 GameAlerts</h1>
      <p class="subtitle">Jogos pagos que estão GRÁTIS agora na Epic Games Store</p>
    </header>

    <div id="status" class="text-center mb-4 text-sm text-gray-400">Carregando lista de jogos...</div>
    <div class="games-grid" id="gamesGrid"></div>

    <div class="text-center mt-6">
      <button class="btn" id="refreshBtn">🔄 Atualizar Agora</button>
    </div>

    <footer>
      <p>Dados em tempo real da <a href="https://store.epicgames.com/pt-BR/free-games" style="color:#00aaff;" target="_blank">Epic Games Store</a></p>
    </footer>
  </div>

  <div class="notification" id="notification">Novo jogo gratuito detectado!</div>

  <script>
    const apiURL = "https://store-site-backend-static.ak.epicgames.com/freeGamesPromotions?locale=pt-BR";
    const gamesGrid = document.getElementById("gamesGrid");
    const statusText = document.getElementById("status");
    const refreshBtn = document.getElementById("refreshBtn");
    const notification = document.getElementById("notification");
    let lastFreeTitles = [];

    // Exibir notificação popup
    function showNotification(msg) {
      notification.textContent = msg;
      notification.classList.add("show");
      setTimeout(() => notification.classList.remove("show"), 5000);
    }

    // Buscar jogos grátis em tempo real
    async function fetchFreeGames() {
      statusText.textContent = "🔍 Atualizando jogos gratuitos...";
      try {
        const response = await fetch(apiURL);
        const data = await response.json();
        const games = data.data.Catalog.searchStore.elements;

        // Filtrar apenas jogos com promoções gratuitas ativas
        const freeGames = games.filter(g => {
          const promo = g.price?.totalPrice?.discountPrice === 0;
          const promoActive = g.promotions?.promotionalOffers?.length > 0;
          return promo && promoActive;
        });

        // Renderizar os jogos
        renderGames(freeGames);

        const currentTitles = freeGames.map(g => g.title);
        const newTitles = currentTitles.filter(t => !lastFreeTitles.includes(t));
        if (lastFreeTitles.length > 0 && newTitles.length > 0) {
          showNotification("🎉 Novo jogo gratuito: " + newTitles.join(", "));
        }
        lastFreeTitles = currentTitles;

        const time = new Date().toLocaleTimeString("pt-BR");
        statusText.textContent = `✅ Atualizado automaticamente às ${time}`;
      } catch (error) {
        console.error("Erro ao buscar dados:", error);
        statusText.textContent = "❌ Erro ao carregar jogos da Epic Games.";
      }
    }

    // Renderizar jogos na tela
    function renderGames(games) {
      gamesGrid.innerHTML = "";
      if (games.length === 0) {
        gamesGrid.innerHTML = "<p class='loading'>Nenhum jogo gratuito disponível no momento 😔</p>";
        return;
      }
      games.forEach(game => {
        const image = game.keyImages?.[1]?.url || game.keyImages?.[0]?.url || "";
        const originalPrice = (game.price?.totalPrice?.fmtPrice?.originalPrice) || "R$ -";
        const endDate = game.promotions.promotionalOffers[0].promotionalOffers[0].endDate;
        const endTime = new Date(endDate).toLocaleString("pt-BR");

        const card = document.createElement("div");
        card.className = "game-card";
        card.innerHTML = `
          <img src="${image}" alt="${game.title}" class="game-image">
          <div class="game-info">
            <h3>${game.title}</h3>
            <p class="original">${originalPrice}</p>
            <p class="price">💥 GRÁTIS!</p>
            <p><small>Disponível até: ${endTime}</small></p>
            <a href="https://store.epicgames.com/p/${game.catalogNs.mappings?.[0]?.pageSlug || ''}" target="_blank">
              <button class="btn mt-2 w-full">Resgatar</button>
            </a>
          </div>`;
        gamesGrid.appendChild(card);
      });
    }

    // Atualização manual
    refreshBtn.addEventListener("click", fetchFreeGames);

    // Atualização automática a cada 10 minutos
    fetchFreeGames();
    setInterval(fetchFreeGames, 600000);
  </script>
</body>
</html>
