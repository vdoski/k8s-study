FROM httpd
ARG UID=1001
WORKDIR /app
COPY ./app /app
COPY ./my-httpd.conf /usr/local/apache2/conf/httpd.conf
RUN mkdir -p /usr/local/apache2/logs/ && chown ${UID}:${UID} /usr/local/apache2/logs/
USER ${UID}
EXPOSE 8000