// server.js
import express from "express";
import fetch from "node-fetch";

const app = express();
const PORT = process.env.PORT || 3000;

// ============= ROTA BACKEND (SEM 404) =============
app.get("/api/freegames", async (req, res) => {
  try {
    const epicURL = "https://store-site-backend-static.ak.epicgames.com/freeGamesPromotions?locale=pt-BR";
    const response = await fetch(epicURL);
    if (!response.ok) throw new Error("Erro ao buscar dados da Epic Games");
    const data = await response.json();
    res.json(data);
  } catch (err) {
    console.error("❌ Erro:", err.message);
    res.status(500).json({ error: "Erro interno ao buscar dados" });
  }
});

// ============= PÁGINA HTML =============
app.get("/", (req, res) => {
  res.send(`
<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>🎮 Game Promos - Epic Games</title>
<script src="https://cdn.tailwindcss.com"></script>
<style>
body { background: #0f172a; color: white; font-family: 'Poppins', sans-serif; text-align: center; }
h1 { color: #38bdf8; font-size: 2rem; margin-top: 20px; }
#brTime { color: #facc15; margin: 10px 0; }
button { background: #2563eb; border: none; color: white; padding: 10px 15px; border-radius: 8px; cursor: pointer; margin: 5px; }
button:hover { background: #1d4ed8; }
.active { background: #38bdf8 !important; }
.game-card { background: #1e293b; border-radius: 10px; padding: 10px; margin: 10px auto; width: 90%; max-width: 350px; box-shadow: 0 0 10px rgba(0,0,0,0.3); }
img { width: 100%; border-radius: 10px; }
.price { color: #22c55e; font-weight: bold; }
.original { text-decoration: line-through; color: #999; }
.countdown { color: #facc15; margin-top: 5px; }
footer { color: #94a3b8; margin-top: 30px; }
</style>
</head>
<body>

<h1>🎁 Jogos Grátis da Epic Games</h1>
<p id="brTime">Carregando hora...</p>

<div>
  <button id="tabAtivos" class="active">🎮 Grátis Agora</button>
  <button id="tabFuturos">⏳ Em Breve</button>
</div>

<p id="status" style="margin:10px;color:#94a3b8;">Carregando...</p>
<div id="gamesGrid"></div>

<button id="refreshBtn">🔄 Atualizar</button>
<button id="reportBtn" style="background:#dc2626;">🐞 Reportar Bug</button>

<footer>© 2025 - Atualizado automaticamente a cada 10 minutos</footer>

<script>
const apiURL = "/api/freegames";
const gamesGrid = document.getElementById("gamesGrid");
const statusText = document.getElementById("status");
const tabAtivos = document.getElementById("tabAtivos");
const tabFuturos = document.getElementById("tabFuturos");

function updateBrazilTime() {
  const now = new Date();
  document.getElementById("brTime").textContent = "🕒 Hora Brasil: " + now.toLocaleString("pt-BR", { timeZone: "America/Sao_Paulo" });
}
setInterval(updateBrazilTime, 1000);
updateBrazilTime();

async function fetchFreeGames() {
  statusText.textContent = "🔄 Atualizando...";
  try {
    const res = await fetch(apiURL);
    const data = await res.json();
    const games = data.data.Catalog.searchStore.elements || [];
    const ativos = games.filter(g => g.promotions?.promotionalOffers?.length);
    const futuros = games.filter(g => g.promotions?.upcomingPromotionalOffers?.length);
    renderGames(ativos, true);
    tabAtivos.onclick = () => { renderGames(ativos, true); tabAtivos.classList.add("active"); tabFuturos.classList.remove("active"); };
    tabFuturos.onclick = () => { renderGames(futuros, false); tabFuturos.classList.add("active"); tabAtivos.classList.remove("active"); };
    statusText.textContent = "✅ Atualizado com sucesso!";
  } catch (err) {
    console.error(err);
    statusText.textContent = "❌ Erro ao carregar dados (ver servidor)";
  }
}

function renderGames(list, isAtivo) {
  gamesGrid.innerHTML = "";
  if (list.length === 0) {
    gamesGrid.innerHTML = "<p>Nenhum jogo disponível.</p>";
    return;
  }
  list.forEach(g => {
    const img = g.keyImages?.[0]?.url || "";
    const title = g.title;
    const offer = isAtivo
      ? g.promotions.promotionalOffers[0].promotionalOffers[0]
      : g.promotions.upcomingPromotionalOffers[0].promotionalOffers[0];
    const start = offer.startDate ? new Date(offer.startDate) : null;
    const end = offer.endDate ? new Date(offer.endDate) : null;
    const link = "https://store.epicgames.com/pt-BR/p/" + (g.productSlug || g.title.replaceAll(" ", "-").toLowerCase());
    const div = document.createElement("div");
    div.className = "game-card";
    div.innerHTML = \`
      <img src="\${img}" alt="\${title}">
      <h3>\${title}</h3>
      <p class="price">💥 Grátis</p>
      <p class="countdown">\${isAtivo ? "Termina em..." : "Começa em..."} </p>
      <a href="\${link}" target="_blank"><button>Resgatar</button></a>
    \`;
    const countdownEl = div.querySelector(".countdown");
    const timer = setInterval(() => {
      const now = new Date();
      let diff = isAtivo ? end - now : start - now;
      if (diff <= 0) {
        countdownEl.textContent = isAtivo ? "Promoção terminada" : "Disponível agora!";
        clearInterval(timer);
        return;
      }
      const d = Math.floor(diff / (1000*60*60*24));
      const h = Math.floor((diff / (1000*60*60)) % 24);
      const m = Math.floor((diff / (1000*60)) % 60);
      const s = Math.floor((diff / 1000) % 60);
      countdownEl.textContent = \`\${d}d \${h}h \${m}m \${s}s\`;
    }, 1000);
    gamesGrid.appendChild(div);
  });
}

document.getElementById("refreshBtn").onclick = fetchFreeGames;
document.getElementById("reportBtn").onclick = () => window.location.href = "mailto:reisjuvenira468@gmail.com?subject=Bug no site&body=Descreva o erro:";

fetchFreeGames();
setInterval(fetchFreeGames, 600000);
</script>
</body>
</html>
`);
});

app.listen(PORT, () => console.log("✅ Servidor rodando em: http://localhost:" + PORT));