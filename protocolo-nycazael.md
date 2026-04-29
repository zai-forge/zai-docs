```markdown
# Protocolo Nycazael
> Integração total: Discord + RetroArch WASM + RetroAchievements + ROM Preservation + Ranking

---

## Visão Geral

O Protocolo Nycazael é a convergência de três projetos do radar:
- **ROM Searcher** — descoberta e acesso via links públicos de preservação
- **Emulador Online** — jogar clássicos com a galera
- **Discord Activities** — jogos embedded no próprio Discord

O objetivo final: usuário entra num canal de voz, clica na Activity,
escolhe um jogo, e está jogando em 10 segundos. Zero configuração local.
Zero download. Zero RetroArch instalado na máquina.

O achievements bot entra de graça porque RetroArch Web já suporta
RetroAchievements nativamente.

---

## Pilares Técnicos

### 1. RetroArch WebAssembly
- RetroArch já tem build oficial em WASM (RetroArch Web Player)
- Roda no browser, logo roda em Discord Activities (iframe)
- Cores (emuladores) disponíveis: Gambatte (GBC), mGBA (GBA),
  Snes9x (SNES), Genesis Plus GX (MD), PCSX-ReARMed (PS1),
  Nestopia (NES), FBNeo (arcade), entre outros
- Performance: GBA/SNES/NES/MD são triviais. PS1 é viável.
  N64 e acima: problemático sem WASM SIMD agressivo.

### 2. Discord Activities
- São web apps em iframe dentro de canal de voz do Discord
- Comunicam com o Discord via Discord Embedded App SDK
- Requerem app registrado no Discord Developer Portal
- Proxy obrigatório: requests externos passam pelo proxy do Discord
  (necessário registrar domínios permitidos: archive.org, myrient.erista.me, etc.)
- Limite de tamanho do iframe: sem limite documentado, mas performance
  depende da máquina do usuário

### 3. ROM Sourcing (Preservação Pública)
- **Internet Archive (archive.org)** — maior acervo público, API aberta
- **Myrient (myrient.erista.me)** — No-Intro e Redump sets completos,
  links diretos sem captcha
- **CDRomance** — ROMs traduzidas e hacks, HTML scraping necessário
- Filosofia: não hospedar nada, apenas linkar para fontes públicas
  já estabelecidas de preservação digital

### 4. RetroAchievements
- API pública em ra.org
- Autenticação por username + API key do usuário
- Endpoints relevantes:
  - `API_GetUserRecentAchievements` — achievements recentes
  - `API_GetUserSummary` — score total, jogos recentes, rank global
  - `API_GetGameInfoAndUserProgress` — progresso por jogo
  - `API_GetAchievementCount` — total de achievements de um jogo
- RetroArch Web Player já suporta login no RA nativamente
- Hardcore mode disponível (sem save states, sem cheats)

### 5. NaviButler (Bot Discord)
- Stack atual: Rust + Serenity + Poise
- Persistência: PostgreSQL
- User metadata já existe: chave `retroachievements_username` por usuário
- Precisa de: background polling task (loop separado do pipeline principal)

---

## Arquitetura do Sistema

```
┌─────────────────────────────────────────────────────┐
│                  Discord Server                      │
│                                                      │
│  ┌──────────────┐    ┌─────────────────────────┐    │
│  │  Canal Voz   │    │     NaviButler Bot       │    │
│  │  + Activity  │    │  (achievements + ranking) │    │
│  └──────┬───────┘    └────────────┬────────────┘    │
│         │                         │                  │
└─────────┼─────────────────────────┼──────────────────┘
          │                         │
          ▼                         ▼
┌─────────────────┐      ┌──────────────────────┐
│  Activity App   │      │  RetroAchievements   │
│  (web frontend) │      │       API            │
│  RetroArch WASM │      └──────────────────────┘
└────────┬────────┘
         │
         ▼
┌─────────────────────────────────┐
│     ROM Backend Service         │
│  (busca, proxy, cache, saves)   │
└────────┬────────────────────────┘
         │
    ┌────┴────┐
    ▼         ▼
