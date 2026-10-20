---
name: aceitacao-final
description: "Checklist de aceitação técnica por área e regras finais de aprovação. Use antes de declarar concluída uma fase ou fundação."
---

# Checklist de aceitação final

Avalie individualmente os critérios aplicáveis. Este checklist não substitui os requisitos detalhados das seções anteriores.

## 16.1 Fundação

* Estrutura de diretórios adequada.
* React, TypeScript e Vite configurados.
* `strict: true` habilitado.
* `noUncheckedIndexedAccess: true` habilitado.
* `exactOptionalPropertyTypes: true` habilitado.
* ESLint e Prettier configurados.
* ESLint analisa `scripts/**/*.mjs` com globais do Node, sem regras desativadas.
* Vitest e Testing Library configurados.
* Testes de `src/` em ambiente de DOM e de `scripts/` em ambiente `node`.
* Descoberta de testes em `src/` e `scripts/` validada.
* Testes da auditoria executados por `npm run test` e `npm run check`.
* Scripts npm funcionando.
* Scripts de auditoria usando o mesmo módulo e modos fixos.
* Importação do módulo de auditoria sem efeitos colaterais.
* Detecção de execução direta robusta (caminho real) e provada por teste de integração.
* Classificação, execução e agregação testadas.
* Fixture real de `npm audit --json`, validação de contagens/lista e identificação de fixtures sintéticos conforme o procedimento de fixture e captura controlada da skill `auditoria-dependencias`.
* `npm run audit` executado na Fase 1A, com estados individuais registrados; produção `REJECTED` impede a aprovação da fase.
* Lockfile coerente.
* `.gitignore` validado, incluindo `.claude/settings.local.json`; `.env.example` não ignorado.
* `.gitattributes` configurado.
* Documentação inicial criada.
* Git inicializado somente após autorização da Fase 0, quando aplicável; sem commit na Fase 1A.
* Portões de saída da Fase 1A verificados individualmente conforme a tabela dos critérios de saída da skill `fases-e-autorizacoes`, incluindo `npm run audit` executado e estados individuais registrados; produção `REJECTED` bloqueia a fase.
* `settings.json` criado e validado na Fase 1A, sem exigir confirmação de carregamento antes de reinício; carregamento confirmado na Fase 1B antes do canário.
* Em projeto existente, `format:check` utilizado sem reformatar arquivos preexistentes; formatação automática restrita aos arquivos criados na fase atual.

## 16.2 Interface

* Componentes e páginas obrigatórios implementados.
* React Router instalado na Fase 1B.
* Zod utilizado efetivamente na validação do contato.
* Nenhuma integração fictícia apresentada como real.
* Para a fundação genérica, `DIRETORIO_REFERENCIAS_PROJETO` permanece vazio por padrão; referências de cliente são analisadas nas instruções da tarefa específico desse cliente, salvo autorização expressa excepcional.
* Quando a tarefa de um projeto de cliente depender de design/referências específicas, pasta configurada ou caminho solicitado antes da análise, inventário recursivo concluído, arquivos não suportados/parciais identificados e originais preservados.
* Decisões visuais e funcionais relevantes rastreáveis a requisitos ou referências reais; ambiguidades e conflitos registrados sem invenção silenciosa.
* Nenhum conteúdo/identidade de cliente foi incorporado ao núcleo reutilizável; personalizações excepcionalmente autorizadas ficaram em `src/client/`.
* Separação entre núcleo e cliente preservada.
* Metadados configurados.
* Testes pertinentes executados.
* Acessibilidade e responsividade verificadas dentro das capacidades disponíveis; contraste registrado como pendente quando não verificado em navegador.

## 16.3 Segurança e auditoria

