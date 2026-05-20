# ⚙️ Dungeon of Shadows — Documentação Técnica

> Explicação detalhada das mecânicas de programação, sistemas, estruturas de dados e decisões de implementação do jogo.

---

## Visão Geral da Arquitetura

O jogo é um loop imperativo clássico de 60fps estruturado em três fases por frame:

```
requestAnimationFrame
        │
        ├── update()   → lógica: input, física, colisão, inimigos, coleta
        └── draw()     → renderização: fundo, plataformas, entidades, luz, HUD
```

Não há framework — tudo é JavaScript puro operando diretamente sobre a **Canvas 2D API**.

---

## 1. Game Loop

```javascript
(function loop() {
  update();
  draw(ctx);
  requestAnimationFrame(loop);
})();
```

`requestAnimationFrame` é uma função nativa do browser que executa o callback sincronizado ao refresh rate do monitor (geralmente 60fps). É equivalente a um `setInterval` de 16.6ms, mas com pausa automática quando a aba está inativa e sem drift de tempo acumulado.

O loop é uma **IIFE** (Immediately Invoked Function Expression) que se auto-agenda a cada frame. Não há controle explícito de `deltaTime` — a física é frame-rate dependente (assumindo 60fps constantes).

---

## 2. Máquina de Estados do Jogo

```javascript
let state = 'playing'; // | 'dead' | 'won'
```

Um único `string` controla o estado global. A função `update()` retorna imediatamente se `state !== 'playing'`, congelando a lógica. A função `draw()` sempre roda, renderizando os overlays de game over e vitória sobre o frame pausado.

Transições:
```
playing ──(player.dead)──→ dead
playing ──(tocou a saída com todas as chaves)──→ won
dead    ──(tecla R)──→ playing  [via resetGame()]
won     ──(tecla R)──→ playing  [via resetGame()]
```

---

## 3. Sistema de Input

```javascript
const input = {};
window.addEventListener('keydown', e => { input[e.code] = true; });
window.addEventListener('keyup',   e => { input[e.code] = false; });
```

O input é um **mapa de estado de teclas** (não de eventos). Em vez de reagir a eventos pontuais de `keydown`, o objeto `input` mantém quais teclas estão pressionadas *neste frame*. Isso permite checar `input['KeyA']` a cada frame sem perder inputs e sem acumular eventos.

**Jump buffering** é implementado com uma variável auxiliar:
```javascript
if (jump && !jumpLast) player.jump(); // só pula no frame em que a tecla foi pressionada
jumpLast = jump;
```
Isso garante que segurar `Space` não cause pulos repetidos.

---

## 4. Física do Jogador

### 4.1 Gravidade e velocidade terminal

```javascript
this.vy = Math.min(this.vy + GRAVITY, 22);
```

A cada frame, `GRAVITY = 0.55` é somado à velocidade vertical. `Math.min(..., 22)` é a **velocidade terminal** — impede que o jogador acelere indefinidamente em quedas longas, o que tornaria o controle impreciso.

### 4.2 Colisão AABB (Axis-Aligned Bounding Box)

AABB é o sistema de colisão mais simples e eficiente para plataformers 2D: cada entidade é um retângulo alinhado aos eixos. Dois retângulos colidem se:

```javascript
const hits = (a, b) =>
  a.x < b.x + b.w && a.x + a.w > b.x &&
  a.y < b.y + b.h && a.y + a.h > b.y;
```

Esta é a **separação de eixos simplificada** — quatro comparações de intervalo. Se qualquer eixo não se sobrepõe, não há colisão.

### 4.3 Resolução de colisão em dois eixos separados

O ponto crítico do platformer é resolver colisões corretamente. O movimento é separado em dois passes:

