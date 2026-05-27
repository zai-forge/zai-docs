# 🎮 Roadmap de Game Art — Do Zero ao AAA

**Para:** Nycazael  
**Plataformas:** Windows & Linux  
**Filosofia:** gratuito ou low-cost sempre que possível

> **Como usar este roadmap:** as fases são sequenciais, mas as trilhas dentro de cada fase podem ser exploradas em paralelo. Não pule os fundamentos — eles sustentam tudo que vem depois.

---

## Legenda

| Símbolo | Significado |
|---|---|
| 🟢 | Gratuito |
| 🟡 | Pago ou freemium |
| 🔵 | Windows |
| 🟣 | Linux |
| ⚫ | Multiplataforma |

---

## Fase 1 — Fundamentos Absolutos
**Duração estimada:** 3–6 meses  
**Pré-requisito:** nenhum

Esta fase é a mais importante do roadmap inteiro. Sem ela, qualquer software ou técnica avançada será construída sobre areia. Não existe atalho.

### Conceitos a dominar

- **Teoria das cores** — círculo cromático, paletas harmônicas, contraste de valor, temperaturas (quente/frio), como as cores interagem entre si
- **Perspectiva e forma** — 1, 2 e 3 pontos de fuga; volumes básicos (cubo, esfera, cilindro, cone); como construir qualquer objeto a partir dessas formas
- **Luz e sombra** — luz difusa vs. dura, sombra própria, sombra projetada, valores tonais (escala de cinza antes da cor), a relação entre a fonte de luz e o volume do objeto
- **Composição** — regra dos terços, linhas de leitura, hierarquia visual, como guiar o olho do espectador pela imagem

### Ferramentas

| Ferramenta | Tipo | Plataforma | Observação |
|---|---|---|---|
| Krita | 🟢 Gratuito | ⚫ Win/Linux | Principal ferramenta de pintura digital; nível profissional |
| Inkscape | 🟢 Gratuito | ⚫ Win/Linux | Para trabalho vetorial; útil para UI e ícones |
| Clip Studio Paint | 🟡 Pago | 🔵 Win | Excelente para ilustração e concept art; vale o investimento |

### Objetivos práticos

