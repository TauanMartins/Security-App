# Build stage: Composer
FROM composer:2.8.9 AS composer
WORKDIR /application

# Copiar apenas os arquivos necessários para instalar dependências
COPY system /application

# Instalar dependências PHP via Composer
RUN composer install --no-scripts --optimize-autoloader --no-interaction

# Final container: PHP-FPM com Xdebug
FROM php:8.4.6-fpm-alpine3.21

# Instalar dependências do sistema e extensões PHP
RUN apk add --no-cache \
    linux-headers \
    vim \
    libpq postgresql-dev \
    autoconf g++ make \
    && docker-php-ext-install sockets pdo_pgsql \
    && pecl install xdebug \
    && docker-php-ext-enable xdebug \
    && apk del postgresql-dev autoconf g++ make \
    && rm -rf /var/cache/apk/

# Copiar arquivos de configuração personalizados da aplicação PHP
COPY --from=composer /application /app
COPY .devops/dev/php/inis/php.ini /usr/local/etc/php/php.ini
COPY .devops/dev/php/inis/www.conf /usr/local/etc/php-fpm.d/www.conf
COPY .devops/dev/php/inis/xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini

# Remover configuração padrão do PHP-FPM para evitar conflitos
RUN rm /usr/local/etc/php-fpm.d/zz-docker.conf

# Criar usuário e grupo específicos
RUN addgroup -g 18000 -S user \
    && adduser -u 8000 -S -G user user  

# Ajustar permissões para o diretório de armazenamento
RUN chown -R user:user /app/storage \
    && chmod -R 775 /app/storage

# Ajustar permissões para o diretório de cache do bootstrap
RUN chown -R user:user /app/bootstrap/cache

# Criar e ajustar permissões para o diretório e socket de segurança
RUN mkdir /socket \
    && chown -R user:user /socket \
    && chmod 0770 /socket

# Criar e ajustar permissões para o log do Xdebug
RUN touch /var/log/xdebug.log \
    && chmod 666 /var/log/xdebug.log

# Configurar diretório de trabalho e usuário

USER user
WORKDIR /app

CMD ["php-fpm"]
