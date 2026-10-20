# Índice de skills da fundação de desenvolvimento web

> Repositório: [`rafavlad/BASE_CODIGO`](https://github.com/rafavlad/BASE_CODIGO). A fonte normativa deste pacote fica em `CLAUDE.md` e `.claude/skills/`. Use os links abaixo para consultar as skills; não mantenha cópias divergentes na raiz.

## Como funciona

- O `CLAUDE.md` da raiz contém as regras sempre ativas e o mapa de skills obrigatórias por fase. Ele funciona como roteador deste repositório de fundação, **não como um `CLAUDE.md` pronto para copiar sem adaptação a projetos de clientes**.
- Cada skill está em `.claude/skills/<nome>/SKILL.md` e possui um `name` e uma `description` no frontmatter. As skills são unidades temáticas que podem ser carregadas separadamente, mas algumas dependem de outras skills; siga a matriz do `CLAUDE.md` e as referências explícitas.
- Os números de subseções dentro dos arquivos são identificadores internos de referência. Não exigem nem apontam para um documento mestre externo.
- `docs/MENSAGENS-DE-SESSAO.md` contém mensagens de preparação/retomada e lembretes de autorização. Não substitui o `CLAUDE.md` nem as skills normativas.
- `docs/REGISTRO-ALTERACOES.md` é o único histórico técnico cumulativo do pacote. Novas entradas são acrescentadas ao final sem apagar as antigas; não deve conter segredos, logs extensos, diffs completos ou snapshots.
- `docs/RELATORIO-DE-VALIDACAO.md` registra verificações estruturais realizadas e limitações. Uma validação documental não prova que o comportamento já foi testado numa sessão real.
- `MANIFEST.json` lista tamanhos e hashes SHA-256 dos arquivos distribuídos, sem incluir o próprio manifesto.

## Skills e quando carregá-las

| Skill | Responsabilidade | Quando carregar |
|---|---|---|
| [`principios-globais`](../.claude/skills/principios-globais/SKILL.md) | Hierarquia de prioridades, escopo, integridade, configuração de caminhos locais e referências por tarefa. | Início de toda tarefa. |
| [`fases-e-autorizacoes`](../.claude/skills/fases-e-autorizacoes/SKILL.md) | Fases, autorizações humanas, estados, portões de saída, relatórios e diagnóstico inicial. | Toda fase. |
| [`seguranca-permissoes`](../.claude/skills/seguranca-permissoes/SKILL.md) | Arquivos de ambiente protegidos, permissões, limites de isolamento e canário. | Toda fase; especialmente antes de permissões e da Fase 1B. |
| [`arquitetura-stack`](../.claude/skills/arquitetura-stack/SKILL.md) | Estrutura do projeto, tecnologias, TypeScript, testes, lint, scripts npm e `.gitignore`. | Fases 0, 1A e 1B; alterações estruturais. |
| [`auditoria-dependencias`](../.claude/skills/auditoria-dependencias/SKILL.md) | Auditoria npm, classificação de resultados, CLI, timeouts, agregação e testes. | Fases 0, 1A e 1B; manutenção da auditoria. |
| [`ci-dependabot`](../.claude/skills/ci-dependabot/SKILL.md) | GitHub Actions, matriz de sistemas, segurança e Dependabot. | Planejamento na Fase 0; configuração na Fase 1A/1B. |
| [`interface-cliente-referencias`](../.claude/skills/interface-cliente-referencias/SKILL.md) | Separação núcleo/cliente, componentes, páginas, formulário e análise segura de referências. | Planejamento na Fase 0; separação na Fase 1A; interface na Fase 1B. |
| [`qualidade-acessibilidade`](../.claude/skills/qualidade-acessibilidade/SKILL.md) | Testes, regressão, acessibilidade, responsividade, revisão de diff e verificação final. | Planejamento na Fase 0; Fases 1A/1B e conclusão de fases. |
| [`modulos-opcionais`](../.claude/skills/modulos-opcionais/SKILL.md) | Requisitos e limites de backend, banco, autenticação, uploads, agendamentos e integrações. | Fase 2 para documentar módulos futuros; implementação somente com autorização própria. |
| [`git-integridade`](../.claude/skills/git-integridade/SKILL.md) | Preservação do repositório, identidade Git, commits e proibições de ações remotas. | Toda fase; antes de ações Git com efeitos colaterais. |
| [`documentacao-claude-md`](../.claude/skills/documentacao-claude-md/SKILL.md) | Documentação obrigatória, `CLAUDE.md` por projeto de cliente e registro cumulativo de alterações. | Fases 1A, 1B e 2; sempre que criar/revisar documentação. |
| [`continuidade-sessoes`](../.claude/skills/continuidade-sessoes/SKILL.md) | Resumos externos, retomada segura e tratamento de sessões interrompidas. | Toda troca, pausa, encerramento ou retomada de sessão. |
| [`aceitacao-final`](../.claude/skills/aceitacao-final/SKILL.md) | Checklist e critérios de aceitação por fase. | Fases 0, 1A, 1B e 2. |

## Matriz resumida de carregamento

As cinco skills globais obrigatórias para toda fase são `principios-globais`, `fases-e-autorizacoes`, `seguranca-permissoes`, `git-integridade` e `continuidade-sessoes`. Além delas, leia as skills temáticas indicadas na tabela a seguir. Se houver dúvida sobre a cobertura de um requisito, leia a skill pertinente em vez de presumir.

| Fase | Skills temáticas adicionais | Limite da fase |
|---|---|---|
| **0 — inspeção** | `arquitetura-stack`, `auditoria-dependencias`, `ci-dependabot`, `interface-cliente-referencias`, `qualidade-acessibilidade`, `aceitacao-final` | Leitura/planejamento somente; não criar nem alterar arquivos do projeto. |
| **1A — fundação técnica** | `arquitetura-stack`, `auditoria-dependencias`, `ci-dependabot`, `interface-cliente-referencias`, `qualidade-acessibilidade`, `documentacao-claude-md`, `aceitacao-final` | A skill de interface define a separação entre núcleo e cliente; não antecipa a implementação da interface. |
| **1B — interface** | `arquitetura-stack`, `auditoria-dependencias`, `interface-cliente-referencias`, `ci-dependabot`, `qualidade-acessibilidade`, `documentacao-claude-md`, `aceitacao-final` | Confirmar `/permissions` e concluir o canário antes de operações dependentes. |
| **2 — documentação** | `documentacao-claude-md`, `modulos-opcionais`, `aceitacao-final` | `modulos-opcionais` serve para documentar `docs/MODULES.md`; não autoriza implementar módulos. |
| **Módulo opcional** | `modulos-opcionais` e skills técnicas correspondentes | Exige escopo e autorização próprios. |

## Uso em projetos de clientes

O `CLAUDE.md` deste repositório é um roteador para a própria fundação. Em cada projeto de cliente, crie um `CLAUDE.md` específico, com os comandos, a stack, a estrutura, as decisões e as regras permanentes daquele projeto, seguindo `documentacao-claude-md`. Copie as skills relevantes sem duplicá-las em caminhos alternativos e mantenha personalizações do cliente fora da base genérica.

## Links diretos para leitura no chat

Se uma interface de chat esconder pastas iniciadas por ponto, use links Raw da branch `main` no README do repositório. Isso é um recurso de leitura de referência; não equivale a execução local do Claude Code nem concede acesso de escrita ao GitHub.
