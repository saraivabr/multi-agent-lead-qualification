# Guia de Implementação - Sistema B2B de Venda Consultiva

## 📋 Visão Geral

Este guia contém tudo que você precisa para implementar um sistema completo de qualificação e conversão de leads B2B usando agentes especializados e técnicas de venda consultiva.

## 🎯 O Que o Sistema Faz

O sistema automatiza todo o funil de vendas B2B através de 4 agentes especializados:

1. **Agente Descoberta** - Identifica dores e gera desconforto (SPIN Selling)
2. **Agente Qualificação** - Valida fit comercial (BANT/MEDDIC)
3. **Agente Follow-up** - Nutre leads com conteúdo relevante
4. **Agente Fechamento** - Converte leads quentes

**Resultado:** Leads qualificados e prontos para seu time de vendas, com taxa de conversão 3-5x maior.

---

## 🚀 Quick Start (5 Passos)

### Passo 1: Setup do Ambiente
```bash
# 1. Clone o repositório
git clone [seu-repo]

# 2. Setup PostgreSQL
createdb lead_qualification

# 3. Execute migrations (schema no final deste doc)
psql lead_qualification < migrations/schema.sql

# 4. Configure n8n
docker run -it --rm --name n8n -p 5678:5678 -v ~/.n8n:/home/node/.n8n n8nio/n8n

# 5. Configure variáveis de ambiente
cp .env.example .env
# Edite: DATABASE_URL, LLM_API_KEY, WHATSAPP_TOKEN, etc
```

