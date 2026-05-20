# Mapeamento de Arte e Áudio — Dungeon of Shadows
### Entrega: Versão Beta · Etapa 1

---

## Justificativa Geral

**Dungeon of Shadows não utiliza assets externos.** Toda a arte visual e todo o áudio são gerados proceduralmente em tempo de execução, via código JavaScript. Essa é uma decisão arquitetural intencional do projeto, não uma limitação.

---

## Arte Visual

| Elemento | Técnica | API Utilizada |
|---|---|---|
| Plataformas e chão | `fillRect` + gradiente linear | Canvas 2D |
| Jogador (corpo, capuz, olho) | `fillRect` composto | Canvas 2D |
| Chama da tocha | `bezierCurveTo` animado (oscilação senoidal) | Canvas 2D |
| Fantasmas | `ellipse` semitransparente + `shadowBlur` | Canvas 2D |
| Morcegos | `arc` + `moveTo/lineTo` (asas) | Canvas 2D |
| Chaves coletáveis | `arc` + `lineTo` (argola e dentes) | Canvas 2D |
| Corações do HUD | `bezierCurveTo` cúbico | Canvas 2D |
| Partículas de faísca | `arc` com física de gravidade e fade | Canvas 2D |
| Parallax (pilares, estalactites, caveiras) | Primitives geométricas com fator de deslocamento | Canvas 2D |
| Porta de saída | `fillRect` + `arc` (cadeado/brilho) | Canvas 2D |
| Iluminação dinâmica | `radialGradient` + `destination-out` | Canvas 2D |

**Resultado:** estilo vetorial/geométrico sombrio — sem sprites, sem pixel art, sem licenciamento de arte.

---

## Áudio

Todos os sons são gerados em tempo real via **Web Audio API** (nativa do browser). Nenhum arquivo `.mp3`, `.wav` ou `.ogg` é utilizado.

| Som | Quando Toca | Técnica |
|---|---|---|
| **Drone ambiente** | Loop contínuo durante o jogo | `OscillatorNode` sawtooth 55 Hz + LFO de vibrato (0,25 Hz) + `BiquadFilterNode` lowpass |
| **Crepitar da chama** | Loop contínuo durante o jogo | Buffer de ruído branco em loop com `BiquadFilterNode` bandpass (900 Hz) |
| **Passos** | A cada 22 frames ao andar no chão | Burst de ruído branco `BiquadFilterNode` lowpass (260 Hz) com envelope rápido |
| **Coletar chave** | Ao pegar cada chave | Arpejo ascendente C5→E5→G5→C6 com `OscillatorNode` triangle e envelope ADSR |
| **Dano recebido** | Ao colidir com inimigo ou cair | Burst de ruído bandpass com pitch exponencialmente descendente (900→160 Hz) |
| **Game Over** | Ao perder todas as vidas | Cluster dissonante A2+D3+F#3 com `OscillatorNode` sawtooth e decay de 3 segundos |
| **Vitória** | Ao alcançar a saída com todas as chaves | Sequência melódica de 8 notas (C5→G6) com `OscillatorNode` triangle |

---

## Por que Zero Assets Externos?

1. **Coerência com a proposta:** O jogo é construído inteiramente em JavaScript vanilla + Canvas 2D API, sem frameworks. Usar assets externos quebraria essa coerência arquitetural.

2. **Custo zero de licenciamento:** Nenhum asset pago, nenhuma dependência de terceiros, nenhum risco de direitos autorais.

3. **Estética intencional:** O estilo procedural/geométrico é uma escolha artística, não uma limitação. Referências como *Limbo* (Playdead) demonstram que visuais minimalistas gerados proceduralmente podem ter alto impacto emocional.

4. **Distribuição simplificada:** Por não ter arquivos externos, o jogo inteiro é um único `index.html` — abre no browser sem instalação, sem servidor, sem dependências.

---

*Equipe de Desenvolvimento — Dungeon of Shadows · UNISATC · Março 2026*
