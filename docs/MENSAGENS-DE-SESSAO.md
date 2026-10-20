# Mensagens curtas para sessões do Claude Code — conjunto de skills v1.31

## Regra comum de registro de alterações

Em qualquer fase autorizada que altere arquivos, registre mudanças relevantes no único arquivo cumulativo `docs/REGISTRO-ALTERACOES.md`, acrescentando a nova entrada ao final e preservando todo o histórico anterior. Não crie arquivos Markdown separados para cada alteração. Registre objetivo, arquivos afetados, verificações realmente executadas, resultados e pendências, sem segredos, logs extensos, diffs completos ou snapshots do Git. A Fase 0 permanece somente leitura e não pode criar ou editar esse arquivo. Consulte a skill `documentacao-claude-md` para o formato completo. O resumo de continuidade continua sendo externo e separado.

Use este arquivo junto do `CLAUDE.md` e das skills em `.claude/skills/`. O índice normativo é `docs/INDICE-DO-PACOTE.md`. Antes da primeira sessão, substitua o marcador de `DIRETORIO_CONTINUIDADE_EXTERNO` por um caminho absoluto válido e verifique que a pasta está fora do repositório. Deixe `DIRETORIO_REFERENCIAS_PROJETO` vazio ao construir a fundação genérica; configure-o somente no prompt do projeto/cliente quando necessário. Campo vazio ou ainda contendo um marcador/placeholder entre colchetes é considerado não configurado.