### Passo 2: Importe Workflows n8n
1. Acesse n8n (http://localhost:5678)
2. Importe workflows da pasta `/workflows/`:
   - `agente-supervisor-b2b.json`
   - `agente-descoberta.json`
   - `agente-qualificacao.json`
   - `agente-followup.json`
   - `agente-fechamento.json`

### Passo 3: Configure LLM (Claude/OpenAI)
Cada agente precisa de:
- **Model**: Claude Sonnet 4 ou GPT-4
- **System Prompt**: Use os arquivos em `/prompts/system-messages/`
- **Tools**: Configure conforme `/prompts/tools/b2b-tools.md`

### Passo 4: Conecte seu Canal
- WhatsApp: Evolution API, Baileys, ou oficial
- Email: SMTP + IMAP
- Chat Web: Widget custom
- Outros: API compatível

### Passo 5: Teste e Deploy
```bash
# Teste com lead simulado
curl -X POST http://localhost:5678/webhook/lead-test \
  -H "Content-Type: application/json" \
  -d '{"name":"João Silva","company":"Empresa XYZ","message":"Oi, quero saber mais"}'

# Monitor logs
tail -f ~/.n8n/logs/n8n.log

# Deploy em produção
# Use Railway, Render, AWS, ou seu servidor
```

---

## 📚 Arquitetura Detalhada

### Fluxo Completo de Conversão

```
LEAD ENTRA (disparo inicial)
    ↓
┌─────────────────────┐
│ Agente Supervisor  │ ← Analisa contexto e roteia
└─────────────────────┘
    ↓
┌─────────────────────┐
│ Agente Descoberta  │ ← Identifica dores (SPIN)
│ - Situation        │
│ - Problem          │
│ - Implication      │ ← GERA DESCONFORTO
│ - Need-Payoff      │
└─────────────────────┘
    ↓ (2+ dores identificadas)
┌─────────────────────┐
│ Agente Qualificação│ ← Valida fit (BANT)
│ - Budget (0-3)     │
│ - Authority (0-3)  │
│ - Need (0-3)       │
│ - Timeline (0-3)   │
│ Score Total: 0-12  │
└─────────────────────┘
    ↓
┌────────┬────────┬────────┐
│  HOT   │  WARM  │  COLD  │
│ (9-12) │ (6-8)  │ (1-5)  │
└────────┴────────┴────────┘
    │       │        │
    ↓       ↓        ↓
FECHAMENTO FOLLOW-UP FOLLOW-UP
(imediato)  (intenso) (leve)
    │       │        │
    ↓       ↓        ↓
  GANHO  [Nutre]  [Nutre]
         ↓ (esquentou)
    REQUALIFICA → FECHA
```

---

## 🤖 Agentes - Resumo

### 1. Agente Supervisor
**Arquivo:** `prompts/system-messages/agente-supervisor-b2b.md`

**Responsabilidade:** Orquestração inteligente

**Lógica:**
- Nova conversa? → Descoberta
- Descoberta completa? → Qualificação
- Qualificação completa? → Follow-up ou Fechamento (por score)
- Lead retornou? → Requalifica ou Fecha (por contexto)

**Tools:**
- `think_tool` (obrigatório)
- Chamadas de outros agentes

---

### 2. Agente Descoberta
**Arquivo:** `prompts/system-messages/agente-descoberta.md`

**Responsabilidade:** Identificar dores usando SPIN Selling

**Fases:**
1. **Situation**: Entender estado atual
2. **Problem**: Identificar dificuldades
3. **Implication**: Explorar consequências (GERA DESCONFORTO!)
4. **Need-Payoff**: Fazer cliente imaginar solução

**Tools:**
- `create_lead`
- `discover_pain_points`
- `calculate_gap_cost`
- `update_discovery_notes`

**Métrica de sucesso:** 2+ dores identificadas e quantificadas

---

### 3. Agente Qualificação
**Arquivo:** `prompts/system-messages/agente-qualificacao.md`

**Responsabilidade:** Validar fit com BANT

**Framework BANT:**
- **B**udget: Orçamento disponível? (0-3 pontos)
- **A**uthority: É decisor? (0-3 pontos)
- **N**eed: Dor é urgente? (0-3 pontos)
- **T**imeline: Prazo curto? (0-3 pontos)

**Classificação:**
- 9-12 pontos = **HOT** 🔥 (fecha agora)
- 6-8 pontos = **WARM** 🟡 (follow-up intenso)
- 1-5 pontos = **COLD** ❄️ (follow-up leve)
- 0 pontos = **DEAD** 💀 (descarta gentilmente)

**Tools:**
- `qualify_bant`
- `calculate_lead_score`
- `classify_fit`
- `update_qualification_notes`

---

### 4. Agente Follow-up
**Arquivo:** `prompts/system-messages/agente-followup.md`

**Responsabilidade:** Nutrição e reativação

**Estratégias por Classificação:**

**HOT (pausado):**
- Frequência: 3, 5, 7, 14, 21 dias
- Conteúdo: Cases específicos, urgência suave
- Objetivo: Manter top-of-mind

**WARM:**
- Frequência: 7, 14, 21, 30, 45, 60 dias
- Conteúdo: Educacional + cases + insights
- Objetivo: Construir valor até esquentar

**COLD:**
- Frequência: 15, 30, 60, 90, 120 dias
- Conteúdo: Educacional geral, tendências
- Objetivo: Manter base, aguardar sinal

**Tools:**
- `schedule_followup`
- `send_content`
- `track_engagement`
- `calculate_engagement_score`
- `check_heat_level`

**Triggers de Reativação:**
- Lead respondeu → Requalifica
- Lead abriu 2+ materiais → Tenta call
- Lead mencionou urgência → Escala para Fechamento

---

### 5. Agente Fechamento
**Arquivo:** `prompts/system-messages/agente-fechamento.md`

**Responsabilidade:** Conversão final

**Técnicas de Closing:**
1. **Assumptive Close**: "Vou preparar..." (não pergunta SE quer)
2. **Alternative Close**: "Opção A ou B?" (assume que fecha, pergunta qual)
3. **Urgency Close**: "Só 2 vagas em dezembro" (scarcity real)
4. **Puppy Dog Close**: "Teste 30 dias sem compromisso"
5. **Summary Close**: Recapitula tudo + decisão lógica

**Tools:**
- `create_meeting_offer`
- `send_proposal`
- `send_case_study`
- `handle_objection`
- `mark_outcome` (won/lost/stalled)
- `notify_team`

**Critérios de Ativação:**
- Score HOT (9-12)
- Lead WARM que reengajou com urgência
- Lead pediu proposta/reunião

---

## 🔧 Tools - Implementação

### Categorias de Tools

1. **Lead Management**: create_lead, update_lead, get_lead_context
2. **Discovery**: discover_pain_points, calculate_gap_cost
3. **Qualification**: qualify_bant, calculate_lead_score, classify_fit
4. **Follow-up**: schedule_followup, send_content, track_engagement
5. **Closing**: create_meeting_offer, send_proposal, mark_outcome
6. **Intelligence**: think_tool, analyze_sentiment, predict_churn_risk

### Exemplo de Implementação: `qualify_bant`

**n8n Workflow:**
```
Webhook (recebe BANT data)
    ↓
Function Node (calcula score = B+A+N+T)
    ↓
PostgreSQL Update (salva em leads table)
    ↓
Return Response (score + classification)
```

**Ver documentação completa:** `/prompts/tools/b2b-tools.md`

---

## 🗄️ Database Schema

### Core Tables

```sql
-- Leads (principal)
CREATE TABLE leads (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name VARCHAR(255) NOT NULL,
  company VARCHAR(255) NOT NULL,
  current_stage VARCHAR(50),
  current_agent VARCHAR(50),
  bant_score INTEGER,
  classification VARCHAR(20), -- HOT/WARM/COLD/DEAD
  outcome VARCHAR(20), -- won/lost/stalled/active
  created_at TIMESTAMP DEFAULT NOW()
);

-- Pain Points
CREATE TABLE pain_points (
  id UUID PRIMARY KEY,
  lead_id UUID REFERENCES leads(id),
  description TEXT NOT NULL,
  severity INTEGER, -- 1-10
  created_at TIMESTAMP DEFAULT NOW()
);

-- Engagement Tracking
CREATE TABLE engagement_tracking (
  id UUID PRIMARY KEY,
  lead_id UUID REFERENCES leads(id),
  action VARCHAR(100), -- opened, clicked, responded, etc
  created_at TIMESTAMP DEFAULT NOW()
);

-- Scheduled Follow-ups
CREATE TABLE scheduled_followups (
  id UUID PRIMARY KEY,
  lead_id UUID REFERENCES leads(id),
  scheduled_for TIMESTAMP NOT NULL,
  followup_type VARCHAR(100),
  classification VARCHAR(20),
  status VARCHAR(20) DEFAULT 'pending'
);
```

**Schema completo:** Ver `/prompts/tools/b2b-tools.md`

---

## 📊 Métricas e KPIs

### Por Estágio
- **Descoberta**: % que identificam 2+ dores
- **Qualificação**: Distribuição Hot/Warm/Cold
- **Follow-up**: Taxa de engajamento, reaquecimento
- **Fechamento**: Taxa de conversão, tempo até close

### Globais
- **Tempo médio de qualificação**: <7 dias (meta)
- **Taxa de conversão total**: >15% (meta)
- **ROI por lead**: Receita / Custo aquisição
- **Lead velocity**: Leads/dia processados

### Dashboard Sugerido
```
┌──────────────────────────────────────┐
│  LEADS POR ESTÁGIO                   │
│  Descoberta: 45 | Qualificação: 23   │
│  Follow-up: 67  | Fechamento: 12     │
├──────────────────────────────────────┤
│  DISTRIBUIÇÃO DE SCORE               │
│  🔥 HOT (9-12): 15 leads             │
│  🟡 WARM (6-8): 34 leads             │
│  ❄️ COLD (1-5): 41 leads            │
├──────────────────────────────────────┤
│  CONVERSÕES                          │
│  Ganhos: 8  | Perdidos: 3           │
│  Taxa: 18.2% | Ticket: R$12.5k      │
└──────────────────────────────────────┘
```

---

## 🎨 Customização para Seu Nicho

### Você Vende: Automação de Marketing

**Dores comuns a identificar:**
- "Quantos leads vocês perdem por responder devagar?"
- "Qual % de leads vocês conseguem realmente acompanhar?"
- "Quanto tempo o time gasta qualificando leads frios?"

**Implicações a explorar:**
- Custo de aquisição desperdiçado
- Oportunidades perdidas para concorrentes
- Burnout do time comercial

---

### Você Vende: Consultoria/Agência

**Dores comuns a identificar:**
- "Como vocês medem ROI das campanhas atuais?"
- "Quantos clientes conseguem atender simultaneamente?"
- "Onde sentem que deixam dinheiro na mesa?"

**Implicações a explorar:**
- Falta de previsibilidade de receita
- Capacidade ociosa (ou sobrecarregada)
- Clientes insatisfeitos por falta de atenção

---

### Você Vende: SaaS B2B

**Dores comuns a identificar:**
- "Quanto tempo o time gasta em tarefas manuais?"
- "Quantos erros acontecem por processo manual?"
- "Qual o custo de não ter visibilidade em tempo real?"

**Implicações a explorar:**
- Horas desperdiçadas × salário do time
- Erros custando dinheiro/reputação
- Decisões lentas perdendo oportunidades

---

## ⚙️ Configurações Avançadas

### A/B Testing de Prompts
Teste variações dos prompts para otimizar conversão:

```bash
# Versão A: Mais direto
# Versão B: Mais consultivo
# Versão C: Mais dados/estatísticas

# Meça: taxa de resposta, tempo até qualificação, conversão final
```

### Lead Scoring com ML (Futuro)
Além de BANT, use ML para prever conversão:

```python
# Features:
# - Engajamento histórico
# - Tamanho da empresa
# - Setor
# - Sentimento nas mensagens
# - Tempo de resposta

# Model: XGBoost, Random Forest, ou Neural Network
# Output: Probabilidade de conversão (0-1)
```

### Integração com CRM
Sincronize com Pipedrive, HubSpot, Salesforce:

```javascript
// n8n: HTTP Request node
POST https://api.pipedrive.com/v1/deals
{
  "title": "{{ $json.company }} - {{ $json.name }}",
  "value": "{{ $json.estimated_value }}",
  "stage_id": "{{ $json.classification === 'HOT' ? 1 : 2 }}"
}
```

---

## 🐛 Troubleshooting

### Lead não está progredindo
**Diagnóstico:**
```sql
SELECT current_stage, current_agent, last_contact
FROM leads
WHERE id = 'lead-id';
```

**Soluções:**
- Verificar se agente está travado
- Forçar transição manual
- Revisar logs de erro

---

### Taxa de conversão baixa
**Checklist:**
- [ ] Dores sendo identificadas corretamente?
- [ ] Implicações sendo exploradas (desconforto)?
- [ ] Qualificação BANT completa?
- [ ] Follow-ups sendo enviados no timing certo?
- [ ] Objeções sendo tratadas?

**Ações:**
- Revisar gravações de conversas
- A/B test de prompts
- Treinar agentes com casos reais

---

### Leads abandonando (churn alto)
**Análise:**
```sql
SELECT
  followup_type,
  AVG(days_until_churn) as avg_days,
  COUNT(*) as churned_leads
FROM leads
WHERE outcome = 'lost'
GROUP BY followup_type;
```

**Ações:**
- Reduzir frequência de follow-up (não seja chato)
- Melhorar qualidade de conteúdo
- Segmentar melhor (classificação errada?)

---

## 📖 Referências e Recursos

### Livros Recomendados
- **"SPIN Selling"** - Neil Rackham (base do Agente Descoberta)
- **"The Gap Selling"** - Keenan (técnica de desconforto)
- **"Predictable Revenue"** - Aaron Ross (processo B2B)
- **"Fanatical Prospecting"** - Jeb Blount (follow-up)

### Frameworks Usados
- **SPIN**: Situation, Problem, Implication, Need-Payoff
- **BANT**: Budget, Authority, Need, Timeline
- **MEDDIC**: Metrics, Economic Buyer, Decision Criteria, Decision Process, Identify Pain, Champion

### Tools & Tecnologias
- **n8n**: Orquestração de workflows
- **PostgreSQL**: Database principal
- **Claude Sonnet 4 / GPT-4**: LLM para agentes
- **Evolution API / Baileys**: WhatsApp
- **Bitly / Rebrandly**: URL tracking

---

## 🚀 Próximos Passos

### Semana 1: Setup Básico
- [ ] Setup database (PostgreSQL)
- [ ] Instalar n8n
- [ ] Importar workflows
- [ ] Configurar agentes com prompts
- [ ] Conectar canal (WhatsApp/Email)

### Semana 2: Teste e Ajuste
- [ ] Testar com 10 leads simulados
- [ ] Ajustar prompts baseado em resultados
- [ ] Configurar tools básicas (create_lead, qualify_bant)
- [ ] Setup tracking de engajamento

### Semana 3: Launch Soft
- [ ] Processar 50-100 leads reais
- [ ] Monitorar métricas
- [ ] Iterar em prompts e fluxos
- [ ] Treinar time em como lidar com leads qualificados

### Semana 4: Escala
- [ ] Aumentar volume de leads
- [ ] Automatizar follow-ups completamente
- [ ] Integrar com CRM
- [ ] Setup dashboards de métricas

---

## 💡 Dicas Finais

### ✅ Boas Práticas
1. **Comece simples**: Implemente 1 agente por vez
2. **Teste muito**: Use leads simulados antes de reais
3. **Monitore sempre**: Logs são seus amigos
4. **Itere rápido**: Ajuste prompts semanalmente
5. **Mantenha humanos no loop**: Agentes qualificam, humanos fecham (no início)

### ❌ Armadilhas Comuns
1. Prompts muito genéricos (personalize para seu nicho!)
2. Pular a fase de Descoberta (identificar dores é crítico)
3. Follow-up muito frequente (respeite o lead)
4. Não documentar decisões (use think_tool sempre)
5. Ignorar métricas (o que não se mede, não se melhora)

---

## 🆘 Suporte

**Documentação completa:**
- Arquitetura: `/docs/arquitetura-b2b-consultivo.md`
- Prompts: `/prompts/system-messages/`
- Tools: `/prompts/tools/b2b-tools.md`

**Issues & Bugs:**
- GitHub Issues (se open source)
- Email de suporte (se comercial)

**Community:**
- Discord/Slack (se houver)
- Fórum (se houver)

---

**Desenvolvido com ❤️ para revolucionar vendas B2B**

**Versão:** 1.0.0
**Última atualização:** Novembro 2025
**Status:** Pronto para Produção 🚀
