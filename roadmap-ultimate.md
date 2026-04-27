# 🧠 ROADMAP v2 (Atualizado)

> **Lema:**
> *Servidor bom não é sobre conversar.
> É sobre ter o que fazer lá dentro.*

---

# ⚙️ FASE 0 — ALINHAR O ORGANISMO (ATUAL)

### Objetivo:

Organizar comportamento, onboarding e estrutura com o que já existe.

---

## ✅ O que já temos

* Botman (arquitetura modular com manifestos)
* Whisper (tradução elegante via botão + DM)
* Stack sólida (Rust + Postgres)
* Estrutura inicial de canais definida

---

## 🔥 O que precisa ser feito AGORA

### 1. 🤖 Manifesto de onboarding (`#welcome`)

Objetivo:

* transformar entrada em experiência
* classificar usuário
* iniciar loop de engajamento

---

### 2. 🌍 Integração Whisper + Botman

Botman deve:

* sugerir uso do 🌍
* reagir a frustração com tradução
* alterar configs do Whisper (opt-in/out automático)

---

### 3. 🧠 Detecção de idioma (core)

* detectar no `message_create`
* salvar no DB
* reutilizar em tudo (Whisper + Botman)

---

### 4. 🧱 Base de dados estabilizada

Garantir:

* users
* message_cache
* translation_cache
* user_translation_settings

---

### 5. ⚠️ ReactionAdd implementado

Desbloqueia:

* interação futura
* economia
* feedback loops

---

## 📊 CHECKPOINT FASE 0

Você venceu se:

* onboarding funciona sozinho
* usuários entram e interagem no `#welcome`
* tradução funciona sem atrito
* sistema está estável (sem bugs críticos)

---

# 🧩 FASE 1 — CANAIS COM PROPÓSITO

Agora sim vamos organizar o servidor.

---

## 🧠 Estrutura atual (validada)

### 🚪 Portal

* `#rules`
* `#welcome`

👉 entrada controlada ✔️

---

### 🏛️ The Council

* `#sala-da-justiça`
* `#sala-de-testes`

👉 uso interno / staff ✔️

---

### 🏠 Home (privado)

* `#living-room`
* `arcade-room`

👉 núcleo social ✔️

---

### 🍓 Onlyfuns (público principal)

* `#general-chat` ⚠️ (candidato a virar sistema)
* `#memes`
* `#ai-chat`
* `#bot-games`
* `streaming`
* `radio`

👉 aqui mora o produto

---

## 🎯 Problema atual

> Muitos canais → pouca densidade

---

## 🔥 Ação da Fase 1

### 1. 🔄 Transformar `#general-chat`

👉 futuro:

> `#game-drops` (ou híbrido inicialmente)

---

### 2. 🎯 Definir papel de cada canal

Você vai precisar responder:

* “por que alguém entra aqui?”
* “o que dá pra fazer aqui?”

---

### 3. 🤖 Criar manifestos por canal

Exemplo:

* `general_chat_manifest`
* `memes_manifest`
* `ai_chat_manifest`

---

## 📊 CHECKPOINT FASE 1

* canais têm comportamento claro
* Botman interage diferente em cada um
* usuários entendem “o que fazer”

---

# 🔥 FASE 2 — LOOP DE INTERAÇÃO

Agora começa o vício 😏

---

## 🧩 Sistemas

### 1. 💰 Shitcoin (MVP)

* ganhar por interação
* gastar em coisas simples

---

### 2. 🎯 Missões leves

* reagir
* comentar
* explorar canais

---

### 3. 🤖 Botman provocador

* chama atenção
* gera discussão
* mantém fluxo vivo

---

### 4. 🌍 Whisper integrado ao loop

* recompensa uso
* incentiva interação cross-language

---

## 📊 CHECKPOINT FASE 2

* usuários interagem sem você
* começam a “jogar” com o sistema
* voltam espontaneamente

---

# 🎮 FASE 3 — GAMIFICAÇÃO

---

## 🃏 Sistema de packs

* coleção simples
* raridade
* expectativa

---

## ⚔️ Interação entre usuários

* troca
* comparação
* competição leve

---

## 🎲 Eventos

* aleatórios
* controlados pelo Botman

---

# 🌐 FASE 4 — AQUISIÇÃO

---

## 🎯 Posicionamento

Você NÃO é:

> “um servidor de Discord”

Você é:

> “um servidor interativo com sistemas vivos + tradução automática”

---

## 📢 Estratégia

* postar em comunidades
* mostrar features únicas (Whisper principalmente)

---

## 🚪 Onboarding refinado

* entra → interage em <1 min
* já ganha algo
* já entende o sistema

---

# 🧬 FASE 5 — EXPANSÃO

---

## 🤖 Botman avançado

* memória longa
* personalidade forte
* decisões contextuais

---

## 🌍 Whisper avançado

* auto modes refinados
* tradução contextual
* aprendizado de preferência

---

## 🧩 Sistemas únicos

* achievements
* integração externa
* features exclusivas

---

# 🧠 PRINCÍPIOS ATUALIZADOS

1. **Sem ação → canal morto**
2. **Tudo precisa gerar interação**
3. **Tradução é invisível, não intrusiva**
4. **Botman não responde — ele vive**
5. **Menos canais, mais densidade**
6. **Feature boa = comportamento emergente**

---

# 🎯 PRÓXIMO PASSO

👉 **curadoria canal por canal**

E depois:

👉 **criar manifestos reais começando por:**

* `welcome`
* `general-chat`
