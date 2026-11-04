# 🎙️ Radar Panorama — podcast diário de notícias

Mesmo pipeline do `radar-tech` (RSS → Gemini → edge-tts → Telegram), rodando de
graça no GitHub Actions, mas com outro foco: um panorama do dia em política,
economia, mundo, esportes, ciência e fé.

## O que tem no episódio

Bate-papo de ~10 a 12 minutos entre dois apresentadores (Ana, Clara e Leo, em
rodízio), com os blocos:

| Bloco | Tag no `feeds.txt` | Obrigatório? |
|---|---|---|
| 🏛️ Política (Brasil, MBL, Partido Missão) | `politica` | sim |
| 🌍 Geopolítica | `geopolitica` | sim |
| 💹 Economia & Mercados (Brasil e mundo, bolsas, ações, ETFs, câmbio) | `economia` | sim |
| 💼 Trabalho & Renda (empregos novos, ganho por hora, onde está o dinheiro) | `trabalho` | quando houver |
| ⚽ Esportes (Santos FC e Manchester City) | `esportes` | sim |
| 📍 Rio Claro & Região | `local` | quando houver |
| 🔬 Ciência & Sociedade (tecnologia, infraestrutura, saúde, educação, segurança, filosofia, comportamento) | `ciencia` | sim |
| ✝️ Fé & Teologia (teologia pentecostal, comentários bíblicos, arqueologia bíblica) | `fe` | quando houver |
| ⚡ Rodada rápida + 📖 Termo do dia | — | sim |

Regras que o roteiro segue:

- **Política e geopolítica:** imparcialidade total. Fatos, posições atribuídas a
  quem as disse, sem os apresentadores tomarem partido.
- **Economia:** informa, não aconselha. Nunca diz pra comprar, vender ou investir.
- **Fé:** tom de respeito; interpretações atribuídas a quem as defende.

## Estrutura do repo

```
radar-panorama/
├── .github/workflows/news.yml   ← agendamento + execução
├── main.py
├── feeds.txt                    ← fontes, cada uma com a tag do bloco
├── apresentadores.json          ← elenco, vozes e rodízio
├── pronuncia.txt                ← como a voz fala siglas e nomes difíceis
├── LICENSE
├── historico.json               ← criado/atualizado pelo bot (não edite)
└── requirements.txt
```

## Setup

1. **Bot novo no Telegram:** fale com o [@BotFather](https://t.me/BotFather),
   `/newbot`, e guarde o token. Mande uma mensagem qualquer pro bot e pegue seu
   `chat_id` em `https://api.telegram.org/bot<TOKEN>/getUpdates`.

2. **Cadastre os secrets:**
   Repo → **Settings → Secrets and variables → Actions → New repository secret**

   | Nome | Valor |
   |---|---|
   | `GEMINI_API_KEY` | chave do https://aistudio.google.com/apikey |
   | `TELEGRAM_BOT_TOKEN` | token do bot novo |
   | `TELEGRAM_CHAT_ID` | seu chat_id |

3. **Teste sem esperar o horário:** aba **Actions** → "Radar Panorama Diário" →
   **Run workflow**. Em ~1 min o áudio chega no Telegram.

4. Pronto. Todo dia ~6h30 (BRT) ele roda sozinho; se falhar, tenta de novo às
   9h15 e 12h15.

## Editar fontes

Edite o `feeds.txt` e dê push. O 4º campo de cada linha é a tag do bloco
(`URL | Nome | janela | tag`). Formatos aceitos estão no topo do arquivo: RSS,
busca no Google Notícias (`gnews: ...`) e sitemap. Fontes que postam pouco (fé,
trabalho) usam janela maior (`72h`, `168h`); o histórico impede notícia repetida.

Dica: `gnews:` é o jeito mais fácil de cobrir um tema novo, ex.:
`gnews: "Santos FC" | Nome | 24h | esportes`.

## Observações

- **Horário não é exato.** O cron do Actions atrasa na fila; o gatilho é às 6h15
  (BRT) pra entrega cair por volta das 6h30.
- **Dê `git pull` antes de mexer no repo.** O bot commita o `historico.json` todo
  dia; sem o pull, seu push é recusado.
- **Sem notícias repetidas.** Notícias e assuntos dos últimos 3 dias entram no
  prompt como "já falado"; pra zerar a memória, apague o `historico.json`.
- **Inatividade desativa o cron.** O commit diário do histórico mantém o repo vivo.

## Licença

[MIT](LICENSE) © 2026 heldemfilipe
