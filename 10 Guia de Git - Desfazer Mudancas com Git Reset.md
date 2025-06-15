# Guia Completo: Desfazer Mudanças com `git reset`
---

O comando **`git reset`** é uma ferramenta poderosa no Git para desfazer alterações e manipular o histórico de commits. Ao contrário de **`git revert`**, que cria um novo commit para reverter mudanças, **`git reset`** move o ponteiro de branch (**`HEAD`**) para um commit anterior, efetivamente **reescrevendo o histórico de commits** a partir daquele ponto. Por essa razão, **`git reset`** deve ser usado com cautela, especialmente em repositórios que já foram compartilhados.

## O que é `git reset`?
---

`git reset` é um comando que desfaz alterações, mas ele atua em três áreas principais do Git:

1.  **Repositório Git (`HEAD`):** Onde o histórico de commits está armazenado.
2.  **Área de Preparação (Staging Area/Index):** Onde as alterações são preparadas para o próximo commit.
3.  **Diretório de Trabalho (Working Directory):** Onde você edita seus arquivos.

A principal função do `git reset` é mover o ponteiro de branch (`HEAD`) para um commit anterior, e dependendo da opção que você usa (`--soft`, `--mixed`, `--hard`), ele também afeta a Área de Preparação e o Diretório de Trabalho.

### Por que usar `git reset`?

* **Corrigir commits recentes locais:** Desfazer commits que ainda não foram enviados para um repositório remoto.
* **Desfazer adições à Área de Preparação:** "Despreparar" arquivos que foram adicionados com `git add`.
* **Descartar alterações locais:** Reverter o diretório de trabalho para um estado limpo de um commit anterior.

## Sintaxe Básica:

```bash
git reset [modo] <hash_do_commit_ou_referencia>
```
* `[modo]`: Define como a Área de Preparação e o Diretório de Trabalho serão afetados.
* `<hash_do_commit_ou_referencia>`: O hash SHA-1 do commit para o qual você deseja redefinir (resetar) o `HEAD` do branch, ou uma referência como `HEAD~1` (o commit anterior a `HEAD`). Se omitido, o padrão é `HEAD`.

## Modos de `git reset`
---

Os três modos principais do `git reset` determinam o quão "destrutivo" ele será para suas alterações locais.

### 1. `git reset --soft`
---

* **`HEAD`:** Move o `HEAD` do branch para o commit especificado.
* **Área de Preparação:** **Não é afetada.** As alterações do commit que foi desfeito (e quaisquer alterações que estavam na Área de Preparação antes) permanecem na Área de Preparação.
* **Diretório de Trabalho:** **Não é afetado.** Seus arquivos permanecem como estão.

**Quando usar:** Quando você quer desfazer um ou mais commits, mas deseja manter todas as alterações desses commits (e quaisquer outras mudanças) prontas para serem comitadas novamente. Útil para refazer uma mensagem de commit ou combinar vários commits em um só.

**Exemplo:**
Imagine que você tem o histórico `A -- B -- C -- D (HEAD)`. Você fez o commit `D`, mas esqueceu de incluir um arquivo.

```bash
# 1. Desfazer o último commit (D), mas manter as mudanças na Área de Preparação
git reset --soft HEAD~1
# Agora o HEAD aponta para C.
# As mudanças do commit D estão na staging area, prontas para serem comitadas novamente.
# Você pode adicionar o arquivo que esqueceu e fazer um novo commit.
```

### 2. `git reset --mixed` (Padrão)
---

* **`HEAD`:** Move o `HEAD` do branch para o commit especificado.
* **Área de Preparação:** **É limpa.** As alterações do commit que foi desfeito são removidas da Área de Preparação.
* **Diretório de Trabalho:** **Não é afetado.** As alterações dos commits desfeitos (e quaisquer outras modificações) permanecem no seu Diretório de Trabalho como "modificações não preparadas" (`unstaged changes`).

**Quando usar:** Este é o modo padrão (`git reset` sem `--soft` ou `--hard` é `git reset --mixed`). Útil quando você quer desfazer um commit e retornar as alterações para o seu diretório de trabalho, para que você possa revisá-las e adicioná-las seletivamente de volta à Área de Preparação.

**Exemplo:**
Histórico `A -- B -- C -- D (HEAD)`. Você quer desfazer o commit `D` e ter suas mudanças disponíveis para edição.

