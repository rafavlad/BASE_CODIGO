---
name: auditoria-dependencias
description: "Implementação e testes da auditoria de dependências npm, classificação de resultados e contratos da CLI. Use ao criar ou revisar scripts/audit.mjs e os comandos de auditoria."
---

# 6. AUDITORIA DE DEPENDÊNCIAS

## 6.1 Arquitetura e modos

Toda a lógica de auditoria deve ficar em um único arquivo: `scripts/audit.mjs`.

Modos permitidos:

* `prod`: auditoria das dependências de produção.
* `all`: auditoria da árvore completa.
* `aggregate`: execução independente das duas auditorias e aplicação da política agregada.

Não crie implementações duplicadas dos três modos.

O módulo deve funcionar de forma confiável em Windows e Linux.

Separe claramente:

1. Execução do comando npm.
2. Captura de stdout, stderr, código de saída e falhas.
3. Validação do JSON.
4. Classificação dos resultados.
5. Agregação dos resultados individuais.
6. Apresentação do resultado final.

Utilize Node.js ESM e JSDoc.

Use `spawn` com argumentos fixos, sem interpolar conteúdo externo nos comandos.

Prefira invocar `process.execPath` com `process.env.npm_execpath` e argumentos fixos, evitando shell quando possível.

Verifique a compatibilidade do mecanismo com as versões selecionadas de Node.js e npm, inclusive no Windows.

Se `npm_execpath` não estiver disponível (por exemplo, execução direta com `node scripts/audit.mjs` fora do npm), utilize uma alternativa compatível. `shell: true` só pode ser utilizado no Windows quando estritamente necessário, com argumentos fixos e sem conteúdo externo. Documente a decisão e suas limitações.

## 6.2 Contrato obrigatório da CLI

O argumento de modo deve corresponder exatamente a um dos seguintes valores:

* `prod`
* `all`
* `aggregate`

A validação deve ocorrer no ponto de entrada, **antes de qualquer auditoria, subprocesso, acesso à rede ou outra operação de execução**.

Se o modo estiver ausente, for desconhecido ou contiver diferenças de grafia:

1. Imprima uma mensagem de uso clara.
2. Informe os três modos válidos.
3. Retorne código diferente de zero.
4. Não execute nenhuma auditoria.
5. Não inicie subprocessos.
6. Não selecione um modo padrão.
7. Não converta automaticamente o argumento inválido para outro modo.

Argumentos adicionais não previstos também devem ser rejeitados com mensagem de uso e código diferente de zero, salvo necessidade técnica explicitamente documentada e aprovada.

Não aceite argumentos arbitrários que possam alterar os comandos executados.

A validação da CLI deve ser separada da lógica de classificação, para permitir testes unitários sem executar auditorias reais.

Os scripts npm definidos na seção 5.6 devem selecionar exclusivamente os modos fixos previstos.

## 6.3 Importação segura e execução direta

O módulo deve poder ser importado pelos testes sem executar auditorias, iniciar subprocessos, acessar a rede ou imprimir a saída normal dos modos.

A execução principal só pode ocorrer quando `scripts/audit.mjs` for chamado diretamente pelo Node.js.

### 6.3.1 Detecção robusta de execução direta

`import.meta.url` já contém o caminho real do módulo, mas `process.argv[1]` pode conter um caminho relativo, um link simbólico ou diferenças de formatação (por exemplo, a letra do drive no Windows). Uma comparação ingênua pode resultar em falso negativo, e nesse caso o script **terminaria com código zero sem executar nada**.

Portanto:

* Resolva `process.argv[1]` com `fs.realpathSync` (dentro de `try/catch`) antes de convertê-lo com `pathToFileURL` e compará-lo com a URL real do módulo (também resolvida de forma equivalente).
* Trate com segurança a ausência de `process.argv[1]` e falhas na resolução, sem lançar exceção e sem iniciar a execução por engano.
* Considere a normalização de maiúsculas e minúsculas e de separadores no Windows quando necessário.
* Documente o método escolhido e suas limitações.

### 6.3.2 Teste de integração obrigatório do ponto de entrada

Crie um teste de integração que execute o arquivo real, sem mocks, com `spawn` e `process.execPath`:

* Sem argumentos: o processo deve terminar com **código diferente de zero**, imprimir a mensagem de uso e não iniciar nenhuma auditoria.
* Com um modo inválido: idem.
* Com argumentos adicionais não permitidos: idem.
* Invocado por caminho relativo e por caminho absoluto: ambos devem alcançar a validação da CLI (comprovando que o ponto de entrada dispara).
* Em plataformas que suportarem links simbólicos sem privilégios elevados, invocado por link simbólico: deve alcançar a validação da CLI. Registre como `NOT RUN` se a plataforma não permitir.

