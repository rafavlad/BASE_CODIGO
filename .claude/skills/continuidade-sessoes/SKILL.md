---
name: continuidade-sessoes
description: "Resumos verificáveis de continuidade e retomada segura entre fases/sessões. Use ao iniciar, interromper ou concluir uma sessão."
---

# Continuidade entre fases e sessões

O conjunto normativo desta fundação é composto pelo `CLAUDE.md` da raiz e pelas skills em `.claude/skills/*/SKILL.md`. Use `docs/INDICE-DO-PACOTE.md` para identificar a fonte normativa e carregar as skills relevantes à tarefa e à fase autorizada.

Em toda sessão, leia o `CLAUDE.md`, consulte o índice e carregue integralmente as skills obrigatórias para a fase atual antes de agir. As mensagens em `docs/MENSAGENS-DE-SESSAO.md` são atalhos de autorização e roteamento; não substituem as instruções normativas das skills.

Para cada nova sessão, use uma mensagem inicial que identifique o resumo de continuidade mais recente e autorize somente a fase pretendida. O resumo não substitui a inspeção do estado atual nem concede autorização nova. Para iniciar a Fase 1B em uma sessão nova, o usuário deve fornecer novamente, no contexto da sessão, o texto completo do procedimento “Canário de permissões” da skill `seguranca-permissoes` junto com o resumo de continuidade. Sem esse conteúdo na sessão, o canário permanece `NOT RUN`; não improvise nem infira autorização a partir dos arquivos do projeto.

Ao finalizar ou interromper uma fase, gere um resumo de continuidade curto, em tabela, para uma nova sessão. Ele deve permitir retomada segura sem repetir o relatório completo nem copiar logs extensos. Salve-o no caminho absoluto externo fornecido pelo usuário para a sessão, seguindo a política de nomes, versionamento, verificação e fallback definida na skill `principios-globais`. Não armazene o caminho pessoal nesta skill ou em outros arquivos versionados. Nunca grave o resumo dentro do repositório.

A gravação do resumo externo é uma tarefa documental separada do trabalho no projeto. Na Fase 0, nenhuma alteração no projeto é permitida, mas o resumo externo é permitido pelo caminho fornecido pelo usuário para a sessão. Não registre segredos nem logs extensos. Se a gravação falhar, não alegue que o arquivo foi salvo: apresente a tabela completa na conversa, informe o erro sem conteúdo sensível e marque a persistência externa como `PENDING MANUAL`. O resumo deve refletir a fase e o estado reais, inclusive quando houver interrupção; não o rotule como conclusão aprovada se os portões não tiverem sido satisfeitos.

Use uma única tabela compacta, com uma linha por assunto relevante:

| Campo | Conteúdo mínimo |
| --- | --- |
| Pacote, fase e estado | Identificador do pacote, fase atual e estado técnico real. |
| Último ponto seguro lógico | Último item da sequência operacional concluído e verificado; não presuma snapshot ou restauração automática. |
| Estado inicial/final | Comparação curta, sem segredos, com diferenças ainda não explicadas. |
| Arquivos | Lista acumulada dos arquivos criados por etapa, além dos alterados e removidos. |
| Comandos e evidências | Comandos relevantes e resultados reais de testes, auditorias e verificações. |
| Permissões e canário | Confirmação manual do usuário no `/permissions`, fontes/configurações relevantes e estado de cada caminho testado; identifique o que não foi testado. |
| Referências do projeto | Caminho configurado (sem dados sensíveis), arquivos analisados/parciais/não suportados e dúvidas visuais relevantes, quando aplicável. |
| Falhas e retomada | Operações iniciadas, concluídas, falhas/interrompidas, arquivos possivelmente afetados e se a repetição é segura ou exige inspeção. |
| Pendências e limitações | Critérios não satisfeitos, evidência que falta, risco e responsável pela próxima ação quando conhecido. |
| Decisões e autorizações | Autorizações explícitas e limites, incluindo eventual autorização excepcional de avanço ou de commit. |
| Próximo passo permitido | Ação precisa autorizada ou indicação de que deve aguardar o usuário. |

Mantenha cada célula concisa. Inclua detalhes adicionais somente quando forem necessários para evitar repetição perigosa, perda de trabalho ou interpretação errada de uma autorização. Não inclua segredos, tokens, valores de arquivos de ambiente ou dados privados.

Durante a fase, mantenha cumulativamente o registro das etapas concluídas e dos arquivos criados; incorpore o consolidado ao relatório final e à tabela de continuidade, sem criar arquivos do projeto para guardar o retrato e sem emitir relatórios intermediários a cada subpasso. Para o significado de **etapa**, aplique a regra 28 de `principios-globais`. Esses checkpoints são documentais: não crie commits antecipados, não use `git stash` e não faça cópias de recuperação fora do escopo como substitutos de um snapshot real. Não afirme que existe um mecanismo de rollback/restauração quando ele não foi configurado e verificado.

Se uma fase falhar ou for interrompida, preserve o trabalho válido já produzido. Não reverta automaticamente alterações nem repita operações com efeitos colaterais até confirmar o estado atual, a segurança da repetição e a autorização vigente. Não invente flags ou mecanismos de continuação do Claude Code.

Ao finalizar a Fase 1A, apresente o relatório e a tabela de continuidade completa na conversa, pronta para copiar, e indique que o próximo passo é a Fase 1B. Informe que a primeira operação de segurança da Fase 1B é confirmar o carregamento das permissões (com reinicialização, se necessária) antes de executar o canário. Tente gravar o resumo no diretório externo configurado, sem sobrescrever versões anteriores; leia-o de volta e verifique o conteúdo antes de declarar a gravação como concluída. Informe o caminho exato somente se a gravação tiver sido verificada. Nunca grave o resumo dentro do repositório. Se o caminho estiver vazio ou ainda contiver um marcador/placeholder, ou o acesso técnico não estiver configurado, apresente o resumo completo na conversa e marque a persistência externa como `PENDING MANUAL`. Se surgir um pedido de aprovação para gravar, aguarde a decisão do usuário: se aprovado, grave, releia e verifique; se negado, bloqueado sem opção de aprovação ou sem resposta até o encerramento/interrupção da sessão, use o fallback sem contornar a restrição. Informe o caminho exato somente após verificação. Essa falha só pode ser tratada como pendência externa não bloqueante nos limites estritos dos critérios de saída da skill `fases-e-autorizacoes`; não alegue que o arquivo foi salvo.

Ao iniciar uma nova sessão:

1. Inspecione novamente o ambiente.
2. Confirme o estado real do projeto.
3. Verifique alterações existentes.
4. Não confie cegamente no resumo anterior.
5. Não repita ações concluídas sem necessidade.
6. Não interprete o resumo como nova autorização.
7. Revalide que as operações pendentes são seguras de repetir, especialmente quando possam causar efeitos colaterais.
8. Para iniciar a Fase 1B, obtenha novamente do usuário o texto completo do procedimento “Canário de permissões” da skill `seguranca-permissoes`, junto com o resumo de continuidade. Sem esse conteúdo no contexto da nova sessão, marque o canário como `NOT RUN` e não improvise nem infira autorização a partir dos arquivos do projeto.

Não execute o canário na Fase 1A.
