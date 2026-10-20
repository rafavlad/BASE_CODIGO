---
name: fases-e-autorizacoes
description: "Fases 0, 1A, 1B e 2, autorizações, critérios de saída, relatórios e primeira ação. Use em todo trabalho por fase."
---

# Fases e autorizações

O projeto possui quatro fases: 0, 1A, 1B e 2.

A conclusão de uma fase não autoriza automaticamente a próxima.

## 2.1 Relatórios e estados

Ao finalizar cada fase, apresente:

* Resumo do trabalho.
* Arquivos criados, alterados ou removidos.
* Dependências e versões utilizadas.
* Comandos executados.
* Testes e verificações realizados.
* Resultados reais das auditorias.
* Estado das permissões e limitações de segurança.
* Decisões técnicas e justificativas.
* Pendências e riscos conhecidos.
* Estado final da fase.
* Próxima etapa permitida.

Utilize estes estados:

* `APPROVED`: todos os portões de saída obrigatórios foram satisfeitos com evidência suficiente, permitindo apenas as pendências externas expressamente classificadas como não bloqueantes nos critérios de saída desta skill e listadas no relatório. Uma exceção formal autorizada pode substituir somente o portão explicitamente abrangido por ela; nunca altera o estado técnico da verificação original.
* `REJECTED`: existe falha ou condição impeditiva comprovada.
* `NOT RUN`: a verificação não foi executada.
* `INCOMPLETE`: a verificação foi iniciada, mas não produziu evidência suficiente.
* `PENDING MANUAL`: exige ação ou decisão humana.
* `NOT APPLICABLE`: o critério não se aplica à fase ou ao contexto, com justificativa registrada.

Uma fase não pode ser aprovada se critérios obrigatórios permanecerem incompletos.

### 2.1.1 Evidências obrigatórias e distinção dos resultados

Para cada critério obrigatório aplicável, registre:

* **Critério:** o requisito que precisa ser satisfeito.
* **Verificação:** comando, teste, inspeção ou procedimento realmente utilizado.
* **Resultado observado:** saída, código de saída, arquivo/linha relevante ou outra evidência suficiente, sem segredos.
* **Estado:** um dos estados definidos nesta seção.
* **Limitações:** o que a evidência não demonstra e qualquer validação manual ainda necessária.

Não é necessário copiar logs extensos para o relatório. Registre evidências suficientes para localizar e confirmar o resultado, preservando informações sensíveis. Não invente hashes, números de linha, saídas ou caminhos.

Diferencie explicitamente:

* execução de teste automatizado;
* revisão estática/manual;
* teste não executado;
* teste executado sem evidência suficiente;
* validação externa ainda pendente.

### 2.1.2 Critérios objetivos para concluir uma fase e portões de saída

Uma fase só pode receber `APPROVED` quando todos os portões de saída obrigatórios aplicáveis abaixo tiverem sido verificados, com evidência suficiente e resultado aprovado, respeitadas apenas as pendências externas não bloqueantes e as exceções formais definidas nesta seção.

