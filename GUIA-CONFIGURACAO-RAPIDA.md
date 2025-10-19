# ⚡ GUIA DE CONFIGURAÇÃO RÁPIDA - BOT VISTORIA IA

## 🚀 5 PASSOS PARA COLOCAR EM PRODUÇÃO

---

### ✅ PASSO 1: Criar Tabelas no n8n

#### Tabela 1: `Vistoria_Sessions`

No n8n, vá em **DataTables** → **New Table** → Nome: `Vistoria_Sessions`

**Adicione os campos:**

| Campo         | Tipo     |
|---------------|----------|
| vistoria_id   | String   |
| chat_id       | String   |
| user_id       | String   |
| username      | String   |
| opened_at     | String   |
| closed_at     | String   |
| status        | String   |
| messages_json | String   |

---

#### Tabela 2: `Vistoria_Completa`

No n8n, vá em **DataTables** → **New Table** → Nome: `Vistoria_Completa`

**Adicione os campos:**

| Campo             | Tipo     |
|-------------------|----------|
| vistoria_id       | String   |
| chat_id           | String   |
| user_id           | String   |
| username          | String   |
| opened_at         | String   |
| closed_at         | String   |
| messages_raw      | String   |
| material_markdown | String   |
| material_clean    | String   |
| stats_total       | Number   |
| stats_texto       | Number   |
| stats_audio       | Number   |
| stats_imagem      | Number   |
| stats_doc         | Number   |
| stats_location    | Number   |
| status            | String   |

---

### ✅ PASSO 2: Copiar IDs das Tabelas

1. Abra a tabela `Vistoria_Sessions`
2. Copie o ID que aparece na URL (ex: `NUbtxOSO9EnbN9YS`)
3. Abra a tabela `Vistoria_Completa`
4. Copie o ID que aparece na URL

---

### ✅ PASSO 3: Atualizar o Workflow

Abra o arquivo `Vistoria-Bot-IA-v2.json` em um editor de texto.

**Procure e substitua:**

```json
"SESSIONS_TABLE_ID"
```
Por: `SEU_ID_DA_TABELA_SESSIONS` (que você copiou no passo 2)

```json
"VISTORIA_FINAL_TABLE_ID"
```
Por: `SEU_ID_DA_TABELA_COMPLETA` (que você copiou no passo 2)

**Exemplo:**
```json
// ANTES
"value": "SESSIONS_TABLE_ID"

// DEPOIS
"value": "NUbtxOSO9EnbN9YS"
```

**Salve o arquivo.**

---

### ✅ PASSO 4: Importar no n8n

1. Abra o n8n
2. Vá em **Workflows**
3. Clique em **Import from File**
4. Selecione: `Vistoria-Bot-IA-v2.json` (já editado no passo 3)
5. Clique em **Import**

---

### ✅ PASSO 5: Configurar Credenciais do Telegram

1. No workflow importado, clique em qualquer nó do Telegram
2. Clique em **Credential to connect with**
3. Selecione sua credencial do Telegram (ou crie uma nova)
4. **Save** o workflow
5. **Ative** o workflow (toggle no canto superior direito)

---

## 🧪 TESTE RÁPIDO

### Teste 1: Iniciar Sessão

No Telegram, envie:
```
/start-teste123
```

**Resposta esperada:**
```
✅ Sessão aberta para a vistoria teste123.

📝 Envie suas observações.
🔚 Para finalizar, digite /end.
```

---

### Teste 2: Enviar Mensagens

Envie várias mensagens:
```
Solo muito seco
[enviar uma foto com legenda "Exposição de raiz"]
[enviar um áudio]
Plantio recente observado
```

**Resposta esperada (a cada mensagem):**
```
📝 Anotado.
```

---

### Teste 3: Finalizar Sessão

Envie:
```
/end
```

**Resposta esperada:**
```
✅ Perfeito! Consolidei o material da vistoria teste123 e salvei no sistema.

📊 Você receberá o retorno assim que a análise for concluída.
```

---

### Teste 4: Verificar Banco de Dados

1. Abra a tabela `Vistoria_Completa` no n8n
2. Você deve ver um registro com:
   - `vistoria_id`: teste123
   - `material_markdown`: Material estruturado completo
   - `material_clean`: Texto limpo para IA
   - `stats_total`: 4 (ou quantas mensagens você enviou)

---

## 🔍 VERIFICAÇÃO DE DADOS

### Ver Sessão Ativa

Abra `Vistoria_Sessions`:
- Enquanto a sessão está aberta, `status = "ativa"`
- Após `/end`, `status = "concluída"`

### Ver Material Consolidado

