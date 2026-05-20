# GAME DESIGN DOCUMENT — DUNGEON OF SHADOWS
### Versão: GDD Preview (Alpha) · Data: Março 2026 · Autor: Bruno Supriano

---

## 1. Visão Geral

| Campo | Descrição |
|---|---|
| **Título** | Dungeon of Shadows |
| **Plataforma** | Browser (PC) — HTML5 / Canvas 2D |
| **Gênero** | Plataforma 2D de Horror Atmosférico |
| **Modo** | Single Player |
| **Tagline** | *"Sua única arma é a luz."* |
| **Resolução** | 1024 × 600 px (canvas fixo) |
| **Largura do Nível** | 4.200 px |
| **Tecnologia** | HTML5 + JavaScript ES6 + Canvas 2D API (sem frameworks) |

**Pitch em uma frase:** Dungeon of Shadows é um plataforma 2D de horror atmosférico onde o jogador carrega uma tocha como única ferramenta de sobrevivência, revelando uma masmorra amaldiçoada enquanto evita espíritos que espreitam na escuridão.

---

## 2. Core Mechanic — A Tocha (Iluminação Dinâmica)

### Diagrama Concêntrico da Core Mechanic

```
┌─────────────────────────────────────────────┐
│                                             │
│   ┌─────────────────────────────────────┐   │
│   │                                     │   │
│   │   ┌─────────────────────────────┐   │   │
│   │   │                             │   │   │
│   │   │   ┌─────────────────────┐   │   │   │
│   │   │   │                     │   │   │   │
│   │   │   │   ESCAPAR DA        │   │   │   │
│   │   │   │   MASMORRA          │   │   │   │
│   │   │   │   (Objetivo)        │   │   │   │
│   │   │   └─────────────────────┘   │   │   │
│   │   │      Navegar o nível         │   │   │
│   │   │      (Exploração)            │   │   │
│   │   └─────────────────────────────┘   │   │
│   │          Pular / Plataformar         │   │
│   │          (Movimento)                 │   │
│   └─────────────────────────────────────┘   │
│        ILUMINAR COM A TOCHA                 │
│        (Core Mechanic — Visibilidade)        │
└─────────────────────────────────────────────┘
```

### Como Funciona

A tocha é implementada via Canvas 2D com composição `destination-out`. A cada frame:

1. Um retângulo preto é desenhado sobre todo o canvas (escuridão total)
2. Uma camada temporária (`globalCompositeOperation = 'destination-out'`) é usada para "apagar" a escuridão ao redor do jogador
3. O buraco na escuridão é definido por um `radialGradient` — opacidade máxima no centro, zero nas bordas — criando uma queda suave da luz
4. Um offset aleatório pequeno é aplicado frame a frame para simular o flicker natural da chama
5. Partículas de faísca sobem do ponto da tocha com física de gravidade e fade-out
6. Uma tonalidade quente laranja (`rgba(255, 160, 50, 0.18)`) é sobreposta para colorir a cena iluminada

### Emergência

Toda decisão do jogador orbita a tocha:
- **Onde ir?** — Só é possível ver o que está no raio da tocha. Navegar = descobrir.
- **O que evitar?** — Inimigos podem estar a centímetros fora da luz. Avançar cega.
- **Como sobreviver?** — A luz mantém criaturas à distância (narrativamente). Apagar a tocha seria equivalente a morrer.
- **Tensão permanente** — Mesmo sem mecânica de "combustível" (na Alpha), a limitação visual cria pressão psicológica constante.

---

## 3. Mecânicas Secundárias

| Mecânica | Descrição | Relação com a Tocha |
|---|---|---|
| **Pular (Plataforming)** | O jogador pode saltar com física de gravidade (aceleração vertical) e AABB collision. Pulo único, sem double jump. | Sem a tocha, plataformas fora do cone de luz são invisíveis — pular às cegas é suicídio. |
| **Coletar Chaves** | 4 chaves douradas estão espalhadas pelo nível. Coletar todas ativa a porta de saída. | As chaves só são visíveis quando o jogador se aproxima o suficiente para que entrem no raio da tocha. |
| **Evitar Inimigos** | 6 fantasmas (patrulha senoidal horizontal) e 4 morcegos (trajetória elíptica). Contato causa 1 de dano. | Inimigos fora do raio da tocha são invisíveis — o jogador pode não ver um fantasma se aproximando até ser tarde demais. |
| **Gerenciar HP** | O jogador possui 3 corações. Ao sofrer dano, recebe iframes de invencibilidade (pisca). Com 0 HP, game over. | A pressa para evitar inimigos (que a escuridão oculta) é a principal fonte de dano. |

