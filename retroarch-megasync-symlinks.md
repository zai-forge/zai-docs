# RetroArch na Nuvem: sincronizando ROMs, saves, savestates e configs com o MEGA

**Guia para Windows, Linux e Android**

O objetivo aqui é simples: fazer com que seus emuladores salvem "em tempo real" na nuvem, do mesmo jeito que jogos modernos fazem com Steam Cloud. Você fecha o Mega Drive no PC, abre o celular e continua exatamente de onde parou.

A técnica principal é o **symlink** (link simbólico) — um "atalho de verdade", que o sistema operacional trata como se fosse a pasta original.

---

## Índice

1. [Como isso funciona (e por que a direção do link importa)](#1-como-isso-funciona)
2. [Avisos importantes antes de começar](#2-avisos-importantes)
3. [Preparando a pasta no MEGA](#3-preparando-a-pasta-no-mega)
4. [Windows](#4-windows)
5. [Linux](#5-linux)
6. [Android](#6-android)
7. [Regras de convivência entre múltiplos aparelhos](#7-regras-de-convivência)
8. [Solução de problemas](#8-solução-de-problemas)
9. [Como desfazer tudo](#9-como-desfazer-tudo)
10. [Alternativas ao symlink](#10-alternativas-ao-symlink)

---

## 1. Como isso funciona

Um symlink é um arquivo especial que diz ao sistema: *"o conteúdo real está lá em outro lugar"*. Programas que abrem esse caminho são redirecionados automaticamente e nem percebem a diferença.

A ideia é substituir as pastas do RetroArch por links que apontam para dentro da pasta sincronizada do MEGA:

```
C:\RetroArch\saves        →  (symlink)  →  C:\Users\Ferb\MEGA\RetroArch\saves
C:\RetroArch\states       →  (symlink)  →  C:\Users\Ferb\MEGA\RetroArch\states
C:\RetroArch\config       →  (symlink)  →  C:\Users\Ferb\MEGA\RetroArch\config
```

Quando o RetroArch grava um `.srm`, o arquivo cai fisicamente dentro da pasta do MEGA, o MEGAsync detecta a mudança e sobe pra nuvem em segundos.

### ⚠️ A regra de ouro: a direção do link

**O MEGAsync (assim como Dropbox, OneDrive e Google Drive) não segue symlinks.** Se você colocar um symlink *dentro* da pasta sincronizada apontando para fora, o MEGA vai ignorar ou subir um arquivo quebrado de 0 KB.

| ❌ Errado | ✅ Certo |
|---|---|
| Arquivos reais no RetroArch, symlink dentro da pasta do MEGA | Arquivos reais dentro da pasta do MEGA, symlink na pasta do RetroArch |

Ou seja: **os dados reais moram no MEGA. O RetroArch é que tem os links.**

---

## 2. Avisos importantes

Leia isso antes de sair criando links. São problemas reais, não formalidade.

### 2.1 Faça backup

Antes de qualquer coisa, copie sua pasta inteira do RetroArch para um lugar seguro (um `.zip` no Desktop já resolve). Se algo der errado, você restaura e recomeça.

### 2.2 Conflitos de sincronização são o inimigo número 1

Nuvem não faz *merge* de save de videogame. Se você jogar no PC e no celular sem esperar a sincronização terminar, o MEGA vai criar um arquivo tipo `Mario.srm (conflito Ferb)` — e um dos dois progressos vai ficar órfão. A [seção 7](#7-regras-de-convivência) trata disso.

### 2.3 ROMs pesam

Sincronizar ROMs é ótimo pra manter a biblioteca igual em todo lugar, mas uma coleção de PS1 passa fácil de 50 GB, e a conta gratuita do MEGA tem 20 GB. Comece sincronizando só saves/states/configs (leves, poucos MB) e adicione ROMs depois se tiver espaço.

Se for sincronizar ROMs, uma dica: deixe as pastas de sistemas grandes (PS1, Saturn, Dreamcast) fora da sincronização automática e mantenha na nuvem só os consoles leves — 8/16 bits, GB/GBA, Neo Geo. A biblioteca inteira de Mega Drive cabe em menos de 1 GB.

### 2.4 O RetroArch precisa estar fechado

Nunca crie ou mexa em symlinks com o RetroArch aberto. Feche completamente (inclusive da bandeja do sistema) antes de começar.

### 2.5 Ligue o autosave da SRAM

Por padrão o RetroArch só grava o arquivo `.srm` (a memória de save do cartucho) **ao fechar o jogo**. Se travar ou você matar o processo, perde tudo. Ative:

> **Settings → Saving → SaveRAM Autosave Interval → 10 seconds**

Isso faz o save "de verdade" cair na nuvem enquanto você joga, que é justamente o comportamento de jogo moderno que você quer.

---

## 3. Preparando a pasta no MEGA

Isso é feito **uma vez só**, no computador que você considera o "principal".

1. Instale o **MEGAsync** e configure uma pasta sincronizada (por exemplo `C:\Users\SeuNome\MEGA` no Windows ou `~/MEGA` no Linux).
2. Dentro dela, crie a estrutura:

```
MEGA/
└── RetroArch/
    ├── saves/        (arquivos .srm — memória de cartucho)
    ├── states/       (savestates .state — "salvar a qualquer momento")
    ├── system/       (BIOS: PS1, Saturn, Neo Geo, etc.)
    ├── config/       (configs por core e remaps de controle)
    ├── playlists/    (suas listas/coleções)
    ├── thumbnails/   (capas — opcional, pesa bastante)
    └── roms/         (opcional — veja 2.3)
```

3. **Mova** (não copie) o conteúdo atual dessas pastas do seu RetroArch para dentro delas. Depois disso as pastas originais no RetroArch devem estar vazias.
4. Espere o MEGAsync terminar de subir tudo (ícone verde com o check).

---

## 4. Windows

### 4.1 Onde está a sua pasta do RetroArch?

Depende de como você instalou:

| Tipo de instalação | Local dos dados |
|---|---|
| **Portátil** (baixou o `.7z`/`.zip`) | Dentro da própria pasta, ex.: `C:\RetroArch-Win64\` |
| **Instalador** (`.exe`) | `C:\RetroArch-Win64\` para os arquivos, mas saves/configs podem ir para `%APPDATA%\RetroArch\` |
| **Steam** | `...\steamapps\common\RetroArch\` |

Para descobrir com certeza, abra o RetroArch e vá em **Settings → Directory**. Ele mostra o caminho real de cada pasta. Anote-os.

> Dica: a versão portátil é muito mais fácil de gerenciar, porque tudo fica em um lugar só.

### 4.2 Escolhendo entre symlink e junction

O Windows tem dois tipos de link para pastas:

| Tipo | Comando | Precisa de admin? | Observação |
|---|---|---|---|
| **Junction** | `mklink /J` | Não | Só funciona em discos locais. **É o recomendado aqui.** |
| **Symlink de diretório** | `mklink /D` | Sim (ou Modo Desenvolvedor ligado) | Funciona também em rede |

Para uso local com o MEGA, **junction (`/J`) é a melhor escolha**: não exige privilégio de administrador e é indistinguível de uma pasta normal para 99% dos programas.

### 4.3 Criando os links

1. Feche o RetroArch completamente.
2. **Apague** (ou renomeie para `saves_old`, etc.) as pastas originais vazias. O comando falha se a pasta de destino já existir.
3. Abra o **Prompt de Comando** (`Win+R` → `cmd`).
4. Rode os comandos, ajustando os caminhos:

```cmd
mklink /J "C:\RetroArch-Win64\saves"     "C:\Users\Ferb\MEGA\RetroArch\saves"
mklink /J "C:\RetroArch-Win64\states"    "C:\Users\Ferb\MEGA\RetroArch\states"
mklink /J "C:\RetroArch-Win64\system"    "C:\Users\Ferb\MEGA\RetroArch\system"
mklink /J "C:\RetroArch-Win64\config"    "C:\Users\Ferb\MEGA\RetroArch\config"
mklink /J "C:\RetroArch-Win64\playlists" "C:\Users\Ferb\MEGA\RetroArch\playlists"
```

A sintaxe é sempre `mklink /J "onde o link vai ficar" "para onde ele aponta"`.

Se der certo, aparece:

```
Junção criada para C:\RetroArch-Win64\saves <<===>> C:\Users\Ferb\MEGA\RetroArch\saves
```

**Pelo PowerShell**, se preferir (precisa de admin ou Modo Desenvolvedor):

```powershell
New-Item -ItemType SymbolicLink -Path "C:\RetroArch-Win64\saves" -Target "C:\Users\Ferb\MEGA\RetroArch\saves"
```

### 4.4 Conferindo

No Explorer, as pastas aparecem com um ícone de atalho. Para checar via terminal:

```cmd
dir /AL "C:\RetroArch-Win64"
```

Isso lista só os links, mostrando `<JUNCTION>` e o alvo entre colchetes.

### 4.5 Se seus dados estiverem em `%APPDATA%`

Alguns instaladores usam `C:\Users\SeuNome\AppData\Roaming\RetroArch\`. Os comandos são idênticos, só muda o caminho:

```cmd
mklink /J "%APPDATA%\RetroArch\saves" "C:\Users\Ferb\MEGA\RetroArch\saves"
```

---

## 5. Linux

No Linux isso é nativo e trivial — `ln -s` resolve tudo, sem privilégios especiais.

### 5.1 Onde está a sua pasta do RetroArch?

| Forma de instalação | Caminho |
|---|---|
| Repositório da distro / PPA | `~/.config/retroarch/` |
| **Flatpak** | `~/.var/app/org.libretro.RetroArch/config/retroarch/` |
| Snap | `~/snap/retroarch/current/.config/retroarch/` |
| AppImage | `~/.config/retroarch/` |

Confirme dentro do programa em **Settings → Directory**, ou pelo terminal:

```bash
grep -E "savefile_directory|savestate_directory|system_directory" ~/.config/retroarch/retroarch.cfg
```

> Se você usa **Flatpak**, atenção: ele roda em sandbox e por padrão só enxerga `~/.var/app/...`. Você vai precisar liberar o acesso à pasta do MEGA:
> ```bash
> flatpak override --user --filesystem=~/MEGA org.libretro.RetroArch
> ```

### 5.2 Criando os links

```bash
# 1. Backup por precaução
cp -a ~/.config/retroarch ~/retroarch-backup

# 2. Mover os dados reais para o MEGA (se ainda não fez)
mkdir -p ~/MEGA/RetroArch
mv ~/.config/retroarch/saves      ~/MEGA/RetroArch/
mv ~/.config/retroarch/states     ~/MEGA/RetroArch/
mv ~/.config/retroarch/system     ~/MEGA/RetroArch/
mv ~/.config/retroarch/config     ~/MEGA/RetroArch/
mv ~/.config/retroarch/playlists  ~/MEGA/RetroArch/

# 3. Criar os links simbólicos no lugar das pastas antigas
ln -s ~/MEGA/RetroArch/saves      ~/.config/retroarch/saves
ln -s ~/MEGA/RetroArch/states     ~/.config/retroarch/states
ln -s ~/MEGA/RetroArch/system     ~/.config/retroarch/system
ln -s ~/MEGA/RetroArch/config     ~/.config/retroarch/config
ln -s ~/MEGA/RetroArch/playlists  ~/.config/retroarch/playlists
```

A sintaxe é `ln -s ALVO_REAL CAMINHO_DO_LINK` — ou seja, o **contrário** do `mklink` do Windows. Fácil confundir.

> Use `-n` (`ln -sn`) se a pasta de destino ainda existir, para evitar que o link seja criado *dentro* dela por acidente. O ideal mesmo é apagar/mover a pasta antes.

### 5.3 Conferindo

```bash
ls -l ~/.config/retroarch/ | grep '\->'
```

Deve sair algo assim:

```
lrwxrwxrwx 1 ferb ferb   28 jul 18 14:02 saves -> /home/ferb/MEGA/RetroArch/saves
```

O `l` no início e a seta confirmam que é um link válido. Se o nome aparecer piscando em vermelho no terminal, o link está quebrado (alvo não existe).

### 5.4 Sincronizando o `retroarch.cfg` inteiro

Dá para linkar até o arquivo de configuração principal, para que atalhos, filtros e hotkeys sejam idênticos em todas as máquinas Linux:

```bash
mv ~/.config/retroarch/retroarch.cfg ~/MEGA/RetroArch/retroarch.cfg
ln -s ~/MEGA/RetroArch/retroarch.cfg ~/.config/retroarch/retroarch.cfg
```

⚠️ **Só faça isso entre máquinas parecidas.** O `retroarch.cfg` guarda resolução, driver de vídeo, mapeamento de controle e caminhos absolutos. Compartilhar o mesmo arquivo entre um PC e um notebook com GPU diferente costuma dar dor de cabeça. Sincronize a pasta `config/` (que tem os overrides por core) e deixe o `retroarch.cfg` local.

Se precisar editar o arquivo na mão:

```bash
micro ~/MEGA/RetroArch/retroarch.cfg
```

---

## 6. Android

Aqui vem a má notícia, e é melhor falar direto: **no Android sem root, symlinks não funcionam no armazenamento interno.**

O `/storage/emulated/0/` é uma camada de emulação (FUSE/sdcardfs) que simplesmente não implementa links simbólicos. Você pode até rodar `ln -s` via Termux e receber um `Function not implemented`. Não é limitação de permissão — é do sistema de arquivos.

A boa notícia: **você não precisa de symlink no Android.** O RetroArch permite apontar cada pasta diretamente para onde você quiser.

### 6.1 A abordagem correta: mudar os diretórios no próprio RetroArch

1. Instale o app do **MEGA** e configure uma pasta de sincronização local. Nas versões atuais o MEGA para Android tem sincronização de pastas em duas vias (procure por **Sincronizações** no menu do app). Anote o caminho local, algo como `/storage/emulated/0/MEGA/RetroArch`.
2. Abra o RetroArch → **Settings → Directory**.
3. Altere as entradas:

| Entrada no menu | Aponte para |
|---|---|
| Save Files | `/storage/emulated/0/MEGA/RetroArch/saves` |
| Save States | `/storage/emulated/0/MEGA/RetroArch/states` |
| System/BIOS | `/storage/emulated/0/MEGA/RetroArch/system` |
| File Browser | `/storage/emulated/0/MEGA/RetroArch/roms` |

4. Volte ao menu principal e use **Configuration File → Save Current Configuration** para gravar.

Resultado prático: idêntico ao do symlink. Os arquivos caem direto na pasta sincronizada.

### 6.2 Se a sincronização automática do MEGA não te atender

Use o **FolderSync** (ou FolderSync Lite, gratuito). Ele suporta MEGA como provedor e permite:

- sincronização em duas vias entre uma pasta local e a nuvem;
- agendamento (a cada 15 min, por exemplo);
- gatilhos por evento — **sincronizar quando um app específico for fechado** é perfeito para emulação;
- sincronizar ao conectar no Wi-Fi.

Configure uma tarefa em duas vias entre `/storage/emulated/0/RetroArch/saves` e a pasta correspondente no MEGA, com o gatilho "ao fechar o RetroArch".

### 6.3 Se você tiver root

Com root e um sistema de arquivos real (`/data`), symlinks funcionam normalmente:

```bash
su
ln -s /data/media/0/MEGA/RetroArch/saves /data/media/0/RetroArch/saves
```

Mas mesmo com root, a abordagem da seção 6.1 continua sendo mais simples e menos frágil a atualizações do sistema.

### 6.4 O detalhe dos nomes de arquivo

Para que um save do PC funcione no celular, o **nome da ROM precisa ser idêntico** — o RetroArch nomeia o save a partir do nome do arquivo da ROM. `Sonic 2 (USA).md` e `Sonic2.md` geram `.srm` diferentes. Use a mesma nomenclatura em todos os aparelhos (o padrão No-Intro é uma boa referência).

Savestates têm uma limitação adicional: são fotografias da memória do core. **Um savestate só é garantido entre a mesma versão do mesmo core.** Se o PC está com o Genesis Plus GX 1.7.4 e o celular com 1.7.6, o state pode não carregar. Para portabilidade entre aparelhos, prefira o save nativo do jogo (`.srm`).

---

## 7. Regras de convivência

Estas quatro regras evitam praticamente todos os conflitos:

1. **Só um aparelho por vez.** Nada de deixar o jogo pausado no PC e abrir no celular.
2. **Espere o ícone verde.** Antes de trocar de aparelho, confirme que o MEGA terminou de subir. Antes de começar a jogar no outro, confirme que terminou de baixar.
3. **Feche o jogo, não só minimize.** No RetroArch, use **Close Content** ou **Quit** — é aí que o `.srm` é gravado (com o autosave da seção 2.5 isso fica menos crítico, mas continua sendo boa prática).
4. **Prefira saves nativos a savestates** quando for trocar de plataforma.

Se mesmo assim aparecer um arquivo de conflito, o MEGA não apaga nada: ele mantém os dois. Compare as datas, escolha o correto, renomeie de volta para o nome original e apague o outro.

---

## 8. Solução de problemas

**O MEGA não está subindo os arquivos novos**
Você provavelmente inverteu a direção do link. Confira: os arquivos reais têm que estar *dentro* da pasta do MEGA. Abra a pasta do MEGA pelo gerenciador de arquivos — se os `.srm` estiverem lá como arquivos normais (não atalhos), está certo.

**"Não é possível criar um arquivo já existente" (Windows)**
A pasta de destino do `mklink` ainda existe. Apague ou renomeie antes.

**O RetroArch ainda está salvando no lugar antigo**
Ele pode estar usando caminhos absolutos gravados no `retroarch.cfg`. Confira em **Settings → Directory** e corrija manualmente, ou edite o arquivo:

```bash
micro ~/.config/retroarch/retroarch.cfg
```

Procure por `savefile_directory` e `savestate_directory`.

**O link aparece vermelho/piscando no terminal Linux**
Link quebrado. O alvo foi movido, renomeado ou não existe. Recrie com `ln -sf`.

**Savestate não carrega no outro aparelho**
Versões diferentes do core. Atualize os cores em todos os aparelhos ou use saves nativos.

**Flatpak não enxerga a pasta do MEGA**
Falta permissão de sandbox — veja a seção 5.1.

**O MEGAsync fica em loop de upload**
Costuma ser thumbnails ou cache sendo reescritos o tempo todo. Tire `thumbnails/` e qualquer pasta de cache da sincronização.

---

## 9. Como desfazer tudo

Um symlink pode ser removido sem medo — apagar o link **não** apaga os arquivos reais.

**Windows:**
```cmd
rmdir "C:\RetroArch-Win64\saves"
```
Use `rmdir`, **não** `del`, e não apague pelo Explorer com Shift+Delete em uma junction — em versões antigas do Windows isso podia apagar o conteúdo do alvo. `rmdir` remove apenas o link.

**Linux:**
```bash
rm ~/.config/retroarch/saves
```
Sem barra no final. `rm pasta/` com barra pode confundir e agir sobre o conteúdo.

Depois, copie os arquivos de volta do MEGA para o lugar original:
```bash
cp -a ~/MEGA/RetroArch/saves ~/.config/retroarch/saves
```

---

## 10. Alternativas ao symlink

Vale conhecer, porque em alguns cenários são melhores:

| Método | Prós | Contras |
|---|---|---|
| **Mudar os diretórios no RetroArch** (Settings → Directory) | Simples, funciona em todo lugar, zero comandos | Não sincroniza o `retroarch.cfg` em si |
| **Cloud Sync nativo do RetroArch** (Settings → Saving → Cloud Sync) | Integrado, feito para isso | Suporta WebDAV/S3; o MEGA não oferece WebDAV nativo, exigiria um intermediário |
| **rclone + MEGA** | Muito flexível, scriptável, funciona em servidor | Requer configuração via linha de comando |
| **Syncthing** | Peer-to-peer, sem nuvem, tempo real de verdade | Os aparelhos precisam estar ligados ao mesmo tempo |
| **Symlink (este guia)** | Transparente, funciona com qualquer emulador, não só RetroArch | Não funciona no Android sem root |

O symlink continua sendo a melhor opção quando você quer aplicar a mesma lógica a **outros emuladores** (PCSX2, Dolphin, DuckStation, Yuzu...), que nem sempre deixam escolher os diretórios livremente. A técnica é exatamente a mesma — só mudam os caminhos.

---

## Resumo rápido

```cmd
:: Windows
mklink /J "C:\RetroArch\saves" "C:\Users\VOCE\MEGA\RetroArch\saves"
```

```bash
# Linux
ln -s ~/MEGA/RetroArch/saves ~/.config/retroarch/saves
```

```
# Android
RetroArch → Settings → Directory → aponte para a pasta do MEGA
```

Boa jogatina. 🎮
