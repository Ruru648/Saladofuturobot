manifest.json:

{
  "manifest_version": 3,
  "name": "Sala do Futuro Bot",
  "version": "1.0",
  "description": "Responde automaticamente questões da Sala do Futuro",
  "permissions": ["activeTab", "scripting"],
  "action": {
    "default_popup": "popup.html",
    "default_icon": "icon.png"
  },
  "background": {
    "service_worker": "background.js"
  },
  "content_scripts": [
    {
      "matches": ["https://saladofuturo.educacao.sp.gov.br/*"],
      "js": ["content.js"]
    }
  ]
}

popup.html:

<!DOCTYPE html>
<html>
  <head>
    <title>Sala do Futuro Bot</title>
  </head>
  <body>
    <button id="ativar">Ativar Bot</button>
    <button id="atualizar">Atualizar Banco</button>
    <script src="popup.js"></script>
  </body>
</html>

popup.js:

document.getElementById('ativar').addEventListener('click', () => {
  chrome.tabs.query({ active: true, currentWindow: true }, (tabs) => {
    chrome.scripting.executeScript({
      target: { tabId: tabs[0].id },
      function: executarBot
    });
  });
});

document.getElementById('atualizar').addEventListener('click', () => {
  chrome.storage.local.set({ bancoRespostas: bancoAtualizado });
});

function executarBot() {
  // Essa função será injetada no site
  chrome.storage.local.get('bancoRespostas', ({ bancoRespostas }) => {
    const perguntas = document.querySelectorAll('.questao');
    perguntas.forEach((questao) => {
      const texto = questao.innerText.toLowerCase();
      const resposta = bancoRespostas.find(r => texto.includes(r.pergunta));
      if (resposta) {
        const alternativas = questao.querySelectorAll('input[type="checkbox"], input[type="radio"]');
        alternativas.forEach((alt) => {
          if (alt.nextSibling?.textContent.includes(resposta.alternativa)) {
            alt.click();
          }
        });
      }
    });
  });
}

const bancoAtualizado = [
  { pergunta: "pares de ângulos", alternativa: "A" },
  { pergunta: "ângulos \u03b1 e \u03b4", alternativa: "adjacentes" },
  { pergunta: "soma das medidas dos ângulos \u03b1 e \u03b3", alternativa: "B" },
  { pergunta: "ordene os passos", alternativa: "Verificar que os ângulos alfa e beta são complementares" }
];

content.js:

// Conteúdo vazio ou usado para triggers automáticos

background.js:

// Pode ficar vazio, pois estamos usando popup.js para ativar o script

icon.png: (Adicione um ícone de 128x128 para a extensão)

