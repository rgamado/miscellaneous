---
name: kickoff-obrigacao
description: Primeira leitura de uma obrigação nova (leiaute, XSD, resoluções, IN) para produzir o inventário de campos, as defini,ões com citação,
as perguntas de entrevista e o esqueleto de regras.md. Nunca escreve interpretação.
argument-hint: <código da obrigacao, ex. acam212>
disable-model-invocation: true
---
# Kickoff de $ARGUMENTS

## Entrada
- `docs/bcb/$ARGUMENTS/layout/`: XSD ou PDF do leiaute, resoluções, instrução normativas.
- `docs/bcb/*/regras.md` e `openspec/specs/` para o que já existe.

Arquivos de layout disponíveis; se faltar XSD/PDF ou alguma resolução citada no pedido, pare e liste o que falta:
!`ls docs/bcb/$ARGUMENTS/layout/`

Regras já escritas para outras obrigações:
!`ls docs/bcb/*/regras.md`

## Passos
1. Campos: do XSD, liste elemento, tipo, obrigatário, enumeração, repetição. Sem XSD, do PDF com página.
2. Normativo: leia um documento por vez; para cada artigo relevante, anote o que exige, define ou exceciona,
   com documento, artigo e página. Grave o resultado antes de abrir o próximo.
3. Perguntas: para cada campo ou definição se há mais de uma leitura possível, ou se nenhuma fonte
   existente (staging, dim) fornece o dado diariamente, escreva uma pergunta. Um linha para cada, com a referência.
4. Reuso: liste regras de outras obrigações que parecem aplicáveis, como "a confirmar", nunca como aplicadas.
5. Escreva `docs/bcb/<codigo>/kickoff.md` (campos, definições, perguntas e reuso) e 
   `docs/bcb/<codigo>/regras.md` com uma linha por campo, status `pendente, Interpretação e Confirmado por vazios.

## Proibido
- Preencher Interpretações, COnfirmado por, Referência.
- Citar artigo, parágrafo ou página não lidas nesta execução. Sem leitura, a cálula fica vazia.
- Respnder perguntas de entrevista com base no que "costuma ser".
- Declarar periodicidade, prazo ou sequencialidade sem o artigo que os define.
