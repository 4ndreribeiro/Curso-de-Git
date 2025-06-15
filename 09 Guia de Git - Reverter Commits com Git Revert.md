# Guia Completo: Reverter Commits com `git revert`
---

O comando `git revert` é uma ferramenta essencial no Git para desfazer alterações que já foram comitadas no histórico do seu repositório. Diferente de `git reset`, que pode reescrever o histórico de commits, `git revert` é uma operação **não destrutiva**, o que o torna ideal para desfazer mudanças em repositórios compartilhados ou em branches públicas.

## O que é `git revert`?
---

`git revert` não "remove" um commit do histórico. Em vez disso, ele cria um **novo commit** que desfaz as alterações introduzidas por um commit anterior. Pense nele como o oposto de um commit: ele registra um novo "instantâneo" que desfaz as modificações de um commit específico, mantendo o histórico de commits intacto e linear.

### Por que usar `git revert`?

* **Não destrutivo:** O histórico de commits permanece intocado. Isso é crucial em repositórios compartilhados, onde a reescrita do histórico (`git reset`) pode causar grandes problemas para outros colaboradores.
* **Segurança:** É a maneira mais segura de desfazer commits que já foram enviados para um repositório remoto.
* **Auditoria:** Como ele cria um novo commit, o processo de desfazer a alteração também é registrado, mantendo um rastro claro de todas as operações.

## Sintaxe Básica:

```bash
git revert <hash_do_commit>
```
* `<hash_do_commit>`: O hash SHA-1 completo ou curto do commit que você deseja reverter.

## Como Usar `git revert`
---

### 1. Revertendo um Único Commit

Vamos imaginar um cenário onde você fez um commit com um bug ou uma funcionalidade indesejada e já o enviou para o repositório remoto.

**Exemplo de Histórico:**

```
A -- B -- C -- D (HEAD -> main)
```
Você quer desfazer as alterações do commit `C`.

1.  **Encontre o hash do commit a ser revertido:**
    Use `git log --oneline` para encontrar o hash. Suponhamos que o hash do commit `C` seja `abcdef1`.

2.  **Execute o comando `git revert`:**
    ```bash
    git revert abcdef1
    ```
    * O Git abrirá seu editor de texto padrão (ex: Vim, Nano) com uma mensagem de commit pré-preenchida. A mensagem padrão será algo como "Revert "Mensagem do commit original"".
    * Você pode editar esta mensagem para adicionar mais detalhes sobre por que você está revertendo o commit.
    * Salve e feche o editor.

**Novo Histórico:**

```
A -- B -- C -- D -- C' (HEAD -> main)
```
* O commit `C'` é o novo commit que desfaz as alterações do commit `C`. Agora, as alterações do commit `C` foram desfeitas, e o histórico original foi preservado.

### 2. Revertendo Múltiplos Commits

Você pode reverter vários commits em uma única operação. O Git os reverterá na ordem inversa em que você os especificar.

**Exemplo:**

```
A -- B -- C -- D -- E -- F (HEAD -> main)
```
Você quer reverter os commits `E` (`hash_E`) e `D` (`hash_D`).

```bash
git revert hash_E hash_D
```
* Isso criará dois novos commits, um para desfazer `E` e outro para desfazer `D`. O Git abrirá o editor para cada commit de reversão separadamente.

**Revertendo um Intervalo de Commits:**

Você também pode reverter um intervalo de commits usando a sintaxe `..`. O Git reverterá os commits dentro do intervalo, excluindo o primeiro hash.

```bash
git revert HEAD~3..HEAD~1
```
* Isso reverterá os commits que estão 3 commits antes de `HEAD` até 1 commit antes de `HEAD`.
* A ordem padrão do `git revert` é do commit mais recente para o mais antigo dentro do intervalo especificado.

### 3. Revertendo Commits de Merge

Reverter um commit de merge é um pouco mais complexo porque um commit de merge tem mais de um pai. Você precisa especificar qual lado do merge você quer manter como a "linha principal" para que o Git saiba como desfazer as alterações.

```
      --- M --- (Merge Commit)
     /         \
... A --- B --- C (Branch Feature)
             /
          --- D --- E (Branch Principal)
```

Se `M` é o commit de merge, e você deseja revertê-lo, você precisa dizer ao Git qual dos pais do merge é a linha "principal" que deve ser considerada a base para a reversão. Você faz isso com a opção `-m <número_do_pai>`.

* `git log --pretty=full M` para ver os pais do merge.
* Pai `1` é geralmente o branch onde você fez o merge (ex: `main`).
* Pai `2` é geralmente o branch que foi mesclado (ex: `feature`).

```bash
git revert -m 1 <hash_do_commit_merge>
# Exemplo: git revert -m 1 f1e2d3c
```
* `-m 1` significa que você quer desfazer o merge como se você estivesse desfazendo as alterações que vieram do segundo pai (`feature` branch) e mantendo o que estava no primeiro pai (`main` branch).
* Isso criará um commit que desfaz a integração do branch de feature, mantendo o histórico principal.

## Diferença entre `git revert` e `git reset`
---

É crucial entender a distinção entre `git revert` e `git reset`, pois eles servem a propósitos muito diferentes:

| Característica   | `git revert`                                  | `git reset`                                     |
| :--------------- | :-------------------------------------------- | :---------------------------------------------- |
| **Histórico** | Não destrutivo; cria um **novo commit** para desfazer. | Destrutivo; **reescreve** o histórico (remove commits). |
| **Uso Ideal** | Para commits **públicos** (já no repositório remoto). | Para commits **locais** (ainda não enviados ao remoto). |
| **Segurança** | Mais seguro para colaboração.                 | Perigoso em repositórios compartilhados.         |
| **Tipo de "Desfazer"** | Adiciona um commit que "nega" as alterações anteriores. | Remove commits, retornando o histórico a um estado anterior. |

## Conclusão
---

O comando `git revert` é uma ferramenta indispensável para desfazer alterações em repositórios Git, especialmente em ambientes de colaboração. Sua natureza não destrutiva garante que o histórico do projeto permaneça intacto e consistente, evitando problemas para outros desenvolvedores. Ao criar um novo commit para desfazer as modificações, o `git revert` proporciona uma trilha de auditoria clara e segura, tornando-o a escolha preferida para desfazer commits que já foram compartilhados.
