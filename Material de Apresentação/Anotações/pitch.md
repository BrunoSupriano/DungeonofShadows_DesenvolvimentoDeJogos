# PITCH DE INVESTIMENTOS — DUNGEON OF SHADOWS
### Documento de Apresentação · Março 2026 · Bruno Supriano

---

> *"Imagine um jogo de horror onde sua única proteção contra monstros invisíveis é uma chama tremendo nas suas mãos — e você pode jogar agora, sem instalar nada, direto no browser."*

---

## 1. O Projeto

**Dungeon of Shadows** é um plataforma 2D de horror atmosférico desenvolvido inteiramente em HTML5 e JavaScript vanilla, sem engines, sem frameworks e sem um único asset externo. O jogador controla um aventureiro preso em uma masmorra amaldiçoada, armado apenas com uma tocha.

A mecânica central é a iluminação dinâmica: a tela é coberta por escuridão total, e a tocha do jogador cria um cone de luz vivo que treme, emite faíscas e projeta calor laranja — revelando o ambiente aos poucos. Fantasmas e morcegos patrulham as trevas. Quatro chaves douradas estão escondidas no nível. A saída só abre quando todas são coletadas.

O que torna Dungeon of Shadows especial não é apenas o que é visível na tela — é o que **não** é. A limitação visual imposta pela tocha transforma cada avanço em uma decisão de risco. O jogador nunca tem certeza do que está a um passo além da luz. Essa tensão permanente, construída sem uma única linha de diálogo ou cutscene, é o coração do projeto.

O jogo está **funcional e jogável hoje**. Basta abrir `index.html` no browser.

---

## 2. Mercado e Público-Alvo

### O Mercado de Jogos Indie Horror

O segmento de horror atmosférico e plataformas indie é um dos mais consistentes da indústria independente:

| Jogo Referência | Cópias Vendidas | Observação |
|---|---|---|
| **Hollow Knight** (Team Cherry, 2017) | 3 milhões+ | Desenvolvido por 3 pessoas; referência de qualidade indie |
| **Limbo** (Playdead, 2010) | 3,5 milhões+ | Estética minimalista sombria; prova que arte procedural vende |
| **Inside** (Playdead, 2016) | 3 milhões+ | Sequência espiritual de Limbo, mesma fórmula |
| **Don't Starve** (Klei, 2013) | Múltiplos milhões | Mecânica de luz/sanidade como core, mesmo conceito |
| **Celeste** (Maddy Makes Games, 2018) | 1 milhão+ | Solo developer inicial; plataforma precisa com narrativa emocional |

### Tendência de Mercado

O horror atmosférico está em crescimento consistente. Títulos como Hollow Knight e Limbo estabeleceram que jogadores pagam por **experiência emocional**, não por polimento de AAA. A geração de jogadores de 16–30 anos — criada em era de jogos indie — valoriza autenticidade criativa e conceitos ousados sobre gráficos de última geração.

### Público-Alvo

**Demográfico primário:** 16–30 anos, PC, familiarizados com jogos indie

**Perfil psicográfico:**
- Fãs de Hollow Knight, Limbo, Inside, Don't Starve
- Apreciam narrativa implícita e horror de atmosfera (não jump scare)
- Dispostos a jogar no browser sem fricção de instalação
- Engajados em comunidades como itch.io, Reddit r/IndieGaming, Steam Discovery

**Demográfico secundário:** Estudantes de ciência da computação e game design interessados na implementação técnica (Canvas 2D, engine do zero)

### Plataformas

**Fase 1 (atual):** Browser nativo — distribuição por link, itch.io, GitHub Pages

**Fase 2 (Gold):** itch.io com modelo pay-what-you-want

**Fase 3 (pós-lançamento):** Steam (avaliação após tração no itch.io), possível port mobile via PWA

---

## 3. Oportunidade de Negócio

### Diferenciais Competitivos