- [ ] Desenhar 10 objetos cotidianos com luz e sombra corretas
- [ ] Pintar uma cena simples com paleta de 3 cores + neutros
- [ ] Fazer 30 esboços rápidos por semana (use [gestualdrawing.com](https://line.sketchdaily.app/))
- [ ] Copiar obras de artistas que admira — estudo dirigido, não plágio
- [ ] Terminar a fase com um sketchbook digital de pelo menos 50 estudos

### Recursos gratuitos

- [Ctrl+Paint](https://www.ctrlpaint.com/) — série completa e gratuita sobre pintura digital
- [Proko](https://www.youtube.com/c/ProkoTV) — anatomia e figura humana (YouTube)
- [Marco Bucci](https://www.youtube.com/c/marcobucci) — teoria da cor e luz (YouTube)
- [GDC Vault](https://www.gdcvault.com/free) — palestras gratuitas de arte de games

> **Dica:** não precisa de mesa digitalizadora cara no começo — um mouse funciona para aprender os fundamentos. O investimento em hardware vem depois, quando você já souber o que precisa.

---

## Fase 2 — Arte 2D para Games
**Duração estimada:** 6–12 meses  
**Pré-requisito:** Fase 1 concluída

A arte 2D tem altíssima demanda no mercado indie e é a porta de entrada mais acessível para a indústria de games. Você pode trabalhar de forma independente ou contribuir para estúdios pequenos e médios desde cedo.

### Trilha A — Pixel Art

A linguagem visual mais associada a games. Alta demanda em projetos indie, retro e mobile.

**Conceitos:**
- Resolução e escala (8x8, 16x16, 32x32, 64x64)
- Anti-aliasing manual vs. pillow shading (evitar)
- Tilesets: autotile, wang tiles, bitmask
- Spritesheet e animação frame-a-frame: idle, walk, run, attack, death
- Efeitos visuais em pixel art: explosões, partículas, hit flashes

**Ferramentas:**

| Ferramenta | Tipo | Plataforma | Observação |
|---|---|---|---|
| Aseprite | 🟡 ~$20 Steam | ⚫ Win/Linux | Padrão da indústria; código aberto — pode compilar de graça |
| LibreSprite | 🟢 Gratuito | ⚫ Win/Linux | Fork do Aseprite; alternativa 100% grátis |
| GrafX2 | 🟢 Gratuito | ⚫ Win/Linux | Opção clássica e leve |

> **Dica sobre o Aseprite:** o repositório é open source. Você pode clonar e compilar gratuitamente. [Instruções aqui](https://github.com/aseprite/aseprite/blob/main/INSTALL.md).

### Trilha B — Concept Art e Ilustração 2D

Base de qualquer produção maior. O concept artist define visualmente o mundo do jogo antes que qualquer modelo 3D exista.

**Conceitos:**
- Thumbnailing e exploração rápida de silhuetas
- Character design: leitura de silhueta, linguagem visual, paleta de personagem
- Environment sketches: estabelecer mood, luz, escala
- Color script: consistência de cor ao longo de uma narrativa visual

**Ferramenta principal:** Krita (já instalado da Fase 1)

### Trilha C — UI e HUD de Jogo

Área com boa demanda e muito espaço para quem tem sensibilidade de design. Todo jogo precisa de interface.

**Conceitos:**
- Hierarquia da informação na interface
- Ícones e iconografia para games
- Barras de status, minimapas, inventários
- Design responsivo para diferentes resoluções

**Ferramentas:**

| Ferramenta | Tipo | Plataforma |
|---|---|---|
| Figma | 🟢 Gratuito | ⚫ Web/Win/Linux |
| Krita | 🟢 Gratuito | ⚫ Win/Linux |

### Trilha D — Animação 2D

**Ferramentas:**

| Ferramenta | Tipo | Plataforma | Observação |
|---|---|---|---|
| DragonBones | 🟢 Gratuito | ⚫ Win/Linux | Rigging e animação por ossos; exporta para Unity/Godot |
| Spine | 🟡 Pago | ⚫ Win/Linux | Padrão da indústria para animação 2D com ossos |

### Objetivos práticos

- [ ] Criar um personagem completo com idle, walk e attack animation (pixel art)
- [ ] Montar um tileset de dungeon 16×16 funcional e coerente
- [ ] Desenhar um HUD completo para um jogo imaginário
- [ ] **Publicar um asset pack gratuito no [itch.io](https://itch.io)** — portfólio real desde cedo

### Recursos gratuitos

- [Pixel Logic](https://pixellogicbook.com/) — referência essencial para pixel art
- [Brandon James Greer](https://www.youtube.com/c/BJGpixelart) — tutoriais de pixel art (YouTube)
- [MortMort](https://www.youtube.com/c/MortMort) — pixel art e game dev (YouTube)
- [Lospec Palette List](https://lospec.com/palette-list) — paletas de cores para pixel art

---

## Fase 3 — Arte 3D: Fundamentos
**Duração estimada:** 8–14 meses  
**Pré-requisito:** Fase 1 concluída (Fase 2 é opcional mas recomendada)

Esta é a fase mais extensa e tecnicamente exigente. Dominar arte 3D abre as portas para o mercado AAA e para praticamente qualquer tipo de projeto.

### Conceitos fundamentais

- **Modelagem low-poly** — box modeling, edge loops, topologia correta para deformação em animação, poly count consciente
- **UV Unwrapping** — seams estratégicos, organização de islands, otimização de espaço no UV space
- **Texturização PBR (Physically Based Rendering)** — os mapas essenciais:
  - **Albedo** — cor base sem iluminação
  - **Roughness** — quão fosco ou brilhante é a superfície
  - **Metallic** — define se o material é metálico ou não
  - **Normal Map** — simula detalhes de superfície sem geometria extra
  - **AO (Ambient Occlusion)** — sombras de contato
- **Rigging** — armature, weight painting, inverse kinematics (IK), forward kinematics (FK)
- **Animação 3D** — os 12 princípios aplicados ao 3D, graph editor, NLA editor
- **Baking** — transferir detalhes de um modelo high-poly para um low-poly via normal map. Técnica central no pipeline AAA

### Pipeline completo de um asset game-ready

```
Concept / Referência
       ↓
Blockout (formas grandes, proporções)
       ↓
High-poly (detalhes, sculpt)
       ↓
Retopologia (low-poly limpo)
       ↓
UV Unwrap
       ↓
Baking (high → low via normal map)
       ↓
Texturização PBR
       ↓
Export (FBX / GLTF) → Engine (Unity / Godot / Unreal)
```

### Ferramentas

| Ferramenta | Tipo | Plataforma | Uso |
|---|---|---|---|
| Blender | 🟢 Gratuito | ⚫ Win/Linux | Modelagem, UV, rigging, animação, baking, render |
| Substance Painter | 🟡 ~$20/mês ou Steam | ⚫ Win/Linux | Texturização PBR; padrão da indústria |
| Quixel Mixer | 🟢 Gratuito | 🔵 Win | Alternativa gratuita ao Substance para texturas |
| Materialize | 🟢 Gratuito | 🔵 Win | Geração de mapas PBR a partir de fotos |
| Marmoset Toolbag | 🟡 Pago | ⚫ Win/Linux | Renderer de portfólio; baking profissional |

> **Blender é o software mais importante deste roadmap inteiro.** É gratuito, multiplataforma, tem recursos comparáveis ao Maya e ao 3ds Max, e é cada vez mais adotado por estúdios profissionais. Vale dedicar tempo de verdade.

### Canais YouTube recomendados (gratuitos)

- [Grant Abbitt](https://www.youtube.com/c/GrantAbbitt) — modelagem, rigging, game art
- [Blender Guru](https://www.youtube.com/c/BlenderGuruOfficial) — fundamentos do Blender
- [Stylized Station](https://www.youtube.com/c/StylizedStation) — arte estilizada para games
- [CG Boost](https://www.youtube.com/c/CGBoost) — projetos práticos em Blender

### Objetivos práticos

- [ ] Modelar uma arma ou prop com textura PBR completa e exportar para uma engine
- [ ] Criar um personagem low-poly riggado com animações de idle e walk
- [ ] Fazer o processo completo de baking: high-poly → low-poly → normal map
- [ ] **Publicar modelos na [Sketchfab](https://sketchfab.com/)** — excelente portfólio 3D online

---

## Fase 4 — Especialização Intermediária
**Duração estimada:** 12–18 meses  
**Pré-requisito:** Fase 3 concluída

Aqui você deixa de ser generalista e começa a desenvolver uma identidade profissional. Escolha uma especialização principal — mas entender as outras em nível básico é um diferencial enorme.

---

### Opção A — Character Artist

Foco em criar personagens de alta qualidade: sculpting detalhado, anatomia, roupa, cabelo, expressões faciais.

**Habilidades:**
- Sculpting orgânico (pele, músculo, gordura, rugas)
- Sculpting hard surface (armadura, acessórios)
- Retopologia manual com topologia de animação
- Grooming de cabelo e pelo (XGen, Hair Cards)
- Facial rigging e blend shapes

**Ferramentas adicionais:**

| Ferramenta | Tipo | Plataforma |
|---|---|---|
| ZBrush | 🟡 Pago (ou ZBrushCore Mini grátis) | 🔵 Win |
| Blender Sculpt Mode | 🟢 Gratuito | ⚫ Win/Linux |
| Marvelous Designer | 🟡 Pago | ⚫ Win/Linux |

---

### Opção B — Environment Artist

Foco em criar cenários, props e mundos. Alta demanda e muito espaço criativo.

**Habilidades:**
- Modular design — criar peças que se encaixam de várias formas
- Trim sheets — uma textura que serve para múltiplos modelos
- Vertex painting para variação de material
- LOD (Level of Detail) — versões simplificadas para distância
- World building em engines (UE5, Unity)
- Iluminação de cena

**Ferramentas adicionais:**

| Ferramenta | Tipo | Plataforma |
|---|---|---|
| Unreal Engine 5 | 🟢 Gratuito | ⚫ Win/Linux |
| SpeedTree | 🟡 Indie gratuito | ⚫ Win/Linux |
| Houdini Indie | 🟡 ~$269/ano | ⚫ Win/Linux |
| Quixel Bridge | 🟢 Gratuito | ⚫ Win/Linux |

---

### Opção C — Technical Artist

A ponte entre arte e programação. Um dos perfis mais valorizados e bem pagos da indústria.

**Habilidades:**
- Shaders customizados (HLSL, ShaderGraph, Material Editor do UE5)
- VFX em tempo real (Niagara no UE5, VFX Graph na Unity)
- Otimização: draw calls, batching, texture atlases
- Pipeline automation com Python/scripting
- Rigging avançado e ferramentas de animação

**Ferramentas adicionais:**

| Ferramenta | Tipo |
|---|---|
| Unreal Engine 5 | 🟢 Gratuito |
| Unity | 🟢 Gratuito (Personal) |
| Houdini | 🟡 Pago |

---

### Opção D — Concept Artist

Define a identidade visual do jogo antes de qualquer produção. Alta influência criativa.

**Habilidades:**
- Design de personagens, veículos, criaturas, mundos
- Exploração de silhuetas e variações rápidas
- Rendering de keyframes e splash arts
- Comunicação visual de narrativa e emoção

**Ferramentas:**
- Krita (gratuito) ou Clip Studio Paint (pago)
- PureRef (🟢 gratuito) — organização de referências

---

### Objetivos práticos da Fase 4

- [ ] Criar um personagem full pipeline: concept → sculpt → retopo → textura → render
- [ ] Montar um environment completo em UE5 com iluminação e pós-processamento
- [ ] Recriar um asset de jogo famoso como estudo técnico (fan art dirigida)
- [ ] Participar de pelo menos uma game jam com foco em asset creation

---

## Fase 5 — Nível Profissional e AAA
**Duração:** contínua — este nível não tem fim  
**Pré-requisito:** Fase 4 em andamento

### Pipeline de estúdio

- Controle de versão com **Perforce** ou **Git LFS** (padrão em estúdios)
- Naming conventions e folder structure de projetos
- Documentação de assets: poly count, texture resolution, material slots
- Code reviews → equivalente em arte: **art reviews** e **feedback cycles**
- Comunicação com outros departamentos: game design, programação, animação

### Construindo o portfólio AAA

O portfólio é mais importante que o currículo na indústria de games. Regras gerais:

- **Plataforma principal:** [ArtStation](https://www.artstation.com/) — padrão da indústria
- **5 a 8 peças de qualidade** valem mais do que 30 peças medianas
- Cada projeto deve ter **breakdown técnico**: wireframes, mapas de textura, processo
- Mostre que você entende o **pipeline real**, não só o resultado final
- Pesquise os portfólios dos artistas dos estúdios onde quer trabalhar e use como referência de nível

### Fontes de renda independente

| Plataforma | Tipo | Observação |
|---|---|---|
| Fab.com (Epic) | Assets 3D | Novo marketplace unificado da Epic |
| Unity Asset Store | Assets 3D/2D | Alta base de compradores |
| itch.io | Qualquer tipo | Melhor para pixel art e assets 2D |
| Sketchfab Store | Modelos 3D | Boa visibilidade para 3D |
| Patreon | Tutoriais / process | Funciona bem para artistas com audiência |

### Comunidade e networking

- **[Polycount](https://polycount.com/)** — fórum técnico mais respeitado da indústria de game art
- **[80.lv](https://80.lv/)** — breakdowns de artistas AAA; leitura obrigatória
- **[The Rookies](https://www.therookies.co/)** — concursos e exposição para artistas emergentes
- **Discord** — praticamente todo estúdio e ferramenta tem servidor; participe ativamente
- **LinkedIn** — mais importante do que parece para vagas internacionais
- **Twitter/X** — comunidade de arte de games muito ativa; compartilhe seu processo

---

## Recursos de Estudo Gratuitos — Compilado Geral

### Vídeo e tutoriais

| Recurso | Foco |
|---|---|
| [GDC Vault (gratuito)](https://www.gdcvault.com/free) | Palestras técnicas dos maiores estúdios |
| [Blender Guru](https://www.youtube.com/c/BlenderGuruOfficial) | Blender do zero |
| [Grant Abbitt](https://www.youtube.com/c/GrantAbbitt) | Game art em Blender |
| [Stylized Station](https://www.youtube.com/c/StylizedStation) | Arte estilizada |
| [Ctrl+Paint](https://www.ctrlpaint.com/) | Pintura digital fundamentos |
| [Proko](https://www.youtube.com/c/ProkoTV) | Anatomia e figura humana |
| [Brandon James Greer](https://www.youtube.com/c/BJGpixelart) | Pixel art |

### Referência e leitura

| Recurso | Foco |
|---|---|
| [80.lv](https://80.lv/) | Breakdowns AAA |
| [Polycount Wiki](https://wiki.polycount.com/) | Técnicas de game art |
| [Lospec](https://lospec.com/) | Pixel art: paletas e tutoriais |
| [ArtStation Learning](https://www.artstation.com/learning) | Cursos (parte gratuita) |

### Assets e referências

| Recurso | Tipo |
|---|---|
| [Quixel Bridge](https://quixelbridge.com/) | Assets AAA gratuitos para UE5 |
| [PureRef](https://www.pureref.com/) | Organização de referências visuais |
| [ArtStation Explore](https://www.artstation.com/) | Referência de portfólios profissionais |
| [Pinterest](https://www.pinterest.com/) | Coleta de referências visual |

---

## Linha do Tempo Estimada

```
Mês 0–6    ████████░░░░░░░░░░░░░░░░░░░░░  Fase 1 — Fundamentos
Mês 6–18   ░░░░░░░░████████████░░░░░░░░░  Fase 2 — Arte 2D
Mês 10–24  ░░░░░░░░░░░░████████████████░  Fase 3 — Arte 3D
Mês 18–36  ░░░░░░░░░░░░░░░░░░░░████████  Fases 4–5 — Especialização
```

> As fases se sobrepõem — você começa a estudar 3D ainda enquanto pratica 2D. O importante é não abandonar os fundamentos da Fase 1 nunca: continue fazendo estudos de luz, cor e forma mesmo nos estágios avançados. Os melhores artistas AAA ainda fazem estudos básicos toda semana.

---

*Roadmap criado com carinho. Bons estudos, Nycazael!* 🎨