| Fase | Portões de saída que bloqueiam `APPROVED` |
| --- | --- |
| **0** | Todos os 18 itens do checklist de diagnóstico inicial desta skill presentes em tabelas curtas e com evidências/limitações; versões baseadas em consulta real ou limitação declarada; nenhum arquivo do projeto criado ou alterado; aprovações de comandos feitas somente de forma pontual, sem salvar regras persistentes durante a fase; qualquer criação/alteração de `.claude/settings.local.json` ou arquivo equivalente de persistência local durante a fase registrada e tratada como falha do portão de somente leitura, sem exclusão automática; retrato inicial do ambiente/repositório registrado no relatório e no resumo de continuidade salvo e verificado, ou apresentado integralmente na conversa conforme a exceção de persistência externa desta seção. Um `settings.local.json` já presente no retrato inicial deve ser preservado e não reprova a fase por sua mera presença. |
| **1A** | `npm ci` e `npm run check` aprovados localmente; testes de `scripts/` descobertos e visíveis na saída do Vitest; ESLint cobrindo `scripts/`; teste de integração do ponto de entrada aprovado; `node scripts/audit.mjs` sem argumentos terminando com código diferente de zero; `npm run audit` executado e estados e campos `cause` individuais de produção, árvore completa e agregado registrados conforme as seções 6.5, 6.6 e 6.9.1. `AUDIT_PROD_STATE=REJECTED` sempre bloqueia a aprovação. `INCOMPLETE` ou `NOT RUN` para produção só podem ser tratados como pendência externa não bloqueante quando a única causa for indisponibilidade externa de rede comprovada pelos critérios desta seção; o estado original continua `INCOMPLETE` ou `NOT RUN` e nunca é descrito como aprovado. Qualquer outra causa, causa ambígua, ausência de evidência suficiente ou falha local bloqueia a aprovação; em especial, ausência de lockfile, erro de código/configuração, falha de spawn local ou modo não executado sem causa externa comprovada não é pendência de rede. Os estados `all` e `aggregate` devem ser relatados fielmente e não podem ser chamados de aprovados se não estiverem aprovados; fixture real e teste de classificação conforme a fonte normativa do procedimento de fixture e captura controlada da skill `auditoria-dependencias`; valores efetivos de retentativa/timeout do npm registrados e orçamento interno justificado, finito e compatível com o fluxo real de endpoints da versão instalada conforme as seções 6.4.1 e 6.4.3; se o orçamento exceder 10 minutos ou for desproporcional, decisão explícita do usuário registrada antes de fixar a constante; capturas controladas de rede realizadas separadamente para árvore completa (`npm audit --json`) e produção (`npm audit --omit=dev --audit-level=high --json`, além da invocação exata de `prod` se diferente), com overrides estritamente restritos ao processo filho; se tecnicamente impossível, `PENDING MANUAL` não satisfaz automaticamente esse portão e deve seguir a regra geral dos critérios de saída da skill `fases-e-autorizacoes`; `.claude/settings.json` criado, sintaxe verificada pela documentação oficial e verificações de configuração possíveis antes de reiniciar registradas, sem exigir nesta fase confirmação de carregamento que dependa de reinício; `git check-ignore` verificado; nenhum segredo incluído ou exposto; regressão das funcionalidades preexistentes aprovada quando houver, ou `NOT APPLICABLE` se o diretório inicial estava vazio; Git inicializado se ainda não existia e sem commit inicial nesta fase. Se o Git já existia, preservar seu histórico e não criar commits. |
| **1B** | Após reinício, quando necessário, configuração carregada confirmada manualmente pelo usuário no diálogo `/permissions` antes do canário, com fonte das regras anotada; canário de permissões executado pelos dois caminhos obrigatórios, ambos classificados `BLOCKED`; arquivo fictício do canário removido/verificado conforme a responsabilidade definida no procedimento “Canário de permissões” da skill `seguranca-permissoes` (se criado pelo usuário, a confirmação de remoção é obrigatória antes de `APPROVED`/commit); `npm run check` aprovado; `audit:prod` aprovada ou exceção formal registrada separadamente e explicitamente autorizada pelo usuário; testes de regressão da 1A aprovados; comparação inicial/final concluída e diff explicado. O estado real da auditoria continua visível mesmo quando houver exceção. A ausência da confirmação manual bloqueia o canário e as operações que dependem dele. |
| **2** | Documentação completa, coerente com a implementação real e revisada; nenhum módulo opcional ou outra funcionalidade nova implementada nesta fase sem autorização específica. |

Além desses portões, em todas as fases:

1. Entregáveis aplicáveis presentes e revisados.
2. Critérios obrigatórios documentados com verificação, resultado observado, estado e limitações.
3. Regressão avaliada conforme as regras específicas para projeto novo ou existente descritas nesta seção.
4. Comparação do estado inicial e final concluída e todas as diferenças explicadas.
5. Alterações preexistentes preservadas, diff revisado e arquivos fora do escopo não modificados indevidamente.
6. Pendências externas e manuais identificadas sem serem apresentadas como concluídas.
7. Relatório e resumo de continuidade completos e coerentes com as evidências.

**Pendências externas não bloqueantes:** permitem que a fase seja marcada como `APPROVED` desde que cada item seja listado com estado, justificativa, evidência e limitação no relatório:

