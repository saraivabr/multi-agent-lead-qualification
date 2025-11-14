# Sistema Multi-Agentes Saraiva Holding

> Sistema completo de atendimento inteligente para as startups da Saraiva Holding: ligacao.ai e escreve.ai. Qualifica, nutre e converte leads B2B automaticamente.

## 🎯 O Que Este Sistema Faz

Este sistema atende automaticamente leads interessados nas startups da Saraiva Holding através de agentes especializados que:

✅ **Atendem 24/7** via WhatsApp com respostas instantâneas
✅ **Qualificam leads** identificando interesse em ligacao.ai ou escreve.ai
✅ **Respondem dúvidas** sobre funcionalidades, preços e casos de uso
✅ **Agendam demonstrações** com especialistas no momento certo
✅ **Nutrem leads** com follow-ups inteligentes e personalizados
✅ **Convertem naturalmente** sem pressão ou abordagem agressiva

**Resultado:** Atendimento consultivo 24/7 que qualifica e converte leads automaticamente.

## 🚀 O Diferencial

A maioria dos chatbots apenas responde perguntas. Este sistema **atende consultivamente**:

- **Atendimento multi-produto**: Qualifica interesse em ligacao.ai ou escreve.ai
- **Consultivo, não robótico**: Conversa natural que identifica necessidades reais
- **Agentes especializados**: Cada startup tem seu agente com conhecimento específico
- **Transições inteligentes**: Direciona para demonstração no momento certo
- **Memória compartilhada**: Contexto preservado entre diferentes agentes
- **100% adaptável**: Fácil adicionar novos produtos/startups ao sistema

## 🏗️ Arquitetura - Sistema Multi-Agentes

```
Lead entra via WhatsApp
    ↓
┌─────────────────────────┐
│  Agente Supervisor     │ ← Analisa e roteia
└─────────────────────────┘
    ↓
    ├─→ Nova conversa → Agente Geral
    │                    ├─→ Apresenta Saraiva Holding
    │                    └─→ Identifica interesse
    │
    ├─→ Interesse ligacao.ai → Agente ligacao.ai
    │                           ├─→ Responde dúvidas
    │                           ├─→ Demonstra funcionalidades
    │                           └─→ Agenda demonstração
    │
    └─→ Interesse escreve.ai → Agente escreve.ai
                                ├─→ Responde dúvidas
                                ├─→ Mostra exemplos
                                └─→ Agenda demonstração

Memória PostgreSQL compartilhada
Contexto preservado entre agentes
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

## 🤖 Os Agentes Especializados

### 1. Agente Supervisor
- **Função**: Rotear mensagens para o agente correto
- **Técnica**: Análise de contexto e intent detection
- **Output**: Direcionamento inteligente
- **Prompt**: [`prompts/system-messages/agente-supervisor.md`](prompts/system-messages/agente-supervisor.md)

### 2. Agente Geral
- **Função**: Atendimento inicial e apresentação da Saraiva Holding
- **Técnica**: Coleta de informações e identificação de interesse
- **Output**: Lead qualificado para ligacao.ai ou escreve.ai
- **Prompt**: [`prompts/system-messages/agente-geral.md`](prompts/system-messages/agente-geral.md)

### 3. Agente ligacao.ai
- **Função**: Especialista em automação de vendas e discador
- **Técnica**: Atendimento consultivo sobre ligacao.ai
- **Output**: Lead educado e pronto para demonstração
- **Prompt**: [`prompts/system-messages/agente-ligacao.md`](prompts/system-messages/agente-ligacao.md)

### 4. Agente escreve.ai
- **Função**: Especialista em geração de conteúdo com IA
- **Técnica**: Atendimento consultivo sobre escreve.ai
- **Output**: Lead educado e pronto para demonstração
- **Prompt**: [`prompts/system-messages/agente-escreve.md`](prompts/system-messages/agente-escreve.md)

## 📚 Documentação Completa

### Prompts dos Agentes
- 🎯 [Agente Supervisor](prompts/system-messages/agente-supervisor.md)
- 👋 [Agente Geral](prompts/system-messages/agente-geral.md)
- 📞 [Agente ligacao.ai](prompts/system-messages/agente-ligacao.md)
- ✍️ [Agente escreve.ai](prompts/system-messages/agente-escreve.md)

### Arquitetura e Configuração
- [Workflows n8n](workflows/)
- [Tools e Integrações](prompts/tools/)

## 🎨 Produtos Atendidos

### ligacao.ai
Startup de automação de vendas com:
- Discador automático inteligente
- CRM integrado
- Gestão de campanhas de vendas
- Relatórios e analytics

### escreve.ai
Startup de geração de conteúdo com IA:
- Copywriting para redes sociais
- Blog posts e artigos
- E-mails marketing
- Landing pages e anúncios

### Fácil Expansão
Sistema preparado para adicionar novos produtos da holding facilmente.

## 📊 Benefícios

Sistema de atendimento inteligente que oferece:
- **24/7** disponibilidade sem custo adicional
- **Respostas instantâneas** para todos os leads
- **Qualificação automática** antes do contato humano
- **Contexto preservado** entre diferentes agentes
- **Escalável** para múltiplos produtos/startups

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

**Desenvolvido para**: Saraiva Holding
**Versão**: 3.0.0 (Multi-Startups)
**Última atualização**: Janeiro 2025
**Status**: 🚀 Em Produção

---

## 🎯 Próximos Passos

1. **Configure os agentes**: Revise os prompts em `/prompts/system-messages/`
2. **Setup workflows**: Importe os workflows do n8n
3. **Configure RAG**: Adicione conteúdo sobre ligacao.ai e escreve.ai
4. **Teste**: Valide o fluxo completo antes de lançar
5. **Deploy**: Conecte ao WhatsApp e comece a atender!

**Dúvidas?** Entre em contato com a equipe Saraiva Holding.