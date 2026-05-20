# 🧠 Dungeon of Shadows — Sugestões de Melhoria

> Documento de análise técnica e de design baseado em princípios de **dopamina**, **gamificação** e **UX design** aplicados a jogos. Cada sugestão nasce de uma lacuna identificada no código atual e é justificada pelo impacto esperado na experiência do jogador.

---

## Diagnóstico do estado atual

Após revisão do código, os pontos fortes identificados são: iluminação de tocha funcional via `destination-out`, física AABB estável, parallax de 3 camadas e estética de dungeon consistente. As lacunas estão principalmente em **feedback sensorial**, **progressão percebida**, **retenção** e **comunicação de estado ao jogador**.

---

## 1. Feedback Imediato (Dopamina de Curto Prazo)

> **Princípio:** O cérebro libera dopamina não apenas na recompensa, mas na *antecipação* e no *sinal* de que algo foi conquistado. Feedbacks instantâneos e satisfatórios criam um loop de reforço positivo.

### 1.1 Coletar chave não tem "juice"

**Problema atual:** quando o jogador coleta uma chave (`k.collected = true; player.keys++`), absolutamente nada acontece além do item sumir. Nenhum som, nenhuma partícula, nenhum flash.

**Sugestão:**
- Explosão de partículas douradas no ponto de coleta (burst radial de 20–30 partículas)
- Flash branco de 3–4 frames no canvas inteiro com `globalAlpha` baixo
- A chave no HUD deveria "preencher" animadamente (escala de 0 → 1.2 → 1.0 com easing)
- Texto flutuante `"+1 Chave"` que sobe e faz fade em 1.5s

**Por quê funciona:** O sinal auditivo/visual imediato é o gatilho dopaminérgico mais básico em jogos. É o equivalente ao "ding" de um caça-níqueis. Sem ele, a conquista parece vazia.

---

### 1.2 Pular não tem "game feel"

**Problema atual:** `this.vy = JUMP_POWER` — o pulo é instantâneo, sem squash/stretch ou partículas de poeira.

**Sugestão:**
- **Squash no pouso:** ao tocar o chão (`onGround = true`), escalar o jogador em `scaleX: 1.3, scaleY: 0.7` por 6 frames e interpolá-lo de volta
- **Stretch no pulo:** ao decolar, `scaleX: 0.8, scaleY: 1.3` por 4 frames
- **Coyote time:** permitir pular por ~8 frames após sair da borda de uma plataforma (sem o jogador perceber, mas sentindo o controle como mais "justo")
- **Jump buffer:** registrar o input de pulo até 10 frames antes de tocar o chão e executar assim que aterrissar
- Partículas de pó no pouso (4–6 partículas brancas/cinzas saindo horizontalmente)

**Por quê funciona:** Squash/stretch é o princípio #1 dos 12 princípios de animação da Disney. Faz objetos parecerem *vivos*. Coyote time e jump buffer são padrões da indústria (Celeste, Hollow Knight) que fazem o controle parecer responsivo sem alterar a física.

---

### 1.3 Tomar dano não dói o suficiente

**Problema atual:** `this.inv = 95` e o personagem pisca — funcional, mas sem impacto emocional.

**Sugestão:**
- **Screen shake:** ao tomar dano, deslocar a câmera aleatoriamente por 12–15 frames com amplitude decaindo (`shake *= 0.85` por frame)
- **Flash vermelho:** overlay `rgba(200, 0, 0, 0.35)` por 4 frames
- **Knockback:** aplicar `vx = -facing * 4; vy = -5` ao tomar dano, para o jogador ser empurrado
- **Coração pulsando no HUD:** o coração perdido deveria fazer um scale-up e fade antes de escurecer

**Por quê funciona:** Dor sem feedback visual/físico não registra. O screen shake é um sinal primário de "algo grave aconteceu" — o mesmo usado por Dead Cells, Hollow Knight e Celeste para reforçar o peso das ações.

---

## 2. Progressão e Objetivos (Loop de Médio Prazo)

> **Princípio de gamificação:** Jogadores precisam de **metas claras**, **progresso visível** e **recompensas escalonadas**. A sensação de "estou avançando" precisa ser contínua.