- **(a) CI:** primeira execução do workflow no GitHub.
- **(b) Validação visual:** contraste e larguras/responsividade em navegador, enquanto a ferramenta necessária realmente não estiver disponível.
- **(c) Pinagem:** verificação/pinagem do SHA completo de GitHub Actions quando a consulta oficial estiver indisponível por falta de rede.
- **(d) Matriz Windows:** até a primeira execução da CI no GitHub, desde que a matriz tenha sido definida/revisada e não exista falha já observada em execução.
- **(e) Persistência externa do resumo:** somente quando o resumo completo, incluindo todos os campos obrigatórios, for apresentado integralmente na conversa, a falha de gravação for registrada sem dados sensíveis e a persistência ficar `PENDING MANUAL`. Esta exceção dispensa somente o arquivo externo; não dispensa a apresentação integral do resumo nem o registro do retrato inicial/final e das demais evidências obrigatórias. Se o resumo estiver incompleto ou não for apresentado integralmente na conversa, a exceção não se aplica.
- **(f) Auditoria de produção na Fase 1A:** estado `INCOMPLETE` ou, excepcionalmente, `NOT RUN` causado exclusivamente por indisponibilidade externa de rede comprovada, conforme a regra estrita abaixo. Esta exceção não altera o estado nem o código de saída da auditoria, não se estende à Fase 1B e não autoriza uma exceção de `audit:prod` nessa fase.

As exceções (a) a (d) só se aplicam enquanto as verificações estiverem realmente pendentes por dependerem de acesso ou execução externa. Quando o acesso estiver disponível, execute-as e registre o resultado real. Uma falha efetivamente observada não pode ser reclassificada como pendência externa, exceto pela regra estrita de auditoria da Fase 1A definida abaixo.

**Regra estrita para indisponibilidade de rede na auditoria de produção da Fase 1A:**

1. `INCOMPLETE` só pode ser tratado pela exceção (f) quando houver evidência direta da execução de que a causa foi indisponibilidade de rede/registry npm, sem falha técnica local concorrente. O resultado deve conter `cause=network`, classificado conforme a seção 6.5, com evidência não sensível que sustente essa classificação.
2. `NOT RUN` só pode receber esse tratamento excepcional quando houver evidência independente e registrada de indisponibilidade externa de rede e evidência de que essa foi a única razão para a auditoria de produção não iniciar. A simples afirmação de que “não há internet” não basta. A execução de `npm run audit` continua obrigatória.
3. Ausência de `package-lock.json`, Node/npm, script ou configuração funcional, erro de código, falha local de spawn, timeout do processo sem causa de rede demonstrada, causa ambígua ou ausência de evidência são bloqueantes. Em particular, `cause=missing-lockfile` não é uma falha de rede.
4. Preserve os estados e códigos de saída reais. A exceção permite apenas avaliar a Fase 1A apesar da limitação externa; nunca transforma a auditoria em `APPROVED`.
5. **Tratamento do agregado:** se a exceção (f) se aplicar à auditoria de produção e `AUDIT_AGGREGATE_STATE=INCOMPLETE`, o agregado só recebe o mesmo tratamento não bloqueante quando `AUDIT_AGGREGATE_CAUSE=network` e as evidências mostrarem que essa incompletude decorre exclusivamente da mesma indisponibilidade externa que afetou a produção, sem outra causa técnica independente que bloqueie a fase. Se a auditoria `all` tiver causa independente (`spawn`, `timeout`, `parse`, `missing-lockfile` ou `unknown`), ou se a causa agregada não puder ser demonstrada, a exceção não se propaga. Os estados e causas de `prod`, `all` e `aggregate` continuam visíveis e inalterados; resultados de desenvolvimento devem ser relatados segundo a política da seção 6.9, sem serem ocultados.

**Bloqueantes técnicos:** aplicam-se conforme os portões específicos de cada fase. Na Fase 1A, `npm run audit` precisa ser executado e seus estados individuais registrados; `REJECTED` de produção sempre bloqueia; `INCOMPLETE` e `NOT RUN` só deixam de bloquear se satisfizerem estritamente a exceção externa de rede acima. Qualquer outro `INCOMPLETE`, `NOT RUN`, estado desconhecido ou evidência insuficiente bloqueia a fase. Na Fase 1B, `audit:prod` precisa estar `APPROVED` ou ter a exceção formal prevista abaixo. A confirmação de carregamento das permissões e o canário são portões da Fase 1B, não da Fase 1A; a Fase 1A exige arquivo de configuração válido e registro das verificações que forem possíveis antes de eventual reinício. Também bloqueiam a aprovação falhas críticas, segredos expostos, alterações destrutivas não autorizadas, estado inesperado do repositório ou dúvida razoável sobre integridade/autorização. Não use a categoria de pendência externa para contornar esses bloqueios.

