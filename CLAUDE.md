# PontOS — instruções para o Claude Code

Fonte de verdade: o "PontOS — Documento Base" (documento vivo). Em caso de dúvida, ele vale mais que este arquivo. Nunca inventar tabela, campo, tela ou papel que não esteja nele: faltou dado, pergunte.

## O que é
App que leva a demanda semanal da Leadec (limpeza e apoio na fábrica da Volkswagen) da coordenação ao colaborador, com cada etapa registrada. Piloto: área de pintura. Pensado para várias áreas desde o início (campo de área em usuário, tarefa e catálogo de tipos).

## Stack
React (PWA instalável no celular) · Supabase (Postgres + Auth + Storage) · GitHub + GitHub Actions · Vercel · Claude Code. Mesmo stack do NexOS.

## Papéis
`coordenacao` (o supervisor usa o mesmo papel), `encarregado`, `colaborador`. O papel fica **só** na tabela `usuarios`, nunca em metadados editáveis pelo próprio usuário.

## Regras de negócio que não se quebram
- Status: Pendente → Atribuído → Concluído → Verificado. Inconcluído é o quinto status (devolução com percentual e motivo).
- Registro feito pelo encarregado (em nome do colaborador, ou ele mesmo executando) vai direto para Verificado. Ninguém verifica a própria entrada.
- `executado_por` e `registrado_por` são sempre campos separados.
- Offline-first: toda mudança de fluxo considera a fila local e o atraso de sincronização (`sincronizado_em`).
- Login por matrícula + senha (e-mail fictício `matricula@pontos.local`). Desativar usuário, nunca apagar.
- A Volks (na OS da Fase 2) vê só o quê, quando e onde. Nunca nomes.

## Banco de dados
- Toda tabela nova nasce com RLS e GRANT explícito por papel, escritos junto com a criação.
- Migrations são `.sql` versionados no Git e testados em branch do Supabase antes de produção.
- Aplicar migration em produção exige atenção redobrada do Kléber. Nunca aplicar sozinho.
- Permissão se valida no banco; a interface só esconde botão.

## Como trabalhar
1. Entender antes de alterar: como funciona hoje, dependências, efeitos colaterais, e só então a alteração mínima.
2. Mudança incremental, nunca "big bang". Sempre em PR, nunca direto na main.
3. Antes de propor mudança, listar quem cria, quem distribui e quem executa, e como cada papel é afetado.
4. Testes automatizados desde o primeiro PR. Fluxos críticos com teste: login, criar avulsa, marcar como feita, verificar, sincronizar fila offline.
5. Erro nunca é silencioso: toda ação que falha mostra mensagem clara (o que falhou, o que fazer). Log com `console.error` com contexto; nunca registrar senha, token ou dado sensível.
6. Nunca expor credenciais em commit, print ou mensagem de erro.
7. Conflito com a arquitetura existente: não alterar direto. Explicar o conflito e propor a solução antes.
8. Revisão do diff pelo Kléber antes de todo merge, mesmo em mudança pequena. Nenhum agente decide sozinho o que entra.
9. Ser direto sobre riscos e decisões ruins. Não concordar só para agradar.

## Interface
- Mockup aprovado pelo Kléber antes de qualquer prompt de implementação de tela.
- Ícones: nunca biblioteca de estoque. Ícone de destaque: pedir ao Kléber. Ícone pequeno que replica uma referência dele: SVG próprio.
- Uso no celular Android, um passo por vez, tema escuro conforme o Design System.

## Estado atual
Fase 3 (fundação do repositório). Ainda **não** implementar schema, RLS nem telas: a Seção 8 do Documento Base lista lacunas que bloqueiam o desenho técnico (políticas de RLS por tabela, ordenação por urgência, tela inicial do encarregado, entre outras).