---

## 4. Core Gameplay — Convergência de Pilares

| Elemento | O que é | Como serve ao Core |
|---|---|---|
| **Narrativa** | Aventureiro preso em masmorra amaldiçoada, precisa de 4 chaves para sair | Justifica a exploração linear, a coleta de objetivos e a presença de criaturas hostis |
| **Estética** | Escuridão quase total, luz quente da tocha, paralaxe de pilares e caveiras, inimigos fantasmagóricos | Reforça a vulnerabilidade do jogador e a hostilidade do ambiente — a tocha é o único ponto de conforto visual |
| **Tecnologia** | `destination-out` + `radialGradient`, AABB collision, lerp camera, `requestAnimationFrame` | O sistema de iluminação dinâmica só é viável porque o Canvas 2D permite composição de camadas — é a tecnologia servindo diretamente a mecânica central |
| **Mecânicas** | Pular, coletar, evitar, sobreviver | Todas as ações são limitadas ou modificadas pelo raio de visão da tocha — nenhuma mecânica existe independente dela |

---

## 5. Boas Práticas de Game Design

### 5.1 Tutorial Implícito (Zona 1)

O jogo nunca exibe texto de instrução. A Zona 1 (0–1.000px) funciona como um **tutorial prático e silencioso**:

| Elemento | Posição | Função Pedagógica |
|---|---|---|
| Plataformas baixas e largas | 0–400px | Ensinar o pulo sem risco de queda fatal |
| Primeira chave visível | ~600px | Demonstrar o objetivo de coleta |
| Ausência total de inimigos | Zona inteira | Jogador aprende visibilidade da tocha sem punição |
| Primeiro fantasma | Início da Zona 2 | Introdução gradual da ameaça após domínio básico |

> "Show, don't tell" — a mecânica da tocha é compreendida pela experiência, não por instruções.

---

### 5.2 Teoria do Fluxo (Csikszentmihalyi)

O nível é projetado para manter o jogador no **canal de flow** — desafio proporcional à habilidade adquirida:

```
Dificuldade
    ▲
    │                                    ████ Zona 4 (Confronto)
    │                          ████████
    │                ██████████           Zona 3 (Desafio)
    │      ████████                       Zona 2 (Introdução)
    │████                                 Zona 1 (Tutorial)
    └────────────────────────────────► Habilidade do Jogador
```

- **Zona 1:** Dificuldade mínima → jogador constrói confiança
- **Zona 2:** Primeiros inimigos lentos e previsíveis → introdução controlada
- **Zona 3:** Sobreposição de padrões → exige atenção dividida
- **Zona 4:** Máxima densidade + HP possivelmente baixo → tensão climática

---

### 5.3 Reforço Positivo

Cada ação correta do jogador recebe feedback imediato e satisfatório:

| Ação | Feedback Visual | Feedback Sonoro |
|---|---|---|
| Coletar chave | Brilho dourado, chave some | Arpejo ascendente C5→C6 |
| Todas as chaves coletadas | Porta muda para verde brilhante | — |
| Chegar à saída | Overlay verde + "ESCAPOU!" | Sequência melódica de vitória |
| Sofrer dano | Player pisca (iframes visuais) | Burst de ruído descendente |
| Aproximar-se de inimigo | — | Som de proximidade sobe gradualmente |

---

### 5.4 Feedback Visual de Estado

O jogador nunca está sem informação sobre seu estado:

- **HP:** 3 corações desenhados com bezier — cheios (vermelho) vs vazios (cinza)
- **Chaves:** Contador "X / 4" no canto superior direito atualiza em tempo real
- **Invencibilidade:** Player pisca a cada 5 frames durante os iframes (~1,5s)
- **Porta travada:** Exibe "X / 4 chaves" sobre a porta enquanto cadeado aparece
- **Porta aberta:** Aura verde pulsante sinaliza que a saída está disponível

---

## 6. Narrativa

### Lore Completo

A masmorra não foi construída — ela cresceu. Durante séculos, as pedras absorveram o medo e a dor de centenas de almas que tentaram atravessá-la. Os espíritos que habitam seus corredores não são monstros — são fragmentos de aventureiros que vieram antes, agora incapazes de partir.

