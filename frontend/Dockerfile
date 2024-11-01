FROM nginx:latest

RUN apt-get update && apt-get install -y gettext-base

# Copia los archivos HTML y CSS
COPY index.html /usr/share/nginx/html/index.html.template
COPY style.css /usr/share/nginx/html/

# Usa envsubst para reemplazar SERVER_COLOR en el index.html
CMD envsubst '${SERVER_COLOR}' < /usr/share/nginx/html/index.html.template > /usr/share/nginx/html/index.html && nginx -g 'daemon off;'