# 🕯️ Dungeon of Shadows

> Um jogo de plataforma 2D com tema de terror ambientado em masmorras sombrias. O jogador explora dungeons escuros carregando uma tocha, coletando chaves, fugindo de criaturas sobrenaturais e, ao final, enfrentando um chefe que teme a luz.

**Disciplina:** Desenvolvimento de Jogos — Centro Universitário SATC · **Professor:** Me. Fabiano Naspolini de Oliveira
**Equipe:** Bruno Supriano · Gustavo Olivo Cittadin · Jean Lucas Preis · Jean Vitor Vieira

---

## 📑 Índice

1. [Sobre o Projeto](#-sobre-o-projeto)
2. [Gameplay](#-gameplay)
3. [Funcionalidades Técnicas](#-funcionalidades-técnicas)
4. [Estrutura do Projeto](#️-estrutura-do-projeto)
5. [Stack & Tecnologias](#️-stack--tecnologias)
6. [Como Rodar](#-como-rodar)
7. [Documentação Técnica Detalhada](#-documentação-técnica-detalhada)
8. [Design do Nível](#-design-do-nível)
9. [Possíveis Extensões](#-possíveis-extensões)
10. [Entrega Acadêmica (SATC)](#-entrega-acadêmica-satc)
11. [Créditos de Áudio](#-créditos-de-áudio)
12. [Licença](#-licença)

---

## 📖 Sobre o Projeto

**Dungeon of Shadows** é um jogo de plataforma 2D desenvolvido inteiramente em JavaScript puro com Canvas 2D API, sem uso de assets externos — todos os gráficos são desenhados via primitivas vetoriais em tempo real.

O destaque técnico do projeto é o **sistema de iluminação dinâmica por tocha**: uma máscara de escuridão é aplicada sobre o frame via `globalCompositeOperation: 'destination-out'`, perfurando a camada escura com um `radialGradient` suavíssimo e acelerado por GPU. O resultado é uma penumbra real com borda suave, tonalidade laranja-quente próxima à chama e flicker aleatório — exatamente o efeito presente em jogos como *Don't Starve* e *Hollow Knight*.

O jogo é dividido em **3 fases** com progressão de dificuldade pensada como um arco:

1. **Fase 1 — Despertar nas Sombras:** tutorial. Ensina mover, pular, coletar chaves, desviar de inimigos, recuperar vida em corações e **manter a tocha acesa** nas tochas de parede.
2. **Fase 2 — Caminho Sem Volta:** consolidação. Reúne as mecânicas e introduz as **mini-sombras**, que recuam e definham na luz da tocha — antecipando a arma do confronto final.
3. **Fase 3 — A Sombra Final:** o chefe. Ao entrar, um vento gélido apaga todas as fogueiras da arena; o jogador precisa **reacendê-las com a tocha** e **encurralar a Sombra na luz** para derrotá-la.

---

## 🎮 Gameplay

| Ação       | Teclas                       |
|------------|------------------------------|
| Mover      | `A` / `D` ou `←` / `→`      |
| Pular      | `Space` / `W` / `↑`         |
| Reiniciar  | `R` (após game over/vitória) |

### Objetivo

1. Explore cada dungeon iluminando o caminho com sua tocha — que **enfraquece com o tempo** e é reacesa no fogo
2. Colete as **chaves douradas** de cada fase para destrancar a porta
3. Recupere vida nos **corações** espalhados pelo mapa (máximo de 3)
4. Sobreviva com **3 vidas** — inimigos drenam uma vida ao toque e empurram o herói (sem perder o progresso da fase)
5. Na fase final, **reacenda as fogueiras** e **encurrale a Sombra na luz**

### Inimigos

| Inimigo        | Comportamento                                              |
|----------------|------------------------------------------------------------|
| **Fantasma**   | Patrulha plataformas com movimento flutuante senoidal; glow de shader e gotas de sangue |
| **Morcego**    | Voa em trajetória elíptica; asas animadas com flap, olhos vermelhos |
| **Mini-sombra**| Persegue na escuridão, mas **recua e definha na luz da tocha** — ensina a usar a luz como arma |
| **A Sombra** (chefe) | Persegue no escuro e investe; **foge e sofre dano na luz** das fogueiras e da tocha. Mais ousada quanto mais fraca estiver sua chama |

---

## ✨ Funcionalidades Técnicas

- **Iluminação real de tocha** via `destination-out` + `createRadialGradient` — acelerado por GPU, sem banding
- **Três camadas de luz**: buraco na escuridão → tonalidade laranja quente → inner glow pulsante
- **Flicker** da chama: variação aleatória do raio a cada 4 frames
- **Tochas decorativas** distribuídas pelo cenário com mini-aura de luz
- **Partículas de faísca** com física própria (gravidade, arrasto por ar, fade por lifetime)
- **Câmera suave** com interpolação linear (`lerp`) em X e Y
- **Parallax de 3 camadas**: pilares de pedra (0.18×), estalactites (0.44×), caveiras (0.68×)
- **Animação procedural** do personagem: pernas sincronizadas com velocidade, chama com seno duplo
- **Colisão AABB** separada por eixo (X → Y) para física estável sem jitter
- **Sistema de invencibilidade** pós-dano com efeito de piscar
- Responsivo: canvas escala automaticamente mantendo aspect ratio via `window.resize`
- Zero dependências, zero assets visuais — abre diretamente no navegador

---

## 🗂️ Estrutura do Projeto

```
DungeonofShadows/
├── index.html                                    # Jogo completo (Versão Gold) — HTML + CSS + JS num único arquivo
├── assets/
│   └── sounds/
│       └── ghost.mp3                             # Som do fantasma (CC BY 3.0 — ver Créditos de Áudio)
├── high-concept.html                             # Pré-projeto visual (Etapa 1) — abrir no navegador
├── gdd.html                                      # Game Design Document final (Etapa 2) — abrir no navegador
├── pitch.html                                    # Pitch de investimentos — abrir no navegador
├── img/                                          # Fluxograma de regras e gráfico de ritmo (usados no GDD)
└── README.md                                     # Este arquivo — visão geral + doc técnica + guia de entrega
```

---

## 🛠️ Stack & Tecnologias

| Tecnologia          | Papel no projeto                                                       |
|---------------------|------------------------------------------------------------------------|
| **HTML5 Canvas 2D** | Renderização de todos os elementos do jogo                             |
| **JavaScript ES6+** | Lógica do jogo: física, colisão, câmera, inimigos, partículas          |
| `createRadialGradient` | Gradiente de luz suave acelerado por GPU                            |
| `globalCompositeOperation: 'destination-out'` | Perfura a máscara de escuridão para simular luz real |
| `requestAnimationFrame` | Loop de jogo sincronizado ao refresh rate do monitor              |
| **Web Audio API**   | Áudio procedural (drone, passos, dano, vento, sons do boss)            |
| **CSS Flexbox**     | Centralização do canvas na tela                                        |

> Nenhuma biblioteca, framework ou dependência externa. Apenas arquivos `.html`.

---

## 🚀 Como Rodar

### Método 1 — Abrir diretamente (recomendado)

Basta fazer **duplo clique** em `index.html`. O jogo abre no navegador imediatamente. Compatível com Chrome, Firefox, Edge, Safari — qualquer navegador moderno.

Os documentos visuais (`High Concept`, `GDD`, `pitch`) também abrem com duplo clique. Nos slides, navegue com as **setas ‹ ›**, as **setas do teclado** ou os **pontos** na base.

### Método 2 — Servidor local (opcional)

```bash
# Node.js (com npx)
npx serve .
```

Depois acesse `http://localhost:8080` no navegador.

---

## 🧪 Documentação Técnica Detalhada

> Explicação das mecânicas de programação, sistemas, estruturas de dados e decisões de implementação do jogo.

### Visão geral da arquitetura

O jogo é um loop imperativo clássico de 60fps estruturado em duas fases por frame:

```
requestAnimationFrame
        │
        ├── update()   → lógica: input, física, colisão, inimigos, coleta
        └── draw()     → renderização: fundo, plataformas, entidades, luz, HUD
```

Não há framework — tudo é JavaScript puro operando diretamente sobre a Canvas 2D API.

### 1. Game Loop

```javascript
(function loop() {
  update();
  draw(ctx);
  requestAnimationFrame(loop);
})();
```

`requestAnimationFrame` executa o callback sincronizado ao refresh rate do monitor (~60fps), com pausa automática quando a aba está inativa e sem drift de tempo. O loop é uma **IIFE** que se auto-agenda a cada frame; a física é frame-rate dependente (assume 60fps constantes).

### 2. Máquina de estados do jogo

```javascript
let state = 'playing'; // | 'dead' | 'won'
```

Um único `string` controla o estado global. `update()` retorna imediatamente se `state !== 'playing'`, congelando a lógica; `draw()` sempre roda, renderizando os overlays de game over e vitória.

```
playing ──(player.dead)──→ dead
playing ──(tocou a saída com todas as chaves)──→ won
dead/won ──(tecla R)──→ playing  [via resetGame()]
```

### 3. Sistema de input

```javascript
const input = {};
window.addEventListener('keydown', e => { input[e.code] = true; });
window.addEventListener('keyup',   e => { input[e.code] = false; });
```

O input é um **mapa de estado de teclas** (não de eventos), permitindo checar `input['KeyA']` a cada frame sem perder inputs. **Jump buffering** evita pulos repetidos ao segurar a tecla:

```javascript
if (jump && !jumpLast) player.jump();
jumpLast = jump;
```

### 4. Física do jogador

**Gravidade e velocidade terminal** — a cada frame soma `GRAVITY = 0.55` ao `vy`, limitado a 22 (`Math.min`) para manter o controle preciso em quedas longas.

**Colisão AABB** — cada entidade é um retângulo alinhado aos eixos:

```javascript
const hits = (a, b) =>
  a.x < b.x + b.w && a.x + a.w > b.x &&
  a.y < b.y + b.h && a.y + a.h > b.y;
```

**Resolução em dois eixos separados** (X depois Y) garante colisões previsíveis em cantos e diagonais. `onGround` é resetado a cada frame e só vira `true` numa colisão vertical descendente.

### 5. Câmera

```javascript
camX = lerp(camX, player.x - SW/2 + player.w/2, 0.10);
camX = clamp(camX, 0, WORLD_W - SW);
```

Câmera elástica via **lerp** (`a + (b-a)*t`): avança 10% da distância ao alvo por frame, criando inércia suave. **Clamp** impede mostrar área fora do mapa. Coordenadas mundo→tela usam `Math.floor(entity.x - camX)` para eliminar tremidos de sub-pixel.

### 6. Sistema de iluminação (núcleo técnico)

Usa um **canvas offscreen** e operações de composição:

```
[lightCvs]  ← preto opaco (escuridão)
    ├── destination-out + radialGradient   ← perfura a escuridão (luz)
    ├── source-over + warm radialGradient   ← tonalidade laranja
    └── source-over + inner radialGradient  ← inner glow pulsante
[canvas principal] ← recebe lightCvs por cima (ctx.drawImage)
```

`destination-out` multiplica o alpha do destino pelo inverso do alpha da fonte: onde o gradiente tem alpha 1, o pixel é apagado (vira luz); onde tem 0, fica escuro. **Flicker**: a cada 4 frames o raio recebe variação aleatória de ±15px.

### 7. Parallax de fundo

Cada camada tem um **fator de velocidade** (`0.18`, `0.44`, `0.68`) — objetos distantes se movem mais devagar, criando profundidade sem 3D. O índice inicial é calculado para iterar só sobre os elementos visíveis (culling).

### 8. Classes de entidades

`Player` (física AABB, tocha, knockback, checkpoint, hp 0–3), `Ghost` (patrulha + flutuação senoidal), `Bat` (trajetória elíptica via `cos`/`sin`), `Shade` (mini-sombra: persegue no escuro, definha na luz), `KeyItem`, `Heart`, `Spark` (partícula com física e fade), `Campfire` (acendível/apagável) e `Boss` (A Sombra: foge e sofre dano na luz). O `getter rect` é calculado na hora para sempre refletir a posição atual.

### 9. Ordem de renderização

```
drawBG → drawPlatform → drawWallTorch → drawExitDoor → drawKeys →
drawEnemies → drawSparks → player.draw → applyLight → drawHUD
```

A iluminação é aplicada sobre tudo; o HUD é desenhado **depois**, sempre legível. Os corações usam **curvas de Bezier cúbicas** — sem imagens.

### 10. Otimizações

`Math.floor`/`| 0` nas coordenadas (elimina sub-pixel), culling de tela para inimigos/decoração, parallax por índice calculado, canvas offscreen para a luz e `sparks.filter()` por frame (pool de partículas sem array crescente).

### 11. Estrutura de dados do nível

O nível é **declarativo** — arrays de objetos literais no topo do arquivo (`PLATFORMS`, `KEY_DEFS`, `ENEMY_DEFS`, `WALL_TORCHES`, `CAMPFIRES`, `BOSS`, `EXIT`). Não há mapa externo; `resetGame()` reinstancia tudo a partir dessas definições.

### 12. Responsividade do canvas

O canvas interno tem resolução lógica fixa; apenas o tamanho CSS é escalado via `Math.min(innerWidth/SW, innerHeight/SH)`, garantindo renderização pixel-perfect sem distorção e sem afetar o input de teclado.

---

## 📐 Design do Nível

| Elemento          | Detalhe                                              |
|-------------------|------------------------------------------------------|
| Fases             | 3 (duas de plataforma + arena do chefe)              |
| Chaves            | 4 por fase de plataforma — destrancam a porta        |
| Corações          | Recuperam 1 de vida (máx. 3)                          |
| Inimigos          | Fantasmas, morcegos, mini-sombras e o chefe          |
| Mecânica de luz   | Tocha que decai, tochas de parede e fogueiras acendíveis |
| Largura dos mapas | 4.200 px (fases 1–2) / 1.800 px (arena do chefe)     |

---

## 🧩 Possíveis Extensões

- Sistema de save/checkpoint persistente via `localStorage`
- Menu inicial e tela de opções
- Editor de nível via JSON/arquivo de configuração externo
- Novos padrões de ataque para o chefe (projéteis de sombra, invocações)
- Temas adicionais de fase (cemitério, castelo, floresta amaldiçoada)
- WebGL via **PixiJS** para shaders de luz ainda mais avançados

---

## 🎓 Entrega Acadêmica (SATC)

> Padrão de envio no AVA / e-mail: **(ETAPA) - (DIA DA TURMA) - Dungeon of Shadows**
> Ex.: `Etapa 2 GDD Final e Gold - Terça - Dungeon of Shadows`
> **Turma / Curso:** Engenharia de Software — *[preencher dia da turma]*

### Entregáveis

| Arquivo | O que é | Entrega |
|---|---|---|
| `index.html` | Jogo Versão Gold — jogável no navegador | Etapas 1 e 2 |
| `high-concept.html` | Pré-projeto visual | Etapa 1 |
| `gdd.html` | Game Design Document final, visual e navegável | Etapa 2 |
| `pitch.html` | Pitch de investimentos | Etapa 2 |

### Coerência com os critérios do professor

- **Formato visual, não documento de texto formal** — GDD e High Concept são apresentações navegáveis com tabelas, diagramas, paleta e telas anotadas (critério de 4 pontos por recursos visuais).
- **Documentação coerente com o jogo** — reflete o estado atual do `index.html`: 3 fases, chefe "A Sombra", inimigos Fantasma/Morcego/Mini-Sombra, fogueiras, checkpoints, decaimento da tocha e áudio procedural.
- **Evolução vs. versão prévia** — de 1 fase sem chefe para 3 fases completas com chefe, fogueiras, checkpoints e trilha — atende ao critério de "melhorias significativas".
- **Visão de futuro** — roadmap incluído no GDD.
- **Todos os tópicos do modelo de GDD** cobertos: Mecânicas (1.1–1.7), Narrativa (2.1–2.6), Estética (3.1–3.3), Level Design (4.1–4.2), Tecnologia e Referências.

---

## 🔊 Créditos de Áudio

A maior parte do áudio é gerada proceduralmente em tempo real via **Web Audio API** (drone ambiente, passos, dano, coleta de chave, vento, sons do boss etc.). O único arquivo de áudio externo é o som do fantasma:

- **`assets/sounds/ghost.mp3`** — "ghostly voices" por **ERH** ([freesound.org/people/ERH/sounds/36757](https://freesound.org/people/ERH/sounds/36757)), via [Wikimedia Commons](https://commons.wikimedia.org/wiki/File:ERH_-_ghostly_voices_(cc-by)_(freesound).mp3). Licença **[CC BY 3.0](https://creativecommons.org/licenses/by/3.0/)**.

---

## 📄 Licença

O código deste projeto está sob a licença **MIT**. O áudio de terceiros mantém suas próprias licenças (veja **Créditos de Áudio** acima).
