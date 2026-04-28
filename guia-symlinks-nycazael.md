# Guia de Symlinks — X-iasu / MEGA

Como redirecionar pastas de saves (e outras coisas) para dentro da pasta sincronizada com o MEGA, sem mover os arquivos manualmente.

---

## O que é um symlink?

Um symlink (link simbólico) é um atalho no sistema de arquivos. O jogo acha que a pasta de saves está onde sempre esteve — mas na verdade qualquer coisa salva ali vai parar dentro da X-iasu, que o MEGA sincroniza automaticamente.

```
# O jogo lê/escreve aqui:
/caminho/original/do/save/
        ↓ na verdade aponta para:
/home/chaso/Área de trabalho/X-iasu/nome-do-jogo/
```

---

## 🐧 Linux — passo a passo (Linux Mint)

### 1. Achar onde o jogo salva

Jogos via Steam/Proton costumam salvar dentro do `compatdata`. O ID numérico é o AppID do jogo no Steam (pesquisa `"nome do jogo steamdb"` pra achar o ID).

```bash
# Procura por nome de arquivo dentro do compatdata
find /media/chaso/Jogos/SteamLibrary/steamapps/compatdata/[APP_ID] \
  -iname "*save*" -o -iname "*.sav" 2>/dev/null

# Lista as pastas de um ID específico (visão geral)
ls /media/chaso/Jogos/SteamLibrary/steamapps/compatdata/[APP_ID]/pfx/drive_c/users/steamuser/
```

### 2. Criar a pasta destino na X-iasu

Cria a pasta dentro da X-iasu que vai receber os saves. Use um nome sem espaços pra facilitar.

```bash
mkdir -p "/home/chaso/Área de trabalho/X-iasu/nome-do-jogo"
```

### 3. Mover os saves existentes (se houver)

Se o jogo já tem saves, move eles antes de criar o symlink.

```bash
mv "/caminho/original/do/save/"* \
   "/home/chaso/Área de trabalho/X-iasu/nome-do-jogo/"
```

### 4. Criar o symlink

A pasta original precisa não existir (ou estar vazia) nesse momento. O symlink ocupa o lugar dela.

```bash
# Remove a pasta original vazia (se ainda existir)
rmdir "/caminho/original/do/save"

# Cria o symlink: ln -s [DESTINO] [ONDE O JOGO PROCURA]
ln -s "/home/chaso/Área de trabalho/X-iasu/nome-do-jogo" \
      "/caminho/original/do/save"
```

> ⚠️ **A ordem importa:** primeiro o destino (X-iasu), depois onde o link vai ficar (onde o jogo procura).

### 5. Confirmar que funcionou

```bash
ls -la "/caminho/onde/o/link/ficou/"
# Deve aparecer algo como:
# nome-do-jogo -> /home/chaso/Área de trabalho/X-iasu/nome-do-jogo
```

---

## Exemplo real — Conan Exiles (AppID 440900)

```bash
mkdir -p "/home/chaso/Área de trabalho/X-iasu/conan-exiles"

mkdir -p "/media/chaso/Jogos/SteamLibrary/steamapps/compatdata/440900/pfx/drive_c/users/steamuser/Saved Games/"

ln -s "/home/chaso/Área de trabalho/X-iasu/conan-exiles" \
      "/media/chaso/Jogos/SteamLibrary/steamapps/compatdata/440900/pfx/drive_c/users/steamuser/Saved Games/Conan Exiles"
```

---

## 🪟 Windows — mklink (Windows 10/11)

### Pré-requisito

No Windows, symlinks de pasta exigem abrir o **Prompt de Comando como Administrador**. Pesquisa `cmd` no menu iniciar, clica com botão direito → *Executar como administrador*.

### 1. Criar pasta destino no MEGA

Cria a pasta dentro da pasta sincronizada pelo MEGAsync (normalmente em `C:\Users\[usuario]\MEGA` ou onde foi configurado).

```cmd
mkdir "C:\Users\chaso\MEGA\X-iasu\nome-do-jogo"
```

### 2. Mover saves existentes (se houver)

```cmd
move "C:\caminho\original\do\save\*" "C:\Users\chaso\MEGA\X-iasu\nome-do-jogo\"
```

### 3. Remover pasta original e criar o link

No Windows o comando é `mklink /D` — cria um junction (link de diretório).

```cmd
rmdir "C:\caminho\original\do\save"

:: mklink /D [ONDE O JOGO PROCURA] [DESTINO NO MEGA]
mklink /D "C:\caminho\original\do\save" ^
           "C:\Users\chaso\MEGA\X-iasu\nome-do-jogo"
```

> ⚠️ **Atenção:** No Windows a ordem é **invertida** em relação ao Linux — primeiro onde o link fica, depois o destino. Essa é a pegadinha clássica.

### 4. Confirmar

```cmd
dir "C:\caminho\onde\o\link\ficou"
:: Vai aparecer com a tag [JUNCTION] indicando o destino
```

---

## Referência rápida

```bash
# Linux
ln -s [DESTINO_MEGA] [ONDE_O_JOGO_PROCURA]

# Windows (cmd como admin)
mklink /D [ONDE_O_JOGO_PROCURA] [DESTINO_MEGA]

# ⚠ A pasta [ONDE_O_JOGO_PROCURA] não pode existir na hora do comando
```
