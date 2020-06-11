FROM centos:8

RUN yum install -y wget gcc git rsync
RUN wget https://github.com/gohugoio/hugo/releases/download/v0.72.0/hugo_extended_0.72.0_Linux-64bit.tar.gz

RUN tar zxvf hugo_extended_0.72.0_Linux-64bit.tar.gz
RUN cp hugo /usr/local/bin

ARG USER=ohara
RUN groupadd $USER
RUN useradd -ms /bin/bash -g $USER $USER

USER $USER
