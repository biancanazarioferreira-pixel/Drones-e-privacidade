from pathlib import Path
import zipfile, textwrap, os

root = Path("/mnt/data/maratona-tech-recriado")
(root / "api").mkdir(parents=True, exist_ok=True)

index_html = r'''<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Fake News sobre Drones e Privacidade</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>
<header class="hero">
  <h1>Fake News sobre Drones e Privacidade</h1>
  <p>Instituição: CEEP Agrícola | Professor: Adilson Redivo</p>
  <div class="meta"><strong>Grupo:</strong> Código da Roça &nbsp;&nbsp; <strong>Turma:</strong> 1º ano A &nbsp;&nbsp; <strong>Área:</strong> Agropecuária</div>
</header>

<nav class="nav" aria-label="Navegação principal">
  <button class="nav-btn active" data-tab="projeto">O Projeto</button>
  <button class="nav-btn" data-tab="diagnostico">Diagnóstico e Riscos</button>
  <button class="nav-btn" data-tab="guia">Guia de Recomendações</button>
  <button class="nav-btn" data-tab="verificador">🔎 Verificador de Informação</button>
  <button class="nav-btn" data-tab="quiz">📊 Aba de Perguntas (Quiz)</button>
</nav>

<main>
<section id="projeto" class="tab active">
  <div class="card">
    <h2>Contextualização do Problema</h2>
    <p><strong>Qual é o problema?</strong> Uma notícia falsa começou a circular nas redes sociais afirmando que todos os drones utilizados no agronegócio estão sendo usados para espionar propriedades rurais vizinhas, filmando trabalhadores, casas e plantações sem autorização.</p>
    <p><strong>Causa-Raiz:</strong> O problema se espalha devido à desinformação intencional, busca por engajamento e, principalmente, pela <strong>falta de verificação</strong> das informações antes de compartilhá-las.</p>
    <h3>Perfil Profissional Escolhido</h3>
    <p>O <strong>Engenheiro Agrônomo</strong> foi o profissional selecionado para mitigar o impacto. Ele contribui orientando os produtores a utilizarem as tecnologias de forma responsável, garantindo a produtividade sem violar os limites éticos e legais de privacidade.</p>
  </div>
</section>

<section id="diagnostico" class="tab">
  <div class="card">
    <h2>Diagnóstico de Riscos Sociais</h2>
    <p>As Fake News geram sérios impactos em nossa sociedade. Veja a classificação identificada pela equipe:</p>
    <table>
      <thead><tr><th>Tipo de Impacto</th><th>Descrição do Risco</th></tr></thead>
      <tbody>
        <tr><td><strong>Informacional</strong></td><td>Informações falsas confundem a população sobre o uso real dos drones.</td></tr>
        <tr><td><strong>Econômico</strong></td><td>A desconfiança gerada pode prejudicar e atrasar a aceitação de novas tecnologias agrícolas.</td></tr>
        <tr><td><strong>Emocional</strong></td><td>Notícias alarmistas geram medo coletivo e sentimento de insegurança.</td></tr>
        <tr><td><strong>Coletivo</strong></td><td>Incentiva conflitos desnecessários entre vizinhos e destrói a confiança na comunidade rural.</td></tr>
      </tbody>
    </table>
  </div>
</section>

<section id="guia" class="tab">
  <div class="card">
    <h2>Guia de Recomendações Públicas</h2>
    <p>Para construir um ecossistema digital seguro no meio rural, nossa campanha sugere ações práticas baseadas nas seguintes diretrizes:</p>
    <ul>
      <li><strong>Verifique sempre:</strong> Cheque a fonte, a data e a autoria antes de repassar qualquer notícia.</li>
      <li><strong>Não exponha:</strong> Nunca divulgue fotos ou filmagens de propriedades vizinhas ou funcionários sem autorização prévia.</li>
      <li><strong>Educação Digital:</strong> Promova palestras, cartilhas e dinâmicas nas comunidades e escolas agrícolas.</li>
    </ul>
    <h3>Mensagem Final da Equipe</h3>
    <div class="quote">"Antes de compartilhar: Pare, pense e verifique!"</div>
  </div>
</section>

<section id="verificador" class="tab">
  <div class="card verifier-card">
    <h2>Verificador de Informação</h2>
    <p>Digite uma notícia, afirmação ou informação sobre drones, privacidade ou tecnologia no campo. A ferramenta analisará o conteúdo e indicará o que pode ser confirmado e o que precisa de cuidado.</p>

    <label for="claim">Informação para verificar</label>
    <textarea id="claim" maxlength="5000" placeholder="Ex.: Todo drone usado no agronegócio pode filmar qualquer propriedade sem autorização."></textarea>
    <div class="counter"><span id="count">0</span>/5000</div>
    <button id="verifyBtn" class="primary">🔎 Verificar informação</button>

    <div id="loading" class="loading hidden">
      <div class="spinner"></div>
      <span>Analisando a informação...</span>
    </div>

    <div id="result" class="result hidden"></div>

    <div class="notice">
      <strong>Importante:</strong> uma IA pode cometer erros. Use o resultado como orientação e confira fontes confiáveis antes de compartilhar uma informação.
    </div>
  </div>
</section>

<section id="quiz" class="tab">
  <div class="card quiz-card">
    <h2>Módulo Interativo: Campanha de Combate aos Erros</h2>
    <p>Responda às perguntas abaixo para testar seus conhecimentos e identificar boatos sobre tecnologias no campo. O sistema validará se sua resposta está correta imediatamente!</p>
    <div id="questions"></div>
    <div class="score" id="score">Respostas Corretas: 0 de 3</div>
  </div>
</section>
</main>

<footer>
  <p>© 2026 - Projeto Desenvolvido pela Equipe "Código da Roça" - CEEP Agrícola</p>
  <p>Referências oficiais: ANPD | ANAC | Gov.br</p>
</footer>

<script src="script.js"></script>
</body>
</html>'''

