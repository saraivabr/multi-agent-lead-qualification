# B2B Tools - Documentação Completa

## Overview

Tools são funções que os agentes podem chamar para executar ações específicas. Cada tool é implementada como um workflow/nó no n8n que recebe parâmetros e retorna resultados.

## Categorias de Tools

### 1. Lead Management
### 2. Discovery & Pain Analysis
### 3. Qualification & Scoring
### 4. Follow-up & Nurturing
### 5. Closing & Conversion
### 6. Intelligence & Analytics

---

## 1. LEAD MANAGEMENT TOOLS

### `create_lead`
**Descrição:** Cria novo registro de lead no CRM/database

**Usado por:** Agente Descoberta

**Parâmetros:**
```json
{
  "name": "string (required)",
  "company": "string (required)",
  "role": "string (optional)",
  "channel": "string (required)", // whatsapp, email, chat, etc
  "phone": "string (optional)",
  "email": "string (optional)",
  "source": "string (optional)", // campaign, organic, referral, etc
  "initial_message": "string (optional)"
}
```

**Retorno:**
```json
{
  "lead_id": "uuid",
  "created_at": "timestamp",
  "status": "created"
}
```

**Implementação n8n:**
- PostgreSQL Insert node
- Tabela: `leads`
- Campos: name, company, role, channel, created_at, updated_at, status

---

### `update_lead`
**Descrição:** Atualiza informações gerais do lead

**Usado por:** Todos os agentes

**Parâmetros:**
```json
{
  "lead_id": "uuid (required)",
  "data": {
    "field_name": "new_value",
    "another_field": "another_value"
  }
}
```

**Retorno:**
```json
{
  "lead_id": "uuid",
  "updated": true,
  "updated_fields": ["field_name", "another_field"]
}
```

**Implementação n8n:**
- PostgreSQL Update node
- Tabela: `leads`
- WHERE lead_id = {{ $json.lead_id }}

---

### `get_lead_context`
**Descrição:** Recupera todo o histórico e contexto do lead

**Usado por:** Todos os agentes (ao iniciar interação)

**Parâmetros:**
```json
{
  "lead_id": "uuid (required)",
  "include_history": "boolean (default: true)",
  "include_notes": "boolean (default: true)"
}
```

**Retorno:**
```json
{
  "lead_id": "uuid",
  "name": "string",
  "company": "string",
  "current_stage": "discovery|qualification|followup|closing",
  "current_agent": "string",
  "bant_score": "number",
  "classification": "HOT|WARM|COLD|DEAD",
  "pains_identified": ["array"],
  "conversation_history": ["array of messages"],
  "notes": ["array of notes"],
  "last_contact": "timestamp"
}
```

**Implementação n8n:**
- PostgreSQL Query (JOIN múltiplas tabelas)
- Tabelas: leads, conversations, pain_points, notes

---

## 2. DISCOVERY & PAIN ANALYSIS TOOLS

### `discover_pain_points`
**Descrição:** Registra dor/problema identificado durante descoberta

**Usado por:** Agente Descoberta

**Parâmetros:**
```json
{
  "lead_id": "uuid (required)",
  "pain_description": "string (required)",
  "severity": "number (1-10, required)",
  "category": "string (optional)", // efficiency, revenue, cost, etc
  "context": "string (optional)" // contexto adicional
}
```

**Retorno:**
```json
{
  "pain_id": "uuid",
  "lead_id": "uuid",
  "registered": true,
  "pain_count": "number" // total de dores do lead
}
```

**Implementação n8n:**
- PostgreSQL Insert node
- Tabela: `pain_points`
- Campos: lead_id, description, severity, category, created_at

---

### `calculate_gap_cost`
**Descrição:** Calcula custo financeiro da dor/gap (gera desconforto)

**Usado por:** Agente Descoberta