### 2.1 Falta um mini-mapa ou indicador de posição no mundo

**Problema atual:** o jogador não sabe onde está no mapa de 4.200px. Não há referência de progresso horizontal.

**Sugestão:**
- Barra de progresso fina (4px de altura) no topo da tela, mostrando a posição do jogador no `WORLD_W`
- Cor: gradiente de `#3a2050` (início) → `#ffd700` (próximo da saída)
- Marcadores de chave na barra (pequenos ícones que acendem quando coletados)

**Por quê funciona:** A barra de progresso é o elemento de gamificação mais estudado. Ela comunica distância percorrida e cria o efeito de "quase lá" que motiva o jogador a continuar (efeito de proximidade da meta / *goal gradient effect*).

---

### 2.2 As chaves são todas iguais — sem hierarquia de dificuldade

**Problema atual:** as 4 chaves têm o mesmo comportamento, aparência e "peso". Coletar a 1ª é tão trivial quanto a 4ª.

**Sugestão:**
- **Chaves numeradas com cores distintas:** Chave 1 = prata, Chave 2 = bronze, Chave 3 = ouro, Chave 4 = vermelha/de sangue (a mais difícil, guardada pelo maior perigo)
- Cada chave coletada deveria revelar uma **frase de lore** flutuante (`"A masmorra sussurra..."`, `"Você está mais fundo agora..."`) que faz fade em 3s
- A 4ª chave poderia estar protegida por um inimigo mais rápido ou em posição de risco maior

**Por quê funciona:** Variedade de recompensas cria antecipação diferenciada. O lore fragmentado (técnica de *environmental storytelling*) adiciona camada narrativa sem custo de implementação — e jogadores exploram mais para encontrá-lo.

---

### 2.3 Não há sistema de pontuação ou tempo

**Problema atual:** o jogo termina com "ESCAPOU!" mas sem nenhuma métrica de desempenho. Não há razão para rejogabilidade.

**Sugestão:**
- **Cronômetro visível** no HUD (mm:ss)
- **Ranking de fim de jogo:** exibir tempo final + número de mortes + chaves coletadas na tela de vitória
- **Classificação por medalhas:** Ouro (< 3min, 0 mortes), Prata (< 5min), Bronze (completou)
- Salvar o melhor tempo em `localStorage` e mostrar o recorde na tela de vitória

**Por quê funciona:** Métricas de performance ativam a competitividade interna (e social). O sistema de medalhas cria três metas simultâneas para diferentes perfis de jogador. O `localStorage` cria persistência de progresso, que é o fator de retenção mais básico.

---

## 3. Curva de Dificuldade e Fluxo (UX de Jogo)

> **Princípio de Flow (Csikszentmihalyi):** O estado ideal de engajamento acontece quando o desafio está levemente acima da habilidade do jogador. Fácil demais = tédio. Difícil demais = frustração. A curva precisa escalar gradualmente.

### 3.1 A dificuldade é plana — não existe progressão

**Problema atual:** o primeiro e o décimo inimigo têm exatamente o mesmo comportamento. Não há escalonamento.

**Sugestão — Zonas de Dificuldade:**

| Zona | Posição X | Mudança                                      |
|------|-----------|----------------------------------------------|
| 1    | 0–1.000   | Apenas fantasmas lentos, chão amplo          |
| 2    | 1.000–2.200 | Morcegos introduzidos, plataformas menores |
| 3    | 2.200–3.200 | Fantasmas mais rápidos, gaps maiores        |
| 4    | 3.200–4.200 | Inimigos múltiplos em tela, última chave protegida |

- Escalar `speed` dos fantasmas com base na zona: `0.8` → `1.0` → `1.3` → `1.6`
- Adicionar um inimigo que *persegue* o jogador quando ele entra no raio de luz (estado de alerta: patrulha → perseguição)

**Por quê funciona:** Sem curva de dificuldade, o jogador experiente enjoa rápido e o iniciante enfrenta picos injustos. A transição suave por zonas é o padrão de design de jogos desde Super Mario Bros.

---

### 3.2 Morte reseta o jogador no início — punição excessiva

