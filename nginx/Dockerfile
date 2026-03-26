FROM nginx:alpine

# Copia as configurações globais e modulares
COPY nginx.conf /etc/nginx/nginx.conf
COPY conf.d/ /etc/nginx/conf.d/

# Copia os certificados SSL
COPY ssl/ /etc/nginx/ssl/

EXPOSE 80 443