```javascript
// Passo 1: move em X, resolve colisões horizontais
this.x += this.vx;
for (const p of plats) {
  if (hits(this.rect, p)) {
    if (this.vx > 0) this.x = p.x - this.w;   // bateu na parede direita
    else             this.x = p.x + p.w;        // bateu na parede esquerda
    this.vx = 0;
  }
}

// Passo 2: move em Y, resolve colisões verticais
this.y += this.vy;
for (const p of plats) {
  if (hits(this.rect, p)) {
    if (this.vy >= 0) { this.y = p.y - this.h; this.onGround = true; }  // caindo
    else              { this.y = p.y + p.h; }                            // subindo
    this.vy = 0;
  }
}
```

**Por que separar os eixos?** Se o movimento fosse resolvido em um único passo combinado, um personagem movendo-se diagonalmente em direção a um canto poderia "grudar" ou "escapar" de forma inesperada. A separação garante que cada eixo seja resolvido de forma independente e previsível.

**`onGround`** é resetado para `false` a cada frame e só vira `true` quando uma colisão vertical descendente é detectada — isso permite que o estado de "no chão" seja sempre exato para aquele frame.

---

## 5. Sistema de Câmera

```javascript
camX = lerp(camX, player.x - SW / 2 + player.w / 2, 0.10);
camY = lerp(camY, player.y - SH / 2 + player.h / 2, 0.10);
camX = clamp(camX, 0, WORLD_W - SW);
camY = clamp(camY, -80, WORLD_H - SH);
```

### Lerp (Linear Interpolation)

```javascript
const lerp = (a, b, t) => a + (b - a) * t;
```

A câmera não "pula" instantaneamente para o jogador. A cada frame, ela avança **10%** da distância restante até o alvo. Isso cria uma câmera com inércia ("câmera elástica") — efeito suave sem código de física adicional.

Com `t = 0.10` e 60fps, a câmera percorre ~99% da distância em ~45 frames (~0.75s). Com `t = 1.0` seria instantânea; com `t = 0.01` seria muito lenta.

### Clamp de bordas

```javascript
const clamp = (v, lo, hi) => Math.min(Math.max(v, lo), hi);
```

Impede que a câmera mostre área fora do mapa. `camX` nunca vai abaixo de 0 (borda esquerda) nem acima de `WORLD_W - SW` (borda direita).

### Projeção de coordenadas mundo → tela

Todos os objetos são desenhados com coordenadas subtraídas da câmera:
```javascript
const sx = Math.floor(entity.x - camX);
const sy = Math.floor(entity.y - camY);
```

`Math.floor` elimina sub-pixels, evitando tremidos causados por posições decimais durante a interpolação da câmera.

---

## 6. Sistema de Iluminação

Este é o núcleo técnico do jogo. Usa um **canvas offscreen** e operações de composição do Canvas 2D.

### 6.1 Canvas offscreen

```javascript
const lightCvs = document.createElement('canvas');
lightCvs.width = SW; lightCvs.height = SH;
const lctx = lightCvs.getContext('2d');
```

Um segundo canvas (invisível, não adicionado ao DOM) é usado exclusivamente para construir a máscara de luz. Ao final do frame, ele é copiado sobre o canvas principal.

### 6.2 Fluxo de composição

```
[lightCvs]  ← preenchido com preto opaco (escuridão)
    │
    ├── destination-out + radialGradient  ← perfura a escuridão
    │       (onde gradiente tem alpha 1 → pixel torna-se transparente)
    │
    ├── source-over + warm radialGradient  ← adiciona tonalidade laranja
    │
    └── source-over + inner radialGradient ← inner glow pulsante

[canvas principal] ← recebe lightCvs por cima (ctx.drawImage)
```

### 6.3 `destination-out` explicado

`globalCompositeOperation = 'destination-out'` opera assim:
- O pixel **destino** (já existente no canvas) tem seu alpha *multiplicado pelo inverso* do alpha da fonte
- Onde a fonte tem alpha `1.0` → destino vira completamente transparente (apagado)
- Onde a fonte tem alpha `0.0` → destino não muda

