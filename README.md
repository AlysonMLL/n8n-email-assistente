# 🤖 AI Email Triage Agent | Pipeline ETL Autônoma

Um Agente Autônomo de Inteligência Artificial de nível corporativo construído para atuar como um micro-pipeline ETL (Extração, Transformação e Carga). Ele lê, categoriza, resume e roteia e-mails em tempo real utilizando LLMs e processamento em lote.

<br>

Workflow 1 (`workflow_triagem.json`):

<img width="1508" height="602" alt="workflow1" src="https://github.com/user-attachments/assets/00691747-e772-4e31-be71-d7dc98484047" />

---

Workflow 2 (`workflow_resumo.json`):

<img width="1508" height="602" alt="workflow2" src="https://github.com/user-attachments/assets/9f77f741-3a31-4af6-b9c5-2d16f23732c3" />

---

Este projeto substitui a triagem manual de caixas de entrada por uma esteira de dados inteligente e orientada a eventos (*Event-Driven*). O sistema não atua como um simples chatbot, mas como um extrator de metadados rígido, convertendo texto não estruturado (HTML/Texto) em objetos JSON validados, arquivando Spams silenciosamente e alertando urgências instantaneamente.

<br>

# 🎯 Principais Funcionalidades

* **Extração em Tempo Real (Event-Driven):** Monitoramento silencioso da caixa de entrada via Gmail API (OAuth2), processando mensagens paralelamente assim que chegam.
* **Transformação via LLM (Zero-Shot Data Extraction):** Engenharia de prompt avançada forçando o Google Gemini a atuar como um *parser* de dados, devolvendo classificações, tags e índices de urgência (1 a 5) em formato JSON estrito.
* **Data Cleansing & Resiliência:** Expressões Regulares (Regex) avançadas em JavaScript para atuar como "triturador de HTML", removendo tags visuais de newsletters e entregando texto limpo para a IA, evitando estouro de tokens de contexto.
* **Roteamento Inteligente (Switching):** Tomada de decisão autônoma. E-mails nível 5 (Urgentes) disparam alertas em tempo real no celular. E-mails promocionais são arquivados automaticamente através de manipulação de IDs do Google.
* **Batch Processing (Resumo Diário):** Um segundo pipeline ETL configurado via *Cron Schedule* (18:00) que extrai os dados do dia no banco de dados, compila um relatório executivo em Markdown e envia para o usuário.

<br>

# 🏗️ Arquitetura & Tecnologias

O projeto adota princípios rigorosos de automação e isolamento de responsabilidades.

**Orquestração & IA:**
* **n8n (Node Automation):** Motor de orquestração de fluxos de trabalho atuando como o núcleo do sistema de microsserviços.
* **Google Gemini (Flash-Lite 3.1):** LLM leve e ultrarrápido utilizado via nós do LangChain para processamento de linguagem natural e estruturação de dados.
* **JavaScript (Code Nodes):** Scripts customizados para navegação profunda em objetos, tratamento de exceções (*Error Handling*) e sanitização de dados.

**Infraestrutura & Persistência:**
* **PostgreSQL (Supabase):** Banco de dados relacional em nuvem utilizado para persistência segura dos e-mails triados.
* **Oracle Cloud Infrastructure (OCI):** Deploy planejado em ambiente Linux ARM (Ampere A1) rodando 24/7 com contêineres Docker, garantindo alta disponibilidade (*Always Free Tier*).
* **Telegram Bot API:** Interface de mensageria para entrega de alertas em tempo real e relatórios em lote diretamente no smartphone.

<br>

# 🗄️ Estrutura do Banco de Dados

O sistema utiliza um modelo relacional simples hospedado no Supabase para gerenciar a fila de resumos diários:

* **Tabela `daily_emails`**: Armazena os metadados extraídos pela IA (`remetente`, `assunto`, `categoria`, `resumo`, `tags` e `urgencia`). 
* *Estratégia de Limpeza:* A tabela sofre um `TRUNCATE` automatizado diariamente após o envio do relatório noturno, operando como uma fila efêmera limpa e pronta para o próximo ciclo (Zero Storage Bloat).

<br>

# ⚙️ Como Executar o Projeto (Reprodução)

Siga os passos abaixo para importar este Agente Autônomo para a sua própria instância do n8n.

**1. Clone este repositório:**
```bash
git clone https://github.com/AlysonMLL/n8n-email-assistente.git
```

**2. Importe os Workflows:**

- Abra o seu painel do n8n.
- No canto superior direito, clique em Import from File (ou arraste e solte a tela).
- Importe os arquivos workflow_triagem.json e workflow_resumo.json presentes neste repositório.

**3. Configure as Credenciais:**

  O n8n solicitará que você crie as conexões. Você precisará de:

- Credenciais OAuth2 do Google Cloud (para ler o Gmail).
- Chave de API do Google Gemini.
- Token do Telegram Bot (obtido via BotFather).
- String de conexão do PostgreSQL (Supabase).

Ative os Fluxos: Mude a chave no canto superior direito de Inactive para Active. O Agente começará a operar silenciosamente em background.
