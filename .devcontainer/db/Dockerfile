# FROM --platform=linux/x86_64 mysql:8.0.30
FROM --platform=linux/x86_64 mariadb:latest

COPY ./my.cnf /etc/mysql/conf.d/my.cnf

RUN chown 644  /etc/mysql/conf.d/my.cnf
