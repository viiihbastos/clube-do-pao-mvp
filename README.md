# Clube do Pão

MVP de assinatura e entrega de pão quente para padarias de bairro, com marketplace de fornadas e agentes de IA. Trabalho prático de AI-Driven Development (MBA).

O cliente assina o Kit Pão Quente ou reserva uma fornada pelo mapa; a padaria enxerga a demanda do dia, agenda fornadas e conta com agentes de previsão de demanda, matchmaking, rotas e retenção.

## Entregáveis

| Entregável | Onde está |
| :--- | :--- |
| Documento de arquitetura | [`ai/docs/arquitetura-sistema.md`](ai/docs/arquitetura-sistema.md) (camadas, permissões, entidades, endpoints e fluxos, com diagramas Mermaid) |
| Arquivos de contexto | [`ai/docs/`](ai/docs/): [`standards.md`](ai/docs/standards.md), [`architecture.md`](ai/docs/architecture.md), [`tech-stack.md`](ai/docs/tech-stack.md) e [`business-rules.md`](ai/docs/business-rules.md) |
| Prompts | [`ai/docs/prompts.md`](ai/docs/prompts.md) (arquitetura, geração de contexto e implementação) |
| Implementação | [`ai/backend`](ai/backend) (API Node.js + Express + SQLite) e [`ai/frontend`](ai/frontend) (React + Vite + Tailwind) |
| Vídeo de demonstração | https://youtu.be/fV50i3FiENY (YouTube, não listado, 3 min 22 s). Cópia do arquivo em [`video/demo-clube-do-pao.mp4`](video/demo-clube-do-pao.mp4) |
| Relatório de refatoração | [`ai/docs/relatorio-modernizacao.pdf`](ai/docs/relatorio-modernizacao.pdf) |

O índice completo da documentação, incluindo o PRD, está em [`ai/docs/README.md`](ai/docs/README.md).

## Como rodar

Requisito: Node.js 20 LTS ou superior.

```bash
cd ai
npm install
cp backend/.env.example backend/.env
npm run seed
npm run dev
```

No Windows, troque o `cp` por `copy backend\.env.example backend\.env`.

Abra `http://localhost:5173`. A API sobe em `http://localhost:3000`. Tudo roda localmente, com dados de demonstração; só o mapa precisa de internet para carregar as imagens de fundo.

`npm run seed` cria padarias, fornadas e as contas abaixo. Rode de novo antes de cada demonstração, porque as fornadas são criadas em relação ao horário atual.

| Papel | Email | Senha |
| :--- | :--- | :--- |
| Consumidor | maria@email.com | 123456 |
| Admin da padaria | joao@padaria.com | 123456 |
| Operador | operador@padaria.com | 123456 |

## O que dá para ver

| Tela | O que faz |
| :--- | :--- |
| `/` | Página inicial com os atalhos |
| `/cardapio` | Cardápio ilustrado dos pães |
| `/cliente` | Assinatura simples do Kit Pão Quente (quantidade e faixa de horário) |
| `/padaria` | Demanda do dia, rota ordenada por horário e despacho |
| `/mapa` | Padarias próximas e matchmaking de pão quente |
| `/fornadas` | Cronograma de fornadas e reserva com pagamento simulado |
| `/reservas`, `/assinaturas` | Reservas feitas, planos e cashback (consumidor) |
| `/operacao` | Agendar fornadas, mudar status e executar os agentes de IA (admin e operador) |
| `/sobre` | PRD, especificações técnicas e equipe |

## Testes

```bash
cd ai
npm test
npm run lint
```

## Equipe

| Integrante | RM |
| :--- | :--- |
| Danilo Sebastiany França | RM378444 |
| Kevin Peterson Coelho | RM379085 |
| Marcos Vinicius Souza Lima | RM377592 |
| Victor Bastos dos Santos | RM377088 |

Mais detalhes de execução e a lista de rotas da API estão em [`ai/README.md`](ai/README.md).
