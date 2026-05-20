<!-- página 1 -->

# **Dungeon of Shadows — Documentação**



# **Final do Projeto**


Esta documentação detalha os pilares técnicos, de design e o roadmap de desenvolvimento do
projeto **Dungeon of Shadows** , um jogo desenvolvido em JavaScript puro.


## **Visão Geral**


**Dungeon of Shadows** é um jogo de plataforma 2D com temática de terror e exploração. O
diferencial técnico reside na ausência total de assets externos; todos os gráficos, animações e
sistemas de iluminação são gerados via código em tempo real através da **Canvas 2D API** .


### **Objetivo do Jogo**


●​ **Exploração:** Navegar por um mapa de 4200px de extensão.
●​ **Coleta:** Localizar as 4 chaves douradas místicas.
●​ **Sobrevivência:** Evitar fantasmas e morcegos (o jogador possui 3 vidas).
●​ **Vitória:** Alcançar o portal de saída após reunir todas as chaves.


## **Arquitetura do Sistema**


O jogo utiliza um modelo de **Game Loop Imperativo** sincronizado via requestAnimationFrame
a 60 FPS.


### **Componentes Principais**


1.​ **Input System:** Gerenciamento de estado de teclas para evitar atrasos na resposta.
2.​ **Physics Engine (AABB):** Detecção de colisão Axis-Aligned Bounding Box com
resolução de eixos separados (X e Y) para garantir estabilidade física.
3.​ **Entity System:** Estrutura de classes para Player, Inimigos (Ghost/Bat) e Itens
(KeyItem/Spark).
4.​ **Camera System:** Interpolação linear ( **Lerp** ) que cria um movimento de câmera suave e
orgânico.

---

<!-- página 2 -->

## **Destaques Técnicos**



### **1. Iluminação Dinâmica**


Sistema baseado na técnica de perfuração de máscara:

●​ **Camada de Penumbra:** Um overlay preto cobre o canvas.
●​ **Composição:** globalCompositeOperation: 'destination-out' apaga a escuridão ao redor
do player.
●​ **Gradiente:** Uso de createRadialGradient() para criar uma dissipação de luz realista.
●​ **Flicker:** Ruído aleatório aplicado ao raio da luz para simular o comportamento de uma
chama real.


### **2. Sistema de Parallax (3 Camadas)**


Criação de profundidade visual através de velocidades relativas à câmera:

●​ **Camada 1 (Pilares):** 18% da velocidade da câmera.
●​ **Camada 2 (Estalactites):** 44% da velocidade da câmera.
●​ **Camada 3 (Caveiras):** 68% da velocidade da câmera.


### **3. Otimizações de Performance**


●​ **Culling:** Entidades fora da tela não são renderizadas.
●​ **Math Optim:** Uso de Math.floor para evitar o custo de anti-aliasing em coordenadas
decimais.
●​ **Object Pooling:** Sistema de partículas (Sparks) que recicla objetos para aliviar o
Garbage Collector.


## **Diagnóstico e Roadmap**



### **Diagnóstico do Estado Alpha**


●​ **Pontos Fortes:** Iluminação única, física estável e código modular.
●​ **Oportunidades:** Necessidade de maior feedback sensorial (Juice) e imersão sonora.

---

<!-- página 3 -->

### **Roadmap de Melhorias**


**Prioridade**
**Funcionalidade**
**Descrição**

**Alta**
**Game Feel (Juice)**
Partículas ao coletar itens e flashes visuais ao sofrer
dano.

**Alta**
**Screen Shake**
Tremor de câmera para enfatizar impactos.

**Média**
**Web Audio API**
Sons procedurais (passos, tocha, gemas) gerados via
código.

**Média**
**Checkpoints**
Implementação de "Fogueiras" para salvar o progresso
no mapa.

**Baixa**
**Mini-mapa**
Indicador de progresso na HUD para orientação do
jogador.


## **Fundamentos Aplicados**


●​ **Dopamina:** Recompensas imediatas e satisfação visual na exploração.
●​ **Estado de Fluxo (Flow):** Equilíbrio entre a dificuldade das plataformas e a visibilidade
limitada.
●​ **UX em Jogos:** Interface reativa, minimalista e que não obstrui a visão periférica.


## **Avaliação Final**


**Critério**
**Nota**

**Técnica**
⭐⭐⭐⭐⭐

**Performance**
⭐⭐⭐⭐⭐

**Game Feel**
⭐⭐ ☆☆☆

**UX/UI**
⭐⭐ ☆☆☆

**Imersão**
⭐⭐⭐ ☆☆

**Conclusão:** O projeto apresenta uma base técnica sólida e acima da média. A transição para a

---

<!-- página 4 -->
versão Gold depende da implementação de camadas de polimento e feedback auditivo.