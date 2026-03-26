# Configuração do Servidor Nginx (Load Balancer)

O Nginx atua como o ponto de entrada principal (Reverse Proxy) e balanceador de carga para a aplicação. Ele é responsável por gerenciar conexões SSL, distribuir tráfego entre múltiplos backends e proteger a infraestrutura contra abusos.

## Estrutura de Diretórios

- `/etc/nginx/nginx.conf`: Configuração global do Nginx.
- `/etc/nginx/conf.d/`: Contém arquivos de configuração modularizados:
    - `load-balancer.conf`: Regras de roteamento e definições de upstream.
    - `ssl.conf`: Parâmetros de segurança para TLS/SSL.
- `/etc/nginx/ssl/`: Armazena os certificados (`cert.pem`) e chaves privadas (`key.pem`).

## Principais Funcionalidades

### 1. Terminologia SSL (HTTPS)
O Nginx está configurado para ouvir na porta 443 com SSL habilitado. Todas as requisições na porta 80 (HTTP) são automaticamente redirecionadas para HTTPS (301 Moved Permanently).

- **Protocolos Suportados**: TLSv1.2 e TLSv1.3.
- **Segurança**: Utiliza HSTS (Strict-Transport-Security) para forçar o uso de HTTPS pelo navegador durante 2 anos.

### 2. Rate Limiting (Controle de Taxa)
Para proteger o backend contra ataques de força bruta ou excesso de requisições, foi implementado um limite de taxa:
- **Zona**: `mylimit` (10MB de memória para rastrear IPs).
- **Taxa**: 10 requisições por segundo (10r/s) por IP.
- **Burst**: Permite picos de até 20 requisições sem atraso imediato (`nodelay`).

### 3. Regras de Roteamento (Proxy Pass)
As rotas são distribuídas com base no prefixo da URL:
- `/`: Encaminha para o container `frontend` (porta 80).
- `/api/`: Encaminha para o grupo de `api_servers` (balanceamento de carga).
- `/admin/`: Encaminha para o container `admin` (porta 80).
- `/nginx-status`: Exibe métricas básicas do Nginx (conexões ativas, aceitas, etc).
- `/health`: Endpoint simples para verificação de integridade do próprio balanceador.

### 4. Logs Customizados
O formato de log foi estendido para incluir métricas de performance do upstream:
- `rt`: Tempo total da requisição.
- `uct`: Tempo de conexão com o upstream.
- `uart`: Tempo de resposta do upstream.
- `uaddr`: Endereço IP do container backend que processou a requisição.