**Parâmetros:**
```json
{
  "lead_id": "uuid (required)",
  "current_state": "string (required)",
  "desired_state": "string (required)",
  "metrics": {
    "current_value": "number",
    "target_value": "number",
    "unit": "string", // leads/month, sales/month, etc
    "monetary_value_per_unit": "number"
  }
}
```

**Exemplo:**
```json
{
  "lead_id": "123",
  "current_state": "2% conversão, 100 leads/mês",
  "desired_state": "5% conversão (média mercado)",
  "metrics": {
    "current_value": 2,
    "target_value": 5,
    "unit": "vendas/mês",
    "monetary_value_per_unit": 5000
  }
}
```

**Retorno:**
```json
{
  "gap_cost_monthly": 15000,
  "gap_cost_yearly": 180000,
  "opportunity_description": "Perda de 3 vendas/mês = R$15k/mês",
  "formatted_message": "Custo de oportunidade: R$180k/ano"
}
```

**Implementação n8n:**
- Function node (JavaScript)
- Lógica: (target - current) * monetary_value * time_period
- Salva resultado em tabela: `gap_analysis`

---

### `update_discovery_notes`
**Descrição:** Salva resumo completo da descoberta

**Usado por:** Agente Descoberta (antes de passar para Qualificação)

**Parâmetros:**
```json
{
  "lead_id": "uuid (required)",
  "summary": "string (required)",
  "pains": ["array of strings (required)"],
  "implications": ["array of strings (required)"],
  "readiness": "string (required)", // ready, hesitant, not_ready
  "next_step": "string (optional)"
}
```

**Retorno:**
```json
{
  "lead_id": "uuid",
  "discovery_completed": true,
  "updated_at": "timestamp"
}
```

**Implementação n8n:**
- PostgreSQL Update node
- Tabela: `leads`
- Campo: `discovery_notes` (JSONB)

---

## 3. QUALIFICATION & SCORING TOOLS

### `qualify_bant`
**Descrição:** Registra avaliação BANT completa

**Usado por:** Agente Qualificação

**Parâmetros:**
```json
{
  "lead_id": "uuid (required)",
  "budget": {
    "score": "number (0-3, required)",
    "notes": "string (required)"
  },
  "authority": {
    "score": "number (0-3, required)",
    "notes": "string (required)"
  },
  "need": {
    "score": "number (0-3, required)",
    "notes": "string (required)"
  },
  "timeline": {
    "score": "number (0-3, required)",
    "notes": "string (required)"
  }
}
```

**Retorno:**
```json
{
  "lead_id": "uuid",
  "total_score": 10,
  "breakdown": {
    "budget": 2,
    "authority": 3,
    "need": 3,
    "timeline": 2
  },
  "bant_completed": true
}
```

**Implementação n8n:**
- Function node: soma scores
- PostgreSQL Update node
- Tabela: `leads`
- Campos: `bant_data` (JSONB), `bant_score` (number)

---

### `calculate_lead_score`
**Descrição:** Calcula score final do lead (pode incluir fatores além de BANT)

**Usado por:** Agente Qualificação

**Parâmetros:**
```json
{
  "lead_id": "uuid (required)",
  "bant_score": "number (required)",
  "engagement_level": "string (optional)", // high, medium, low
  "company_size": "string (optional)", // small, medium, large
  "industry_fit": "boolean (optional)",
  "previous_interactions": "number (optional)"
}
```

**Retorno:**
```json
{
  "final_score": 9.5,
  "classification": "HOT",
  "recommendation": "route_to_closing_agent",
  "priority": "P0",
  "factors": {
    "bant": 10,
    "engagement": 1.5,
    "fit": 1.0,
    "weighted_total": 9.5
  }
}
```

**Implementação n8n:**
- Function node (algoritmo de scoring)
- Pode usar ML model (opcional, futuro)
- Salva em tabela: `leads`

---

### `classify_fit`
**Descrição:** Classifica lead em Hot/Warm/Cold/Dead baseado em score

**Usado por:** Agente Qualificação

