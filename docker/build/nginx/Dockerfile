FROM nginx

MAINTAINER Alfredo <stuzzo> AIELLO <stuzzo@gmail.com>

RUN apt-get update && apt-get install -y \
    nano \
    openssl

# Set timezone
RUN rm /etc/localtime && ln -s /usr/share/zoneinfo/Europe/Rome /etc/localtime && echo Europe/Rome > /etc/timezone

RUN rm /etc/nginx/nginx.conf
ADD nginx.conf /etc/nginx/

# Generate self-signed certificate
RUN mkdir /etc/nginx/ssl
RUN openssl dhparam -out /etc/nginx/ssl/dh.pem 2048
RUN openssl req -x509 -newkey rsa:4086 -days 3650 -nodes -sha256 -keyout /etc/nginx/ssl/server.key -out /etc/nginx/ssl/server.crt -subj "/C=IT/ST=Pescara/L=Pescara/O=Purple Network/OU=IT deparment/CN=${PROJECT_NAME}.local"

ARG PROJECT_NAME
ARG PROJECT_PROTOCOL

ADD symfony_${PROJECT_PROTOCOL}.conf /etc/nginx/sites-available/
RUN mkdir /etc/nginx/sites-enabled
RUN sed -i "s/PROJECT_NAME/${PROJECT_NAME}/g" /etc/nginx/sites-available/symfony_${PROJECT_PROTOCOL}.conf
RUN mv /etc/nginx/sites-available/symfony_${PROJECT_PROTOCOL}.conf /etc/nginx/sites-available/${PROJECT_NAME}.conf
RUN ln -sf /etc/nginx/sites-available/${PROJECT_NAME}.conf /etc/nginx/sites-enabled/${PROJECT_NAME}.conf
RUN echo "upstream php-upstream { server ${PROJECT_NAME}_php:9000; }" > /etc/nginx/conf.d/upstream.conf

# Fix to avoid timeout on debug session
RUN echo "fastcgi_read_timeout 6000;" >> /etc/nginx/fastcgi.conf

RUN usermod -u 1000 www-data

RUN echo 'alias ll="ls -alF"' >> ~/.bashrc
RUN echo 'alias la="ls -A"' >> ~/.bashrc
RUN echo 'alias l="ls -CF"' >> ~/.bashrc

CMD ["nginx"]

EXPOSE 80
EXPOSE 443