# 🎮 Dungeon of Shadows — Avaliação de Especialista & 100 Pontos de Correção

> Documento de revisão técnica e de design. Foco prioritário: **som de fundo** e **mecânica de acender a chama** (ação deliberada do jogador). Avalia também jogabilidade, controles, câmera, level design, IA, boss, narrativa, UI/UX, acessibilidade e performance.

---

## 🔎 Diagnóstico-resumo

**Som de fundo:** a base melhorou (master bus + compressor + reverb), mas continua estruturalmente frágil: a "chama" é ruído branco filtrado (soa como vento, não fogo, pois falta crepitação), o `ghost.mp3` fica fora do grafo Web Audio (sem compressão/mute do master), tudo é mono (sem panorâmica), o drone é estático e os sons de UI são tonalmente alegres demais para o tema.

**Acender a chama:** hoje a fogueira acende automaticamente por proximidade (`FIRE_REFUEL_R`), sem intenção do jogador. Deve exigir uma **ação explícita** (tecla de interação) e, idealmente, **manter pressionado** por um curto tempo (canalização), criando risco e peso.

---

## 🔊 A. Som de fundo e áudio (1–22) — *prioridade máxima*

1. **Substituir a "chama" de ruído branco por fogo real com crepitação**: somar transientes aleatórios (pequenos *pops* de ruído curtos disparados em intervalos irregulares) ao leito filtrado — só ruído filtrado soa como vento/chiado.
2. **Rotear o `ghost.mp3` pelo grafo Web Audio** via `createMediaElementSource` → `masterGain`, para receber compressão, reverb e o mute do master (hoje ele toca por fora, desbalanceado).
3. **Adicionar panorâmica estéreo (`StereoPannerNode`)** aos sons de inimigos conforme a posição em relação ao jogador — dá espacialidade e reduz fadiga auditiva.
4. **Criar submixes (buses) por categoria**: Ambiente, SFX e UI, cada um com seu `GainNode`, para balancear volumes de forma independente e estável.
5. **Evoluir o drone ao longo do tempo**: variar lentamente o corte do filtro e a mistura das vozes (envelope longo / LFOs múltiplos) — hoje é um pad estático que cansa.
6. **Harmonizar os sons de UI com o tema**: os arpejos maiores (coleta de chave, vitória) destoam do ambiente menor/sombrio; usar escala menor ou intervalos mais neutros.
7. **Aplicar reverb também aos SFX** (passos, dano, coleta) com *send* leve — hoje só drone/chama recebem, então alguns sons soam "numa sala" e outros "secos".
8. **Adicionar um limiter final** (segundo `DynamicsCompressor` com ratio alto + makeup) como teto de segurança contra clipping em picos somados.
9. **Variar mais os passos**: além do pitch, randomizar levemente o filtro e o intervalo entre passos para não soar mecânico/robótico.
10. **Suavizar transições de estado**: alguns caminhos cortam a ambiência seca via `stopLoops`; usar crossfade entre fases em vez de corte.
11. **Música/motivo do boss**: introduzir um *motif* musical sutil e tenso na Fase 3 (pulso grave rítmico) para diferenciar o clímax do ambiente exploratório.
12. **Stinger de descoberta** ao revelar uma memória (coleta de chave) — um acorde curto e etéreo reforça o momento narrativo.
13. **Gate real no oscilador do morcego**: hoje ele roda sempre com ganho 0; conectar/desconectar ou usar `start/stop` sob demanda evita vazamento e custo ocioso.
14. **Curva de atenuação por distância mais natural** (inversa/quadrática) nos sons de proximidade, em vez de linear, para sensação de profundidade.
15. **Som dedicado de "acender fogueira"** (fwoom + crepitar inicial) distinto do `playRelight` da tocha — o jogador precisa de feedback sonoro próprio para a nova ação.
16. **Som de "fogueira apagando" no vento** (sopro + sibilo decrescente) — hoje o vento apaga sem assinatura sonora clara da fogueira morrendo.
17. **Loop de chama por fogueira acesa**, com volume por proximidade — atualmente só existe a chama da tocha do herói; fogueiras acesas são silenciosas.
18. **Ducking**: abaixar levemente a ambiência quando toca um SFX importante (dano, boss) para destacá-lo na mixagem.
19. **Respeitar `prefers-reduced-motion`/volume do sistema** e iniciar com volume conservador; muitos navegadores penalizam autoplay alto.
20. **Persistir a preferência de mudo/volume** em `localStorage` para não resetar a cada partida.
21. **Slider de volume na tela** (além do mudo por tecla) — controle fino é esperado num jogo.
22. **Tratar `AudioContext` suspenso/ível de sample rate variável**: alguns dispositivos usam 44.1k vs 48k; garantir que buffers e LFOs não dependam de sample rate fixo.

