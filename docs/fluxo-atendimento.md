# Fluxo de Atendimento - Sistema Multi-Agentes

## 🎯 Visão Geral do Atendimento

O sistema implementa um fluxo inteligente que processa automaticamente as mensagens dos clientes e os direciona para especialistas adequados, mantendo uma experiência natural e consultiva. Funciona em qualquer canal (WhatsApp, Chat, Email, etc).

## 📱 Jornada do Lead

### 1. Primeiro Contato
```
Lead envia mensagem → Seu Canal → Webhook → Sistema de Processamento
```

**Processamento Inicial:**
- Recepção via webhook
- Classificação do tipo de mensagem
- Processamento de áudio/imagem se necessário
- Buffer de 10 segundos para mensagens fragmentadas

### 2. Roteamento Inteligente
```
Mensagem processada → Agente Supervisor → Análise → Decisão de roteamento
```

**Critérios de Decisão:**
- **Nova conversa**: Sempre → Agente Geral
- **Continuação**: Manter agente atual (se apropriado)
- **Mudança de assunto**: Novo roteamento
- **Interesse específico**: Agente especializado

### 3. Atendimento Especializado
```
Agente escolhido → Análise + Contexto → Resposta personalizada → Cliente
```

## 🎭 Fluxos por Tipo de Agente

### 🔄 Agente Supervisor - Roteador Inteligente
**Processo de Decisão:**

1. **Think Tool Obrigatório**
   - Análise da mensagem atual
   - Revisão do histórico
   - Identificação do contexto e intenção

2. **Classificação Inteligente**
   - Primeira mensagem → Agente Geral
   - Contexto identificável → Agente Especializado apropriado
   - Assuntos novos/indefinidos → Agente Geral
   - **Customizável**: Defina seus próprios critérios via prompts

3. **Execução do Roteamento**
   - Chamada do agente apropriado com contexto
   - Transferência de histórico completo
   - Monitoramento de resposta e quality assurance

### 👋 Agente Geral - Triagem e Qualificação Inicial

**Fluxo Típico:**

1. **Saudação Inicial**
   - Mensagem personalizada (customizável via prompt)
   - Coleta de informações básicas (nome, contexto)

2. **Qualificação Inicial**
   - Tool `create_lead` registra novo lead
   - Coleta de informações preliminares
   - Classificação da categoria/necessidade

3. **Identificação de Especialidade**
   - Analisa contexto da conversa
   - Identifica se há especialista apropriado
   - Prepara contexto para roteamento

4. **Roteamento ou Continuação**
   - **Contexto claro**: Transição suave para Agente Especializado
   - **Contexto vago**: Continua qualificando com perguntas
   - **Fora do escopo**: Direcionamento para seu time (customizável)

5. **Finalização com Notas**
   - Tool `update_lead_notes` com resumo da conversa
   - Pronto para roteamento ou follow-up

### 🎯 Agentes Especializados - Padrão Genérico

**Fluxo Consultivo (customizável para cada especialidade):**

1. **Recepção Especializada**
   - Mensagem de boas-vindas apropriada para o domínio
   - Confirmação de que está falando com o especialista certo
   - Exemplo: "Ótimo! Vou te ajudar com informações sobre [domínio]"

2. **Qualificação Gradual** (1 pergunta por vez)
   - Perguntas estratégicas para seu domínio
   - Coleta de dados específicos (customizável)
   - Pacing natural, sem parecer interrogatório

3. **Apresentação de Conteúdo Relevante**
   - Consulta `semantic_search` em sua base de conhecimento
   - Extrai informações mais relevantes
   - Envia materiais via `send_media` se apropriado

4. **Monitoramento de Sinais**
   - 🟢 **Interesse ativo**: Continue qualificando
   - 🟡 **Satisfação aparente**: "Tem mais alguma dúvida?"
   - 🔴 **Desinteresse**: Pause e aguarde

5. **Decisão de Qualificação**
   - Avalia resposta do lead
   - Pontua de acordo com critérios seu domínio
   - Define próximo passo (mais qualificação ou direcionamento)

6. **Transição para Seu Time**
   - `classify_interest` com análise
   - `qualify_lead` se critério atendido
   - `notify_team` para que seu time acompanhe
   - `update_lead_notes` com insights completos

## 🎥 Sub-fluxos de Entrega de Conteúdo

### Envio de Materiais & Recursos
```
Cliente solicita: "Quero ver mais sobre [tópico]"
↓
Tool: send_media / semantic_search
↓
Parâmetros: query="busca do cliente", contexto="histórico da conversa"
↓
Sub-workflow busca e filtra recursos relevantes
↓
Retorna até 5 recursos mais relevantes
↓
Cliente recebe materiais (fotos, docs, links, etc)
```

### Exemplo Prático (Real Estate)
```
Cliente: "Quero ver fotos desse imóvel"
↓
Agente busca no knowledge base
↓
Encontra fotos, vídeo tour, especificações
↓
Envia os 5 materiais mais relevantes
↓
Cliente recebe tudo organizado
```

### Exemplo Prático (SaaS)
```
Cliente: "Como isso funciona com integração?"
↓
Agente busca documentação de integração
↓
Encontra docs, tutorials, exemplos de código
↓
Envia recursos relevantes ao nível técnico do cliente
↓
Cliente consegue entender rapidamente
```

## 🚨 Cenários de Exceção

### 1. Assuntos Fora do Escopo
```
Cliente: "Vocês oferecem [serviço fora do escopo]?"
↓
Agente Geral detecta que não é especialidade
↓
Script de direcionamento (customizável):
"Entendo, mas esse tipo de solicitação é melhor
atendida por [time/departamento/parceiro apropriado].
Posso conectar você com eles?"
```

### 2. Cliente Não Responde
- Após 2-3 mensagens sem resposta
- Sistema para de enviar mensagens
- Aguarda reativação pelo cliente

### 3. Mudança de Assunto
```
Cliente estava falando de loteamentos
→ Muda para construção
→ Agente Supervisor detecta
→ Redireciona para Agente Construtora
```

### 4. Informação Não Encontrada
```
RAG não retorna resultados relevantes
↓
"Não tenho essa informação específica.
Quer que eu conecte você com nosso especialista
para esclarecer isso?"
```

## 📊 Indicadores de Qualidade

### Métricas de Fluxo
- **Tempo até primeira resposta**: < 3 segundos
- **Taxa de roteamento correto**: > 95%
- **Conversão para especialista**: Meta definida por agente
- **Satisfação subjetiva**: Monitoramento de feedback

### Pontos de Controle
- Resposta inicial do Supervisor
- Primeira interação do agente especializado
- Momento de sugestão de especialista
- Finalização com anotações

## 🔄 Melhorias Contínuas

### Otimizações Implementadas
- Buffer de mensagens para evitar fragmentação
- Think Tool obrigatório para decisões críticas
- Sinais de interesse para timing adequado
- Fallback robusto para cenários não previstos

### Evolução do Sistema
- Análise de logs para padrões
- Ajuste de prompts baseado em performance
- Adição de novos cenários conforme necessário
- Refinamento de critérios de roteamento

---

Este fluxo garante uma experiência natural e eficiente, maximizando a conversão de leads enquanto mantém a qualidade do atendimento. É totalmente customizável para sua vertical e caso de uso específico.