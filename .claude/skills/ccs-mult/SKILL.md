---
name: ccs-mult
description: "CCS - Mult: índice/roteador central para as capacidades multi-agente instaladas neste projeto (cartographer, claude-mem, headroom, ponytail, strix, OmniRoute, agent-skills). Não substitui essas skills nem duplica o conteúdo delas — decide QUAL delas usar para cada pedido, evitando sobreposição e escolhas erradas. Consulte esta skill sempre que o pedido do usuário for ambíguo entre memória/contexto, mapeamento de código, estilo de implementação (enxuto vs. rigoroso), segurança ofensiva vs. defensiva, ou roteamento de provedores de IA — ou sempre que o usuário disser 'CCS', 'CCS-Mult', 'qual skill eu uso pra isso', ou pedir para combinar mais de uma dessas ferramentas no mesmo fluxo."
metadata:
  author: user
  version: "1.0.0"
---

# CCS - Mult

Roteador central para 7 conjuntos de capacidades instalados neste projeto. Cada um resolve um problema diferente; esta skill existe só para apontar qual usar — e avisar quando dois deles puxam em direções opostas — para eles trabalharem em sintonia em vez de se atropelar.

**Regra de ouro: nunca copie ou reescreva o conteúdo das skills abaixo aqui dentro.** Sempre invoque a skill/plugin correspondente (via Skill tool, pelo nome exato listado) ou rode o comando dela diretamente. Se uma referência aqui ficar desatualizada, corrija a tabela — não duplique a lógica.

## Mapa de decisão

| Se o pedido for sobre... | Use | Como |
|---|---|---|
| Entender a arquitetura de um repo, gerar `CODEBASE_MAP.md`, onboarding em código desconhecido | **cartographer** | Skill `cartographer` (plugin `cartographer@cartographer-marketplace`) |
| Lembrar de decisões/trabalho de sessões passadas, buscar histórico, retomar um projeto, "o que eu fiz semana passada" | **claude-mem** | Skills do plugin `claude-mem@thedotmack`: `mem-search` (busca), `learn-codebase`, `knowledge-agent`, `make-plan`, `standup`, `timeline-report`, `weekly-digests`, `pathfinder`, `smart-explore`, `babysit`, `cloud-sync`, `version-bump`, `wowerpoint`, `oh-my-issues`, `what-the`, `mode-creator`, `design-is`, `do`, `how-it-works` |
| Sessão ficando lenta / prompt gigante / preocupação com custo de contexto | **headroom** | Não tem skill própria — atua sozinho via hooks (`SessionStart`, `PreToolUse`) assim que o plugin está habilitado. Não precisa invocar nada, só confirmar que `headroom@headroom-marketplace` está `enabled` (`claude plugin list`) |
| Pedido explícito de solução mínima/enxuta, "faz o mais simples", "yagni", "modo preguiçoso" | **ponytail** | Skills do plugin `ponytail@ponytail`: `ponytail` (aplica o modo), `ponytail-review`, `ponytail-audit`, `ponytail-debt`, `ponytail-gain`, `ponytail-help` |
| Testar/explorar vulnerabilidades de forma ativa em uma aplicação (pentest, OWASP Top 10, exploração) | **strix** | Skills soltas em `.claude/skills/`: `penetration-testing-with-strix`, `web-app-penetration-testing`, `api-security-testing`, `application-security-testing`, `owasp-top-10-testing`, `find-security-vulnerabilities-in-code`, `fix-security-vulnerabilities-with-strix`, `managed-pentesting-with-strix`, `ci-security-scanning-with-strix`. **Só use com autorização explícita do dono do sistema-alvo** — é teste ofensivo real |
| Auditar/proteger o PRÓPRIO código contra falhas comuns (input handling, auth, OWASP) sem atacar nada | **agent-skills: security-and-hardening** | Não confundir com strix — isso é revisão defensiva, não teste ofensivo |
| Rotear chamadas de IA entre múltiplos provedores, cache, custo/uso de API, MCP, webhooks de um gateway OmniRoute | **OmniRoute** | Skills soltas prefixadas `omniroute-*` em `.claude/skills/` (ex.: `omniroute-cli-routing`, `omniroute-omni-providers`, `omniroute-omni-budget`). Só fazem sentido se o projeto já roda o serviço OmniRoute |
| Qualquer etapa do ciclo de vida de engenharia — spec, planejamento, TDD, code review, CI/CD, performance, lançamento, ADRs, debugging | **agent-skills** (Addy Osmani) | 24 skills soltas em `.claude/skills/`, ex.: `spec-driven-development`, `planning-and-task-breakdown`, `test-driven-development`, `code-review-and-quality`, `debugging-and-error-recovery`, `shipping-and-launch`, `frontend-ui-engineering`. Comece por `using-agent-skills` se não souber qual das 24 encaixa |

## Quando duas dessas capacidades competem

- **ponytail vs. agent-skills**: ponytail empurra pro código mais curto/mínimo possível; várias skills de `agent-skills` (`constraint-driven-development`, `incremental-implementation`, `test-driven-development`) empurram pro processo mais rigoroso. Não ative os dois modos ao mesmo tempo sem avisar o usuário — pergunte qual prioridade vale mais para aquela tarefa específica (velocidade/minimalismo vs. robustez), a menos que o usuário já tenha deixado claro que quer o modo ponytail para tudo.
- **strix vs. security-and-hardening**: strix ataca de fora pra achar brecha; security-and-hardening revisa de dentro antes de existir brecha. Um pedido de "revisa esse código pra ver se tá seguro" é `security-and-hardening`; "tenta invadir/quebrar esse sistema" (com autorização) é `strix`.
- **claude-mem vs. cartographer**: claude-mem lembra do que já aconteceu (histórico, decisões, conversas); cartographer entende o que já existe (estrutura atual do código). "O que decidimos sobre X mês passado" → claude-mem. "Como esse repo é organizado" → cartographer.

## Pedido simples de uma coisa só

Se o pedido já é claramente de um único domínio (ex.: "busca na memória o que conversamos sobre X" ou "mapeia esse repositório"), pule a tabela e invoque a skill direto — este roteador é para os casos ambíguos ou que cruzam mais de um domínio.

## Inventário instalado neste projeto

| Fonte | Tipo de instalação | Nome para `claude plugin` |
|---|---|---|
| cartographer | plugin (project scope) | `cartographer@cartographer-marketplace` |
| claude-mem | plugin (project scope) | `claude-mem@thedotmack` |
| headroom | plugin (project scope, só hooks) | `headroom@headroom-marketplace` |
| ponytail | plugin (project scope) | `ponytail@ponytail` |
| strix | skills soltas (sem empacotamento de plugin no upstream) | — |
| OmniRoute | skills soltas, prefixo `omniroute-` (sem empacotamento de plugin no upstream) | — |
| agent-skills | skills soltas (empacotamento via GitHub exigia SSH não configurado nesta máquina) | — |

Para atualizar os 4 instalados como plugin: `claude plugin update <nome>@<marketplace>`. Para os 3 copiados como skills soltas, é preciso reclonar o repositório upstream e copiar `skills/` de novo — não há comando de update automático para eles.
