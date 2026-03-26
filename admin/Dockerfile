FROM nginx:alpine
COPY . /usr/share/nginx/html
RUN sed -i 's/index  index.html index.htm;/index dashboard.html index.html index.htm; auth_basic off;/' /etc/nginx/conf.d/default.conf