Esse teste existe para provar que o ponto de entrada realmente dispara. Os testes unitários de detecção de execução direta não substituem este teste.

### 6.3.3 Ausência de efeitos colaterais na importação

Exporte as funções relevantes para execução, classificação, agregação e apresentação.

A importação deve ser livre de efeitos colaterais de execução. Inicializações inevitáveis devem ser inofensivas, sem comandos externos, rede, escrita de arquivos ou saída de auditoria.

Teste explicitamente essas garantias.

## 6.4 Classificação dos resultados

### 6.4.1 Fonte da verdade e validação cruzada

Para um relatório `npm audit --json`:

* A fonte principal das contagens é `metadata.vulnerabilities` (contagens por severidade).
* A lista `vulnerabilities` (por pacote, com o campo `severity`) deve ser usada para validação cruzada.
* Contagens devem ser inteiros não negativos. Valores ausentes, negativos, fracionários ou de outro tipo tornam o relatório inválido.

**Validação da relação entre contagens e lista:** use pelo menos uma saída real de `npm audit --json` da versão de npm instalada para verificar empiricamente como `metadata.vulnerabilities` se relaciona com as entradas de `vulnerabilities`. Compare as contagens por severidade com a quantidade e a estrutura das entradas sempre que a saída real fornecer valores que permitam essa comparação. Se o fixture real for um relatório limpo com contagens zero e lista vazia, registre que ele confirma apenas o caso zero-a-zero; não adicione dependências vulneráveis de propósito para obter um relatório não vazio. Não presuma que as contagens por severidade sempre equivalem ao número de entradas por severidade em todas as versões. Se a saída real contradisser essa premissa, registre a evidência, a versão do npm e o comportamento observado e revise explicitamente o Caso F antes de implementá-lo ou mantê-lo; não aplique automaticamente uma regra de divergência que possa produzir falso `INCOMPLETE`. Ajuste a validação somente com justificativa baseada na evidência e sem enfraquecer a detecção de `high`/`critical`.

Inclua pelo menos um fixture capturado de uma saída real de `npm audit --json` produzida pela versão do npm instalada e use-o em um teste de classificação. **Ordem obrigatória:** depois de o `package-lock.json` existir e o ambiente estar preparado, capture primeiro a saída real normal, antes da execução principal de `npm run audit` na Fase 1A, enquanto o registry estiver acessível. Isso reduz o risco de uma queda posterior impedir a captura. Não adicione dependências vulneráveis de propósito; um relatório limpo com contagens zero e lista vazia é válido, mas confirma apenas esse caso. Não fabrique uma saída nem a apresente como captura real. Antes de guardar o fixture, confirme que não contém dados sensíveis; remova apenas informação incidental sensível, se houver, sem alterar os campos e valores necessários para representar fielmente a estrutura testada. Registre a versão do npm usada para capturá-lo. Identifique explicitamente os fixtures sintéticos como **sintéticos** nos testes e em `docs/SECURITY.md`; nunca os apresente como capturas reais.

Antes de escolher o timeout interno da auditoria ou iniciar as capturas, registre `npm --version` e consulte os valores efetivos com `npm config get fetch-retries fetch-retry-mintimeout fetch-retry-maxtimeout fetch-timeout`. Se a versão instalada não aceitar várias chaves no mesmo comando, consulte-as separadamente. Registre os valores observados e os códigos de saída. Não presuma valores padrão, não altere arquivos `.npmrc` e não imprima a configuração completa do npm, que pode conter informações sensíveis.

**Definição do orçamento do timeout interno:** confirme o fluxo de rede do `npm audit` para a versão realmente instalada usando a documentação oficial e, se as páginas divergirem, o changelog/código-fonte correspondente à versão. Há uma divergência conhecida nas fontes oficiais: a página de documentação do `npm audit` ainda descreve fallback de Bulk Advisory para Quick Audit, enquanto o changelog oficial do npm CLI registra a remoção desse fallback a partir da linha de versões 9. Por isso, não presuma que o fallback exista só porque uma página genérica ainda o descreve. A requisição Bulk Advisory usa `POST`; a política documentada de `make-fetch-happen` não aplica retentativas a requisições `POST` e não repete `getaddrinfo ENOTFOUND`. Não multiplique mecanicamente `fetch-retries` pelo timeout dos endpoints `POST`. Baseie o orçamento no caminho de requisições sequenciais que realmente existe na versão instalada: some os timeouts por requisição para endpoints que podem ser chamados em sequência, adicione margem explícita para inicialização, parsing e encerramento do processo e inclua tentativas/esperas adicionais somente para requisições idempotentes cujo caminho e política de repetição tenham sido confirmados. Se a implementação instalada realizar apenas a requisição Bulk Advisory e não tiver fallback Quick Audit nem outras requisições de rede relevantes, use como referência conservadora `fetch-timeout + margem explícita`; se a implementação instalada realmente chamar também Quick Audit em sequência, use `2 × fetch-timeout + margem explícita`. Inclua quaisquer outras requisições sequenciais comprovadas. Essas expressões são orçamentos condicionais, não garantias universais. Registre a versão, as fontes, o comportamento observado, as premissas, a fórmula, os valores e o orçamento resultante. Se a documentação e o código instalado não permitirem determinar o fluxo com confiança, não adivinhe: apresente a divergência e os orçamentos possíveis ao usuário e aguarde decisão explícita ou mantenha o critério bloqueado.

