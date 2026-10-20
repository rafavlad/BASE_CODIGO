# Índice do pacote de skills — Fundação web v1.31

> Repositório de referência: [`rafavlad/BASE_CODIGO`](https://github.com/rafavlad/BASE_CODIGO). As skills ficam em `.claude/skills/`; não as mova para `skills/` na raiz, pois isso quebra o caminho canônico de descoberta do Claude Code. Para abrir arquivos em interfaces que ocultam pastas iniciadas por ponto, use os links diretos do README.


## Como funciona

- `CLAUDE.md` contém as regras globais que devem permanecer sempre ativas, incluindo a obrigação de manter um único histórico cumulativo.
- Cada skill reside em `.claude/skills/<nome>/SKILL.md`, o formato reconhecido pelo Claude Code. Os links abaixo são relativos à raiz do repositório e também abrem no GitHub quando os arquivos estão na branch `main`.
- `docs/REGISTRO-ALTERACOES.md` é o único arquivo de histórico técnico cumulativo: as entradas são adicionadas ao final, sem sobrescrever as anteriores nem criar um Markdown novo para cada mudança. O arquivo não deve conter retratos brutos do repositório, diffs extensos ou dados sensíveis.
- O corpo de cada skill conserva os requisitos detalhados e numerados do Prompt Oficial v1.31, exceto a seção de continuidade, adaptada para não contradizer a arquitetura modular.
- As referências `seção X` foram preservadas para rastreabilidade com a numeração original. Consulte o mapeamento abaixo quando uma seção fizer referência a conteúdo localizado em outro arquivo.
- Skills não são todas carregadas automaticamente no contexto. `CLAUDE.md` estabelece a matriz de leitura obrigatória; siga-a antes de executar cada fase.

## Skills e escopo

| Skill / comando | Conteúdo original | Quando carregar |
| --- | --- | --- |
| [`principios-globais`](../.claude/skills/principios-globais/SKILL.md) | Introdução, configuração de continuidade e referências, seção 1 | Início de toda tarefa; estabelece prioridade e limites globais. |
| [`fases-e-autorizacoes`](../.claude/skills/fases-e-autorizacoes/SKILL.md) | Seção 2, seção 17 e instrução final | Toda fase; autorizações, portões, relatório e primeira ação. |
| [`seguranca-permissoes`](../.claude/skills/seguranca-permissoes/SKILL.md) | Seção 3 | Sempre; obrigatória antes de permissões, ambiente e canário. |
| [`arquitetura-stack`](../.claude/skills/arquitetura-stack/SKILL.md) | Seções 4 e 5 | Fases 0, 1A, 1B ou alterações estruturais da base. |
| [`auditoria-dependencias`](../.claude/skills/auditoria-dependencias/SKILL.md) | Seção 6 | Fase 1A/1B, `scripts/audit.mjs` ou npm audit. |
| [`ci-dependabot`](../.claude/skills/ci-dependabot/SKILL.md) | Seção 7 | Fase 1A e manutenção do workflow/Dependabot. |
| [`interface-cliente-referencias`](../.claude/skills/interface-cliente-referencias/SKILL.md) | Seção 8, incluindo 8.6 | Fase 1B, interface e análise autorizada de referências. |
| [`qualidade-acessibilidade`](../.claude/skills/qualidade-acessibilidade/SKILL.md) | Seções 9 e 10 | Implementação e validação; antes de concluir fases. |
| [`modulos-opcionais`](../.claude/skills/modulos-opcionais/SKILL.md) | Seção 11 | Somente quando um módulo opcional for explicitamente considerado/autorizado. |
| [`git-integridade`](../.claude/skills/git-integridade/SKILL.md) | Seção 12 | Antes de qualquer operação Git com efeitos colaterais. |
| [`documentacao-claude-md`](../.claude/skills/documentacao-claude-md/SKILL.md) | Seções 13–14, mais a política de registro cumulativo | Criar/revisar documentação, o `CLAUDE.md` e `docs/REGISTRO-ALTERACOES.md`. |
| [`continuidade-sessoes`](../.claude/skills/continuidade-sessoes/SKILL.md) | Seção 15 adaptada | Toda troca, pausa, encerramento ou retomada de sessão. |
| [`aceitacao-final`](../.claude/skills/aceitacao-final/SKILL.md) | Seção 16 | Antes de avaliar ou declarar aceitação de uma fase. |

## Matriz mínima por fase

| Fase | Skills obrigatórias além das regras sempre ativas de `CLAUDE.md` |
| --- | --- |
| 0 | `arquitetura-stack`, `aceitacao-final`; consultas de leitura somente, sem mutações no projeto. |
| 1A | `arquitetura-stack`, `auditoria-dependencias`, `ci-dependabot`, `documentacao-claude-md`, `qualidade-acessibilidade`, `aceitacao-final`. |
| 1B | `arquitetura-stack`, `auditoria-dependencias`, `interface-cliente-referencias`, `ci-dependabot`, `qualidade-acessibilidade`, `documentacao-claude-md`, `aceitacao-final`; confirmação manual de permissões e canário primeiro. |
| 2 | `documentacao-claude-md`, `aceitacao-final`; sem implementação de módulos opcionais sem autorização específica. |
| Retomada | `continuidade-sessoes` mais as skills da fase que já estava autorizada; revalidar ambiente e autorização. |

## Mapeamento das seções originais

- Instruções iniciais/configurações e seção 1 → [`.claude/skills/principios-globais/SKILL.md`](../.claude/skills/principios-globais/SKILL.md)
- Seção 2, seção 17 e instrução final → [`.claude/skills/fases-e-autorizacoes/SKILL.md`](../.claude/skills/fases-e-autorizacoes/SKILL.md)
- Seção 3 → [`.claude/skills/seguranca-permissoes/SKILL.md`](../.claude/skills/seguranca-permissoes/SKILL.md)
- Seções 4–5 → [`.claude/skills/arquitetura-stack/SKILL.md`](../.claude/skills/arquitetura-stack/SKILL.md)
- Seção 6 → [`.claude/skills/auditoria-dependencias/SKILL.md`](../.claude/skills/auditoria-dependencias/SKILL.md)
- Seção 7 → [`.claude/skills/ci-dependabot/SKILL.md`](../.claude/skills/ci-dependabot/SKILL.md)
- Seção 8 (inclui análise de referências na 8.6) → [`.claude/skills/interface-cliente-referencias/SKILL.md`](../.claude/skills/interface-cliente-referencias/SKILL.md)
- Seções 9–10 → [`.claude/skills/qualidade-acessibilidade/SKILL.md`](../.claude/skills/qualidade-acessibilidade/SKILL.md)
- Seção 11 → [`.claude/skills/modulos-opcionais/SKILL.md`](../.claude/skills/modulos-opcionais/SKILL.md)
- Seção 12 → [`.claude/skills/git-integridade/SKILL.md`](../.claude/skills/git-integridade/SKILL.md)
- Seções 13–14 → [`.claude/skills/documentacao-claude-md/SKILL.md`](../.claude/skills/documentacao-claude-md/SKILL.md); política do histórico cumulativo → a mesma skill e `docs/REGISTRO-ALTERACOES.md`
- Seção 15 → [`.claude/skills/continuidade-sessoes/SKILL.md`](../.claude/skills/continuidade-sessoes/SKILL.md) (texto adaptado para o modelo modular)
- Seção 16 → [`.claude/skills/aceitacao-final/SKILL.md`](../.claude/skills/aceitacao-final/SKILL.md)

## Decisões de migração

1. **Formato válido de skills:** cada skill tem diretório próprio e arquivo `SKILL.md` com frontmatter YAML `name` e `description`, para que o Claude Code possa descobri-la e invocá-la.
2. **Regras globais versus procedimentos:** os limites críticos ficam resumidos no `CLAUDE.md`; as regras especializadas completas ficam em skills separadas para carregamento sob demanda.
3. **Continuidade:** a antiga instrução de manter um único prompt integral fora do repositório foi substituída por manter o `CLAUDE.md` e as skills no projeto. Os resumos de continuidade continuam fora do repositório. Na Fase 1B, a seção 3.5 continua exigindo fornecimento explícito pelo usuário na sessão atual.
4. **Mensagens de sessão:** o arquivo foi atualizado para mandar ler o `CLAUDE.md`, o índice e as skills da fase, em vez de apontar para um prompt monolítico que não existe mais.
5. **Sem mudança intencional dos requisitos técnicos originais:** os corpos das seções 1–14 e 16–17 foram preservados, salvo as adaptações documentadas para o modelo modular. A seção 15 foi reescrita para eliminar o conflito entre prompt monolítico e pacote modular.
6. **Histórico cumulativo:** `docs/REGISTRO-ALTERACOES.md` é um único arquivo append-only para alterações relevantes, com regras em `CLAUDE.md` e na skill `documentacao-claude-md`. `CHANGELOG.md` continua reservado a notas relevantes de versão/release; relatórios de fase e resumos de continuidade mantêm sua função separada.

## Verificação

O pacote foi gerado a partir de `Prompt_Oficial_v1.31(1).md` e `Mensagens_Sessao_v1.31(1).md`. A divisão foi feita pelos cabeçalhos numerados e comparada com o conteúdo original. Consulte `docs/RELATORIO-DE-VALIDACAO.md` para a cobertura dos blocos, arquivos, tamanho e verificações executadas.
