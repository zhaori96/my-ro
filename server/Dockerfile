# Estágio 1: Builder - Compila o rAthena
FROM debian:10 as builder

# Instale as dependências de compilação
RUN echo 'deb http://archive.debian.org/debian buster main contrib non-free' > /etc/apt/sources.list && \
    echo 'deb http://archive.debian.org/debian-security buster/updates main contrib non-free' >> /etc/apt/sources.list && \
    echo 'Acquire::Check-Valid-Until "false";' > /etc/apt/apt.conf.d/99no-check-valid-until && \
    apt-get update && \
    apt-get install -y \
         git \
         make \
         libmariadb-dev \
         libmariadbclient-dev \
         libmariadbclient-dev-compat \
         gcc \
         g++ \
         zlib1g-dev \
         libpcre3-dev \
     && rm -rf /var/lib/apt/lists/*

# Clone o repositório do rAthena
WORKDIR /rathena
RUN git clone https://github.com/rathena/rathena.git .

# Compile o servidor
RUN ./configure --enable-packetver=20230331 --enable-epoll
RUN make clean
RUN make server
# 20230327
# ---

# Estágio 2: Final - Imagem de produção leve
FROM debian:10-slim

RUN echo 'deb http://archive.debian.org/debian buster main contrib non-free' > /etc/apt/sources.list && \
    echo 'deb http://archive.debian.org/debian-security buster/updates main contrib non-free' >> /etc/apt/sources.list && \
    echo 'Acquire::Check-Valid-Until "false";' > /etc/apt/apt.conf.d/99no-check-valid-until

# Instale APENAS as dependências de execução
RUN apt-get update && \
    apt-get install -y \
        libmariadb3 \
        zlib1g \
        libpcre3 \
        procps \
        vim \
        git \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /rathena

# Copie os binários compilados do estágio builder
COPY --from=builder /rathena .

# Configurações
COPY ./configuration/imports conf/import
COPY ./configuration/messages/map_msg.conf conf/msg_conf
COPY ./configuration/messages/login_msg.conf conf/msg_conf
COPY ./configuration/messages/import conf/msg_conf/import

# Banco de dados
COPY ./database/ db/import

# NPC
COPY ./npc/scripts_custom.conf npc
COPY ./npc/shops.txt npc/custom

# Defina o comando de inicialização
CMD ["./athena-start", "watch"]