---

## 🔥 B. Mecânica de acender a chama (23–32) — *ação deliberada*

23. **Exigir tecla de interação para acender** (ex.: `E` ou `F`), removendo o acendimento automático por proximidade — acender deve ser uma escolha, não um efeito colateral de andar.
24. **Manter pressionado para acender (canalização)**: 0,4–0,8 s segurando a tecla junto à fogueira, com **barra de progresso**, dando peso ao ato.
25. **Vulnerabilidade durante a canalização**: o jogador fica parado e exposto enquanto acende — cria tensão e decisão de risco (acender agora ou fugir?).
26. **Prompt de interação claro** ("Segure E para acender") aparecendo só quando o jogador está no alcance e com a tocha viável.
27. **Feedback visual de progresso** na própria fogueira (faíscas crescentes, brasa intensificando) durante a canalização.
28. **Custo de combustível ao acender**: gastar uma fração da tocha para acender a fogueira — torna o recurso uma moeda de decisão, não infinito.
29. **Acender também as tochas de parede** com a mesma ação, unificando o "verbo" e tornando-as interativas (hoje são cenário passivo).
30. **Cancelamento de canalização** ao se mover/levar dano, com feedback (a barra recua) — regra clara e justa.
31. **Diferenciar reacender a própria tocha de acender o cenário**: reacender a tocha numa fogueira já acesa pode seguir automático; acender algo apagado exige a ação.
32. **Tutorial dedicado à ação**, com a fogueira da Fase 1 num local seguro e sem inimigos, para o jogador praticar o "segurar para acender" sem pressão.

---

## 🕹️ C. Jogabilidade & game feel (33–46)

33. **Coyote time**: permitir pulo por ~5–7 frames após sair da plataforma — perdão de timing essencial em plataforma.
34. **Jump buffering**: registrar o pulo se pressionado pouco antes de tocar o chão, para saltos encadeados responsivos.
35. **Altura de pulo variável**: cortar a velocidade vertical ao soltar a tecla (pulo curto x longo) — hoje o pulo é fixo.
36. **Aceleração/desaceleração horizontal** em vez de velocidade instantânea (`vx` liga/desliga), para movimento com inércia e mais natural.
37. **Knockback do dano não deve roubar controle por muito tempo**: garantir janela curta e previsível para o jogador reagir.
38. **Feedback de aterrissagem** (pequena poeira/partícula + som) ao cair de altura — reforça o peso.
39. **Screenshake leve** em dano, queda e golpe no boss — *juice* que comunica impacto.
40. **Hitstop (micro-pausa)** ao ferir o boss na luz — vende o impacto do dano.
41. **Indicador de dano direcional** (vinheta vermelha no lado da fonte) ao levar hit.
42. **Invencibilidade pós-dano com feedback claro** (já pisca; adicionar som/UI de cooldown).
43. **Margem de colisão mais generosa** para inimigos (hitbox do jogador levemente menor que o sprite) — reduz mortes "injustas".
44. **Velocidade de queda com terminal mais baixo** e *gravidade ascendente/descendente* assimétrica para pulos mais satisfatórios.
45. **Plataformas atravessáveis por baixo (one-way)** onde fizer sentido — hoje toda colisão é sólida nos quatro lados.
46. **Telegrafar perigos**: abismos e quedas mortais devem ter sinalização visual (borda, partículas) antes do salto cego.

