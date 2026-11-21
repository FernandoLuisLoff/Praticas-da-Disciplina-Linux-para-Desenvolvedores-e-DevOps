# Prática – Etapa 2: Permissões e Auditoria de Logs

## Atividade 1: Hardening de Permissões e Análise Crítica

### 1. Significado da permissão 600

A permissão 600 significa que somente o dono do arquivo possui permissão de leitura e escrita. O grupo e os demais usuários não possuem nenhuma permissão de acesso.

### 2. Por que o arquivo backend/.env exige permissão 600?

Porque esse arquivo pode armazenar informações sensíveis, como senhas, tokens e credenciais de acesso ao banco de dados. A permissão 600 impede que outros usuários do sistema leiam essas informações.

### 3. Por que chmod 777 é uma falha de segurança?

Porque concede permissão total de leitura, escrita e execução para todos os usuários. Isso permite que qualquer usuário modifique ou execute arquivos da aplicação, podendo causar alterações indevidas e comprometer a segurança do servidor.

## Atividade 2: Edição de Configurações no Terminal (Requisito 05)

Foi utilizado o editor de terminal **Vim** para editar o arquivo `backend/.env`.

Durante a edição, foram realizadas as seguintes alterações:

- Modificada a variável `PORT` para `PORT=8080`.
- Adicionada a variável `MAX_REQUEST_LIMIT=100`.

**Comandos e atalhos utilizados no Vim:**

- `i`: Entrar no modo de inserção para editar o conteúdo.
- `ESC`: Sair do modo de inserção e retornar ao modo normal.
- `:w`: Salvar as alterações no arquivo.
- `:q`: Sair do editor.

Após salvar e sair do editor, foi executado o comando `cat backend/.env` para verificar se as alterações foram aplicadas corretamente.

## Atividade 3: Mineração Forense de Logs (Requisito 06)

### 1. Identificação de ataque de força bruta

Comando utilizado:

```bash
grep '/api/v1/login' logs/access.log | awk '$8 == 401 {print $1}' | sort | uniq -c | sort -nr | head -1
```

O `grep` filtra as requisições de login, o `awk` extrai os IPs com erro 401, o `sort` organiza os dados, o `uniq -c` contabiliza as ocorrências e o `head -1` apresenta o IP com mais tentativas.

**Resultado:** IP `192.168.1.15`, com 10 tentativas.

### 2. Identificação de falhas críticas

Comando utilizado:

```bash
awk '$8 == 500 {count++} END {print count+0}' logs/access.log
```

O `awk` identifica as requisições com código HTTP 500 e contabiliza todas as ocorrências.

**Resultado:** 4 requisições com erro HTTP 500.

### 3. Geração de relatório

Comando utilizado:

```bash
awk '$8 ~ /^[45][0-9][0-9]$/ {print $1}' logs/access.log | sort -u > logs/relatorio-incidentes.txt
```

O `awk` filtra os erros HTTP 4xx e 5xx e extrai os IPs. O `sort -u` organiza e remove duplicações. O operador `>` salva o resultado no arquivo `logs/relatorio-incidentes.txt`.

**IPs identificados no relatório:**

```text
10.0.0.45
10.0.0.99
172.16.0.8
192.168.1.15
```

## Atividade 4: Edição em Lote com Sed (Requisito 06)

### 1. Comando para alterar a porta:

```bash
sed -i 's/PORT=8080/PORT=3000/' backend/.env
```

O comando utiliza o `sed` para substituir `PORT=8080` por `PORT=3000`. A flag `-i` aplica a alteração diretamente no arquivo, sem precisar abrir um editor.

### 2. Vantagens do Sed no CI/CD:

O `sed` permite automatizar alterações em arquivos de configuração, tornando os processos mais rápidos e padronizados, reduzindo erros humanos e dispensando a edição manual.

## Atividade 5: Experimentação com Inteligência Artificial

### 1. Por que o chmod 777 representa um risco?

A permissão `777` concede leitura, escrita e execução para todos os usuários. Em diretórios de upload, isso permite que usuários não autorizados modifiquem ou enviem arquivos maliciosos, podendo possibilitar execução remota de código (RCE) caso o servidor execute esses arquivos.

### 2. Configuração correta para mitigar o risco

Recomenda-se utilizar um usuário exclusivo para a aplicação, um grupo específico e permissões restritas:

- Usuário: `appuser`.
- Grupo: `www-data`.
- Diretório: `750`.
- Arquivos: `640`.

Também é recomendado armazenar uploads fora da pasta pública e impedir a execução de scripts nesse diretório.

**Conclusão:** utilizar permissões mínimas reduz a superfície de ataque e protege o servidor contra modificações e execuções não autorizadas.