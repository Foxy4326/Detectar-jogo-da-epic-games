<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GameAlerts - Jogos Gratuitos da Epic Games</title>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <style>
        :root {
            --primary: #2a2a2a;
            --secondary: #121212;
            --accent: #0074e4;
            --accent-hover: #0056b3;
            --text: #ffffff;
            --text-secondary: #b0b0b0;
            --success: #4CAF50;
            --warning: #FF9800;
            --error: #f44336;
        }
        
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
        }
        
        body {
            background-color: var(--primary);
            color: var(--text);
            line-height: 1.6;
            padding: 0;
            margin: 0;
        }
        
        .container {
            max-width: 1200px;
            margin: 0 auto;
            padding: 20px;
        }
        
        header {
            text-align: center;
            padding: 30px 0;
            background: linear-gradient(135deg, var(--secondary) 0%, var(--primary) 100%);
            border-radius: 10px;
            margin-bottom: 30px;
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.3);
            position: relative;
            overflow: hidden;
        }
        
        header::before {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            height: 4px;
            background: linear-gradient(90deg, var(--accent), var(--success));
        }
        
        h1 {
            font-size: 2.5rem;
            margin-bottom: 10px;
            color: var(--accent);
            text-shadow: 0 2px 4px rgba(0, 0, 0, 0.3);
        }
        
        .subtitle {
            font-size: 1.2rem;
            opacity: 0.8;
            max-width: 600px;
            margin: 0 auto;
        }
        
        .status-bar {
            display: flex;
            justify-content: space-between;
            align-items: center;
            background-color: var(--secondary);
            padding: 15px 20px;
            border-radius: 10px;
            margin-bottom: 20px;
            flex-wrap: wrap;
            gap: 15px;
        }
        
        .notification-toggle {
            display: flex;
            align-items: center;
            gap: 10px;
        }
        
        .toggle-switch {
            position: relative;
            display: inline-block;
            width: 60px;
            height: 34px;
        }
        
        .toggle-switch input {
            opacity: 0;
            width: 0;
            height: 0;
        }
        
        .slider {
            position: absolute;
            cursor: pointer;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background-color: #ccc;
            transition: .4s;
            border-radius: 34px;
        }
        
        .slider:before {
            position: absolute;
            content: "";
            height: 26px;
            width: 26px;
            left: 4px;
            bottom: 4px;
            background-color: white;
            transition: .4s;
            border-radius: 50%;
        }
        
        input:checked + .slider {
            background-color: var(--success);
        }
        
        input:checked + .slider:before {
            transform: translateX(26px);
        }
        
        .notification-config {
            display: flex;
            gap: 15px;
            flex-wrap: wrap;
        }
        
        .config-item {
            display: flex;
            align-items: center;
            gap: 5px;
        }
        
        .games-grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
            gap: 20px;
            margin-bottom: 30px;
        }
        
        .game-card {
            background-color: var(--secondary);
            border-radius: 10px;
            overflow: hidden;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
            transition: transform 0.3s ease, box-shadow 0.3s ease;
            position: relative;
        }
        
        .game-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 8px 16px rgba(0, 0, 0, 0.3);
        }
        
        .game-image {
            width: 100%;
            height: 180px;
            object-fit: cover;
            border-bottom: 2px solid var(--accent);
        }
        
        .game-badge {
            position: absolute;
            top: 10px;
            right: 10px;
            background-color: var(--success);
            color: white;
            padding: 5px 10px;
            border-radius: 4px;
            font-size: 0.8rem;
            font-weight: bold;
        }
        
        .game-info {
            padding: 15px;
        }
        
        .game-title {
            font-size: 1.2rem;
            margin-bottom: 10px;
            color: var(--text);
        }
        
        .game-description {
            font-size: 0.9rem;
            opacity: 0.8;
            margin-bottom: 15px;
            min-height: 60px;
        }
        
        .game-price {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 10px;
        }
        
        .original-price {
            text-decoration: line-through;
            opacity: 0.6;
            font-size: 0.9rem;
        }
        
        .current-price {
            color: var(--success);
            font-weight: bold;
            font-size: 1.1rem;
        }
        
        .game-meta {
            display: flex;
            justify-content: space-between;
            font-size: 0.8rem;
            opacity: 0.7;
            margin-top: 10px;
        }
        
        .notification-area {
            background-color: var(--secondary);
            padding: 20px;
            border-radius: 10px;
            margin-bottom: 20px;
        }
        
        .notification-title {
            font-size: 1.3rem;
            margin-bottom: 15px;
            color: var(--accent);
            display: flex;
            align-items: center;
            gap: 10px;
        }
        
        .notification-list {
            max-height: 200px;
            overflow-y: auto;
        }
        
        .notification-item {
            padding: 10px;
            border-bottom: 1px solid rgba(255, 255, 255, 0.1);
            display: flex;
            align-items: center;
            gap: 10px;
        }
        
        .notification-item:last-child {
            border-bottom: none;
        }
        
        .notification-icon {
            color: var(--accent);
        }
        
        .notification-time {
            margin-left: auto;
            font-size: 0.8rem;
            opacity: 0.7;
        }
        
        .btn {
            background-color: var(--accent);
            color: white;
            border: none;
            padding: 10px 20px;
            border-radius: 5px;
            cursor: pointer;
            font-weight: bold;
            transition: background-color 0.3s;
            display: inline-flex;
            align-items: center;
            gap: 8px;
            text-decoration: none;
        }
        
        .btn:hover {
            background-color: var(--accent-hover);
        }
        
        .btn-block {
            display: block;
            width: 100%;
            text-align: center;
        }
        
        .btn-success {
            background-color: var(--success);
        }
        
        .btn-success:hover {
            background-color: #3d8b40;
        }
        
        footer {
            text-align: center;
            padding: 20px 0;
            margin-top: 30px;
            border-top: 1px solid rgba(255, 255, 255, 0.1);
            font-size: 0.9rem;
            opacity: 0.7;
        }
        
        .instructions {
            background-color: var(--secondary);
            padding: 20px;
            border-radius: 10px;
            margin-bottom: 20px;
        }
        
        .instructions h3 {
            margin-bottom: 10px;
            color: var(--accent);
        }
        
        .instructions ol {
            margin-left: 20px;
            margin-bottom: 15px;
        }
        
        .instructions li {
            margin-bottom: 8px;
        }
        
        @media (max-width: 768px) {
            .games-grid {
                grid-template-columns: 1fr;
            }
            
            .status-bar {
                flex-direction: column;
                gap: 15px;
            }
            
            .notification-config {
                justify-content: center;
            }
        }
        
        .push-notification {
            position: fixed;
            top: 20px;
            right: 20px;
            background-color: var(--secondary);
            border-left: 4px solid var(--accent);
            padding: 15px;
            border-radius: 5px;
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.3);
            z-index: 1000;
            max-width: 300px;
            transform: translateX(400px);
            transition: transform 0.5s ease;
        }
        
        .push-notification.show {
            transform: translateX(0);
        }
        
        .push-title {
            font-weight: bold;
            margin-bottom: 5px;
            display: flex;
            align-items: center;
            gap: 8px;
        }
        
        .push-message {
            font-size: 0.9rem;
            opacity: 0.9;
        }
        
        .loading {
            text-align: center;
            padding: 20px;
            font-size: 1.2rem;
        }
        
        .loading i {
            color: var(--accent);
            margin-right: 10px;
        }
        
        .error-message {
            background-color: var(--error);
            color: white;
            padding: 10px;
            border-radius: 5px;
            margin: 10px 0;
            text-align: center;
        }
        
        .success-message {
            background-color: var(--success);
            color: white;
            padding: 10px;
            border-radius: 5px;
            margin: 10px 0;
            text-align: center;
        }
        
        .api-status {
            display: flex;
            align-items: center;
            gap: 8px;
            font-size: 0.9rem;
        }
        
        .status-indicator {
            width: 10px;
            height: 10px;
            border-radius: 50%;
            display: inline-block;
        }
        
        .status-online {
            background-color: var(--success);
        }
        
        .status-offline {
            background-color: var(--error);
        }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <h1><i class="fas fa-gamepad"></i> GameAlerts</h1>
            <p class="subtitle">Detecta jogos pagos que ficaram gratuitos na Epic Games</p>
        </header>
        
        <div class="instructions">
            <h3><i class="fas fa-info-circle"></i> Como funciona</h3>
            <p>Este site monitora a API oficial da Epic Games e detecta quando jogos pagos ficam gratuitos por tempo limitado.</p>
            <ol>
                <li>Ative as notificações para receber alertas</li>
                <li>O sistema verifica automaticamente a cada 30 minutos</li>
                <li>Clique em "Resgatar" para ir direto para a Epic Games Store</li>
            </ol>
        </div>
        
        <div class="status-bar">
            <div class="notification-toggle">
                <span>Notificações:</span>
                <label class="toggle-switch">
                    <input type="checkbox" id="notificationToggle" checked>
                    <span class="slider"></span>
                </label>
                <span id="toggleStatus">Ativadas</span>
            </div>
            
            <div class="api-status">
                <span class="status-indicator status-online" id="apiStatus"></span>
                <span id="apiStatusText">Conectado à Epic Games</span>
            </div>
            
            <div>
                <span id="lastUpdate">Última verificação: Agora</span>
            </div>
        </div>
        
        <div class="notification-area">
            <h2 class="notification-title"><i class="fas fa-bell"></i> Notificações Recentes</h2>
            <div class="notification-list" id="notificationList">
                <div class="notification-item">
                    <span class="notification-icon"><i class="fas fa-bell"></i></span>
                    <span>Sistema iniciado. Monitorando jogos gratuitos...</span>
                    <span class="notification-time">Agora</span>
                </div>
            </div>
        </div>
        
        <h2 style="margin-bottom: 20px; display: flex; align-items: center; gap: 10px;">
            <i class="fas fa-gift"></i> Jogos Gratuitos Atuais
            <span id="gamesCount" style="font-size: 0.8rem; opacity: 0.7;"></span>
        </h2>
        
        <div id="loadingGames" class="loading">
            <i class="fas fa-spinner fa-spin"></i> Conectando à Epic Games API...
        </div>
        
        <div class="games-grid" id="gamesGrid">
            <!-- Jogos serão carregados aqui via JavaScript -->
        </div>
        
        <button class="btn btn-block" id="checkNow">
            <i class="fas fa-sync-alt"></i> Verificar Agora
        </button>
        
        <footer>
            <p>Este site usa a API pública da Epic Games para detectar jogos gratuitos em tempo real.</p>
            <p>As ofertas são atualizadas todas as quintas-feiras às 16:00 (horário de Brasília).</p>
        </footer>
    </div>
    
    <div class="push-notification" id="pushNotification">
        <div class="push-title" id="pushTitle">
            <i class="fas fa-gamepad"></i> Novo Jogo Gratuito!
        </div>
        <div class="push-message" id="pushMessage"></div>
    </div>

    <script>
        // Elementos DOM
        const gamesGrid = document.getElementById('gamesGrid');
        const notificationList = document.getElementById('notificationList');
        const notificationToggle = document.getElementById('notificationToggle');
        const toggleStatus = document.getElementById('toggleStatus');
        const checkNowBtn = document.getElementById('checkNow');
        const pushNotification = document.getElementById('pushNotification');
        const pushTitle = document.getElementById('pushTitle');
        const pushMessage = document.getElementById('pushMessage');
        const lastUpdate = document.getElementById('lastUpdate');
        const loadingGames = document.getElementById('loadingGames');
        const apiStatus = document.getElementById('apiStatus');
        const apiStatusText = document.getElementById('apiStatusText');
        const gamesCount = document.getElementById('gamesCount');

        // Estado da aplicação
        let currentGames = [];
        let previousGames = [];
        let notifications = [
            { id: 1, message: "Sistema iniciado. Monitorando jogos gratuitos...", time: getCurrentTime() }
        ];

        // API da Epic Games
        const EPIC_API_URL = 'https://store-site-backend-static.ak.epicgames.com/freeGamesPromotions?locale=pt-BR&country=BR&allowCountries=BR';

        // Função para buscar jogos gratuitos da Epic Games
        async function fetchFreeGames() {
            try {
                updateApiStatus('Conectando...', 'status-offline');
                
                const response = await fetch(EPIC_API_URL);
                
                if (!response.ok) {
                    throw new Error(`Erro na API: ${response.status}`);
                }
                
                const data = await response.json();
                updateApiStatus('Conectado à Epic Games', 'status-online');
                return parseEpicGamesData(data);
                
            } catch (error) {
                console.error('Erro ao buscar jogos:', error);
                updateApiStatus('Erro na conexão', 'status-offline');
                addNotification(`Erro ao conectar com a Epic Games: ${error.message}`, 'error');
                return [];
            }
        }

        // Função para processar os dados da API da Epic
        function parseEpicGamesData(data) {
            const games = [];
            const elements = data?.data?.Catalog?.searchStore?.elements || [];
            
            elements.forEach(element => {
                // Verificar se é um jogo gratuito atual
                const promotions = element.promotions;
                if (promotions && promotions.promotionalOffers && promotions.promotionalOffers.length > 0) {
                    const promotionalOffers = promotions.promotionalOffers[0].promotionalOffers;
                    
                    promotionalOffers.forEach(offer => {
                        const startDate = new Date(offer.startDate);
                        const endDate = new Date(offer.endDate);
                        const now = new Date();
                        
                        // Verificar se a promoção está ativa
                        if (now >= startDate && now <= endDate) {
                            const originalPrice = element.price?.totalPrice?.fmtPrice?.originalPrice || "R$ 0,00";
                            const currentPrice = element.price?.totalPrice?.fmtPrice?.discountPrice || "Grátis";
                            
                            // Só incluir se realmente for gratuito
                            if (currentPrice === "Grátis" || currentPrice === "R$ 0,00") {
                                games.push({
                                    id: element.id,
                                    title: element.title,
                                    description: element.description || "Descrição não disponível",
                                    image: getBestImage(element),
                                    originalPrice: originalPrice,
                                    currentPrice: "Grátis",
                                    url: `https://store.epicgames.com/pt-BR/p/${element.productSlug || element.urlSlug || element.id}`,
                                    timeLeft: formatTimeLeft(endDate),
                                    isNew: true,
                                    startDate: startDate,
                                    endDate: endDate
                                });
                            }
                        }
                    });
                }
            });
            
            return games;
        }

        // Função para obter a melhor imagem disponível
        function getBestImage(element) {
            if (element.keyImages && element.keyImages.length > 0) {
                // Priorizar imagens do tipo "OfferImageWide"
                const wideImage = element.keyImages.find(img => img.type === "OfferImageWide");
                if (wideImage) return wideImage.url;
                
                // Usar a primeira imagem disponível
                return element.keyImages[0].url;
            }
            
            // Fallback para placeholder
            return `https://via.placeholder.com/300x180/2a2a2a/ffffff?text=${encodeURIComponent(element.title)}`;
        }

        // Função para formatar o tempo restante
        function formatTimeLeft(endDate) {
            const now = new Date();
            const diff = endDate - now;
       
