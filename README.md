# BASE_CODIGO

Base reutilizável para desenvolvimento web, organizada em um `CLAUDE.md` de roteamento e 13 skills temáticas para orientar o Claude Code no desenvolvimento de projetos.

**Repositório oficial:** [https://github.com/rafavlad/BASE_CODIGO](https://github.com/rafavlad/BASE_CODIGO)  
**Branch de referência dos links diretos:** `main`  
**Versão deste pacote:** `2.0.1`

## Para que serve

Este repositório concentra regras técnicas reutilizáveis para desenvolvimento web: arquitetura, fases e autorizações, segurança, auditoria de dependências, CI, interface, qualidade, Git, documentação, continuidade e critérios de aceitação.

O pacote não é, por si só, um aplicativo web pronto. Ele documenta o processo e as regras que o Claude Code deve seguir ao trabalhar em um projeto.

## Estrutura oficial

```text
BASE_CODIGO/
├── CLAUDE.md
├── .claude/
│   └── skills/
│       ├── aceitacao-final/SKILL.md
│       ├── arquitetura-stack/SKILL.md
│       ├── auditoria-dependencias/SKILL.md
│       ├── ci-dependabot/SKILL.md
│       ├── continuidade-sessoes/SKILL.md
│       ├── documentacao-claude-md/SKILL.md
│       ├── fases-e-autorizacoes/SKILL.md
│       ├── git-integridade/SKILL.md
│       ├── interface-cliente-referencias/SKILL.md
│       ├── modulos-opcionais/SKILL.md
│       ├── principios-globais/SKILL.md
│       ├── qualidade-acessibilidade/SKILL.md
│       └── seguranca-permissoes/SKILL.md
├── docs/
│   ├── INDICE-DO-PACOTE.md
│   ├── MAPA-DE-LEITURA.md
│   ├── MENSAGENS-DE-SESSAO.md
│   ├── REGISTRO-ALTERACOES.md
│   └── RELATORIO-DE-VALIDACAO.md
└── MANIFEST.json
```

**Importante:** a pasta `.claude` começa com ponto de propósito. Não mantenha outra cópia duplicada das skills em `skills/` na raiz. O diretório `.claude/skills/<nome>/SKILL.md` é o caminho canônico esperado pelo Claude Code.

## Arquivos principais

- [`CLAUDE.md`](CLAUDE.md): instruções permanentes e matriz de carregamento por fase para este repositório de fundação; não deve ser copiado sem adaptação para um projeto de cliente.
- [Índice do pacote](docs/INDICE-DO-PACOTE.md): links relativos para as 13 skills e indicação de quando carregá-las.
- [Mapa central de leitura](docs/MAPA-DE-LEITURA.md): ponto de entrada único com links Raw para consultar esta base por chat. O mapa orienta a leitura; não carrega arquivos automaticamente.
- [Mensagens de sessão](docs/MENSAGENS-DE-SESSAO.md): modelos de mensagens para iniciar ou retomar cada fase.
- [Registro cumulativo de alterações](docs/REGISTRO-ALTERACOES.md): um único histórico técnico, com novas entradas adicionadas ao final.
- [Relatório de validação](docs/RELATORIO-DE-VALIDACAO.md): histórico da migração e verificações estruturais realizadas.
- [`MANIFEST.json`](MANIFEST.json): tamanhos e SHA-256 dos arquivos distribuídos, exceto o próprio manifesto.

## Links diretos para leitura em Claude (chat)

Algumas interfaces de chat escondem arquivos de diretórios iniciados por ponto, como `.claude/`. Comece por [MAPA-DE-LEITURA.md](docs/MAPA-DE-LEITURA.md), que reúne os links Raw da base. Se precisar abrir um arquivo individual, use o endereço `raw.githubusercontent.com` correspondente. Estes links dependem de os arquivos estarem publicados na branch `main` com a estrutura acima.

- [`CLAUDE.md` (Raw)](https://raw.githubusercontent.com/rafavlad/BASE_CODIGO/main/CLAUDE.md)
- [`README.md` (Raw)](https://raw.githubusercontent.com/rafavlad/BASE_CODIGO/main/README.md)
- [Índice (Raw)](https://raw.githubusercontent.com/rafavlad/BASE_CODIGO/main/docs/INDICE-DO-PACOTE.md)
- [Princípios globais (Raw)](https://raw.githubusercontent.com/rafavlad/BASE_CODIGO/main/.claude/skills/principios-globais/SKILL.md)
- [Fases e autorizações (Raw)](https://raw.githubusercontent.com/rafavlad/BASE_CODIGO/main/.claude/skills/fases-e-autorizacoes/SKILL.md)
- [Segurança e permissões (Raw)](https://raw.githubusercontent.com/rafavlad/BASE_CODIGO/main/.claude/skills/seguranca-permissoes/SKILL.md)
- [Arquitetura e stack (Raw)](https://raw.githubusercontent.com/rafavlad/BASE_CODIGO/main/.claude/skills/arquitetura-stack/SKILL.md)
- [Auditoria de dependências (Raw)](https://raw.githubusercontent.com/rafavlad/BASE_CODIGO/main/.claude/skills/auditoria-dependencias/SKILL.md)
- [CI e Dependabot (Raw)](https://raw.githubusercontent.com/rafavlad/BASE_CODIGO/main/.claude/skills/ci-dependabot/SKILL.md)
- [Interface e referências (Raw)](https://raw.githubusercontent.com/rafavlad/BASE_CODIGO/main/.claude/skills/interface-cliente-referencias/SKILL.md)
- [Qualidade e acessibilidade (Raw)](https://raw.githubusercontent.com/rafavlad/BASE_CODIGO/main/.claude/skills/qualidade-acessibilidade/SKILL.md)
- [Módulos opcionais (Raw)](https://raw.githubusercontent.com/rafavlad/BASE_CODIGO/main/.claude/skills/modulos-opcionais/SKILL.md)
- [Git e integridade (Raw)](https://raw.githubusercontent.com/rafavlad/BASE_CODIGO/main/.claude/skills/git-integridade/SKILL.md)
- [Documentação (Raw)](https://raw.githubusercontent.com/rafavlad/BASE_CODIGO/main/.claude/skills/documentacao-claude-md/SKILL.md)
- [Continuidade de sessões (Raw)](https://raw.githubusercontent.com/rafavlad/BASE_CODIGO/main/.claude/skills/continuidade-sessoes/SKILL.md)
- [Aceitação final (Raw)](https://raw.githubusercontent.com/rafavlad/BASE_CODIGO/main/.claude/skills/aceitacao-final/SKILL.md)

## Como usar com Claude Code

1. Clone o repositório ou baixe o pacote completo.
2. Abra a raiz do repositório no Claude Code.
3. Mantenha `CLAUDE.md` e `.claude/skills/` juntos e versionados.
4. Leia `docs/INDICE-DO-PACOTE.md` e carregue explicitamente as skills exigidas pela fase, conforme `CLAUDE.md`.
5. Siga `docs/MENSAGENS-DE-SESSAO.md` para iniciar uma fase, sem pular as autorizações humanas.
6. Revise todas as alterações e execute as verificações reais antes de aprovar qualquer entrega.

Para aplicar esta fundação a outro projeto, copie somente as skills necessárias de `.claude/skills/` e os documentos de apoio pertinentes. Crie um `CLAUDE.md` próprio para o cliente, seguindo a skill `documentacao-claude-md` e refletindo a stack, os comandos e as decisões reais daquele projeto. Não copie este `CLAUDE.md` de roteamento sem adaptação e não mantenha cópias divergentes das mesmas skills.

## Regras essenciais

- Não coloque segredos, tokens, chaves de API ou arquivos `.env` reais no GitHub.
- A IA ajuda na produção, mas os profissionais continuam responsáveis por compreender e validar o código.
- Não declare testes aprovados sem executá-los.
- O histórico técnico cumulativo deve ficar em `docs/REGISTRO-ALTERACOES.md`; acrescente entradas ao final sem apagar o histórico anterior.
- Resumos de continuidade são separados do histórico técnico e seguem as regras de `continuidade-sessoes`.

Consulte sempre a documentação oficial do Claude Code para confirmar detalhes que dependam da versão instalada.
