# TF04 - E-commerce com Load Balancer Avançado

## Aluno
- **Nome:** Yago Canton
- **RA:** 6324598
- **Curso:** Análise e Desenvolvimento de Sistemas

## Arquitetura
- **Nginx (Load Balancer):** Balanceamento com SSL e rate limiting
- **Backend:** 3 instâncias Nginx simplificadas para alta disponibilidade
- **Frontend:** Loja virtual estática premium
- **Admin:** Painel administrativo de gestão e monitoramento (Acesso restrito)

## Funcionalidades Implementadas
- ✅ Load balancing com algoritmo least_conn
- ✅ Health checks automáticos
- ✅ Failover transparente
- ✅ SSL/TLS com certificado self-signed
- ✅ Rate limiting para proteção
- ✅ Logs detalhados com upstream info
- ✅ Compressão gzip
- ✅ Virtual hosts
- ✅ Redirecionamento 80 para 443

## Como Executar

### Pré-requisitos
- Docker e Docker Compose

### Execução
```bash
git clone [https://github.com/yagocanton21/EntregaTF04.git]
cd EntregaTF04

# Subir todos os serviços (certificados são gerados automaticamente no build)
docker-compose up -d --build

# Verificar status
docker-compose ps
```

### Endpoints
- **Frontend:** [https://localhost](https://localhost)
- **API (Cluster Info):** [https://localhost/api/info](https://localhost/api/info)
- **Admin:** [https://localhost/admin/login.html](https://localhost/admin/login.html)
- **Status:** [https://localhost/nginx-status](https://localhost/nginx-status)
- **Health:** [https://localhost/health](https://localhost/health)

> [!IMPORTANT]
> O certificado é **auto-assinado** (auto-signed). Ao acessar via navegador, clique em "Avançado" e "Prosseguir para localhost" para ignorar o aviso de segurança.

### Testes de Load Balancing

# Testar distribuição de carga (verificando o nó que responde)
for i in {1..10}; do
  curl -ks https://localhost/api/info | grep "node"
done

# Simular falha de instância
docker stop ecommerce-backend1

# Verificar failover
curl -k https://localhost/api/info

### Monitoramento
- **Logs detalhados:** `docker-compose logs nginx`
- **Métricas:** `https://localhost/nginx-status`
- **Health checks automáticos:** A cada 30s conforme configurado no `docker-compose.yml`.

### Validação
```bash
# Teste de load balancing
docker-compose up -d --build
for i in {1..6}; do curl -k -s https://localhost/api/info; done
docker-compose down
```

### Dicas
- Teste failover parando instâncias com `docker stop`.
- Configure logs detalhados (já embutido no `nginx.conf`).
- Implemente rate limiting adequado (10r/s configurado).
- Use health checks em todos os serviços (configurado no compose).
- Documente cada configuração do Nginx (comentários em `conf.d/load-balancer.conf`).