A autorização textual para salvar resumos não substitui as permissões reais do Claude Code. Antes de abrir a sessão, adicione a pasta de continuidade aos diretórios acessíveis pelos mecanismos documentados para a versão instalada (por exemplo, `--add-dir` ou `permissions.additionalDirectories`). Para reduzir pedidos repetidos, o usuário pode configurar nas configurações de usuário `permissions.additionalDirectories` e uma regra `Edit` restrita à pasta exclusiva de continuidade. Confirme sintaxe/caminho absoluto e precedência nas fontes oficiais: [CLI reference](https://code.claude.com/docs/en/cli-reference), [permissions](https://code.claude.com/docs/en/permissions) e [settings](https://code.claude.com/docs/en/settings); regras `deny` e `ask` aplicáveis não são superadas por `allow`. Confira `/permissions`. Não use bypass e não modifique configurações de usuário automaticamente. Se surgir um pedido de aprovação para gravar, aguarde a decisão do usuário. Se aprovado, grave e verifique; se negado, bloqueado sem opção de aprovação ou sem resposta até o encerramento/interrupção da sessão, não contorne: mostre o resumo completo na conversa e marque `PENDING MANUAL`. A mensagem curta não substitui o `CLAUDE.md` nem as skills normativas listadas no índice.

## Preparação única

Mantenha `CLAUDE.md` e `.claude/skills/` versionados no projeto-base e use o índice para localizar as regras de cada fase. Na skill `principios-globais`, configure uma única vez `DIRETORIO_CONTINUIDADE_EXTERNO`, por exemplo `C:\Users\SEU_USUARIO\Documents\Claude-Prompts\Resumos\Fundacao-Web`. Antes da primeira sessão, adicione essa pasta ao acesso do Claude Code pelos meios documentados para a versão instalada e confirme as permissões efetivas, sem usar bypass. **Antes de iniciar a Fase 0, lembre-se: quando aparecer um diálogo de aprovação para comando de inspeção, você mesmo deve escolher somente a autorização pontual `Yes`; não escolha `Yes, and don't ask again`, `Always allow` nem outra opção persistente. O Claude Code deve aguardar sua seleção e não pode escolher por você.** Ao fim de cada fase, o Claude Code deverá criar automaticamente o resumo nessa pasta, versionar sem sobrescrever arquivos anteriores e ler o arquivo de volta para verificar a gravação. Você só precisará fornecer o caminho do resumo mais recente na sessão seguinte. Se a permissão de gravação continuar bloqueada, use o fallback previsto nas skills. Um pedido de aprovação pendente não é, por si só, falha: aguarde a decisão do usuário. Mantenha `DIRETORIO_REFERENCIAS_PROJETO` vazio na fundação genérica.

## Sessão inicial — Fase 0

```text

Leia `CLAUDE.md` e `docs/INDICE-DO-PACOTE.md`; carregue e leia integralmente as skills obrigatórias da fase autorizada. Trate o conjunto modular como fonte normativa e execute exclusivamente a Fase 0.

Antes de começar, confirme que a pasta externa de continuidade foi adicionada ao acesso do Claude Code pelo mecanismo oficialmente suportado na versão instalada. Não use bypass nem suponha que isso supera regras `deny`. Se não houver acesso efetivo, siga o fallback previsto nas skills.

Não crie nem altere arquivos do projeto, não instale dependências, não inicialize Git, não altere configurações/permissões do projeto e não avance para outra fase. Não faça nenhuma gravação no projeto. Se um comando de inspeção gerar um diálogo de aprovação, pare e aguarde minha escolha: eu selecionarei somente a autorização pontual disponível (por exemplo, `Yes`). Você não pode clicar, responder por mim nem presumir minha escolha. Nunca selecione nem me oriente a selecionar `Yes, and don't ask again`, `Always allow` ou opção que grave regra persistente. Antes de iniciar a sessão, devo estar preparado para escolher apenas `Yes` pontual sempre que esse diálogo aparecer. Se `.claude/settings.local.json` ou arquivo equivalente de persistência local de permissões surgir ou mudar durante a fase, registre o caminho e a diferença sem expor conteúdo e não o apague; o local pode variar conforme a versão, o shell/ambiente e a existência de Git. Se a comparação comprovar uma gravação não autorizada durante a Fase 0, o portão de somente leitura falhou. Se o arquivo já existia no retrato inicial e não mudou, preserve-o. A única gravação externa autorizada é o resumo da Fase 0 no diretório configurado, seguindo as regras de versionamento e verificação. Se surgir um pedido de aprovação para criar a pasta ou gravar o resumo, aguarde a decisão do usuário. Se aprovado, prossiga somente com essa operação; se negado ou bloqueado sem opção de aprovação, use o fallback previsto nas skills. Não contorne a restrição.

Entregue o relatório conciso em tabelas curtas com os 18 itens obrigatórios. Se a persistência externa falhar, apresente o resumo completo na conversa e use `PENDING MANUAL`; informe o caminho exato somente após verificar a gravação. Aguarde minha aprovação explícita.

```

## Nova sessão — Fase 1A

Use somente depois de revisar o relatório da Fase 0 e decidir explicitamente autorizar a Fase 1A.

```text

Leia `CLAUDE.md` e `docs/INDICE-DO-PACOTE.md`; carregue e leia integralmente as skills obrigatórias da fase autorizada. Leia também o resumo de continuidade da Fase 0: [CAMINHO_ABSOLUTO_DO_RESUMO_FASE_0]. Confirme o estado real do ambiente e do repositório antes de agir. Eu autorizo explicitamente a execução exclusiva da Fase 1A. Não execute o canário nesta fase, não faça commit e não avance para a Fase 1B.

Antes de definir o timeout interno e executar a auditoria principal, registre `npm --version` e os valores efetivos obtidos com `npm config get fetch-retries fetch-retry-mintimeout fetch-retry-maxtimeout fetch-timeout` (consulte as chaves separadamente se necessário). Baseie o orçamento de `scripts/audit.mjs` nos endpoints sequenciais reais da versão instalada: não multiplique cegamente `fetch-retries` pelos endpoints `POST`; a página geral de `npm audit` e o changelog oficial divergem sobre a existência do fallback Quick Audit, então confirme o código/documentação da versão instalada antes de contar endpoints e aplique retentativas somente aos pedidos para os quais elas realmente se aplicam. O timeout deve ser finito, interno ao módulo e testado. Se o orçamento passar de 10 minutos ou for desproporcional, apresente o cálculo, as premissas e as alternativas ao usuário e aguarde sua decisão explícita antes de fixar a constante; um limite menor não autoriza classificar timeout como `network`. Antes da execução principal de `npm run audit`, capture o fixture real normal `npm audit --json` e siga a seção 6.4.1. Depois, faça capturas controladas de falha com variáveis restritas ao processo filho para ambos os modos: `npm audit --json` e `npm audit --omit=dev --audit-level=high --json` (mais a invocação exata de `prod` se for diferente), usando `npm_config_fetch_retries=0`, `npm_config_fetch_timeout` curto e um registry de loopback confirmado indisponível. Confirme que o shell real foi identificado se a execução depender de sintaxe de shell, não altere configurações persistentes e registre os valores/diagnósticos observados. Distinga erros realmente observados dos fixtures sintéticos. Se alguma captura controlada for impossível ou inconclusiva, mantenha `PENDING MANUAL` e não trate o portão como aprovado sem exceção formal prevista no prompt. Depois execute `npm run audit` e registre estados, campos `cause` e evidências reais de produção, árvore completa e agregado. Produção `REJECTED` sempre bloqueia. `INCOMPLETE` ou `NOT RUN` para produção só podem ser pendência externa não bloqueante nos limites estritos da seção 2.1.2, com causa de rede comprovada; qualquer outra causa bloqueia. O agregado `INCOMPLETE` só recebe tratamento equivalente quando a causa comprovada for exclusivamente a mesma indisponibilidade externa da produção, sem causa técnica independente. Preserve os estados e códigos de saída.

Ao terminar, apresente o relatório e a tabela de continuidade completa na conversa. Tente salvar o resumo na pasta externa configurada, sem sobrescrever versões anteriores, e confira o arquivo após gravar. Se surgir um pedido de aprovação, aguarde a decisão do usuário. Se aprovado, grave e verifique; se negado, bloqueado sem opção de aprovação ou sem resposta até o encerramento/interrupção da sessão, não contorne: mostre o resumo completo na conversa e declare `PENDING MANUAL`. Informe o caminho exato somente após verificar a gravação. Pare e aguarde minha decisão.

```

## Nova sessão — Fase 1B

**Importante:** forneça nesta sessão o texto completo da seção 3.5 da skill `seguranca-permissoes`, junto com o resumo de continuidade. Leia também `CLAUDE.md`, o índice e as skills obrigatórias da Fase 1B. Depois envie a mensagem abaixo. Não dependa apenas de `CLAUDE.md`, `docs/SECURITY.md` ou de um resumo antigo para autorizar o canário.

```text

Use como fonte normativa o `CLAUDE.md` e as skills indicadas no índice e leia o resumo de continuidade da Fase 1A: [CAMINHO_ABSOLUTO_DO_RESUMO_FASE_1A]. Confirme o estado real do projeto e a autorização explícita para a Fase 1B. Execute somente a Fase 1B.

Esta fundação deve continuar genérica. Referências de clientes pertencem ao prompt específico desse cliente; não personalize o núcleo com elas. Qualquer uso excepcional de referências de cliente nesta base exige minha autorização expressa, e toda personalização autorizada deve ficar em `src/client/`. Se a interface depender de referências específicas autorizadas, aplique a seção 8.6 para inventariar/analisar a pasta configurada sem modificar originais. Se for apenas a interface genérica da fundação, não bloqueie o trabalho pela ausência de referências.

Antes do canário, confirme se uma reinicialização é necessária e faça-a somente pelo mecanismo autorizado. Peça ao usuário que abra `/permissions`, revise as regras relevantes e confirme explicitamente que foram carregadas e de qual configuração vêm. Registre essa confirmação como verificação manual; não afirme que você abriu o diálogo. Sem essa confirmação, mantenha o canário como `NOT RUN` e pare.

Execute o canário exclusivamente conforme a seção 3.5. Se a criação de `.env.fake-test` for bloqueada, não use outro caminho: peça ao usuário que crie o arquivo fictício e aguarde confirmação explícita. Se ele confirmar que o criou em resposta a esse pedido nesta sessão, execute as duas tentativas de leitura, sem registrar conteúdo; a remoção será responsabilidade do usuário. Se qualquer tentativa obrigatória não resultar em `BLOCKED`, registre o estado real e pare as operações dependentes; não contorne o bloqueio.

Só continue tarefas de interface se eu autorizar expressamente, citando o resultado observado. Essa autorização deve dizer se inclui a instalação de React Router e Zod. Limite a continuação às tarefas que não dependam do canário nem manipulem arquivos de ambiente. A autorização não cobre manuseio de outros arquivos de ambiente. Se o próprio Claude Code criou `.env.fake-test` nesta execução, ele pode removê-lo conforme a seção 3.5; se você criou o arquivo após o pedido, a limpeza é sua responsabilidade. Não faça commit enquanto o arquivo permanecer no projeto. Não trate essa autorização como aprovação técnica, autorização de commit ou permissão para acessar arquivos de ambiente.

Ao final, apresente o relatório e a tabela de continuidade na conversa. Tente salvar o resumo na pasta externa configurada, sem sobrescrever versões anteriores, e confira o arquivo após a gravação. Se surgir um pedido de aprovação, aguarde a decisão do usuário. Se aprovado, grave e verifique; se negado, bloqueado sem opção de aprovação ou sem resposta até o encerramento/interrupção da sessão, não contorne: mostre o resumo completo na conversa e marque `PENDING MANUAL`. Informe o caminho exato somente se a gravação for verificada. Pare e aguarde minha decisão.

```

## Nova sessão — Fase 2

Use somente depois de verificar o relatório da Fase 1B e decidir explicitamente autorizar a Fase 2. A autorização excepcional, se houver, precisa citar a fase de destino e as pendências aceitas, conforme o conjunto de skills. Campo de continuidade vazio ou ainda contendo marcador/placeholder não é válido. Antes da sessão, verifique o acesso real à pasta externa pelos mecanismos oficiais; não use bypass. A pasta de referências deve permanecer vazia na fundação genérica.

```text

Leia `CLAUDE.md` e `docs/INDICE-DO-PACOTE.md`; carregue e leia integralmente as skills obrigatórias da fase autorizada. Leia o resumo de continuidade mais recente: [CAMINHO_ABSOLUTO_DO_RESUMO]. Confirme o estado real do projeto e as autorizações existentes. Eu autorizo explicitamente somente a Fase 2, nos seguintes limites: [DESCREVA A APROVAÇÃO OU AS PENDÊNCIAS QUE ESTOU AUTORIZANDO]. Não implemente módulos opcionais nem faça commit, push, publicação ou deploy sem autorização própria. Execute somente a Fase 2. Ao terminar, tente salvar e verificar o resumo no diretório externo configurado. Se surgir um pedido de aprovação, aguarde a decisão do usuário; se aprovado, grave e releia o arquivo. Informe o caminho exato somente após a verificação, apresente o relatório e aguarde. Se negado, bloqueado sem opção de aprovação ou sem resposta até o encerramento/interrupção da sessão, apresente o resumo completo na conversa e marque `PENDING MANUAL`.

```

## Nova sessão para retomar uma fase interrompida

```text

Leia `CLAUDE.md` e `docs/INDICE-DO-PACOTE.md`; carregue e leia integralmente as skills obrigatórias da fase autorizada. Leia o resumo de continuidade: [CAMINHO_ABSOLUTO_DO_RESUMO]. Não presuma que os registros ainda refletem o estado real. Inspecione o ambiente e o repositório, identifique o que foi concluído, o que pode ter sido afetado e quais operações são seguras de repetir. Não repita operações com efeitos colaterais antes de revalidar os resultados e as autorizações. Continue somente a fase já autorizada; não avance de fase nem interprete este pedido como aprovação de pendências não citadas. Apresente os bloqueios encontrados antes de prosseguir com uma operação dependente. Ao suspender ou interromper o trabalho, tente gerar e verificar um resumo de continuidade que registre o estado real, sem declarar a fase aprovada. Se surgir um pedido de aprovação, aguarde a decisão do usuário. Se aprovada, grave e verifique; se negada, bloqueada sem opção de aprovação ou sem resposta até o encerramento/interrupção da sessão, apresente a tabela completa na conversa e marque `PENDING MANUAL`.

```

## Tabela de autorizações humanas

Não use uma autorização como substituta de outra:

| Autorização | Significado | Não autoriza |
| --- | --- | --- |
| Aprovar relatório | Confirma aceitação do relatório como registro do trabalho. | Próxima fase, exceção de auditoria, commit ou ações remotas, salvo se autorizados explicitamente em separado. |
| Avançar de fase | Autoriza a fase de destino indicada; se a fase anterior estiver incompleta, cita as pendências aceitas. | Não muda o estado técnico e não supera segredo exposto, falha crítica de integridade, operação destrutiva não autorizada ou dependência de segurança não satisfeita. |
| Exceção de `audit:prod` | Substitui somente o portão de `audit:prod` da Fase 1B, com aceitação expressa. | Não muda o estado da auditoria nem cobre outro portão, commit ou operação remota. |
| Commit inicial | Autoriza apenas o commit local conforme a seção 12.6 da skill `git-integridade`. | Não avança fase, não muda estado técnico e não autoriza remoto, push, publicação ou deploy. |
