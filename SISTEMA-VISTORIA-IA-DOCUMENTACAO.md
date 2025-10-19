# 🤖 BOT DE VISTORIA IA - SISTEMA DE SESSÃO COMPLETO

## 📋 VISÃO GERAL

Sistema inteligente de coleta e consolidação de informações de vistoria via Telegram, com geração automática de material estruturado para análise por IA.

---

## 🎯 FLUXO OPERACIONAL

### 1️⃣ INICIAR SESSÃO

**Comando:** `/start-<ID>`  
**Exemplos:**
- `/start-079-2025`
- `/start_C2EQ09`
- `/start-vistoria-123`

**O que acontece:**
```
✅ Sessão aberta para a vistoria 079-2025.

📝 Envie suas observações.
🔚 Para finalizar, digite /end.
```

**No banco:**
```json
{
  "vistoria_id": "079-2025",
  "chat_id": "123456",
  "user_id": "789",
  "status": "ativa",
  "opened_at": "2025-10-19T10:30:00.000Z",
  "messages_json": "[]"
}
```

---

### 2️⃣ ENVIAR OBSERVAÇÕES

Durante a sessão ativa, **qualquer mensagem** é coletada automaticamente:

#### ✍️ Mensagens de Texto
```
Usuário: Solo muito seco na área C2EQ09
Bot: 📝 Anotado.
```

#### 🎤 Áudios
```
Usuário: [envia áudio]
Bot: 📝 Anotado.
```
*O áudio é marcado para transcrição futura*

#### 📸 Imagens
```
Usuário: [envia foto com legenda "Exposição de raiz"]
Bot: 📝 Anotado.
```

#### 📄 Documentos
```
Usuário: [envia PDF]
Bot: 📝 Anotado.
```

#### 📍 Localização
```
Usuário: [compartilha localização]
Bot: 📝 Anotado.
```

**Cada mensagem é salva IMEDIATAMENTE** no banco na tabela `Vistoria_Sessions`:
```json
{
  "messages_json": "[{\"idx\":1,\"ts\":\"...\",\"kind\":\"text\",\"content\":\"Solo muito seco...\"}]"
}
```

---

### 3️⃣ FINALIZAR SESSÃO

**Comando:** `/end`

**O que acontece:**

1. **Consolidação Automática** - Gera material estruturado:

```markdown
### Contexto Geral
Vistoria ID: 079-2025
Período: 19/10/2025 10:30 - 19/10/2025 11:45
Total de registros: 8

### Achados
1) Solo muito seco na área C2EQ09
2) Plantio recente; plântulas com clorose
3) Déficit hídrico observado

### Evidências
- #3 (imagem): Exposição de raiz
- #5 (áudio): [Áudio recebido - transcrição pendente]
- #7 (localização): -23.5505, -46.6333

### Ações Recomendadas
1) Revisar achados identificados
2) Validar evidências multimídia
3) Gerar relatório final consolidado

### Itens em Aberto / Dúvidas
A definir após análise detalhada.

### Linha do Tempo Sintética
- #1 (10:30:15): Solo muito seco na área C2EQ09
- #2 (10:32:08): Plantio recente identificado
- #3 (10:35:42): Exposição de raiz
...
```

2. **Material Limpo para IA:**
```
Vistoria 079-2025.

ACHADOS:
1. Solo muito seco na área C2EQ09
2. Plantio recente; plântulas com clorose
3. Déficit hídrico observado

EVIDÊNCIAS MULTIMÍDIA:
- IMAGE: Exposição de raiz
- AUDIO: [Áudio recebido - transcrição pendente]
- LOCATION: Localização: -23.5505, -46.6333
```

3. **Salvamento no Banco** - Tabela `Vistoria_Completa`:
```json
{
  "vistoria_id": "079-2025",
  "material_markdown": "### Contexto Geral...",
  "material_clean": "Vistoria 079-2025...",
  "messages_raw": "[{\"idx\":1,...}]",
  "stats_total": 8,
  "stats_texto": 5,
  "stats_audio": 1,
  "stats_imagem": 1,
  "stats_location": 1,
  "status": "concluída"
}
```

4. **Mensagem para Usuário:**
```
✅ Perfeito! Consolidei o material da vistoria 079-2025 e salvei no sistema.

📊 Você receberá o retorno assim que a análise for concluída.
```

---

## 🗄️ ESTRUTURA DE BANCO DE DADOS

### Tabela 1: `Vistoria_Sessions` (Sessões Ativas)

