# Balanceamento de Carga (Load Balancing)

Neste projeto, o Nginx é o responsável pelo balanceamento de carga entre as instâncias de backend. O objetivo é garantir que nenhuma instância fique sobrecarregada e que o sistema seja resiliente a falhas individuais de containers.

## Arquitetura de Carga

O Nginx utiliza um bloco `upstream` para agrupar as instâncias de backend em um pool logicamente unificado:

```nginx
upstream api_servers {
    least_conn;
    server backend1:80;
    server backend2:80;
    server backend3:80;
}
```

### Algoritmo: `least_conn` (Menos Conexões)

Foi escolhido o algoritmo **Least Connections** para este projeto. O Nginx direciona as novas requisições para o backend que possuir o menor número de conexões ativas no momento.
- **Vantagens**: Melhor distribuição de carga se as requisições demorarem tempos diferentes para serem processadas.
- **Por que não Round Robin?**: O Round Robin distribui sequencialmente, o que pode causar gargalos se um backend estiver preso em uma tarefa pesada e receber novas requisições.

## Alta Disponibilidade e Escalonamento

A infraestrutura é definida no `docker-compose.yml` com as seguintes características:

- **Instâncias**: 3 backends replicados (`backend1`, `backend2`, `backend3`).
- **Rede Isolada**: `ecommerce-net` (driver: bridge) garante a comunicação segura entre os containers.
- **Health Checks**: Os backends possuem verificação de integridade a cada 10 segundos via `wget` no endpoint `/health`.

### Tolerância a Falhas (Failover)

No arquivo `load-balancer.conf`, configuramos o diretiva `proxy_next_upstream`:

```nginx
proxy_next_upstream error timeout http_500 http_502 http_503 http_504;
```

Isto significa que se o backend retornar um erro (como 500 ou 502) ou sofrer um timeout, o Nginx tentará automaticamente a mesma requisição com o próximo backend disponível antes de retornar uma mensagem de erro ao cliente.

## Verificação de Distribuição

Para verificar como o tráfego está sendo distribuído, observe os logs do container do Nginx:

```bash
docker logs -f ecommerce-lb
```

Nos campos `uaddr` e `uart`, você verá qual IP interno e quanto tempo cada backend levou para responder a cada requisição.
