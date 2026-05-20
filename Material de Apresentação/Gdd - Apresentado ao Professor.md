# Game Design Document (GDD)
# Dungeon of Shadows

---

## 1. Visao Geral

**Dungeon of Shadows** e um jogo de plataforma 2D com tematica de terror, focado em tensao, velocidade e precisao. O jogador explora uma masmorra escura utilizando uma tocha como unica fonte de luz, enquanto evita inimigos e coleta chaves para escapar.

O jogo prioriza reflexo rapido, movimentacao agil e tomada de decisao sob pressao.

---

## 2. Experiencia do Jogador

O jogo foi projetado para provocar:

- **Tensao constante** — visao limitada pela escuridao
- **Adrenalina** — movimento rapido e perigo constante
- **Foco e precisao** — plataformas e inimigos exigentes
- **Tentativa e erro** — aprendizado atraves da falha

> O jogador deve sentir que esta sempre em risco, sem momentos de conforto.

---

## 3. Core Gameplay Loop

```
Explorar -> Iluminar o caminho -> Evitar inimigos -> Coletar chave -> Avancar -> Repetir -> Escapar
```

---

## 4. Mecanicas de Gameplay

### Movimentacao
- Movimento lateral rapido
- Pulo padrao (sem variacoes)
- Controle responsivo e preciso

### Iluminacao
- O jogador carrega uma tocha
- A visao e limitada ao raio de luz
- O ambiente fora da luz permanece oculto

### Coleta
- O jogador deve coletar **4 chaves**
- As chaves estao espalhadas em areas de risco

### Objetivo
- Apos coletar todas as chaves, o jogador deve alcancar a saida

---

## 5. Sistema de Vida e Derrota

| Parametro        | Valor                        |
|------------------|------------------------------|
| Vidas iniciais   | 3                            |
| Dano por contato | 1 vida                       |
| Condicao de perda| 0 vidas restantes (Game Over)|

**Nao ha:**
- Checkpoints
- Regeneracao de vida
- Itens de cura

> O jogo e punitivo e incentiva execucao perfeita.

---

## 6. Inimigos

### Fantasma
| Atributo     | Descricao                                         |
|--------------|---------------------------------------------------|
| Movimento    | Patrulha com trajetoria senoidal                  |
| Comportamento| Previsivel, porem dificil de evitar em espacos fechados |
| Funcao       | Pressionar o jogador em areas medias              |

### Morcego
| Atributo     | Descricao                          |
|--------------|------------------------------------|
| Movimento    | Trajetoria eliptica                |
| Comportamento| Ocupa areas superiores             |
| Funcao       | Dificultar movimentacao vertical   |

---

## 7. Level Design

- **Fases:** 1 (atualmente)
- **Extensao:** ~4200px horizontal

### Estrutura
- Plataformas em diferentes alturas
- Areas de risco crescente
- Distribuicao estrategica de inimigos

### Progressao de Dificuldade

| Segmento | Descricao                                          |
|----------|----------------------------------------------------|
| Inicio   | Introducao ao controle e iluminacao                |
| Meio     | Aumento de inimigos e precisao exigida             |
| Final    | Alta densidade de risco e pressao constante        |

---

## 8. Fisica e Jogabilidade

O sistema de jogo nao possui:
- Sistema de stamina
- Upgrades
- Evolucao de habilidades

O gameplay e focado em:
- **Reflexo**
- **Timing**
- **Execucao**

---

## 9. Direcao Artistica

- **Estilo:** sombrio, minimalista e atmosferico

### Paleta de Cores

| Cor                | Uso              |
|--------------------|------------------|
| Preto profundo     | Escuridao        |
| Tons de cinza      | Ambiente         |
| Laranja quente     | Luz da tocha     |

### Iluminacao
- Elemento central da experiencia
- Contraste forte entre luz e escuridao
- Sensacao de isolamento

---

## 10. Audio

### Trilha Sonora
- Rapida
- Tensa
- Ritmo acelerado

### Objetivos
- Aumentar sensacao de urgencia
- Intensificar momentos de risco

> Efeitos sonoros podem ser adicionados futuramente.

---

## 11. Interface (HUD)

### Elementos Exibidos
- Vidas restantes (coracoes)
- Quantidade de chaves coletadas
- Indicacao basica de objetivo

### Feedback Visual
- **Dano:** efeito de invencibilidade com piscar
- **Coleta:** destaque visual nas chaves

---

## 12. Regras do Jogo

**O jogador vence se:**
1. Coletar todas as chaves
2. Alcancar a saida

**O jogador perde se:**
- Receber 3 danos

**Sem assistencia ao jogador:**
- Sem checkpoints
- Sem recuperacao
- Sem upgrades

---

## 13. Escopo Atual

### Implementado
- [x] Movimentacao
- [x] Inimigos
- [x] Sistema de vida
- [x] Iluminacao dinamica
- [x] Level completo

### Futuro (opcional)
- [ ] Novas fases
- [ ] Boss
- [ ] Sons e musica
- [ ] Sistema de save
- [ ] Novos tipos de inimigos

---

## 14. Pilares do Jogo

Os 3 pilares principais sao:

1. **Tensao pela escuridao**
2. **Velocidade e agilidade**
3. **Desafio punitivo e justo**

---

## Resumo Final

**Dungeon of Shadows** e um jogo de plataforma focado em habilidade pura, onde o jogador depende exclusivamente de sua execucao e reflexos para sobreviver em um ambiente hostil e limitado pela escuridao.