```bash
# 1. Desfazer o último commit (D) e mover as mudanças para o diretório de trabalho
git reset HEAD~1
# Ou: git reset --mixed HEAD~1
# Agora o HEAD aponta para C.
# As mudanças do commit D não estão na staging area, mas estão no seu diretório de trabalho.
# 'git status' mostrará estas mudanças como 'Changes not staged for commit'.
```

### 3. `git reset --hard`
---

* **`HEAD`:** Move o `HEAD` do branch para o commit especificado.
* **Área de Preparação:** **É limpa.**
* **Diretório de Trabalho:** **É limpo.** Todas as alterações no Diretório de Trabalho (incluindo arquivos modificados, novos arquivos não rastreados e arquivos na Área de Preparação) que não estão presentes no commit de destino serão **descartadas permanentemente**.

**Quando usar:** Este é o modo mais **destrutivo** e deve ser usado com extrema cautela, pois você perderá todas as alterações não comitadas e não preparadas que foram feitas após o commit de destino. Use quando você tem certeza de que quer descartar todas as mudanças e retornar a um estado limpo de um commit anterior.

**Exemplo:**
Histórico `A -- B -- C -- D (HEAD)`. Você fez algumas modificações e commits (`C`, `D`) e agora quer voltar completamente para o estado do commit `B`, descartando tudo de `C` e `D`, e quaisquer outras mudanças locais.

```bash
# 1. Desfazer os últimos dois commits (D e C) e descartar todas as mudanças
git reset --hard HEAD~2
# Ou: git reset --hard <hash_do_commit_B>
# Agora o HEAD aponta para B.
# O diretório de trabalho e a staging area estão limpos, correspondendo exatamente ao commit B.
# As mudanças de C e D foram PERMANENTEMENTE perdidas, se não houverem sido comitadas em outro branch.
```

## Outros Usos Comuns de `git reset`
---

### 1. "Despreparar" Arquivos (Unstaging)

Se você adicionou arquivos à Área de Preparação com `git add` e depois decidiu que não quer incluí-los no próximo commit:

```bash
git reset HEAD <caminho/do/arquivo>
# Ou (equivalente para remover da staging area):
git restore --staged <caminho/do/arquivo>
# Exemplo: git reset HEAD meu_arquivo.txt
```
Isso remove o arquivo da Área de Preparação, movendo-o de volta para o estado "modificado" no Diretório de Trabalho.

### 2. Descartar Alterações em um Arquivo (para o estado do HEAD)

Se você modificou um arquivo no seu Diretório de Trabalho e quer descartar essas mudanças, voltando para a versão do arquivo no último commit:

```bash
git restore <caminho/do/arquivo>
# Ou (método antigo):
git checkout -- <caminho/do/arquivo>
# Exemplo: git restore meu_arquivo.txt
```
Isso reverterá o arquivo `meu_arquivo.txt` para o estado que ele tinha no seu `HEAD` atual, perdendo as mudanças locais não salvas.

## Diferença entre `git reset` e `git revert`
---

É crucial entender a distinção entre `git reset` e `git revert`, pois eles servem a propósitos muito diferentes:

| Característica   | `git reset`                                     | `git revert`                                  |
| :--------------- | :---------------------------------------------- | :-------------------------------------------- |
| **Histórico** | Destrutivo; **reescreve** o histórico (remove commits). | Não destrutivo; cria um **novo commit** para desfazer. |
| **Uso Ideal** | Para commits **locais** (ainda não enviados ao remoto). | Para commits **públicos** (já no repositório remoto). |
| **Segurança** | Perigoso em repositórios compartilhados, pois força outros a "rebasear". | Mais seguro para colaboração, pois não afeta o histórico compartilhado. |
| **Tipo de "Desfazer"** | Remove commits, retornando o histórico a um estado anterior. | Adiciona um commit que "nega" as alterações anteriores. |

## Conclusão
---

O comando `git reset` é uma ferramenta extremamente poderosa e flexível para manipular o histórico do seu repositório Git e gerenciar o estado dos seus arquivos. Seja para desfazer um commit local, "despreparar" um arquivo ou descartar completamente as mudanças no diretório de trabalho, `git reset` oferece a granularidade necessária. No entanto, devido à sua capacidade de reescrever o histórico, ele deve ser usado com cautela e, idealmente, apenas em branches que ainda não foram compartilhados publicamente. Para desfazer commits em branches públicos, `git revert` é a ferramenta preferida.