**1. Pipeline Zero-Asset (Zero Custo de Arte)**
Toda a arte de Dungeon of Shadows é gerada via Canvas 2D primitives — arcos, retângulos, gradientes, curvas bezier. Sem sprites. Sem pixel art. Sem licenciamento. Isso significa que cada funcionalidade nova não gera custo adicional de asset. A arte escala com o código. Nenhum concorrente direto no browser opera dessa forma com esse nível de qualidade visual.

**2. Browser-Native: Fricção Zero**
O maior obstáculo para jogar qualquer jogo indie é o processo de instalação. Dungeon of Shadows elimina esse obstáculo completamente. O jogador abre um link — o jogo roda. Isso é relevante especialmente para campanhas de marketing em redes sociais, onde a transição de "ver o vídeo" para "jogar agora" pode ser de um clique.

**3. Sistema de Iluminação Procedural Raro**
Iluminação dinâmica com `destination-out` + `radialGradient` em Canvas 2D é uma técnica pouco explorada. A maioria dos jogos browser usa canvas para jogos casuais simples. Dungeon of Shadows demonstra capacidade técnica incomum na plataforma — o que o diferencia não apenas para jogadores, mas para cobertura de mídia especializada (desenvolvedores, blogs de gamedev).

**4. Modelo de Monetização Flexível**
| Canal | Modelo | Estimativa de Receita |
|---|---|---|
| itch.io | Pay-what-you-want (sugestão R$15) | Variável — comunidade engajada |
| Steam | Premium (R$15–R$25) | Maior alcance, maior ticket |
| Mobile (PWA) | Freemium / Premium | Mercado adicional sem reescrita |
| Speedrun Community | Free-to-play com DLC | Engajamento de longo prazo |

---

## 4. Timeline de Desenvolvimento

| Fase | Duração | Entregáveis | Recursos Necessários |
|---|---|---|---|
| **Alpha** *(Concluída)* | 2 meses | Motor completo, iluminação dinâmica, 1 nível, todos os inimigos, sistema de coleta, HP, HUD | Solo developer (Bruno Supriano) — ~120h investidas |
| **Beta** *(Em andamento)* | 4–6 semanas | Áudio procedural (Web Audio API), checkpoints, mecânica de combustível da tocha, estados de inimigos, tela de título, polimento de level design | Solo developer + eventual colaboração em áudio |
| **Gold** *(Target: Junho 2026)* | 4–6 semanas | Versão completa e polida, sistema de score, narrativa visual, múltiplos níveis, publicação no itch.io, submissão ao Steam | Solo developer + orçamento de marketing |
| **Post-Launch** *(Q3 2026)* | Contínuo | Atualizações baseadas em feedback, port mobile, conteúdo adicional (novos níveis, modos de jogo), community management | Solo developer + suporte de comunidade |

**Tempo total estimado do estado atual até Gold:** 3 meses

---

## 5. Equipe

### Bruno Supriano — Desenvolvedor Solo

| Competência | Aplicação no Projeto |
|---|---|
| **Engenharia de Software** | Arquitetura do motor do jogo, otimização do game loop, gerenciamento de estados |
| **JavaScript / Canvas 2D** | Implementação de toda a renderização, física, iluminação e lógica de jogo |
| **Física de Jogos** | Sistema AABB collision, gravidade, camera lerp, hitboxes |
| **Programação de Gráficos** | Técnica `destination-out` para iluminação, radialGradient, parallax scrolling |
| **UX / Game Design** | Progressão de dificuldade, design de level, feedback visual ao jogador |
| **Rendering Procedural** | Todos os gráficos gerados em código — sem dependência de artistas externos |

**O que isso demonstra:** A capacidade de um único desenvolvedor de conceber, projetar, implementar e lançar um produto de jogo completo, do zero ao Gold, sem dependências externas de equipe ou assets. Essa eficiência operacional é um diferencial direto para investidores — menor risco de execução, menor custo operacional.

**Formação:** Curso de Engenharia de Software — UNISATC

---

## 6. Investimento Necessário

### Estado Atual

O projeto já está funcional como produto jogável. O investimento solicitado destina-se exclusivamente à elevação do produto do estado Alpha para um lançamento comercial polido (Gold).

### Breakdown do Investimento