**Parâmetros:**
```json
{
  "lead_id": "uuid (required)",
  "score": "number (required)"
}
```

**Retorno:**
```json
{
  "lead_id": "uuid",
  "classification": "HOT", // HOT (9-12), WARM (6-8), COLD (1-5), DEAD (0)
  "updated": true
}
```

**Implementação n8n:**
- Function node:
  - score >= 9: HOT
  - score >= 6: WARM
  - score >= 1: COLD
  - score = 0: DEAD
- PostgreSQL Update node

---

### `identify_decision_maker`
**Descrição:** Registra informações sobre decisor

**Usado por:** Agente Qualificação

**Parâmetros:**
```json
{
  "lead_id": "uuid (required)",
  "current_contact_role": "string (required)",
  "decision_maker_identified": "string (required)",
  "access_to_decision_maker": "boolean (required)",
  "decision_process": "string (optional)"
}
```

**Retorno:**
```json
{
  "lead_id": "uuid",
  "authority_validated": true,
  "decision_maker": "João Silva (CEO)"
}
```

**Implementação n8n:**
- PostgreSQL Update node
- Tabela: `leads`
- Campo: `authority_info` (JSONB)

---

### `update_qualification_notes`
**Descrição:** Salva resumo completo da qualificação

**Usado por:** Agente Qualificação (antes de rotear)

**Parâmetros:**
```json
{
  "lead_id": "uuid (required)",
  "bant_summary": {
    "budget": "string",
    "authority": "string",
    "need": "string",
    "timeline": "string"
  },
  "total_score": "number (required)",
  "classification": "string (required)",
  "next_step": "string (required)",
  "notes": "string (optional)"
}
```

**Retorno:**
```json
{
  "lead_id": "uuid",
  "qualification_completed": true,
  "updated_at": "timestamp"
}
```

**Implementação n8n:**
- PostgreSQL Update node
- Tabela: `leads`
- Atualiza: `qualification_notes`, `current_stage`

---

## 4. FOLLOW-UP & NURTURING TOOLS

### `schedule_followup`
**Descrição:** Agenda próximo contato automático

**Usado por:** Agente Follow-up

**Parâmetros:**
```json
{
  "lead_id": "uuid (required)",
  "days_until_next": "number (required)",
  "followup_type": "string (required)", // educational_content, case_study, check_in, breakup
  "classification": "string (required)", // HOT, WARM, COLD
  "notes": "string (optional)",
  "content_to_send": "string (optional)"
}
```

**Retorno:**
```json
{
  "followup_id": "uuid",
  "lead_id": "uuid",
  "scheduled_for": "timestamp",
  "type": "educational_content",
  "scheduled": true
}
```

**Implementação n8n:**
- PostgreSQL Insert node
- Tabela: `scheduled_followups`
- Trigger node com Cron job para processar followups

---

### `send_content`
**Descrição:** Envia conteúdo educativo/case study para o lead

**Usado por:** Agente Follow-up

**Parâmetros:**
```json
{
  "lead_id": "uuid (required)",
  "content_type": "string (required)", // case_study, article, guide, webinar, etc
  "topic": "string (required)",
  "content_url": "string (optional)",
  "content_text": "string (optional)",
  "tracking_enabled": "boolean (default: true)"
}
```

**Retorno:**
```json
{
  "content_id": "uuid",
  "lead_id": "uuid",
  "sent": true,
  "sent_at": "timestamp",
  "tracking_url": "string" // se tracking_enabled
}
```

**Implementação n8n:**
- HTTP Request (para encurtador de URL com tracking)
- Send Message node (WhatsApp/Email/etc)
- PostgreSQL Insert (log de envio)

---

### `track_engagement`
**Descrição:** Registra ação de engajamento do lead

**Usado por:** Agente Follow-up (automaticamente via webhooks)

**Parâmetros:**
```json
{
  "lead_id": "uuid (required)",
  "action": "string (required)", // opened_email, clicked_link, downloaded_file, responded, visited_site
  "content_id": "uuid (optional)",
  "timestamp": "timestamp (required)"
}
```

