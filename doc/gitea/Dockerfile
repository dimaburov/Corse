# образ основан на golang 
FROM golang:1.16-alpine3.13 AS build-env
#аргументы приложения
ARG GOPROXY

#переменная окружения
ENV GOPROXY ${GOPROXY:-direct}

ARG GITEA_VERSION
ARG TAGS="sqlite sqlite_unlock_notify"
ENV TAGS "bindata timetzdata $TAGS"
ARG CGO_EXTRA_CFLAGS

#установка основных пакетов
RUN apk --no-cache add build-base git nodejs npm

#Настройка репозитория
COPY . ${GOPATH}/src/code.gitea.io/gitea
#Изменяем текущий рабочий каталог
WORKDIR ${GOPATH}/src/code.gitea.io/gitea

#Проверка версии
RUN if [ -n "${GITEA_VERSION}" ]; then git checkout "${GITEA_VERSION}"; fi \
 && make clean-all build

# Начало сборки
RUN go build contrib/environment-to-ini/environment-to-ini.go
# используется образ alpine
FROM alpine:3.13
# тот кто создал и поддерживает данные
LABEL maintainer="maintainers@gitea.io"
# Указывает что нужно открыть 22 и 3000 порт
EXPOSE 22 3000
# Устанавливаем пакеты
RUN apk --no-cache add \
    bash \
    ca-certificates \
    curl \
    gettext \
    git \
    linux-pam \
    openssh \
    s6 \
    sqlite \
    su-exec \
    gnupg
# создаём группу индификатор группы 1000
# создаём пользователя
RUN addgroup \
    -S -g 1000 \
    git && \
  adduser \
    -S -H -D \
    -h /data/git \
    -s /bin/bash \
    -u 1000 \
    -G git \
    git && \
  echo "git:*" | chpasswd -e
#переменные окружения
ENV USER git
ENV GITEA_CUSTOM /data/gitea
# место хранения данных
VOLUME ["/data"]
#даёт команду с аргументами для вызова во время работы контейнера
ENTRYPOINT ["/usr/bin/entrypoint"]
#команда с оругементами, которая будет выполнена, когда запустится контейнер
CMD ["/bin/s6-svscan", "/etc/s6"]
#копирование локальных файлов из билд-контектекста
COPY docker/root /
COPY --from=build-env /go/src/code.gitea.io/gitea/gitea /app/gitea/gitea
COPY --from=build-env /go/src/code.gitea.io/gitea/environment-to-ini /usr/local/bin/environment-to-ini
#Создание символической ссылки
RUN ln -s /app/gitea/gitea /usr/local/bin/gitea

