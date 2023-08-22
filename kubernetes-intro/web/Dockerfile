FROM ubuntu
WORKDIR /app
ARG UID=1001
RUN apt update \
    && apt install -y apache2 \
    && chown -R ${UID} /etc/apache2 /var/www /app \
    && rm -rf /var/lib/apt/lists/*
USER ${UID}
EXPOSE 8000