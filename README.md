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

## Estilo do podcast

Por padrão o áudio é um **bate-papo entre dois dos três apresentadores** do
`apresentadores.json`, e a dupla muda a cada 2 dias:

| | Voz | Personalidade |
|---|---|---|
| **Ana** | Francisca | analítica, contexto histórico, separa fato de especulação |
| **Clara** | Thalita | bem-humorada, irônica, tom de conversa de café |
| **Leo** | Antonio | brincalhão, energia alta, foco no que muda no bolso e na rotina |

As três duplas se revezam por igual: Ana+Leo, Clara+Leo e Ana+Clara. Quem
explica os conceitos é sempre uma das mulheres; quem conduz abre com a vinheta
("tá no ar a edição de hoje do Radar Panorama...") e depois os dois se
apresentam pelo nome. A dupla depende só da data: a repescagem do mesmo dia cai
na mesma dupla, e o `historico.json` guarda quem apresentou cada episódio.

- **Trocar ou adicionar alguém:** edite o `apresentadores.json` (nome sem
  acento, `voz`, `rate`, `pitch`, `persona`). `rodizio_dias` muda a frequência
  da troca e `dupla_feminina_a_cada` a da dupla feminina (0 desliga). Na
  persona, descreva o jeito da pessoa **sem frases prontas entre aspas**: o
  Gemini copia exemplo literal todo dia e vira bordão.
- **Forçar uma dupla** (teste): variável `DUPLA`, ex.: `DUPLA=Clara,Leo`.
- **Narrador único:** descomente `PODCAST_STYLE: solo` no `news.yml`.
- **Vinheta:** variável `VINHETA` troca a frase de abertura.

### Diálogo mais humano

O prompt tem uma seção "Soar humano" (falas de tamanhos variados com reações
curtas, interrupções, hesitação, pouca exclamação, pouco uso do nome do
colega, transições variadas, sem formato de entrevista) e não tem bordão fixo
de abertura, despedida ou pergunta. O script também procura nos episódios
anteriores os **começos de frase que se repetiram** e manda essa lista no prompt
como proibida.

A cada episódio o log do Actions mostra uma linha `[DIÁLOGO]` com as métricas
(falas curtas, longas, exclamações, interrupções, frases gastas) e um `[AVISO]`
se o roteiro voltou a soar como locutor. O aviso não derruba o episódio: é só
um indicador pra ajustar o prompt.

### Pausas e pronúncia

- O silêncio longo que o edge-tts coloca no fim de cada fala é cortado e
  substituído por pausas curtas (reação rápida emenda quase colada; troca de
  apresentador ~0,4 s; troca de bloco ~1 s).
- `pronuncia.txt` diz como a voz fala siglas e nomes difíceis (ex.:
  `ETF = i-tê-éfe`). Ouviu um erro? Adicione uma linha e dê push.
- No Telegram chega, além do áudio, uma mensagem com as notícias por bloco, com
  link pra matéria, e o termo do dia.

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
   `chat_id` em `https://api.telegram.org/bot<TOKEN>/getUpdates`: é o número
   de `"chat":{"id": ...}` (o seu, de pessoa). **Não** use o número que vem
   antes dos `:` no token: esse é o id do próprio bot e dá
   `403: Forbidden: the bot can't send messages to the bot`.

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