**Retorno:**
```json
{
  "engagement_id": "uuid",
  "lead_id": "uuid",
  "action": "opened_email",
  "tracked": true
}
```

**Implementação n8n:**
- Webhook receiver (para cliques, aberturas)
- PostgreSQL Insert node
- Tabela: `engagement_tracking`

---

### `calculate_engagement_score`
**Descrição:** Calcula score de engajamento do lead (identifica aquecimento)

**Usado por:** Agente Follow-up

**Parâmetros:**
```json
{
  "lead_id": "uuid (required)",
  "timeframe_days": "number (default: 30)"
}
```

**Retorno:**
```json
{
  "engagement_score": 7.5,
  "trend": "increasing", // increasing, stable, decreasing
  "recommendation": "requalify_lead", // requalify_lead, continue_nurturing, pause_sequence
  "signals": [
    "opened_3_emails",
    "clicked_2_links",
    "responded_once"
  ],
  "last_engagement": "timestamp"
}
```

**Implementação n8n:**
- PostgreSQL Query (aggregate engagement actions)
- Function node (algoritmo de scoring)
  - Opened = +1 ponto
  - Clicked = +2 pontos
  - Responded = +5 pontos
  - Visited site = +3 pontos

---

### `check_heat_level`
**Descrição:** Verifica se classificação do lead mudou (esquentou ou esfriou)

**Usado por:** Agente Follow-up

**Parâmetros:**
```json
{
  "lead_id": "uuid (required)"
}
```

**Retorno:**
```json
{
  "current_classification": "WARM",
  "previous_classification": "COLD",
  "changed": true,
  "reason": "Increased engagement - opened 3 materials in 7 days",
  "new_score": 7,
  "recommendation": "requalify"
}
```

**Implementação n8n:**
- Query engagement_score
- Compare com classification anterior
- Function node (lógica de mudança)

---

### `update_followup_notes`
**Descrição:** Atualiza notas de follow-up após interação

**Usado por:** Agente Follow-up

**Parâmetros:**
```json
{
  "lead_id": "uuid (required)",
  "interaction_date": "date (required)",
  "summary": "string (required)",
  "next_action": "string (optional)",
  "heat_level_change": "string (optional)" // ex: "COLD → WARM"
}
```

**Retorno:**
```json
{
  "lead_id": "uuid",
  "updated": true,
  "updated_at": "timestamp"
}
```

**Implementação n8n:**
- PostgreSQL Insert node
- Tabela: `followup_notes`

---

## 5. CLOSING & CONVERSION TOOLS

### `create_meeting_offer`
**Descrição:** Propõe agendamento de call/demo

**Usado por:** Agente Fechamento

**Parâmetros:**
```json
{
  "lead_id": "uuid (required)",
  "meeting_type": "string (required)", // strategic_call, demo, diagnosis, etc
  "proposed_dates": ["array of datetime strings (required)"],
  "duration_minutes": "number (required)",
  "agenda": "string (optional)"
}
```

**Retorno:**
```json
{
  "meeting_id": "uuid",
  "lead_id": "uuid",
  "proposed_dates": ["..."],
  "booking_link": "string", // calendly, etc
  "offered": true
}
```

**Implementação n8n:**
- Integração com Calendly/Google Calendar
- PostgreSQL Insert (log de proposta)
- Send message com link

---

### `send_proposal`
**Descrição:** Gera e envia proposta comercial customizada

**Usado por:** Agente Fechamento

**Parâmetros:**
```json
{
  "lead_id": "uuid (required)",
  "proposal_type": "string (required)", // custom, standard
  "includes": ["array of strings (required)"], // gap_analysis, solution_overview, timeline, investment_roi
  "valid_until": "date (required)",
  "custom_data": {
    "pain_points": ["array"],
    "estimated_cost_of_pain": "number",
    "expected_roi": "string"
  }
}
```

