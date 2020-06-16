---
title: Integration Test
linktitle: Integration Test
toc: true
type: docs
date: "2020-06-16T00:00:00+01:00"
draft: false
menu:
  master:
    parent: Contents
    weight: 90
---

**How to deploy ohara integration test to QA environment?**


## Node 裡需要安裝的工具

- 安裝 JDK 11, 需要設定以下的 link

    ```console
    $ sudo yum install -y java-11-openjdk-devel
    ```

- 在 CentOS 的 Node 需要安裝 jq

    ```console
    $ sudo yum install -y epel-release
    $ sudo yum install -y jq
    ```
  
- ssh server

    ```console
    $ sudo yum install -y openssh-server
    ```

- Docker: Please follow [docker official tutorial](https://docs.docker.com/install/linux/docker-ce/centos/)

- 防火牆設定允許 docker container 的 port, 並且重新 reload 防火牆的服務

    ```console
    # sudo firewall-cmd --permanent --zone=trusted --add-interface={docker network}
    # sudo firewall-cmd --reload
    ```

## Jenkins 需要做的設定

1. 確認 jenkins 是否加入了登入的帳號密碼設定

    Credentials -> global -> Add Credentials -> 輸入 Username, Password, Description. ID 不用輸入 -> OK

1. 把 Node 加入到 Jenkins 裡

    管理 Jenkins -> 管理節點 -> 新增節點 -> 輸入節點名稱的 hostname -> 選複製既有節點 -> 複製來源選一台現有的 slave 來輸入, 例如：ohara-it01 -> OK

1. 把 Node 加入到 ssh remote hosts

    管理 Jenkins -> 設定系統 -> SSH remote hosts -> 往下拉會看到新增按鈕 -> 之後輸入 Hostname, port, 選 Credentials -> 新增

1. 設定 PreTest 和 PreCommit Job
    * 修改標籤表示式, 避免 IT 的 node 跑到 UT 上面
    * 增加 node 在 shell script 裡
        ```shell                
        NODE01_HOSTNAME="ohara-it02"
        NODE01_IP=$(getent hosts $NODE01_HOSTNAME | cut -d" " -f 1)
        NODE01_INFO="$NODE_USER_NAME:$NODE_PASSWORD@$NODE01_HOSTNAME:22"
        EXTRA_PROPERTIES="-Pohara.it.docker=$NODE00_INFO,$NODE01_INFO"
        ```
    * 建立 Execute shell script on remote host using ssh, 用來在 IT 的 Node 拉 docker image
        新增建置步驟 -> Execute shell script on remote host using ssh -> 輸入拉 docker image 的 command
