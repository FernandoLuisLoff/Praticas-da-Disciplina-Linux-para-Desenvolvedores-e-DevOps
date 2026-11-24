# Prática: Etapa 4 • Testes HTTP e Acesso Remoto SSH

## Atividade 1: Execução e Diagnóstico HTTP com Shell Scripting

# Atividade 1: Execução e Diagnóstico HTTP com Shell Scripting (Requisito 09)

### 1. O significado das principais informações retornadas pelo cabeçalho HTTP do `curl -I` (código de status HTTP, tipo de conteúdo MIME e servidor).

O comando `curl -I` realiza uma requisição HTTP do tipo HEAD, retornando apenas os cabeçalhos da resposta.

- **Código de status HTTP:** indica o resultado da requisição. `200 OK` significa sucesso, `404 Not Found` indica que o recurso não foi encontrado e `500 Internal Server Error` indica um erro interno do servidor.
- **Tipo de conteúdo MIME (Content-Type):** informa o formato do conteúdo retornado, como `text/html`, `text/plain` ou `application/javascript`.
- **Servidor (Server):** identifica o software responsável por atender à requisição. Nesse caso, normalmente aparece `SimpleHTTP/0.6 Python/3.x`.

### 2. Para que serve o arquivo `~/.bashrc` e por que é necessário executar o comando `source ~/.bashrc` para que os novos aliases tenham efeito imediato na sessão atual.

O arquivo `~/.bashrc` armazena configurações do Bash, como variáveis de ambiente, aliases e personalizações do terminal.
O comando `source ~/.bashrc` recarrega essas configurações na sessão atual, permitindo utilizar imediatamente as novas variáveis e aliases, sem precisar fechar e abrir outro terminal.


## Atividade 2: Desafio Prático — Governança Criptográfica SSH (Requisito 10)

### 1. Geração de Chaves Ed25519

```bash
ssh-keygen -t ed25519 -C "deploy@app-producao.local" -f ~/.ssh/id_ed25519_app -N ""
```

O algoritmo Ed25519 utiliza criptografia de curva elíptica, oferecendo segurança moderna e bom desempenho. A chave privada é utilizada para autenticação, enquanto a chave pública é cadastrada no servidor. A opção `-N ""` cria a chave sem senha, facilitando a automação dos deploys.

### 2. Hardening de Permissões

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_ed25519_app
```

A permissão **700** na pasta `.ssh` garante que somente o proprietário tenha acesso de leitura, escrita e execução (rwx------). A permissão de execução permite acessar os arquivos dentro do diretório.

A permissão **600** na chave privada permite somente ao proprietário realizar leitura e escrita (rw-------), impedindo que outros usuários tenham acesso às credenciais privadas.

### 3. Autorização de Acesso

```bash
cat ~/.ssh/id_ed25519_app.pub >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

A chave pública é adicionada ao arquivo `authorized_keys`, permitindo que o servidor reconheça e autorize conexões autenticadas pela chave privada correspondente.

A permissão **600** garante que somente o proprietário possa ler e modificar o arquivo, protegendo a lista de chaves autorizadas contra alterações indevidas.

### 4. Auditoria da Estrutura

```bash
ls -la ~/.ssh
```

O comando lista os arquivos do diretório `.ssh`, exibindo permissões, proprietário, grupo, tamanho e data de modificação, permitindo verificar se as configurações de segurança foram aplicadas corretamente.

### 5. Fundamentação Teórica

O princípio do menor privilégio determina que cada usuário deve possuir apenas as permissões necessárias para executar suas atividades. No SSH, esse princípio é aplicado por meio de permissões restritivas nos diretórios e arquivos de credenciais.

As permissões **700** e **600** impedem que outros usuários acessem informações sensíveis, reduzindo os riscos de exposição da chave privada e de alterações não autorizadas nas chaves de acesso.

A utilização de chaves Ed25519 proporciona autenticação criptográfica sem depender de senhas tradicionais, sendo adequada para ambientes de integração e entrega contínua (CI/CD). Como a chave privada foi criada sem passphrase, sua proteção depende especialmente das permissões do sistema de arquivos.

## Atividade 3: [Pesquisa Autônoma - Além da Disciplina] Hardening do Daemon OpenSSH

### 1. Quais são e o que fazem as 3 diretivas de segurança fundamentais recomendadas para proteger servidores expostos à internet (`PasswordAuthentication no`, `PermitRootLogin no` e `PubkeyAuthentication yes`)?

- **PasswordAuthentication no:** desativa a autenticação por senha, impedindo tentativas de acesso utilizando senhas tradicionais.
- **PermitRootLogin no:** impede o acesso remoto direto utilizando a conta root, reduzindo os riscos de comprometimento do usuário com privilégios administrativos.
- **PubkeyAuthentication yes:** habilita a autenticação por chaves criptográficas SSH, permitindo acesso seguro por meio de um par de chaves pública e privada.

### 2. Por que manter a autenticação tradicional por login e senha aberta na porta 22 torna o servidor vulnerável a ataques automatizados de força bruta e dicionário?

Porque a porta 22 é amplamente conhecida como a porta padrão do SSH e constantemente recebe tentativas automatizadas de conexão. Os atacantes utilizam ferramentas que testam milhares de combinações de usuários e senhas (força bruta) ou listas de senhas comuns (ataques de dicionário). Caso encontrem credenciais fracas ou reutilizadas, podem obter acesso não autorizado ao servidor. Desativar a autenticação por senha e utilizar chaves SSH reduz significativamente esse risco.


## Atividade 4: Experimentação com Inteligência Artificial

### 1. No Linux, por que o cliente OpenSSH se recusa terminantemente a utilizar uma chave privada caso ela esteja com permissão 644 ou 777, exibindo o erro 'Permissions are too open'? Explique a fundamentação técnica de segurança defensiva por trás dessa decisão de projeto.

O OpenSSH verifica as permissões da chave privada para impedir que outros usuários do sistema tenham acesso às credenciais. A permissão **644** permite que outros usuários leiam o arquivo, enquanto a **777** permite leitura, escrita e execução para todos.

Caso uma chave privada seja exposta, terceiros podem copiá-la e utilizá-la para acessar servidores sem autorização. Por isso, o OpenSSH rejeita chaves com permissões inseguras, exibindo o erro `Permissions are too open`.

Essa proteção está relacionada ao princípio do **menor privilégio**, que determina que somente o proprietário deve ter acesso às informações necessárias. A permissão recomendada é **600**, permitindo leitura e escrita exclusivamente ao proprietário, protegendo a chave contra acessos indevidos.