style_css = r'''*{box-sizing:border-box}
body{margin:0;background:#f3f7f6;color:#123b68;font-family:Arial,Helvetica,sans-serif;font-size:16px}
.hero{background:linear-gradient(115deg,#2563eb,#6195f7);color:#fff;text-align:center;padding:36px 20px 38px}
.hero h1{margin:0 0 20px;font-size:40px;font-weight:700}
.hero p{margin:0 0 25px;font-size:18px}
.meta{font-size:14px}.meta strong{font-weight:700}
.nav{height:55px;background:#fff;display:flex;justify-content:center;align-items:stretch;box-shadow:0 2px 4px #00000012;gap:2px;position:sticky;top:0;z-index:5;overflow-x:auto}
.nav-btn{border:0;background:#fff;color:#172d47;padding:0 18px;font-size:16px;cursor:pointer;white-space:nowrap;border-bottom:3px solid transparent}
.nav-btn:hover{color:#0667d8}.nav-btn.active{color:#0069d9;border-bottom-color:#0877df}
main{max-width:1050px;margin:30px auto 75px;padding:0 16px}
.tab{display:none}.tab.active{display:block}
.card{background:#fff;border-radius:14px;padding:38px 40px;box-shadow:0 3px 12px #00000012;margin-bottom:25px}
h2{margin:0 0 24px;color:#0568c9;font-size:25px;border-left:6px solid #00b894;padding-left:10px}
h3{color:#0068c9;font-size:19px;margin-top:28px}
p{line-height:1.65;margin:0 0 18px}
li{line-height:1.7;margin:10px 0}
table{border-collapse:collapse;width:100%;margin-top:22px}
th{background:#086bc9;color:#fff;text-align:left;padding:15px 12px}
td{padding:14px 12px;border:1px solid #d8d8d8;color:#24486e}
.quote{background:#effbf5;padding:20px;text-align:center;font-size:18px;font-style:italic;margin-top:4px;border-radius:7px;color:#234b69}
.verifier-card{max-width:900px;margin-left:auto;margin-right:auto}
label{display:block;font-weight:700;margin:25px 0 9px}
textarea{width:100%;min-height:145px;border:1px solid #cdd5dd;border-radius:8px;padding:15px;font:16px Arial;resize:vertical;color:#173d66}
textarea:focus{outline:2px solid #79b5f5;border-color:#1774d0}
.counter{text-align:right;color:#718096;font-size:13px;margin:5px 0 16px}
.primary{background:#086bc9;color:#fff;border:0;border-radius:7px;padding:14px 22px;font-size:16px;font-weight:700;cursor:pointer}
.primary:hover{background:#075aa9}.primary:disabled{opacity:.65;cursor:wait}
.loading{display:flex;align-items:center;gap:12px;margin-top:25px;padding:17px;background:#eef7ff;border-radius:8px;color:#075fae;font-weight:700}
.hidden{display:none!important}.spinner{width:20px;height:20px;border:3px solid #cfe5fa;border-top-color:#0871ce;border-radius:50%;animation:spin .8s linear infinite}@keyframes spin{to{transform:rotate(360deg)}}
.result{margin-top:24px;border-radius:9px;padding:22px;border:1px solid #d9e2eb}
.result.true{background:#effbf3;border-color:#92d4aa}.result.false{background:#fff3f2;border-color:#efb1aa}.result.uncertain{background:#fffaf0;border-color:#efd08c}
.result-title{font-size:23px;font-weight:800;margin-bottom:12px}
.result.true .result-title{color:#16803b}.result.false .result-title{color:#c0392b}.result.uncertain .result-title{color:#a56b00}
.result p{margin:8px 0}.truth-box{margin-top:17px;background:#fff;padding:15px;border-radius:7px;border-left:4px solid #08a46c}
.sources{margin-top:18px}.sources a{display:block;color:#0669ca;margin:7px 0;text-decoration:none}.sources a:hover{text-decoration:underline}
.notice{margin-top:20px;background:#f3f6f8;padding:13px 15px;border-radius:7px;color:#536579;font-size:13px;line-height:1.5}
.quiz-card{padding-bottom:40px}.question{background:#fafafa;border:1px solid #e4e4e4;border-radius:8px;padding:20px;margin:18px 0}
.question h4{font-size:17px;margin:0 0 16px;color:#173c65;line-height:1.5}
.option{display:block;width:100%;text-align:left;background:#fff;border:2px solid #ddd;border-radius:7px;padding:13px;margin:10px 0;font-size:15px;cursor:pointer}
.option:hover{border-color:#6da9e9}.option.selected.correct{background:#e9f8ee;border-color:#34a853}.option.selected.wrong{background:#fff0ee;border-color:#d9534f}
.score{background:#e3f4ff;color:#0568c9;text-align:center;font-weight:800;font-size:20px;padding:20px;border-radius:8px;margin-top:20px}
footer{text-align:center;color:#5c7187;font-size:13px;padding:0 20px 40px}footer p{margin:8px}
@media(max-width:700px){.hero h1{font-size:29px}.hero{padding:28px 14px}.hero p{font-size:16px}.meta{line-height:1.8}.nav{justify-content:flex-start}.nav-btn{padding:0 13px;font-size:14px}main{margin-top:18px}.card{padding:25px 20px}h2{font-size:22px}table{font-size:14px}th,td{padding:10px 8px}.verifier-card{padding:25px 18px}}
'''

