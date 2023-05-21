FROM jenkins/jenkins:2.249.3-slim

USER root

RUN cp /etc/apt/sources.list /etc/apt/sources.list.bak
RUN echo " " > /etc/apt/sources.list

RUN echo "deb http://ftp.cn.debian.org/debian/ stable main contrib non-free" >> /etc/apt/sources.list
RUN echo "deb-src http://ftp.cn.debian.org/debian/ stable main contrib non-free" >> /etc/apt/sources.list
RUN echo "deb http://ftp.cn.debian.org/debian/ stable-updates main contrib non-free" >> /etc/apt/sources.list
RUN echo "deb-src http://ftp.cn.debian.org/debian/ stable-updates main contrib non-free" >> /etc/apt/sources.list
RUN echo "deb http://security.debian.org/ stable/updates main" >> /etc/apt/sources.list
RUN echo "deb-src http://security.debian.org/ stable/updates main" >> /etc/apt/sources.list
RUN echo "deb http://ftp.debian.org/debian buster-backports main" >> /etc/apt/sources.list
RUN echo "deb-src http://ftp.debian.org/debian buster-backports main" >> /etc/apt/sources.list

RUN apt-get update && apt-get install -y apt-transport-https \
       ca-certificates curl gnupg2 \
       software-properties-common
RUN curl -fsSL http://mirrors.aliyun.com/docker-ce/linux/debian/gpg | apt-key add -
RUN apt-key fingerprint 0EBFCD88
RUN add-apt-repository \
       "deb [arch=amd64] http://mirrors.aliyun.com/docker-ce/linux/debian \
       $(lsb_release -cs) stable"
RUN apt-get update && apt-get install -y docker-ce-cli
USER jenkins
RUN jenkins-plugin-cli --plugins blueocean:1.24.3