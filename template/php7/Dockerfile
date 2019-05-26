FROM entanet/php72-kafka


# Composer requirements
ARG COMPOSER_AUTH='{}'
ENV COMPOSER_AUTH=${COMPOSER_AUTH}

# Add watchdog and packages
RUN echo "Pulling watchdog binary from Github." && \
    curl -sSL https://github.com/openfaas/faas/releases/download/0.13.0/fwatchdog > /usr/bin/fwatchdog && \
    chmod +x /usr/bin/fwatchdog

# create non-root user
RUN addgroup --system app && adduser --system --group app && \
    mkdir -p /home/app

# Import function
WORKDIR /home/app
COPY index.php ./
COPY ./function ./function
WORKDIR /home/app/function

# Install php extensions
RUN  bash ./php-extension.sh && \
    chown -R app /home/app

# Entry
USER app

# Install Composer Dependecies
RUN composer install --no-dev

USER root

# Cleanup
RUN rm -rf /usr/src/php && \
    { find /usr/local/lib -type f -print0 | xargs -0r strip --strip-all -p 2>/dev/null || true; }

USER app

WORKDIR /home/app
ENV fprocess="php index.php"
HEALTHCHECK --interval=3s CMD [ -e /tmp/.lock ] || exit 1
CMD ["fwatchdog"]