script_js = r'''const navButtons = document.querySelectorAll(".nav-btn");
const tabs = document.querySelectorAll(".tab");

navButtons.forEach(btn => {
  btn.addEventListener("click", () => {
    navButtons.forEach(b => b.classList.remove("active"));
    tabs.forEach(t => t.classList.remove("active"));
    btn.classList.add("active");
    document.getElementById(btn.dataset.tab).classList.add("active");
    window.scrollTo({top: 0, behavior: "smooth"});
  });
});

const claim = document.getElementById("claim");
const count = document.getElementById("count");
const verifyBtn = document.getElementById("verifyBtn");
const loading = document.getElementById("loading");
const result = document.getElementById("result");

claim.addEventListener("input", () => count.textContent = claim.value.length);

verifyBtn.addEventListener("click", async () => {
  const text = claim.value.trim();
  if (!text) {
    result.className = "result uncertain";
    result.innerHTML = '<div class="result-title">⚠️ Digite uma informação</div><p>Escreva uma afirmação ou notícia para que ela possa ser analisada.</p>';
    result.classList.remove("hidden");
    return;
  }

  verifyBtn.disabled = true;
  loading.classList.remove("hidden");
  result.classList.add("hidden");

  try {
    const response = await fetch("/api/verify", {
      method: "POST",
      headers: {"Content-Type":"application/json"},
      body: JSON.stringify({claim: text})
    });
    const data = await response.json();
    if (!response.ok) throw new Error(data.error || "Não foi possível realizar a análise.");

    const cls = data.verdict === "VERDADEIRA" ? "true" : data.verdict === "FALSA" ? "false" : "uncertain";
    const icon = cls === "true" ? "🟢" : cls === "false" ? "🔴" : "🟡";
    let html = `<div class="result-title">${icon} ${escapeHtml(data.verdict)}</div>`;
    html += `<p><strong>Análise:</strong> ${escapeHtml(data.explanation || "")}</p>`;
    if (data.true_information) {
      html += `<div class="truth-box"><strong>O que é verdadeiro:</strong><br>${escapeHtml(data.true_information)}</div>`;
    }
    if (Array.isArray(data.sources) && data.sources.length) {
      html += `<div class="sources"><strong>📚 Fontes indicadas:</strong>`;
      for (const s of data.sources) {
        const label = escapeHtml(s.title || s.url || "Fonte");
        const url = safeUrl(s.url);
        html += url ? `<a href="${url}" target="_blank" rel="noopener noreferrer">${label}</a>` : `<div>${label}</div>`;
      }
      html += `</div>`;
    }
    result.className = `result ${cls}`;
    result.innerHTML = html;
    result.classList.remove("hidden");
  } catch (err) {
    result.className = "result uncertain";
    result.innerHTML = `<div class="result-title">🟡 Não foi possível concluir</div><p>${escapeHtml(err.message)}</p><p>Confira se a API está configurada na Vercel e tente novamente.</p>`;
    result.classList.remove("hidden");
  } finally {
    loading.classList.add("hidden");
    verifyBtn.disabled = false;
  }
});

function escapeHtml(value) {
  return String(value).replace(/[&<>"']/g, c => ({'&':'&amp;','<':'&lt;','>':'&gt;','"':'&quot;',"'":'&#039;'}[c]));
}
function safeUrl(value) {
  try {
    const u = new URL(value);
    return ["http:","https:"].includes(u.protocol) ? u.href : "";
  } catch { return ""; }
}

const questions = [
  {
    q:"De acordo com o projeto, qual é a principal causa-raiz para o espalhamento do boato dos drones?",
    options:["A) Mau funcionamento mecânico dos drones.","B) A falta de verificação das informações antes de compartilhá-las.","C) O uso exclusivo de inteligência artificial avançada pelas fazendas."],
    answer:1
  },
  {
    q:"Qual impacto social é gerado quando produtores recusam novas tecnologias por medo de boatos?",
    options:["A) Impacto Emocional.","B) Impacto Econômico.","C) Impacto Ambiental."],
    answer:1
  },
  {
    q:"Quais são as três perguntas de ouro recomendadas pela equipe antes de compartilhar uma mensagem?",
    options:["A) Qual o preço do drone? Quem é o fabricante? Ele filma em HD?","B) O vizinho tem internet? A plantação é grande? Quantos robôs existem lá?","C) Quem publicou? Existem provas e outras fontes? Respeita a privacidade?"],
    answer:2
  }
];

let score = 0;
const qContainer = document.getElementById("questions");
questions.forEach((item, qi) => {
  const box = document.createElement("div");
  box.className = "question";
  box.innerHTML = `<h4>${qi+1}. ${item.q}</h4>`;
  item.options.forEach((opt, oi) => {
    const b = document.createElement("button");
    b.className = "option";
    b.textContent = opt;
    b.addEventListener("click", () => {
      if (box.dataset.done) return;
      box.dataset.done = "1";
      if (oi === item.answer) { b.classList.add("selected","correct"); score++; }
      else {
        b.classList.add("selected","wrong");
        box.querySelectorAll(".option")[item.answer].classList.add("selected","correct");
      }
      document.getElementById("score").textContent = `Respostas Corretas: ${score} de 3`;
    });
    box.appendChild(b);
  });
  qContainer.appendChild(box);
});
'''

