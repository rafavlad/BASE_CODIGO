---
name: qualidade-acessibilidade
description: "Acessibilidade, responsividade, metadados, verificações finais, regressão e revisão de diff. Use ao criar ou validar interface e antes de concluir fases."
---

# Qualidade e acessibilidade

## 9.1 Acessibilidade

Utilize WCAG 2.2 nível AA como objetivo técnico.

Não declare conformidade integral sem evidências suficientes.

Implemente, conforme aplicável:

* HTML semântico.
* Idioma `pt-BR`.
* Navegação por teclado.
* Foco visível.
* Link para pular ao conteúdo principal.
* Labels associados aos campos.
* Erros associados aos controles.
* Contraste adequado.
* Estados que não dependam exclusivamente de cores.
* Respeito a `prefers-reduced-motion`.
* Gestão de foco em modais.
* Fechamento de modal por Escape.
* Restauração do foco ao fechar modal.
* Ordem lógica de leitura.
* Nomes acessíveis para controles.

Utilize `eslint-plugin-jsx-a11y` e axe quando compatíveis e autorizados.

Testes automatizados com jsdom não verificam adequadamente o contraste de cores. Registre o contraste como `PENDING MANUAL` quando não houver verificação em navegador ou ferramenta apropriada.

## 9.2 Responsividade

Adote abordagem mobile-first.

Verifique, no mínimo, 360 px, 768 px e 1280 px.

Investigue overflow horizontal, controles inacessíveis, textos cortados, espaçamentos e comportamento dos componentes.

Se não houver navegador ou ferramenta visual disponível, registre as verificações como `NOT RUN` ou `PENDING MANUAL`. Não invente resultados.

## 9.3 Metadados

Configure em `index.html`:

* Idioma.
* Título.
* Descrição.
* Viewport.
* Favicon.
* Conteúdo alternativo sem JavaScript, quando aplicável.

Não inclua informações de SEO específicas de um cliente que não tenham sido fornecidas.

---

## Testes, verificação final e revisão do diff

## 10.1 Testes gerais

Implemente testes comportamentais pertinentes para:

* Renderização e estados dos componentes.
* Validação válida e inválida do formulário.
* Associação e exibição de erros.
* Modal, fechamento por Escape e foco.
* Navegação entre páginas.
* Página não encontrada.
* Configuração do cliente.
* Tratamento de erros.
* Classificação, execução e agregação de auditorias.

Não crie testes que apenas confirmem a existência de uma função. Teste o comportamento esperado.

Os testes de auditoria em `scripts/` devem integrar a suíte normal do Vitest e ser executados por `npm run test` e `npm run check`.

Os requisitos específicos de teste da auditoria estão na seção 6. Não os substitua por uma lista menor nesta seção.

## 10.2 Verificações finais

Execute:

* `npm run typecheck`
* `npm run lint`
* `npm run format:check`
* `npm run test`
* `npm run build`
* `npm run audit:prod`
* `npm run audit:all`
* `npm run audit`
* `npm run check`

Evite repetições desnecessárias, mas não omita verificações obrigatórias.

Em projeto existente, execute `npm run format:check`; não execute cegamente `npm run format` se ele reformatar todo o repositório. Aplique formatação automática somente aos arquivos criados na fase atual, usando uma lista explícita de arquivos. Se o script `format` não permitir escopo seguro, utilize o Prettier com caminhos explícitos ou não aplique formatação automática. Não reformate arquivos preexistentes do usuário sem autorização.

Registre os resultados reais de cada comando. Se um comando não puder ser executado, informe o motivo e o estado correto.

Respeite os contratos de saída da seção 6. Não declare `audit:all` aprovada porque o agregado retornou zero.

Execute também, manualmente, `node scripts/audit.mjs` sem argumentos e confirme código diferente de zero e mensagem de uso.

## 10.3 Revisão de alterações, regressão e integridade

Antes de realizar a primeira alteração autorizada de cada fase, registre um retrato do estado inicial relevante do repositório, sem expor segredos. Registre-o somente no relatório da fase e no resumo de continuidade, nunca em arquivos do projeto. Inclua, conforme aplicável:

* estado do Git e branch atual;
* alterações rastreadas já existentes;
* arquivos não rastreados relevantes;
* arquivos existentes que serão modificados;
* estado dos testes e verificações pertinentes antes da alteração, quando for viável executá-los sem modificar o ambiente fora do escopo.

Use comandos somente de leitura para essa inspeção. Não adicione, remova ou limpe arquivos para simplificar a comparação. Testes de baseline podem gerar caches conhecidos em diretórios ignorados, como `node_modules/.vite`; identifique esses artefatos e exclua-os apenas da comparação lógica do estado do projeto, sem ocultar arquivos ou alterações desconhecidas. Não use essa exceção para ignorar arquivos não rastreados fora de caches esperados. Não limpe caches ou diretórios automaticamente. Se o estado inicial não puder ser determinado com segurança, interrompa as alterações que dependam dessa informação e solicite orientação.

Antes de finalizar a fase, compare o estado final com o retrato inicial. Identifique arquivos criados, alterados, removidos, renomeados e não rastreados, distinguindo mudanças próprias da fase de alterações preexistentes ou externas. Se `.claude/settings.local.json` aparecer, confira se está coberto pelo `.gitignore`, registre sua presença e a causa provável sem expor informação sensível e preserve-o; não o apague automaticamente nem suponha que uma aprovação de edição de arquivo necessariamente o criou. Para o significado de **etapa**, use a definição da regra 28 da seção 1.2: cada item da sequência operacional da fase, não cada comando ou microação. Mantenha a lista de arquivos criados por etapa acumulada e registre-a no relatório final e no resumo de continuidade. Não produza relatórios intermediários por etapa. A comparação deve considerar caches reconhecidos e ignorados conforme o parágrafo anterior, sem tratá-los como mudanças funcionais do projeto. Toda outra diferença inesperada deve ser investigada. Não descarte uma diferença para produzir um diff limpo.

### Testes de regressão

Antes de finalizar uma fase:
* Execute os testes existentes relevantes para as funcionalidades afetadas.
* Acrescente ou ajuste testes para cobrir o comportamento novo ou corrigido.
* Execute a suíte obrigatória definida nas seções 5, 6 e 10.
* Compare falhas com o estado inicial quando houver evidência de uma falha preexistente.
* Não classifique uma falha preexistente como causada pela mudança sem evidência, mas também não a ignore: registre-a e avalie se impede a aprovação.
* Não reduza a suíte, desative testes ou altere expectativas válidas apenas para obter aprovação.

Se o diretório inicial estiver vazio e ainda não houver funcionalidades preexistentes, marque a regressão como `NOT APPLICABLE` nas Fases 0 e 1A; a partir da Fase 1B, use os testes aprovados da Fase 1A como baseline. Em projeto existente, se não houver testes para uma funcionalidade afetada, registre essa lacuna e crie testes pertinentes quando estiver dentro do escopo autorizado. Se não for possível testar uma regressão relevante que seja aplicável, registre `NOT RUN` ou `PENDING MANUAL`; não afirme que a regressão foi descartada.

### Revisão final

Antes de finalizar uma fase:

* Revise o diff completo.
* Verifique arquivos criados, alterados e removidos.
* Verifique arquivos temporários.
* Verifique segredos e dados de ambiente.
* Confirme que alterações do usuário foram preservadas.
* Confirme que nenhum arquivo fora do escopo foi modificado indevidamente.
* Verifique a coerência entre código, scripts e documentação.
* Confira os estados e códigos de saída das auditorias.
* Confirme que testes não foram desativados ou ignorados para obter aprovação.
* Confirme que testes em `src/` e `scripts/` foram descobertos pelo Vitest e que `scripts/` roda em ambiente `node`.
* Confirme que o ESLint analisa `scripts/` sem regras desativadas.
* Confirme que estados individuais não foram falsamente aprovados pelo agregado.
* Confirme que a importação do módulo de auditoria não executa operações.
* Confirme que o ponto de entrada dispara (teste de integração e execução manual).
* Confirme que argumentos inválidos não iniciam auditorias.
* Confirme que os scripts npm usam os modos fixos previstos.
* Confirme que os cenários de falha de execução, timeout, JSON truncado e diferenças de plataforma foram testados ou documentados como pendentes.
* Revise o YAML do workflow conforme a seção 7.8.

A implementação não está concluída apenas porque o código foi escrito.

---
