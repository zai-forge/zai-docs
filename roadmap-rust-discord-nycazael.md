# 🦀 Roadmap Rust + Discord Bot — Para Nycazael

> **Feito especialmente pra você, Nycazael!**
> Este guia vai te levar do zero absoluto até ter um bot de Discord rodando no seu Linux Mint, escrito em Rust. Vai com calma, segue a ordem, e não pula etapas.

---

## 📋 Índice

1. [Preparando o Ambiente](#-etapa-1-preparando-o-ambiente)
2. [Instalando o Rust](#-etapa-2-instalando-o-rust)
3. [Fundamentos de Rust](#-etapa-3-fundamentos-de-rust)
4. [Criando o Projeto do Bot](#-etapa-4-criando-o-projeto-do-bot)
5. [Configurando o Bot no Discord](#-etapa-5-configurando-o-bot-no-discord)
6. [Construindo o Bot com Serenity](#-etapa-6-construindo-o-bot-com-serenity)
7. [Próximos Passos](#-próximos-passos)

---

## 🛠 Etapa 1: Preparando o Ambiente

Antes de instalar o Rust, o Linux Mint precisa ter algumas ferramentas essenciais de compilação. Sem elas, vários pacotes simplesmente não vão compilar.

Abra o terminal (`Ctrl + Alt + T`) e rode:

```bash
sudo apt update && sudo apt upgrade -y
```

Agora instala o conjunto de ferramentas de build:

```bash
sudo apt install -y \
  build-essential \
  cmake \
  clang \
  libclang-dev \
  pkg-config \
  libssl-dev \
  git \
  curl
```

**Por que cada um?**

| Pacote | Para que serve |
|---|---|
| `build-essential` | GCC, make e outras ferramentas base de compilação |
| `cmake` | Sistema de build usado por muitas dependências nativas |
| `clang` | Compilador C/C++ alternativo; várias crates Rust precisam dele |
| `libclang-dev` | Headers do clang; necessário para `bindgen` e crates que fazem FFI |
| `pkg-config` | Ajuda o compilador a encontrar bibliotecas do sistema |
| `libssl-dev` | Necessário para conexões HTTPS (o bot precisa disso pra falar com a API do Discord) |
| `git` | Controle de versão (você vai querer isso) |
| `curl` | Usado para baixar o instalador do Rust |

Verifica se o cmake e o clang foram instalados corretamente:

```bash
cmake --version
clang --version
```

Se os dois imprimirem versões, tá tudo certo. Segue em frente.

---

## 🦀 Etapa 2: Instalando o Rust

Rust se instala através do **rustup**, que é o gerenciador oficial de versões e toolchains.

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

O instalador vai te perguntar algumas coisas. Escolhe a opção **1 (padrão)** e aperta Enter.

Quando terminar, recarrega o shell para o PATH ser atualizado:

```bash
source ~/.bashrc
```

Verifica a instalação:

```bash
rustc --version
cargo --version
```

Você deve ver algo como:
```
rustc 1.xx.x (...)
cargo 1.xx.x (...)
```

**O que é cada um?**

- **`rustc`** → o compilador do Rust
- **`cargo`** → o gerenciador de pacotes e build system (você vai usar ele o tempo todo)

---

## 📚 Etapa 3: Fundamentos de Rust

Antes de partir pro bot, você precisa entender o básico da linguagem. Rust tem conceitos únicos que não existem em Python ou JavaScript — entender eles vai te salvar de muita dor de cabeça.

### 3.1 — Conceitos Essenciais (estude nessa ordem)

#### ① Variáveis e Mutabilidade

```rust
fn main() {
    let nome = "Nycazael";        // imutável por padrão
    let mut contador = 0;         // mut = pode ser modificado

    contador += 1;
    println!("Olá, {}! Contador: {}", nome, contador);
}
```

> **Dica:** Em Rust, variáveis são imutáveis por padrão. Você precisa escrever `mut` explicitamente para poder mudar o valor.

#### ② Tipos de Dados

```rust
fn main() {
    let inteiro: i32 = 42;
    let flutuante: f64 = 3.14;
    let booleano: bool = true;
    let texto: &str = "olá";
    let string: String = String::from("olá também");

    println!("{} {} {} {} {}", inteiro, flutuante, booleano, texto, string);
}
```

#### ③ Ownership (o coração do Rust)

Este é o conceito mais importante e mais diferente de Rust. Cada valor tem **um único dono**. Quando o dono sai de escopo, o valor é destruído automaticamente — sem garbage collector.

```rust
fn main() {
    let s1 = String::from("olá");
    let s2 = s1;             // s1 foi "movido" para s2
    // println!("{}", s1);   // ERRO! s1 não existe mais

    println!("{}", s2);      // isso funciona
}
```

Para "emprestar" sem mover, use **referências**:

```rust
fn imprimir(texto: &String) {
    println!("{}", texto);
}

fn main() {
    let s = String::from("Nycazael");
    imprimir(&s);            // passa uma referência
    println!("{}", s);       // s ainda existe aqui
}
```

#### ④ Structs e Implementações

```rust
struct Usuario {
    nome: String,
    nivel: u32,
}

impl Usuario {
    fn apresentar(&self) {
        println!("Sou {} e meu nível é {}", self.nome, self.nivel);
    }
}

fn main() {
    let u = Usuario {
        nome: String::from("Nycazael"),
        nivel: 99,
    };
    u.apresentar();
}
```

#### ⑤ Enums e Pattern Matching

```rust
enum Comando {
    Ping,
    Ajuda,
    Desconhecido(String),
}

fn processar(cmd: Comando) {
    match cmd {
        Comando::Ping => println!("Pong!"),
        Comando::Ajuda => println!("Lista de comandos..."),
        Comando::Desconhecido(nome) => println!("Comando '{}' não encontrado", nome),
    }
}
```

#### ⑥ Tratamento de Erros com Result

Rust não usa exceções. Funções que podem falhar retornam `Result<T, E>`:

```rust
use std::fs;

fn main() {
    match fs::read_to_string("arquivo.txt") {
        Ok(conteudo) => println!("Conteúdo: {}", conteudo),
        Err(erro) => println!("Erro ao ler: {}", erro),
    }
}
```

O operador `?` é um atalho que propaga o erro automaticamente (você vai ver isso muito no código do bot):

```rust
fn ler_arquivo() -> Result<String, std::io::Error> {
    let conteudo = fs::read_to_string("arquivo.txt")?;
    Ok(conteudo)
}
```

#### ⑦ Async/Await

O bot vai usar programação assíncrona. O conceito básico:

```rust
use tokio;

#[tokio::main]
async fn main() {
    let resultado = buscar_dados().await;
    println!("{}", resultado);
}

async fn buscar_dados() -> String {
    // simula uma operação que leva tempo
    String::from("dados chegaram!")
}
```

### 3.2 — Recursos para Aprender

- 📖 **[The Rust Book (PT-BR)](https://rust-br.github.io/rust-book-pt-br/)** — a bíblia oficial do Rust em português
- 🏋️ **[Rustlings](https://github.com/rust-lang/rustlings)** — exercícios interativos no terminal
- 🌐 **[Rust by Example](https://doc.rust-lang.org/rust-by-example/)** — exemplos práticos de cada conceito

> **Sugestão:** Passa pelo menos uns 3-5 dias estudando os conceitos acima antes de partir pro bot. Ownership e lifetimes vão parecer estranhos no começo — é normal. Continua.

---

## 📦 Etapa 4: Criando o Projeto do Bot

Agora sim! Vamos criar o projeto com o Cargo:

```bash
cargo new meu-bot-discord
cd meu-bot-discord
```

Isso cria a seguinte estrutura:

```
meu-bot-discord/
├── Cargo.toml       ← manifesto do projeto (dependências ficam aqui)
└── src/
    └── main.rs      ← código principal
```

### Adicionando as Dependências

Abre o `Cargo.toml` com um editor de texto e substitui o conteúdo por:

```toml
[package]
name = "meu-bot-discord"
version = "0.1.0"
edition = "2021"

[dependencies]
serenity = { version = "0.12", features = ["client", "gateway", "model", "cache"] }
tokio = { version = "1", features = ["macros", "rt-multi-thread"] }
dotenv = "0.15"
tracing = "0.1"
tracing-subscriber = "0.3"
```

**O que é cada um?**

| Crate | Função |
|---|---|
| `serenity` | Framework principal para bots de Discord em Rust |
| `tokio` | Runtime assíncrono (o "motor" que roda código async) |
| `dotenv` | Carrega variáveis de ambiente de um arquivo `.env` |
| `tracing` | Sistema de logs estruturados |
| `tracing-subscriber` | Exibe os logs no terminal |

Para baixar todas as dependências:

```bash
cargo build
```

Na primeira vez vai demorar alguns minutos. É normal — está compilando tudo.

---

## 🔑 Etapa 5: Configurando o Bot no Discord

### 5.1 — Criando a Aplicação

1. Acessa o [Discord Developer Portal](https://discord.com/developers/applications)
2. Clica em **"New Application"** e dá um nome ao bot
3. No menu lateral, clica em **"Bot"**
4. Clica em **"Add Bot"** e confirma
5. Em **"Privileged Gateway Intents"**, habilita:
   - ✅ `SERVER MEMBERS INTENT`
   - ✅ `MESSAGE CONTENT INTENT`
6. Clica em **"Reset Token"** e **copia o token gerado** (guarda bem — você só vê uma vez)

### 5.2 — Convidando o Bot para um Servidor

1. No menu lateral, vai em **"OAuth2" → "URL Generator"**
2. Em **Scopes**, marca: `bot`
3. Em **Bot Permissions**, marca: `Send Messages`, `Read Message History`, `View Channels`
4. Copia a URL gerada e abre no navegador
5. Escolhe um servidor de teste e confirma

### 5.3 — Arquivo de Configuração (.env)

Na raiz do projeto, cria um arquivo chamado `.env`:

```bash
nano .env
```

Coloca isso dentro:

```
DISCORD_TOKEN=seu_token_aqui
```

Substitui `seu_token_aqui` pelo token que você copiou.

> ⚠️ **NUNCA sobe o `.env` pro GitHub.** Adiciona ele ao `.gitignore`:
> ```bash
> echo ".env" >> .gitignore
> ```

---

## 🤖 Etapa 6: Construindo o Bot com Serenity

### 6.1 — Bot Mínimo (só pra ver funcionar)

Abre `src/main.rs` e substitui tudo por:

```rust
use serenity::async_trait;
use serenity::model::channel::Message;
use serenity::model::gateway::Ready;
use serenity::prelude::*;
use dotenv::dotenv;
use std::env;

// Handler é onde você define como o bot reage aos eventos
struct Handler;

#[async_trait]
impl EventHandler for Handler {
    // Chamado quando o bot fica online
    async fn ready(&self, _ctx: Context, ready: Ready) {
        println!("✅ {} está online!", ready.user.name);
    }

    // Chamado toda vez que alguém manda uma mensagem
    async fn message(&self, ctx: Context, msg: Message) {
        // Ignora mensagens do próprio bot
        if msg.author.bot {
            return;
        }

        if msg.content == "!ping" {
            if let Err(e) = msg.channel_id.say(&ctx.http, "Pong! 🏓").await {
                println!("Erro ao enviar mensagem: {:?}", e);
            }
        }
    }
}

#[tokio::main]
async fn main() {
    // Carrega o .env
    dotenv().ok();

    // Configura os logs
    tracing_subscriber::fmt::init();

    // Pega o token da variável de ambiente
    let token = env::var("DISCORD_TOKEN")
        .expect("Token não encontrado! Verifique seu arquivo .env");

    // Define quais eventos o bot vai "escutar"
    let intents = GatewayIntents::GUILD_MESSAGES
        | GatewayIntents::MESSAGE_CONTENT
        | GatewayIntents::DIRECT_MESSAGES;

    // Cria e inicia o cliente
    let mut client = Client::builder(&token, intents)
        .event_handler(Handler)
        .await
        .expect("Erro ao criar o cliente");

    if let Err(e) = client.start().await {
        println!("Erro ao iniciar o cliente: {:?}", e);
    }
}
```

Roda o bot:

```bash
cargo run
```

Se aparecer `✅ NomeDoBot está online!`, funcionou! Vai no Discord, manda `!ping` no servidor onde o bot está e ele vai responder `Pong! 🏓`.

---

### 6.2 — Adicionando Mais Comandos

Agora vamos expandir o bot com uma estrutura mais organizada. Cria um arquivo `src/comandos.rs`:

```bash
nano src/comandos.rs
```

```rust
use serenity::model::channel::Message;
use serenity::prelude::*;

pub async fn ping(ctx: &Context, msg: &Message) {
    if let Err(e) = msg.channel_id.say(&ctx.http, "Pong! 🏓").await {
        println!("Erro: {:?}", e);
    }
}

pub async fn ajuda(ctx: &Context, msg: &Message) {
    let texto = "
📋 **Comandos disponíveis:**
`!ping` — Testa se o bot está vivo
`!ajuda` — Mostra esta mensagem
`!oi` — O bot te cumprimenta
    ";

    if let Err(e) = msg.channel_id.say(&ctx.http, texto).await {
        println!("Erro: {:?}", e);
    }
}

pub async fn oi(ctx: &Context, msg: &Message) {
    let resposta = format!("Olá, {}! 👋", msg.author.name);

    if let Err(e) = msg.channel_id.say(&ctx.http, resposta).await {
        println!("Erro: {:?}", e);
    }
}
```

Agora atualiza `src/main.rs` para usar o módulo:

```rust
mod comandos;

use serenity::async_trait;
use serenity::model::channel::Message;
use serenity::model::gateway::Ready;
use serenity::prelude::*;
use dotenv::dotenv;
use std::env;

struct Handler;

#[async_trait]
impl EventHandler for Handler {
    async fn ready(&self, _ctx: Context, ready: Ready) {
        println!("✅ {} está online!", ready.user.name);
    }

    async fn message(&self, ctx: Context, msg: Message) {
        if msg.author.bot {
            return;
        }

        // Roteamento de comandos
        match msg.content.as_str() {
            "!ping" => comandos::ping(&ctx, &msg).await,
            "!ajuda" => comandos::ajuda(&ctx, &msg).await,
            "!oi" => comandos::oi(&ctx, &msg).await,
            _ => {} // ignora tudo que não for um comando conhecido
        }
    }
}

#[tokio::main]
async fn main() {
    dotenv().ok();
    tracing_subscriber::fmt::init();

    let token = env::var("DISCORD_TOKEN")
        .expect("Token não encontrado! Verifique seu arquivo .env");

    let intents = GatewayIntents::GUILD_MESSAGES
        | GatewayIntents::MESSAGE_CONTENT
        | GatewayIntents::DIRECT_MESSAGES;

    let mut client = Client::builder(&token, intents)
        .event_handler(Handler)
        .await
        .expect("Erro ao criar o cliente");

    if let Err(e) = client.start().await {
        println!("Erro ao iniciar o cliente: {:?}", e);
    }
}
```

```bash
cargo run
```

---

### 6.3 — Estrutura Final do Projeto

Quando o projeto crescer, uma boa estrutura é:

```
meu-bot-discord/
├── .env                  ← token (nunca sobe pro git!)
├── .gitignore
├── Cargo.toml
└── src/
    ├── main.rs           ← ponto de entrada, inicialização
    ├── handler.rs        ← lógica do EventHandler
    └── comandos/
        ├── mod.rs        ← exporta todos os comandos
        ├── ping.rs
        ├── ajuda.rs
        └── oi.rs
```

---

## 🚀 Próximos Passos

Agora que o bot básico está funcionando, aqui está o que você pode explorar:

### Funcionalidades para Adicionar
- [ ] **Slash Commands** — comandos modernos do Discord com `/` (veja a doc do Serenity sobre `application_command`)
- [ ] **Embeds** — mensagens formatadas com cor, título e campos estruturados
- [ ] **Banco de Dados** — integrar o bot com SQLite usando a crate `sqlx`
- [ ] **Cooldown de Comandos** — evitar spam usando `HashMap` para rastrear tempo de uso por usuário
- [ ] **Roles e Permissões** — verificar cargo do usuário antes de executar um comando

### Recursos para Continuar Aprendendo
- 📚 [Documentação oficial do Serenity](https://docs.rs/serenity/latest/serenity/)
- 💬 [Servidor Discord da comunidade Serenity](https://discord.gg/serenity-rs)
- 🦀 [Exemplos oficiais do Serenity no GitHub](https://github.com/serenity-rs/serenity/tree/current/examples)
- 📖 [The Rust Book completo](https://doc.rust-lang.org/book/)

---

> **Boa sorte, Nycazael!** 🦀  
> Rust tem uma curva de aprendizado íngreme no começo, mas quando clica, clica de verdade.  
> Qualquer erro de compilação que aparecer — lê a mensagem com calma, o compilador do Rust é um dos mais descritivos que existem. Ele quase sempre te diz exatamente o que fazer.
