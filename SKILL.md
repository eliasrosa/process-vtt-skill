---
name: process-vtt
description: Use when the user sends a VTT file path or says 'processa esse VTT', 'lê essa transcrição', '$process-vtt', '/process-vtt', 'transcrição da reunião', 'daily VTT', 'sync VTT'. Parses meeting transcripts, creates squad diary notes, and distributes tasks to Inbox.
inclusion: auto
---

# Processar VTT de reunião

Lê um arquivo VTT (transcrição de reunião do Teams), cria a nota da reunião em `Cogna/Squad/Diario/`, e distribui as pendências do Elias para `Tarefas/Inbox.md`.

- Vault: `/home/elias/dev/obsidian-vault`
- Convenção do diário: `Cogna/Squad/Diario/_README.md`
- Convenção de tarefas: `Tarefas/_como-usar.md`
- Skill de tarefas: `vault-nova-tarefa` (reusar a lógica de formato e destino)

## Visão Geral

Uma transcrição de reunião vira:
1. **Nota do dia** em `Cogna/Squad/Diario/AAAA-MM-DD-<tipo>.md`
2. **Tarefas no Inbox** — ações do Elias com `#tarefa #cogna`, cobranças com `#aguardando`
3. **Atualizações em frentes** — se a reunião tocar uma frente existente

O agente faz tudo e commita local; o push é do cron `vault-push`.

## Quando Usar

- Elias manda o caminho de um `.vtt` (ex.: `~/Downloads/[Voomp] Squad GW - Daily.vtt`)
- Elias diz "processa esse VTT", "lê essa transcrição", "$process-vtt"
- Qualquer pedido para processar transcrição de reunião do Teams

## Referência Rápida

| Passo | O que fazer |
|-------|-------------|
| 1. Ler o VTT | `read` no caminho; expandir `~` para `/home/elias` |
| 2. Identificar tipo | daily, sync, planning, retro, 1:1 — pelo título ou conteúdo |
| 3. Identificar falantes | Extrair do formato `<v Speaker Name>` ou rótulos de legenda |
| 4. Gerar a nota | Template abaixo → `Cogna/Squad/Diario/AAAA-MM-DD-<tipo>.md` |
| 5. Separar blocos | Combinados Elias, Combinados outros, Decisões, Status, Ausências, Notas |
| 6. Tarefas no Inbox | Ações do Elias → `#tarefa #cogna #qN 📅`; cobranças → `#aguardando` |
| 7. Atualizar frentes | Se citar frente existente, adicionar linha de status |
| 8. Commit local | `git add` + `git commit -m "docs(vault): <tipo> AAAA-MM-DD"` |

## Template da Nota

```markdown
---
title: <Tipo> Squad GW — DD/MM/AAAA
created: AAAA-MM-DD
updated: AAAA-MM-DD
tags: [reuniao, <tipo>, squad-gateway, cogna]
tipo: nota
status: nota
origem: Elias (Cogna)
reuniao: <tipo>
data: AAAA-MM-DD
---

# <Tipo> Squad GW — DD/MM/AAAA

> DD/MM/AAAA · Teams · ~Xh de gravação · Falaram: A, B, C · Ausentes: D

Fonte: transcrição VTT da reunião. Convenção do diário: [[Cogna/Squad/Diario/_README|README]].

> As pendências desta reunião estão como tarefas em [[Tarefas/Inbox|Inbox]] (com `origem` apontando para esta nota). Aqui fica o registro do que foi combinado; **não** use `#tarefa` nesta nota para não duplicar.

## 🟦 Combinados do Elias
- ...

## ⏳ Combinados dos outros (a cobrar)
- **Fulano:** ...

## Decisões
- ...

## Status das tasks
| Task | Quem | Status | Notas |
|------|------|--------|-------|
| ... | ... | ... | ... |

## Ausências e cobertura
- ...