Abra `Vistoria_Completa`:
- Campo `material_markdown`: Material estruturado em Markdown
- Campo `material_clean`: Texto limpo para enviar à IA
- Campo `messages_raw`: JSON com todas as mensagens

**Exemplo de `messages_raw`:**
```json
[
  {
    "idx": 1,
    "ts": "2025-10-19T10:30:15.000Z",
    "kind": "text",
    "content": "Solo muito seco",
    "meta": {}
  },
  {
    "idx": 2,
    "ts": "2025-10-19T10:32:08.000Z",
    "kind": "image",
    "content": "Exposição de raiz",
    "meta": {
      "file_id": "AgACAgIAAxkBAAI..."
    }
  }
]
```

---

## ⚙️ CONFIGURAÇÕES OPCIONAIS

### Adicionar Mais Usuários Autorizados

1. Abra a tabela `Users`
2. Adicione um novo registro:
   - `id_telegram_user`: ID do Telegram do usuário
   - `name`: Nome do usuário

**Como descobrir o ID do Telegram:**
- Envie `/start` para o bot `@userinfobot`
- Ele retornará seu ID

---

### Personalizar Mensagens

Edite o nó `Session Manager` (Code) no workflow:

**Linha ~80:** Mensagem de sessão iniciada
```javascript
message: `✅ Sessão aberta para a vistoria ${newVistoriaId}...`
```

**Linha ~105:** Mensagem sem sessão ativa
```javascript
message: '⚠️ Não há sessão ativa...'
```

**Linha ~150:** Confirmação de mensagem coletada
```javascript
confirmation: '📝 Anotado.'
```

---

## 🐛 TROUBLESHOOTING

### Problema: Bot não responde

**Solução:**
1. Verifique se o workflow está **ativado** (toggle verde)
2. Verifique se as credenciais do Telegram estão corretas
3. Veja os logs de execução no n8n

---

### Problema: Erro ao salvar no banco

**Solução:**
1. Verifique se você substituiu `SESSIONS_TABLE_ID` e `VISTORIA_FINAL_TABLE_ID`
2. Confirme que os IDs das tabelas estão corretos
3. Verifique se todos os campos das tabelas foram criados

---

### Problema: "Usuário não autorizado"

**Solução:**
1. Adicione seu ID do Telegram na tabela `Users`
2. Campo `id_telegram_user` deve ser uma **string** (ex: "123456789")

---

## 📊 EXEMPLO DE MATERIAL GERADO

### `material_markdown`:
```markdown
### Contexto Geral
Vistoria ID: teste123
Período: 19/10/2025 10:30 - 19/10/2025 11:45
Total de registros: 4

### Achados
1) Solo muito seco
2) Plantio recente observado

### Evidências
- #2 (imagem): Exposição de raiz
- #3 (áudio): [Áudio recebido - transcrição pendente]

### Ações Recomendadas
1) Revisar achados identificados
2) Validar evidências multimídia
3) Gerar relatório final consolidado

### Linha do Tempo Sintética
- #1 (10:30:15): Solo muito seco
- #2 (10:32:08): Exposição de raiz
- #3 (10:35:42): [Áudio recebido - transcrição pendente]
- #4 (10:40:20): Plantio recente observado
```

### `material_clean` (para IA):
```
Vistoria teste123.

ACHADOS:
1. Solo muito seco
2. Plantio recente observado

EVIDÊNCIAS MULTIMÍDIA:
- IMAGE: Exposição de raiz
- AUDIO: [Áudio recebido - transcrição pendente]
```

---

## ✅ CHECKLIST FINAL

Antes de colocar em produção, verifique:

- [ ] Tabela `Vistoria_Sessions` criada com todos os campos
- [ ] Tabela `Vistoria_Completa` criada com todos os campos
- [ ] Tabela `Users` com pelo menos um usuário autorizado
- [ ] IDs das tabelas substituídos no JSON
- [ ] Workflow importado no n8n
- [ ] Credenciais do Telegram configuradas
- [ ] Workflow ativado (toggle verde)
- [ ] Teste com `/start-teste123` funcionou
- [ ] Mensagens sendo coletadas corretamente
- [ ] Comando `/end` consolida e salva no banco
- [ ] Dados aparecem na tabela `Vistoria_Completa`

---

## 🎯 PRONTO!

Seu bot de vistoria IA está **100% operacional**! 🚀

**Comandos:**
- `/start-<ID>` → Inicia sessão
- `[mensagens]` → Coleta automática
- `/end` → Finaliza e consolida

**Material gerado:**
- ✅ Estruturado em Markdown
- ✅ Limpo para análise por IA
- ✅ Estatísticas completas
- ✅ Linha do tempo detalhada

---

📖 **Documentação completa:** `SISTEMA-VISTORIA-IA-DOCUMENTACAO.md`
