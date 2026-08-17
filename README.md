# Jogo da Velha UNIFOR

Aplicação web do Jogo da Velha desenvolvida para a atividade prática de **Desenvolvimento Guiado por Requisitos (Spec-Driven Development)** da disciplina de Engenharia de Software / Desenvolvimento Web — Universidade de Fortaleza (UNIFOR).

O projeto foi construído a partir da especificação de caso de uso [`docs/cdu_JogarJogodavelha.md`](docs/cdu_JogarJogodavelha.md), utilizada como guia soberano de requisitos.

**Autor:** _[SEU NOME COMPLETO AQUI]_
**Matrícula:** _[SUA MATRÍCULA AQUI]_

🔗 **Aplicação publicada (GitHub Pages):** https://joaoroberto1.github.io/jogo-da-velha-unifor/

## Como executar

### Localmente
Não é necessário servidor ou instalação de dependências. Basta abrir o arquivo diretamente no navegador:

1. Clone ou baixe este repositório.
2. Abra o arquivo `src/index.html` em qualquer navegador moderno (Chrome, Edge, Firefox).

### Via GitHub Pages
Acesse o link publicado acima — a aplicação roda inteiramente no navegador, sem back-end.

## Funcionalidades

- **Modo de Jogo:** 2 Jogadores (PVP) ou Contra o Computador (CPU joga como `O` após uma pausa de reflexão).
- **Formato da Partida:** Partida Única ou Melhor de 3 (MD3, com transição automática entre rodadas).
- **Placar** persistente por partida, com contador de rodada (`Atual/Total`).
- **Linha de vitória** desenhada dinamicamente sobre as 3 células vencedoras.
- **Confetes** disparados na vitória (implementados em `<canvas>`, sem bibliotecas externas).
- **Áudio sintetizado** via Web Audio API (sons de jogada, vitória e empate), sem arquivos `.mp3`/`.wav`.
- **Identidade visual UNIFOR** (Azul `#003366`, Azul Destaque `#0056b3`, Laranja `#d97706`, Fundo `#f4f6f9`).

## Estrutura do repositório

```
jogo-da-velha-unifor/
├── docs/
│   └── cdu_JogarJogodavelha.md   # Especificação de requisitos (CDU) fornecida
├── src/
│   └── index.html                # Aplicação completa (HTML + CSS + JS em arquivo único)
├── README.md
└── RELATORIO_PROMPTS.md          # Relatório de interação com IA e autoavaliação (CA-01 a CA-07)
```

## Tecnologias

- HTML5, CSS3 e JavaScript puro (vanilla), sem frameworks ou dependências externas.
- Web Audio API nativa do navegador para efeitos sonoros.
- Canvas 2D API nativa para a animação de confetes.