**Problema atual:** `this.x = 50; this.y = 490` — qualquer morte joga o jogador de volta ao ponto inicial do mapa, independentemente de onde ele está.

**Sugestão:**
- **Checkpoints:** adicionar 2–3 pontos de salvamento automático no mapa (posição X ~1.800 e ~3.000)
- Visualizar o checkpoint como uma fogueira animada que acende ao ser ativada
- Ao morrer, o jogador respawna no último checkpoint ativado, mantendo as chaves coletadas
- Primeira tentativa: sem checkpoints (modo survival). A partir da 2ª morte: checkpoints ativados automaticamente (onboarding silencioso de dificuldade)

**Por quê funciona:** Punição excessiva é a maior causa de abandono em jogos de plataforma. A fogueira é um ícone já estabelecido pelo Dark Souls como símbolo de ponto seguro — o jogador reconhece intuitivamente sem precisar de texto.

---

### 3.3 Tela de início inexistente

**Problema atual:** `resetGame()` + `loop()` → o jogo começa imediatamente, sem contexto.

**Sugestão — Tela de título:**
- Fundo animado com parallax e partículas de chama flutuantes
- Título `DUNGEON OF SHADOWS` com efeito de texto cintilante (variação de opacidade por letra)
- Subtítulo de lore: *"A tocha é sua única aliada. A escuridão, seu único inimigo."*
- `[ PRESSIONE QUALQUER TECLA ]` pulsando lentamente
- Estado `'title'` no `state` machine

**Por quê funciona:** A tela de título é o primeiro contrato emocional com o jogador. Ela define o tom, cria expectativa e respeita o ritmo do jogador — em vez de lançá-lo na ação sem preparação. UX games chama isso de *emotional onboarding*.

---

## 4. Comunicação Visual e UX de Interface

> **Princípio de UX:** Toda informação que o jogador precisa para tomar decisões deve ser **visível, legível e hierarquizada**. A interface deve ser invisível — o jogador não deve pensar nela.

### 4.1 O HUD não escala com os eventos do jogo

**Problema atual:** o contador de chaves `"Chaves: 0 / 4"` é estático. Ele muda de valor mas não *reage*.

**Sugestão:**
- Ao coletar uma chave: o ícone no HUD faz `scale: 0 → 1.4 → 1.0` em 20 frames (bounce)
- Mostrar ícones de chave individualmente no HUD (4 slots), não apenas um número
- Cada slot vazio = cinza escuro; slot preenchido = dourado com glow
- Quando todas as chaves forem coletadas: todos os ícones pulsam juntos + texto `"SAÍDA DESBLOQUEADA"` aparece com fade-in de 2s

**Por quê funciona:** Substituir texto abstrato (`"3/4"`) por ícones concretos é um princípio fundamental de UX (*direct representation*). O estado atual comunica uma quantidade; a sugestão comunica *progresso visual imediato*.

---

### 4.2 A porta de saída é passiva — não chama atenção

**Problema atual:** a porta fica estática no fim do mapa. Quando desbloqueada, apenas um ponto branco aparece no lugar do cadeado.

**Sugestão:**
- Quando bloqueada: efeito de "runas vermelhas" pulsando na moldura
- Quando desbloqueada: a porta emite partículas douradas subindo continuamente + um feixe de luz vertical que penetra a escuridão (visível de longe no sistema de iluminação)
- Adicionar a porta como **ponto no mini-mapa de progresso** (sugestão 2.1) para que o jogador sempre saiba onde ela está

**Por quê funciona:** Em game design isso se chama *beacon design* — elementos que guiam o jogador visualmente sem texto ou setas. O feixe de luz é um dos mais eficazes porque funciona dentro da lógica do mundo (não quebra a imersão).

---

### 4.3 Tela de game over sem informação útil

**Problema atual:** `"VOCÊ MORREU"` + `"Pressione R"` — não há contexto do que causou a morte ou quanto progresso foi perdido.

**Sugestão:**
- Mostrar onde no mapa o jogador morreu (marcador na barra de progresso)
- Exibir o número de mortes acumuladas na sessão
- Mensagens de morte contextuais (ex: se morreu por queda → *"A escuridão abaixo não perdoa"*; se por inimigo → *"O fantasma encontrou sua chama"*)
- Botão `[TENTAR NOVAMENTE]` e, após 3ª morte, `[DICA]` que revela uma dica contextual sobre a seção atual