---

## ⌨️ D. Controles & input (47–54)

47. **Suporte a gamepad** (`Gamepad API`) — esperado em jogo de plataforma/ação.
48. **Remapeamento de teclas** e exibição das teclas atuais num menu.
49. **Pausa (`Esc`/`P`)** com menu — hoje não há como pausar.
50. **Não usar `keydown` repetido para ações de toque**: garantir *edge-trigger* (pressionar uma vez) para interação/pulo, evitando *auto-repeat* do SO.
51. **Suporte a toque/mobile** (botões na tela) ou ao menos detectar e avisar que requer teclado.
52. **Input durante transições/fade**: bloquear ou enfileirar corretamente para não perder/duplicar comandos.
53. **Tecla de interação configurável** (ligada ao item B23) e consistente em todos os contextos.
54. **Feedback visual de foco/heads-up das teclas** na primeira fase (overlay discreto que some).

---

## 🎥 E. Câmera, luz & renderização (55–64)

55. **Câmera com *look-ahead*** na direção do movimento — antecipa o caminho e melhora leitura do nível.
56. **Zona morta (deadzone)** na câmera para não tremer com micro-movimentos do jogador.
57. **Suavizar o `flicker` da luz**: hoje é aleatório por frame (`flickT % 4`); usar ruído coerente (Perlin/valor interpolado) evita tremor "elétrico".
58. **Iluminação por fogueira com cor/intensidade própria** e cintilação independente, não só o mesmo gradiente da tocha.
59. **Cache de gradientes radiais**: recriar `createRadialGradient` por luz a cada frame é custoso; reutilizar/poolar por raio.
60. **Resolução independente de densidade (DPR)**: o canvas é fixo em 1024×600 e escalado por CSS — em telas HiDPI fica borrado; renderizar em `devicePixelRatio`.
61. **Camada de luz em canvas de menor resolução** (ex.: metade) para ganho de performance no `destination-out`.
62. **Vinheta/escurecimento de bordas** para reforçar a claustrofobia sem custo alto.
63. **Partículas de poeira/atmosfera** flutuando na luz (motes) — profundidade e vida ao ambiente.
64. **Evitar `shadowBlur` em excesso** (custoso) — vários inimigos usam blur por frame; medir e limitar.

---

## 🧱 F. Level design (65–74)

65. **Introduzir uma mecânica por vez** na Fase 1 com espaço seguro para praticar antes de combinar (pulo → chave → inimigo → tocha → acender).
66. **Curva de dificuldade dentro de cada fase** (ritmo: tensão → alívio → tensão), não inimigos distribuídos uniformemente.
67. **"Momento de ensino" antes de "momento de teste"**: cada novo elemento aparece primeiro inofensivo, depois como desafio real.
68. **Checkpoints visíveis** (fogueiras-marco) em vez de checkpoint invisível por posição — também amarra com o tema da luz.
69. **Recompensar exploração**: rotas alternativas com corações/segredos para o jogador curioso.
70. **Variar o pacing visual** entre fases (paleta, densidade de cenário) — hoje as fases 1 e 2 são muito parecidas.
71. **Sinalizar o objetivo/saída** com pistas visuais (luz distante, marcas) para orientar sem precisar de texto.
72. **Evitar saltos de fé**: garantir que toda plataforma de destino seja visível antes do salto (com a luz limitada, isso é crítico).
73. **Trecho escuro da Fase 2**: equilibrar para que acender fogueiras seja necessário mas não tedioso (espaçamento e quantidade).
74. **Arena do boss**: desenhar coberturas/altos e baixos que tornem o "encurralar na luz" uma tática espacial legível.

---

## 👻 G. Inimigos, IA & boss (75–84)