**Exceção formal para `audit:prod` na Fase 1B:** se a auditoria não estiver `APPROVED`, documente em item separado o estado real, a evidência, a causa, o risco e a justificativa. Essa exceção só substitui o portão da Fase 1B após autorização explícita do usuário que identifique e aceite a exceção. Ela não muda o estado da auditoria para `APPROVED`, não oculta falhas nem autoriza publicação, deploy ou operações remotas. Sem autorização explícita, o portão permanece não satisfeito.

Uma verificação `PENDING MANUAL` nunca conta como aprovada. Ela bloqueia `APPROVED` quando for um portão obrigatório ou quando envolver segurança, integridade, autorização ou outro risco impeditivo, exceto pelas pendências externas enumeradas acima e pelas exceções formais explicitamente definidas. Para o tratamento específico do fixture real de `npm audit --json`, aplique exclusivamente a regra de referência do procedimento de fixture e captura controlada da skill `auditoria-dependencias`.

Critérios não aplicáveis devem ser marcados como `NOT APPLICABLE` com justificativa, não omitidos. Em diretório inicialmente vazio, sem funcionalidades preexistentes, a regressão é `NOT APPLICABLE` nas Fases 0 e 1A; a partir da Fase 1B, use como baseline os testes aprovados da Fase 1A. Em projeto existente, avalie regressões pertinentes desde a primeira fase autorizada que altere arquivos; a Fase 0 permanece somente leitura e não exige testes de regressão de código se nenhum código for alterado. Se faltar evidência de outro portão obrigatório, use `INCOMPLETE`, `NOT RUN` ou `PENDING MANUAL`, conforme apropriado, e não aprove a fase.

**Autorização humana excepcional para avançar de fase:** o usuário pode autorizar expressamente o início da fase seguinte mesmo quando a fase anterior não estiver `APPROVED`, desde que cite as pendências específicas que aceita e identifique a fase de destino. Essa autorização é uma decisão humana separada: não muda o estado técnico da fase anterior, não converte pendências em aprovadas e não autoriza automaticamente operações fora do escopo. Ela não cobre segredo real exposto, falha crítica de integridade, operação destrutiva não autorizada ou outra situação em que a operação seguinte dependa diretamente de uma verificação de segurança ainda não satisfeita. Nesses casos, a operação dependente permanece bloqueada. Registre o texto e os limites da autorização no relatório e no resumo de continuidade.

A aprovação humana do relatório é uma decisão separada do estado técnico. Aceitar um risco não transforma uma auditoria incompleta em aprovada. A autorização humana para avançar, quando concedida nos termos do parágrafo anterior, não altera o estado técnico da fase anterior. A autorização humana para um commit, quando permitida pelas regras da seção 12, também não altera o estado técnico da fase nem autoriza a próxima fase; são autorizações distintas.

## 2.2 Fase 0: inspeção somente leitura

A primeira ação deve ser exclusivamente a inspeção do ambiente.

Investigue:

* Sistema operacional e shell.
* Versões instaladas de Node.js e npm.
* Estrutura existente do diretório.
* Estado do Git, se houver repositório.
* Arquivos de configuração relevantes, sem expor segredos.
* Dependências existentes que possam ser verificadas sem instalação.
* Documentação oficial e compatibilidade técnica necessárias para o planejamento.

Prefira `git --no-optional-locks status` para inspecionar o estado do Git.

São permitidas consultas de leitura à rede, incluindo `npm view`, quando necessárias para verificar metadados, versões e compatibilidade.

Não execute:

* Instalações.
* Criação ou alteração de arquivos do projeto.
* Inicialização do Git.
* Commits.
* Alterações de configurações globais.
* Alterações de permissões.
* Operações destrutivas.
* Configuração de remotos.
* Publicações ou implantações.

A inspeção de configurações não autoriza a leitura de valores secretos.