Um aventureiro sem nome cai pela abertura no chão de um castelo em ruínas. Não há escada. Não há corda. Só há escuridão e, perto da entrada, uma tocha abandonada no chão — como se alguém tivesse largado ali de propósito.

As criaturas não atacam por maldade. Elas são atraídas pelo calor dos vivos. A tocha as repele — por enquanto.

Quatro chaves douradas foram escondidas pelos guardiões da masmorra ao longo de seus corredores. A porta de saída, entalhada na parede mais distante, só abre quando todas as quatro são reunidas.

O adventureiro não tem armas. Não tem mapa. Não tem aliados. Só tem a chama nas mãos e a determinação de que esta masmorra não vai se tornar seu túmulo.

### Estrutura Narrativa (3 Atos)

**Ato I — Queda**
O jogador acorda (inicia) na zona inicial da masmorra. Introdução gradual das mecânicas: aprender a pular, descobrir a primeira chave, encontrar o primeiro fantasma. A escuridão é apresentada como personagem.

**Ato II — Busca**
O jogador atravessa as zonas intermediárias, coletando as 3 chaves restantes. A densidade de inimigos aumenta. As plataformas ficam mais exigentes. A tensão cresce — cada chave coletada é uma vitória, mas abre caminho para territórios mais perigosos.

**Ato III — Fuga**
Com todas as chaves coletadas, a porta de saída (na extremidade direita do nível) está acessível. O trajeto de volta — ou a corrida final para a saída — é o clímax. A masmorra "reage": maior densidade de inimigos na zona final.

### Tom Narrativo
Sem texto em tela. Sem diálogos. A narrativa é transmitida exclusivamente pela estética, pela disposição dos elementos e pelo comportamento dos inimigos. Narrativa ambiental pura — no estilo Limbo e Inside.

---

## 6. Estética Visual

### Paleta de Cores

| Nome | Hex | Uso |
|---|---|---|
| Preto Profundo | `#000000` | Camada de escuridão, fundo base |
| Cinza Caverna | `#1a1a2e` | Plataformas, chão |
| Cinza Pedra | `#2d2d44` | Detalhe de plataformas, paredes |
| Laranja Chama | `#ff8c00` | Centro da luz da tocha |
| Âmbar Quente | `#ffa032` | Halo externo da tocha, tonalidade ambiente |
| Branco Fantasma | `#e8e8ff` | Corpo dos fantasmas |
| Azul Espectral | `#8888cc` | Detalhe dos fantasmas, aura |
| Marrom Morcego | `#4a3728` | Corpo dos morcegos |
| Dourado Chave | `#ffd700` | Chaves coletáveis |
| Vermelho Coração | `#cc2222` | HUD — corações cheios |
| Cinza Coração | `#444444` | HUD — corações vazios |
| Verde Porta | `#2a5a2a` | Porta de saída inativa |
| Verde Brilhante | `#44ff44` | Porta de saída ativa (todas as chaves coletadas) |

### Estilo Visual

Todo o visual do jogo é gerado via Canvas 2D primitives — sem sprites, sem pixel art, sem assets externos. Os elementos visuais utilizam:

- `arc()` — corpos circulares de fantasmas, morcegos, partículas
- `bezierCurveTo()` / `quadraticCurveTo()` — corações no HUD, formas orgânicas
- `fillRect()` / `strokeRect()` — plataformas, porta de saída, chaves
- `radialGradient` — luz da tocha, brilho das chaves
- `linearGradient` — gradiente de profundidade nas plataformas

O resultado é um estilo **vetorial/geométrico sombrio** — não é pixel art nem arte realista, mas formas abstratas que evocam as referências (Limbo, Hollow Knight) pela composição e paleta, não por detalhe.

### Parallax — 3 Camadas

| Camada | Elemento | Fator de Velocidade | Função |
|---|---|---|---|
| Camada 1 (fundo) | Pilares de pedra | 0,18× | Profundidade máxima, lento — cria ilusão de distância |
| Camada 2 (meio) | Estalactites | 0,44× | Profundidade intermediária — texturiza o teto |
| Camada 3 (frente) | Crânios / Caveiras | 0,68× | Próximo ao plano de jogo — reforça a atmosfera macabra |

### Design Visual dos Inimigos

