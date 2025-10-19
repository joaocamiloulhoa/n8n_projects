# n8n Projects

Este repositório contém workflows do n8n para automação de processos.

## Projetos

### Telegram Bot com Supabase

Workflow de bot do Telegram que verifica permissões de usuários através de uma tabela no Supabase.

**Arquivo:** `jsonTel.json`

**Funcionalidades:**
- Recebe mensagens do Telegram
- Verifica se o usuário está cadastrado na tabela de usuários autorizados
- Envia mensagem de permissão ou negação baseado no cadastro

**Como usar:**
1. Importe o arquivo `jsonTel.json` no n8n
2. Configure as credenciais do Telegram
3. Configure a conexão com a tabela de usuários no Supabase
4. Ative o workflow

## Estrutura do Projeto

```
n8n_projects/
├── jsonTel.json          # Workflow do Telegram Bot
├── test-workflow.md      # Documentação de teste
└── README.md             # Este arquivo
```

## Requisitos

- n8n instalado e configurado
- Conta no Telegram com Bot criado
- Tabela de usuários configurada no Supabase

## Autor

João Camilo Ulhoa

## Data

Outubro 2025
