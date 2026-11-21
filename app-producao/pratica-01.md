# Prática: Etapa 1 • Estrutura de Servidor Web

## Atividade 1: Navegação, Inspeção e Deploy Atômico (Requisito 01 e 03)

### 1. Qual é a diferença entre a saída de um comando ls comum e o comando ls -la? Que tipo de arquivos a flag -a tornou visíveis?

O comando ls lista os arquivos e diretórios visíveis. Já o ls -la mostra todos os arquivos, incluindo os ocultos (que começam com ponto .), além de exibir informações detalhadas, como permissões, proprietário, tamanho e data de modificação. 

### 2. O que o comando ln -s realizou? Por que a criação de links simbólicos para apontar para a versão ativa de uma aplicação (current-api) é uma prática padrão na indústria para atualizações e deploys atômicos (zero downtime)?

O comando ln -s backend/api current-api criou um link simbólico chamado current-api, que aponta para o diretório backend/api. Essa prática permite trocar a versão ativa de uma aplicação rapidamente, apenas alterando o link simbólico, sem precisar interromper o serviço. Isso facilita atualizações e deploys atômicos, reduzindo o tempo de indisponibilidade (zero downtime).

## Atividade 2: Limpeza de Resíduos e Governança

### 1. Localização e remoção dos arquivos temporários

Comandos utilizados:

```bash
find . -type f -name "*.tmp"
find . -type f -name "*.tmp" -delete
```

Explicação: Utilizei o comando `find` para localizar todos os arquivos com extensão `.tmp` e depois removê-los com a opção `-delete`.

### 2. Cópia do arquivo de configuração

Comando utilizado:

```bash
cp config/env.template backend/.env
```

Explicação: Copiei o arquivo modelo `env.template` para `backend/.env`, criando o arquivo de configuração ativa da aplicação.

### 3. Configuração do arquivo .gitignore

Comando utilizado:

```bash
vim .gitignore
```

Conteúdo adicionado:

```gitignore
*.tmp
logs/*.log
backend/.env
```

Explicação: Configurei o `.gitignore` para impedir que arquivos temporários, logs de execução e arquivos com credenciais sejam incluídos no controle de versão do Git.

## Atividade 3: Filtros de Tempo e Tamanho no Find

### 1. Arquivos modificados nos últimos 7 dias

Comando:

```bash
find logs/ -type f -mtime -7
```

Explicação: Localiza apenas arquivos dentro da pasta `logs/` que foram modificados nos últimos 7 dias.

### 2. Diferença entre -mtime -7 e -mtime +7**

- `-mtime -7`: encontra arquivos modificados há menos de 7 dias.
- `-mtime +7`: encontra arquivos modificados há mais de 7 dias.

O sinal de menos representa um período mais recente, enquanto o sinal de mais representa um período mais antigo.

### 3. Arquivos maiores que 10 megabytes

Comando:

```bash
find . -type f -size +10M
```

Explicação: Localiza arquivos maiores que 10 MiB dentro do projeto, utilizando o parâmetro `-size` para filtrar pelo tamanho.
