# process-vtt

Processa transcrições VTT de reuniões do Teams, criando a nota da reunião no vault Obsidian e distribuindo as pendências para o Inbox.

## O que faz

1. Lê o arquivo `.vtt` (transcrição do Teams)
2. Identifica o tipo de reunião (daily, sync, planning, retro, 1:1)
3. Extrai falantes, combinados, decisões e status de tasks
4. Cria a nota em `Cogna/Squad/Diario/AAAA-MM-DD-<tipo>.md`
5. Distribui pendências do Elias para `Tarefas/Inbox.md`
6. Atualiza frentes mencionadas na reunião
7. Commita local no vault (push é do cron)

## Requisitos

| Item | Tipo | Como verificar |
|------|------|----------------|
| Vault Obsidian | Diretório | Existe `/home/elias/dev/obsidian-vault` |
| Convenção de diário | Arquivo | Existe `Cogna/Squad/Diario/_README.md` |
| Convenção de tarefas | Arquivo | Existe `Tarefas/_como-usar.md` |
| Git | Ferramenta | `git --version` |

Esta skill não tem scripts próprios — é um guia para o agente. Os requisitos são apenas a estrutura do vault.

## Uso

Enviar ao agente:

```
~/Downloads/[Voomp] Squad GW - Daily.vtt

processa esse VTT
```

Ou usar o trigger:

```
$process-vtt ~/Downloads/arquivo.vtt
```

## Saída

- Nota em `Cogna/Squad/Diario/AAAA-MM-DD-daily.md`
- Tarefas em `Tarefas/Inbox.md` com `#tarefa #cogna` e `origem` linkando a nota
- Cobranças com `#aguardando` e `📅` (data de cobrar)
- Commit local no vault

## Estrutura

```
process-vtt/
├── SKILL.md          # Instruções para o agente
├── README.md         # Este arquivo
└── .data/            # (gitignored) dados locais futuros
```

## Limitações

- Não extrai VTT do Teams automaticamente (use `auto/teams-meeting-transcript-cdp` para isso)
- Não cria issues no Jira automaticamente (apenas sugere)
- Identificação de action items é pelo agente, com revisão humana

## Dependências

- Skill `vault-nova-tarefa` (lógica de formato de tarefas)
- Skill `vault-nota` (se precisar criar nota em `Notas/`)

## Autor

Criada para o fluxo de trabalho do Elias na Cogna.
