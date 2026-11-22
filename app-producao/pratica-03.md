# Prática: Etapa 3 • Processos e Recursos do Servidor

## Atividade 1: Execução em Background e Encerramento Gracioso (Requisito 07)

### 1. O que os operadores `&` e `> logs/api.log 2>&1` realizaram na linha de execução da aplicação?

O operador `&` executa a aplicação em segundo plano, permitindo continuar utilizando o terminal. O redirecionamento `> logs/api.log 2>&1` envia as saídas padrão e de erro para o arquivo de log.

### 2. O que a variável especial `$!` capturou para ser salva dentro de `pids/api.pid`?

A variável `$!` captura o PID (identificador do processo) executado mais recentemente em segundo plano. Esse número foi armazenado em `pids/api.pid` para permitir localizar e encerrar o processo posteriormente.

### 3. Qual é a diferença técnica essencial entre o sinal SIGTERM (`kill -15`) e o sinal SIGKILL (`kill -9`)? Por que na administração de servidores web é uma péssima prática iniciar o encerramento de serviços diretamente com `kill -9`?

O SIGTERM (`kill -15`) solicita o encerramento do processo de forma graciosa, permitindo finalizar operações e liberar recursos. Já o SIGKILL (`kill -9`) força o encerramento imediato, sem permitir uma finalização adequada.

Utilizar diretamente o `kill -9` é uma péssima prática porque pode causar perda de dados, deixar operações incompletas e impedir a liberação correta de recursos. Por isso, deve-se priorizar o SIGTERM.

## Atividade 2: Desafio Prático — Diagnóstico de Hardware e Portas de Rede (Requisito 08)

### 1. Auditoria de Memória RAM: Exibir a memória total, utilizada e alocada para buffers/cache.

**Comando utilizado:**

```bash
free -h
```

O comando apresenta a memória RAM em unidades legíveis.

**Resultado encontrado:**
- Memória total: 15 GiB.
- Memória utilizada: 2,5 GiB.
- Memória livre: 12 GiB.
- Buffers/cache: 765 MiB.
- Memória disponível: 12 GiB.

A memória utilizada corresponde aos processos ativos. O Linux também utiliza parte da RAM para buffers e cache, podendo liberar esse espaço quando necessário.

### 2. Auditoria de Armazenamento: Verificar a porcentagem de espaço em disco utilizada na partição raiz (/).

**Comando utilizado:**

```bash
df -h /
```

O comando apresenta o espaço total, utilizado e disponível na partição raiz.

**Resultado encontrado:**
- Tamanho total: 1007 GB.
- Espaço utilizado: 38 GB.
- Espaço disponível: 919 GB.
- Utilização: 4%.

### 3. Auditoria de Portas Ocupadas: Identificar o processo e o PID que está escutando na porta 8080 e encerrá-lo graciosamente.

**Comandos utilizados:**

```bash
python3 -m http.server 8080 --directory backend > logs/api.log 2>&1 &
echo $! > pids/api.pid
sudo lsof -i :8080
kill -15 $(cat pids/api.pid)
```

O servidor foi iniciado em segundo plano e seu PID foi armazenado em um arquivo. O comando `lsof` identificou o processo que estava utilizando a porta 8080. Por fim, foi enviado o sinal SIGTERM para encerrar o servidor graciosamente.

**Resultado encontrado:**
- Processo: `python3`.
- PID: `11953`.
- Usuário: `fernando`.
- Porta: `8080`.
- Estado: LISTEN.

O processo foi identificado corretamente como o servidor Python escutando na porta 8080.

## Atividade 3: Pesquisa Autônoma — Métricas de Memória no Top/Htop (Requisito 08)

### 1. Qual é a diferença conceitual entre as colunas VIRT (Virtual Image), RES (Resident Size) e SHR (Shared Memory)?

- **VIRT:** representa o total de memória virtual utilizada ou reservada pelo processo.
- **RES:** representa a quantidade de memória física que o processo está utilizando atualmente.
- **SHR:** representa a parte da memória residente que pode ser compartilhada com outros processos.

### 2. Ao monitorar um processo web em execução (Node.js ou Python), qual dessas três métricas reflete com maior precisão a quantidade real de memória física que o processo está ocupando na máquina?

A métrica **RES** é a mais adequada, pois indica a quantidade de memória física residente utilizada pelo processo, sem incluir páginas que foram transferidas para a memória swap.

## Atividade 4: Experimentação com Inteligência Artificial

### 1. No Linux Ubuntu, por que processos que são encerrados abruptamente com `kill -9` podem deixar portas TCP presas em estado TIME_WAIT ou conexões de banco de dados órfãs?

O `kill -9` encerra o processo imediatamente, sem permitir a execução de rotinas de limpeza. Isso pode interromper conexões com bancos de dados e deixar conexões TCP em estados temporários, como `TIME_WAIT`, que são administrados pelo sistema operacional.

### 2. Como uma aplicação Node.js ou Python deve tratar o sinal SIGTERM para realizar um encerramento gracioso (Graceful Shutdown)?

A aplicação deve capturar o SIGTERM e iniciar uma rotina de encerramento, parando de aceitar novas requisições, finalizando as operações em andamento, fechando conexões com o banco de dados e liberando recursos antes de encerrar o processo.