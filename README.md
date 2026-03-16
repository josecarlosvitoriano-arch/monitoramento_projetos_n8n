# 📡 Daily Supabase and Streamlit Status Monitor

Workflow n8n que verifica diariamente o status dos serviços **Supabase** e **Streamlit** e envia uma notificação consolidada no **Discord**.

---

## 🔄 Como funciona

```
Trigger Diário (09h)
    ├── Verificar Supabase (HTTP GET)
    │       └── Tag Supabase
    │
    └── Verificar Streamlit (HTTP GET /_stcore/health)
            └── Tag Streamlit
                        │
                Aguardar os Dois (Merge)
                        │
                Montar Mensagem (Code)
                        │
                Enviar Notificação Discord
```

Todos os dias às 9h o workflow faz uma requisição para cada serviço, aguarda os dois responderem e envia **uma única mensagem** no Discord com o status de cada um.

---

## 📋 Pré-requisitos

- [n8n](https://n8n.io/) instalado e rodando
- Bot do Discord criado e adicionado ao servidor
- Projeto Supabase ativo
- App Streamlit publicado

---

## ⚙️ Configuração

### 1. Importe o workflow
No n8n, vá em **Workflows → Import from JSON** e cole o conteúdo do arquivo `Daily_Status_Monitor_PUBLIC.json`.

### 2. Configure as URLs

No nó **Verificar Supabase**, substitua a URL:
```
https://SEU_PROJETO_ID.supabase.co
```

No nó **Verificar Streamlit**, substitua a URL:
```
https://SEU_APP.streamlit.app/_stcore/health
```

### 3. Configure o Discord

#### Criar o Bot
1. Acesse [discord.com/developers/applications](https://discord.com/developers/applications)
2. Crie uma **New Application**
3. Vá em **Bot** → copie o **Token**
4. Vá em **OAuth2 → URL Generator**, marque `bot` + permissão `Send Messages`
5. Acesse a URL gerada e adicione o bot ao seu servidor

#### Pegar os IDs
Ative o **Modo Desenvolvedor** no Discord (Configurações → Aparência) e:
- **Server ID**: botão direito no nome do servidor → Copiar ID
- **Channel ID**: botão direito no canal → Copiar ID

#### Configurar a credencial no n8n
1. No nó **Enviar Notificação Discord**, clique em **Credenciais → Create new**
2. Cole o Token do bot
3. Substitua `SEU_DISCORD_SERVER_ID` e `SEU_DISCORD_CHANNEL_ID` pelos IDs copiados

### 4. Ative o workflow
Clique no toggle **Ativo** no canto superior direito do workflow.

---

## 📨 Exemplo de mensagem recebida

```
📡 Status dos Serviços - 16/03/2026, 09:00:00

Supabase: Online ✅

Streamlit: Online ✅
```

---

## 🗒️ Observações

- O workflow só executa enquanto o n8n estiver rodando
- O Streamlit é verificado pelo endpoint `/_stcore/health` para evitar erros de redirecionamento
- O Supabase é verificado pela URL base do projeto — um retorno `401` (sem API key) também é considerado **Online**, pois indica que o servidor está respondendo normalmente