api_js = r'''const OpenAI = require("openai");

module.exports = async function handler(req, res) {
  if (req.method !== "POST") {
    return res.status(405).json({ error: "Método não permitido." });
  }

  const claim = String(req.body?.claim || "").trim();
  if (!claim) return res.status(400).json({ error: "Informe uma afirmação." });
  if (claim.length > 5000) return res.status(400).json({ error: "A informação é muito longa." });

  if (!process.env.OPENAI_API_KEY) {
    return res.status(500).json({ error: "OPENAI_API_KEY não configurada na Vercel." });
  }

  try {
    const client = new OpenAI({ apiKey: process.env.OPENAI_API_KEY });

    const response = await client.responses.create({
      model: process.env.OPENAI_MODEL || "gpt-5.6-luna",
      tools: [{ type: "web_search" }],
      input: `Você é um verificador educativo de informações para um projeto escolar brasileiro sobre fake news, drones, privacidade e tecnologia no agronegócio.

Analise a afirmação abaixo. Pesquise na web quando necessário. Não invente fontes.
Classifique como exatamente uma destas opções:
VERDADEIRA = a afirmação está de acordo com evidências confiáveis.
FALSA = a afirmação contradiz evidências confiáveis.
INCONCLUSIVA = não há evidência suficiente para confirmar ou negar.

Se for FALSA, explique claramente qual é a informação verdadeira ou mais precisa.
Se houver contexto importante, explique de forma simples.
Priorize fontes oficiais e fontes jornalísticas ou acadêmicas confiáveis.
Não trate uma postagem de rede social como prova por si só.

Retorne SOMENTE um JSON válido neste formato:
{
  "verdict": "VERDADEIRA|FALSA|INCONCLUSIVA",
  "explanation": "explicação curta e clara",
  "true_information": "o que é verdadeiro ou mais preciso; vazio se não necessário",
  "sources": [{"title":"nome da fonte","url":"URL"}]
}

Afirmação para verificar:
${claim}`
    });

    const text = response.output_text || "";
    const match = text.match(/\{[\s\S]*\}/);
    if (!match) throw new Error("A IA não retornou um resultado estruturado.");

    const data = JSON.parse(match[0]);
    if (!["VERDADEIRA","FALSA","INCONCLUSIVA"].includes(data.verdict)) {
      data.verdict = "INCONCLUSIVA";
    }
    data.sources = Array.isArray(data.sources) ? data.sources.slice(0, 6) : [];
    return res.status(200).json(data);
  } catch (error) {
    console.error(error);
    return res.status(500).json({ error: "Erro ao consultar a IA. Tente novamente." });
  }
};
'''

