# 🦀 Rust + Discord Bot Roadmap — For UNC

> **This one's for you, UNC.** 🎖️
>
> You picked up a guide that wasn't even written for you, translated it yourself, and pushed through the friction of Portuguese variable names just to learn something you wanted to learn. That kind of stubbornness is exactly what it takes to get good at Rust. This version was rebuilt from scratch with you in mind: fully in English, all variable names clean, and with proper mobile options so you can code from anywhere.
>
> Let's build something.

---

## 📱 Mobile or Desktop?

This guide works on both. Before diving in, pick your path:

| Your setup | Recommended approach |
|---|---|
| Android phone/tablet | **Termux** — full Linux terminal, runs Rust natively |
| iPhone/iPad | **GitHub Codespaces** — browser-based VS Code, no install needed |
| Any device with a browser | **GitHub Codespaces** — best cross-platform option |
| Linux desktop | Follow the guide as-is |
| Windows desktop | Use WSL2 (Windows Subsystem for Linux) |

Jump to [Appendix A — Mobile Setup](#-appendix-a--mobile-setup-termux--codespaces) before Step 1 if you're on mobile. Come back here after.

---

## 📋 Table of Contents

1. [Setting Up the Environment](#-step-1-setting-up-the-environment)
2. [Installing Rust](#-step-2-installing-rust)
3. [Rust Fundamentals](#-step-3-rust-fundamentals)
4. [Creating the Bot Project](#-step-4-creating-the-bot-project)
5. [Setting Up the Bot on Discord](#-step-5-setting-up-the-bot-on-discord)
6. [Building the Bot with Serenity](#-step-6-building-the-bot-with-serenity)
7. [Next Steps](#-next-steps)
8. [Appendix A — Mobile Setup](#-appendix-a--mobile-setup-termux--codespaces)

---

## 🛠 Step 1: Setting Up the Environment

> 📱 **On mobile?** Skip this step — your environment is already handled. Go to [Step 2](#-step-2-installing-rust).

Before installing Rust, your Linux system needs a set of essential build tools. Without them, many packages simply won't compile.

Open a terminal (`Ctrl + Alt + T`) and run:

```bash
sudo apt update && sudo apt upgrade -y
```

Now install the build toolchain:

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

**What each package does:**

| Package | Purpose |
|---|---|
| `build-essential` | GCC, make, and other core compilation tools |
| `cmake` | Build system used by many native dependencies |
| `clang` | Alternative C/C++ compiler; required by several Rust crates |
| `libclang-dev` | Clang headers; needed for `bindgen` and FFI crates |
| `pkg-config` | Helps the compiler locate system libraries |
| `libssl-dev` | Required for HTTPS connections (the bot needs this to talk to Discord's API) |
| `git` | Version control (you'll want this) |
| `curl` | Used to download the Rust installer |

Verify the installation:

```bash
cmake --version
clang --version
```

If both print version numbers, you're good. Move on.

---

## 🦀 Step 2: Installing Rust

Rust is installed through **rustup**, the official version and toolchain manager.

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
```

The installer will ask a few questions. Choose option **1 (default)** and press Enter.

When it's done, reload your shell so the PATH gets updated:

```bash
source ~/.bashrc
```

> 📱 **Termux users:** Use `source ~/.profile` instead.

Verify the installation:

```bash
rustc --version
cargo --version
```

You should see something like:

```
rustc 1.xx.x (...)
cargo 1.xx.x (...)
```

**What's each one?**

- **`rustc`** → the Rust compiler
- **`cargo`** → the package manager and build system (you'll use this constantly)

---

## 📚 Step 3: Rust Fundamentals

Before jumping into the bot, you need to understand the basics of the language. Rust has unique concepts that don't exist in Python or JavaScript — understanding them will save you a lot of headaches.

### 3.1 — Essential Concepts (study in this order)

#### ① Variables and Mutability

```rust
fn main() {
    let name = "UNC";             // immutable by default
    let mut counter = 0;          // mut = can be modified

    counter += 1;
    println!("Hello, {}! Counter: {}", name, counter);
}
```

> **Key idea:** In Rust, variables are immutable by default. You need to explicitly write `mut` to allow changes.

#### ② Data Types

```rust
fn main() {
    let integer: i32 = 42;
    let float: f64 = 3.14;
    let boolean: bool = true;
    let text: &str = "hello";
    let owned_string: String = String::from("hello too");

    println!("{} {} {} {} {}", integer, float, boolean, text, owned_string);
}
```

#### ③ Ownership (the heart of Rust)

This is the most important and most unique concept in Rust. Every value has **exactly one owner**. When the owner goes out of scope, the value is automatically dropped — no garbage collector needed.

```rust
fn main() {
    let s1 = String::from("hello");
    let s2 = s1;              // s1 was "moved" into s2
    // println!("{}", s1);    // ERROR! s1 no longer exists

    println!("{}", s2);       // this works fine
}
```

To "borrow" a value without moving it, use **references**:

```rust
fn print_greeting(text: &String) {
    println!("{}", text);
}

fn main() {
    let message = String::from("Hello from UNC");
    print_greeting(&message);    // passes a reference
    println!("{}", message);     // message still exists here
}
```

#### ④ Structs and Implementations

```rust
struct User {
    name: String,
    level: u32,
}

impl User {
    fn introduce(&self) {
        println!("I'm {} and my level is {}", self.name, self.level);
    }
}

fn main() {
    let user = User {
        name: String::from("UNC"),
        level: 99,
    };
    user.introduce();
}
```

#### ⑤ Enums and Pattern Matching

```rust
enum Command {
    Ping,
    Help,
    Unknown(String),
}

fn process(cmd: Command) {
    match cmd {
        Command::Ping => println!("Pong!"),
        Command::Help => println!("Showing command list..."),
        Command::Unknown(name) => println!("Command '{}' not found", name),
    }
}
```

#### ⑥ Error Handling with Result

Rust doesn't use exceptions. Functions that can fail return `Result<T, E>`:

```rust
use std::fs;

fn main() {
    match fs::read_to_string("file.txt") {
        Ok(content) => println!("Content: {}", content),
        Err(error) => println!("Error reading file: {}", error),
    }
}
```

The `?` operator is a shorthand that propagates errors automatically (you'll see this a lot in bot code):

```rust
fn read_file() -> Result<String, std::io::Error> {
    let content = fs::read_to_string("file.txt")?;
    Ok(content)
}
```

#### ⑦ Async/Await

The bot uses asynchronous programming. The basic concept:

```rust
use tokio;

#[tokio::main]
async fn main() {
    let result = fetch_data().await;
    println!("{}", result);
}

async fn fetch_data() -> String {
    // simulates an operation that takes time
    String::from("data arrived!")
}
```

### 3.2 — Resources to Learn From

- 📖 **[The Rust Book](https://doc.rust-lang.org/book/)** — the official Rust bible
- 🏋️ **[Rustlings](https://github.com/rust-lang/rustlings)** — interactive exercises in the terminal
- 🌐 **[Rust by Example](https://doc.rust-lang.org/rust-by-example/)** — practical examples for every concept
- 🧪 **[Rust Playground](https://play.rust-lang.org/)** — test snippets in your browser (great for mobile!)

> **Suggestion:** Spend at least 3–5 days studying the concepts above before jumping to the bot. Ownership and lifetimes will feel strange at first — that's normal. Keep going.

---

## 📦 Step 4: Creating the Bot Project

Now we're talking. Let's create the project with Cargo:

```bash
cargo new my-discord-bot
cd my-discord-bot
```

This creates the following structure:

```
my-discord-bot/
├── Cargo.toml       ← project manifest (dependencies go here)
└── src/
    └── main.rs      ← main source file
```

### Adding Dependencies

Open `Cargo.toml` with a text editor and replace its contents with:

```toml
[package]
name = "my-discord-bot"
version = "0.1.0"
edition = "2021"

[dependencies]
serenity = { version = "0.12", features = ["client", "gateway", "model", "cache"] }
tokio = { version = "1", features = ["macros", "rt-multi-thread"] }
dotenv = "0.15"
tracing = "0.1"
tracing-subscriber = "0.3"
```

**What each crate does:**

| Crate | Purpose |
|---|---|
| `serenity` | Main Discord bot framework for Rust |
| `tokio` | Async runtime (the "engine" that runs async code) |
| `dotenv` | Loads environment variables from a `.env` file |
| `tracing` | Structured logging system |
| `tracing-subscriber` | Displays logs in the terminal |

Download all dependencies:

```bash
cargo build
```

The first time will take a few minutes. That's normal — it's compiling everything from source.

---

## 🔑 Step 5: Setting Up the Bot on Discord

### 5.1 — Creating the Application

1. Go to the [Discord Developer Portal](https://discord.com/developers/applications)
2. Click **"New Application"** and give your bot a name
3. In the left sidebar, click **"Bot"**
4. Click **"Add Bot"** and confirm
5. Under **"Privileged Gateway Intents"**, enable:
   - ✅ `SERVER MEMBERS INTENT`
   - ✅ `MESSAGE CONTENT INTENT`
6. Click **"Reset Token"** and **copy the generated token** (save it carefully — you only see it once)

### 5.2 — Inviting the Bot to a Server

1. In the left sidebar, go to **"OAuth2" → "URL Generator"**
2. Under **Scopes**, check: `bot`
3. Under **Bot Permissions**, check: `Send Messages`, `Read Message History`, `View Channels`
4. Copy the generated URL and open it in a browser
5. Choose a test server and confirm

### 5.3 — Configuration File (.env)

In the project root, create a file called `.env`:

```bash
nano .env
```

Put this inside:

```env
DISCORD_TOKEN=your_token_here
```

Replace `your_token_here` with the token you copied.

> ⚠️ **NEVER push `.env` to GitHub.** Add it to `.gitignore`:
>
> ```bash
> echo ".env" >> .gitignore
> ```

---

## 🤖 Step 6: Building the Bot with Serenity

### 6.1 — Minimal Bot (just to see it work)

Open `src/main.rs` and replace everything with:

```rust
use serenity::async_trait;
use serenity::model::channel::Message;
use serenity::model::gateway::Ready;
use serenity::prelude::*;
use dotenv::dotenv;
use std::env;

// Handler is where you define how the bot reacts to events
struct Handler;

#[async_trait]
impl EventHandler for Handler {
    // Called when the bot comes online
    async fn ready(&self, _ctx: Context, ready: Ready) {
        println!("✅ {} is online!", ready.user.name);
    }

    // Called every time someone sends a message
    async fn message(&self, ctx: Context, msg: Message) {
        // Ignore messages from the bot itself
        if msg.author.bot {
            return;
        }

        if msg.content == "!ping" {
            if let Err(e) = msg.channel_id.say(&ctx.http, "Pong! 🏓").await {
                println!("Error sending message: {:?}", e);
            }
        }
    }
}

#[tokio::main]
async fn main() {
    // Load the .env file
    dotenv().ok();

    // Set up logging
    tracing_subscriber::fmt::init();

    // Get the token from the environment variable
    let token = env::var("DISCORD_TOKEN")
        .expect("Token not found! Check your .env file");

    // Define which events the bot will listen to
    let intents = GatewayIntents::GUILD_MESSAGES
        | GatewayIntents::MESSAGE_CONTENT
        | GatewayIntents::DIRECT_MESSAGES;

    // Create and start the client
    let mut client = Client::builder(&token, intents)
        .event_handler(Handler)
        .await
        .expect("Error creating the client");

    if let Err(e) = client.start().await {
        println!("Error starting the client: {:?}", e);
    }
}
```

Run the bot:

```bash
cargo run
```

If you see `✅ YourBotName is online!`, it worked! Go to Discord, send `!ping` in the server where the bot is, and it should reply `Pong! 🏓`.

---

### 6.2 — Adding More Commands

Now let's expand the bot with a cleaner structure. Create a file `src/commands.rs`:

```bash
nano src/commands.rs
```

```rust
use serenity::model::channel::Message;
use serenity::prelude::*;

pub async fn ping(ctx: &Context, msg: &Message) {
    if let Err(e) = msg.channel_id.say(&ctx.http, "Pong! 🏓").await {
        println!("Error: {:?}", e);
    }
}

pub async fn help(ctx: &Context, msg: &Message) {
    let text = "
📋 **Available commands:**
`!ping` — Tests if the bot is alive
`!help` — Shows this message
`!hi` — The bot greets you
    ";

    if let Err(e) = msg.channel_id.say(&ctx.http, text).await {
        println!("Error: {:?}", e);
    }
}

pub async fn hi(ctx: &Context, msg: &Message) {
    let response = format!("Hello, {}! 👋", msg.author.name);

    if let Err(e) = msg.channel_id.say(&ctx.http, response).await {
        println!("Error: {:?}", e);
    }
}
```

Now update `src/main.rs` to use the module:

```rust
mod commands;

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
        println!("✅ {} is online!", ready.user.name);
    }

    async fn message(&self, ctx: Context, msg: Message) {
        if msg.author.bot {
            return;
        }

        // Command routing
        match msg.content.as_str() {
            "!ping" => commands::ping(&ctx, &msg).await,
            "!help" => commands::help(&ctx, &msg).await,
            "!hi"   => commands::hi(&ctx, &msg).await,
            _       => {} // ignore anything that isn't a known command
        }
    }
}

#[tokio::main]
async fn main() {
    dotenv().ok();
    tracing_subscriber::fmt::init();

    let token = env::var("DISCORD_TOKEN")
        .expect("Token not found! Check your .env file");

    let intents = GatewayIntents::GUILD_MESSAGES
        | GatewayIntents::MESSAGE_CONTENT
        | GatewayIntents::DIRECT_MESSAGES;

    let mut client = Client::builder(&token, intents)
        .event_handler(Handler)
        .await
        .expect("Error creating the client");

    if let Err(e) = client.start().await {
        println!("Error starting the client: {:?}", e);
    }
}
```

```bash
cargo run
```

---

### 6.3 — Final Project Structure

As the project grows, a clean structure to aim for:

```
my-discord-bot/
├── .env                  ← token (NEVER commit this!)
├── .gitignore
├── Cargo.toml
└── src/
    ├── main.rs           ← entry point, initialization
    ├── handler.rs        ← EventHandler logic
    └── commands/
        ├── mod.rs        ← exports all commands
        ├── ping.rs
        ├── help.rs
        └── hi.rs
```

---

## 🚀 Next Steps

Now that the basic bot is running, here's what you can explore:

### Features to Add

- [ ] **Slash Commands** — modern Discord commands with `/` (see Serenity's docs on `application_command`)
- [ ] **Embeds** — formatted messages with color, title, and structured fields
- [ ] **Database** — integrate the bot with SQLite using the `sqlx` crate
- [ ] **Command Cooldowns** — prevent spam using a `HashMap` to track per-user timing
- [ ] **Roles and Permissions** — check a user's role before executing a command

### Resources to Keep Learning

- 📚 [Official Serenity Documentation](https://docs.rs/serenity/latest/serenity/)
- 💬 [Serenity Community Discord Server](https://discord.gg/serenity-rs)
- 🦀 [Official Serenity Examples on GitHub](https://github.com/serenity-rs/serenity/tree/current/examples)
- 📖 [The Rust Book (full)](https://doc.rust-lang.org/book/)

---

## 📱 Appendix A — Mobile Setup: Termux & Codespaces

### Option 1: Termux (Android — Recommended)

Termux gives you a real Linux environment directly on your Android device. Rust runs natively.

**Install Termux:**

Download Termux from **[F-Droid](https://f-droid.org/packages/com.termux/)** (not the Play Store — the Play Store version is outdated and unsupported).

**Set up the environment:**

```bash
pkg update && pkg upgrade -y
pkg install -y rust git openssl-dev pkg-config
```

> Termux bundles everything you need. There's no separate `apt install build-essential` — `pkg install rust` handles the full toolchain.

**Verify:**

```bash
rustc --version
cargo --version
```

**Create and run a project:**

```bash
cargo new my-discord-bot
cd my-discord-bot
nano src/main.rs
cargo run
```

**Tips for Termux:**

- Storage: run `termux-setup-storage` to access your phone's files from Termux
- Keep the screen on or use `termux-wake-lock` during `cargo build` — long compilations can get killed by Android's battery manager
- First `cargo build` with Serenity will take 5–15 minutes on a phone. That's expected.
- Use a Bluetooth keyboard if you can — editing Rust on a touchscreen is painful for anything beyond small changes

---

### Option 2: GitHub Codespaces (iOS, any browser — Universal)

Codespaces gives you a full VS Code environment in the cloud. Nothing installs on your device — it runs on GitHub's servers. Works on any browser, including Safari on iOS.

**How to set it up:**

1. Go to [github.com](https://github.com) and sign in (free account works)
2. Create a new repository (e.g., `my-discord-bot`)
3. Click the green **"Code"** button → **"Codespaces"** tab → **"Create codespace on main"**
4. VS Code opens in your browser. You have a full terminal at the bottom.

**Inside the Codespace terminal:**

```bash
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
source ~/.bashrc
rustc --version
```

Everything from the main guide works from here. You can edit files directly in the VS Code UI on the left, or use `nano` in the terminal.

**Tips for Codespaces:**

- Free tier: 60 hours/month of compute — more than enough for learning
- Your files persist between sessions (it's a cloud VM)
- On iOS, add the GitHub page to your home screen for a better full-screen experience
- The bot runs in the cloud, so it stays online as long as the Codespace is active

---

### Comparison: Termux vs Codespaces

| | Termux | Codespaces |
|---|---|---|
| Platform | Android only | Any browser |
| Runs locally | ✅ Yes | ❌ No (cloud) |
| Setup difficulty | Medium | Easy |
| Works offline | ✅ Yes | ❌ No |
| iOS support | ❌ No | ✅ Yes |
| Free | ✅ Yes | ✅ (60h/month) |
| Best for | Offline/serious use | Quick start / iOS |

---

> **Good luck, UNC.** 🦀
>
> Rust's learning curve is steep, but it's worth it — when it clicks, it *really* clicks. Every compiler error you read carefully is one less bug in production. The Rust compiler is one of the most informative compilers ever built. Trust it. Read its messages. It almost always tells you exactly what to do.
>
> You've already proven you can push through friction. Now you've got a guide in your language and a setup that works on your device. No more excuses. Go build something cool.
