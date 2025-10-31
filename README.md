<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>🎮 Jogos Pagos Grátis — Epic Games</title>
<script src="https://cdn.tailwindcss.com"></script>
<style>
  body {
    background: linear-gradient(135deg, #111, #1f2937);
    color: white;
    font-family: "Poppins", sans-serif;
    min-height: 100vh;
  }
  .game-card {
    transition: transform 0.3s, box-shadow 0.3s;
  }
  .game-card:hover {
    transform: scale(1.03);
    box-shadow: 0 0 20px rgba(255,255,255,0.2);
  }
</style>
</head>
<body class="flex flex-col items-center justify-center p-6">

  <h1 class="text-3xl font-bold mb-6 text-center">🎁 Jogos Pagos Grátis — Epic Games Store</h1>
  <p id="status" class="text-gray-300 mb-6 text-center">Carregando lista de jogos...</p>

  <div id="games" class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 gap-6 max-w-6xl w-full"></div>

  <script>
    async function carregarJogosGratis() {
      const status = document.getElementById("status");
      const container = document.getElementById("games");

      try {
        // API com proxy para evitar bloqueio CORS
        const apiURL = "https://api.allorigins.win/raw?url=" +
          encodeURIComponent("https://store-site-backend-static.ak.epicgames.com/freeGamesPromotions?locale=pt-BR");

        const resposta = await fetch(apiURL);
        if (!resposta.ok) throw new Error("Falha ao conectar à API.");

        const dados = await resposta.json();
        const jogos = dados.data.Catalog.searchStore.elements;

        const jogosGratis = jogos.filter(jogo =>
          jogo.price.totalPrice.originalPrice > 0 &&
          jogo.price.totalPrice.discountPrice === 0
        );

        if (jogosGratis.length === 0) {
          status.textContent = "🕹️ Nenhum jogo pago está gratuito no momento.";
          return;
        }

        status.textContent = "✅ Jogos pagos gratuitos encontrados:";
        container.innerHTML = "";

        jogosGratis.forEach(jogo => {
          const img = jogo.keyImages.find(img => img.type === "DieselStoreFrontWide")?.url || "";
          const nome = jogo.title;
          const link = `https://store.epicgames.com/p/${jogo.productSlug}`;
          const precoOriginal = (jogo.price.totalPrice.originalPrice / 100).toFixed(2);

          const card = document.createElement("div");
          card.className = "game-card bg-gray-800 rounded-2xl overflow-hidden shadow-lg";

          card.innerHTML = `
            <img src="${img}" alt="${nome}" class="w-full h-48 object-cover">
            <div class="p-4">
              <h2 class="text-lg font-bold mb-2">${nome}</h2>
              <p class="text-sm text-gray-400 mb-2">💰 De R$${precoOriginal} por <span class="text-green-400 font-bold">GRÁTIS</span></p>
              <a href="${link}" target="_blank" class="inline-block bg-green-600 hover:bg-green-700 px-3 py-2 rounded-md text-sm font-semibold">Resgatar</a>
            </div>
          `;
          container.appendChild(card);
        });

      } catch (erro) {
        console.error(erro);
        status.textContent = "❌ Erro ao carregar jogos da Epic Games.";
      }
    }

    carregarJogosGratis();
  </script>
</body>
</html>
