# GREEN FIELD PROJECT CODE ASSISTANT COOKBOOK

## PHASE 0 - RESEACH

```
Você é um pesquisador técnico. NÃO escreve código.
Projeto: Criar uma solução para raspar o site https://www.tiendeo.com.br/ e baixar todas as páginas dos folhetos de ofertas da minha cidade (Bauru-SP) da categoria supermercados por estabelecimento, e usando solução de visão computacional open-source, ler a imagem das páginas do encarte e retornar SOMENTE um JSON válido, sem explicação, sem markdown, sem comentários.
O JSON deve ter esta estrutura exata:
{
  "items": [
    {
      "product": "nome do produto exatamente como aparece no encarte",
      "brand": "marca se estiver visível, senão vazio",
      "price": "R$ 22,90",
      "price_cents": 2290,
      "unit": "kg|un|pacote|lata|caixa",
      "notes": "de 29,90 por 22,90|2 por 10|leve 3 pague 2"
    }
  ]
}
Pesquise e produza um RESEARCH.md com:
1. Como sistemas semelhantes são arquitetados (3 referências reais, com links)
2. Stack recomendada com versões atuais e justificativa (busque na web, cite datas)
3. Top 5 riscos técnicos e como projetos maduros os mitigam
4. O que NÃO devemos construir (usar pronto): auth, pagamentos, files. etc.
5. Perguntas em aberto que só EU posso responder
Pada cada afirmação factual diga a fonte. Onde tiver incerteza, marque como incerto.
```

## PHASE 1 - PRD (/office-hours)

Leia docs/RESEARCH.md antes de perguntar. As perguntas 1-10 já foram respondidas no research - não repita. Desafie o que ainda não foi decidido. Vamos escreve um PRD de UMA página. Me entreviste: faça até 7 perguntas, uma de cada vez, para extrair o problema, usuários, 5 comportamentos do v1 e  - principalmente - o que fica fora da v1. Depois proponha o PRD no formato do template abaixo:
```markdown
# __Nome_do_Projeto__ - PRD

## Problema
Descrição do problema que eu quero resolver. Ex: Barbearias pequenas agendam pro WhatsApp; horários duplicados, no-show sem aviso, dono sem visão de agenda.

## Usuários
Quem e como usa o sistema, em bullets. Ex:
  - Dono da barbearia (admin da tentant)
  - Barbeiro (vê e gerencia a própria agenda)
  - Cliente final (agenda sem cria conta - link público)

## O que a versão FAZ (nível: frases, não campos)
Descrição breve do que o sistema faz. Ex:
1. Dono cadastra barbearia, serviços (nome/duração/preço) e barbeiros
2. Cliente agenda pelo link público, escholhendo serviços -> barbeiro -> horário
3. Sistema impede conflito de horário (invariante central  do produto)
4. Barbeiro vê sua agenda do dia/semana
5. Cliente recebe confirmação por e-mail

## Fora de escopo do v1 (DECIDO - não reabrir sem novo PRD)
**O QUE ESTÁ FORA DA VERSÃO**. Ex:
- Pagamento online          -> Talvez v2
- App mobile nativo         -> não planejado
- Notificação por WhatsApp  -> v2 (e-mail primeiro; canal é detalhe)
- Multi-idioma              -> não planejado

## Invariants (as 3 verdades que NUNCA podem quebrar)
1. Dois agendamento não podem ocupar o mesmo barbeiro no mesmo intervalo
2. Dados de um tenant jamais paracem para outro tenant
3. Agendamento só existe dentro do horário de funcionamento

## Métrica de sucesso do v1
Ex: Uma barbearia real usa por 2 semanas sem volta para o WhatsApp
```
Não detalhe telas, campos, endpoints: isso é proibido neste documento.

## PHASE 2 - Fatiamento

A partir do PRD, proponha milestones em fatias VERTICAIS.
Regas: 
