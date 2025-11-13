<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
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
        header { text-align: center; padding: 20px 0; }
        h1 { font-size: 2rem; color: #00aaff; font-weight: 700; }
        .subtitle { color: #ccc; margin-top: 5px; }
        #brTime { font-weight: bold; margin-top: 5px; color: #ffcc00; }
        nav { text-align: center; margin-top: 10px; }
        nav button {
            background: #1e40af; color: white; border: none; padding: 10px 15px;
            margin: 5px; border-radius: 8px; cursor: pointer; font-weight: bold;
            transition: 0.3s;
        }
        nav button.active { background: #00aaff; }
        nav button:hover { background: #2563eb; }
        .games-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(260px, 1fr));
            gap: 20px; max-width: 1000px; margin: 20px auto; padding: 0 20px;
        }
        .game-card {
            background: #1c1c1c; border-radius: 12px; overflow: hidden;
            box-shadow: 0 4px 10px rgba(0,0,0,0.4);
            transition: transform 0.3s; position: relative;
        }
        .game-card:hover { transform: translateY(-6px); }
        .game-image { width: 100%; height: 160px; object-fit: cover; background: #333; }
        .game-info { padding: 15px; }
        .price { color: #00ff99; font-weight: bold; }
        .original { text-decoration: line-through; color: #999; }
        .btn {
            background: #0074e4; border: none; padding: 10px 15px; border-radius: 8px;
            color: white; cursor: pointer; transition: 0.3s; font-weight: bold;
            display: block; width: 100%; text-align: center;
        }
        .btn:hover { background: #005bb5; }
        .bug-btn { background: #dc2626; margin-top: 10px; }
        .bug-btn:hover { background: #b91c1c; }
        footer { text-align: center; margin-top: 30px; color: #aaa; padding-bottom: 30px; }
        .loading { text-align: center; font-size: 1.2rem; color: #bbb; margin-top: 40px; }
        .notification {
            position: fixed; top: 20px; right: 20px; background: #222; color: white;
            padding: 15px 20px; border-left: 4px solid #00ff99; border-radius: 6px;
            box-shadow: 0 4px 10px rgba(0,0,0,0.4); transform: translateX(300px);
            transition: transform 0.5s ease; z-index: 9999;
        }
        .notification.show { transform: translateX(0); }
        .countdown { font-weight: bold; margin-top: 5px; color: #ffcc00; }
        .soon-badge {
            position: absolute;
            top: 10px;
            right: 10px;
            background: #ff9900;
            color: #000;
            padding: 5px 10px;
            border-radius: 4px;
            font-weight: bold;
            font-size: 0.8rem;
        }
        .error-message {
            background: #dc2626;
            color: white;
            padding: 10px;
            border-radius: 8px;
            margin: 10px auto;
            max-width: 600px;
            text-align: center;
        }
    </style>
</head>
<body>
    <header>
        <h1>🎁 GameAlerts</h1>
        <p class="subtitle">Jogos pagos que estão ou estarão GRÁTIS na Epic Games Store</p>
        <div id="brTime">Carregando hora...</div>
    </header>

    <nav>
        <button id="tabAtivos" class="active">🎮 Grátis Agora</button>
        <button id="tabFuturos">⏳ Em Breve</button>
    </nav>

    <div id="status" class="text-center mb-4 text-sm text-gray-400">Carregando lista de jogos...</div>
    <div id="errorContainer"></div>
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

    <div class="notification" id="notification">Novo jogo gratuito detectado!</div>

    <script>
        // Configuração e variáveis globais
        const apiURLs = [
            "https://api.allorigins.win/raw?url=https://store-site-backend-static.ak.epicgames.com/freeGamesPromotions?country=BR&locale=pt-BR",
            "https://store-site-backend-static.ak.epicgames.com/freeGamesPromotions?country=BR&locale=pt-BR",
            "https://corsproxy.io/?https://store-site-backend-static.ak.epicgames.com/freeGamesPromotions?country=BR&locale=pt-BR"
        ];
        
        const gamesGrid = document.getElementById("gamesGrid");
        const statusText = document.getElementById("status");
        const errorContainer = document.getElementById("errorContainer");
        const refreshBtn = document.getElementById("refreshBtn");
        const reportBtn = document.getElementById("reportBtn");
        const notification = document.getElementById("notification");
        const tabAtivos = document.getElementById("tabAtivos");
        const tabFuturos = document.getElementById("tabFuturos");
        const brTimeEl = document.getElementById("brTime");
        let allGames = { ativos: [], futuros: [] };
        let countdownIntervals = [];

        // Atualizar hora do Brasil
        function updateBrazilTime() {
            const now = new Date();
            brTimeEl.textContent = "🕒 Hora Brasil: " + now.toLocaleString("pt-BR", { timeZone: "America/Sao_Paulo" });
        }
        setInterval(updateBrazilTime, 1000);
        updateBrazilTime();

        // Função para mostrar erro
        function showError(message) {
            errorContainer.innerHTML = `<div class="error-message">${message}</div>`;
        }

        // Função para limpar erro
        function clearError() {
            errorContainer.innerHTML = '';
        }

        // Tentar diferentes APIs até uma funcionar (com timeout)
        async function tryFetch(url, timeout = 7000) {
            const controller = new AbortController();
            const timer = setTimeout(() => controller.abort(), timeout);
            try {
                const response = await fetch(url, { signal: controller.signal });
                if (!response.ok) throw new Error(`HTTP ${response.status}`);
                return await response.json();
            } finally {
                clearTimeout(timer);
            }
        }

        // Buscar jogos gratuitos da API
        async function fetchFreeGames() {
            statusText.textContent = "🔍 Atualizando jogos gratuitos...";
            clearError();
            
            let data = null;
            let lastError = null;

            // Tentar cada URL até uma funcionar
            for (const url of apiURLs) {
                try {
                    data = await tryFetch(url);
                    break; // Se funcionou, para de tentar
                } catch (error) {
                    lastError = error;
                    continue; // Continua para a próxima URL
                }
            }

            if (!data) {
                const errorMsg = "❌ Erro 404: não foi possível conectar à Epic Games. Tentando carregar dados de exemplo...";
                showError(errorMsg);
                statusText.textContent = errorMsg;
                
                // Carregar dados de exemplo (fallback)
                loadSampleData();
                return;
            }

            allGames = data.data.Catalog.searchStore.elements.reduce((acc, game) => {
                const isFree = game.price.totalPrice.discountPrice === 0;
                if (isFree) {
                    if (new Date(game.promotions.promotionalOffers[0].endDate) > new Date()) {
                        acc.futuros.push(game);
                    } else {
                        acc.ativos.push(game);
                    }
                }
                return acc;
            }, { ativos: [], futuros: [] });

            renderGames();
        }

        // Exibir os jogos na tela
        function renderGames() {
            const activeGames = tabAtivos.classList.contains("active") ? allGames.ativos : [];
            const upcomingGames = tabFuturos.classList.contains("active") ? allGames.futuros : [];
            const gamesToDisplay = activeGames.concat(upcomingGames);
            
            gamesGrid.innerHTML = gamesToDisplay.map(game => {
                const imageUrl = game.keyImages.find(img => img.type === 'DieselStorefrontWide').url;
                const price = game.price.totalPrice.discountPrice === 0 ? "GRÁTIS" : game.price.totalPrice.formattedPrice;
                const originalPrice = game.price.totalPrice.discountPrice === 0 ? '' : `<span class="original">${game.price.totalPrice.originalPrice}</span>`;
                const endDate = game.promotions.promotionalOffers[0].endDate;
                const countdown = (new Date(endDate) - new Date()) / 1000;

                return `
                    <div class="game-card">
                        <img src="${imageUrl}" alt="${game.title}" class="game-image" />
                        <div class="game-info">
                            <h3 class="text-xl font-bold">${game.title}</h3>
                            <p>${game.description}</p>
                            <div class="flex justify-between items-center mt-3">
                                <div class="price">${price}</div>
                                ${originalPrice}
                            </div>
                            <button class="btn" onclick="window.open('${game.url}', '_blank')">Ver na Epic</button>
                            ${countdown < 3600 && countdown > 0 ? `<div class="countdown">⏳ Faltam ${Math.ceil(countdown / 60)} minutos para acabar!</div>` : ''}
                            ${countdown > 0 ? `<div class="soon-badge">Faltam ${Math.ceil(countdown / 60)} min</div>` : ''}
                        </div>
                    </div>
                `;
            }).join("");
            statusText.textContent = `${gamesToDisplay.length} jogos encontrados.`;
        }

        // Atualizar jogos ao clicar no botão de atualização
        refreshBtn.addEventListener('click', fetchFreeGames);

        // Alternar entre abas
        tabAtivos.addEventListener("click", () => {
            tabAtivos.classList.add("active");
            tabFuturos.classList.remove("active");
            renderGames();
        });
        tabFuturos.addEventListener("click", () => {
            tabFuturos.classList.add("active");
            tabAtivos.classList.remove("active");
            renderGames();
        });

        // Carregar dados de exemplo se a API falhar
        function loadSampleData() {
            allGames = {
                ativos: [
                    { title: "Exemplo de Jogo 1", description: "Este é um jogo de teste grátis!", price: { totalPrice: { discountPrice: 0 } }, url: "https://epicgames.com" },
                    { title: "Exemplo de Jogo 2", description: "Outro jogo grátis para testar", price: { totalPrice: { discountPrice: 0 } }, url: "https://epicgames.com" }
                ],
                futuros: []
            };
            renderGames();
        }

        // Carregar jogos ao inicializar
        fetchFreeGames();
    </script>
</body>
</html>
