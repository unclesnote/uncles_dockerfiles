FROM ubuntu:22.04

ARG ARG_ID=myuser
ARG ARG_PASSWD=32143214

# =============================================================
#                       Essential
# =============================================================
# "DEBIAN_FRONTEND=noninteractive" is to pass timezone and area selection for first apt installation
# after container initialization is done, need to run "dpkg-reconfigure tzdata" to configure timezone to avoid time issues on app
ENV DEBIAN_FRONTEND=noninteractive

# environment
ENV TZ="Asia/Seoul"

# basic
RUN apt update && apt install -y vim git wget rsyslog sudo build-essential

# ssh
RUN apt install -y net-tools openssh-server
RUN sed -i 's/#PermitRootLogin prohibit-password/PermitRootLogin yes/' /etc/ssh/sshd_config
RUN echo "root:$ARG_PASSWD" |  chpasswd

EXPOSE 22

# code
RUN apt update && apt install software-properties-common apt-transport-https wget -y
RUN wget -q https://packages.microsoft.com/keys/microsoft.asc -O- |  apt-key add -
RUN add-apt-repository "deb [arch=amd64] https://packages.microsoft.com/repos/vscode stable main"
RUN apt update && apt -y install code
RUN code --user-data-dir=/root/vscode --install-extension ms-vscode-remote.remote-ssh

# =============================================================
#                       mysql
# =============================================================
RUN apt update && apt install expect mysql-server -y

EXPOSE 3306

# =============================================================
#                       entry
# =============================================================
WORKDIR /root
COPY readme.txt /root/
CMD service rsyslog start; service ssh start; service mysql start;bash

