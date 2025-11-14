# Arquitetura B2B - Sistema de Venda Consultiva

## 🎯 Objetivo do Sistema

Qualificar leads B2B através de uma abordagem consultiva que:
1. **Identifica dores** sem que o cliente perceba que está sendo vendido
2. **Gera desconforto** mostrando gaps e oportunidades perdidas
3. **Nutre** o lead com follow-ups inteligentes
4. **Converte** quando o lead está pronto (inbound)

## 🧠 Metodologia de Vendas

### SPIN Selling Adaptado
- **Situation**: Entender situação atual do negócio
- **Problem**: Identificar problemas e dificuldades
- **Implication**: Explorar consequências (gerar desconforto)
- **Need-Payoff**: Mostrar valor da solução (sem vender ainda)

### Gap Selling
- **Estado Atual** → **Estado Desejado** = GAP
- Foco no custo de não resolver o gap
- Cliente percebe a dor sozinho

## 🤖 Agentes do Sistema

### 1. Agente Supervisor
**Papel:** Orquestrador inteligente
**Decisões:**
- Novo lead → Agente Descoberta
- Lead em qualificação → Agente Qualificação
- Lead qualificado + tempo → Agente Follow-up
- Lead quente → Agente Fechamento

### 2. Agente Descoberta (Discovery Agent)
**Papel:** Consultor que faz perguntas estratégicas

**Objetivos:**
- Entender o negócio do cliente
- Identificar dores e gaps
- Gerar awareness sem vender
- Criar rapport e confiança

**Técnicas:**
- Perguntas abertas sobre situação atual
- Identificação de problemas específicos
- Exploração de consequências (implicação)
- Comparação com mercado/concorrentes

**Perguntas Estratégicas:**
```
SITUAÇÃO:
- "Como vocês estão gerando leads atualmente?"
- "Quantos leads vocês estão convertendo por mês?"
- "Qual o ticket médio das suas vendas?"

PROBLEMA:
- "Qual o maior desafio que vocês enfrentam com isso?"
- "Onde vocês sentem que estão perdendo mais vendas?"
- "O que impede vocês de escalar mais rápido?"

IMPLICAÇÃO:
- "Quanto isso está custando para vocês por mês?"
- "Como isso afeta o crescimento da empresa?"
- "Se nada mudar, onde vocês estarão em 6 meses?"

NEED-PAYOFF:
- "Como seria se vocês conseguissem dobrar a conversão?"
- "Quanto valeria resolver esse problema?"
- "O que vocês poderiam fazer com esse tempo/dinheiro extra?"
```

**Tools:**
- `create_lead` - Registra novo lead
- `discover_pain_points` - Anota dores identificadas
- `calculate_gap_cost` - Calcula custo da dor
- `update_discovery_notes` - Salva insights

### 3. Agente Qualificação (Qualification Agent)
**Papel:** Validador de fit comercial

**Frameworks:**
- **BANT**: Budget, Authority, Need, Timeline
- **MEDDIC**: Metrics, Economic Buyer, Decision Criteria, Decision Process, Identify Pain, Champion

**Critérios de Score:**
```
🔥 HOT (9-10):
- Dor clara e urgente
- Budget disponível
- Decisor na conversa
- Timeline curto (<30 dias)

🟡 WARM (6-8):
- Dor identificada
- Budget provável
- Acesso ao decisor
- Timeline médio (30-90 dias)

❄️ COLD (1-5):
- Dor vaga
- Budget incerto
- Não é decisor
- Timeline longo (>90 dias)
```

**Tools:**
- `qualify_bant` - Qualificação BANT
- `calculate_lead_score` - Calcula score 1-10
- `classify_fit` - Hot/Warm/Cold
- `identify_decision_maker` - Valida autoridade

### 4. Agente Follow-up (Nurturing Agent)
**Papel:** Nutricionista de leads

**Estratégias:**
- Follow-up baseado em tempo (3, 7, 14, 30 dias)
- Follow-up baseado em comportamento (abriu link, baixou material)
- Conteúdo educativo progressivo
- Prova social (cases, depoimentos)

**Tipos de Follow-up:**

**Sequência para WARM:**
```
Dia 3: "Vi que você mencionou [DOR]. Preparei um material sobre isso"
Dia 7: "Case: Como [Empresa X] resolveu [DOR SIMILAR]"
Dia 14: "Checando: conseguiu avaliar aquele material?"
Dia 30: "[Dado de mercado] que pode impactar seu negócio"
```

**Sequência para COLD:**
```
Dia 7: Conteúdo educativo geral
Dia 21: Artigo sobre tendências do setor
Dia 45: Newsletter com insights
Dia 90: "Ainda faz sentido conversarmos?"
```

**Triggers de Re-engajamento:**
- Lead abriu email/mensagem
- Lead clicou em link
- Lead baixou material
- Lead respondeu mensagem
- Lead visitou site

**Tools:**
- `schedule_followup` - Agenda próximo contato
- `send_educational_content` - Envia conteúdo
- `track_engagement` - Monitora engajamento
- `check_heat_level` - Verifica se esquentou

### 5. Agente Fechamento (Closing Agent)
**Papel:** Conversor final

**Gatilhos de Ativação:**
- Lead score > 8
- Engajamento alto (abriu 3+ materiais)
- Resposta positiva em follow-up
- Mencionou urgência/timeline
- Pediu mais informações

**Técnicas:**
- Oferta soft (não agressiva)
- Foco em próximo passo pequeno
- Agendamento de call estratégica
- Apresentação de proposta