| Campo         | Tipo     | Descrição                           |
|---------------|----------|-------------------------------------|
| id            | Auto     | ID único da sessão                  |
| vistoria_id   | String   | ID fornecido pelo usuário           |
| chat_id       | String   | ID do chat do Telegram              |
| user_id       | String   | ID do usuário do Telegram           |
| username      | String   | Username do Telegram                |
| opened_at     | DateTime | Timestamp de abertura               |
| closed_at     | DateTime | Timestamp de fechamento (nullable)  |
| status        | String   | "ativa" ou "concluída"              |
| messages_json | Text     | JSON com array de mensagens         |

---

### Tabela 2: `Vistoria_Completa` (Material Final)

| Campo             | Tipo     | Descrição                              |
|-------------------|----------|----------------------------------------|
| id                | Auto     | ID único do registro                   |
| vistoria_id       | String   | ID da vistoria                         |
| chat_id           | String   | ID do chat                             |
| user_id           | String   | ID do usuário                          |
| username          | String   | Username                               |
| opened_at         | DateTime | Início da sessão                       |
| closed_at         | DateTime | Fim da sessão                          |
| messages_raw      | Text     | JSON com todas as mensagens            |
| material_markdown | Text     | Material estruturado em Markdown       |
| material_clean    | Text     | Material limpo para IA                 |
| stats_total       | Integer  | Total de mensagens                     |
| stats_texto       | Integer  | Quantidade de mensagens de texto       |
| stats_audio       | Integer  | Quantidade de áudios                   |
| stats_imagem      | Integer  | Quantidade de imagens                  |
| stats_doc         | Integer  | Quantidade de documentos               |
| stats_location    | Integer  | Quantidade de localizações             |
| status            | String   | "concluída"                            |

---

### Tabela 3: `Users` (Controle de Acesso)

| Campo              | Tipo   | Descrição                    |
|--------------------|--------|------------------------------|
| id                 | Auto   | ID único                     |
| id_telegram_user   | String | ID do Telegram do usuário    |
| name               | String | Nome do usuário              |

---

## 🔄 CENÁRIOS ESPECIAIS

### ✅ Cenário 1: Nova Sessão Durante Sessão Ativa

```
[Sessão ativa: vistoria-100]
Usuário: /start-200

Bot:
1. Fecha automaticamente vistoria-100 (consolida tudo)
2. Abre nova sessão vistoria-200
```

**Resultado:**
- Vistoria-100: Material salvo em `Vistoria_Completa`
- Vistoria-200: Nova sessão criada em `Vistoria_Sessions`

---

### ⚠️ Cenário 2: Enviar Mensagem SEM Sessão Ativa

```
Usuário: Olá, tudo bem?
Bot: ⚠️ Nenhuma sessão ativa. Use /start-<ID> para iniciar uma vistoria.
```

---

### ⚠️ Cenário 3: Fechar SEM Sessão Ativa

```
Usuário: /end
Bot: ⚠️ Não há sessão ativa. Use /start-<ID> para iniciar uma nova vistoria.
```

---

### ✅ Cenário 4: Fechar Sessão VAZIA

```
Usuário: /start-300
Bot: ✅ Sessão aberta...

Usuário: /end
Bot: ✅ Perfeito! Consolidei o material da vistoria 300...
```

**Resultado no banco:**
```json
{
  "stats_total": 0,
  "material_clean": "Vistoria 300.\n\nACHADOS:\nNenhum achado registrado.\n"
}
```

---

## 🛠️ CONFIGURAÇÃO

### Passo 1: Criar Tabelas no n8n DataTable

#### Tabela `Vistoria_Sessions`:
```
vistoria_id: String
chat_id: String
user_id: String
username: String
opened_at: String (ou DateTime)
closed_at: String (ou DateTime, nullable)
status: String
messages_json: String (Text)
```

#### Tabela `Vistoria_Completa`:
```
vistoria_id: String
chat_id: String
user_id: String
username: String
opened_at: String
closed_at: String
messages_raw: String (Text)
material_markdown: String (Text)
material_clean: String (Text)
stats_total: Number
stats_texto: Number
stats_audio: Number
stats_imagem: Number
stats_doc: Number
stats_location: Number
status: String
```

---

### Passo 2: Atualizar IDs das Tabelas no Workflow

No arquivo `Vistoria-Bot-IA-v2.json`, substitua:

