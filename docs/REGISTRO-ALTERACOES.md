# Registro cumulativo de alterações

Este é o único arquivo destinado ao histórico técnico cumulativo de alterações relevantes deste projeto. As entradas devem ser adicionadas ao final deste documento, preservando integralmente todas as entradas anteriores. Não crie um arquivo Markdown separado para cada alteração.

Este arquivo começa sem registros de implementação. Não invente entradas retrospectivas. Crie uma entrada somente após uma alteração autorizada e relevante ter sido realizada. Não inclua segredos, tokens, valores de arquivos de ambiente, dados pessoais, logs extensos, diffs completos ou snapshots brutos do repositório. Os relatórios de fase e resumos externos de continuidade continuam separados.

## Modelo para novas entradas

Copie o modelo abaixo para o final do arquivo e substitua os marcadores da nova entrada. Não altere nem remova entradas anteriores.

```md
## AAAA-MM-DD | Resumo da alteração

- **Objetivo:** ...
- **Arquivos afetados:** ...
- **Alterações:** ...
- **Verificações executadas:** ...
- **Resultados observados:** ...
- **Pendências/limitações:** ...
```


## 2026-10-20 | Alinhamento da estrutura e links do GitHub

- **Objetivo:** corrigir a divergência entre a estrutura publicada no GitHub e o caminho canônico das skills do Claude Code.
- **Arquivos afetados:** `CLAUDE.md`, `README.md`, `MANIFEST.json`, `docs/INDICE-DO-PACOTE.md`, `docs/REGISTRO-ALTERACOES.md`, `docs/RELATORIO-DE-VALIDACAO.md` e `.claude/skills/principios-globais/SKILL.md`.
- **Alterações:** documentado o caminho canônico `.claude/skills/<nome>/SKILL.md`; adicionados links GitHub/Raw no README e links relativos para as skills no índice; removido o título duplicado da skill de princípios globais; atualizado o manifesto.
- **Verificações executadas:** conferência da estrutura do pacote, verificação de todos os caminhos internos documentados, cálculo SHA-256 e tamanhos dos arquivos do manifesto, validação JSON do manifesto e teste de integridade do ZIP.
- **Resultados observados:** as 13 skills ficam no caminho canônico `.claude/skills/`; os links internos para arquivos locais existem no pacote; os hashes registrados correspondem aos arquivos empacotados.
- **Pendências/limitações:** o pacote não foi executado numa sessão real do Claude Code e as alterações ainda precisam ser publicadas pelo proprietário no GitHub.