75. **Telegrafia de ataque dos inimigos**: fantasmas/morcegos precisam de antecipação visual antes de causar dano.
76. **Variedade de comportamento**: hoje fantasma patrulha e morcego faz elipse fixa; adicionar perseguição/condições para quebrar a previsibilidade.
77. **Mini-sombra (Shade) com feedback de dano por luz** mais claro (hoje recua/definha; reforçar com partículas e som).
78. **Estados de IA explícitos** (patrulha/alerta/perseguição) com transições, em vez de movimento puramente senoidal.
79. **Boss com fases de vida**: mudar padrão ao perder X% de HP (mais investidas, apaga mais fogueiras) — clímax crescente.
80. **Telegrafar a investida (lunge) do boss** com antecipação visível (recuo + brilho dos olhos) para ser justo.
81. **Padrões de ataque do boss além de tocar**: projéteis de sombra, áreas de escuridão — hoje só dano por contato.
82. **Variar o vento do boss**: direção/intensidade telegrafadas, para o jogador planejar quais fogueiras proteger.
83. **Reação do boss à luz mais legível**: além do flash, um recuo audível/visual nítido que ensine a tática.
84. **Balancear HP do boss e dano da luz** para que o combate dure o suficiente para "contar uma história", sem arrastar.

---

## 📖 H. Narrativa & progressão (85–90)

85. **Sincronizar revelação com gameplay**: as memórias das chaves devem escalar com a tensão da fase, não só pela ordem de coleta.
86. **Dar identidade visual ao herói e à Sombra** que reforce o *twist* (são o mesmo ser) — paleta/silhueta espelhadas.
87. **Falas da Sombra durante o combate** (não só na intro/morte) reagindo ao que o jogador faz.
88. **Evitar competição de textos na tela**: tutorial, narrativa e banners não devem aparecer juntos; enfileirar por prioridade.
89. **Epílogo jogável curto** (caminhar para a luz) em vez de só tela de texto — encerramento mais forte.
90. **Coerência de tom**: garantir que UI alegre (sons/efeitos) não contradiga a narrativa sombria.

---

## 🖥️ I. UI / HUD / UX (91–96)

91. **Tela de opções** (volume, teclas, dificuldade) acessível do menu/pausa.
92. **HUD legível sobre fundo claro/escuro**: contornos/sombra garantidos para corações, tocha e textos em qualquer cenário.
93. **Indicador de objetivo persistente** (chaves restantes / "derrote a Sombra") sempre visível e claro.
94. **Feedback de coleta**: corações e chaves precisam de um *pop* visual + UI ao serem pegos.
95. **Tela de título/menu real** (Jogar, Opções, Créditos) em vez de só a intro de história.
96. **Telas de morte/vitória com mais opções** (Reiniciar / Menu / Continuar) e navegação clara.

---

## ♿ J. Acessibilidade, opções & performance (97–100)

97. **Opções de acessibilidade**: aumento de brilho da escuridão, redução de cintilação (para fotossensibilidade) e modo de alto contraste.
98. **Legendas/indicadores visuais para áudio** (ex.: ícone quando há fantasma próximo) — não depender só do som para perigo.
99. **`requestAnimationFrame` com delta-time**: a física hoje assume 60 fps fixos; usar `dt` para comportamento consistente em 120/144 Hz e em quedas de frame.
100. **Refatorar `index.html` (1 arquivo, ~2k linhas) em módulos** (engine, áudio, níveis, entidades) e adicionar um build simples — manutenção e testes ficam viáveis.

---

### Prioridade sugerida de execução
1. **Áudio estrutural** (1–8, 13, 15–17): crepitação da chama, rotear o fantasma, panorâmica, buses e limiter — é o que mais "limpa" o som.
2. **Ação de acender** (23–28, 32): tecla + segurar + barra + tutorial — atende ao pedido central.
3. **Game feel** (33–40): coyote time, jump buffering, pulo variável, hitstop/screenshake — maior salto de qualidade percebida.
4. O restante por área, conforme o tempo do projeto.
