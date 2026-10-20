---
name: ci-dependabot
description: "GitHub Actions, CI, segurança de tokens, pinagem de actions e Dependabot. Use ao construir ou revisar a automação CI."
---

# CI e Dependabot

## 7.1 Estrutura do workflow

Configure um workflow para pull requests e para a branch principal.

Estruture o workflow para que a auditoria seja tentada **independentemente** do resultado das demais verificações. Duas abordagens são aceitas; escolha uma e documente-a:

* **Jobs independentes (preferível):** um job de qualidade e um job de auditoria, sem dependência (`needs`) entre eles.
* **Job único com `if: always()`:** etapas de auditoria com `if: always()` após as demais.

### Job de qualidade

* Selecione Node.js a partir de `.nvmrc`, usando `node-version-file`.
* Execute `npm ci`.
* Execute `npm run check`.
* **Execute em matriz com pelo menos `ubuntu-latest` e `windows-latest`**, para verificar a compatibilidade declarada com Windows (finais de linha, formatação, execução direta do script, testes). Use `fail-fast: false` para que o resultado de cada sistema seja preservado.
* Se a execução no Windows falhar, registre e investigue a causa. Não remova o Windows da matriz para obter um resultado verde.
* Se a matriz com Windows não puder ser mantida, registre a compatibilidade com Windows como `PENDING MANUAL`.

### Job de auditoria

* Execute em `ubuntu-latest`.
* Faça checkout e selecione Node.js a partir de `.nvmrc`.
* A auditoria usa o `package-lock.json` e apenas módulos nativos do Node.js; **não depende de `npm ci`**. Não execute `npm ci` neste job a menos que haja necessidade técnica documentada.
* Execute a auditoria de produção como etapa bloqueante.
* Execute a auditoria completa de forma independente, com `continue-on-error: true` ou estratégia equivalente.
* Registre os estados individuais e o resumo.

Não configure publicação ou implantação.

Somente a auditoria de produção bloqueia a CI por vulnerabilidades de dependências. Falhas obrigatórias de instalação, tipos, lint, formatação, testes ou build continuam bloqueando a CI conforme suas próprias regras.

## 7.2 Estados da CI

Utilize os estados `APPROVED`, `REJECTED`, `NOT RUN`, `INCOMPLETE`, `PENDING MANUAL` e `NOT APPLICABLE` conforme a seção 2. Justifique qualquer `NOT APPLICABLE`.

Não apresente verificações não executadas ou incompletas como aprovadas.

## 7.3 Preservação de resultados

A implementação deve:

1. Tentar executar as auditorias quando houver condições técnicas para isso.
2. Registrar se cada etapa realmente iniciou.
3. Capturar o resultado real.
4. Registrar `NOT RUN` ou `INCOMPLETE` quando aplicável.
5. Preservar a falha original de `npm ci` e de `npm run check`.
6. Manter a auditoria de produção bloqueante.
7. Não transformar uma auditoria completa não executada em aprovada.
8. Não permitir que `continue-on-error: true` esconda resultados no resumo.
9. Não permitir que a auditoria completa neutralize falhas obrigatórias.

Se for utilizado `if: always()`, lembre que ele não garante que uma auditoria conseguirá iniciar ou terminar com sucesso. Se não houver Node.js, lockfile ou condições técnicas para iniciar a auditoria, registre o motivo real e o estado correspondente. Não declare que uma auditoria foi tentada se a execução nunca começou.

A falha não bloqueante de uma etapa não pode ser apresentada como sucesso técnico daquela verificação.

## 7.4 Resumo da CI

O resumo deve ser escrito em `$GITHUB_STEP_SUMMARY`.

**Com `continue-on-error: true`, utilize `steps.<id>.outcome` (o resultado real, que permanece `failure`) e não `steps.<id>.conclusion` (que passa a `success`) para determinar o estado de uma etapa.** Utilize também as linhas de estado e causa emitidas pelo script (seção 6.9.1); não deduza a causa apenas pelo texto livre nem mascare as causas individuais.

Apresente separadamente:

* Instalação de dependências.
* Verificações de qualidade por sistema operacional.
* Auditoria de produção.
* Auditoria completa.
* Estado de cada etapa.
* Motivo de cada falha.
* Verificações não executadas.
* Pendências manuais.

Quando a auditoria de produção estiver aprovada e a completa falhar, informe que a política de produção foi satisfeita, mas que a auditoria completa permanece reprovada ou incompleta.

O resumo deve deixar claro que **o código zero do agregado não significa que todas as verificações passaram**.

## 7.5 Permissões e segredos

Utilize permissões mínimas para `GITHUB_TOKEN`.

Quando suficiente, declare:

```yaml
permissions:
  contents: read
```

Não conceda permissões adicionais sem justificativa técnica documentada.

Não solicite segredos desnecessários, publique artefatos externos, realize deploy, habilite auto-merge do Dependabot ou exponha arquivos de ambiente.

## 7.6 Pinagem das GitHub Actions

Utilize hashes SHA completos verificados em fontes oficiais e comentários com a versão correspondente.

Não invente hashes.

Se a verificação oficial estiver indisponível, não declare a pinagem aprovada. Utilize somente a alternativa de versão exata autorizada pela política do projeto, documente a limitação e registre `PENDING MANUAL`.

## 7.7 Dependabot

Configure pull requests de atualização para:

* Dependências npm.
* GitHub Actions.

Não habilite auto-merge nem atualizações silenciosas.

## 7.8 Verificação do workflow

O workflow não pode ser executado localmente. Portanto:

* Revise o YAML manualmente: sintaxe, estrutura de jobs, matriz, condições, `permissions`, uso de `outcome`, ausência de segredos e de deploy.
* Não adicione ferramentas de lint de workflow como dependência sem justificativa e autorização.
* Registre o comportamento do workflow como `PENDING MANUAL` até que ele seja executado no GitHub, e não declare a CI validada antes disso.
* Os comportamentos "falha de `npm ci` não impede a tentativa da auditoria" e "falhas anteriores são preservadas" são propriedades do workflow, e não do `audit.mjs`: verifique-os por revisão da estrutura do YAML, e não por testes unitários do script.

---