**Fantasmas:** Formas ovais brancas semitransparentes com aura azulada. Sem olhos definidos — apenas uma mancha escura sugerindo um rosto. Movimento flutuante pela função senoidal (oscilação vertical).

**Morcegos:** Formas de arco duplo (asas abertas) em marrom escuro com corpo central pequeno. Trajetória elíptica sugere voo real sem necessidade de animação frame a frame.

---

## 7. Estética Sonora

### Estado Atual (Beta)

**Áudio implementado via Web Audio API procedural** — todos os sons são gerados em código, sem arquivos externos.

| Som | Tipo | Implementação |
|---|---|---|
| **Drone Ambiente** | Loop contínuo | `OscillatorNode` sawtooth 55 Hz (A1) com LFO de vibrato (0,25 Hz) + filtro lowpass — cria tensão constante |
| **Crepitar da Chama** | Loop contínuo | Buffer de ruído branco em loop com `BiquadFilterNode` bandpass (900 Hz) — simula fogo |
| **Passos do Jogador** | SFX pontual | Burst de ruído filtrado lowpass (260 Hz), dispara a cada 22 frames quando no chão em movimento |
| **Coletar Chave** | SFX pontual | Arpejo ascendente C5→E5→G5→C6 com `OscillatorNode` triangle e envelope ADSR |
| **Dano Recebido** | SFX pontual | Burst de ruído bandpass com pitch exponencialmente descendente (900→160 Hz) |
| **Game Over** | SFX de estado | Cluster dissonante A2+D3+F#3 com decay lento (3s) em sawtooth |
| **Vitória** | SFX de estado | Sequência melódica de 8 notas (C5→G6) em triangle com envelope suave |

---

## 8. Tecnologia

### Stack

| Tecnologia | Versão/Tipo | Papel |
|---|---|---|
| HTML5 | — | Container do canvas, estrutura do documento |
| JavaScript | ES6 (vanilla) | Toda a lógica de jogo |
| Canvas 2D API | — | Renderização, iluminação, física visual |
| requestAnimationFrame | API nativa | Game loop principal |

**Zero frameworks. Zero bibliotecas. Zero assets externos.**

O jogo inteiro está contido em um único arquivo `index.html` com aproximadamente 750 linhas de JavaScript vanilla.

### Decisões Técnicas Justificadas

**`destination-out` para iluminação:**
A composição `globalCompositeOperation = 'destination-out'` permite "apagar" pixels de uma camada já desenhada. Ao desenhar o retângulo de escuridão primeiro e depois aplicar `destination-out` com o `radialGradient` da tocha, obtém-se um buraco suave na escuridão — exatamente o efeito de lanterna/tocha — sem shaders, sem WebGL.

**AABB (Axis-Aligned Bounding Box) para colisão:**
Colisão por retângulos alinhados aos eixos é suficiente para um plataforma 2D com plataformas horizontais. A resolução por eixo separado (separar correção horizontal da vertical) evita o bug clássico de "grudar na parede" ao saltar em arestas.

**Lerp Camera:**
A câmera não segue o jogador instantaneamente — ela interpola linearmente (`lerp`) em direção à posição alvo a cada frame. Isso cria um efeito de câmera "pesada" que dá sensação de peso ao movimento sem complexidade de código.

**`requestAnimationFrame` como game loop:**
`requestAnimationFrame` sincroniza o loop com o refresh rate do monitor, evitando desperdício de CPU em segundo plano e garantindo que a animação seja suave em qualquer taxa de atualização.

---

## 9. Level Design

### Filosofia

O nível único de 4.200px de largura é projetado em 4 zonas de dificuldade crescente. A progressão é linear (sem bifurcações) para garantir que o jogador encontre todas as chaves e compreenda a mecânica de iluminação progressivamente.

### Zonas do Nível

```
[INÍCIO]──[ZONA 1]────────[ZONA 2]──────────[ZONA 3]──────────[ZONA 4]──[SAÍDA]
   0px      400px           1200px             2200px             3200px   4100px

 ZONA 1       ZONA 2         ZONA 3             ZONA 4
 Tutorial    Introdução     Desafio           Confronto Final
 Sem         Primeiros      Densidade         Máxima densidade
 inimigos    fantasmas      alta              inimigos + plataformas
 Chave #1    Chave #2       Chave #3          Chave #4
             Morcegos       Fantasmas         Todos os tipos
                            + morcegos
```