**Abordagem:**
```
"[Nome], pelos nossos papos vi que [DOR] é algo urgente
para vocês.

Tenho ajudado empresas como [SIMILAR] a resolver isso,
gerando [RESULTADO ESPECÍFICO].

Faz sentido a gente fazer uma call de 20min para eu te
mostrar como podemos fazer o mesmo para vocês?

Tenho [DATA 1] ou [DATA 2] disponível. Qual funciona melhor?"
```

**Tools:**
- `create_meeting_offer` - Propõe agendamento
- `send_case_study` - Envia case relevante
- `generate_custom_proposal` - Cria proposta
- `mark_as_closed_won` - Marca como ganho
- `mark_as_closed_lost` - Marca como perdido

## 🔄 Fluxo Completo

```
Lead entra (disparo inicial)
    ↓
Agente Supervisor → Roteia para Descoberta
    ↓
Agente Descoberta:
- Faz perguntas SPIN
- Identifica dores
- Gera awareness/desconforto
- Cria rapport
    ↓
[Se dor identificada] → Agente Qualificação
    ↓
Agente Qualificação:
- Valida BANT/MEDDIC
- Calcula score (1-10)
- Classifica: Hot/Warm/Cold
    ↓
┌─────────┬─────────┬─────────┐
│   HOT   │  WARM   │  COLD   │
│ (9-10)  │ (6-8)   │ (1-5)   │
└─────────┴─────────┴─────────┘
    │         │         │
    ↓         ↓         ↓
Fechamento Follow-up  Follow-up
Imediato   Intensivo  Espaçado
    │         │         │
    ↓         ↓         ↓
  Call    [Nutrição]  [Nutrição]
  Agendada   + Monitoramento
    │         │         │
    ↓         ↓         ↓
 GANHO    [Se esquentar]  [Se esquentar]
           → Fechamento → Fechamento
```

## 🎯 Critérios de Transição Entre Agentes

### Descoberta → Qualificação
**Trigger:**
- Pelo menos 2 dores identificadas
- Cliente engajado (respondendo perguntas)
- Contexto de negócio claro

### Qualificação → Follow-up
**Trigger:**
- Score calculado (qualquer valor)
- BANT respondido (parcial ou completo)
- Classificação definida

### Follow-up → Fechamento
**Trigger:**
- Score aumentou para 8+
- Cliente demonstrou urgência
- Cliente pediu mais informações
- Alta taxa de engajamento

### Fechamento → Ganho/Perdido
**Trigger:**
- Cliente agendou call ✅
- Cliente aceitou proposta ✅
- Cliente recusou explicitamente ❌
- Cliente não responde após 3 tentativas ❌

## 🧰 Tools Compartilhadas

### Lead Management
- `create_lead(name, company, role, channel)`
- `update_lead(lead_id, data)`
- `get_lead_context(lead_id)`

### Pain Point Analysis
- `discover_pain_points(lead_id, pains[])`
- `calculate_gap_cost(current_state, desired_state)`
- `prioritize_pains(pains[])`

### Qualification
- `qualify_bant(budget, authority, need, timeline)`
- `calculate_lead_score(criteria{})`
- `classify_fit(score)` → Hot/Warm/Cold

### Follow-up Management
- `schedule_followup(lead_id, days, type)`
- `send_content(lead_id, content_type, topic)`
- `track_engagement(lead_id, action)`
- `calculate_engagement_score(lead_id)`

### Closing
- `propose_meeting(lead_id, dates[])`
- `send_proposal(lead_id, custom_data)`
- `mark_outcome(lead_id, status)` → won/lost

### Intelligence
- `think_tool(context, decision_needed)` - Reflexão interna
- `analyze_sentiment(messages)` - Analisa tom do lead
- `predict_churn_risk(lead_id)` - Risco de desengajamento

## 📊 Métricas e KPIs

### Por Estágio
- **Descoberta**: % de leads que identificam dores
- **Qualificação**: Distribuição Hot/Warm/Cold
- **Follow-up**: Taxa de engajamento, reaquecimento
- **Fechamento**: Taxa de conversão, tempo até close

### Globais
- **Tempo médio de qualificação**: Meta <7 dias
- **Taxa de conversão total**: Meta >15%
- **Lead score médio**: Acompanhar evolução
- **ROI por lead**: Receita / Custo de aquisição

## 🔐 Regras de Negócio

### Never Do
❌ Vender diretamente na primeira conversa
❌ Forçar resposta (dar espaço)
❌ Fazer muitas perguntas seguidas (max 2)
❌ Ignorar sinais de desinteresse
❌ Perseguir cold leads eternamente

### Always Do
✅ Fazer perguntas abertas
✅ Validar entendimento (parafrasear)
✅ Focar nas consequências da dor (implicação)
✅ Usar dados e comparações de mercado
✅ Respeitar timing do lead
✅ Documentar tudo no CRM

## 🎨 Personalização por Vertical

Este sistema é agnóstico, mas exemplos de adaptação:

### Se você vende: Automação de Marketing
**Dores comuns:**
- "Quantos leads vocês perdem por não responder rápido?"
- "Quanto tempo o time gasta qualificando leads frios?"
- "Qual % dos leads vocês conseguem realmente acompanhar?"

### Se você vende: Consultoria/Agência
**Dores comuns:**
- "Como vocês medem ROI das campanhas atuais?"
- "Quantos clientes vocês conseguem atender simultaneamente?"
- "Onde vocês sentem que deixam dinheiro na mesa?"

### Se você vende: SaaS B2B
**Dores comuns:**
- "Quanto tempo o time gasta em tarefas manuais?"
- "Quantos erros acontecem por processo manual?"
- "Qual o custo de não ter visibilidade em tempo real?"

---

**Próximos passos:** Implementação dos prompts de cada agente com as técnicas detalhadas.
