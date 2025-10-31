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
            height: 60px;
            overflow: hidden;
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
        }
        
        .btn:hover {
            background-color: var(--accent-hover);
        }
        
        .btn-block {
            display: block;
            width: 100%;
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
        
        /* Simulação de notificação push */
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
    </style>
</head>
<body>
    <div class="container">
        <header>
            <h1><i class="fas fa-gamepad"></i> GameAlerts</h1>
            <p class="subtitle">Receba notificações quando a Epic Games liberar jogos gratuitos</p>
        </header>
        
        <div class="instructions">
            <h3><i class="fas fa-info-circle"></i> Como funciona</h3>
            <p>Este site monitora a página de jogos gratuitos da Epic Games e envia notificações quando novos jogos são disponibilizados gratuitamente.</p>
            <ol>
                <li>Ative as notificações no botão abaixo</li>
                <li>Receba alertas quando novos jogos gratuitos forem detectados</li>
                <li>Clique no jogo para resgatá-lo na Epic Games Store</li>
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
            
            <div class="notification-config">
                <div class="config-item">
                    <input type="checkbox" id="soundToggle" checked>
                    <label for="soundToggle">Som</label>
                </div>
                <div class="config-item">
                    <input type="checkbox" id="desktopToggle" checked>
                    <label for="desktopToggle">Notificação Desktop</label>
                </div>
            </div>
            
            <div>
                <span id="lastUpdate">Última verificação: Agora</span>
            </div>
        </div>
        
        <div class="notification-area">
            <h2 class="notification-title"><i class="fas fa-bell"></i> Notificações Recentes</h2>
            <div class="notification-list" id="notificationList">
                <!-- Notificações serão adicionadas aqui via JavaScript -->
            </div>
        </div>
        
        <h2 style="margin-bottom: 20px; display: flex; align-items: center; gap: 10px;">
            <i class="fas fa-gift"></i> Jogos Gratuitos Atuais
        </h2>
        
        <div id="loadingGames" class="loading">
            <i class="fas fa-spinner fa-spin"></i> Carregando jogos gratuitos...
        </div>
        
        <div class="games-grid" id="gamesGrid">
            <!-- Jogos serão carregados aqui via JavaScript -->
        </div>
        
        <button class="btn btn-block" id="checkNow">
            <i class="fas fa-sync-alt"></i> Verificar Agora por Novos Jogos
        </button>
        
        <footer>
            <p>Este site não é afiliado à Epic Games. As informações são atualizadas automaticamente.</p>
            <p>Para notificações reais no celular, ative as notificações do navegador.</p>
        </footer>
    </div>
    
    <!-- Simulação de notificação push -->
    <div class="push-notification" id="pushNotification">
        <div class="push-title" id="pushTitle">
            <i class="fas fa-gamepad"></i> Novo Jogo Gratuito!
        </div>
        <div class="push-message" id="pushMessage"></div>
    </div>

    <script>
        // Dados simulados de jogos gratuitos (em um sistema real, viriam de uma API)
        const freeGames = [
            {
                id: 1,
                title: "Fallout 3: Game of the Year Edition",
                description: "Entre no mundo pós-apocalíptico de Fallout 3 e experimente uma das melhores aventuras de RPG.",
                image: "https://cdn1.epicgames.com/offer/3ddd6a590da64e3686042d108968a6b2/EGS_Fallout3GameoftheYearEdition_BethesdaGameStudios_S1_2560x1440-32d7a6153a0b8128d35d652e8e5b51a2",
                originalPrice: "R$ 49,99",
                currentPrice: "Grátis",
                url: "https://store.epicgames.com/pt-BR/p/fallout-3",
                timeLeft: "5 dias",
                isNew: true
            },
            {
                id: 2,
                title: "Chivalry 2",
                description: "Um jogo de combate medieval em primeira pessoa com batalhas épicas e sangrentas.",
                image: "https://cdn1.epicgames.com/spt-assets/14e47f4956cd4f5fb040e9d5246dfd0d/chivalry-2-1y9a5.jpg",
                originalPrice: "R$ 99,99",
                currentPrice: "Grátis",
                url: "https://store.epicgames.com/pt-BR/p/chivalry-2",
                timeLeft: "3 dias",
                isNew: false
            },
            {
                id: 3,
                title: "Rumble Club",
                description: "Um jogo de luta party com físicas engraçadas e batalhas caóticas.",
                image: "https://cdn1.epicgames.com/spt-assets/5c6c6ddb5c9942c2a6e8c5f5c53c4014/rumbleclub-offer-1bv8c.jpg",
                originalPrice: "R$ 39,99",
                currentPrice: "Grátis",
                url: "https://store.epicgames.com/pt-BR/p/rumbleclub",
                timeLeft: "7 dias",
                isNew: true
            },
            {
                id: 4,
                title: "The Sims™ 4",
                description: "Crie sims únicos, construa casas ideais e personalize cada detalhe da vida deles.",
                image: "https://cdn1.epicgames.com/spt-assets/20ae317d4c5a4f5d822c80d248d96645/the-sims-4-17t0k.jpg",
                originalPrice: "R$ 159,00",
                currentPrice: "Grátis",
                url: "https://store.epicgames.com/pt-BR/p/the-sims-4",
                timeLeft: "Sempre gratuito",
                isNew: false
            }
        ];

        // Notificações iniciais
        let notifications = [
            { id: 1, message: "Fallout 3: Game of the Year Edition está gratuito!", time: "10:30" },
            { id: 2, message: "Rumble Club foi adicionado aos jogos gratuitos!", time: "09:15" },
            { id: 3, message: "Sistema de notificações ativado com sucesso!", time: "08:00" }
        ];

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
        const soundToggle = document.getElementById('soundToggle');
        const desktopToggle = document.getElementById('desktopToggle');

        // Função para renderizar jogos
        function renderGames() {
            gamesGrid.innerHTML = '';
            freeGames.forEach(game => {
                const gameCard = document.createElement('div');
                gameCard.className = 'game-card';
                gameCard.innerHTML = `
                    <img src="${game.image}" alt="${game.title}" class="game-image" onerror="this.src='https://via.placeholder.com/300x180/2a2a2a/ffffff?text=Imagem+do+Jogo'">
                    ${game.isNew ? '<div class="game-badge">NOVO</div>' : ''}
                    <div class="game-info">
                        <h3 class="game-title">${game.title}</h3>
                        <p class="game-description">${game.description}</p>
                        <div class="game-price">
                            <span class="original-price">${game.originalPrice}</span>
                            <span class="current-price">${game.currentPrice}</span>
                        </div>
                        <div class="game-meta">
                            <span>Disponível por: ${game.timeLeft}</span>
                            <a href="${game.url}" target="_blank" class="btn btn-success" style="padding: 5px 10px; font-size: 0.8rem;">
                                <i class="fas fa-external-link-alt"></i> Resgatar
                            </a>
                        </div>
                    </div>
                `;
                gamesGrid.appendChild(gameCard);
            });
            
            // Esconder loading
            loadingGames.style.display = 'none';
        }

        // Função para renderizar notificações
        function renderNotifications() {
            notificationList.innerHTML = '';
            notifications.forEach(notification => {
                const notificationItem = document.createElement('div');
                notificationItem.className = 'notification-item';
                notificationItem.innerHTML = `
                    <span class="notification-icon"><i class="fas fa-bell"></i></span>
                    <span>${notification.message}</span>
                    <span class="notification-time">${notification.time}</span>
                `;
                notificationList.appendChild(notificationItem);
            });
        }

        // Função para mostrar notificação push
        function showPushNotification(title, message) {
            if (notificationToggle.checked && desktopToggle.checked) {
                pushTitle.innerHTML = `<i class="fas fa-gamepad"></i> ${title}`;
                pushMessage.textContent = message;
                pushNotification.classList.add('show');
                
                // Tocar som se ativado
                if (soundToggle.checked) {
                    playNotificationSound();
                }
                
                setTimeou
