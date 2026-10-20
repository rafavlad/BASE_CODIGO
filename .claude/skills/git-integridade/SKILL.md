---
name: git-integridade
description: "Integridade do repositório, identidade Git, autorização de commit e proibições de operações remotas. Use antes de qualquer operação Git com efeitos colaterais."
---

# Integridade do Git

## 12.1 Fase 0

Não inicialize Git durante a Fase 0.

## 12.2 Fase 1A

Após aprovação da Fase 0, se ainda não houver repositório, inicialize-o no início da Fase 1A.

Prefira:

`git init --initial-branch=main`

Se indisponível, utilize alternativa compatível e documentada.

Não modifique configurações globais. Não faça commits na Fase 1A.

## 12.3 Identidade Git

Antes de um commit, consulte a identidade local com comandos de leitura, como:

* `git config user.name`
* `git config user.email`

Se a identidade estiver ausente, incorreta ou não puder ser confirmada, interrompa e solicite orientação.

Não invente nome ou e-mail. Não altere identidade global ou local sem autorização explícita.

## 12.4 Integridade antes e depois das alterações

Antes de qualquer alteração autorizada, registre o estado inicial do repositório e dos arquivos relevantes conforme a seção 10.3. Registre-o somente no relatório da fase e no resumo de continuidade, nunca em arquivos do projeto. Isso não autoriza ler segredos, modificar arquivos, descartar mudanças nem executar operações destrutivas.

Depois das alterações, compare o estado final com o inicial. Revise o diff completo, incluindo arquivos não rastreados que serão adicionados. Explique cada criação, alteração, remoção ou renomeação. Caches esperados gerados por testes em diretórios ignorados (por exemplo, `node_modules/.vite`) podem ser excluídos da comparação lógica conforme a seção 10.3; não use isso para ignorar alterações desconhecidas nem execute limpeza automática. Investigue diferenças inesperadas antes de prosseguir.

Não use `git reset --hard`, `git clean`, checkout destrutivo, exclusões recursivas ou comandos equivalentes para "limpar" o projeto. Se uma operação potencialmente destrutiva parecer necessária, pare e solicite autorização explícita, explicando o alvo, o impacto esperado e as alternativas seguras.

Se detectar mudanças que não foram feitas nesta execução ou cuja origem não esteja clara, preserve-as e interrompa a operação que possa afetá-las. Não tente restaurar automaticamente o estado inicial caso isso possa sobrescrever trabalho de terceiros ou do usuário.

## 12.5 Preparação do commit

Antes do commit inicial:

1. Verifique o estado do Git.
2. Revise o diff completo.
3. Inspecione os arquivos que serão incluídos.
4. Confirme que arquivos de ambiente protegidos não serão versionados.
5. Confirme que arquivos temporários não serão versionados.
6. Confirme que alterações preexistentes do usuário foram preservadas.
7. Confirme que os critérios obrigatórios foram atendidos ou, se o commit estiver sendo autorizado pela via excepcional da regra de commit inicial da skill `git-integridade`, que cada pendência aceita foi explicitamente citada e autorizada pelo usuário. A autorização excepcional não supera falha crítica de integridade, segredo exposto, identidade Git ausente/incorreta nem dúvida razoável sobre os arquivos a incluir.
8. Confirme a identidade Git.
9. Confirme que o commit corresponde ao escopo autorizado.

Não utilize comandos destrutivos para limpar o repositório.

## 12.6 Autorização do commit inicial

O commit inicial local só está autorizado em uma destas condições: (a) o estado técnico da Fase 1B é `APPROVED`; ou (b) o usuário aprova explicitamente o relatório da Fase 1B, cita as pendências específicas e autoriza expressamente o commit apesar delas. A segunda condição é uma autorização humana separada, não transforma a fase em `APPROVED`, não muda os estados técnicos das verificações e não autoriza a Fase 2.

Essa autorização nunca justifica ocultar uma falha, expor segredos, ignorar uma alteração inesperada nem realizar uma operação destrutiva não autorizada. Falha crítica comprovada de integridade, identidade Git ausente/incorreta ou dúvida razoável sobre o conteúdo a ser commitado exige interrupção antes do commit.

Não solicite uma segunda autorização para o mesmo commit quando ele estiver exatamente dentro do escopo autorizado nos termos acima.

Se houver falha impeditiva não explicitamente aceita, identidade ausente ou alteração inesperada, interrompa antes do commit.

## 12.7 Operações não autorizadas

A autorização para o commit inicial não autoriza:

* Criar repositório remoto.
* Adicionar remotos.
* Fazer push.
* Publicar pacotes.
* Criar releases.
* Fazer deploy.
* Publicar o site.
* Avançar para a Fase 2.

Essas operações exigem autorização explícita própria.

## 12.8 Fundação central e projetos de clientes

Documente a diferença entre repositório central, template, clone, fork e projeto independente de cliente.

Não implemente sincronização automática entre projetos.

Templates não atualizam automaticamente os projetos criados a partir deles.

Utilize SemVer quando aplicável e mantenha `CHANGELOG.md` atualizado com notas relevantes de versão/release. O histórico técnico cumulativo de alterações fica em `docs/REGISTRO-ALTERACOES.md`: acrescente entradas ao final desse único arquivo, preserve as anteriores e não crie arquivos Markdown separados para cada alteração. Não duplique todas as entradas técnicas no changelog.

---
