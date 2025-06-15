# Guia Completo: Comparar Alterações com `git diff`
---

O comando `git diff` é uma das ferramentas mais essenciais no Kit de Ferramentas do Git. Ele permite que você veja as diferenças entre as alterações nos seus arquivos em vários estágios do fluxo de trabalho do Git. Entender como usar `git diff` é crucial para revisar seu trabalho, inspecionar mudanças antes de um commit e depurar problemas.

## O que é o `git diff`?
---

O `git diff` mostra as diferenças entre dois conjuntos de dados. Esses conjuntos podem ser:

* O seu diretório de trabalho e a área de preparação (staging area).
* A área de preparação e o último commit.
* Dois commits diferentes.
* Duas branches diferentes.
* Duas versões de um mesmo arquivo.

Ele exibe as diferenças no formato "patch", onde as linhas adicionadas são prefixadas com `+` e as linhas removidas com `-`.

## Entendendo a Saída do `git diff`
---

Uma saída típica do `git diff` pode parecer complexa à primeira vista, mas ela segue um padrão:

```diff
diff --git a/README.md b/README.md
index a1b2c3d..e4f5g6h 100644
--- a/README.md
+++ b/README.md
@@ -1,3 +1,4 @@
 # Meu Projeto
-Este é um projeto de exemplo.
+Este é um projeto de exemplo atualizado.
 Agora com mais informações.
+Adicionada uma nova linha.
```

* **`diff --git a/README.md b/README.md`**: Indica que estamos comparando duas versões do arquivo `README.md`. `a/` e `b/` representam as versões antiga e nova, respectivamente.
* **`index a1b2c3d..e4f5g6h 100644`**: Mostra os hashes dos objetos Git que representam o arquivo nas duas versões e o modo do arquivo.
* **`--- a/README.md`**: O arquivo original (versão antiga).
* **`+++ b/README.md`**: O arquivo modificado (versão nova).
* **`@@ -1,3 +1,4 @@`**: Informações sobre as linhas que foram alteradas. `-1,3` significa que o trecho começa na linha 1 e tem 3 linhas na versão antiga. `+1,4` significa que o trecho começa na linha 1 e tem 4 linhas na versão nova.
* **Linhas com `+`**: Linhas adicionadas na versão nova.
* **Linhas com `-`**: Linhas removidas na versão antiga.
* **Linhas sem prefixo**: Linhas de contexto que não foram alteradas.

## Casos de Uso do `git diff`
---

### 1. Comparar Diretório de Trabalho com Área de Preparação (Staging Area)

Este é o uso mais comum do `git diff` quando você está trabalhando localmente. Ele mostra o que você *mudou*, mas que ainda **não foi adicionado** à área de preparação (`git add`).

```bash
git diff
```

**Quando usar:** Antes de um `git add`, para revisar as alterações que você fez desde o último `git add` ou `git commit`.

### 2. Comparar Área de Preparação com Último Commit

Este comando mostra as alterações que **foram adicionadas** à área de preparação (`git add`), mas que ainda **não foram comitadas**.

```bash
git diff --staged
# Ou
git diff --cached
```
**Quando usar:** Antes de um `git commit`, para revisar as alterações que serão incluídas no próximo commit.

### 3. Comparar Diretório de Trabalho com Último Commit

Este comando mostra todas as alterações que você fez desde o último commit, incluindo tanto as alterações na área de trabalho quanto as na área de preparação.

```bash
git diff HEAD
```
**Quando usar:** Para ter uma visão geral de todas as suas mudanças locais em relação ao último commit.

### 4. Comparar Dois Commits Específicos

Você pode comparar quaisquer dois commits usando seus hashes (completos ou parciais) ou referências (como `HEAD~1` para o commit anterior).

```bash
git diff <hash_commit1> <hash_commit2>
# Exemplo: git diff a9b8c7d f1e2d3c
```
**Quando usar:** Para ver o que mudou entre dois pontos arbitrários no histórico do seu projeto.

### 5. Comparar Duas Branches

Para ver as diferenças entre a ponta de duas branches.

```bash
git diff <branch_original> <branch_a_comparar>
# Exemplo: git diff main feature/nova-funcionalidade
```
**Quando usar:** Antes de mesclar branches, para ver o que a outra branch introduziria no seu código.

### 6. Comparar uma Versão Específica de um Arquivo entre Commits/Branches

Você pode comparar a versão de um arquivo específico entre dois commits ou branches.

```bash
git diff <hash_commit1> <hash_commit2> -- <caminho/do/arquivo>
# Exemplo: git diff HEAD~1 HEAD -- README.md (compara README.md entre o penúltimo e o último commit)
```
**Quando usar:** Para ver a evolução de um arquivo específico.

### 7. Opções Úteis do `git diff`

* **`--stat`:** Mostra um resumo estatístico das mudanças (número de linhas adicionadas/removidas por arquivo), sem mostrar o patch completo.
    ```bash
    git diff --stat
    ```

* **`--color-words`:** Destaca as palavras que foram alteradas, em vez de linhas inteiras. Muito útil para ver pequenas edições em frases longas.
    ```bash
    git diff --color-words
    ```

* **`--name-only`:** Mostra apenas os nomes dos arquivos que foram alterados.
    ```bash
    git diff --name-only
    ```

* **`--compact-summary`:** Um resumo mais conciso das mudanças.
    ```bash
    git diff --compact-summary
    ```

* **`--word-diff`:** Exibe as diferenças palavra por palavra, com palavras adicionadas entre `{+ +}` e removidas entre `[- -]`.
    ```bash
    git diff --word-diff
    ```

## Conclusão
---

O comando `git diff` é uma ferramenta indispensável para qualquer usuário de Git. Ele oferece uma maneira poderosa e flexível de inspecionar as alterações em seu código em diferentes estágios do seu fluxo de trabalho, desde modificações locais até comparações complexas entre branches e commits. Dominar o `git diff` e suas diversas opções aprimorará significativamente sua capacidade de gerenciar e revisar seu código.