| Item | Justificativa | Valor |
|---|---|---|
| **Produção de Áudio** | Composição de trilha ambiente + SFX procedural (Web Audio API). A ausência de áudio é a maior lacuna do produto atual. Investimento nessa área tem retorno imediato em qualidade percebida. | R$ 2.000,00 |
| **Marketing / Visibilidade no itch.io** | Impulsionamento de página, criação de trailer de gameplay (captura + edição), divulgação em comunidades relevantes (Reddit r/IndieGaming, Twitter gamedev, Discord de horror games) | R$ 1.500,00 |
| **Taxa de Publicação Steam** | Steamworks publishing fee (~$100 USD ≈ R$550). Abre o maior canal de distribuição de jogos PC do mundo. | R$ 550,00 |
| **Contingência (10%)** | Buffer para custos imprevistos de desenvolvimento e marketing | R$ 450,00 |
| **TOTAL SOLICITADO** | | **R$ 4.500,00** |

### Cenário de Retorno sobre Investimento (ROI)

| Cenário | Premissa | Receita Bruta | ROI |
|---|---|---|---|
| **Conservador** | 200 cópias no itch.io a R$15 médio | R$ 3.000,00 | 67% do investimento recuperado |
| **Base** | 500 cópias no itch.io a R$15 médio | R$ 7.500,00 | 167% — retorno total + lucro de R$3.000 |
| **Otimista** | 300 itch.io + 500 Steam a R$20 médio | R$ 16.000,00 | 356% — viabiliza desenvolvimento do próximo projeto |

> **Nota:** Esses números são conservadores em relação ao histórico de jogos de horror indie no itch.io. Títulos com mecânica diferenciada e boa página de store regularmente superam 1.000 cópias nos primeiros 3 meses pós-lançamento.

---

## 7. Por que Agora?

### O Timing é Estratégico

**O Horror Indie está em alta.** O sucesso recente de títulos como Signalis, Dredge, e Fear the Spotlight demonstra que o mercado de horror atmosférico está ativamente comprando jogos independentes. A janela de oportunidade para um novo título com mecânica diferenciada está aberta.

**A Resurgência dos Jogos Browser pós-COVID.** Durante e após a pandemia, jogos acessíveis via browser experimentaram um crescimento expressivo. Plataformas como itch.io relataram aumento de 300%+ no tráfego durante 2020–2021, e o hábito de jogar no browser se manteve. Dungeon of Shadows capitaliza diretamente nesse comportamento: zero instalação, máxima acessibilidade.

**A Vantagem da Ausência de Fricção.** Em um ecossistema onde a maioria dos lançamentos indie exige que o jogador "baixe, instale, crie conta, atualize drivers", um jogo que roda com um clique tem vantagem viral orgânica. Criadores de conteúdo podem linkar diretamente para o jogo em seus vídeos. Professores podem usar em sala de aula. A distribuição viral custa zero.

**O Produto Existe.** Não estamos pedindo investimento para construir algo hipotético. O motor está rodando. O jogo é jogável hoje. O investimento solicitado é para polimento e alcance — as fases de menor risco de execução em qualquer projeto de software.

---

### Resumo Executivo

| | |
|---|---|
| **Produto** | Jogo browser funcional, ~750 linhas de JavaScript, mecânica de iluminação única |
| **Mercado** | Horror indie — comprovado por Hollow Knight (3M+), Limbo (3.5M+) |
| **Diferencial** | Zero assets, zero instalação, iluminação dinâmica procedural no browser |
| **Investimento** | R$ 4.500,00 para levar Alpha → Gold + lançamento |
| **Retorno Base** | R$ 7.500,00 (500 cópias × R$15) — ROI positivo |
| **Prazo** | 3 meses até Gold a partir do estado atual |
| **Risco** | Baixo — produto funcional, desenvolvedor único, sem dependências externas |

---

*Dungeon of Shadows — "Sua única arma é a luz."*

**Contato:** Bruno Supriano · Desenvolvedor & Game Designer
*Curso de Engenharia de Software — UNISATC · Março 2026*