**Aprovação pontual na Fase 0:** se um comando de inspeção gerar um diálogo de aprovação, o Claude Code deve parar e orientar o usuário a selecionar somente a autorização pontual disponível (por exemplo, `Yes`). **Quem escolhe e confirma é o usuário; o Claude Code não deve clicar, responder em nome do usuário nem presumir qual opção foi selecionada.** O usuário deve evitar `Yes, and don't ask again`, `Always allow` e qualquer opção que salve uma regra persistente; não crie uma pré-aprovação persistente para evitar novos pedidos durante esta fase. Esse tipo de escolha pode gravar `.claude/settings.local.json` ou arquivo equivalente no local de persistência usado pela versão instalada; em alguns contextos sem Git e no Windows, esse local pode não ser a raiz de um repositório. Isso violaria o portão de somente leitura. Não altere configurações de usuário para evitar prompts. Se já existir uma regra de leitura previamente configurada nas configurações de usuário, não a modifique.

Se `.claude/settings.local.json` ou qualquer arquivo equivalente de configuração local de permissões aparecer ou mudar durante a Fase 0, registre o caminho, a diferença e quando foi observado, sem expor o conteúdo e sem presumir a causa apenas por sua presença. Como o local pode variar conforme a versão, o sistema operacional e a existência de Git, compare todo o estado relevante do diretório e registre o caminho realmente observado. Não apague, reverta nem tente limpar o diff. Se a comparação inicial/final comprovar uma gravação não autorizada durante a fase, o portão de somente leitura falhou e a Fase 0 não pode receber `APPROVED`. Se o arquivo já constava no retrato inicial e permaneceu inalterado, preserve-o e registre que não foi criado/alterado nesta fase.

Ao final, tente criar e verificar o resumo de continuidade externo da Fase 0 conforme o caminho externo fornecido pelo usuário para esta sessão. Isso não autoriza nenhuma escrita no projeto: a única gravação permitida nesta fase é o resumo no diretório externo explicitamente configurado. Se surgir um pedido de aprovação, aguarde a decisão do usuário e só grave após aprovação. Informe o caminho exato somente se a gravação tiver sido verificada; caso contrário, registre a falha de persistência. **Se a gravação externa falhar, a Fase 0 ainda pode receber `APPROVED` conforme a exceção (e) dos critérios de saída da skill `fases-e-autorizacoes`, desde que o resumo completo, com todos os campos obrigatórios, seja apresentado integralmente na conversa e todos os demais portões sejam satisfeitos.** Aguarde aprovação explícita para iniciar a Fase 1A.

## 2.3 Fase 1A: fundação técnica

Após `APPROVED` e autorização explícita da Fase 0, ou após autorização humana excepcional de avanço nos termos dos critérios de saída da skill `fases-e-autorizacoes`:

* Crie a estrutura técnica básica.
* Configure React, TypeScript e Vite.
* Configure testes, lint (incluindo `scripts/`), formatação e auditoria de dependências.
* Configure scripts npm.
* Configure CI e Dependabot.
* Configure as regras de segurança do Claude Code dentro das capacidades suportadas.
* Estabeleça a separação entre núcleo e personalizações do cliente.
* Crie a documentação técnica inicial.
* Configure Git de acordo com a seção 12.

Não instale React Router ou Zod nesta fase.

Não realize o commit inicial nesta fase.

Configure e teste a descoberta dos testes em `src/` e `scripts/`. Os testes da aplicação e da auditoria devem ser executados por `npm run test` e, consequentemente, por `npm run check`.

Na Fase 1A, execute `npm run audit` e registre separadamente os estados, campos `cause` e evidências de `prod`, `all` e `aggregate`. Para o portão específico desta fase, `AUDIT_PROD_STATE=REJECTED` bloqueia `APPROVED`. `INCOMPLETE` ou `NOT RUN` de produção só podem receber o tratamento excepcional de pendência externa nos termos estritos dos critérios de saída da skill `fases-e-autorizacoes`, usando evidências e `cause` verificáveis. Os demais estados continuam com seus significados reais e devem ser registrados; não os apresente como aprovados. O estado agregado `INCOMPLETE` só recebe o mesmo tratamento excepcional quando a causa agregada for comprovadamente a mesma indisponibilidade externa de rede e não houver causa técnica independente, conforme os critérios de saída e exceções desta skill. Essa regra específica da Fase 1A não altera os contratos de código de saída de `audit:prod`, `audit:all` ou `audit` definidos na seção 6.

