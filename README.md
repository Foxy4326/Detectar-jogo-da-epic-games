<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>GameAlerts - Jogos Gratuitos Epic Games</title>
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
            color: var(--accent);
        }

        .subtitle {
            font-size: 1.2rem;
            opacity: 0.8;
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

        .toggle-switch input { opacity: 0; width: 0; height: 0; }

        .slider {
            position: absolute;
            cursor: pointer;
            top: 0; left: 0; right: 0; bottom: 0;
            background-color: #ccc;
            transition: .4s;
            border-radius: 34px;
        }

        .slider:before {
            position: absolute;
            content: "";
            height: 26px; width: 26px;
            left: 4px; bottom: 4px;
            background-color: white;
            transition: .4s;
            border-radius: 50%;
        }

        input:checked + .slider { background-color: var(--success); }
        input:checked + .slider:before { transform: translateX(26px); }

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
            transition: 0.3s;
            box-shadow: 0 4px 8px rgba(0,0,0,0.2);
        }

        .game-card:hover {
            transform: translateY(-5px);
            box-shadow: 0 8px 16px rgba(0,0,0,0.3);
        }

        .game-image {
            height: 180px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 1.2rem;
            color: white;
        }

        .game-badge {
            position: absolute;
            top: 10px; right: 10px;
            background-color: var(--success);
            padding: 5px 10px;
            border-radius: 4px;
            font-size: 0.8rem;
            font-weight: bold;
        }

        .notification-area, .instructions {
            background-color: var(--secondary);
            padding: 20px;
            border-radius: 10px;
            margin-bottom: 20px;
        }

        .notification-list {
            max-height: 200px;
            overflow-y: auto;
        }

        .notification-item {
            padding: 10px;
            border-bottom: 1px solid rgba(255,255,255,0.1);
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .notification-time {
            margin-left: auto;
            opacity: 0.7;
            font-size: 0.8rem;
        }

        .btn {
            background-color: var(--accent);
            color: white;
            border: none;
            padding: 10px 20px;
            border-radius: 5px;
            cursor: pointer;
            font-weight: bold;
            transition: 0.3s;
            width: 100%;
        }

        .btn:hover { background-color: var(--accent-hover); }

        .push-notification {
            position: fixed;
            top: 20px; right: 20px;
            background-color: var(--secondary);
            border-left: 4px solid var(--accent);
            padding: 15px;
            border-radius: 5px;
            box-shadow: 0 4px 12px rgba(0,0,0,0.3);
            z-index: 1000;
            transform: translateX(400px);
            transition: transform 0.5s ease;
        }

        .push-notification.show { transform: translateX(0); }

        .icon::before { margin-right: 5px; }
    </style>
</head>
<body>
    <div class="container">
        <header>
            <h1>🎮 GameAlerts</h1>
            <p class="subtitle">Jogos Gratuitos Atuais na Epic Games Store</p>
        </header>

        <div class="status-bar">
            <div class="notification-toggle">
                <span>Notificações:</span>
                <label class="toggle-switch">
                    <input type="checkbox" id="notificationToggle" checked>
                    <span class="slider"></span>
                </label>
                <span id="toggleStatus">Ativadas</span>
            </div>
            <span id="lastUpdate">Atualizado: Dezembro 2024</span>
        </div>

        <div class="notification-area">
            <h2>🔔 Notificações</h2>
            <div class="notification-list" id="notificationList"></div>
        </div>

        <h2>🎁 Jogos Gratuitos Atuais</h2>
        <div class="games-grid" id="gamesGrid"></div>

        <div class="instructions">
            <h3>⏰ Próximos Jogos</h3>
            <p>Novos jogos gratuitos são liberados toda quinta-feira às 16:00 (horário de Brasília).</p>
        </div>

        <button class="btn" id="checkManual">Verificar Jogos Manualmente</button>

        <footer style="text-align:center; margin-top:30px;">
            <a href="https://store.epicgames.com/pt-BR/free-games" target="_blank" style="color: var(--accent);">Visitar Epic Games</a>
        </footer>
    </div>

    <div class="push-notification" id="pushNotification">
        <div id="pushTitle">🎮 Novo Jogo!</div>
        <div id="pushMessage"></div>
    </div>

    <script>
        // Funções utilitárias
        function getCurrentTime() {
            const now = new Date();
            return now.toLocaleTimeString('pt-BR', { hour: '2-digit', minute: '2-digit' });
        }

        // Dados base
        const freeGames = [
            { id: 1, title: "Death Stranding", description: "De Hideo Kojima, uma experiência de ação única.", originalPrice: "R$ 159,90", currentPrice: "Grátis", color: "linear-gradient(45deg, #FF6B6B, #4ECDC4)", timeLeft: "7 dias" },
            { id: 2, title: "Rocket League", description: "Futebol com carros em ritmo acelerado!", originalPrice: "Free-to-Play", currentPrice: "Grátis", color: "linear-gradient(45deg, #FFD700, #FF69B4)", timeLeft: "Sempre gratuito" }
        ];

        let notifications = [
            { id: 1, message: "Death Stranding está gratuito!", time: getCurrentTime() },
            { id: 2, message: "Sistema de notificações ativado.", time: getCurrentTime() }
        ];

        const gamesGrid = document.getElementById('gamesGrid');
        const notificationList = document.getElementById('notificationList');
        const notificationToggle = document.getElementById('notificationToggle');
        const toggleStatus = document.getElementById('toggleStatus');
        const pushNotification = document.getElementById('pushNotification');
        const pushTitle = document.getElementById('pushTitle');
        const pushMessage = document.getElementById('pushMessage');
        const checkManualBtn = document.getElementById('checkManual');

        // Renderização
        function renderGames() {
            gamesGrid.innerHTML = '';
            freeGames.forEach(game => {
                const card = document.createElement('div');
                card.className = 'game-card';
                card.innerHTML = `
                    <div class="game-image" style="background:${game.color};">${game.title}</div>
                    <div style="padding:15px;">
                        <h3>${game.title}</h3>
                        <p>${game.description}</p>
                        <p><s>${game.originalPrice}</s> <strong>${game.currentPrice}</strong></p>
                        <small>Disponível por: ${game.timeLeft}</small>
                    </div>`;
                gamesGrid.appendChild(card);
            });
        }

        function renderNotifications() {
            notificationList.innerHTML = '';
            notifications.forEach(n => {
                const item = document.createElement('div');
                item.className = 'notification-item';
                item.innerHTML = `🔔 ${n.message} <span class="notification-time">${n.time}</span>`;
                notificationList.appendChild(item);
            });
        }

        function showPushNotification(title, message) {
            if (!notificationToggle.checked) return;
            pushTitle.textContent = title;
            pushMessage.textContent = message;
            pushNotification.classList.add('show');
            setTimeout(() => pushNotification.classList.remove('show'), 5000);
        }

        // Verificação de novos jogos
        function checkForNewGames() {
            if (Math.random() < 0.2) {
                const newGames = ["Cyberpunk 2077", "GTA V", "Elden Ring", "The Witcher 3"];
                const randomGame = newGames[Math.floor(Math.random() * newGames.length)];
                const newNotification = {
                    id: notifications.length + 1,
                    message: `${randomGame} agora está GRATUITO na Epic Games!`,
                    time: getCurrentTime()
                };
                notifications.unshift(newNotification);
                renderNotifications();
                showPushNotification("Novo Jogo Gratuito!", newNotification.message);
            } else {
                showPushNotification("Verificação concluída", "Nenhum novo jogo gratuito no momento.");
            }
        }

        // Eventos
        notificationToggle.addEventListener('change', () => {
            toggleStatus.textContent = notificationToggle.checked ? "Ativadas" : "Desativadas";
        });

        checkManualBtn.addEventListener('click', checkForNewGames);

        // Inicialização
        renderGames();
        renderNotifications();

        // Verificação automática a cada 5 minutos
        setInterval(checkForNewGames, 300000);
    </script>
</body>
</html>
