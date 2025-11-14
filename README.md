# Sistema Multi-Agentes para Qualificação Inteligente de Leads

> Transforma conversas de leads em dados qualificados e prontos para vendas.

## O Que Este Sistema Faz

Imagine que cada lead chegando em seu canal de comunicação (WhatsApp, chat, email, etc.) seja automaticamente analisado por agentes especializados que:

✅ **Qualificam leads em segundos** - Classificação automática sem perda de tempo
✅ **Aumenta taxa de conversão** - Roteamento inteligente para o especialista certo
✅ **Reduz custos operacionais** - Automação inteligente substituindo múltiplos SDRs
✅ **Preserva qualidade** - Interações naturais e contextualizadas
✅ **Funciona 24/7** - Qualificação contínua, sem pausas

## O Diferencial

A maioria dos chatbots é genérica. Este sistema é diferente:

- **Multi-especialização**: Cada agente é especialista em seu domínio
- **Roteamento inteligente**: Supervisor decisão qual agente intervém
- **Contexto completo**: Memória de toda a conversa para decisões informadas
- **Flexibilidade**: Prompts centralizados, fáceis de ajustar para qualquer vertical
- **Dados estruturados**: Leads saem do outro lado com informações completas e validadas

## Arquitetura em Camadas

```
Cliente (WhatsApp/Chat/etc)
    ↓
Webhook → Classificação → Buffer (evita fragmentação)
    ↓
Agente Supervisor (Think + Decide)
    ↓
Agentes Especializados (RAG + Tools)
    ↓
Base de Dados (Leads qualificados)
    ↓
Seu time de vendas (prontos para fechar)
```

## Como Implementar

1. **Configure seus agentes** - Defina especialidades via prompts
2. **Estruture sua base de conhecimento** - Informações que os agentes precisam
3. **Integre seu canal** - Connect ao WhatsApp, chat, ou qual for seu canal
4. **Monitore e refine** - Ajuste prompts baseado em performance real

## Componentes Principais

- **Supervisor Agent**: Roteador inteligente baseado em contexto
- **Specialized Agents**: Múltiplos agentes, cada um expert em seu domínio
- **RAG System**: Busca semântica em sua base de conhecimento
- **Database Layer**: PostgreSQL + Vector Store para memória e busca
- **Orchestration**: n8n workflows para orquestração completa

## Documentação Técnica

Quando você estiver pronto para mergulhar nos detalhes:
- [Arquitetura do Sistema](docs/arquitetura-sistema.md)
- [Fluxo de Atendimento](docs/fluxo-atendimento.md)
- [Instalação e Configuração](docs/instalacao-configuracao.md)

---

**Desenvolvido por**: Fellipe Saraiva
**Última atualização**: Novembro 2025
**Status**: Código-aberto | Pronto para Produção

---

## Próximos Passos

- Clone o repositório
- Customize os agentes para seu domínio
- Configure sua base de conhecimento
- Integre com seu canal de comunicação
- Deploy e comece a qualificar leads automaticamente