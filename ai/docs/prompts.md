# Prompts

Os três prompts do laboratório, na ordem do fluxo de trabalho. A ideia é não pedir "o sistema inteiro" de uma vez: primeiro o agente escreve a especificação (arquitetura e arquivos de contexto), ela é revisada, e só então a implementação acontece por etapas a partir desses arquivos.

| Etapa | Resultado | Ferramenta |
| :--- | :--- | :--- |
| 1. Arquitetura | [`arquitetura-sistema.md`](arquitetura-sistema.md) | Chat para montar o prompt; agente de código na IDE para gerar o documento e os diagramas em Mermaid |
| 2. Geração de contexto | [`standards.md`](standards.md), [`architecture.md`](architecture.md), [`tech-stack.md`](tech-stack.md) e [`business-rules.md`](business-rules.md) | Agente de código na IDE |
| 3. Implementação | `ai/backend` e `ai/frontend` | Agente de código na IDE (Antigravity, Cursor e Claude Code) |

---

## 1. Prompt de arquitetura

O briefing nasceu em aula e foi crescendo em rodadas: primeiro o problema e as funcionalidades básicas, depois assinaturas e pagamento, por fim os agentes como diferencial. Abaixo está a versão consolidada.

```text
Quero definir a arquitetura de um sistema antes de implementar qualquer coisa.
Não escreva código agora: o resultado deve ser um documento de arquitetura em
Markdown, com diagramas em Mermaid.

# Sistema
"Sistema de localização de pãozinho quente": um clube de assinatura e entrega
de pão fresco para padarias de bairro.

A lógica do negócio:
- Receita recorrente: em vez de depender de vendas esporádicas, a padaria tem
  um fluxo de caixa previsível com assinaturas.
- Fidelização: uma base de consumidores associados que recebe o produto com
  comodidade na porta de casa.
- Transformar uma mercadoria simples e de consumo diário em um negócio
  estruturado, priorizando inteligência comercial antes de grandes estruturas.

# Problema
- Toda vez que vou à padoca, o pãozinho não está quente.
- Não tenho tempo de ir comprar o pãozinho na padoca.

# Funcionalidades principais
- Cadastro de estabelecimento
- Cronograma de pãozinho quente (fornadas)
- Mapa de padarias próximas
- Cadastro de usuário
- Assinaturas (subscription) -> gateway de pagamento -> cashback
- Sistema de agendamento
- Reserva
- Pagamento na reserva

# Diferenciais (agentes)
- Previsão de demanda (PCP autônomo): analisa as assinaturas ativas e o
  histórico de pedidos avulsos para prever quantos pães assar em cada horário
  e sugerir ajustes nas fornadas.
- Matchmaking dinâmico: para pedidos avulsos, busca a padaria num raio
  otimizado que tenha o produto recém-saído do forno (ou prestes a sair).
- Otimização de rotas de assinatura: recalcula diariamente a melhor rota de
  entrega dos pedidos recorrentes, agrupando vizinhos e bairros próximos.
- Retenção e upsell: avisa o cliente de forma contextual (ex.: "Notei que você
  costuma pedir pão de queijo às sextas. A Padaria X acabou de tirar uma
  fornada, quer adicionar à sua entrega de hoje?").

# Tipos de usuários e permissões
- Estabelecimento
  - ADM
  - Operador
- Consumidor
  - Visualização, reserva e assinatura

# Camadas
FRONT -> API -> DB

# O que o documento precisa ter
1. Funcionalidades principais
2. Tipos de usuários e matriz de permissões
3. Diagrama de arquitetura em camadas (frontend, backend, banco de dados)
4. Entidades principais e relacionamentos (diagrama ER)
5. Endpoints da API (principais rotas REST)
6. Tecnologias sugeridas
7. Fluxos principais (diagramas de sequência ou fluxogramas)

É um MVP acadêmico: prefira a solução mais simples que funcione localmente.
Onde houver mais de um caminho, registre a decisão e o motivo. Se alguma
informação estiver faltando, pergunte antes de assumir.
```

---

## 2. Prompt de geração de contexto

Para ser enviado no mesmo chat da arquitetura, depois da revisão do documento. O objetivo é transformar a arquitetura em arquivos curtos que qualquer agente consiga ler antes de escrever código.

