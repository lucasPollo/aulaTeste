
---

# Roteiro 2 â€“ Branches

## 1) Voltando a um commit (Revert / Reset) - Roteiro 1 (ainda)

| MÃ©todo          | Quando usar                             | Comando principal                     |
| --------------- | --------------------------------------- | ------------------------------------- |
| **Nova branch** | Precisa testar mudanÃ§a sem tocar `main` | `git checkout -b volta-commit <hash>` |
| **git revert**  | Quer preservar histÃ³rico (opÃ§Ã£o segura) | `git revert <hash>`                   |
| **git reset**   | Precisa reescrever histÃ³rico local      | `git reset --hard <hash>`             |

1. **Descobrir hash** do commit alvo:
   ```bash
   git log --oneline --graph --decorate -n 10
   ```
2. **Criar branch** (opcional, mas recomendado):
   ```bash
   git checkout -b hotfix/volta-commit <hash>
   ```
3. **Reverter (seguro):**
   ```bash
   git revert <hash>
   git push origin main
   ```
4. **Reset (perigoso):**
   ```bash
   # Desfaz commits locais e move ponteiro; use --soft para manter staging, --mixed (padrÃ£o) ou --hard.
   git reset --hard <hash>
   git push --force-with-lease
   ```

> **Dica:** nunca use `git reset --hard` depois que commits jÃ¡ foram enviados e utilizados por outros.

---

## 2) ConvenÃ§Ãµes de **Git SemÃ¢ntico** (Conventional Commits)

Use mensagens padronizadas e curtas, com **tipo**, **escopo** (opcional) e **resumo no imperativo**.

**Formato:**

```
<tipo>(<escopo>): <resumo>

[corpo opcional explicando o porquÃª]
[Refs: #<issue>]
```

**Tipos comuns:**

- `feat` (nova funcionalidade)
- `fix` (correÃ§Ã£o de bug)
- `docs` (documentaÃ§Ã£o)
- `style` (formataÃ§Ã£o, sem mudanÃ§a de lÃ³gica)
- `refactor` (refatoraÃ§Ã£o, sem alterar comportamento externo)
- `test` (testes)
- `build` (build, dependÃªncias)
- `ci` (pipelines de CI)
- `chore` (tarefas diversas que nÃ£o afetam src/test)

**Exemplos prÃ¡ticos:**

```bash

feat(readme): inclusÃ£o do campo about me
fix(api): tratar null pointer ao criar pedido
docs(contrib): adicionar guia de pull request
refactor(core): extrair serviÃ§o de cÃ¡lculo de frete
chore(deps): atualizar axios para ^1.7.0
```

> **Por que usar?** Facilita *changelog*, leitura do histÃ³rico e automaÃ§Ãµes (versionamento semÃ¢ntico, release notes, validaÃ§Ãµes de CI).


## 3) Trabalhando com `git stash`

O `git stash` Ã© usado para **guardar mudanÃ§as temporariamente** sem precisar fazer commit, permitindo trocar de branch ou atualizar cÃ³digo sem perder alteraÃ§Ãµes nÃ£o finalizadas.

**Comandos principais:**

- Guardar alteraÃ§Ãµes:
  ```bash
  git stash
  ```
- Guardar com mensagem:
  ```bash
  git stash save "ajustes parciais no componente header"
  ```
- Listar stashes salvos:
  ```bash
  git stash list
  ```
- Recuperar o Ãºltimo stash (mantendo no histÃ³rico):
  ```bash
  git stash apply
  ```
- Recuperar e remover do histÃ³rico:
  ```bash
  git stash pop
  ```
- Aplicar stash especÃ­fico:
  ```bash
  git stash apply stash@{2}
  ```
- Limpar todos os stashes:
  ```bash
  git stash clear
  ```

> Ãštil quando precisa trocar de branch no meio do desenvolvimento ou atualizar a `main` sem perder o que jÃ¡ comeÃ§ou.

## 4) ConvenÃ§Ãµes de **nomenclatura** de branches

Use nomes curtos, descritivos e com *slug* (kebab-case). Inclua tipo e, se houver, ID do ticket.

- `feature/<id>-<descricao>` â†’ `feature/1234-criar-endpoint-pedidos`
- `fix/<id>-<descricao>` â†’ `fix/235-bug-null-pointer-checkout`
- `chore/<descricao>` â†’ `chore/atualizar-dependencias`
- `hotfix/<descricao>` â†’ `hotfix/corrigir-regra-frete`
- `docs/<descricao>` â†’ `docs/roteiro-git-pr`

---

## 5) Criar branch a partir da `main`

Sempre atualize sua `main` local antes de ramificar.

```bash
git checkout main
git pull

git checkout -b feature/1234-criar-endpoint-pedidos

```

---

## 6) Ciclo de commits (aplicando Git SemÃ¢ntico)

Mantenha commits pequenos, coesos e com mensagens claras.

**Exemplo completo:**

```bash
git add .
git commit -m "feat(api): criar endpoint POST /pedidos Adiciona validaÃ§Ãµes e integra com serviÃ§o de estoque. Refs: #1234"
```

---

## 7) Manter sua branch atualizada (rebase recomendado)

Enquanto desenvolve, replique alteraÃ§Ãµes da `main` para evitar *big bang merges*.

```bash
git fetch origin
git rebase origin/main
# Resolva conflitos (se houver), depois:
git rebase --continue

```

---

## Extra: O que Ã© `--force-with-lease` e por que usar?

Ao reescrever histÃ³rico (ex.: `git rebase`) e enviar para o remoto, o comando `git push` normal serÃ¡ rejeitado se houver divergÃªncias. O `--force` sobrescreve tudo **sem verificar** se alguÃ©m mais atualizou o remoto, podendo apagar trabalho alheio.

O `--force-with-lease` Ã© mais seguro: ele **sÃ³ forÃ§a o push se a referÃªncia remota ainda estiver como vocÃª a viu no Ãºltimo **``**/**``. Se outra pessoa tiver feito um push no intervalo, o comando falha, evitando sobrescrever mudanÃ§as sem querer.

**Fluxo recomendado:**

```bash
git fetch origin
git rebase origin/main
git push --force-with-lease
```

> Sempre combine com a equipe antes de usar, especialmente em branches compartilhadas.

---

## 8) Material de estudo extra:

- [Video da explicaÃ§Ã£o do que Ã© branche (legendado)](https://www.youtube.com/watch?v=e9lnsKot_SQ)

---


