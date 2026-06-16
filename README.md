# 🕯️ Dungeon of Shadows

> Um jogo de plataforma 2D com tema de terror ambientado em masmorras sombrias. O jogador explora dungeons escuros carregando uma tocha, coletando chaves, fugindo de criaturas sobrenaturais e, ao final, enfrentando um chefe que teme a luz.

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
- Zero dependências, zero assets — abre diretamente no navegador

---

## 🗂️ Estrutura do Projeto

```
DungeonofShadows/
├── index.html              # Jogo completo (HTML + CSS + JS em arquivo único)
├── assets/
│   └── sounds/
│       └── ghost.mp3       # Som do fantasma (CC BY 3.0 — ver Créditos de Áudio)
└── README.md               # Este arquivo
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
| **CSS Flexbox**     | Centralização do canvas na tela                                        |

> Nenhuma biblioteca, framework ou dependência externa. Apenas um arquivo `.html`.

---

## 🚀 Como Rodar

### Método 1 — Abrir diretamente (recomendado)

Basta fazer **duplo clique** em `index.html`. O jogo abre no navegador imediatamente.

> Compatível com Chrome, Firefox, Edge, Safari — qualquer navegador moderno.

### Método 2 — Servidor local (opcional)

Se preferir rodar via servidor HTTP local:

```bash
# Node.js (com npx)
npx serve .
```

Depois acesse `http://localhost:8080` no navegador.

---

## 🏗️ Arquitetura do Código

```
index.html
│
├── <style>                   Centralização do canvas, fundo preto
│
└── <script>
    │
    ├── Constantes            Física, dimensões, dados do nível (plataformas, inimigos, chaves)
    │
    ├── Input                 keydown/keyup → objeto { code: bool }
    │
    ├── class Player          Física AABB, colisão, animação, tocha, knockback, checkpoint
    ├── class Ghost           Patrulha senoidal, glow via shadowBlur, gotas de sangue
    ├── class Bat             Trajetória elíptica, asas animadas, olhos vermelhos
    ├── class Shade           Mini-sombra: persegue no escuro, recua/definha na luz
    ├── class KeyItem         Hover senoidal, brilho dourado
    ├── class Heart           Coletável de vida com pulso e brilho vermelho
    ├── class Spark           Partícula de faísca com física e fade
    ├── class Campfire        Fogueira acendível/apagável (arena do chefe)
    ├── class Boss            A Sombra: foge e sofre dano na luz; investidas no escuro
    │
    ├── applyLight()          Sistema de iluminação: destination-out + radialGradient
    │
    ├── drawBG()              Parallax 3 camadas: pilares → estalactites → caveiras
    ├── drawPlatform()        Plataforma de pedra com musgo e gretas vetoriais
    ├── drawWallTorch()       Tochas decorativas animadas no cenário
    ├── drawExitDoor()        Porta bloqueada/desbloqueada com glow
    ├── drawHUD()             Corações bezier + contador de chaves + dica inicial
    │
    ├── update()              Input → física → inimigos → coleta → câmera lerp
    ├── draw()                Renderiza tudo em ordem de profundidade
    │
    └── loop()                requestAnimationFrame — 60fps
```

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
- Editor de nível via JSON/arquivo de configuração externo
- Efeito de névoa animada com superfícies alpha sobrepostas
- Novos padrões de ataque para o chefe (projéteis de sombra, invocações)
- Temas adicionais de fase (cemitério, castelo, floresta amaldiçoada)
- WebGL via **PixiJS** para shaders de luz ainda mais avançados

---

## 🔊 Créditos de Áudio

A maior parte do áudio é gerada proceduralmente em tempo real via **Web Audio API** (drone ambiente, passos, dano, coleta de chave, vento, sons do boss etc.). O único arquivo de áudio externo é o som do fantasma:

- **`assets/sounds/ghost.mp3`** — "ghostly voices" por **ERH** ([freesound.org/people/ERH/sounds/36757](https://freesound.org/people/ERH/sounds/36757)), via [Wikimedia Commons](https://commons.wikimedia.org/wiki/File:ERH_-_ghostly_voices_(cc-by)_(freesound).mp3). Licença **[CC BY 3.0](https://creativecommons.org/licenses/by/3.0/)**.

---

## 📄 Licença

O código deste projeto está sob a licença **MIT**. Consulte o arquivo `LICENSE` para mais detalhes.
O áudio de terceiros mantém suas próprias licenças (veja **Créditos de Áudio** acima).
