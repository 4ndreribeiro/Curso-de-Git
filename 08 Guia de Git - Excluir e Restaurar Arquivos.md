# Guia Completo: Excluir e Restaurar Arquivos com Git
---

No dia a dia com o **Git**, é comum precisar lidar com a exclusão e a recuperação de arquivos. O Git oferece comandos específicos para remover arquivos do seu controle de versão e também para restaurar versões anteriores de arquivos, seja do seu diretório de trabalho, da área de preparação ou de um commit específico.

## Entendendo a Exclusão no Git
---

Quando você exclui um arquivo no seu sistema de arquivos de forma comum (ex: `rm arquivo.txt`), o Git detecta essa exclusão como uma modificação. Para que o Git registre essa exclusão no histórico do repositório, você precisa informá-lo explicitamente.

## 1. Excluindo Arquivos com `git rm`
---

O comando `git rm` é usado para remover arquivos do seu repositório Git. Ele faz duas coisas:

1.  **Remove o arquivo** do seu diretório de trabalho.
2.  **Adiciona a remoção** à área de preparação (staging area), marcando-o para ser comitado.

### Sintaxe Básica:

```bash
git rm <caminho/do/arquivo>
```

**Exemplo:**
Imagine que você tem um arquivo chamado `old_feature.js` que não é mais necessário.

```bash
# 1. Remova o arquivo do diretório de trabalho e adicione a remoção à staging area
git rm old_feature.js

# 2. Verifique o status - a remoção está preparada
git status
# Saída: Changes to be committed: deleted: old_feature.js

# 3. Comite a remoção
git commit -m "Remove arquivo de feature antiga"
```

### Opções Comuns do `git rm`:

* **`--cached`:** Remove o arquivo apenas do índice (staging area) e do repositório Git, mas o **mantém no seu diretório de trabalho**. Isso é útil se você adicionou acidentalmente um arquivo ao Git e agora quer que ele não seja mais rastreado, mas sem apagá-lo localmente.
    ```bash
    git rm --cached sensitive_data.log
    # O arquivo sensitive_data.log permanece no disco, mas não é mais rastreado pelo Git.
    ```
    Após usar `--cached`, você deve adicionar o arquivo a um `.gitignore` para evitar que ele seja adicionado novamente por engano.

* **`-f` ou `--force`:** Força a remoção de um arquivo que possui modificações não comitadas no diretório de trabalho ou que está na área de preparação. Use com cautela!
    ```bash
    git rm -f modified_file.txt
    ```

## 2. Restaurando Arquivos com `git restore`
---

O comando `git restore` (disponível a partir do Git 2.23) é a ferramenta moderna e recomendada para "desfazer" modificações em arquivos, restaurando seu estado a partir de um commit, da área de preparação ou do diretório de trabalho. Ele é mais claro e seguro que o uso anterior de `git checkout` para essa finalidade.

### Sintaxe Básica:

```bash
git restore [opções] <caminho/do/arquivo>
```

### Casos de Uso do `git restore`:

#### a) Desfazer Alterações no Diretório de Trabalho (Restaurar do Último Commit)

Isso reverte um arquivo para o seu estado no último commit, descartando quaisquer modificações não salvas no diretório de trabalho.

```bash
# Modifiquei um arquivo e quero descartar as mudanças
git restore my_document.txt
```
*Equivalente a: `git checkout -- my_document.txt` (método antigo).*

#### b) Desfazer Adição à Área de Preparação (Unstage)

Isso remove um arquivo da área de preparação, movendo-o de volta para o estado "modificado" no diretório de trabalho, sem descartar as alterações.

```bash
# Modifiquei um arquivo, usei 'git add', mas decidi não comitar ainda
git restore --staged my_file_to_unstage.js
```
*Equivalente a: `git reset HEAD my_file_to_unstage.js` (método antigo).*
Após este comando, `git status` mostrará `my_file_to_unstage.js` como "modified" (não preparado).

#### c) Restaurar um Arquivo para uma Versão de um Commit Específico

Você pode restaurar um arquivo para a sua versão em qualquer commit anterior.

```bash
# Restaura 'config.json' para o estado em um commit específico (hash)
git restore --source=<hash_do_commit> config.json
# Exemplo: git restore --source=a1b2c3d config.json

# Restaura 'index.html' para a versão de 3 commits atrás no branch atual
git restore --source=HEAD~3 index.html
```
*Isto irá restaurar o arquivo para o seu diretório de trabalho, mas não o adicionará automaticamente à área de preparação. Você precisará de um `git add` e `git commit` se quiser salvar essa restauração no histórico.*

#### d) Restaurar Arquivos Removidos do Diretório de Trabalho (Não Comitados)

Se você acidentalmente removeu um arquivo do seu diretório de trabalho (usando `rm`) e essa remoção ainda não foi comitada, você pode restaurá-lo.

```bash
# Acidentalmente removi 'lost_file.txt' com 'rm'
git restore lost_file.txt
```
Isso pegará a última versão rastreada do `lost_file.txt` e a restaurará para o seu diretório de trabalho.

## Conclusão
---

O Git oferece ferramentas robustas para gerenciar a exclusão e a recuperação de arquivos. O comando `git rm` é sua escolha para remover arquivos do controle de versão de forma definitiva. Já o `git restore` é um comando moderno e flexível para desfazer mudanças em vários estágios, permitindo que você recupere versões de arquivos do seu histórico ou desfaça operações de preparação. Dominar esses comandos é essencial para manter seu repositório limpo e seu trabalho seguro.