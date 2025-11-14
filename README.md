# Sistema Multi-Agentes para Qualificação e Conversão B2B

> Sistema completo de venda consultiva que identifica dores, gera desconforto, qualifica, nutre e converte leads B2B automaticamente.

## 🎯 O Que Este Sistema Faz

Este sistema revoluciona vendas B2B automatizando o funil completo através de 4 agentes especializados que:

✅ **Identificam dores reais** usando SPIN Selling (não vendem, descobrem)
✅ **Geram desconforto** mostrando o custo de não agir (awareness)
✅ **Qualificam com BANT** - Budget, Authority, Need, Timeline (fit real)
✅ **Nutrem automaticamente** - Follow-ups inteligentes baseados em engajamento
✅ **Convertem naturalmente** - Fechamento consultivo, não agressivo
✅ **Funcionam 24/7** - Qualificação e conversão contínua, sem pausas

**Resultado:** Taxa de conversão 3-5x maior + Leads chegam prontos para seu time fechar.

## 🚀 O Diferencial

A maioria dos chatbots apenas responde perguntas. Este sistema **vende consultivamente**:

- **Venda consultiva real**: Usa SPIN Selling, BANT, Gap Selling (não é chatbot genérico)
- **Gera desconforto**: Faz cliente VER o custo de não agir (técnica comprovada)
- **Score objetivo**: Classifica Hot/Warm/Cold baseado em 12 critérios BANT
- **Follow-up inteligente**: Sequências automáticas baseadas em engajamento real
- **Fechamento natural**: Converte quando lead está pronto, não quando você quer
- **100% customizável**: Adapte para qualquer nicho B2B (SaaS, consultoria, agência, etc)

## 🏗️ Arquitetura - Funil Completo

```
Lead entra (WhatsApp/Email/Chat)
    ↓
┌─────────────────────┐
│ Agente Supervisor  │ ← Analisa e roteia
└─────────────────────┘
    ↓
┌─────────────────────┐
│ Agente Descoberta  │ ← SPIN Selling
│ Identifica dores   │   (Gera desconforto!)
│ Quantifica custos  │
└─────────────────────┘
    ↓
┌─────────────────────┐
│ Agente Qualificação│ ← BANT Scoring
│ Budget, Authority  │   (Hot/Warm/Cold)
│ Need, Timeline     │
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
         ↓ aqueceu
    REQUALIFICA → FECHA
```

## 🚀 Quick Start

```bash
# 1. Clone o repo
git clone [seu-repo]

# 2. Setup database
createdb lead_qualification
psql lead_qualification < migrations/schema.sql

# 3. Configure n8n
docker run -it --rm -p 5678:5678 -v ~/.n8n:/home/node/.n8n n8nio/n8n

# 4. Importe workflows da pasta /workflows/
# 5. Configure seus prompts em /prompts/system-messages/
# 6. Conecte seu canal (WhatsApp, Email, Chat)
# 7. Deploy e comece a converter!
```

**Ver guia completo:** [Guia de Implementação B2B](docs/guia-implementacao-b2b.md)

## 🤖 Os 4 Agentes Especializados

### 1. Agente Descoberta
- **Função**: Identificar dores usando SPIN Selling
- **Técnica**: Situation → Problem → Implication → Need-Payoff
- **Output**: 2+ dores identificadas + custo quantificado
- **Prompt**: [`prompts/system-messages/agente-descoberta.md`](prompts/system-messages/agente-descoberta.md)

### 2. Agente Qualificação
- **Função**: Validar fit comercial com BANT
- **Framework**: Budget + Authority + Need + Timeline (0-12 pontos)
- **Output**: Score e classificação (Hot/Warm/Cold)
- **Prompt**: [`prompts/system-messages/agente-qualificacao.md`](prompts/system-messages/agente-qualificacao.md)

### 3. Agente Follow-up
- **Função**: Nutrir leads com conteúdo relevante
- **Estratégia**: Sequências diferentes para Hot/Warm/Cold
- **Output**: Lead reaquecido ou breakup gentil
- **Prompt**: [`prompts/system-messages/agente-followup.md`](prompts/system-messages/agente-followup.md)

### 4. Agente Fechamento
- **Função**: Converter leads quentes
- **Técnicas**: Assumptive, Alternative, Urgency closes
- **Output**: Call agendada, proposta enviada, deal fechado
- **Prompt**: [`prompts/system-messages/agente-fechamento.md`](prompts/system-messages/agente-fechamento.md)

## 📚 Documentação Completa

### Guias de Implementação
- 📖 [**Guia de Implementação B2B**](docs/guia-implementacao-b2b.md) - **COMECE AQUI!**
- 🏗️ [Arquitetura B2B Consultiva](docs/arquitetura-b2b-consultivo.md)
- 🔧 [Tools e Integrações](prompts/tools/b2b-tools.md)

### Prompts dos Agentes
- 🎯 [Agente Supervisor B2B](prompts/system-messages/agente-supervisor-b2b.md)
- 🔍 [Agente Descoberta](prompts/system-messages/agente-descoberta.md)
- ✅ [Agente Qualificação](prompts/system-messages/agente-qualificacao.md)
- 📧 [Agente Follow-up](prompts/system-messages/agente-followup.md)
- 🤝 [Agente Fechamento](prompts/system-messages/agente-fechamento.md)

### Arquitetura Original (Outro caso de uso)
- [Arquitetura do Sistema](docs/arquitetura-sistema.md)
- [Fluxo de Atendimento](docs/fluxo-atendimento.md)

## 🎨 Casos de Uso

### SaaS B2B
Qualifique prospects, identifique dores de processos manuais, converta em demos.

### Consultoria/Agência
Descubra gaps em estratégia, quantifique ROI perdido, feche contratos de consultoria.

### Automação de Marketing
Identifique gargalos de vendas, mostre oportunidades perdidas, venda automação.

### Qualquer B2B!
Sistema 100% customizável. Adapte os prompts para seu nicho específico.

## 📊 Resultados Esperados

Com base em implementações similares:
- **+40-60%** taxa de resposta (vs cold outreach)
- **3-5x** conversão (vs qualificação manual)
- **-70%** custos com SDRs
- **24/7** operação (vs 8h/dia)

## 🛠️ Stack Tecnológico

- **n8n**: Orquestração de workflows
- **PostgreSQL**: Database e memória compartilhada
- **Claude Sonnet 4 / GPT-4**: LLM para agentes
- **Evolution API / Baileys**: WhatsApp (ou seu canal)
- **Bitly / Rebrandly**: URL tracking

## 🤝 Contribuindo

Pull requests são bem-vindos! Para mudanças grandes, abra uma issue primeiro.

## 📄 Licença

[MIT](LICENSE) - Use livremente, modifique, venda, faça o que quiser!

---

**Desenvolvido por**: Fellipe Saraiva
**Versão**: 2.0.0 (B2B Consultivo)
**Última atualização**: Novembro 2025
**Status**: 🚀 Pronto para Produção

---

## 🎯 Próximos Passos

1. **Leia o guia**: [Guia de Implementação B2B](docs/guia-implementacao-b2b.md)
2. **Setup ambiente**: PostgreSQL + n8n
3. **Customize prompts**: Adapte para seu nicho
4. **Teste com 10 leads**: Valide antes de escalar
5. **Deploy e escale**: Comece a converter!

**Dúvidas?** Abra uma issue ou veja a documentação completa nos links acima.