archive.org  myrient
```

---

## Componentes Detalhados

### Componente A — Discord Activity Frontend

**Stack sugerida:** Svelte + TypeScript + Discord Embedded App SDK

**Responsabilidades:**
- Tela inicial: busca de jogo por nome ou browse por plataforma
- Exibe thumbnail, nome, tamanho, plataforma
- Botão "Jogar" → carrega ROM no RetroArch WASM
- Login RetroAchievements integrado (username + API key)
- HUD de achievements em tempo real durante o jogo
- Indicador de quem mais está na sessão (multiplayer futuro)
- Save states: salvar/carregar via backend
- Controles customizáveis por usuário (mapeamento de teclado/gamepad)

**Telas:**
1. Home — jogos recentes da comunidade, trending, continuar de onde parou
2. Busca — search com filtros de plataforma, região, ano
3. Biblioteca pessoal — jogos salvos, progresso, saves
4. Leaderboard — ranking global do servidor por RA score
5. Perfil — achievements conquistados, jogos completados, stats

---

### Componente B — ROM Backend Service

**Stack sugerida:** Rust (Axum) ou Node (Fastify)

**Responsabilidades:**
- Busca por nome em múltiplas fontes (Archive.org API, Myrient scraper)
- Retorna: nome normalizado, plataforma, tamanho, URL de download, hash MD5
- Proxy de download: não armazena ROM, faz pipe do source para o cliente
  (necessário por causa do Discord proxy que não permite redirect externo)
- Cache de metadados: evita bater na API do Archive.org a cada busca
  (Redis ou PostgreSQL com TTL)
- Hash verification: confirma integridade antes de servir
- Rate limiting por usuário Discord para evitar abuso

**Fontes e estratégia de busca:**
- Archive.org: `https://archive.org/advancedsearch.php` com filtros
  por mediatype:software e subject:console-name
- Myrient: índice público, scraping do diretório HTML, cache local do índice
- Prioridade: No-Intro (mais limpo) > Redump (dumps de CD) > Archive misc

**ROM sizing por plataforma e viabilidade:**
- NES: ~256KB — trivial
- SNES: ~512KB–4MB — trivial
- GBC/GBA: ~1MB–32MB — tranquilo
- Mega Drive: ~512KB–4MB — trivial
- PS1: ~300MB–700MB (bin/cue) — pesado mas viável com loading progressivo
- N64: ~32MB–64MB — viável
- PS2: 1GB–8GB — inviável para streaming no browser

---

### Componente C — Save System

**O problema:** RetroArch WASM usa localStorage do browser por padrão.
Fecha o Discord, perdeu o save.

**Solução:** Save sync automático via backend

- A cada checkpoint ou a cada N minutos, serializa o save state do RetroArch WASM
- Envia via API para o backend (autenticado pelo Discord user token da Activity)
- Backend armazena em PostgreSQL como BYTEA ou em S3-compatible storage
- Na próxima sessão, baixa o save automaticamente

**Schema sugerido:**
```sql
CREATE TABLE game_saves (
  discord_id    TEXT NOT NULL,
  game_hash     TEXT NOT NULL,  -- MD5 da ROM para identificar o jogo
  platform      TEXT NOT NULL,
  save_type     TEXT NOT NULL,  -- 'sram' | 'state_0' | 'state_1' | ...
  data          BYTEA NOT NULL,
  updated_at    TIMESTAMPTZ DEFAULT NOW(),
  PRIMARY KEY (discord_id, game_hash, save_type)
);
```

---

### Componente D — NaviButler: Módulo RA Achievements

**Novo behavior no NaviButler (background task, não manifest)**

**Onboarding flow:**
```
Usuário: !ra link nycazael123
Bot: Verificando conta... ✅ Conta vinculada!
     Score atual: 4.521 pontos | Rank: #1.204 global
```

