# Base Image
FROM nginx:stable-alpine3.20-slim

# Copiar arquivos da aplicação e configurações do Nginx
COPY system /app
COPY .devops/dev/nginx/inis/default.conf /etc/nginx/conf.d/default.conf
COPY .devops/dev/nginx/inis/nginx.conf /etc/nginx/nginx.conf
RUN rm docker-entrypoint.d/10-listen-on-ipv6-by-default.sh

# Configurar usuário e grupo para execução
RUN addgroup -g 18000 -S user \
    && adduser -u 8000 -S -G user user

# Ajustar permissões para o diretório da aplicação
RUN chown -R user:user /app

# Criar e ajustar permissões para o arquivo de PID do Nginx
RUN touch /var/run/nginx.pid \
    && chown user:user /var/run/nginx.pid

# Ajustar permissões para os diretórios de cache e log do Nginx
RUN chown -R user:user /var/cache/nginx /var/log/nginx

# Criar diretórios e ajustar permissões para os arquivos de socket
RUN mkdir /socket \
    && chown -R user:user /socket \
    && chmod 0770 /socket

# Definir o usuário padrão e diretório de trabalho
USER user
WORKDIR /app

# Comando inicial para rodar o Nginx
CMD ["nginx", "-g", "daemon off;"]
