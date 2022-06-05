FROM alpine:3.14

COPY ./scripts/commands/* /etc/scripts/
COPY ./scripts/daily/* /etc/periodic/daily/

RUN apk update && \
    apk upgrade && \
    apk add --no-cache postgresql-client rsync restic bash && \
    chmod a+x /etc/periodic/daily/* && \
    chmod a+x /etc/scripts/*