**Por quê funciona:** A tela de game over é um momento de *alta receptividade* — o jogador acabou de falhar e está psicologicamente aberto a aprender. Desperdiçar esse momento com apenas dois textos é uma oportunidade perdida de ensino e retenção.

---

## 5. Atmosfera e Imersão (Terror como Mecânica)

> **Princípio:** Em jogos de terror, o medo não vem do que é mostrado, mas do que é *sugerido*. A tocha já cria o mecanismo correto — falta explorar o potencial narrativo dela como *pressão psicológica*.

### 5.1 A tocha não tem estado de "perigo"

**Problema atual:** `TORCH_R = 240` — constante, imutável. A tocha nunca muda de estado.

**Sugestão — Tocha com Durabilidade:**
- A tocha tem um combustível (0–100%) que diminui lentamente ao longo do tempo
- Conforme o combustível diminui: `TORCH_R` encolhe de 240 → 80
- Abaixo de 20%: flicker agressivo + cor da chama muda para azulado/roxo (mais assustador)
- Abaixo de 5%: tela começa a escurecer ainda mais, trilha sonora (quando implementada) muda
- **Recargas de combustível** espalhadas pelo mapa (pequenos frascos de óleo brilhando) — criam novas metas de curto prazo
- Ao chegar perto de um inimigo: a tocha *reage*, oscilando mais violentamente

**Por quê funciona:** Cria uma segunda dimensão de pressão. O jogador não está apenas evitando inimigos — está gerenciando um recurso finito. Isso é o loop de sobrevivência que define jogos como Don't Starve e Amnesia. A tocha diminuindo **é o terror** — não os inimigos.

---

### 5.2 Inimigos não comunicam intenção

**Problema atual:** fantasmas e morcegos aparecem silenciosamente. Não há estado de "alerta" — eles simplesmente colidem.

**Sugestão:**
- **Estado de patrulha vs perseguição:** quando o jogador entra no `TORCH_R` de um fantasma, ele muda para estado de perseguição (velocidade +80%, olhos ficam vermelhos)
- Indicador visual de estado: uma exclamação `!` aparece sobre o inimigo quando detecta o jogador
- Quando um inimigo está fora da luz mas *próximo* da borda do raio da tocha: sua silhueta fica levemente visível (contorno roxo translúcido) como aviso

**Por quê funciona:** Dar ao jogador *informação antecipada* de perigo reduz a frustração de mortes "injustas" e aumenta o terror *consciente* (ver o inimigo chegando é mais assustador que ser surpreendido). É a diferença entre suspen e susto — o bom terror usa suspense.

---

### 5.3 Sem áudio — o maior buraco de imersão

**Problema atual:** silêncio total.

**Sugestão — Web Audio API (sem assets externos):**
Toda a trilha pode ser gerada proceduralmente via osciladores:

- **Drone de fundo:** oscilador senoidal em 60Hz com LFO lento (0.05Hz) → som de masmorra profunda
- **Passos:** burst de ruído branco filtrado a cada alternância de perna (`tick % 18 === 0` quando `vx !== 0`)
- **Chama crepitando:** ruído rosa filtrado com ganho oscilante sincronizado ao flicker
- **Coletar chave:** arpejo ascendente de 3 notas (C4 → E4 → G4) via `OscillatorNode`, duração 0.3s
- **Tomar dano:** ruído vermelho + queda de frequência (pitch bend de 220Hz → 80Hz em 0.4s)
- **Fantasma próximo:** tom etéreo em 432Hz que aumenta de volume com a proximidade

**Por quê funciona:** Estudos de UX em jogos mostram que o áudio é responsável por até **30% da percepção de qualidade**. Silêncio em um jogo de terror quebra completamente a suspensão de descrença. A Web Audio API permite tudo isso sem um único arquivo de som.

---

## 6. Rejogabilidade e Retenção (Loop de Longo Prazo)

> **Princípio:** Um jogo "completo" é aquele que o jogador tem motivo para abrir mais de uma vez. Sem variabilidade ou progressão entre sessões, a segunda rodada é idêntica à primeira.

