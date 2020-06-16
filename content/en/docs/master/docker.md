---
title: Docker and Docker-compose
linktitle: Docker and Docker-compose
toc: true
type: docs
date: "2020-06-16T00:00:00+01:00"
draft: false
menu:
  master:
    parent: Contents
    weight: 200
---

## Why we need docker-compose

Ohara is good at connecting to various systems to collect, transform, aggregate (and other operations you can imagine) data. In order to test Ohara, we need a way to run a bunch of systems simultaneously. We can build a heavy infra to iron out this problem. Or we can leverage docker-compose to host various systems "locally" (yes, you need a powerful machine to use Ohara's docker-compose file).

## Prerequisites

-   Centos 7.6+ (supported by official community. However, other
    GNU/Linux should work well also)
-   Docker 18.09+
-   Docker-compose 1.23.2+


## How to install

### Install docker

> This section is a clone of https://docs.docker.com/install/linux/docker-ce/centos/

**Uninstall old versions**

```console
$ sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```
    
**Install required packages**

```console
$ sudo yum install -y yum-utils \
    device-mapper-persistent-data \
    lvm2
```

**Install using the repository**

```console
$ sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```

**Install docker-ce**

```console
$ sudo yum install docker-ce
```

### Install Docker-compose

```console
$ wget https://github.com/docker/compose/releases/download/1.23.2/docker-compose-Linux-x86_64 -O docker-compose
$ sudo chmod +x ./docker-compose
```

{{% alert hint %}}
see <https://github.com/docker/compose/releases> for more details
{{% /alert %}}

## How to ...

### Start services by docker-compose file

Before start services, you must set postgresql connection info for
environment variable, example:

```bash
export POSTGRES_DB=postgres
export POSTGRES_USER=username
export POSTGRES_PASSWORD=password
```

Start services command

```console
$ ./docker-compose -f {docker-compose file} up
```

### Stop services

```console
$ ctrl+c
```

We are talking about tests, right? We don't care about how to shut down services gracefully

### Clean up all containers

```console
$ docker rm -f $(docker ps -q -a)
```

We are talking about tests, right? You should have a machine for testing
only so it is ok to remove all containers quickly. That does simplify
your work and life.

### Enable IPv4 IP Forwarding

```console
$ sudo vi /usr/lib/sysctl.d/00-system.conf
```

Add the following line:

```text
net.ipv4.ip_forward=1
```

Save and exit the file. Restart network:

```console
$ sudo systemctl restart network
```
