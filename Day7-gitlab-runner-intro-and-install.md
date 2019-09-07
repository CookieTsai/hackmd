---
title: Day 7 - GitLab Runner 簡介與安裝
tags: 鐵人賽
description: |
  整理一年來的實務經驗並在三十天的自我挑戰中，找出 GitLab 與 GCP 眾多服務的一個最佳實踐方式
---

# Best Practice for DevOps on GitLab and GCP : GitLab Runner 簡介與安裝 - Day 7 -

![GitLab CI/CD Architecture](https://i.imgur.com/w7otxUB.png)

## 簡介

GitLab Server 和 GitLab Runner 是 GitLab CI/CD 中不可或缺的兩者。在公有的 [GitLab](https://about.gitlab.com/) 由於有 Shared Runner，所以無需特地設定，就可以開始嘗試 GitLab CI/CD，但在免費版本有兩千分鐘的使用限制，不免讓人覺得用起來綁手綁腳。但所幸想要解除這樣的限制並不難，我們可以通過兩個步驟，來準備私有的 GitLab Runner 這樣就可以沒有限制的使用 CI/CD 了。

## 如何建置 GitLab Runner

想要 GitLab CI/CD 能完整正常運作，建議建置步驟如下：

1. 準備/安裝 一個 GitLab Server
2. 安裝一個與 GitLab Server 對應版本的 GitLab Ruuner
3. 安裝 v1.13.0 以上的 Docker `(Option)`
4. 在安裝 GitLab Runner 的設備上設定 [Executor](https://docs.gitlab.com/runner/executors/)

```
備註: 
  1. 第三步驟是在使用到 Docker 時必須安裝的工具
  2. 使用 Kubernetes Executor 則建置方式完全不同
```

## 什麼是 Executor

如果把 GitLab Runner 當作是一個工廠，那 Executor 就是一個個的產線，如同一個工廠可以擁有多個不同種類的產線，Runner 與 Executor 之間的關係亦是如此，這些產線會根據專案中 `.gitlab-ci.yml` 的內容，決定產線如何產出開發者們期望的產品。

另外，值得注意的一件事是 Executor 的種類非常多，至少有六種以上，在這麼多的種類之中，作者依照過去的使用經驗，推薦可以學習 Shell, Docker, Kubernetes 這三種 Executor 應該就足以面對使用者的各種需求，其中 Docker Executor 是作者最為推薦，除了容易上手外各式功能也是最為完善的一種。

## 使用公有 GitLab Server 建置自己的 GitLab Runner

1. 首先，先到 [GitLab](https://gitlab.com/) 找專案的 `URL` 和 `Token`，如圖所示：

![gitlab url and token](https://i.imgur.com/ofUM2dT.png)

2. 本機安裝 Docker 並啟動服務，請參考官方文件
    * [Docker-for-windows](https://docs.docker.com/docker-for-windows/install/)
    * [Docker-for-mac](https://docs.docker.com/docker-for-mac/install/)
    * [Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/)
    * [CentOS](https://docs.docker.com/install/linux/docker-ce/centos/)

3. 執行以下命令 (以 Mac 為例，docker.sock 及目錄需根據作業系統做調整)

```shell=
$ docker run -d --name my-gitlab-runner --restart always \
   -v ~/Shared/gitlab-runner/config:/etc/gitlab-runner \
   -v /var/run/docker.sock:/var/run/docker.sock \
   gitlab/gitlab-runner:latest
```

4. Docker Runner 註冊 Executor

```shell=
$ docker exec -it my-gitlab-runner gitlab-runner register

Runtime platform                                    arch=amd64 os=linux pid=57 revision=f100a208 version=11.6.0
Running in system-mode.

Please enter the gitlab-ci coordinator URL (e.g. https://gitlab.com/):
https://gitlab.com/

Please enter the gitlab-ci token for this runner
xxx

Please enter the gitlab-ci description for this runner
[hostname] my-runner

Please enter the gitlab-ci tags for this runner (comma separated):
tag1,tag2

Please enter the executor: ssh, docker+machine, docker-ssh+machine, kubernetes, docker, parallels, virtualbox, docker-ssh, shell:
docker

Please enter the Docker image (eg. ruby:2.1):
docker:stable
```

5. 成功後可以在取得 URL 和 Token 的介面上看到註冊的 Runner

## 結語

簡單介紹了 GitLab Runner 並在單機上進行安裝與設定，如此一來就可以獲得無限時數的 GitLab CI/CD 功能。

在這樣的架構下，GitLab Server 可以有數個 GitLab Runner 和數個 Executor 分散在不同的機器設備中，在執行時如果沒有通過 Tags 來指定 Executor，則不同的 Job 將會隨機選擇其一來執行任務。反之則可以指定特定的目標來執行任務。

這很大的影響 CI/CD 中兩種暫存的方法 Artifects 和 Cache 上的使用與維護，在後續的文章中，會有專門為這些暫存的方法做更細部的介紹。

## Reference

* [GitLab.org / gitlab-runner · GitLab](https://gitlab.com/gitlab-org/gitlab-runner)
* [GitLab Runner Docs | GitLab](https://docs.gitlab.com/runner/)
* [Executors | GitLab](https://docs.gitlab.com/runner/executors/)