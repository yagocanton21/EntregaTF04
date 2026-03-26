FROM nginx:alpine

# Remove o index padrão do Nginx
RUN rm -rf /usr/share/nginx/html/*

# Cria um script de inicialização amigável ao padrão do Nginx Docker
# Ele cria o arquivo /health e o index.html com o hostname no momento da inicialização
RUN printf '#!/bin/sh\n\
echo "<h1>Nó do Backend: $(hostname)</h1>" > /usr/share/nginx/html/index.html\n\
echo "OK" > /usr/share/nginx/html/health\n\
echo "{\"status\": \"online\", \"type\": \"nginx-static\", \"node\": \"$(hostname)\"}" > /usr/share/nginx/html/info\n' > /docker-entrypoint.d/20-setup.sh

RUN chmod +x /docker-entrypoint.d/20-setup.sh

EXPOSE 80