```javascript
const hole = lctx.createRadialGradient(tx, ty, 0, tx, ty, radius);
hole.addColorStop(0,    'rgba(0,0,0,1)');    // centro: apaga tudo (luz total)
hole.addColorStop(0.55, 'rgba(0,0,0,0.78)'); // meio: apaga 78% (meia-luz)
hole.addColorStop(1.0,  'rgba(0,0,0,0)');    // borda: não apaga nada (escuro)
```

O resultado: a camada preta de escuridão tem um "buraco" suave e radial centrado na tocha — exatamente onde a luz deveria estar.

### 6.4 Flicker da tocha

```javascript
if (flickT % 4 === 0) flicker = ((Math.random() - 0.5) * 30) | 0;
// ...
applyLight(ctx, tp.x, tp.y, TORCH_R + flicker);
```

A cada 4 frames (~15 vezes por segundo), o raio da tocha recebe uma variação aleatória de ±15px. O operador `| 0` é um truque de performance equivalente a `Math.floor()` para inteiros positivos e negativos.

---

## 7. Parallax de Fundo

```javascript
const pi0 = Math.floor((cx * 0.18) / 185) - 1;
for (let i = pi0; i <= pi0 + Math.ceil(SW / 185) + 3; i++) {
  const px = Math.floor(i * 185 - cx * 0.18);
  // desenha pilar em px
}
```

### Como funciona

Cada camada de parallax tem um **fator de velocidade** menor que 1 (`0.18`, `0.44`, `0.68`). Isso faz objetos mais distantes parecerem se mover mais devagar — criando profundidade sem 3D.

A posição de cada elemento na tela é: `posicao_mundo * fator - camX * fator`.

O índice inicial `pi0` é calculado para que o loop só itere sobre os elementos **visíveis na tela**, evitando iterar sobre o mundo inteiro a cada frame (otimização de culling).

---

## 8. Classes de Entidades

### 8.1 `Player`

| Propriedade | Tipo   | Descrição                                    |
|-------------|--------|----------------------------------------------|
| `x, y`      | float  | Posição no espaço do mundo                   |
| `vx, vy`    | float  | Velocidade nos eixos X e Y                   |
| `onGround`  | bool   | Atualizado por frame pela colisão vertical    |
| `facing`    | -1 / 1 | Direção que o personagem está olhando         |
| `hp`        | int    | Pontos de vida (0–3)                         |
| `inv`       | int    | Contador de frames de invencibilidade         |
| `tick`      | int    | Contador de frames global (usado em animações)|
| `keys`      | int    | Chaves coletadas                              |

O `getter rect` retorna um objeto `{x, y, w, h}` calculado na hora — não é armazenado — para garantir que sempre reflete a posição atual.

### 8.2 `Ghost`

Movimento por **patrulha simples**:
```javascript
this.x += this.speed * this.dir;
if (this.x > this.bx + this.patrol) this.dir = -1;
else if (this.x < this.bx)          this.dir =  1;
```
Um ponto de origem `bx` e um `patrol` (range em pixels) definem os limites. A flutuação vertical é uma função seno:
```javascript
this.y = this.by + Math.sin(this.t * 0.045) * 10;
```
O multiplicador `0.045` define a frequência (lenta e suave). O `10` define a amplitude (±10px).

### 8.3 `Bat`

Movimento por **trajetória elíptica**:
```javascript
this.angle += 0.028;
this.x = this.bx + Math.cos(this.angle) * this.patrol * 0.5;
this.y = this.by + Math.sin(this.angle * 1.4) * 55;
```
O ângulo avança constante a cada frame. `cos` e `sin` convertem o ângulo em coordenadas X e Y de uma elipse. O fator `1.4` no eixo Y cria uma elipse irregular (não circular), tornando o movimento menos previsível.

### 8.4 `Spark` (sistema de partículas)