**Retorno:**
```json
{
  "proposal_id": "uuid",
  "lead_id": "uuid",
  "proposal_url": "string",
  "sent": true,
  "sent_at": "timestamp",
  "valid_until": "date"
}
```

**Implementação n8n:**
- Generate PDF node (template)
- Upload to storage (S3, Drive)
- Send proposal via email/WhatsApp
- PostgreSQL Insert (log)

---

### `send_case_study`
**Descrição:** Envia case relevante que ajuda na decisão

**Usado por:** Agente Fechamento

**Parâmetros:**
```json
{
  "lead_id": "uuid (required)",
  "case_company": "string (required)",
  "similar_pain": "string (required)",
  "result": "string (required)",
  "timeframe": "string (required)"
}
```

**Retorno:**
```json
{
  "case_id": "uuid",
  "lead_id": "uuid",
  "sent": true,
  "tracking_enabled": true
}
```

**Implementação n8n:**
- Query case studies database
- Send message com case
- Track engagement

---

### `handle_objection`
**Descrição:** Documenta objeção e resposta dada

**Usado por:** Agente Fechamento

**Parâmetros:**
```json
{
  "lead_id": "uuid (required)",
  "objection_type": "string (required)", // price, timing, need_approval, competitor, etc
  "objection_text": "string (required)",
  "response_strategy": "string (required)", // roi_reframe, urgency, social_proof, etc
  "resolved": "boolean (required)"
}
```

**Retorno:**
```json
{
  "objection_id": "uuid",
  "lead_id": "uuid",
  "logged": true,
  "resolved": true
}
```

**Implementação n8n:**
- PostgreSQL Insert node
- Tabela: `objections`
- Útil para análise e ML futuro

---

### `mark_outcome`
**Descrição:** Marca resultado final (won/lost/stalled)

**Usado por:** Agente Fechamento

**Parâmetros WON:**
```json
{
  "lead_id": "uuid (required)",
  "outcome": "won",
  "details": {
    "close_date": "date",
    "contract_value": "number",
    "payment_terms": "string",
    "start_date": "date"
  }
}
```

**Parâmetros LOST:**
```json
{
  "lead_id": "uuid (required)",
  "outcome": "lost",
  "reason": "string", // no_budget, chose_competitor, not_right_time, etc
  "details": "string",
  "competitor": "string (optional)"
}
```

**Parâmetros STALLED:**
```json
{
  "lead_id": "uuid (required)",
  "outcome": "stalled",
  "reason": "string",
  "followup_date": "date",
  "route_to": "agente_followup"
}
```

**Retorno:**
```json
{
  "lead_id": "uuid",
  "outcome": "won",
  "updated": true,
  "updated_at": "timestamp"
}
```

**Implementação n8n:**
- PostgreSQL Update node
- Tabela: `leads`
- Campos: `outcome`, `outcome_date`, `outcome_details`
- Trigger: notificação para time

---

### `generate_custom_proposal`
**Descrição:** Gera proposta comercial baseada em template e dados do lead

**Usado por:** Agente Fechamento

**Parâmetros:**
```json
{
  "lead_id": "uuid (required)",
  "template": "string (required)", // standard_b2b, enterprise, startup, etc
  "include_roi_calculator": "boolean (default: true)",
  "include_timeline": "boolean (default: true)",
  "custom_sections": ["array of strings (optional)"]
}
```

**Retorno:**
```json
{
  "proposal_id": "uuid",
  "proposal_url": "string",
  "generated": true
}
```

**Implementação n8n:**
- Template engine (Handlebars/Jinja)
- PDF generation
- Upload to storage

---

### `notify_team`
**Descrição:** Notifica time de vendas/CS sobre evento importante

**Usado por:** Agente Fechamento, Agente Follow-up

