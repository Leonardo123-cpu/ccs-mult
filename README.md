# CCS - Mult

Skill/roteador para Claude Code que decide **qual** das capacidades multi-agente instaladas neste projeto usar para cada pedido — sem duplicar o conteúdo delas.

## O que ela resolve

Este projeto tem 7 fontes de capacidades diferentes instaladas (memória, mapeamento de código, estilo de implementação, segurança, roteamento de IA, ciclo de vida de engenharia). Cada uma resolve um problema diferente, e várias delas se sobrepõem ou até competem entre si. A `ccs-mult` funciona como um índice: olha o pedido do usuário e aponta pra skill certa, evitando que o Claude escolha a ferramenta errada ou tente fazer tudo sozinho.

Ela não substitui nenhuma das skills abaixo e não copia o conteúdo delas — só direciona.

## O que está instalado

| Fonte | O que faz | Como está instalado |
|---|---|---|
| **cartographer** | Mapeia a arquitetura de um repositório (`docs/CODEBASE_MAP.md`) usando subagentes paralelos | Plugin (`cartographer@cartographer-marketplace`) |
| **claude-mem** | Memória persistente entre sessões: busca, histórico de decisões, retomada de projetos | Plugin (`claude-mem@thedotmack`), 19 skills |
| **headroom** | Comprime o contexto enviado ao modelo automaticamente (hooks, sem skill própria) | Plugin (`headroom@headroom-marketplace`) |
| **ponytail** | Força a solução mais simples/enxuta possível (YAGNI, stdlib primeiro) | Plugin (`ponytail@ponytail`), 6 skills |
| **strix** | Pentest ofensivo real (OWASP Top 10, APIs, apps web) — **exige autorização do dono do sistema-alvo** | Skills soltas em `.claude/skills/` |
| **OmniRoute** | Gateway/roteador de múltiplos provedores de IA (custo, cache, MCP) | Skills soltas, prefixo `omniroute-*` |
| **agent-skills** (Addy Osmani) | Ciclo de vida de engenharia: spec, planejamento, TDD, code review, CI/CD, lançamento | Skills soltas, 24 skills |

Ver a tabela de decisão completa e os casos de conflito entre skills em [`.claude/skills/ccs-mult/SKILL.md`](.claude/skills/ccs-mult/SKILL.md).

## Como funciona na prática

- **Plugins** (cartographer, claude-mem, headroom, ponytail) ficam habilitados automaticamente a cada sessão do Claude Code aberta neste projeto — confirme com `claude plugin list`.
- **Skills soltas** (ccs-mult, strix, OmniRoute, agent-skills) ficam disponíveis assim que o Claude Code lê a pasta `.claude/skills/` do projeto — não precisam de instalação separada.
- A `ccs-mult` é acionada automaticamente quando o pedido é ambíguo entre domínios, ou manualmente ao mencionar "CCS" / "CCS-Mult" na conversa.

## Atualizar

- Plugins: `claude plugin update <nome>@<marketplace>`
- Skills soltas (strix, OmniRoute, agent-skills): reclonar o repositório upstream e copiar a pasta `skills/` de novo — não há update automático para elas.