O timeout interno em `scripts/audit.mjs` deve ser finito, uma constante interna, testado e não configurável por argumento de linha de comando. Não diminua silenciosamente o timeout do npm nem o número de retentativas da auditoria principal. **Se o orçamento calculado ultrapassar 10 minutos ou parecer desproporcional, apresente ao usuário o valor, a fórmula, as premissas e as alternativas antes de fixar a constante; aguarde decisão explícita.** As alternativas devem distinguir: (a) adotar o orçamento conservador e aceitar a espera maior; (b) autorizar explicitamente um limite menor, compreendendo que qualquer término por esse limite sem evidência direta de rede continuará `cause=timeout`/`unknown` e não poderá usar a exceção de rede; ou (c) suspender a implementação da constante/a auditoria dependente até decidir. Não fixe silenciosamente o valor nem escolha em nome do usuário. Enquanto a decisão necessária estiver pendente, registre `PENDING MANUAL` e não declare satisfeitos os portões dependentes.

Para validar empiricamente a classificação de `cause=network`, faça capturas manuais controladas de falha do npm, quando tecnicamente possível, **depois do fixture real normal e antes da execução principal de `npm run audit` na Fase 1A**. Execute separadamente: (1) `npm audit --json`, cobrindo a árvore completa; e (2) `npm audit --omit=dev --audit-level=high --json`, cobrindo o caminho de produção usado pelo modo `prod` quando esse comando corresponder à implementação projetada. Se o modo `prod` tiver argumentos/execução diferentes, capture também a invocação exata que ele usará, sem assumir equivalência não verificada. Para cada invocação, use um processo isolado e variáveis de ambiente restritas ao processo filho: `npm_config_registry` aponta para uma porta de loopback cuja indisponibilidade tenha sido confirmada; `npm_config_fetch_retries='0'`; e `npm_config_fetch_timeout='10000'` (ou outro valor curto explicitamente registrado). Esses valores temporários destinam-se apenas às capturas controladas, nunca à auditoria principal por padrão.

Prefira um executor que passe um objeto `env` diretamente ao subprocesso (por exemplo, `spawn`/`spawnSync` do Node), em vez de alterar o ambiente da sessão de shell. Se comandos diretos no shell forem inevitáveis, identifique primeiro se é PowerShell ou CMD, use a sintaxe correta e restaure explicitamente quaisquer variáveis preexistentes; não deixe alterações no ambiente da sessão nem na configuração persistente. Não altere configurações globais/persistentes do npm, não passe overrides da captura como argumentos de `audit.mjs`, não use um endereço externo nem um hostname fora de loopback e não armazene tokens/credenciais. Registre, para cada invocação, versão do npm, comando exato, overrides temporários, código de saída, códigos/diagnósticos realmente observados, `stdout`/`stderr` relevante já sanitizado e resultado do classificador. Se a implementação instalada tiver fallback Quick Audit, os endpoints Bulk Advisory e Quick Audit podem produzir diagnósticos separados; preserve a ordem e não suponha que ambos falharam da mesma maneira. Se não houver fallback nessa versão, registre apenas os diagnósticos realmente produzidos. Não guarde saídas brutas se puderem conter dados sensíveis.

