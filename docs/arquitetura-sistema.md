# Arquitetura do Sistema Multi-Agentes

## 🏛️ Visão Geral da Arquitetura

Este sistema utiliza uma arquitetura de múltiplos agentes especializados orquestrados por um agente supervisor central. Cada componente tem responsabilidades específicas e bem definidas. A solução é agnóstica a canal e vertical de negócio.

## 🔄 Fluxo de Dados

### 1. Entrada de Mensagens
```
Cliente (WhatsApp/Chat/Email/etc) → Webhook → n8n Workflow Principal
```

### 2. Processamento Inicial
```
Webhook → Classificação de Tipo → Buffer 10s → Agente Supervisor
```

### 3. Roteamento Inteligente
```
Agente Supervisor → [Think Tool] → Análise de Contexto → Agente Especializado
```

### 4. Processamento Especializado
```
Agente Especializado → [Tools + RAG] → Resposta Qualificada → Canal → Cliente
```

## 🧠 Componentes da Arquitetura

### Workflow Principal: Message Receiver
**Responsabilidades:**
- Receber webhooks do seu canal de comunicação
- Classificar tipo de mensagem (texto/áudio/imagem/documento)
- Processar áudio com transcrição (opcional)
- Aplicar OCR em imagens/documentos (opcional)
- Implementar buffer de 10 segundos (evita fragmentação)
- Acionar Agente Supervisor

**Tecnologias:**
- Webhook trigger
- Conditional logic
- Audio processing
- OCR integration
- Timer/delay functions

### Agente Supervisor
**Responsabilidades:**
- Analisar contexto completo da conversa
- Decidir qual agente especializado acionar
- Implementar fallback para Agente Geral
- Manter log de decisões

**Processo de Decisão:**
1. **Think Tool**: Reflexão obrigatória
2. **Análise de Contexto**: Histórico + mensagem atual
3. **Classificação de Intenção**: Loteamentos/Construção/Geral
4. **Roteamento**: Chamada do agente apropriado

### Agentes Especializados

O sistema suporta qualquer número de agentes especializados. Cada um tem seu próprio domínio e conjunto de ferramentas.

#### Agente Geral (Triagem)
- **Função**: Atendimento inicial e coleta de informações básicas
- **Especialidade**: Classificação e direcionamento
- **Tools**: cadastro_lead, anotacao_lead, think_tool
- **Quando acionado**: Primeira mensagem ou assuntos não categorizados

#### Agentes Especializados (Domínio-específico)
- **Função**: Consultoria avançada em sua área de especialidade
- **Especialidade**: Qualificação profunda, recomendações, coleta de dados específicos
- **Tools**: rag_knowledge_base, media_delivery, interest_classification, qualified_lead
- **Quando acionados**: Supervisor identifica contexto relevante

**Exemplo de customização para seu caso:**
- Construção: Agente especializado em projetos, técnicas, materiais
- Real Estate: Agente especializado em imóveis, localização, características
- SaaS: Agente especializado em features, pricing, casos de uso
- Etc: Customizable para qualquer vertical

## 🗄️ Camada de Dados

### PostgreSQL
- **Memória Compartilhada**: Histórico de conversas entre agentes
- **Leads Database**: Cadastro, anotações, qualificação, scores
- **Session Management**: Controle de sessões ativas
- **Audit Log**: Rastreamento de decisões e roteamentos

### Vector Store (Supabase, Pinecone, Weaviate, etc)
- **Knowledge Base**: Base de conhecimento customizada para seu domínio
- **Embeddings**: Busca semântica em seu conteúdo
- **Semantic Search**: RAG (Retrieval Augmented Generation) para respostas contextualizadas
- **Escalabilidade**: Suporta bases grandes de conhecimento

## 🔧 Ferramentas (Tools)

### Categoria: Lead Management
- **create_lead**: Registro inicial de novo lead
- **update_lead_notes**: Anotações para seu time
- **classify_interest**: Classificação de interesse e estágio
- **qualify_lead**: Marcação de lead como qualificado

### Categoria: Conhecimento & Context
- **semantic_search**: Busca em sua base de conhecimento via RAG
- **get_lead_context**: Recupera histórico completo da conversa
- **think_tool**: Ferramenta de reflexão interna para raciocínio

### Categoria: Delivery & Integration
- **send_media**: Sub-workflow para envio de materiais (imagens, docs, etc)
- **send_calendar**: Integração para agendamentos
- **notify_team**: Notifica seu time de vendas quando lead qualificado

## 🔀 Sub-workflows

### Fluxo de Envio de Material
```
Cliente solicita material/info → Query → Vector Search
→ Filter por categoria → Return Top 5 relevantes → Send via canal
```

### Fluxo de Qualificação
```
Conversa → Coleta de dados → Classification → Score
→ If score > threshold → Mark qualified → Notify team
```

## 🔐 Segurança e Controle

### Validação de Entrada
- Sanitização de mensagens
- Validação de tipos de arquivo
- Controle de tamanho de uploads

### Rate Limiting
- Buffer de 10 segundos para evitar spam
- Controle de sessões simultâneas
- Timeout de inatividade

### Fallback Strategy
1. **Primeiro nível**: Agente especializado (se aplicável)
2. **Segundo nível**: Agente Geral (triagem)
3. **Terceiro nível**: Direcionamento manual para seu time (número/email customizável)

## 📊 Monitoramento

### Métricas Coletadas
- Tempo de resposta por agente
- Taxa de acerto de roteamento
- Conversão de leads qualificados
- Volume de mensagens por período

### Logs Estruturados
- Decisões do Agente Supervisor
- Uso de ferramentas por agente
- Erros e exceções
- Performance de sub-workflows

## 🚀 Escalabilidade

### Horizontal
- Múltiplas instâncias de n8n
- Load balancing de webhooks
- Distribuição de workload

### Vertical
- Otimização de prompts
- Cache de respostas RAG
- Melhoria de embeddings

## 🔄 Manutenibilidade

### Modularização
- Agentes independentes
- Prompts centralizados
- Tools reutilizáveis

### Versionamento
- Controle de mudanças em prompts
- Rollback de configurações
- A/B testing de comportamentos

---

Esta arquitetura garante flexibilidade, escalabilidade e manutenibilidade, permitindo ajustes finos em cada componente sem impactar o sistema completo.