---
name: arquitetura-stack
description: "Estrutura do projeto, tecnologias, TypeScript, testes, lint, scripts npm, Node, lockfile e gitignore. Use para planejar ou implementar a fundação técnica."
---

# Arquitetura e stack

Utilize esta estrutura como referência:

```text
project/
├── .claude/
│   └── settings.json
├── .github/
│   ├── workflows/
│   └── dependabot.yml
├── docs/
│   ├── SECURITY.md
│   ├── WORKFLOW.md
│   ├── PRIVACY-LGPD.md
│   ├── MODULES.md
│   ├── CLIENT-PROMPT-TEMPLATE.md
│   └── REGISTRO-ALTERACOES.md
├── public/
├── scripts/
│   └── audit.mjs
├── src/
│   ├── components/
│   │   └── ui/
│   ├── layouts/
│   ├── pages/
│   ├── schemas/
│   ├── styles/
│   ├── client/
│   │   ├── content/
│   │   ├── theme.css
│   │   └── site.config.ts
│   ├── App.tsx
│   └── main.tsx
├── .env.example
├── .gitattributes
├── .gitignore
├── .nvmrc
├── CLAUDE.md
├── CHANGELOG.md
├── README.md
├── eslint.config.js
├── index.html
├── package.json
├── package-lock.json
├── tsconfig.json
└── vite.config.ts
```

Adapte a estrutura somente com justificativa técnica concreta.

* Não crie arquivos vazios sem utilidade.
* Não duplique configurações do Vitest sem necessidade. Um `vitest.config.ts` separado é aceito se for tecnicamente necessário (ver seção 5.4) e deve ser documentado.
* Permita que npm gere o `package-lock.json`.
* Preserve arquivos existentes fora do escopo.
* Não mova ou substitua arquivos do usuário sem autorização.
* Documente desvios da estrutura prevista.

Arquivos de teste podem ser criados em `src/` e `scripts/` quando houver propósito técnico.

---

## Stack, configurações e scripts

## 5.1 Tecnologias da Fase 1A

Utilize:

* React.
* TypeScript em modo estrito.
* Vite.
* CSS nativo com variáveis.
* Vitest.
* Testing Library.
* ESLint com configuração flat.
* `typescript-eslint`.
* `eslint-plugin-react-hooks`.
* `eslint-plugin-jsx-a11y`.
* Prettier.
* Node.js e npm em versões compatíveis.

Verifique as versões e a compatibilidade antes de decidir a configuração.

## 5.2 Tecnologias da Fase 1B

Somente na Fase 1B, instale:

* `react-router`.
* `zod`.

Utilize Zod efetivamente na validação do formulário de contato.

Não implemente integrações fictícias para simular funcionalidades não autorizadas.

## 5.3 TypeScript

Configure obrigatoriamente:

* `strict: true`
* `noUncheckedIndexedAccess: true`
* `exactOptionalPropertyTypes: true`

Não remova essas opções por conveniência.

Em caso de incompatibilidade:

1. Identifique o erro.
2. Investigue a causa.
3. Procure uma solução compatível com o modo estrito.
4. Apresente evidências.
5. Se não houver solução segura sem alterar os requisitos, interrompa a operação dependente e solicite autorização explícita.

Não use `any`, conversões forçadas ou exclusões de verificações como atalhos silenciosos.

## 5.4 Testes e ambientes

Configure testes executáveis desde a Fase 1A.

Não utilize `--passWithNoTests` para transformar a ausência de testes em aprovação.

Configure o Vitest para descobrir os testes relevantes em `src/` e `scripts/`, incluindo `.test.mjs` caso essa extensão seja escolhida.

**Ambientes de execução distintos:** os testes de componentes em `src/` exigem um ambiente de DOM (por exemplo, `jsdom`); os testes de `scripts/` devem rodar no ambiente `node`. Configure essa separação pela forma suportada na versão do Vitest escolhida (por exemplo, `projects`/workspace ou a diretiva `// @vitest-environment node` por arquivo). Verifique a forma suportada por consulta à documentação oficial da versão instalada; não presuma opções de versões anteriores. Um `vitest.config.ts` separado é aceito se necessário e deve ser documentado.

