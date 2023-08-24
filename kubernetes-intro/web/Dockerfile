FROM httpd
WORKDIR /app
COPY ./app /app
COPY ./my-httpd.conf /usr/local/apache2/conf/httpd.conf
ARG UID=1001
USER ${UID}
EXPOSE 8000