Ao concluir, execute as verificações pertinentes e tente gravar o resumo de continuidade externo conforme o caminho externo fornecido pelo usuário para esta sessão. Se surgir um pedido de aprovação, aguarde a decisão do usuário; somente após aprovação, grave e releia o arquivo para verificar seu conteúdo. Se a gravação for negada, bloqueada ou ficar sem resposta até o encerramento/interrupção da sessão, apresente o resumo completo na conversa, informe a limitação e marque `PENDING MANUAL`; aplique eventual exceção de aprovação apenas nos limites dos critérios de saída da skill `fases-e-autorizacoes`. Informe o caminho exato somente se a gravação tiver sido verificada. Apresente o relatório e aguarde autorização para a Fase 1B.

## 2.4 Fase 1B: interface e componentes

Após `APPROVED` e autorização explícita da Fase 1A, ou após autorização humana excepcional de avanço nos termos dos critérios de saída da skill `fases-e-autorizacoes`:

1. Verifique se a configuração de permissões requer reinicialização do Claude Code.
2. Se for necessária uma reinicialização, solicite-a ou execute-a somente pelo mecanismo autorizado e disponível.
3. Peça ao usuário que abra `/permissions`, confira as regras relevantes e confirme que foram reconhecidas/carregadas e de qual configuração vêm. Registre como verificação manual confirmada pelo usuário; não afirme que você abriu o diálogo. Se a confirmação não for fornecida, registre `PENDING MANUAL`, mantenha o canário `NOT RUN` e interrompa a Fase 1B antes de executar o canário ou qualquer etapa posterior; aguarde a confirmação do usuário.
4. Antes do canário, confirme que o usuário forneceu nesta sessão o texto completo do procedimento “Canário de permissões” da skill `seguranca-permissoes` da skill `seguranca-permissoes`, junto com o resumo de continuidade da sessão anterior. A autorização do canário é específica ao contexto da sessão; o `CLAUDE.md`, o `docs/SECURITY.md` e o resumo, isoladamente, não a substituem. Se o procedimento “Canário de permissões” da skill `seguranca-permissoes` não tiver sido fornecida pelo usuário nesta sessão, registre o canário como `NOT RUN`, não improvise o procedimento e pare antes de executar o canário ou tarefas que dependam dele.
5. Execute o canário de permissões da seção 3 pelos dois caminhos obrigatórios. O resultado é evidência adicional dos caminhos efetivamente testados; não prova proteção de comandos/caminhos não testados.
6. Se ambos os caminhos resultarem em `BLOCKED`, instale React Router e Zod. Se algum caminho não resultar em `BLOCKED`, siga a autorização restrita descrita no parágrafo seguinte: a instalação dessas dependências só pode ocorrer se estiver explicitamente incluída na autorização das tarefas de interface.
7. Implemente a interface e os componentes previstos.
8. Utilize Zod efetivamente na validação do formulário de contato.
9. Implemente e execute os testes aplicáveis.
10. Verifique acessibilidade e responsividade.
11. Execute as verificações técnicas e auditorias.
12. Revise o diff completo e os arquivos modificados.

Se qualquer caminho do canário não resultar em `BLOCKED`, registre o resultado real e pare as operações que dependem do canário. Não contorne, não enfraqueça permissões e não repita tentativas por caminhos alternativos. O usuário pode autorizar expressamente a continuação das tarefas de interface citando o resultado concreto do canário. Essa autorização permite somente tarefas de interface que não leiam, criem, copiem, registrem ou versionem valores de arquivos de ambiente reais, não alterem permissões e não dependam diretamente da proteção não confirmada. Aplique a responsabilidade de limpeza do procedimento “Canário de permissões” da skill `seguranca-permissoes`: o Claude Code só remove `.env.fake-test` quando houver evidência de que ele próprio o criou nesta execução; se o usuário o criou após pedido explícito do canário, o usuário o remove e confirma a limpeza. Não realize commit enquanto o fixture permanecer no projeto. A autorização posterior não cobre qualquer outro manuseio de arquivos de ambiente. Ela não muda o estado do canário, não torna a Fase 1B `APPROVED` e não substitui a autorização separada para commit. Sem essa autorização explícita, pare a Fase 1B após registrar o canário.

O estado técnico da Fase 1B e a decisão humana de aprovar o relatório são coisas separadas. O commit inicial local só está autorizado quando a Fase 1B estiver `APPROVED` ou quando o usuário aprovar explicitamente o relatório citando as pendências e autorizando o commit, nos termos da seção 12. A aprovação humana não muda o estado técnico da fase nem supera falhas críticas de integridade ou ações destrutivas não autorizadas.

