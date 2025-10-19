# 🤖 n8n Telegram Bot - Sistema de Vistoria IA v2.0

Bot inteligente para Telegram que coleta informações de vistoria, consolida em material estruturado e prepara para análise por IA.

## ✨ Funcionalidades Principais

- 🚀 **Sessões de Vistoria** - Inicie com `/start-<ID>`, finalize com `/end`
- 📝 **Coleta Automática** - Texto, áudio, imagem, documento, localização
- 🧠 **Material para IA** - Consolidação automática em formato estruturado
- 📊 **Estatísticas** - Contadores automáticos por tipo de evidência
- 🔒 **Controle de Acesso** - Validação de usuários autorizados
- ⏱️ **Linha do Tempo** - Registro temporal completo de todas as interações

## 🎯 Como Funciona

### 1. Iniciar Sessão
```
/start-079-2025
```
✅ Sessão aberta, comece a enviar observações

### 2. Coletar Dados
Envie mensagens de texto, áudios, fotos, documentos...  
Cada item é salvo automaticamente em matriz ordenada

### 3. Finalizar
```
/end
```
✅ Material consolidado e salvo no banco de dados

## 📦 Arquivos do Projeto

| Arquivo | Descrição |
|---------|-----------|
| `Vistoria-Bot-IA-v2.json` | **Workflow principal** (IMPORTAR ESTE) |
| `SISTEMA-VISTORIA-IA-DOCUMENTACAO.md` | Documentação técnica completa |
| `GUIA-CONFIGURACAO-RAPIDA.md` | **5 passos para colocar em produção** |
| `jsonTel.json` | Workflow original (legado) |

## ⚡ Instalação Rápida (5 Passos)

### 1. Criar Tabelas no n8n DataTable

**Tabela: `Vistoria_Sessions`**
```
vistoria_id, chat_id, user_id, username, opened_at, closed_at, status, messages_json (todos String)
```

**Tabela: `Vistoria_Completa`**
```
vistoria_id, chat_id, user_id, username, opened_at, closed_at, messages_raw, 
material_markdown, material_clean, status (String)
stats_total, stats_texto, stats_audio, stats_imagem, stats_doc, stats_location (Number)
```

**Tabela: `Users`** (para controle de acesso)
```
id_telegram_user (String), name (String)
```

### 2. Copiar IDs das Tabelas

Abra cada tabela e copie o ID da URL (ex: `NUbtxOSO9EnbN9YS`)

### 3. Editar o Workflow

Abra `Vistoria-Bot-IA-v2.json` e substitua:
- `SESSIONS_TABLE_ID` → ID da tabela Vistoria_Sessions
- `VISTORIA_FINAL_TABLE_ID` → ID da tabela Vistoria_Completa

### 4. Importar no n8n

Import from File → `Vistoria-Bot-IA-v2.json`

### 5. Ativar

Configure credencial do Telegram → Ative o workflow

## 🧪 Teste Rápido

```
/start-teste123
Solo muito seco
[enviar foto]
Plantio recente
/end
```

Verifique a tabela `Vistoria_Completa` - você verá o material consolidado!

## 📖 Documentação Completa

🔗 [GUIA DE CONFIGURAÇÃO RÁPIDA](GUIA-CONFIGURACAO-RAPIDA.md)  
🔗 [DOCUMENTAÇÃO TÉCNICA COMPLETA](SISTEMA-VISTORIA-IA-DOCUMENTACAO.md)

## 🎓 Exemplo de Material Gerado

### Material Estruturado (Markdown):
```markdown
### Contexto Geral
Vistoria ID: 079-2025
Total de registros: 5

### Achados
1) Solo muito seco
2) Plantio recente observado

### Evidências
- #2 (imagem): Exposição de raiz
- #3 (áudio): Relato de campo

### Linha do Tempo
- #1 (10:30): Solo muito seco
- #2 (10:32): Exposição de raiz
...
```

### Material Limpo (para IA):
```
Vistoria 079-2025.

ACHADOS:
1. Solo muito seco
2. Plantio recente observado

EVIDÊNCIAS:
- IMAGE: Exposição de raiz
- AUDIO: Relato de campo
```

## 🔧 Requisitos

- n8n (v1.0+)
- Telegram Bot Token
- n8n DataTables habilitado

## 📊 Arquitetura

```
Telegram → Session Manager → Router → [start/collect/end]
                                           ↓
                                    Consolidate Material
                                           ↓
                                    Save to Database
                                           ↓
                                    Send Confirmation
```

## ✅ Comandos Disponíveis

| Comando | Função |
|---------|--------|
| `/start-<ID>` | Inicia sessão de vistoria |
| `[mensagem]` | Adiciona à sessão ativa |
| `/end` | Finaliza e consolida material |

## 🚀 Sistema Pronto para Produção!

Implementa **exatamente** as especificações do prompt original:
- ✅ Sessões com identificação única
- ✅ Coleta em matriz ordenada
- ✅ Consolidação em material único
- ✅ Payload estruturado para IA
- ✅ Persistência em banco de dados

---

**Autor:** João Camilo Ulhoa  
**Versão:** 2.0  
**Última atualização:** Outubro 2025
