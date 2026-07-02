# 👵 Granny's Kitchen — Chatbot de Culinária

Um chatbot híbrido (regras + IA) com a persona da "Granny Rosa": receitas ao vivo do
TheMealDB, substituições de ingredientes, dicas de cozinha e conversa livre com o Claude.
Criado como experimento para sala de aula.

## 🧠 Arquitetura

| Camada | O que faz | Custo |
|---|---|---|
| **1. Regras** (`app.js`) | Detecta intenções: saudações, buscar receita por nome/ingrediente/categoria/país, substituições, dicas, "surprise me" | Grátis |
| **2. TheMealDB** (API pública) | 700+ receitas com foto, ingredientes, modo de preparo e vídeo do YouTube | Grátis |
| **3. Claude** (`api/chat.js`) | Perguntas livres, follow-ups sobre a receita mostrada E **receitas próprias estruturadas** (JSON: título, tempo, porções, passos) quando o TheMealDB não tem — sempre baseadas em pratos clássicos reais, sem inventar links/marcas | ~centavos |

**Perfil do usuário:** no primeiro pedido de receita, a vovó pergunta dieta
(vegetariana, vegana, sem glúten…), alergias (multi-seleção) e aversões.
Tudo é salvo (localStorage) e aplicado em TODAS as camadas: resultados são
filtrados, receitas com ingrediente proibido vêm com alerta + substituição,
e a IA recebe o perfil como regra rígida. Vídeos e buscas usam links REAIS
(YouTube/Google search) — nunca URLs inventadas.

Se a camada 3 estiver fora do ar (sem chave, sem créditos), as camadas 1 e 2
continuam funcionando — o bot nunca quebra na demo.

## 🚀 Como publicar (Vercel) — ~15 minutos

### Passo 1 — Suba o projeto no GitHub
1. Crie uma conta em https://github.com (se ainda não tiver)
2. Crie um repositório novo (ex: `grannys-kitchen`), pode ser privado
3. Envie estes arquivos para o repositório (pela própria página do GitHub:
   **Add file → Upload files** — arraste `index.html`, `style.css`, `app.js`,
   a pasta `api/` e este README)

### Passo 2 — Importe na Vercel
1. Crie uma conta em https://vercel.com (entre com o GitHub — 1 clique)
2. **Add New → Project** → selecione o repositório `grannys-kitchen`
3. Não mude nenhuma configuração de build (é um site estático + 1 função)

### Passo 3 — Adicione sua chave da Anthropic (ANTES do deploy)
1. Na tela de import, abra **Environment Variables**
2. Adicione:
   - **Name:** `ANTHROPIC_API_KEY`
   - **Value:** sua chave criada em https://console.anthropic.com (começa com `sk-ant-`)
3. Clique em **Deploy** 🎉

A Vercel te dará um link tipo `https://grannys-kitchen.vercel.app` —
é esse que você compartilha com a turma. Funciona pra qualquer pessoa,
sem login, no celular ou notebook.

> ⚠️ **Nunca** coloque a chave `sk-ant-...` dentro de `app.js`, `index.html`
> ou em qualquer arquivo do repositório. Ela vive só na variável de ambiente.

### Testar localmente (opcional)
```bash
npm i -g vercel
cd grannys-kitchen
vercel dev          # pede login na 1ª vez
# defina a chave local: crie um arquivo .env com ANTHROPIC_API_KEY=sk-ant-...
```
Sem a Vercel CLI, dá pra abrir o `index.html` direto no navegador:
as camadas 1 e 2 funcionam; só o cérebro de IA fica off.

## 💰 Custo estimado

Modelo usado: **Claude Haiku 4.5** (US$ 1/milhão de tokens de entrada,
US$ 5/milhão de saída). Uma aula com 30 pessoas × 10 mensagens ≈ **US$ 0,10–0,30**.
Os US$ 5 mínimos de crédito duram dezenas de demos.

## 🧪 Frases para testar na demo

- Primeiro pedido → onboarding: dieta, alergias, aversões (botões)
- `Surprise me!` → receita aleatória com foto e vídeo (respeitando o perfil)
- `I have chicken` → lista de pratos para escolher
- `Something Italian` / `Show me a dessert`
- `Recipe for lasagna`
- `What can I use instead of eggs?` → camada de regras
- `rice` (ou `I have rice`) → a vovó pergunta o tipo: jasmine? basmati? arbóreo? — cada resposta traz a dica de cozimento certa e receitas adequadas
- `basmati rice` → ela reconhece o tipo direto, sem perguntar de novo
- `sauces` / `which sauce for steak` → guia de molhos com mini-receita e harmonização
- `how do I cook mushrooms` → método da vovó + pratos pra praticar
- `invent a dish with rice, beans and coconut` → receita estruturada da IA (card com passos, tempo, porções, link real de busca no YouTube)
- `my preferences` → refazer o perfil a qualquer momento
- `Give me a tip` → dicas da vovó
- `Why did my rice come out sticky?` → camada Claude (IA)
- `What's the capital of France?` → guardrail: a vovó só fala de cozinha 😄
- Depois de abrir uma receita: `Can I make this without wine?` → IA com contexto

## 📁 Estrutura

```
grannys-kitchen/
├── index.html      # estrutura da página
├── style.css       # visual: toalha xadrez, cards de fichário
├── app.js          # motor híbrido (regras + TheMealDB + chamada à IA)
├── api/
│   └── chat.js     # função serverless → API da Anthropic (chave protegida)
└── README.md
```

## 🙏 Créditos

- Receitas e fotos: [TheMealDB](https://www.themealdb.com) (API gratuita, chave de teste "1" para uso educacional)
- IA: [Claude Haiku 4.5](https://www.anthropic.com) via API da Anthropic
- Fontes: Yeseva One, Caveat e Nunito (Google Fonts)