## Notas
### <Assunto 1>
- ...
```

## Regras de Separação

| Bloco | O que entra | Onde vira tarefa |
|-------|-------------|------------------|
| 🟦 **Combinados do Elias** | O que o Elias prometeu ou precisa fazer | Inbox: `#tarefa #cogna` + quadrante + `📅` se houver data |
| ⏳ **Combinados dos outros** | O que outra pessoa assumiu e Elias precisa cobrar | Inbox: `#tarefa #cogna #aguardando`, `📅` = data de cobrar |
| **Decisões** | O que foi decidido (ou "sem decisão final") | Só texto na nota |
| **Status das tasks** | Tabela por task: quem, status, nota | Só texto na nota |
| **Ausências e cobertura** | Férias, faltas, horários | Só texto na nota |
| **Notas** | Contexto, conversa lateral, análises | Só texto na nota |

## Formato das Tarefas no Inbox

```markdown
- [ ] <verbo + objeto> #tarefa #cogna #qN [📅 AAAA-MM-DD] · origem: [[2026-10-29-daily|daily 29/10]]
- [ ] Fulano: <o que ficou de fazer> #tarefa #cogna #aguardando #qN 📅 AAAA-MM-DD · origem: [[2026-10-29-daily|daily 29/10]]
```

- **Quadrante:** propor sempre (Q1 = urgente+importante, Q2 = importante sem urgência)
- **Prazo:** só se foi dito na reunião; cobrança sem data leva "(data proposta)"
- **Origem:** sempre linkar a nota da reunião
- **Frente:** se pertence a uma frente, adicionar `· frente: [[Cogna/Frentes/X/_index|X]]`
- **IDs Jira:** linkar como `[VGAT-XXX](https://cogna.atlassian.net/browse/VGAT-XXX)`

## Checklist Antes de Terminar

- [ ] Nota criada em `Cogna/Squad/Diario/AAAA-MM-DD-<tipo>.md`
- [ ] Todas as ações do Elias estão no Inbox com `#tarefa #cogna` e `origem`
- [ ] Todas as cobranças estão no Inbox com `#aguardando` e `📅` (data de cobrar)
- [ ] Tarefas já existentes no Inbox foram AJUSTADAS, não duplicadas
- [ ] Frentes mencionadas foram atualizadas (se aplicável)
- [ ] Commit local feito (`git status` antes e depois)
- [ ] Mostrar ao Elias: linhas gravadas, suposições (quadrante, data proposta)

## Atualização de Frentes

Se a reunião mencionar uma frente existente (`Cogna/Frentes/<nome>/`):

1. Buscar a frente: `grep -ril '<termo>' Cogna/Frentes/`
2. Adicionar linha de status ou pendência no `_index.md` ou `backlog.md`
3. Linkar a nota da reunião como origem

Frentes comuns: Migração de assinaturas, Recálculo de split, Kafka, Exibição de contratos.

## Erros Comuns

| Erro | Correção |
|------|----------|
| `#tarefa` dentro da nota da reunião | O painel conta em dobro — tarefas só no Inbox |
| Duplicar tarefa que já existe | Buscar por palavras-chave/IDs antes; ajustar a linha original |
| Cobrança sem `#aguardando` | Combinado de outro precisa de `#aguardando` para o painel saber |
| Cobrança sem `📅` | Data de cobrar é obrigatória; se não foi dita, propor |
| Criar nota com tipo errado | daily, sync, planning, retro, 1:1 — extrair do título do VTT ou conteúdo |
| Não linkar IDs do Jira | Todo ID `VGAT-XXX` vira link para `cogna.atlassian.net` |
| Não commitar | O vault precisa de commit local; o push é do cron |

## Perguntar Antes de Agir

- Contexto ambíguo (tarefa é pessoal ou da Cogna?)
- Dono de cobrança indefinido (quem ficou de fazer?)
- Item que parece frente nova (criar pasta em `Cogna/Frentes/`?)
- Criar issue no Jira (não criar automaticamente)
