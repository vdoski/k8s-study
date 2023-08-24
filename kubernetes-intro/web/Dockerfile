FROM alpine:3.12.12 
ARG UID=1001
WORKDIR /app
RUN apk add --no-cache nginx && \
    ln -fs /dev/stdout /var/log/nginx/access.log && ln -fs /dev/stderr /var/log/nginx/error.log && \
    chown ${UID} -R /var/log/nginx /var/lib/nginx /app && \
    echo "server {listen 8000;location / {root   /app;index  index.html;}}" > /etc/nginx/conf.d/default.conf && \
    echo "pid /tmp/nginx.pid;" >> /etc/nginx/nginx.conf
USER ${UID}
EXPOSE 8000
CMD ["nginx","-g","daemon off;"]