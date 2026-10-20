---
name: seguranca-permissoes
description: "Proteção de arquivos de ambiente, configuração e confirmação de permissões, isolamento e canário de segurança. Use antes de qualquer tarefa que toque configurações, permissões ou a Fase 1B."
---

# 3. SEGURANÇA, ARQUIVOS DE AMBIENTE E PERMISSÕES

## 3.1 Arquivos protegidos

Considere protegidos, no mínimo:

* `.env`
* `.env.local`
* `.env.development`
* `.env.production`
* `.env.test`
* `.env.fake-test`

Não leia, imprima, copie, registre em logs ou exponha seus valores.

A proteção abrange operações equivalentes por terminal, ferramentas de leitura, processos auxiliares e outros caminhos disponíveis.

**Exceção única e limitada:** a tentativa controlada de leitura de `.env.fake-test` descrita na seção 3.5, cujo conteúdo é fictício e jamais deve ser impresso ou registrado. Essa exceção não se estende a nenhum outro arquivo de ambiente.

`.env.example` pode conter apenas valores fictícios. Nunca inclua credenciais reais.

Variáveis com prefixo `VITE_` são expostas ao cliente e não devem conter segredos.

## 3.2 Configuração de permissões

Configure `.claude/settings.json` com a sintaxe e os recursos oficialmente suportados pela versão do Claude Code em uso. Consulte a [documentação oficial de permissões](https://code.claude.com/docs/en/permissions) e a [referência de configurações](https://code.claude.com/docs/en/settings), e confirme os comportamentos na versão realmente instalada.

Restrinja, conforme as capacidades reais da ferramenta:

* Leitura direta dos arquivos de ambiente protegidos.
* Leitura pelo terminal, incluindo comandos equivalentes a `cat`, `type` e `Get-Content`.
* Operações destrutivas, como `git reset --hard`, `git clean` e exclusões recursivas.
* `git push`.
* Publicação, implantação e outras operações remotas não autorizadas.

Não presuma suporte, sem conferência, a padrões, curingas ou exceções. A documentação oficial atual descreve padrões compatíveis com sintaxe Git-ignore para regras `Read`/`Edit`, incluindo `*`, `**` e negações `!`, mas os recursos e detalhes podem variar por versão. Verifique a documentação da versão instalada antes de usá-los. Uma negação `!` só pode excluir caminhos cobertos por padrões anteriores da mesma origem de configuração; ela não supera regras `deny` de outra origem nem reabre um diretório bloqueado por inteiro. A precedência continua sendo `deny`, `ask`, `allow`.

Use nomes de arquivos protegidos e padrões tão explícitos quanto possível. A documentação distingue regras como `Read(.env)` (que, conforme o tipo de padrão/regra, pode corresponder a arquivos homônimos em diferentes níveis) de `Read(./.env)` (ancorada no diretório de trabalho atual). Confirme os efeitos de cada padrão real e teste-os. Se uma regra ampla impedir o funcionamento de `.env.example`, só considere uma exceção `!` se a versão instalada a documentar e se o recorte puder ser verificado na mesma origem da regra; não use `allow` para tentar superar um `deny`. Se não houver configuração compatível demonstravelmente segura, mantenha a proteção dos arquivos reais e documente o conflito.

## 3.3 Verificação das permissões carregadas

A existência de `.claude/settings.json` não prova que as regras estão ativas. Consulte também a [documentação oficial de configurações](https://code.claude.com/docs/en/settings) e a [referência da CLI](https://code.claude.com/docs/en/cli-reference) para verificar os meios de diagnóstico disponíveis na versão instalada.

Na Fase 1A, confirme que o arquivo foi criado, que sua sintaxe é compatível com a documentação oficial da versão em uso e que as verificações possíveis antes de reiniciar foram realizadas e registradas. Se a confirmação do carregamento depender de reinicialização, não a declare como concluída na Fase 1A.

Na Fase 1B, antes do canário, realize a reinicialização quando necessária e confirme, pelos mecanismos oficiais disponíveis:

* Se as configurações foram reconhecidas.
* Se as regras relevantes estão carregadas.
* Se há conflitos ou avisos.
* Quais limitações permanecem.
* Se ainda existe alguma condição que exija reinicialização.

Como `/permissions` é um diálogo interativo, o usuário deve abri-lo na sessão da Fase 1B, revisar as regras relevantes e confirmar explicitamente que foram reconhecidas/carregadas e quais arquivos de configuração as fornecem. Solicite essa confirmação antes do canário e registre-a como **verificação manual confirmada pelo usuário**, não como teste automatizado. O assistente não deve alegar que abriu ou verificou o diálogo por conta própria. Se a confirmação não for fornecida, registre o carregamento como `PENDING MANUAL`, mantenha o canário `NOT RUN` e interrompa as operações dependentes. O canário fornece evidência adicional sobre os caminhos efetivamente testados, mas não substitui a confirmação de configuração.

Não invente comandos de diagnóstico nem suponha que a configuração local substitui políticas superiores.

## 3.4 Limites das permissões e isolamento real

Diferencie explicitamente:

1. Regras de permissão do Claude Code.
2. Restrições do ambiente de execução.
3. Controles do sistema operacional.
4. Isolamento efetivo por usuário, permissões de arquivos ou sandbox.

**Uma configuração de permissões do Claude Code não equivale automaticamente a uma sandbox nem garante isolamento absoluto do sistema operacional.**

Não afirme que o ambiente está isolado sem evidências da implementação e de sua eficácia.

Não tente contornar restrições por comandos alternativos, processos filhos, scripts, codificação de conteúdo ou outras ferramentas.

A documentação oficial esclarece que as regras `Read`/`Edit` também são aplicadas a certas ferramentas e comandos de arquivo reconhecidos no Bash, como `cat`, `head`, `tail`, `sed`, `tee` e redirecionamentos. Isso não cobre de forma geral comandos que leem arquivos sem nomeá-los, como certas formas de `grep -r`, nem subprocessos arbitrários que abrem arquivos por conta própria. Da mesma forma, regras textuais do Bash não constituem fronteira de segurança: por exemplo, `Bash(git push *)` não cobre todas as formas alternativas de invocar Git e `Bash(rm *)` não cobre necessariamente `/bin/rm` ou chamadas encapsuladas por `bash -c`. Confirme os limites na documentação da versão instalada. Se for necessário impedir acesso no nível do sistema operacional a todos os processos, avalie o sandbox suportado e adequado ao ambiente; não afirme que regras de prompt ou de permissão textual equivalem a esse isolamento.

Não enfraqueça permissões sem autorização explícita.

## 3.5 Canário de permissões

O canário avalia se as proteções contra leitura de arquivos de ambiente funcionam pelos caminhos relevantes, sem expor conteúdo nem enfraquecer regras.

Deve ser avaliado no início da Fase 1B, antes de operações que dependam dessa verificação.

### 3.5.1 Autorização específica

A autorização abaixo é específica à sessão em que o usuário forneceu explicitamente o texto completo desta seção 3.5. Ao iniciar a Fase 1B em uma sessão nova, o usuário deve fornecer novamente esta seção 3.5, junto com a mensagem de continuidade. Sem isso, o canário permanece `NOT RUN` e não deve ser improvisado. O `CLAUDE.md`, o `docs/SECURITY.md` e o resumo de continuidade documentam o procedimento, mas não concedem por si sós esta autorização.

O texto desta seção autoriza:

* Tentar criar `.env.fake-test` com conteúdo estritamente fictício, somente pelos mecanismos permitidos.
* Ler de forma controlada esse arquivo pelos dois caminhos previstos, somente quando houver evidência de que estava ausente no início do canário e de que foi criado nesta execução ou criado pelo usuário, nesta mesma sessão, em resposta ao pedido explícito do canário.
* Remover o arquivo por conta do Claude Code somente se houver evidência de que o próprio Claude Code o criou nesta execução.

Se a criação pelo Claude Code for bloqueada, ele pode pedir ao usuário que crie o arquivo fictício para o canário. Depois que o usuário confirmar explicitamente que o criou em resposta a esse pedido na sessão atual, o arquivo pode ser usado apenas para as tentativas controladas de leitura; a remoção fica a cargo do usuário. Não trate como arquivo autorizado um arquivo que já existia no primeiro exame ou cuja origem temporal não possa ser confirmada.

Nenhum outro arquivo pode ser removido ou lido com base nessa autorização. A exceção não libera a leitura irrestrita do arquivo, o registro do seu conteúdo nem a criação de exceções nas regras de proteção.

### 3.5.2 Procedimento

1. No início do canário, verifique somente a existência de `.env.fake-test`, sem ler seu conteúdo, e registre se ele já existia.
2. Se o arquivo já existia no primeiro exame, não o abra, altere ou remova. Registre o canário como `NOT RUN` e solicite orientação. Não o reclassifique como arquivo de teste criado nesta sessão.
3. Se o arquivo estava ausente, tente criá-lo com valores fictícios mínimos usando somente um mecanismo permitido. Uma regra `Read` de negação pode bloquear também ferramentas de escrita/edição no mesmo caminho, inclusive a criação, dependendo da versão e da configuração. Consulte a documentação da versão instalada; não tente contornar a proteção por outro comando ou ferramenta.
4. Se a criação for bloqueada ou insegura, registre a evidência de bloqueio da criação, mas **não** classifique por isso as tentativas de leitura como `BLOCKED`. Peça ao usuário que crie o arquivo fictício para este canário e aguarde confirmação explícita. Se o usuário confirmar que o criou em resposta ao pedido nesta mesma sessão, prossiga com os testes de leitura sem revelar o conteúdo. Se não houver confirmação, mantenha o canário `NOT RUN`.
5. Se o Claude Code criou o arquivo nesta execução após confirmar que ele estava ausente, prossiga diretamente aos testes. Se foi criado pelo usuário após o pedido do item 4, prossiga somente depois da confirmação explícita do usuário. Em ambos os casos, teste separadamente o bloqueio pela ferramenta de leitura e pelo terminal.
6. Registre exatamente qual ferramenta de leitura e qual comando de terminal foram testados (por exemplo, `cat .env.fake-test` ou `Get-Content .env.fake-test`, conforme o shell real). Não generalize o resultado para comandos ou ferramentas não testados. A documentação atual descreve cobertura de comandos de arquivo que o Claude Code reconhece e de redirecionamentos, mas essa cobertura não é universal: certos usos de `grep -r` que não nomeiam o arquivo e subprocessos arbitrários que abrem arquivos por conta própria podem não ser cobertos por regras `Read`/`Edit`. Para bloqueio efetivo no nível do sistema operacional, avalie sandboxing adequado.
7. Não imprima nem registre qualquer conteúdo do arquivo, nem o leia por caminhos alternativos se alguma tentativa for permitida. Registre apenas a ferramenta/comando, se a tentativa chegou ao mecanismo de permissão e o resultado observado.
8. Classifique cada caminho separadamente com base na evidência efetivamente observada. Um erro por arquivo inexistente, uma falha genérica ou uma tentativa que não chegou à avaliação de permissão não prova `BLOCKED`.
9. Se o Claude Code criou o arquivo nesta execução, remova-o somente quando a origem estiver comprovada e for seguro fazê-lo; depois verifique a remoção sem ler ou exibir o conteúdo.
10. Se o usuário criou o arquivo em resposta ao canário, não o remova nem tente removê-lo. Informe que o usuário deve removê-lo após as tentativas e confirmar a limpeza. Não realize commit enquanto esse arquivo de teste permanecer no projeto.
11. Inclua `.env.fake-test` no `.gitignore` durante a fase autorizada e confirme a regra sem ler o conteúdo do arquivo. Não altere arquivos fora do escopo da fase.
12. Registre as limitações encontradas, incluindo comandos/ferramentas não testados, comportamento de criação bloqueada e a responsabilidade por limpeza.

Se um arquivo criado pelo Claude Code nesta execução não puder ser removido com segurança, não contorne as restrições. Informe sua localização sem revelar conteúdo e aguarde orientação. Se o arquivo tiver sido criado pelo usuário, a limpeza é responsabilidade dele; aguarde a confirmação de remoção antes de qualquer commit.

### 3.5.3 Estados do canário

* `BLOCKED`: evidência suficiente de que o mecanismo de permissão impediu a tentativa.
* `ALLOWED`: a operação de leitura foi efetivamente permitida, representando falha da proteção esperada.
* `INCONCLUSIVE`: evidência insuficiente para determinar a causa.
* `NOT RUN`: teste não executado.

Uma tentativa que nunca chegou ao mecanismo de permissão não pode ser classificada como `BLOCKED`.

O canário não pode ser aprovado se um caminho obrigatório estiver `ALLOWED`, `INCONCLUSIVE` ou `NOT RUN`.

A proteção dos arquivos reais prevalece sobre a conclusão do canário.

**Interpretação do resultado:** exigir `BLOCKED` nos dois caminhos é intencional e o canário pode revelar uma proteção insuficiente, especialmente pelo terminal. Se algum caminho resultar em `ALLOWED`, isso é uma falha real do controle avaliado; `INCONCLUSIVE` ou `NOT RUN` também não comprovam bloqueio. Não trate esses resultados como defeito do prompt nem force uma aprovação, enfraqueça a regra ou tente um caminho alternativo para obter `BLOCKED`. Registre a evidência sem conteúdo do arquivo. A continuação de tarefas de interface após falha do canário segue exclusivamente a regra da seção 2.4. Qualquer autorização de continuação, avanço de fase ou commit é separada, não altera o estado do canário nem libera operações que dependam diretamente de uma proteção ainda não confirmada.

---