```javascript
// Procure por:
"SESSIONS_TABLE_ID"
// Substitua pelo ID real da tabela Vistoria_Sessions

// Procure por:
"VISTORIA_FINAL_TABLE_ID"
// Substitua pelo ID real da tabela Vistoria_Completa
```

---

### Passo 3: Importar e Ativar

1. Importe `Vistoria-Bot-IA-v2.json` no n8n
2. Configure credenciais do Telegram
3. Ative o workflow

---

## 🎓 ARQUITETURA TÉCNICA

### Fluxo de Dados:

```
Telegram → Trigger → Get User → Get Active Session → Session Manager
                                                            ↓
                                                    Router Action
                                                            ↓
                        ┌───────────────┬───────────────┬──────────────┬─────────────┐
                        ↓               ↓               ↓              ↓             ↓
                    [deny]         [start]        [collect]        [end]     [no_session]
                        ↓               ↓               ↓              ↓             ↓
                Send Denied    Create Session   Update Session  Consolidate    Send Warning
                                      ↓               ↓          Material
                              Send Confirm    Send Confirm         ↓
                                                            Save Final Material
                                                                    ↓
                                                            Close Session
                                                                    ↓
                                                            Send Conclusion
```

---

## 📊 PAYLOAD DE SAÍDA (EXEMPLO COMPLETO)

```json
{
  "db_payload": {
    "vistoria_id": "079-2025",
    "chat_id": "123456789",
    "user_id": "987654321",
    "username": "joao_silva",
    "opened_at": "2025-10-19T10:30:00.000Z",
    "closed_at": "2025-10-19T11:45:00.000Z",
    "messages_raw": "[{\"idx\":1,\"ts\":\"2025-10-19T10:30:15.000Z\",\"kind\":\"text\",\"content\":\"Solo muito seco\",\"meta\":{}}]",
    "material_unico_markdown": "### Contexto Geral\n...",
    "material_unico_clean": "Vistoria 079-2025...",
    "stats_total": 8,
    "stats_texto": 5,
    "stats_audio": 1,
    "stats_imagem": 1,
    "stats_doc": 0,
    "stats_location": 1,
    "status": "concluída"
  },
  "user_message": "✅ Perfeito! Consolidei o material da vistoria 079-2025 e salvei no sistema.\n\n📊 Você receberá o retorno assim que a análise for concluída.",
  "diagnostics": {
    "vistoria_id": "079-2025",
    "contagem": {
      "total": 8,
      "texto": 5,
      "audio": 1,
      "imagem": 1,
      "doc": 0,
      "location": 1
    },
    "observacao": "Sessão encerrada com sucesso. 8 mensagens processadas."
  }
}
```

---

## 🚀 COMANDOS DISPONÍVEIS

| Comando            | Descrição                                  |
|--------------------|--------------------------------------------|
| `/start-<ID>`      | Inicia nova sessão de vistoria             |
| `[qualquer texto]` | Adiciona mensagem à sessão ativa           |
| `[áudio]`          | Adiciona áudio à sessão (marcado p/ IA)    |
| `[imagem]`         | Adiciona imagem com legenda                |
| `[documento]`      | Adiciona documento                         |
| `[localização]`    | Adiciona coordenadas GPS                   |
| `/end`             | Finaliza sessão e consolida material       |

---

## ✅ VANTAGENS DESTE SISTEMA

1. ✅ **Persistência Real** - Mensagens salvas no banco imediatamente
2. ✅ **Sessões Isoladas** - Uma sessão por chat (automaticamente gerenciado)
3. ✅ **Material Estruturado** - Pronto para análise por IA
4. ✅ **Suporte Multimídia** - Texto, áudio, imagem, documento, localização
5. ✅ **Estatísticas Automáticas** - Contadores por tipo de evidência
6. ✅ **Linha do Tempo** - Registro temporal completo
7. ✅ **Segurança** - Validação de permissão por usuário
8. ✅ **Flexibilidade** - Aceita IDs personalizados (079-2025, C2EQ09, etc.)

---

## 🎯 PRONTO PARA PRODUÇÃO!

Este sistema implementa **EXATAMENTE** as especificações solicitadas:
- ✅ `/start-<ID>` abre sessão
- ✅ Coleta TODAS as mensagens em matriz ordenada
- ✅ `/end` consolida em material único
- ✅ Gera payload estruturado para banco
- ✅ Retorna confirmação ao usuário

🚀 **Sistema de Vistoria IA v2.0 - Completo e Funcional!**