* Arquivos de ambiente protegidos.
* `.env.example` contém apenas valores fictícios.
* `.claude/settings.json` criado com sintaxe validada conforme a documentação oficial, e as verificações possíveis antes de reiniciar registradas na Fase 1A.
* Carregamento das permissões confirmado manualmente pelo usuário no diálogo `/permissions` na Fase 1B após reinício, quando necessário, antes do canário; confirmação registrada no relatório e na continuidade.
* `.claude/settings.local.json` (quando presente) tratado no diff sem exclusão automática e confirmado como ignorado quando aplicável.
* Limitações documentadas.
* Canário executado com segurança ou registrado com estado correto, com os comandos e ferramentas testados identificados; falha em qualquer caminho não é mascarada.
* Caminhos de leitura avaliados separadamente.
* Nenhum bloqueio presumido sem evidência.
* Nenhum segredo exposto.
* Auditorias de produção e completa implementadas.
* Classificação baseada em `metadata.vulnerabilities` com validação cruzada pela lista.
* Saída não analisável nunca resulta em `APPROVED` nem em `REJECTED`.
* `audit:prod` retorna zero somente quando aprovado.
* `audit:all` retorna zero somente quando aprovado, em execução isolada.
* O agregado aplica a política de produção sem ocultar a auditoria completa.
* Estados individuais preservados e emitidos em linhas de formato fixo.
* Campo `cause` presente nos resultados, com categorias e evidências conforme a seção 6.5; causas de rede não inferidas de erros genéricos.
* Saída normal real e fixture capturados antes da execução principal de `npm run audit`, conforme o procedimento de fixture e captura controlada da skill `auditoria-dependencias`.
* Capturas manuais controladas da falha de rede do npm realizadas separadamente para árvore completa e produção conforme o procedimento de fixture e captura controlada da skill `auditoria-dependencias`, ou limitações `PENDING MANUAL` documentadas por modo sem alegar validação empírica.
* Linhas de causa `AUDIT_PROD_CAUSE`, `AUDIT_ALL_CAUSE` e `AUDIT_AGGREGATE_CAUSE` consistentes com estados e evidências.
* Exceção de rede da Fase 1A e sua aplicação ao agregado verificadas conforme os critérios de saída da skill `fases-e-autorizacoes`, sem alterar estados técnicos e distinguindo falha comum de causa independente.
* Subprocessos utilizados somente para executar o `npm audit` real.
* Modos validados contra lista fixa.
* Modos ausentes, desconhecidos e argumentos adicionais inválidos rejeitados antes de qualquer auditoria.
* Mensagem de uso clara e código de saída diferente de zero para argumentos inválidos.
* Nenhum modo padrão selecionado.
* Importação sem execução de auditorias.
* JSON truncado e corrompido tratados como incompletos.
* Timeout como constante interna, não configurável por argumento, testado e justificado pelo fluxo de endpoints e pelas retentativas realmente aplicáveis à versão instalada, sem presumir fallback Quick Audit; `fetch-retries` não é multiplicado cegamente pelos endpoints `POST`; se o orçamento exceder 10 minutos ou for desproporcional, valor/alternativas apresentados ao usuário e decisão explícita registrada antes de fixar a constante; timeout sem evidência direta de rede permanece `cause=timeout`/`unknown` e bloqueia a exceção de rede.
* Capturas controladas de rede separadas para a árvore completa e a invocação de produção (`--omit=dev` quando equivalente ao modo `prod`), com `npm_config_fetch_retries=0` e `npm_config_fetch_timeout` curto apenas no processo filho; shell/ambiente confirmado, configuração persistente inalterada e valores/diagnósticos efetivamente usados registrados; distinção entre códigos de rede observados e fixtures sintéticos. Se tecnicamente impossível, `PENDING MANUAL` é registrado e não satisfaz automaticamente o portão obrigatório; aplicar os critérios de saída da skill `fases-e-autorizacoes`.
* Encerramentos inesperados e falhas de processo tratados e testados.
* Compatibilidade entre Windows e Linux verificada pela matriz de CI ou registrada como `PENDING MANUAL`.
* Job de auditoria independente do job de qualidade, ou `if: always()` documentado.
* CI preserva falhas anteriores e distingue tentativa, execução efetiva, falha e ausência de execução.
* Resumo da CI baseado em `steps.<id>.outcome`.
* Somente a auditoria de produção bloqueia a CI por vulnerabilidades de dependências.
* Auditoria completa visível e reportada mesmo quando não bloqueante.
* Dependabot configurado sem auto-merge.
* Pinagem das actions verificada ou pendência documentada.
* Permissões mínimas de `GITHUB_TOKEN`.
* Workflow revisado manualmente e registrado como `PENDING MANUAL` até execução real no GitHub; essa pendência é não bloqueante antes da primeira execução, conforme os critérios de saída da skill `fases-e-autorizacoes`.
* Pendências externas não bloqueantes listadas separadamente das pendências técnicas bloqueantes.