```text
Com base na arquitetura que acabamos de definir, gere os arquivos de contexto
do projeto. Eles serão lidos por agentes de IA antes de qualquer implementação,
então escreva de forma direta, sem repetir o mesmo conteúdo em dois arquivos.

Crie esta estrutura:

docs/
├── standards.md        # Convenções de código e estilo
├── architecture.md     # Decisões de alto nível (ADRs)
├── tech-stack.md       # Versões e libs permitidas
└── business-rules.md   # Lógica de negócio e domínio

O que vai em cada arquivo:

- standards.md: estrutura de pastas do repositório, regras de lint e
  formatação, nomenclatura (variáveis, componentes e arquivos) e padrão de
  commits.
- architecture.md: um ADR por decisão, sempre com Contexto, Decisão e
  Consequência. Comece pelas decisões já tomadas: frontend e backend
  separados, demonstração 100% local e notificações simuladas.
- tech-stack.md: somente as tecnologias homologadas, com versão mínima e o
  motivo da escolha. O que não estiver na lista não deve ser usado sem
  perguntar.
- business-rules.md: regras numeradas por domínio (BR-1.1, BR-1.2...), cada
  uma verificável: o que é obrigatório, valores permitidos, estados e
  transições, cálculos.

Restrições do MVP:
- Roda localmente, sem serviços pagos nem deploy em nuvem.
- Banco em arquivo local.
- Integrações externas (pagamento, WhatsApp) são simuladas.

Não escreva código de aplicação nesta etapa. Ao final, liste as dúvidas ou
suposições que você fez para eu confirmar.
```

---

## 3. Prompt de implementação

Para ser executado por um agente dentro do repositório, uma etapa por vez: o prompt é o mesmo, mudando apenas a etapa pedida na última linha.

```text
Você vai implementar o MVP do Clube do Pão neste repositório.

Antes de escrever qualquer código, leia nesta ordem:
1. docs/business-rules.md
2. docs/architecture.md
3. docs/tech-stack.md
4. docs/standards.md
5. docs/prd.md e docs/arquitetura-sistema.md

Esses arquivos são a fonte da verdade. Se o que eu pedir conflitar com eles,
pare e me avise em vez de escolher sozinho.

Regras de trabalho:
- Implemente uma etapa por vez e pare ao final de cada uma para revisão.
- Use somente as tecnologias de tech-stack.md. Precisa de outra biblioteca?
  Pergunte antes e, se aprovada, atualize o tech-stack.md.
- Siga a estrutura e a nomenclatura de standards.md. Backend em camadas:
  routes -> controllers -> repositories (e services quando houver regra que
  não é de HTTP nem de SQL).
- Dados mockados: pagamento e notificações são simulados, e deve existir um
  script de seed com dados de demonstração.
- Tudo precisa rodar localmente com "npm install" e "npm run dev".
- Não quebre o que já funciona: antes de alterar uma tela ou rota existente,
  confira como ela é usada.
- Toda regra implementada deve corresponder a uma regra de business-rules.md.
  Se a regra não existir lá, adicione-a no documento junto com o código.
- Ao terminar a etapa, rode o lint e os testes e me mostre o resultado real,
  incluindo o que falhou.

Etapas:
1. Base: estrutura de frontend e backend, banco SQLite, cadastro do cliente
   (nome, endereço e WhatsApp) com assinatura ativa na hora, painel da padaria
   com o total de clientes e despacho com notificação simulada.
2. Logística: quantidade de pães e faixa de horário no cadastro, total de
   pães a produzir no painel e rota ordenada por horário.
3. Marketplace: contas com os três papéis e login, cadastro de padarias e
   produtos, cronograma de fornadas com status, mapa de padarias próximas,
   reserva com pagamento simulado e assinaturas por plano com cashback.
4. Agentes: previsão de demanda, matchmaking, otimização de rotas e retenção,
   com regras determinísticas e registro de cada execução, mantendo entrada e
   saída estáveis para uma futura troca por LLM.
5. Qualidade: testes de API e de frontend para os fluxos principais e
   documentação atualizada (README, PRD e ADRs).

Ao final de cada etapa, me entregue: o que foi feito, como testar manualmente,
o que ficou de fora e as decisões que você tomou sem estar nos arquivos de
contexto.

Comece pela etapa 1.
```

---

## Sobre o uso do agente

- **Especificação antes do código:** pedir a arquitetura e os arquivos de contexto primeiro evita que o agente escolha a stack e as regras por conta própria a cada conversa.
- **Dificuldade principal:** juntar duas bases feitas em paralelo, com stacks diferentes, e uma refatoração em andamento. Os arquivos de contexto foram o que permitiu ao agente reescrever uma delas no padrão da outra.