- Salva `retroachievements_username` em `user_metadata`
- Salva `retroachievements_apikey` em `user_metadata` (criptografado)
- Primeira sync: puxa score atual como baseline

**Polling task:**
- Background loop a cada 5 minutos
- Para cada usuário com RA vinculado:
  - `GET /API/API_GetUserRecentAchievements.php?u={user}&c=10`
  - Compara com `ra_last_seen_date` no metadata
  - Achievements novos → posta no canal `#achievements`
  - Atualiza `ra_score` no metadata

**Formato do anúncio:**
```
🏆 @Nycazael conquistou um achievement!
🎮 Chrono Trigger (SNES)
⭐ "Dream's End" — Derrote Lavos em 2300 AD
💎 Hardcore | 50 pontos
📊 Score total: 4.571 (+50)
```

**Canal dedicado:**
- `#achievements` — apenas anúncios do bot, sem conversa
- Reações automáticas: 🏆 em cada anúncio para engajamento

---

### Componente E — Sistema de Ranking

**Dois rankings paralelos:**

**1. Ranking RetroAchievements (externo)**
- Baseado no score oficial do RA
- Atualizado a cada sync do polling
- Reflete esforço global do usuário (não só no servidor)

**2. Ranking interno do servidor (local)**
- Pontos ganhos apenas por achievements conquistados enquanto vinculado ao servidor
- Permite ranking justo para quem entrou depois
- Bônus por: primeiro a conquistar um achievement específico no servidor,
  completar um jogo 100%, conquistar em hardcore mode

**Comando de ranking:**
```
!ra top         — top 10 do servidor por score RA
!ra top local   — top 10 por pontos internos
!ra perfil      — seu perfil completo
!ra perfil @user — perfil de outro membro
!ra jogo "Chrono Trigger" — quem no servidor jogou, progresso de cada um
```

---

## Roadmap por Fase

### Fase 0 — Fundação (2–3 semanas)
- [ ] Registrar app no Discord Developer Portal (Activity)
- [ ] Testar RetroArch Web Player standalone (fora do Discord)
- [ ] Confirmar que cores essenciais rodam no WASM com performance aceitável
- [ ] Testar fetch de ROM do Archive.org e Myrient via browser (CORS, tamanho)
- [ ] Criar conta de teste no RetroAchievements, validar todos os endpoints da API
- [ ] Decisão de stack do backend (Rust/Axum vs Node)

### Fase 1 — MVP Jogável (4–6 semanas)
- [ ] Activity funcional com RetroArch WASM embutido
- [ ] Busca básica de ROMs (só Archive.org por enquanto)
- [ ] Proxy de download funcionando dentro do Discord proxy
- [ ] Login RetroAchievements dentro da Activity
- [ ] Save states salvos no backend (sem perder progresso)
- [ ] Deploy mínimo: activity servida de domínio próprio

### Fase 2 — Integração Bot (3–4 semanas)
- [ ] Comando `!ra link` para vincular conta RA
- [ ] Background polling no NaviButler
- [ ] Anúncios de achievements no canal dedicado
- [ ] Ranking básico por RA score
- [ ] Comando `!ra top` e `!ra perfil`

### Fase 3 — Expansão (ongoing)
- [ ] Suporte a Myrient como segunda fonte
- [ ] Multiplayer (RetroArch Netplay via WebRTC — pesquisa necessária)
- [ ] Ranking interno com sistema de pontos próprio
- [ ] Bônus e eventos: torneios de speedrun, jogo do mês
- [ ] Tela de perfil dentro da Activity com histórico de jogos
- [ ] Notificações in-game de achievements de outros membros do servidor
- [ ] Biblioteca pessoal: marcar favoritos, criar listas

---

## Riscos e Mitigações

### Performance do WASM
**Risco:** Usuários com máquinas fracas sofrem com emulação pesada
**Mitigação:** Limitar plataformas no MVP (GBA, SNES, NES, MD).
PS1 como beta opt-in. N64 como experimento. PS2 fora do escopo.

