# 🕯️ Dungeon of Shadows

> Um jogo de plataforma 2D com tema de terror ambientado em masmorras sombrias. O jogador explora um dungeon escuro carregando uma tocha, coletando chaves e fugindo de criaturas sobrenaturais.

---

## 📖 Sobre o Projeto

**Dungeon of Shadows** é um jogo de plataforma 2D desenvolvido inteiramente em JavaScript puro com Canvas 2D API, sem uso de assets externos — todos os gráficos são desenhados via primitivas vetoriais em tempo real.

O destaque técnico do projeto é o **sistema de iluminação dinâmica por tocha**: uma máscara de escuridão é aplicada sobre o frame via `globalCompositeOperation: 'destination-out'`, perfurando a camada escura com um `radialGradient` suavíssimo e acelerado por GPU. O resultado é uma penumbra real com borda suave, tonalidade laranja-quente próxima à chama e flicker aleatório — exatamente o efeito presente em jogos como *Don't Starve* e *Hollow Knight*.

O objetivo é atravessar um dungeon de **4.200 pixels de largura**, coletar as **4 chaves** espalhadas por plataformas perigosas e chegar à **porta de saída** sem ser derrotado.

---

## 🎮 Gameplay

| Ação       | Teclas                       |
|------------|------------------------------|
| Mover      | `A` / `D` ou `←` / `→`      |
| Pular      | `Space` / `W` / `↑`         |
| Reiniciar  | `R` (após game over/vitória) |

### Objetivo

1. Explore o dungeon iluminando o caminho com sua tocha
2. Colete as **4 chaves douradas** espalhadas pelo mapa
3. Chegue à **porta de saída** no fim do nível
4. Sobreviva com **3 vidas** — fantasmas e morcegos drenam uma vida ao toque

### Inimigos

| Inimigo      | Comportamento                                              |
|--------------|------------------------------------------------------------|
| **Fantasma** | Patrulha plataformas com movimento flutuante senoidal; glow de shader e gotas de sangue |
| **Morcego**  | Voa em trajetória elíptica; asas animadas com flap, olhos vermelhos |

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
TesteJogo/
├── index.html    # Jogo completo (HTML + CSS + JS em arquivo único)
└── README.md     # Este arquivo
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
    ├── class Player          Física AABB, colisão, animação, tocha com bezier
    ├── class Ghost           Patrulha senoidal, glow via shadowBlur, gotas de sangue
    ├── class Bat             Trajetória elíptica, asas animadas, olhos vermelhos
    ├── class KeyItem         Hover senoidal, brilho dourado
    ├── class Spark           Partícula de faísca com física e fade
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

| Elemento          | Quantidade | Detalhe                                  |
|-------------------|------------|------------------------------------------|
| Plataformas       | 24         | Alturas variadas, incluindo pilares/paredes |
| Chaves            | 4          | Requerem platforming preciso             |
| Fantasmas         | 6          | Distribuídos ao longo de todo o percurso |
| Morcegos          | 4          | Zonas superiores do mapa                 |
| Tochas decorativas| 9          | Distribuídas pelo cenário com mini-luz   |
| Largura do mapa   | 4.200 px   | Câmera com scroll horizontal             |

---

## 🧩 Possíveis Extensões

- Adicionar áudio com **Web Audio API** (passos, música ambiente, grunhidos dos inimigos)
- Criar múltiplas fases com temas (cemitério, castelo, floresta amaldiçoada)
- Sistema de save via `localStorage`
- Editor de nível via JSON/arquivo de configuração externo
- Efeito de névoa animada com superfícies alpha sobrepostas
- Chefe final com padrões de ataque
- WebGL via **PixiJS** para shaders de luz ainda mais avançados

---

## 📄 Licença

Este projeto está sob a licença **MIT**. Consulte o arquivo `LICENSE` para mais detalhes.