**Parâmetros:**
```json
{
  "lead_id": "uuid (required)",
  "notification_type": "string (required)", // deal_closed, hot_lead, needs_human_intervention, etc
  "priority": "string (required)", // high, medium, low
  "message": "string (required)",
  "assignee": "string (optional)" // email ou user_id
}
```

**Retorno:**
```json
{
  "notification_id": "uuid",
  "sent": true,
  "sent_to": ["array of recipients"],
  "sent_at": "timestamp"
}
```

**Implementação n8n:**
- Send Email node
- Slack/Discord notification
- SMS (Twilio) para casos urgentes
- PostgreSQL Insert (log)

---

## 6. INTELLIGENCE & ANALYTICS TOOLS

### `think_tool`
**Descrição:** Ferramenta de reflexão interna (prompt engineering)

**Usado por:** Todos os agentes (especialmente Supervisor)

**Parâmetros:**
```json
{
  "context": "string (required)", // contexto atual
  "decision_needed": "string (required)", // o que precisa decidir
  "factors": "array of strings (optional)" // fatores a considerar
}
```

**Retorno:**
```json
{
  "thinking_process": "string", // raciocínio estruturado
  "decision": "string", // decisão tomada
  "confidence": "number (0-10)", // confiança na decisão
  "reasoning": "string" // justificativa
}
```

**Implementação n8n:**
- Não é uma tool externa
- É um system prompt pattern para o LLM
- Força o agente a raciocinar antes de agir

---

### `analyze_sentiment`
**Descrição:** Analisa sentimento/tom do lead nas mensagens

**Usado por:** Todos os agentes (background)

**Parâmetros:**
```json
{
  "lead_id": "uuid (required)",
  "messages": ["array of strings (required)"]
}
```

**Retorno:**
```json
{
  "sentiment": "positive", // positive, neutral, negative
  "confidence": 0.87,
  "tone": "engaged", // engaged, hesitant, frustrated, excited
  "signals": ["responsive", "asking_questions", "showing_interest"]
}
```

**Implementação n8n:**
- LLM API call (sentiment analysis)
- Ou ML model específico
- Salva em `lead_analytics`

---

### `predict_churn_risk`
**Descrição:** Prediz risco de lead desengajar/abandonar

**Usado por:** Agente Follow-up

**Parâmetros:**
```json
{
  "lead_id": "uuid (required)"
}
```

**Retorno:**
```json
{
  "churn_risk": "high", // high, medium, low
  "probability": 0.73,
  "factors": [
    "No engagement last 21 days",
    "Opened 0 of 3 emails",
    "Never responded to follow-ups"
  ],
  "recommendation": "send_breakup_message"
}
```

**Implementação n8n:**
- PostgreSQL Query (engagement data)
- Function node (scoring algorithm)
- Opcional: ML model (futuro)

---

## Database Schema (PostgreSQL)

### Table: `leads`
```sql
CREATE TABLE leads (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name VARCHAR(255) NOT NULL,
  company VARCHAR(255) NOT NULL,
  role VARCHAR(100),
  channel VARCHAR(50) NOT NULL,
  phone VARCHAR(50),
  email VARCHAR(255),
  source VARCHAR(100),

  -- Stage tracking
  current_stage VARCHAR(50), -- discovery, qualification, followup, closing
  current_agent VARCHAR(50),

  -- Scoring & Classification
  bant_score INTEGER,
  classification VARCHAR(20), -- HOT, WARM, COLD, DEAD
  final_score DECIMAL(4,2),

  -- Notes & Data (JSONB for flexibility)
  discovery_notes JSONB,
  qualification_notes JSONB,
  bant_data JSONB,
  authority_info JSONB,

  -- Outcome
  outcome VARCHAR(20), -- won, lost, stalled, active
  outcome_date TIMESTAMP,
  outcome_details JSONB,

  -- Timestamps
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW(),
  last_contact TIMESTAMP
);
```

### Table: `pain_points`
```sql
CREATE TABLE pain_points (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  lead_id UUID REFERENCES leads(id),
  description TEXT NOT NULL,
  severity INTEGER, -- 1-10
  category VARCHAR(100),
  context TEXT,
  created_at TIMESTAMP DEFAULT NOW()
);
```

