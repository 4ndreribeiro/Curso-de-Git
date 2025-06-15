# Guia Completo: Histórico e Versões no Git - Comandos `git log` e `git show`
---

O **Git** é, em sua essência, uma ferramenta para rastrear e gerenciar o histórico de alterações de um projeto. A capacidade de visualizar esse histórico e inspecionar versões específicas de arquivos é fundamental para entender o desenvolvimento do projeto, depurar problemas e colaborar de forma eficaz. Os comandos `git log` e `git show` são as principais ferramentas para essa finalidade.

## O que é o Histórico no Git?
---

No Git, o histórico é uma série de **commits**, onde cada commit é um "instantâneo" completo do seu projeto em um determinado momento. Cada commit aponta para o commit anterior (seu "pai"), formando uma cadeia que representa a evolução do projeto.

* **Imutável:** Uma vez que um commit é criado, ele não pode ser alterado. Isso garante a integridade do histórico.
* **Detalhado:** Cada commit inclui metadados como autor, data, mensagem descritiva e um hash SHA-1 único.

## 1. `git log` - Visualizando o Histórico de Commits
---

O comando `git log` é usado para exibir o histórico de commits do seu repositório. Por padrão, ele mostra os commits em ordem cronológica reversa (os mais recentes primeiro).

### Sintaxe Básica:

```bash
git log
```

**Saída Padrão do `git log`:**

```
commit b2a3c4d5e6f7a8b9c0d1e2f3a4b5c6d7e8f9a0b1 (HEAD -> main)
Author: Seu Nome <seu.email@example.com>
Date:   Fri Jun 14 10:00:00 2024 -0300

    Adiciona funcionalidade de login de usuário

commit f1e2d3c4b5a6978d7c6b5a4d3c2b1a0f9e8d7c6b
Author: Outro Desenvolvedor <outro.email@example.com>
Date:   Thu Jun 13 18:30:00 2024 -0300

    Corrige bug na validacao de formulario

commit a9b8c7d6e5f4g3h2i1j0k9l8m7n6o5p4q3r2s1t0
Author: Seu Nome <seu.email@example.com>
Date:   Wed Jun 12 09:15:00 2024 -0300

    Commit inicial do projeto
```

* **`commit <hash>`:** O hash SHA-1 completo do commit (um identificador único).
* **`Author:`:** O autor do commit.
* **`Date:`:** A data e hora do commit.
* **Mensagem do Commit:** A descrição das alterações.

### Opções Comuns do `git log`:

* **`--oneline`:** Exibe cada commit em uma única linha, mostrando apenas o hash curto e a mensagem do commit. Ideal para uma visão geral compacta.
    ```bash
    git log --oneline
    # Exemplo:
    # b2a3c4d (HEAD -> main) Adiciona funcionalidade de login de usuário
    # f1e2d3c Corrige bug na validacao de formulario
    # a9b8c7d Commit inicial do projeto
    ```

* **`--graph`:** Desenha um gráfico ASCII do histórico de commits, útil para visualizar branches e merges.
    ```bash
    git log --graph
    ```

* **`--decorate`:** Mostra onde os branches e tags estão apontando. Geralmente usado com `--oneline` ou `--graph`.
    ```bash
    git log --oneline --decorate --graph
    ```

* **`-p` ou `--patch`:** Mostra o patch (as diferenças) introduzido por cada commit. É útil para ver as linhas que foram adicionadas, removidas ou modificadas.
    ```bash
    git log -p -2 # Mostra os dois últimos commits com seus patches
    ```

* **`--stat`:** Mostra estatísticas resumidas das mudanças para cada commit (quais arquivos foram alterados e quantas linhas foram adicionadas/removidas).
    ```bash
    git log --stat
    ```

* **`--author="Nome do Autor"`:** Filtra commits por autor.
    ```bash
    git log --author="Seu Nome"
    ```

* **`--grep="palavra-chave"`:** Filtra commits por palavra-chave na mensagem do commit.
    ```bash
    git log --grep="login"
    ```

* **`--since="2 weeks ago"` ou `--until="YYYY-MM-DD"`:** Filtra commits por data.
    ```bash
    git log --since="yesterday"
    git log --until="2024-01-01"
    ```

* **`-- <caminho/do/arquivo>`:** Mostra o histórico de commits de um arquivo específico.
    ```bash
    git log -- README.md
    ```

* **`-n <quantidade>`:** Limita o número de commits exibidos.
    ```bash
    git log -n 5 # Mostra os 5 últimos commits
    ```

## 2. `git show` - Inspecionando um Commit ou Objeto Específico
---

O comando `git show` é usado para exibir informações detalhadas sobre um objeto Git específico, que na maioria das vezes é um commit. Ele mostra a mensagem do commit, o autor, a data e, por padrão, o patch (as diferenças) introduzido por esse commit.

### Sintaxe Básica:

```bash
git show <hash_do_commit>
# Ou (para o último commit se nenhum hash for especificado)
git show
```

**Exemplo de Uso:**

Para ver os detalhes do último commit:
```bash
git show HEAD
# Ou simplesmente: git show
```

Para ver os detalhes de um commit específico (substitua pelo hash completo ou curto):
```bash
git show b2a3c4d
```

**Saída de `git show` (exemplo parcial):**

```
commit b2a3c4d5e6f7a8b9c0d1e2f3a4b5c6d7e8f9a0b1 (HEAD -> main)
Author: Seu Nome <seu.email@example.com>
Date:   Fri Jun 14 10:00:00 2024 -0300

    Adiciona funcionalidade de login de usuário

diff --git a/src/auth.py b/src/auth.py
index e69de29..7d8b9c0 100644
--- a/src/auth.py
+++ b/src/auth.py
@@ -0,0 +1,10 @@
+def login(username, password):
+    if username == "admin" and password == "secret":
+        print("Login bem-sucedido!")
+        return True
+    else:
+        print("Nome de usuário ou senha inválidos.")
+        return False
+
+if __name__ == "__main__":
+    login("admin", "secret")
```

### Opções Comuns do `git show`:

* **`--name-only`:** Mostra apenas os nomes dos arquivos alterados no commit.
    ```bash
    git show --name-only b2a3c4d
    ```

* **`--stat`:** Mostra estatísticas resumidas das mudanças (similar ao `git log --stat`).
    ```bash
    git show --stat b2a3c4d
    ```

* **`--pretty=format:"..."`:** Personaliza a saída. Útil para extrair informações específicas do commit.
    ```bash
    git show --pretty=format:"%h - %an, %ar : %s" b2a3c4d
    # %h: hash curto, %an: nome do autor, %ar: data relativa, %s: assunto
    ```

* **`git show <hash>:<caminho/do/arquivo>`:** Exibe o conteúdo de um arquivo em um commit específico.
    ```bash
    git show b2a3c4d:src/auth.py
    ```
    Isso mostrará o conteúdo do arquivo `src/auth.py` como ele estava no commit `b2a3c4d`.

## Conclusão
---

Os comandos `git log` e `git show` são ferramentas indispensáveis para navegar e entender o histórico do seu projeto Git. Com `git log`, você pode explorar a linha do tempo dos commits de diversas maneiras, filtrando e formatando a saída para suas necessidades. Com `git show`, você pode mergulhar nos detalhes de um commit específico, vendo exatamente quais mudanças foram introduzidas e como um arquivo se parecia em um determinado ponto da história do projeto. Dominar esses comandos é um passo crucial para gerenciar efetivamente seus projetos versionados com Git.