Confirme por execução real que os testes de auditoria são encontrados e executados e que rodam no ambiente `node`.

O comando de testes deve falhar quando testes obrigatórios não forem encontrados ou ocorrer uma falha real.

Não esconda testes com padrões restritivos, diretórios excluídos ou scripts que executem apenas parte da suíte.

Os testes específicos da auditoria são definidos na seção 6.

## 5.5 Lint de `scripts/`

O ESLint deve analisar `scripts/**/*.mjs` com um bloco de configuração próprio, com globais do Node.js (por exemplo, `process`, `console`) e regras compatíveis com ESM.

Declare os globais do Node de forma suportada (por exemplo, com o pacote `globals`, se for justificado como dependência de desenvolvimento, ou definindo-os manualmente).

Não desative `no-undef` nem outras regras para fazer o script passar. Não exclua `scripts/` do lint.

## 5.6 Scripts npm

Implemente scripts equivalentes a:

* `dev`
* `build`
* `preview`
* `typecheck`
* `lint`
* `format`
* `format:check`
* `test`
* `audit:prod`
* `audit:all`
* `audit`
* `check`

Os três scripts de auditoria devem usar o mesmo arquivo `scripts/audit.mjs`, com argumentos literais:

* `audit:prod`: `node scripts/audit.mjs prod`
* `audit:all`: `node scripts/audit.mjs all`
* `audit`: `node scripts/audit.mjs aggregate`

Não construa esses argumentos com entrada externa, variáveis arbitrárias ou interpolação dinâmica.

Não duplique a lógica de auditoria em outros arquivos.

O script `check` deve executar tipos, lint, verificação de formatação, testes e build.

`npm run test` deve executar os testes da aplicação e da auditoria. Consequentemente, `npm run check` também deve executar esses testes.

O script `audit` deve executar e avaliar separadamente as auditorias de produção e da árvore completa conforme a seção 6.

Não permita que scripts ignorem falhas obrigatórias e terminem com sucesso.

Os códigos de saída devem obedecer à seção 6.

## 5.7 Node.js, npm e finais de linha

* Utilize `private: true` no `package.json`.
* Declare requisitos compatíveis de Node.js e npm.
* Mantenha `.nvmrc` coerente com a versão selecionada.
* Documente alternativas para Windows, como nvm-windows, Volta ou fnm, sem exigir instalações adicionais.
* Configure finais de linha consistentes com `.gitattributes` e Prettier (`endOfLine: "lf"`).
* Utilize `npm install` para gerar o lockfile inicial.
* Utilize `npm ci` para instalações reproduzíveis depois que o lockfile existir.
* Não atualize dependências automaticamente fora do escopo autorizado.
* Não utilize correções forçadas de dependências.

Configure `.gitattributes` com comportamento equivalente a:

```gitattributes
* text=auto eol=lf
```

## 5.8 `.gitignore`

Inclua arquivos de ambiente protegidos, além de:

* `node_modules/`
* `dist/`
* `coverage/`
* Logs.
* Arquivos temporários.
* Arquivos locais de editores que não devam ser versionados.
* `.claude/settings.local.json`, que pode ser criado para persistir determinadas regras locais de permissão. Não presuma que toda aprovação de edição será persistida: confirme o comportamento na versão instalada e no diálogo utilizado.

Não ignore `.env.example`.

Verifique com `git check-ignore` que os arquivos protegidos e `.claude/settings.local.json` são ignorados, enquanto `.env.example` não é ignorado. Se `settings.local.json` aparecer no estado inicial/final, registre sua presença e a causa provável sem expor informação sensível, confirme se está ignorado e preserve-o; não o apague automaticamente nem suponha que uma aprovação de edição de arquivo necessariamente o criou.

Não crie arquivos com valores reais de ambiente para realizar essa verificação.

---