package_json = r'''{
  "name": "maratona-tech-drones-privacidade",
  "version": "1.0.0",
  "private": true,
  "dependencies": {
    "openai": "^5.0.0"
  }
}
'''

readme = r'''# Site Maratona Tech — Código da Roça

Esta versão mantém o conteúdo e o visual mostrado nas capturas e acrescenta:
**Guia de Recomendações → Verificador de Informação → Quiz**.

## Como colocar a IA para funcionar na Vercel

1. Crie um projeto na Vercel a partir desta pasta.
2. A Vercel detectará o arquivo `api/verify.js` como uma função.
3. Em **Settings → Environment Variables**, crie:
   - `OPENAI_API_KEY` = sua chave da API.
   - opcional: `OPENAI_MODEL` = `gpt-5.6-luna`
4. Faça um novo deploy.

A chave fica somente no servidor, dentro da variável de ambiente. Não coloque a chave diretamente no `script.js`.

## Observação
O verificador usa pesquisa na web pela API para procurar evidências e devolve:
- VERDADEIRA
- FALSA
- INCONCLUSIVA

A ferramenta é educativa e deve ser apresentada como apoio à verificação, não como garantia absoluta de verdade.
'''

(root / "index.html").write_text(index_html, encoding="utf-8")
(root / "style.css").write_text(style_css, encoding="utf-8")
(root / "script.js").write_text(script_js, encoding="utf-8")
(root / "api" / "verify.js").write_text(api_js, encoding="utf-8")
(root / "package.json").write_text(package_json, encoding="utf-8")
(root / "README.md").write_text(readme, encoding="utf-8")

zip_path = Path("/mnt/data/maratona-tech-recriado.zip")
with zipfile.ZipFile(zip_path, "w", zipfile.ZIP_DEFLATED) as z:
    for p in root.rglob("*"):
        z.write(p, p.relative_to(root))

print(f"Arquivo criado: {zip_path}")
print("Conteúdo:", [str(p.relative_to(root)) for p in root.rglob("*")])