Depois das capturas, confirme que a configuração efetiva normal do npm continua igual à pré-checagem. Identifique a evidência como **captura manual controlada de falha de rede**, separada do fixture real normal e dos fixtures sintéticos. Teste cada caminho de produção e árvore completa com os diagnósticos efetivamente observados. A captura de loopback normalmente exercita uma falha de conexão local, por exemplo `ECONNREFUSED`; isso não demonstra experimentalmente `ENOTFOUND`, `EAI_AGAIN` ou `ETIMEDOUT`. Teste outros códigos por fixtures sintéticos claramente rotulados e registre quais códigos foram observados de verdade e quais foram sintéticos. Não adicione um host `.invalid` nem relaxe a restrição de loopback sem nova autorização explícita. Se uma invocação controlada não produzir evidência direta compatível com `network`, registre-a como inconclusiva/`PENDING MANUAL`; não alegue que a classificação foi empiricamente validada. Essa pendência não é automaticamente não bloqueante: aplique a seção 2.1.2 e não aprove a Fase 1A enquanto o portão obrigatório permanecer pendente, salvo exceção formal permitida, explicitada, documentada e autorizada. A exceção de rede da Fase 1A não dispensa o fixture real normal exigido acima.

**Fonte normativa para o fixture real:** este parágrafo é a única definição completa da exigência de captura, teste, rotulagem de fixtures sintéticos e tratamento de falha de captura. Nas tabelas de portões, no relatório e no checklist, faça referência a esta seção em vez de repetir a regra. Se não for possível obter a saída real, registre o critério como `PENDING MANUAL`, não substitua a evidência por JSON inventado e não declare esse critério aprovado. A fase 1A não recebe `APPROVED` enquanto esse portão permanecer não satisfeito; o usuário pode autorizar excepcionalmente o avanço conforme a seção 2.1.2, mas essa autorização não converte o teste em aprovado.

### 6.4.2 Princípio central

* **`REJECTED` exige evidência analisada com sucesso.** Somente um relatório JSON que tenha sido efetivamente analisado (parse bem-sucedido, objeto) e cujas contagens **ou** cuja lista indiquem vulnerabilidades `high` ou `critical` pode resultar em `REJECTED`.
* **Saída que não pôde ser analisada nunca resulta em `REJECTED` nem em `APPROVED`.** JSON inválido, vazio, truncado ou corrompido é sempre `INCOMPLETE`, sem exceção. Não tente completar, adivinhar ou reconstruir dados ausentes.
* **`APPROVED` exige evidência completa e coerente** (ver Caso B).

### 6.4.3 Casos

**Caso A: vulnerabilidades altas ou críticas indicadas em relatório analisado.**
Se `metadata.vulnerabilities.high + metadata.vulnerabilities.critical > 0` **ou** se qualquer entrada da lista `vulnerabilities` tiver `severity` igual a `high` ou `critical`, classifique como `REJECTED`, independentemente do código de saída e de divergências entre a contagem e a lista.

**Caso B: aprovação.**
Classifique como `APPROVED` somente se **todas** as condições forem verdadeiras:

* O código de saída do processo é zero.
* Não ocorreu timeout nem encerramento por sinal.
* O JSON foi analisado como objeto e não contém a chave `error`.
* `metadata.vulnerabilities` existe, com contagens válidas.
* A lista `vulnerabilities` existe como objeto.
* Não há vulnerabilidades `high` ou `critical` pela contagem nem pela lista.
* Contagem e lista não divergem de outra forma (ver Caso F).

Vulnerabilidades `low`, `moderate` e `info` não impedem a aprovação segundo esta política, mas devem continuar visíveis.

**Caso C: código diferente de zero sem evidência de `high`/`critical`.**
Classifique como `INCOMPLETE`. Um JSON aparentemente limpo não neutraliza uma falha de execução.

**Caso D: relatório inválido, vazio, truncado ou corrompido.**
Classifique como `INCOMPLETE`, sem exceção (ver princípio central).

**Caso E: chave `error`, metadados ausentes ou contagens inválidas.**
Se houver a chave `error`, classifique como `INCOMPLETE`. Se `metadata.vulnerabilities` estiver ausente ou com contagens inválidas, classifique como `INCOMPLETE`, a menos que a lista `vulnerabilities`, analisada com sucesso, contenha entradas `high` ou `critical` (Caso A). Não interprete dados ausentes como zero.

**Caso F: divergências entre contagem e lista.**
* Se a contagem indicar `high`/`critical` ou a lista indicar `high`/`critical`, prevalece `REJECTED` (Caso A).
* Se a contagem indicar zero `high`/`critical` e a lista também, mas os totais divergirem de outra forma (por exemplo, o total por severidade não corresponder ao número de entradas de uma severidade), classifique como `INCOMPLETE`.
* Se a lista `vulnerabilities` estiver ausente, mesmo com contagens válidas e zero `high`/`critical`, classifique como `INCOMPLETE`.

**Caso G: falha de rede ou execução.**
Falhas de rede, falha ao iniciar o processo ou erros equivalentes resultam em `INCOMPLETE`. Não presuma que falha de rede significa ausência de vulnerabilidades.