```javascript
class Spark {
  constructor(x, y) {
    this.vx = rng(-2.2, 2.2);   // velocidade horizontal aleatória
    this.vy = rng(-3.0, -0.5);  // sempre sobe inicialmente
    this.life = rng(14, 30) | 0;
    this.maxLife = this.life;
    this.size = rng(1.5, 3.5);
  }
  update() {
    this.x += this.vx; this.y += this.vy;
    this.vy += 0.09;   // gravidade leve
    this.vx *= 0.97;   // arrasto do ar (decaimento exponencial)
    this.life--;
  }
}
```

O pool de partículas é gerenciado com `filter`:
```javascript
sparks = sparks.filter(s => s.life > 0);
```
Partículas mortas são descartadas a cada frame. O tamanho e a opacidade são proporcionais ao `life / maxLife` — a partícula encolhe e some ao mesmo tempo.

---

## 9. HUD e Renderização de Interface

O HUD é desenhado **após** a iluminação, garantindo que ele seja sempre legível independente da escuridão:

```javascript
// Ordem de renderização:
drawBG()         // 1. Fundo com parallax
drawPlatform()   // 2. Plataformas
drawWallTorch()  // 3. Tochas decorativas
drawExitDoor()   // 4. Porta
drawKeys()       // 5. Chaves coletáveis
drawEnemies()    // 6. Inimigos
drawSparks()     // 7. Partículas
player.draw()    // 8. Jogador
applyLight()     // 9. ← Escuridão + iluminação (sobre tudo)
drawHUD()        // 10. ← HUD (acima da escuridão)
```

Os corações são desenhados com **curvas de Bezier cúbicas** (`bezierCurveTo`), que permitem curvas suaves com 4 pontos de controle — produzindo a forma de coração sem usar imagens.

---

## 10. Otimizações Presentes

| Técnica | Onde | Efeito |
|---|---|---|
| `Math.floor()` nas coords de desenho | Todas as entidades | Elimina sub-pixel rendering |
| Culling de tela (`sx < -60 \|\| sx > SW + 60`) | Inimigos, decorações | Não desenha o que está fora da tela |
| Parallax por índice calculado | `drawBG()` | Loop itera apenas sobre elementos visíveis |
| `\| 0` para truncar floats | Flicker, partículas | Mais rápido que `Math.floor` para inteiros |
| Canvas offscreen para luz | `applyLight()` | Composição em buffer separado, sem reflow do DOM |
| `sparks.filter()` por frame | Loop principal | Pool de partículas sem overhead de array crescente |

---

## 11. Estrutura de Dados do Nível

O nível é declarativo — definido como arrays de objetos literais no topo do arquivo:

```javascript
const PLATFORMS = [ {x, y, w, h}, ... ];  // 24 plataformas
const KEY_DEFS  = [ {x, y}, ... ];        // 4 chaves
const ENEMY_DEFS = [ {type, x, y, patrol}, ... ]; // 10 inimigos
const WALL_TORCHES = [ {x, y}, ... ];     // 9 tochas decorativas
const EXIT = {x, y};                      // 1 saída
```

Não há arquivo de mapa externo — o nível inteiro vive em memória como constantes JavaScript. `resetGame()` reinstancia todas as entidades a partir dessas definições, garantindo que reiniciar o jogo restaure o estado original.

---

## 12. Responsividade do Canvas

```javascript
function fitCanvas() {
  const scale = Math.min(window.innerWidth / SW, window.innerHeight / SH);
  canvas.style.width  = `${SW * scale}px`;
  canvas.style.height = `${SH * scale}px`;
}
window.addEventListener('resize', fitCanvas);
```

O canvas **interno** sempre tem `1024 × 600` pixels (resolução lógica fixa). Apenas o tamanho CSS é escalado. Isso garante que a renderização seja pixel-perfect na resolução nativa e escale proporcionalmente em telas menores, sem distorção. O input de teclado não é afetado pelo scale pois não depende de coordenadas de tela.
