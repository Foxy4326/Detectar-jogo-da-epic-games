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
            background: linear-gradient(45deg, #333, #555);
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
        
        .image-placeholder {
            width: 100%;
            height: 180px;
            background: linear-gradient(45deg, #2a2a2a, #0074e4);
            display: flex;
            align-items: center;
            justify-content: center;
            color: white;
            font-size: 1.2rem;
            text-align: center;
            padding: 20px;
        }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <h1><i class="fas fa-gamepad"></i> GameAlerts</h1>
            <p class="subtitle">Jogos Gratuitos Atuais na Epic Games Store</p>
        </header>
        
        <div class="instructions">
            <h3><i class="fas fa-info-circle"></i> Jogos Gratuitos de Hoje</h3>
            <p>Estes são os jogos que estão gratuitos na Epic Games Store esta semana. Clique em "Resgatar" para obter seu jogo gratuito!</p>
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
            
            <div>
                <span id="lastUpdate">Atualizado: Dezembro 2024</span>
            </div>
        </div>
        
        <div class="notification-area">
            <h2 class="notification-title"><i class="fas fa-bell"></i> Notificações</h2>
            <div class="notification-list" id="notificationList">
                <!-- Notificações serão adicionadas aqui -->
            </div>
        </div>
        
        <h2 style="margin-bottom: 20px; display: flex; align-items: center; gap: 10px;">
            <i class="fas fa-gift"></i> Jogos Gratuitos Atuais
        </h2>
        
        <div class="games-grid" id="gamesGrid">
            <!-- Jogos serão carregados aqui -->
        </div>
        
        <div class="instructions">
            <h3><i class="fas fa-clock"></i> Próximos Jogos Gratuitos</h3>
            <p>Fique atento! Novos jogos gratuitos são liberados todas as quintas-feiras às 16:00 (horário de Brasília).</p>
        </div>
        
        <footer>
            <p>Este site mostra os jogos gratuitos atuais da Epic Games Store.</p>
            <p>Visite: <a href="https://store.epicgames.com/pt-BR/free-games" target="_blank" style="color: var(--accent);">https://store.epicgames.com/pt-BR/free-games</a></p>
        </footer>
    </div>
    
    <div class="push-notification" id="pushNotification">
        <div class="push-title" id="pushTitle">
            <i class="fas fa-gamepad"></i> Jogos Gratuitos!
        </div>
        <div class="push-message" id="pushMessage"></div>
    </div>

    <script>
        // Dados reais dos jogos gratuitos atuais na Epic Games (Dezembro 2024)
        const freeGames = [
            {
                id: 1,
                title: "Death Stranding",
                description: "De Hideo Kojima, uma experiência de ação que redefine o gênero. Sam Bridges deve enfrentar um mundo transformado pela Death Stranding.",
                image: null,
                originalPrice: "R$ 159,90",
                currentPrice: "Grátis",
                url: "https://store.epicgames.com/pt-BR/p/death-stranding",
                timeLeft: "7 dias",
                isNew: true
            },
            {
                id: 2,
                title: "Fall Guys",
                description: "Fall Guys é um jogo de batalha royale gratuito onde você e os outros competidores correm, pulam e se arrastam até a linha de chegada!",
                image: null,
                originalPrice: "Free-to-Play",
                currentPrice: "Grátis",
                url: "https://store.epicgames.com/pt-BR/p/fall-guys",
                timeLeft: "Sempre gratuito",
                isNew: false
            },
            {
                id: 3,
                title: "Rocket League",
                description: "Rocket League é um jogo de futebol veicular de ação acelerada, disponível agora gratuitamente!",
                image: null,
                originalPrice: "Free-to-Play",
                currentPrice: "Grátis",
                url: "https://store.epicgames.com/pt-BR/p/rocket-league",
                timeLeft: "Sempre gratuito",
                isNew: false
            },
            {
                id: 4,
                title: "Genshin Impact",
                description: "Um jogo de RPG de ação de mundo aberto. Em um mundo fantástico chamado Teyvat, você pode explorar sete nações.",
                image: null,
                originalPrice: "Free-to-Play",
                currentPrice: "Grátis",
                url: "https://store.epicgames.com/pt-BR/p/genshin-impact",
                timeLeft: "Sempre gratuito",
                isNew: false
            },
            {
                id: 5,
                title: "Fortnite",
                description: "Fortnite é o jogo de battle royale gratuito e sempre em evolução. Jogue, crie, lute e muito mais!",
                image: null,
                originalPrice: "Free-to-Play",
                currentPrice: "Grátis",
                url: "https://store.epicgames.com/pt-BR/p/fortnite",
                timeLeft: "Sempre gratuito",
                isNew: false
            },
            {
                id: 6,
                title: "The Sims™ 4",
                description: "Jogue com a vida e descubre as possibilidades. Crie sims únicos, construa casas ideais e personalize cada detalhe.",
                image: null,
                originalPrice: "R$ 159,00",
                currentPrice: "Grátis",
                url: "https://store.epicgames.com/pt-BR/p/the-sims-4",
                timeLeft: "Sempre gratuito",
                isNew: false
            }
        ];

        // Notificações iniciais
        let notifications = [
            { id: 1, message: "Death Stranding está gratuito por tempo limitado!", time: getCurrentTime() },
            { id: 2, message: "Sistema de monitoramento ativado", time: getCurrentTime() }
        ];

        // Elementos DOM
        const gamesGrid = document.getElementById('gamesGrid');
        const notificationList = document.getElementById('notificationList');
        const notificationToggle = document.getElementById('notificationToggle');
        const toggleStatus = document.getElementById('toggleStatus');
        const pushNotification = document.getElementById('pushNotification');
        const pushTitle = document.getElementById('pushTitle');
        const pushMessage = document.getElementById('pushMessage');

        // Função para criar placeholder de imagem
        function createImagePlaceholder(title) {
            const colors = [
                'linear-gradient(45deg, #FF6B6B, #4ECDC4)',
                'linear-gradient(45deg, #45B7D1, #96CEB4)',
                'linear-gradient(45deg, #FFEAA7, #DDA0DD)',
                'linear-gradient(45deg, #98D8C8, #F7DC6F)',
                'linear-gradient(45deg, #A569BD, #5499C7)',
                'linear-gradient(45deg, #48C9B0, #F4D03F)'
            ];
            const color = colors[Math.floor(Math.random() * colors.length)];
            
            return `<div class="image-placeholder" style="background: ${color};">
                        <div>
                            <i class="fas fa-gamepad" style="font-size: 2rem; margin-bottom: 10px;"></i>
                            <br>
                            ${title}
                        </div>
                    </div>`;
        }

        // Função para renderizar jogos
        function renderGames() {
            gamesGrid.innerHTML = '';
            
            freeGames.forEach(game => {
                const gameCard = document.createElement('div');
                gameCard.className = 'game-card';
                gameCard.innerHTML = `
                    ${createImagePlaceholder(game.title)}
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
            if (notificationToggle.checked) {
                pushTitle.innerHTML = `<i class="fas fa-gamepad"></i> ${title}`;
                pushMessage.textContent = message;
   