**Caso H: timeout, processo travado ou encerramento inesperado.**
O timeout deve seguir exclusivamente o método de orçamento e a regra de decisão humana da seção 6.4.1; esta subseção não cria uma fórmula alternativa. A constante interna deve ser finita, justificada pelo fluxo real de requisições sequenciais e pelas retentativas que realmente se aplicam à versão instalada, testada e **não configurável por argumento de linha de comando**. Não multiplique cegamente `fetch-retries` pelos endpoints `POST` e não altere silenciosamente as configurações da auditoria principal. Se o orçamento exceder 10 minutos ou for desproporcional, apresente os dados e aguarde a decisão explícita do usuário antes de fixar a constante. Se o limite interno expirar antes de existir evidência direta de falha de rede, o resultado deve permanecer `cause=timeout` ou `unknown`, conforme a evidência, e não pode usar a exceção de rede da Fase 1A.

Quando o processo exceder o timeout ou for encerrado por sinal:

* Encerre o processo pelos mecanismos suportados pelo sistema operacional.
* Preserve a evidência do timeout ou do sinal.
* Classifique como `INCOMPLETE`. O estado `APPROVED` nunca é permitido nesses casos.
* O estado `REJECTED` só é permitido se a saída já capturada puder ser analisada com sucesso e indicar `high` ou `critical` (Caso A).
* Não alegue que processos filhos foram encerrados se isso não puder ser verificado. Documente a limitação, especialmente no Windows.

## 6.5 Separação entre lógica pura e execução

A classificação deve ser determinística e não executar comandos nem acessar a rede.

A camada de execução coleta os resultados (stdout, stderr, código de saída, sinal, timeout, falha de spawn) e os entrega à função classificadora. Cada resultado individual deve conter, além de `state` e das evidências, um campo estruturado `cause`. Para `INCOMPLETE` e `NOT RUN`, `cause` é obrigatório e deve ser uma destas categorias: `network`, `spawn`, `timeout`, `parse`, `missing-lockfile` ou `unknown`. Use `none` quando não houver uma falha operacional/de coleta a classificar (por exemplo, um relatório válido que resulte em `APPROVED` ou `REJECTED` por vulnerabilidades). O campo `cause` descreve a causa operacional, não substitui `state` nem o motivo de rejeição por vulnerabilidade. Se dados válidos já capturados demonstrarem vulnerabilidades `high`/`critical` mesmo após timeout/sinal, mantenha `REJECTED` conforme a seção 6.4.2 e preserve a causa operacional observada.

Classifique a causa a partir de evidência real, não de suposição: `network` exige evidência direta de erro de resolução/conexão do registry npm (por exemplo, códigos ou diagnósticos como `ENOTFOUND`, `EAI_AGAIN`, `ECONNREFUSED` ou `ECONNRESET` claramente associados ao registry; a lista de exemplos não substitui a verificação do contexto). `spawn` indica que o subprocesso local não iniciou; `timeout` indica que o timeout interno do script expirou; `parse` indica que a saída/estrutura não pôde ser analisada/validada; `missing-lockfile` indica que a auditoria não começou porque o lockfile exigido estava ausente; `unknown` cobre falhas sem evidência suficiente para uma categoria mais específica; `none` indica que não há falha operacional/de coleta identificada. Se o timeout interno terminar o processo antes de surgir uma evidência direta de rede, classifique como `timeout` (ou `unknown` se houver causas concorrentes/ambíguas), nunca como `network` por suposição; a exceção (f) não se aplica nesse caso. Se houver causas concorrentes ou ambíguas, utilize `unknown` para o resultado composto e preserve cada evidência individual.

A camada de agregação chama diretamente as funções internas de execução e classificação. Ela também deve emitir um `cause` agregado que não esconda múltiplas causas; só pode usar `network` quando as evidências mostrarem que a incompletude agregada decorre exclusivamente da indisponibilidade de rede da auditoria de produção e não houver causa técnica independente em `all`. Nos demais casos compostos/ambíguos, use `unknown`.

Exporte as funções necessárias para testes unitários sem executar npm ou acessar a rede.

Utilize injeção de dependências, mocks ou mecanismos equivalentes para testar a execução sem auditorias reais.

## 6.6 Casos obrigatórios de teste

Inclua testes para:

1. Relatório limpo e válido.
2. Vulnerabilidades baixas.
3. Vulnerabilidades moderadas.
4. Vulnerabilidades altas.
5. Vulnerabilidades críticas.
6. Código diferente de zero com vulnerabilidades altas ou críticas.
7. Código diferente de zero com JSON aparentemente limpo (`INCOMPLETE`).
8. Código zero com vulnerabilidades altas ou críticas (`REJECTED`).
9. JSON com a chave `error`.
10. JSON inválido.
11. Saída vazia.
12. Metadados ausentes.
13. Contagens inválidas (negativas, fracionárias, não numéricas).
14. Falha de execução (falha ao iniciar o processo).
15. Falha de rede.
16. Contagem zero com entrada `high` ou `critical` na lista (`REJECTED`).
17. Contagem `high` ou `critical` com lista vazia ou ausente (`REJECTED`).
18. Divergência entre contagem e lista sem `high`/`critical` (`INCOMPLETE`).
19. Lista `vulnerabilities` ausente com contagens zero (`INCOMPLETE`).
20. JSON truncado.
21. JSON corrompido.
22. Processo que excede o timeout.
23. Encerramento por sinal ou inesperado.
24. Preservação de stdout, stderr, código de saída e sinal quando disponíveis.
25. Ausência de aprovação quando não há evidência suficiente.
26. Falha ao encerrar um processo travado, sem mascarar a limitação.
27. Lógica de escolha do comando de execução por plataforma (Windows e Linux), com a plataforma injetada.
28. Auditoria não executada por falta de pré-requisitos (por exemplo, ausência de `package-lock.json`).
29. Importação do módulo sem executar auditorias, subprocessos, rede ou saída normal.
30. Detecção de execução direta (unitário, com argumentos injetados, incluindo `process.argv[1]` ausente).
31. Teste de integração do ponto de entrada (seção 6.3.2).
32. Rejeição de modo ausente ou desconhecido sem executar auditorias.
33. Rejeição de argumentos adicionais não permitidos.
34. Mensagem de uso com os três modos válidos.
35. Ausência de seleção automática de modo padrão.
36. Código de saída diferente de zero para argumentos inválidos.
37. Cada script npm de auditoria seleciona exclusivamente o modo fixo previsto (leia o `package.json` e verifique).
38. Fixture real e teste de classificação conforme a seção 6.4.1; qualquer fixture sintético deve estar identificado como sintético no teste e em `docs/SECURITY.md`.
39. Presença de `cause` obrigatória em resultados `INCOMPLETE` e `NOT RUN`, com valores restritos a `network`, `spawn`, `timeout`, `parse`, `missing-lockfile` ou `unknown`; `none` deve ser usado quando não houver falha operacional/de coleta a classificar.
40. Classificação de `network` baseada em evidência direta de erro de rede/registry e distinção de `spawn`, `timeout`, `parse` e `unknown`; mensagens genéricas ou evidências ambíguas não podem virar `network`.
41. `missing-lockfile` produz `NOT RUN` quando o processo de auditoria não chegou a iniciar por ausência do lockfile; a causa não pode ser apresentada como indisponibilidade de rede.
42. Preservação da causa operacional quando dados parciais válidos permitem `REJECTED` por `high`/`critical` apesar de timeout ou sinal, conforme a seção 6.4.2.
43. Emissão e coerência de linhas `AUDIT_PROD_CAUSE`, `AUDIT_ALL_CAUSE` e `AUDIT_AGGREGATE_CAUSE` com as evidências e os estados reais.
44. Propagação restrita da causa `network` ao agregado: só classificar `AUDIT_AGGREGATE_CAUSE=network` quando a incompletude decorrer exclusivamente da indisponibilidade comprovada da auditoria de produção e da mesma falha compartilhada de registry em `all`, sem causa técnica independente; o estado `network` de `all` pela mesma indisponibilidade **não** é causa independente por si só. Testar também o caso de causa independente/ambígua em `all`, no qual a exceção não pode ser aplicada.
45. Capturas manuais controladas da saída real de falha de rede do npm para árvore completa e produção conforme a seção 6.4.1 (incluindo invocação exata de `prod` quando diferente), com versão npm, comando, código de saída e diagnósticos observados; classificar os resultados separadamente sem inventar estrutura ou diagnóstico.
46. Ausência de falso `network`: uma falha com mensagem genérica, `spawn`, `timeout`, `parse`, `missing-lockfile` ou causa ambígua não deve ser reclassificada como `network`.
47. A tentativa de captura controlada não altera a configuração global/persistente do npm nem fornece a URL de registry por argumento do `audit.mjs`; o override limita-se ao ambiente do subprocesso e usa loopback confirmado indisponível.

Utilize mocks ou injeção de dependências, exceto no teste de integração do ponto de entrada, para que os testes não iniciem auditorias reais nem acessem a rede.

Os testes de modos inválidos devem comprovar que nenhuma função de execução de auditoria foi chamada.

