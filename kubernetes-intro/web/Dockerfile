FROM httpd
COPY ./app /usr/local/apache2/htdocs/
WORKDIR /app
COPY ./app /app
ARG UID=1001
USER ${UID}
EXPOSE 8000