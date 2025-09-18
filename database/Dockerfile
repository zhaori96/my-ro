FROM alpine/git:latest AS files

WORKDIR /rathena

RUN git clone --filter=blob:none --no-checkout https://github.com/rathena/rathena.git .
RUN git sparse-checkout init --cone
RUN git sparse-checkout set sql-files
RUN git checkout master

FROM mysql:5.7

COPY mysql-custom.cnf /etc/mysql/conf.d/mysql-custom.cnf

COPY ./sql/ /docker-entrypoint-initdb.d/
COPY --from=files /rathena/sql-files/*.sql /docker-entrypoint-initdb.d/