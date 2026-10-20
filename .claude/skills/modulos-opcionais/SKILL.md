---
name: modulos-opcionais
description: "Restrições e critérios para módulos futuros como backend, banco, autenticação, uploads, agendamentos, pagamentos e integrações. Use quando um módulo opcional for proposto."
---

# 11. MÓDULOS FUTUROS OPCIONAIS

Documente os módulos futuros em `docs/MODULES.md`. Não os implemente automaticamente. Cada módulo exige escopo e autorização próprios.

## 11.1 API e backend

Tecnologias possíveis: Node.js, Express, TypeScript, Zod, Helmet, CORS com allowlist, limites de requisições e tamanho de corpo, tratamento centralizado de erros, logs com dados sensíveis redigidos, endpoint de saúde e testes de integração.

Documente limites, configuração e riscos.

## 11.2 Banco de dados

Tecnologias possíveis: PostgreSQL, Drizzle, migrações, usuários com privilégios mínimos, consultas parametrizadas, backups e dados sintéticos para testes.

Não utilize dados reais de clientes para testes sem autorização e justificativa adequadas.

## 11.3 Autenticação

Considere um provedor especializado ou implementação apropriada com Argon2id quando aplicável.

Documente:

* Recuperação de senha com tokens únicos e expirantes.
* Limites de tentativas.
* Cookies seguros.
* Proteção CSRF quando aplicável.
* Autorização no servidor.
* Testes contra IDOR.
* Sessões e expiração.
* Proteção contra enumeração de contas.

Autenticação no frontend não substitui autorização no servidor.

## 11.4 Uploads

Documente validação de tipo e conteúdo, limites de tamanho, nomes seguros, armazenamento isolado, controle de acesso e tratamento de arquivos perigosos.

## 11.5 Agendamentos

Documente fuso horário, disponibilidade calculada no servidor, prevenção de reservas concorrentes, validação de horários, cancelamentos e consistência dos dados.

## 11.6 Pagamentos

Utilize um provedor especializado quando necessário.

Documente autenticidade de webhooks, idempotência, falhas, reconciliação, proteção de dados e proibição de armazenar dados completos de cartão sem estrutura e autorização adequadas.

## 11.7 Integrações externas

Documente timeouts, limites de uso, custos, retentativas controladas, validação de respostas, proteção de credenciais e tratamento de indisponibilidade.

Documentar um módulo não autoriza sua implementação.

---
