# Registro cumulativo de alterações

Este é o único arquivo destinado ao histórico técnico cumulativo de alterações relevantes deste projeto. As entradas devem ser adicionadas ao final deste documento, preservando integralmente todas as entradas anteriores. Não crie um arquivo Markdown separado para cada alteração.

Este arquivo contém o histórico cumulativo de manutenção do pacote. Preserve as entradas anteriores e acrescente apenas novos registros relevantes ao final; não invente eventos nem reescreva resultados históricos. Crie uma entrada somente após uma alteração autorizada e relevante ter sido realizada. Não inclua segredos, tokens, valores de arquivos de ambiente, dados pessoais, logs extensos, diffs completos ou snapshots brutos do repositório. Os relatórios de fase e resumos externos de continuidade continuam separados.

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


## 2026-10-20 | Atualização dos caminhos do GitHub para BASE_CODIGO

- **Objetivo:** alinhar os arquivos distribuídos ao nome atual do repositório público.
- **Arquivos afetados:** `CLAUDE.md`, `README.md`, `docs/INDICE-DO-PACOTE.md`, `docs/RELATORIO-DE-VALIDACAO.md`, `docs/REGISTRO-ALTERACOES.md` e `MANIFEST.json`.
- **Alterações:** atualizadas as URLs do repositório e os links Raw para `rafavlad/BASE_CODIGO`; atualizado o título e a árvore de exemplo do README; incrementada a versão do pacote para `modular-r3`.
- **Verificações executadas:** busca por referências antigas; validação JSON do manifesto; conferência dos caminhos locais; recálculo dos tamanhos e hashes SHA-256; verificação de integridade do ZIP.
- **Resultados observados:** os links e exemplos ativos no pacote apontam para o nome `BASE_CODIGO`; o manifesto corresponde aos arquivos finais.
- **Pendências/limitações:** o pacote atualizado ainda precisa ser publicado pelo proprietário no GitHub. A criação do ZIP não altera o repositório remoto.

## 2026-10-20 | Simplificação das skills e correção da matriz de processo

- **Objetivo:** apresentar cada skill como unidade temática identificável pelo nome, eliminar referências de origem que não são necessárias para operar a fundação e corrigir as lacunas de roteamento e configuração apontadas na revisão.
- **Arquivos afetados:** `CLAUDE.md`, `README.md`, `MANIFEST.json`, `docs/INDICE-DO-PACOTE.md`, `docs/MENSAGENS-DE-SESSAO.md`, `docs/REGISTRO-ALTERACOES.md`, `docs/RELATORIO-DE-VALIDACAO.md` e as 13 skills em `.claude/skills/`.
- **Alterações:** títulos principais das skills simplificados; checklist de diagnóstico inicial nomeado funcionalmente; matriz de carregamento ampliada para as tarefas exigidas em cada fase; caminho externo de continuidade e caminhos de referências de cliente retirados da configuração versionada; função do `CLAUDE.md` da fundação separada da função do `CLAUDE.md` de projetos de clientes; autorização excepcional da Fase 1B esclarecida; relatório de validação atualizado; identificação do pacote atualizada.
- **Verificações executadas:** JSON do manifesto válido; os 19 caminhos listados existem; tamanhos e hashes SHA-256 correspondem aos arquivos; as 13 skills têm frontmatter válido e exatamente um título principal funcional; links Markdown relativos locais verificados; busca por identificações antigas sem resultados; ZIP reaberto e integridade conferida.
- **Resultados observados:** alterações aplicadas localmente ao pacote de distribuição; o repositório remoto não foi alterado por esta operação.
- **Pendências/limitações:** publicar esta edição no GitHub e, depois, exercitar as skills numa sessão real do Claude Code. A validação estrutural não equivale à execução real das fases.

## 2026-10-20 | Mapa central de leitura e alinhamento do pacote 2.0.1

- **Objetivo:** oferecer um único ponto de entrada para assistentes de chat consultarem a BASE_CODIGO e manter a identificação da versão coerente entre os arquivos distribuídos.
- **Arquivos afetados:** `docs/MAPA-DE-LEITURA.md`, `README.md`, `docs/INDICE-DO-PACOTE.md`, `CLAUDE.md`, `docs/RELATORIO-DE-VALIDACAO.md`, `docs/REGISTRO-ALTERACOES.md` e `MANIFEST.json`.
- **Alterações:** criado o mapa central com links Raw dos documentos e das 13 skills; README, índice e instruções permanentes apontam para o mapa; versão do pacote definida como `2.0.1` no README e manifesto; registro e relatório atualizados sem apagar entradas históricas.
- **Verificações executadas:** verificação de estrutura, links relativos locais, sintaxe e contagem do manifesto, tamanhos e hashes SHA-256 de todos os arquivos listados, presença dos caminhos de skills e integridade do ZIP.
- **Resultados observados:** arquivos locais e links relativos verificados; manifesto consistente com os bytes finais do pacote.
- **Pendências/limitações:** este ZIP não publica nem altera o repositório remoto. Após a publicação pelo proprietário, os links Raw precisam ser testados no GitHub; a execução em uma sessão real do Claude Code ainda não foi realizada.