Essa decisão não autoriza a Fase 2, a criação de repositório remoto, push, publicação ou implantação.

## 2.5 Fase 2: documentação e consolidação

A Fase 2 só pode ser iniciada após `APPROVED` e autorização explícita da Fase 1B, ou após autorização humana excepcional de avanço nos termos dos critérios de saída da skill `fases-e-autorizacoes`. Ela destina-se à consolidação e documentação.

Não implemente módulos opcionais nesta fase sem autorização específica.

Complete a documentação, incluindo `docs/MODULES.md`, conforme as skills `modulos-opcionais` e `documentacao-claude-md`.

Ao terminar, apresente o relatório e aguarde qualquer autorização adicional necessária.

---

## Primeira ação: somente Fase 0

Comece exclusivamente pela Fase 0.

Inspecione o ambiente e produza o relatório de diagnóstico.

Verifique sistema operacional, shell, versões disponíveis, estrutura do projeto, estado do Git e configurações pertinentes, sem expor segredos.

Consulte documentação oficial e metadados atuais quando necessário.

Consultas de leitura, incluindo `npm view`, estão autorizadas nesta fase. Não instale dependências nem altere o projeto.

O relatório deve incluir os 18 itens abaixo, sem omitir nenhum. Para manter o relatório operacional e fácil de revisar, apresente-os em uma ou mais tabelas curtas com colunas como **Item**, **Evidência/resultado**, **Estado** e **Limitação/pendência**. Seja conciso; não copie logs extensos nem transforme cada item em um texto longo.

1. Ambiente encontrado.
2. Estado do diretório e do Git.
3. Versões verificadas.
4. Stack recomendada e compatibilidades (incluindo a forma suportada de configurar ambientes distintos no Vitest).
5. Dependências previstas.
6. Configuração planejada do ESLint e dos plugins, incluindo `scripts/`.
7. Estratégia de separação entre núcleo e cliente.
8. Estrutura de arquivos proposta.
9. Estratégia de auditoria e testes, incluindo o método de detecção de execução direta, os valores efetivos do npm que serão consultados na Fase 1A, o orçamento de timeout baseado nos endpoints sequenciais reais do `npm audit` e a decisão explícita do usuário se o valor exceder 10 minutos ou for desproporcional; não adotar timeout arbitrário sem justificar o fluxo de requisições observado.
10. Estratégia de CI e Dependabot, incluindo a matriz de sistemas operacionais.
11. Plano de permissões e limitações conhecidas.
12. Plano seguro para o canário de permissões.
13. Ordem de execução das fases.
14. Riscos e decisões pendentes.
15. Critérios objetivos de conclusão da Fase 0 e evidências observadas.
16. Como o estado inicial/final do repositório será comparado nas fases seguintes.
17. Estratégia de regressão e registro de falhas/retomada segura.
18. Próximo passo solicitado para aprovação.

Não instale dependências, edite arquivos, inicialize Git, faça commits, altere permissões, publique ou implante nesta primeira ação.

Ao terminar, tente criar e verificar o resumo de continuidade externo da Fase 0 conforme o diretório configurado na skill `principios-globais`. Informe o caminho exato somente se a gravação tiver sido verificada; se surgir um pedido de aprovação, aguarde a decisão do usuário e só grave após aprovação. Se a gravação for negada, bloqueada ou ficar sem resposta até o encerramento/interrupção da sessão, apresente o resumo completo na conversa e use `PENDING MANUAL`. Aplique a exceção (e) dos critérios de saída da skill `fases-e-autorizacoes` somente se o resumo for apresentado integralmente e os demais portões forem satisfeitos. Apresente o relatório e aguarde autorização explícita.

Não avance automaticamente.

---

## Regra final

Aplique as instruções desta fundação com rigor técnico e sem atalhos.

Não remova requisitos, não enfraqueça controles, não presuma sucesso e não invente evidências.

Quando uma operação for incompatível com uma regra de segurança, interrompa-a e explique o conflito. Quando uma verificação não puder ser executada, registre seu estado real. Quando uma decisão exigir autorização, aguarde.

O objetivo não é apenas criar um projeto que funcione agora. É estabelecer uma fundação reutilizável, auditável, verificável e sustentável para projetos futuros, sem contradições de segurança ou falhas silenciosas de execução.