### Distribuição Detalhada

**Zona 1 (0–1.000px) — Tutorial Implícito**
- Plataformas baixas, espaçadas, sem abismos fatais
- Nenhum inimigo — o jogador aprende movimento e visibilidade da tocha
- Chave #1 visível ao entrar no raio da tocha em plataforma elevada simples
- Propósito: estabelecer a mecânica de iluminação sem punição

**Zona 2 (1.000–2.200px) — Introdução ao Perigo**
- Primeiros 2 fantasmas (patrulha senoidal, velocidade baixa)
- 2 morcegos em trajetória elíptica ampla (previsível)
- Plataformas com espaçamentos médios — requer timing no pulo
- Chave #2 posicionada além de um abismo — requer pulo preciso
- Propósito: introduzir inimigos e colisão com plataformas exigentes

**Zona 3 (2.200–3.400px) — Desafio Real**
- 3 fantasmas + 2 morcegos — padrões começam a se sobrepor
- Plataformas verticais (pilares) requerem sequência de pulos
- Abismos mais longos — erro é fatal
- Chave #3 em área com fantasma guardião (não literalmente, mas posicionado para dificultar a coleta)
- Propósito: testar o domínio da mecânica de pulo em contexto de ameaça

**Zona 4 (3.400–4.200px) — Confronto Final**
- 1 fantasma + 2 morcegos restantes — maior concentração
- Plataformas mais curtas e espaçadas
- Chave #4 próxima à porta de saída — última coleta antes do fim
- Porta de saída: ativada (brilho verde) apenas se todas as 4 chaves foram coletadas
- Propósito: clímax — o jogador mais vulnerável (possivelmente com HP baixo) enfrenta o trecho mais denso

### Rationale de Colocação de Inimigos

Fantasmas em patrulha senoidal são colocados em corredores horizontais onde o jogador **deve** passar. Sua oscillação vertical torna a janela de passagem imprevisível — o jogador precisa observar o padrão antes de avançar.

Morcegos em trajetória elíptica cobrem verticalmente as áreas de pulo — forçam o jogador a calcular não apenas a distância horizontal, mas o timing vertical do inimigo.

---

## 10. Inimigos

| Inimigo | Tipo de Movimento | Velocidade | Range de Patrulha | Design Visual | Nível de Ameaça |
|---|---|---|---|---|---|
| **Fantasma (x6)** | Senoidal horizontal (X linear + Y senoidal) | 1,2–1,8 px/frame | ~300px horizontal | Oval branco semitransparente, aura azul, face vagamente sugerida | Médio — previsível mas constante |
| **Morcego (x4)** | Elíptico (Math.cos/sin em torno de ponto fixo) | Velocidade angular 0,03 rad/frame | Elipse ~200×100px | Arco duplo (asas), corpo central escuro | Alto — cobertura vertical dificulta pulos |

**Comportamento de dano:** Contato com hitbox do inimigo remove 1 coração e ativa iframes de invencibilidade (jogador pisca por ~1,5s). Durante os iframes, nenhum dano adicional é computado.

**Sem sistema de agro:** Na versão Alpha, os inimigos não reagem ao jogador — apenas executam seus padrões. Isso é planejado como ponto de melhoria (ver Seção 14).

---

## 11. HUD e Interface

| Elemento | Posição na Tela | Informação Comunicada | Implementação |
|---|---|---|---|
| **Corações (3×)** | Canto superior esquerdo | HP atual do jogador | Desenhados com `bezierCurveTo` — cheios em vermelho, vazios em cinza |
| **Contador de Chaves** | Canto superior direito | Quantidade de chaves coletadas (ex: "🗝 2/4") | Texto simples + ícone desenhado |
| **Overlay de Game Over** | Centro do canvas | Informação de morte + instrução de reinício | Semi-transparente preto, texto branco |
| **Overlay de Vitória** | Centro do canvas | Confirmação de conclusão + instrução de reinício | Semi-transparente escuro, texto dourado |

**Filosofia de HUD:** Minimalista. Apenas as informações estritamente necessárias para decisões de gameplay (vida restante e progresso de coleta). Sem mapa, sem cronômetro, sem pontuação — para preservar a imersão atmosférica.

---

## 12. Estados do Jogo

### Máquina de Estados