### 6.1 Sem variabilidade — o mapa é sempre o mesmo

**Sugestão:**
- **Seed aleatória de nível:** ao iniciar, gerar uma seed que modifica levemente as posições de chaves e inimigos (dentro de ranges seguros para não tornar o jogo impossível)
- Mostrar a seed na tela de vitória para que o jogador possa compartilhar (`"Seed: 4821"`)
- Isso cria discussão social ("consegui completar a seed 4821 em 2min30!") sem implementar multiplayer

### 6.2 Conquistas ocultas

**Sugestão:**
- Implementar 5–6 conquistas armazenadas em `localStorage`:

| Conquista           | Condição                                      |
|---------------------|-----------------------------------------------|
| Sem Arranhões       | Completar sem tomar dano                      |
| Velocista           | Completar em menos de 2 minutos               |
| Colecionador        | Coletar todas as chaves sem morrer            |
| Sobrevivente        | Morrer 5 vezes e ainda completar              |
| Na Escuridão        | Completar com tocha abaixo de 10% (se impl.)  |
| Primeiro Passo      | Completar pela primeira vez                   |

- Mostrar ícone de cadeado desbloqueando na tela de vitória com animação

**Por quê funciona:** Conquistas ocultas criam exploração ativa — o jogador joga diferente para descobrir o que desbloqueia. É o mecanismo de retenção mais poderoso após a narrativa.

---

## 7. Resumo Priorizado por Impacto vs Esforço

| Prioridade | Sugestão                              | Impacto | Esforço | Razão                          |
|------------|---------------------------------------|---------|---------|--------------------------------|
| 🔴 Alta    | Feedback ao coletar chave (partículas + flash) | ★★★★★ | ★★☆☆☆ | Dopamina imediata, ~30 linhas |
| 🔴 Alta    | Screen shake ao tomar dano            | ★★★★★ | ★★☆☆☆ | Impacto enorme no game feel    |
| 🔴 Alta    | Checkpoints (fogueiras)               | ★★★★★ | ★★★☆☆ | Reduz abandono drasticamente   |
| 🔴 Alta    | Web Audio API básico                  | ★★★★★ | ★★★☆☆ | 30% da percepção de qualidade  |
| 🟡 Média   | Barra de progresso + mini-mapa        | ★★★★☆ | ★★☆☆☆ | Goal gradient effect           |
| 🟡 Média   | Coyote time + jump buffer             | ★★★★☆ | ★★☆☆☆ | Controle parece justo          |
| 🟡 Média   | Tocha com combustível                 | ★★★★☆ | ★★★☆☆ | Loop de sobrevivência          |
| 🟡 Média   | Tela de título com lore               | ★★★☆☆ | ★★☆☆☆ | Onboarding emocional           |
| 🟡 Média   | Inimigos com estado de perseguição    | ★★★★☆ | ★★★☆☆ | Terror consciente > surpresa   |
| 🟢 Baixa   | Cronômetro + medalhas                 | ★★★☆☆ | ★★☆☆☆ | Rejogabilidade                 |
| 🟢 Baixa   | Conquistas ocultas                    | ★★★☆☆ | ★★★☆☆ | Retenção de longo prazo        |
| 🟢 Baixa   | Seed aleatória                        | ★★★☆☆ | ★★★★☆ | Variabilidade                  |
| 🟢 Baixa   | Squash/stretch no pulo                | ★★★☆☆ | ★★★☆☆ | Polish visual                  |

---

## Referências de Design

- **Celeste** (2018) — coyote time, jump buffer, screen shake, checkpoints frequentes
- **Hollow Knight** (2017) — torch lighting, enemy state machines, environmental storytelling
- **Don't Starve** (2013) — sanidade como recurso, tocha com durabilidade, pressão psicológica
- **Dead Cells** (2018) — game feel, partículas de impacto, juice em cada ação
- **Mihaly Csikszentmihalyi** — *Flow: The Psychology of Optimal Experience* (1990)
- **Koster, R.** — *A Theory of Fun for Game Design* (2004)
- **Schell, J.** — *The Art of Game Design: A Book of Lenses* (2008)