### Table: `gap_analysis`
```sql
CREATE TABLE gap_analysis (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  lead_id UUID REFERENCES leads(id),
  current_state TEXT,
  desired_state TEXT,
  gap_cost_monthly DECIMAL(12,2),
  gap_cost_yearly DECIMAL(12,2),
  metrics JSONB,
  created_at TIMESTAMP DEFAULT NOW()
);
```

### Table: `scheduled_followups`
```sql
CREATE TABLE scheduled_followups (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  lead_id UUID REFERENCES leads(id),
  scheduled_for TIMESTAMP NOT NULL,
  followup_type VARCHAR(100),
  classification VARCHAR(20),
  notes TEXT,
  content_to_send TEXT,
  status VARCHAR(20) DEFAULT 'pending', -- pending, sent, cancelled
  sent_at TIMESTAMP,
  created_at TIMESTAMP DEFAULT NOW()
);
```

### Table: `engagement_tracking`
```sql
CREATE TABLE engagement_tracking (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  lead_id UUID REFERENCES leads(id),
  action VARCHAR(100) NOT NULL, -- opened_email, clicked_link, etc
  content_id UUID,
  created_at TIMESTAMP DEFAULT NOW()
);
```

### Table: `objections`
```sql
CREATE TABLE objections (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  lead_id UUID REFERENCES leads(id),
  objection_type VARCHAR(100),
  objection_text TEXT,
  response_strategy VARCHAR(100),
  resolved BOOLEAN,
  created_at TIMESTAMP DEFAULT NOW()
);
```

### Table: `conversations`
```sql
CREATE TABLE conversations (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  lead_id UUID REFERENCES leads(id),
  agent VARCHAR(50),
  role VARCHAR(20), -- user, assistant
  message TEXT,
  created_at TIMESTAMP DEFAULT NOW()
);
```

---

## Implementação n8n - Exemplo de Workflow

### Exemplo: `qualify_bant` Tool

```
[Webhook Trigger] → Recebe requisição com parâmetros BANT
    ↓
[Function Node] → Calcula total_score (soma B+A+N+T)
    ↓
[PostgreSQL Update] → Atualiza leads table
    SET bant_score = total_score
    SET bant_data = JSONB com breakdown
    WHERE lead_id = {{ $json.lead_id }}
    ↓
[Return Response] → Retorna JSON com score e breakdown
```

### Exemplo: `send_content` Tool

```
[Webhook Trigger] → Recebe lead_id, content_type, topic
    ↓
[PostgreSQL Query] → Busca conteúdo relevante em content_library
    WHERE type = content_type
    AND topic LIKE %topic%
    ↓
[Bitly/Rebrandly] → Gera link rastreável
    ↓
[WhatsApp Send Message] → Envia mensagem com link
    ↓
[PostgreSQL Insert] → Log em engagement_tracking
    ↓
[Return Response] → Confirma envio
```

---

## Best Practices

### 1. Idempotência
Tools devem ser idempotentes quando possível. Se chamada múltiplas vezes com mesmos parâmetros, resultado deve ser o mesmo.

### 2. Error Handling
Sempre retornar erro estruturado:
```json
{
  "error": true,
  "error_code": "LEAD_NOT_FOUND",
  "error_message": "Lead with ID 123 not found",
  "timestamp": "2025-11-14T10:30:00Z"
}
```

### 3. Logging
Toda tool call deve ser logada:
- Timestamp
- Lead ID
- Tool name
- Parameters
- Result
- Duration

### 4. Rate Limiting
Implementar rate limiting para tools que enviam mensagens (evitar spam).

### 5. Async quando apropriado
Tools pesadas (send_proposal, generate_pdf) devem ser assíncronas com callback.

---

**Todas as tools estão documentadas e prontas para implementação no n8n!**