```
                    ┌─────────────┐
                    │    TITLE    │
                    │  (Tela de   │
                    │   Início)   │
                    └──────┬──────┘
                           │ Pressiona qualquer tecla
                           ▼
                    ┌─────────────┐
              ┌────►│   PLAYING   │◄────┐
              │     │  (Em Jogo)  │     │
              │     └──────┬──────┘     │
              │            │            │
              │    ┌───────┴───────┐    │
              │    │               │    │
              │    ▼               ▼    │
              │ ┌──────┐      ┌───────┐ │
              │ │ DEAD │      │  WON  │ │
              │ │(Game │      │(Vitó- │ │
              │ │ Over)│      │ ria)  │ │
              │ └──┬───┘      └───┬───┘ │
              │    │              │     │
              │    └──────┬───────┘     │
              │           │ R (restart) │
              └───────────┘─────────────┘
```

**TITLE:** Tela inicial com título do jogo e instrução para iniciar. (Planejado — na Alpha atual, o jogo inicia diretamente.)

**PLAYING:** Estado de gameplay ativo. O game loop roda, física é processada, colisões são detectadas, inimigos se movem.

**DEAD:** HP chega a 0. Overlay de Game Over é exibido. Input de gameplay desabilitado. Tecla R reinicia.

**WON:** Jogador colide com a porta de saída com todas as 4 chaves. Overlay de Vitória é exibido. Tecla R reinicia.

---

## 13. Versão Atual (MVP / Alpha)

### Implementado

- Motor de renderização Canvas 2D completo
- Sistema de iluminação dinâmica (tocha com flicker, faíscas, tonalidade quente)
- Parallax de 3 camadas funcional
- Física de jogador: gravidade, pulo, AABB collision com plataformas
- 6 fantasmas com patrulha senoidal
- 4 morcegos com voo elíptico
- 4 chaves coletáveis com lógica de contagem
- Porta de saída com ativação condicional (todas as chaves)
- Sistema de HP (3 corações) + iframes de invencibilidade
- HUD: corações desenhados com bezier + contador de chaves
- Estados PLAYING / DEAD / WON com overlays
- Câmera com lerp seguindo o jogador
- Level completo de 4.200px com plataformas e inimigos posicionados

### Não Implementado (Pendente)

- ~~Sistema de áudio~~ ✅ Implementado via Web Audio API (Beta)
- Tela de título/menu inicial
- Checkpoints (morte = recomeço do início)
- Mecânica de combustível da tocha
- Sistema de pontuação / cronômetro
- Narrativa visual (cutscenes, textos ambientais)
- Inimigos com estados (agro, ataque, patrulha)
- Efeitos de partícula para coleta de chaves
- Animação de abertura da porta

---

## 14. Visão de Futuro

### Beta (Milestone 2 — Abril 2026)

**Prioridade 1 — Áudio Procedural (Web Audio API)**
Implementação de drone ambiente, passos, crepitar da chama e SFX de eventos. Impacto imediato na imersão.

**Prioridade 2 — Checkpoints**
Sistema de 2–3 checkpoints no nível (tocha no chão que o jogador "acende"). Reduz frustração sem eliminar o desafio.

**Prioridade 3 — Mecânica de Combustível da Tocha**
A tocha tem um medidor de combustível que diminui com o tempo. O jogador precisa encontrar tochas extras no nível para reabastecer. Adiciona urgência e nova camada estratégica à exploração — o que coletar primeiro: a chave ou o combustível?

### Gold (Milestone 4 — Junho 2026)

**Estados de Inimigos**
Fantasmas e morcegos ganham estados de patrulha → agro → ataque. Ao entrar no raio de visão do inimigo (cônico), ele acelera em direção ao jogador. Faz o sistema de iluminação ainda mais central — a luz que te protege também te delata.

**Sistema de Pontuação**
Score baseado em tempo de conclusão + corações restantes + ordem de coleta de chaves. Incentiva replays e speedrunning.

**Tela de Título e Narrativa Visual**
Sequência de entrada com texto ambiental narrando a queda do aventureiro. Reforça a narrativa sem quebrar a estética silenciosa.

**Múltiplos Níveis / Geração Procedural**
Geração procedural de layouts de masmorra para runs infinitas (roguelite mode opcional).

**Publicação**
- Lançamento em itch.io (pay-what-you-want)
- Avaliação para Steam (Steamworks SDK + taxa de publicação)
- Possível port mobile via PWA (Progressive Web App)