## 16.4 Qualidade, regressão e integridade

* Critérios objetivos de saída da fase avaliados individualmente.
* Cada critério obrigatório possui verificação, resultado observado e estado.
* Testes de regressão relevantes executados e resultados registrados.
* Testes existentes não foram desativados ou enfraquecidos para obter aprovação.
* Estado inicial relevante do repositório registrado no relatório e no resumo de continuidade, nunca em arquivo do projeto.
* Estado final comparado ao estado inicial, desconsiderando apenas caches reconhecidos em diretórios ignorados conforme a seção 10.3.
* Todas as diferenças explicadas, incluindo arquivos não rastreados e removidos.
* Alterações preexistentes e desconhecidas preservadas.
* Falhas, interrupções e operações incompletas registradas para retomada segura.
* Lista acumulada dos arquivos criados por etapa registrada no único relatório final e na tabela curta de continuidade, sem relatórios intermediários por subpasso, commits antecipados, stash ou cópias de recuperação fora do escopo.
* Regressão marcada como `NOT APPLICABLE` em Fases 0 e 1A somente quando o diretório inicial estava vazio e não havia funcionalidades preexistentes; baseline da Fase 1B baseada nos testes da 1A.
* Operações destrutivas não autorizadas interrompidas.
* Falhas críticas e autorizações ausentes resultaram em parada da operação dependente.
* Verificações técnicas executadas ou pendências declaradas.
* Diff completo revisado.
* Documentação consistente com a implementação.
* Nenhuma alteração fora do escopo sem autorização.
* Arquivos de referência tratados como entrada somente para leitura, sem execução, edição, remoção, cópia ou inclusão em Git não autorizadas.
* Nenhuma alteração preexistente do usuário descartada.
* Nenhum arquivo temporário desnecessário deixado no projeto.
* Nenhuma falha ocultada.
* Estados finais sustentados por evidências.

## 16.5 Regras de aprovação

Um critério obrigatório não executado não pode ser marcado como aprovado.

Uma auditoria incompleta nunca satisfaz o critério de auditoria aprovada.

Uma auditoria completa não aprovada pode não bloquear o agregado quando a auditoria de produção estiver aprovada, mas deve manter seu estado individual verdadeiro e um alerta visível.

A aprovação agregada não pode ser interpretada como aprovação da auditoria completa ou de todas as verificações.

A aprovação de uma fase não autoriza automaticamente a próxima.

A aceitação humana de um risco deve ser registrada separadamente do estado técnico.

Se existir um critério obrigatório impeditivo, não declare a fase concluída como aprovada.

Não confunda "fase trabalhada" com "fase concluída": uma fase pode ter entregáveis parciais e ainda permanecer `INCOMPLETE` ou `REJECTED`.

Se houver uma falha crítica, autorização ausente, conflito de requisitos, alteração inesperada ou dúvida razoável sobre o impacto destrutivo de uma ação, interrompa a operação dependente. Registre o estado seguro, as evidências e a decisão necessária; não improvise uma forma de contornar o bloqueio.

---
