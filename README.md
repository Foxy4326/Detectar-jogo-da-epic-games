// server.js
import express from "express";
import fetch from "node-fetch";
import path from "path";

const app = express();
const PORT = process.env.PORT || 3000;

// Middleware para servir arquivos estáticos (ícones do app)
app.use(express.static("public"));

// ===== API: Buscar jogos gratuitos da Epic Games =====
app.get("/api/freegames", async (req, res) => {
  try {
    const response = await fetch(
      "https://store-site-backend-static.ak.epicgames.com/freeGamesPromotions?locale=pt-BR"
    );
    const data = await response.json();
    res.json(data);
  } catch (error) {
    console.error("Erro ao buscar dados:", error);
    res.status(500).json({ error: "Erro ao buscar dados da Epic Games" });
  }
});

// ===== Página principal =====
app.get("/", (req, res) => {
  res.send(`
<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>🎁 GameAlerts - Jogos Grátis Epic Games</title>
<link rel="manifest" href="/manifest.json">
<script src="https://cdn.tailwindcss.com"></script>
<style>
  body { background: #0f172a; color: white; font-family: sans-serif; text-align: center; padding: 20px; }
  .card { background: #1e293b; padding: 16px; border-radius: 12px; margin: 12px auto; max-width: 420px; box-shadow: 0 0 12px #000; }
  .btn { padding: 10px 16px; border-radius: 10px; margin: 4px; font-weight: bold; }
  .btn-primary { background: #2563eb; color: white; }
  .btn-secondary { background: #ca8a04; color: black; }
  .btn-red { background: #dc2626; color: white; }
</style>
</head>
<body>

<h1 class="text-3xl font-bold text-blue-400 mb-3">🎮 GameAlerts</h1>
<p class="text-gray-300">Jogos pagos que estão ou estarão GRÁTIS na Epic Games Store</p>
<hr class="my-4 border-gray-500">

<h3 id="brTime" class="text-yellow-400">⏰ Carregando hora...</h3>
<button id="tabNow" class="btn btn-primary">🎁 Grátis Agora</button>
<button id="tabSoon" class="btn btn-secondary">⏳ Em Breve</button>

<div id="gamesList" class="mt-4"></div>

<button id="refresh" class="btn btn-primary mt-4">🔄 Atualizar Agora</button>
<button id="reportBug" class="btn btn-red mt-2">🐞 Reportar Bug</button>

<p class="text-gray-400 mt-6">Dados em tempo real da Epic Games Store</p>

<script>
// Hora do Brasil em tempo real
setInterval(() => {
  const now = new Date().toLocaleString("pt-BR", { timeZone: "America/Sao_Paulo" });
  document.getElementById("brTime").innerText = "🕒 Horário de Brasília: " + now;
}, 1000);

// Função: Buscar jogos
async function carregarJogos() {
  document.getElementById("gamesList").innerHTML = "<p>⏳ Carregando lista de jogos...</p>";
  try {
    const res = await fetch("/api/freegames");
    const data = await res.json();

    const jogos = data.data.Catalog.searchStore.elements;
    const container = document.getElementById("gamesList");
    container.innerHTML = "";

    const agora = [];
    const emBreve = [];

    jogos.forEach(jogo => {
      const promo = jogo.promotions?.promotionalOffers?.[0]?.promotionalOffers?.[0];
      const futuro = jogo.promotions?.upcomingPromotionalOffers?.[0]?.promotionalOffers?.[0];

      if (promo) agora.push({ 
        titulo: jogo.title, 
        img: jogo.keyImages?.[0]?.url,
        fim: promo.endDate 
      });

      if (futuro) emBreve.push({ 
        titulo: jogo.title, 
        img: jogo.keyImages?.[0]?.url,
        inicio: futuro.startDate 
      });
    });

    mostrar(agora, "agora");

    document.getElementById("tabNow").onclick = () => mostrar(agora, "agora");
    document.getElementById("tabSoon").onclick = () => mostrar(emBreve, "breve");

  } catch (e) {
    document.getElementById("gamesList").innerHTML = "<p>❌ Erro ao carregar jogos da Epic Games.</p>";
  }
}

function mostrar(lista, tipo) {
  const container = document.getElementById("gamesList");
  if (!lista.length) {
    container.innerHTML = "<p>🚫 Nenhum jogo encontrado.</p>";
    return;
  }

  container.innerHTML = "";
  lista.forEach(jogo => {
    const fim = tipo === "agora" ? new Date(jogo.fim) : new Date(jogo.inicio);
    const agora = new Date();
    const diff = Math.max(0, fim - agora);
    const dias = Math.floor(diff / (1000 * 60 * 60 * 24));
    const horas = Math.floor((diff / (1000 * 60 * 60)) % 24);
    const mins = Math.floor((diff / (1000 * 60)) % 60);

    const tempo = tipo === "agora"
      ? \`⏰ Termina em: \${dias}d \${horas}h \${mins}m\`
      : \`🕒 Começa em: \${dias}d \${horas}h \${mins}m\`;

    container.innerHTML += \`
      <div class="card">
        <img src="\${jogo.img}" alt="\${jogo.titulo}" class="w-full rounded-lg mb-2">
        <h2 class="text-xl font-bold mb-1">\${jogo.titulo}</h2>
        <p class="text-sm text-gray-300">\${tempo}</p>
      </div>
    \`;
  });
}

document.getElementById("refresh").onclick = carregarJogos;

// Sistema de Reportar Bug
document.getElementById("reportBug").onclick = () => {
  window.open("mailto:reisjuvenira468@gmail.com?subject=Bug no GameAlerts&body=Descreva o erro encontrado:");
};

// PWA - Adicionar App
window.addEventListener("load", () => {
  let deferredPrompt;
  window.addEventListener("beforeinstallprompt", e => {
    e.preventDefault();
    deferredPrompt = e;
    if (confirm("Deseja instalar o app GameAlerts na tela inicial?")) {
      deferredPrompt.prompt();
    }
  });
});

carregarJogos();
</script>
</body>
</html>
  `);
});

// ===== Manifesto para instalar o app =====
app.get("/manifest.json", (req, res) => {
  res.json({
    name: "GameAlerts",
    short_name: "GameAlerts",
    start_url: "/",
    display: "standalone",
    background_color: "#0f172a",
    theme_color: "#2563eb",
    icons: []
  });
});

// ===== Página de promoção encerrada =====
app.get("/encerrado", (req, res) => {
  res.send(`
  <html><body style="background:#111;color:white;text-align:center;padding:50px;font-family:sans-serif">
  <h1>🚫 Promoção Encerrada</h1>
  <p>Volte em breve para ver novas promoções da Epic Games!</p>
  <a href="/" style="color:#2563eb">⬅️ Voltar</a>
  </body></html>
  `);
});

app.listen(PORT, () => console.log("🚀 Servidor rodando na porta " + PORT));