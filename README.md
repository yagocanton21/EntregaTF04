# TF04 - E-commerce com Load Balancer Avançado

## Aluno
- **Nome:** Yago Canton
- **RA:** 6324598
- **Curso:** Análise e Desenvolvimento de Sistemas

## Arquitetura
- **Nginx:** Load balancer com SSL e rate limiting
- **Backend:** 3 instâncias da API para alta disponibilidade
- **Frontend:** Loja virtual estática
- **Admin:** Painel administrativo

## Funcionalidades Implementadas
- ✅ Load balancing com algoritmo least_conn
- ✅ Health checks automáticos
- ✅ Failover transparente
- ✅ SSL/TLS com certificado self-signed
- ✅ Rate limiting para proteção
- ✅ Logs detalhados com upstream info (JSON estruturado)
- ✅ Compressão gzip
- ✅ Virtual hosts

## Como Executar

### Pré-requisitos
- Docker e Docker Compose

### Execução
```bash
git clone https://github.com/yagocanton21/EntregaTF04.git
cd EntregaTF04

# Subir todos os serviços (certificados já presentes em nginx/ssl/)
docker-compose up -d --build

# Verificar status
docker-compose ps
```

### Endpoints
- **Frontend:** [https://localhost](https://localhost)
- **API:** [https://localhost/api/](https://localhost/api/)
- **Admin:** [https://localhost/admin/dashboard.html](https://localhost/admin/dashboard.html)
- **Status:** [https://localhost/nginx-status](https://localhost/nginx-status)
- **Health:** [https://localhost/health](https://localhost/health)

### Testes de Load Balancing

```bash
# Testar distribuição de carga
for i in {1..10}; do
  curl -ks https://localhost/api/info | grep instance_id
done

# Simular falha de instância
docker stop ecommerce-backend1

# Verificar failover
curl -k https://localhost/api/info
```

### Monitoramento
- **Logs detalhados:** `docker logs ecommerce-lb`
- **Métricas:** `https://localhost/status` (JSON) ou `https://localhost/nginx-status` (Stub)
- **Dashboard:** Disponível no painel Admin com monitoramento em tempo real dos 3 nós.

### Validação
```bash
# Teste de load balancing
docker-compose up -d --build
for i in {1..6}; do curl -ks https://localhost/api/info; done
docker-compose down
```