A quantidade de testes, isoladamente, não prova cobertura adequada. Revise também os cenários de borda e o comportamento real. Não crie testes vazios ou redundantes para atingir uma contagem.

## 6.7 `audit:prod`

Execute uma auditoria equivalente a:

`npm audit --omit=dev --audit-level=high --json`

Essa auditoria é bloqueante.

O modo `prod`, executado por `node scripts/audit.mjs prod`, retorna código zero somente quando seu estado é `APPROVED`.

Se o resultado for `REJECTED`, `INCOMPLETE` ou `NOT RUN`, retorne código diferente de zero.

Não converta ausência de evidência em aprovação.

## 6.8 `audit:all`

Execute uma auditoria equivalente a:

`npm audit --audit-level=high --json`

O resultado deve incluir vulnerabilidades de dependências de desenvolvimento.

O estado e os resultados devem ser visíveis mesmo quando não bloquearem o processo agregado.

O modo `all`, executado por `node scripts/audit.mjs all`, retorna código zero somente quando seu estado é `APPROVED`.

Se o resultado for `REJECTED`, `INCOMPLETE` ou `NOT RUN`, retorne código diferente de zero.

A política de agregação não pode alterar o contrato do modo isolado.

## 6.9 Agregação: `audit`

O modo `aggregate`, executado por `node scripts/audit.mjs aggregate`, deve tentar executar as auditorias de produção e da árvore completa independentemente.

O agregador deve chamar diretamente as funções internas de execução e classificação do mesmo módulo. Não execute `npm run audit:prod`, `npm run audit:all` ou outros scripts npm como subprocessos intermediários.

Os subprocessos devem ser utilizados somente para executar o comando real `npm audit`.

O agregador deve:

* Tentar ambas as auditorias, mesmo que a primeira falhe.
* Preservar os resultados individuais.
* Preservar as evidências que justificam cada estado.
* Distinguir falha de execução, relatório inválido, vulnerabilidades encontradas e auditoria não executada.
* Não converter estados individuais em `APPROVED` porque o agregado foi aprovado.
* Não ocultar resultados ou códigos de saída individuais.
* Aplicar a política de saída agregada exclusivamente na função de agregação.

Política agregada:

| Auditoria de produção | Auditoria completa | Resultado agregado                                         |
| --------------------- | ------------------ | ---------------------------------------------------------- |
| `APPROVED`            | `APPROVED`         | `APPROVED`, código zero                                    |
| `APPROVED`            | `REJECTED`         | `APPROVED`, código zero, alerta de desenvolvimento         |
| `APPROVED`            | `INCOMPLETE`       | `APPROVED`, código zero, alerta de auditoria incompleta    |
| `APPROVED`            | `NOT RUN`          | `APPROVED`, código zero, alerta de auditoria não executada |
| `REJECTED`            | Qualquer estado    | `REJECTED`, código diferente de zero                       |
| `INCOMPLETE`          | Qualquer estado    | `INCOMPLETE`, código diferente de zero                     |
| `NOT RUN`             | Qualquer estado    | `INCOMPLETE`, código diferente de zero                     |

**A aprovação agregada significa exclusivamente que a política de bloqueio de produção foi satisfeita. Ela não significa que todas as verificações passaram nem que toda a árvore de dependências está livre de vulnerabilidades.**

Se a produção falhar, tente a auditoria completa independentemente, registre seu resultado e preserve a falha bloqueante de produção.

Se a produção for aprovada e a auditoria completa encontrar vulnerabilidades altas ou críticas exclusivamente em dependências de desenvolvimento, o agregado poderá ser aprovado, mas deverá exibir um alerta claro.

A mesma política se aplica à falha de execução da auditoria completa: ela não bloqueia o agregado quando a produção está aprovada, mas o resultado individual deve permanecer `INCOMPLETE` ou `NOT RUN`.

Essa exceção não autoriza `audit:all` isolado a retornar código zero quando não estiver aprovado.

### 6.9.1 Apresentação inequívoca do agregado

A apresentação deve mostrar, separadamente:

* Estado e código de saída da auditoria de produção.
* Estado e código de saída da auditoria completa.
* Evidências e motivos de falhas.
* Alertas de vulnerabilidades.
* Estado e código de saída agregados.

