#образ создан на основе alpine
FROM alpine:3.14.0
#места хранения данных
VOLUME /var/www/app/data
VOLUME /var/www/app/plugins
VOLUME /etc/nginx/ssl
#необходимо открыть порты 80 и 443
EXPOSE 80 443
#переменная для передачи в обаз во время сборки
ARG VERSION
#устанавливаем все необходимые пакеты и удаляем localhost вместе с конфигурационным файлом
RUN apk --no-cache --update add \
    tzdata openssl unzip nginx bash ca-certificates s6 curl ssmtp mailx php7 php7-phar php7-curl \
    php7-fpm php7-json php7-zlib php7-xml php7-dom php7-ctype php7-opcache php7-zip php7-iconv \
    php7-pdo php7-pdo_mysql php7-pdo_sqlite php7-pdo_pgsql php7-mbstring php7-session php7-bcmath \
    php7-gd php7-mcrypt php7-openssl php7-sockets php7-posix php7-ldap php7-simplexml && \
    rm -rf /var/www/localhost && \
    rm -f /etc/php7/php-fpm.d/www.conf
#копируем файлы в контейнер
ADD . /var/www/app
ADD docker/ /
# удаляем файл docker и сохраняем нашу версию
RUN rm -rf /var/www/app/docker && echo $VERSION > /version.txt
#команда вызываемая во время выполнения контейнера
ENTRYPOINT ["/usr/local/bin/entrypoint.sh"]
#команда которую нужно выполнить когда приложения запуститься
CMD []

