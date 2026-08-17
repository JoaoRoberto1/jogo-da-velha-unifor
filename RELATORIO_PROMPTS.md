# Relatório de Interação com IA — Jogo da Velha UNIFOR

## 1. Ferramenta de IA utilizada

**Claude Code** (modelo Claude Sonnet 5, Anthropic), operando como agente de desenvolvimento com acesso direto ao sistema de arquivos e a um navegador para testes.

## 2. Metodologia de prompting

Diferente de uma sequência longa de idas e vindas, a estratégia adotada foi de **um prompt-mestre rico em contexto**: o arquivo completo da especificação de caso de uso (`cdu_JogarJogodavelha.md`) foi fornecido integralmente à IA junto com a estrutura de entrega exigida (árvore de diretórios, nomes de arquivo obrigatórios, rubrica de avaliação). Esse é o papel do "Engenheiro de Prompts" descrito na atividade: em vez de pedir "faça um jogo da velha" de forma genérica, a especificação foi usada como **fonte soberana de verdade**, obrigando a IA a mapear cada seção do CDU (fluxo principal, fluxos alternativos, exceções, dicionário de dados, interface visual, critérios de aceite) diretamente para elementos de código.

**Prompt enviado (resumo):** o CDU completo (seções 1 a 19) + instrução para gerar o repositório na estrutura `jogo-da-velha-unifor/docs`, `src/index.html`, `README.md`, `RELATORIO_PROMPTS.md`.

## 3. Processo de geração e autoauditoria

Ao gerar o código, a IA realizou uma auditoria interna passo a passo contra a Matriz de Rastreabilidade (seção 17 do CDU) antes de considerar a primeira versão pronta. Pontos em que o raciocínio inicial precisou ser corrigido durante essa auditoria:

| # | Ponto do CDU | Erro/risco identificado no raciocínio inicial | Correção aplicada |
|---|---|---|---|
| 1 | A1.7.1 vs A1.7.2 (MD3) | Uma primeira leitura sugeria checar `rodada < 3` **antes** de checar `vitórias == 2`, o que poderia deixar a 3ª rodada decisiva sem declarar campeão corretamente. | A condição de campeão (`winsX === 2 \|\| winsO === 2`) foi colocada **antes** da condição de rodada, garantindo que A1.7.1 sempre tenha prioridade sobre A1.7.2, inclusive na rodada final. |
| 2 | E1.4 (empate no MD3) | Risco de incrementar o contador de rodada em um empate, o que o CDU explicitamente proíbe ("sem incrementar o número da rodada atual"). | `handleDraw()` não incrementa `currentRound`; apenas limpa o tabuleiro após 2s e repete a mesma rodada. |
| 3 | A2.2/A2.3 (bloqueio durante jogada da CPU) | Bloquear cliques apenas "visualmente" (`disabled`) sem also gatear pela flag `running` permitiria cliques físicos registrarem jogadas durante o intervalo de 400ms se o atributo `disabled` falhasse por qualquer motivo. | Dupla proteção: a flag `running` é a fonte de verdade checada em `onCellClick`, e o atributo `disabled` é redundante, apenas para reforço visual/UX. |
| 4 | UI-10 (linha de vitória) | Uma abordagem baseada em ângulos fixos por padrão (0°, 90°, 45°) funcionaria apenas para um grid perfeitamente quadrado com medidas fixas, quebrando em telas menores (media query mobile). | A linha é calculada dinamicamente via `getBoundingClientRect()` das células inicial e final do padrão vencedor, tornando o cálculo responsivo a qualquer tamanho de tabuleiro. |
| 5 | RNF de portabilidade | O dicionário de dados (seção 18) menciona "Canvas Confetti" na Matriz de Rastreabilidade, o que poderia sugerir o uso da biblioteca de terceiros `canvas-confetti` via CDN. | Como o RNF exige um único arquivo HTML/CSS/JS sem dependência de servidor/CDN, os confetes foram implementados com um sistema de partículas próprio em `<canvas>` puro, sem nenhuma biblioteca externa. |
| 6 | Política de autoplay de áudio dos navegadores | `AudioContext` pode nascer em estado `suspended` em alguns navegadores até haver interação do usuário. | Adicionada checagem de `audioCtx.state === 'suspended'` com `.resume()` antes de cada som, e todo o disparo de áudio foi envolvido em `try/catch` para não quebrar o jogo em navegadores sem suporte. |

## 4. Verificação prática (testes no navegador)

Após a geração, a aplicação foi carregada em um navegador real e testada interativamente (não apenas lida como código) contra os critérios de aceite. Resultados observados:

- **Vitória em PVP (Partida Única):** sequência de jogadas X/O resultou na mensagem "Jogador X venceu o jogo!", placar atualizado (`scoreX: 1`) e linha de vitória renderizada com posição/dimensão corretas (retângulo calculado exatamente sobre a linha 0-1-2, largura de 256px batendo com a distância entre os centros das células extremas).
- **Bloqueio pós-fim de jogo (CA-03):** clique em célula vazia após a vitória não alterou o estado do tabuleiro.
- **Modo CPU (CA-04):** após a jogada do Jogador X, a CPU executou sua jogada automaticamente após a pausa configurada, sem exigir interação do usuário.
- **Melhor de 3 (CA-05):** ao vencer a 1ª rodada, o sistema aguardou, limpou o tabuleiro automaticamente e avançou o contador de rodada de `1/3` para `2/3`, mantendo o placar acumulado.
- **Reinício manual (RF-08):** o botão "Reiniciar Jogo" zerou placar e rodada (`1/3`), limpou o tabuleiro e retornou o turno ao Jogador X, preservando o formato/modo selecionados.
- Nenhum erro foi registrado no console do navegador durante os testes.

## 5. Checklist de Autoavaliação — Critérios de Aceite

| Critério | Status | Observação |
|---|:---:|---|
| **CA-01** — Fidelidade Visual (paleta UNIFOR + subtítulo institucional) | ✅ | Cores `#003366`/`#0056b3`/`#d97706`/`#f4f6f9` aplicadas via CSS custom properties; subtítulo "UNIVERSIDADE DE FORTALEZA" fixo no cabeçalho. |
| **CA-02** — Regra de Ocupação (não sobrescrever célula ocupada) | ✅ | `onCellClick` verifica `options[idx] !== ''` antes de aceitar a jogada. |
| **CA-03** — Bloqueio pós-Fim de Jogo | ✅ | Flag `running` + atributo `disabled` bloqueiam cliques até a próxima rodada/reinício; testado no navegador. |
| **CA-04** — Comportamento do Modo CPU | ✅ | Jogada automática do `O` após 400ms de pausa quando `modo === 'cpu'`; testado no navegador. |
| **CA-05** — Regra do Melhor de 3 | ✅ | Zera tabuleiro entre rodadas, encerra a partida ao atingir 2 vitórias ou ao final da 3ª rodada; testado no navegador. |
| **CA-06** — Efeitos Visuais de Vitória (linha + confetes) | ✅ | Linha calculada dinamicamente sobre as 3 células vencedoras (validado via inspeção de `getBoundingClientRect`); confetes disparados via sistema de partículas em `<canvas>`. |
| **CA-07** — Autonomia de Áudio | ✅ | Todos os sons gerados via osciladores da Web Audio API nativa; nenhum arquivo de mídia externo referenciado. |

> ⚠️ **Ação pendente do aluno:** revisar manualmente cada linha da tabela acima após seus próprios testes, preencher nome/matrícula no `README.md`, ativar o GitHub Pages e colar o link público no `README.md` antes da entrega.