Emita, para cada estado, uma linha em formato fixo e fácil de analisar por scripts (por exemplo, `AUDIT_PROD_STATE=APPROVED`, `AUDIT_PROD_CAUSE=none`, `AUDIT_ALL_STATE=INCOMPLETE`, `AUDIT_ALL_CAUSE=network`, `AUDIT_AGGREGATE_STATE=INCOMPLETE`, `AUDIT_AGGREGATE_CAUSE=network`), de modo que o workflow possa montar o resumo sem reinterpretar texto livre. Os campos de causa são obrigatórios, com a taxonomia e as condições de uso da seção 6.5; `none` só se aplica quando não houver falha operacional/de coleta a classificar. Não infira `network` de um erro genérico.

Quando o agregado retornar zero e a auditoria completa não estiver aprovada, imprima um aviso explícito equivalente a:

`A política de produção foi satisfeita. A auditoria completa NÃO foi aprovada. Consulte o estado individual e os alertas.`

Não utilize mensagens genéricas como "todas as auditorias passaram" quando isso não for verdade.

## 6.10 Testes específicos do agregador

Teste obrigatoriamente:

* Produção aprovada e auditoria completa aprovada.
* Produção aprovada e auditoria completa rejeitada.
* Produção aprovada e auditoria completa incompleta.
* Produção aprovada e auditoria completa não executada.
* Produção rejeitada e auditoria completa aprovada.
* Produção rejeitada e auditoria completa rejeitada.
* Produção incompleta e auditoria completa aprovada.
* Falha de execução em uma auditoria.
* Falha nas duas auditorias.
* Continuação da segunda auditoria após falha da primeira.
* Impossibilidade de a auditoria completa mascarar falha de produção.
* Correspondência entre saída final, linhas de estado e política agregada.
* `audit:all` isolado retornando código diferente de zero quando não aprovado.
* Agregado retornando zero quando a produção está aprovada e a auditoria completa falha, conforme a política, com o aviso explícito da seção 6.9.1.
* Preservação dos resultados individuais classificados pelas funções internas.
* Uso de subprocessos apenas para executar o `npm audit` real.
* Rejeição de modos inválidos antes de qualquer auditoria.
* Ausência de subprocessos para argumentos ausentes ou adicionais não permitidos.
* Preservação dos estados individuais quando um processo sofre timeout.
* Preservação da falha de produção mesmo quando a auditoria completa falha de outra maneira.
* `prod` e `all` com falha de rede comprovada causada pela mesma indisponibilidade do registry: `aggregate` pode usar `cause=network` somente quando a evidência demonstrar a causa comum.
* `prod` com `cause=network` e `all` com causa independente (`spawn`, `timeout`, `parse`, `missing-lockfile` ou `unknown`): o agregado não pode ocultar a causa independente nem receber automaticamente a exceção de rede da Fase 1A.
* Emissão de `AUDIT_PROD_CAUSE`, `AUDIT_ALL_CAUSE` e `AUDIT_AGGREGATE_CAUSE` coerente com os estados e as evidências.
* Causa agregada `network` apenas quando o resultado agregado `INCOMPLETE` decorre exclusivamente da mesma indisponibilidade comprovada na produção, sem causa técnica independente em `all`; testar cenários em que a exceção não se propaga.

## 6.11 Documentação da auditoria

Documente em `docs/SECURITY.md`:

* Escopo de cada auditoria.
* Diferença entre dependências de produção e desenvolvimento.
* Fonte da verdade das contagens e validação cruzada com a lista.
* Relação entre contagens e lista validada com saída real conforme a seção 6.4.1; identificação explícita dos fixtures sintéticos; registro separado das capturas manuais controladas de falha de rede para a árvore completa e para produção (incluindo a invocação exata de `prod` quando diferente) e dos códigos realmente observados.
* Campo `cause`, categorias aceitas e precedência de classificação, evidências necessárias para `network` e diferença entre `INCOMPLETE` e `NOT RUN`, incluindo `missing-lockfile`.
* Causa comum de rede em `prod` e `all` versus causa técnica independente na agregação.
* Estados e critérios de classificação, incluindo o princípio central da seção 6.4.2.
* Linhas de estado e causa emitidas pela CLI e regra de propagação restrita da causa ao agregado.
* Códigos de saída individuais e agregados.
* Falhas de rede e execução.
* JSON inválido, truncado ou incompleto.
* Timeout (valor da constante) e encerramento de processos.
* Política de agregação.
* Funções de execução, classificação e agregação.
* Preservação dos resultados individuais.
* Importação sem efeitos colaterais e detecção de execução direta (método e limitações).
* Teste de integração do ponto de entrada.
* Modos fixos e scripts npm.
* Rejeição de argumentos ausentes, desconhecidos ou adicionais.
* Compatibilidade e limitações específicas de Windows e Linux.
* Riscos conhecidos e procedimento para registrar exceções.
* Proibição de utilizar `npm audit fix --force` sem autorização explícita.

---
