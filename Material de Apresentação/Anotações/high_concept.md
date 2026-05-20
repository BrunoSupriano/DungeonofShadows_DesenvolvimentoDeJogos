# HIGH CONCEPT — DUNGEON OF SHADOWS

---

## Título e Tagline

| | |
|---|---|
| **Título** | Dungeon of Shadows |
| **Tagline** | *"Sua única arma é a luz."* |
| **Gênero** | Plataforma 2D de Horror Atmosférico |
| **Plataforma** | Browser (PC) — HTML5 / Canvas 2D |
| **Modo** | Single Player |
| **Status** | Alpha — Versão Jogável |

---

## Gêneros

**Principal:** Plataforma 2D de Horror Atmosférico

**Secundários:** Puzzle de Exploração · Survival · Dungeon Crawler

---

## Referências

| Jogo | Influência |
|---|---|
| **Hollow Knight** | Atmosfera opressiva, level design labiríntico, inimigos com padrões elegantes |
| **Limbo** | Paleta monocromática/sombria, horror silencioso, narrativa implícita |
| **Don't Starve** | Mecânica de luz como sobrevivência, escuridão como inimigo ativo |
| **Celeste** | Plataforma precisa, feedback de movimento, progressão de habilidade |
| **Amnesia: The Dark Descent** | Sanidade/medo como mecânica central, iluminação dinâmica, atmosfera claustrofóbica |

---

## Características

`Horror Atmosférico` `Iluminação Dinâmica` `Plataforma 2D` `Single Player` `Browser Nativo` `Zero Assets Externos` `Dungeon Crawler` `Narrativa Implícita` `Parallax Scrolling` `Inimigos Procedurais`

---

## Mecânicas (Tags)

`Tocha Dinâmica` `Física AABB` `Gravidade e Pulo` `Coleta de Chaves` `Patrulha Senoidal (Fantasmas)` `Voo Elíptico (Morcegos)` `Sistema de HP (3 Corações)` `Iframes de Invencibilidade` `Câmera com Lerp` `Parallax 3 Camadas` `Partículas de Faísca` `Flicker da Chama`

---

## Lore

> *Em algum momento entre o entardecer e a meia-noite, o aventureiro pisou na pedra errada.*
>
> A masmorra existe há séculos sob as ruínas de um castelo esquecido. Suas paredes estão impregnadas com os espíritos de todos que tentaram atravessá-la — e falharam. Quatro chaves douradas selam a única saída. Elas foram escondidas pelos guardiões da masmorra para garantir que ninguém saísse vivo.
>
> O único aliado do aventureiro é uma tocha encontrada no chão da entrada. Sua luz mantém as criaturas à distância — mas por quanto tempo a chama vai durar?

**Tom:** Sombrio, silencioso, opressivo. Horror de atmosfera, não de susto.

**Estrutura narrativa:** Queda → Busca → Fuga

---

## Gameplay

**Objetivo Principal:** Coletar as 4 chaves douradas espalhadas pela masmorra e alcançar a porta de saída.

**Loop Central:**
1. Explorar o nível (4.200px de largura) usando a tocha para enxergar
2. Plataformar por obstáculos e abismos
3. Evitar fantasmas (patrulha senoidal) e morcegos (voo elíptico)
4. Coletar chaves enquanto gerencia 3 pontos de vida
5. Abrir a porta de saída ao coletar todas as chaves

**Sessão de jogo estimada:** 5–15 minutos por run

**Mecânica Central — A Tocha:**
A tela é coberta por uma camada de escuridão (`fillRect` preto). A tocha do jogador cria um buraco nessa escuridão via `destination-out` + `radialGradient`. O resultado é uma área de visibilidade dinâmica que tremula, emite faíscas e projeta luz quente laranja. Tudo o que está fora do raio da tocha é invisível — incluindo inimigos, plataformas e chaves.

---

## Equipe

| Membro | Função |
|---|---|
| **Bruno Supriano** | Programador & Game Designer (Solo Developer) |

**Stack técnica:** HTML5 · JavaScript ES6 · Canvas 2D API · Zero frameworks · Zero assets externos

---

## Custos

| Item | Quantidade | Valor Unitário | Total |
|---|---|---|---|
| Horas de desenvolvimento | 120h | R$ 80,00/h | R$ 9.600,00 |
| Assets externos | — | R$ 0,00 | R$ 0,00 |
| Ferramentas e licenças | — | R$ 0,00 | R$ 0,00 |
| **TOTAL** | | | **R$ 9.600,00** |

> Todos os gráficos são gerados proceduralmente via Canvas 2D primitives. Zero custo de licenciamento de arte.

---

## Público-Alvo

**Faixa etária:** 16–30 anos

**Perfil:**
- Fãs de jogos indie atmosféricos (Hollow Knight, Limbo, Inside)
- Apreciadores de horror psicológico leve (sem gore, horror de atmosfera)
- Jogadores casuais/intermediários que buscam experiências de 10–15 min no browser
- Estudantes de game design interessados em técnicas de Canvas 2D

**Plataforma de acesso:** Browser PC (Chrome, Firefox, Edge) — sem instalação, basta abrir `index.html`

**Motivação de jogo:** Exploração, atmosfera, desafio de plataforma moderado

---

## Progresso de Desenvolvimento

| Área | Progresso |
|---|---|
| Visual (Canvas 2D art) | ████████░░ 85% |
| Mecânicas (física, colisão, câmera) | █████████░ 90% |
| Scripts / Lógica de Jogo | ████████░░ 85% |
| Level Design | ███████░░░ 70% |
| Efeitos Sonoros e Música | ████████░░ 80% |
| Roteiro / Narrativa | ████░░░░░░ 40% |

**Média geral:** ~78% completo

---

## Cronograma / Schedule

| Marco | Entregável | Período Estimado | Status |
|---|---|---|---|
| **Milestone 1** | High Concept + Versão Alfa | Março 2026 | Concluído |
| **Milestone 2** | Versão Beta (áudio, checkpoints, nível expandido) | Abril 2026 | Em andamento |
| **Milestone 3** | GDD Final + Polimento Visual | Maio 2026 | Planejado |
| **Milestone 4** | Pitch de Investimentos + Versão Gold (itch.io / Steam) | Junho 2026 | Planejado |

**Duração total estimada do projeto (Alpha → Gold):** 4 meses

---

## Diferenciais

**1. Iluminação Dinâmica 100% Procedural no Browser**
Sistema de luz via `destination-out` + `radialGradient` com flicker, faíscas e tonalidade quente — tudo sem shaders ou WebGL. Raro em jogos browser nativos.

**2. Zero Atrito para o Jogador**
Nenhuma instalação. Nenhum download. Apenas `index.html` no browser. Barreira de entrada praticamente zero.

**3. Zero Custo de Assets**
Toda a arte é gerada por Canvas 2D primitives (arcos, retângulos, bezier curves). Sem licenciamento, sem dependências externas.

**4. Estética Coerente com Referências Premium**
Paleta e atmosfera inspiradas em Hollow Knight e Limbo, executadas inteiramente com código — demonstrando que estética de qualidade não requer sprites ou engines.

**5. Código 100% Vanilla**
~750 linhas de JavaScript puro, em um único arquivo HTML. Demonstra domínio técnico profundo sem dependência de frameworks.