### Discord Proxy e CORS
**Risco:** Discord pode bloquear fetch para Archive.org/Myrient
**Mitigação:** Todo request externo passa pelo backend próprio primeiro.
A Activity nunca faz fetch direto para fontes externas.

### Tamanho das ROMs
**Risco:** PS1 de 600MB é impraticável para loading completo
**Mitigação:** Streaming parcial — RetroArch WASM pode começar a rodar
com os primeiros chunks enquanto o resto baixa. Pesquisa necessária.

### Sustentabilidade da API do RA
**Risco:** RetroAchievements pode mudar ou limitar a API
**Mitigação:** Cachear respostas, respeitar rate limits, ter fallback
gracioso (ranking para de atualizar mas não quebra o bot)

### Zona cinza legal
**Risco:** ROMs de jogos com publisher ativo
**Mitigação:** Mesma filosofia do Traditions.txt — não hospedar, não lucrar,
apenas linkar para preservação pública já estabelecida. Archive.org tem
proteção legal mais estabelecida para esse tipo de conteúdo.

### Engajamento humano
**Risco:** Ninguém usa se precisar criar conta no RA
**Mitigação:** Modo sem RA funcional (joga sem achievements).
RA é opt-in, não bloqueante. A experiência de jogar no Discord
já é o hook principal — o RA é bônus.

---

## Ideias Futuras (Backlog Livre)

- **Jogo do Mês** — bot anuncia um jogo, todos jogam, ranking especial no fim do mês
- **Speedrun Mode** — timer integrado, leaderboard por jogo/categoria
- **Screenshot automático** — captura tela a cada achievement, posta junto no anúncio
- **Replay sharing** — salvar replay de momentos específicos (WASM state rewind)
- **Torneios bracket** — NaviButler gerencia chaves de torneio por jogo
- **Daily challenge** — achievement específico por dia, primeiros 3 a completar ganham cargo
- **ROM recommendations** — LLM do NaviButler sugere jogos baseado no histórico do usuário no RA
- **Integração com Twitch** — detecta quando membro está streamando o jogo, anuncia no servidor
- **Custom achievements** — achievements criados pelo servidor, fora do RA (engine própria)
- **Coop sessions** — canal de voz + activity + netplay ao mesmo tempo
- **Hall of Fame** — parede permanente dos primeiros a completar jogos 100% no servidor
- **Patch support** — aplicar patches de tradução/hack em ROMs antes de servir (xdelta3 em WASM)
- **Savestate sharing** — compartilhar save state com outro membro ("tenta esse boss aqui")
- **Achievement hunting mode** — bot sugere próximo achievement mais fácil de conquistar no jogo atual
- **Retro radio** — trilha sonora de jogos clássicos tocando no canal de voz enquanto jogam
- **Playtime tracking** — quanto tempo cada membro passou em cada jogo pela Activity
- **Cargo por milestone** — 1000 pontos RA = cargo especial no servidor
- **Wishlist de ROMs** — usuário pede um jogo que não está indexado ainda, bot adiciona na fila
- **Cheat code database** — opcional, disponível mas desativa achievements (fair play)
- **Controller profile sync** — salvar mapeamento de controles por usuário, carrega automático

---

## Stack Final Sugerida

| Camada | Tecnologia | Motivo |
|---|---|---|
| Activity Frontend | Svelte + TypeScript | Já é o padrão do Claude.gg (Svelte) |
| RetroArch | WASM (build oficial) | Única opção viável no browser |
| ROM Backend | Rust + Axum | Consistência com o resto do stack |
| Save Storage | PostgreSQL (BYTEA) | Já existe, sem nova dependência |
| Cache de metadados | PostgreSQL + TTL | Simples, sem Redis por enquanto |
| Bot (achievements) | NaviButler existente | Extensão natural, não novo bot |
| Hosting Activity | VPS própria ou Cloudflare Pages | Activity precisa de domínio HTTPS |

---

## Nome

**Nycazael** — nome do protocolo, nome da Activity, nome do canal.
O Judge que trouxe a ideia que batizou o projeto.
```
