---
title: Day 24 - DevOps Lab 專案環境建置 - GitLab Runner篇
tags: 鐵人賽
description: |
  整理一年來的實務經驗並在三十天的自我挑戰中，找出 GitLab 與 GCP 眾多服務的一個最佳實踐方式
---

# Best Practice for DevOps on GitLab and GCP : DevOps Lab 專案環境建置 - GitLab Runner篇 - Day 24 -

![Imgur](https://i.imgur.com/riS7LgF.jpg)

## 前言

這次要來真的建置私有的 GitLab Runner 啦 ~ 

參考第 7、14 天所寫的 [GitLab Runner 簡介與安裝](https://ithelp.ithome.com.tw/articles/10214266)和[GCP Google Cloud SDK 快速入手模式 (上) ](https://ithelp.ithome.com.tw/articles/10215467)，這次一樣要選用 ubuntu 16.04 以及 Docker 的安裝模式，話不多說直接進入主題吧！

## 查詢 Image 列表

> 使用指令 `gcloud compute images list` 並篩選關鍵字 ubuntu 這時可以看到，第一個欄位是 `Name`，第二個欄位是 `Project`，這兩個是建立計算實例需要的參數要記住。

```bash
$ gcloud compute images list | grep ubuntu
```

```bash
ubuntu-1404-trusty-v20190514                          ubuntu-os-cloud    ubuntu-1404-lts                               READY
ubuntu-1604-xenial-v20190913                          ubuntu-os-cloud    ubuntu-1604-lts                               READY
ubuntu-1804-bionic-v20190918                          ubuntu-os-cloud    ubuntu-1804-lts                               READY
ubuntu-1904-disco-v20190918                           ubuntu-os-cloud    ubuntu-1904                                   READY
...
```

## 建立 Runner 計算實例

> 建立計算實例並指定使用 ubuntu 16.04 版本

```bash
$ gcloud compute instances create runner \
--image=ubuntu-1604-xenial-v20190913 --image-project=ubuntu-os-cloud
```

```bash
Created [https://www.googleapis.com/compute/v1/projects/my-devops-lab/zones/asia-east1-a/instances/runner].
NAME    ZONE          MACHINE_TYPE   PREEMPTIBLE  INTERNAL_IP  EXTERNAL_IP     STATUS
runner  asia-east1-a  n1-standard-1               10.140.0.6   35.201.157.228  RUNNING
```

## 連線進入 Runner

> 第一次進行遠端連線會被詢問是否建立 SSH keys 選擇 `Y`

```bash
$ gcloud compute ssh cookie@runner
```

```bash
WARNING: The public SSH key file for gcloud does not exist.
WARNING: The private SSH key file for gcloud does not exist.
WARNING: You do not have an SSH key for gcloud.
WARNING: SSH keygen will be executed to generate a key.
This tool needs to create the directory [/home/mcacookie/.ssh] before
being able to generate SSH keys.

Do you want to continue (Y/n)?  y

Generating public/private rsa key pair.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/mcacookie/.ssh/google_compute_engine.
Your public key has been saved in /home/mcacookie/.ssh/google_compute_engine.pub.
The key fingerprint is:
SHA256:yqzo8NyLmbdpghfERUEgK8QGhsP/csFUyGSzkaNY7iw mcacookie@cs-6000-devshell-vm-3f850de5-31a4-4d72-96c9-b7445c070002
The key's randomart image is:
+---[RSA 2048]----+
|Bo.+**+.         |
|=* .o*+          |
|+o=.+..          |
|..o+ o           |
| .o . . S        |
| E.+ = .         |
|....o +          |
|.+o*oo           |
| oO+*o           |
+----[SHA256]-----+
Updating project ssh metadata...⠶Updated [https://www.googleapis.com/compute/v1/projects/my-devops-lab].
Updating project ssh metadata...done.
Waiting for SSH key to propagate.
Warning: Permanently added 'compute.8202143156685772178' (ECDSA) to the list of known hosts.
Linux dev 4.9.0-11-amd64 #1 SMP Debian 4.9.189-3 (2019-09-02) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.

cookie@runner:~$
```

## 安裝 Docker CE

```bash
$ curl -sSL https://get.docker.com/ | sh
```

> 將當前使用者新增入 docker 的群組 

```bash
$ sudo usermod -aG docker {USER_NAME}
```

`提示：設定完成後要在一次重新連入 runner 才能正常使用 Docker 相關指令`

## 安裝 Docker Compose

根據 GitLab 相關文件 Docker in Docker 功能會運用到 Docker Compose 所以也要安裝，後續會利用 Docker in Docker 的模式進行 Dockerfile 的建置

> 下載 Docker Compose 並放置於 `/usr/local/bin/docker-compose`

```bash
$ sudo curl -L "https://github.com/docker/compose/releases/download/1.24.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

> 變更下載的 docker-compose 為可執行檔，如此就安裝好咯 !!

```bash
$ sudo chmod +x /usr/local/bin/docker-compose
```

## 安裝 GitLab Runner

> 要選擇與作業系統相對應的版本下載，通過 `uname -a` 可以得知，我們應該選擇第一個 64 位元的版本下載：

```bash
# Linux x86-64
$ sudo curl -L --output /usr/local/bin/gitlab-runner https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-linux-amd64

# Linux x86
$ sudo curl -L --output /usr/local/bin/gitlab-runner https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-linux-386

# Linux arm
$ sudo curl -L --output /usr/local/bin/gitlab-runner https://gitlab-runner-downloads.s3.amazonaws.com/latest/binaries/gitlab-runner-linux-arm
```

> 變更 `/usr/local/bin/gitlab-runner` 為可執行檔

```bash
$ sudo chmod +x /usr/local/bin/gitlab-runner
```

> 建立名為 GitLab Runner 的使用者

```bash
$ sudo useradd --comment 'GitLab Runner' --create-home gitlab-runner --shell /bin/bash
```

> 安裝 gitlab-runner 並啟動服務

```bash
$ sudo gitlab-runner install --user=gitlab-runner --working-directory=/home/gitlab-runner
$ sudo gitlab-runner start
```


## 使用 GitLab Runner 進行註冊


> 首先，先到 [GitLab](https://gitlab.com/) 找專案的 `URL` 和 `Token`，如圖所示：

![gitlab url and token](https://i.imgur.com/ofUM2dT.png)

> 使用 GitLab Runner 進行註冊。(提示：REGISTRATION_TOKEN 要輸入專案的 Token )

```bash
$ sudo gitlab-runner register -n \
  --executor docker \
  --description "my-devops-runner" \
  --tag-list "devops-runner" \
  --run-untagged="true" \
  --docker-image "docker:stable" \
  --docker-privileged \
  --url https://gitlab.com/ \
  --registration-token {REGISTRATION_TOKEN}
```

> 查看設定檔

```bash
$ sudo cat /etc/gitlab-runner/config.toml
```

```bash
concurrent = 1
check_interval = 0

[session_server]
  session_timeout = 1800

[[runners]]
  name = "my-devops-runner"
  url = "https://gitlab.com/"
  token = "ZaNRQsRFtk3vmhr5yFYK"
  executor = "docker"
  [runners.custom_build_dir]
  [runners.docker]
    tls_verify = false
    image = "docker:stable"
    privileged = true
    disable_entrypoint_overwrite = false
    oom_kill_disable = false
    disable_cache = false
    volumes = ["/cache"]
    shm_size = 0
  [runners.cache]
    [runners.cache.s3]
    [runners.cache.gcs]
```

* `concurrent` 決定當前的 Runner 可以同時有幾個 Executor 一起運行
* `privileged` 要是 true 才可以使用 Docker in Docker 的功能

> 成功註冊後可以在取得 Token 的頁面看到註冊成功的 Runner

![Imgur](https://i.imgur.com/bNWQB7k.png)

 ## 參考
 
 * [Install GitLab Runner manually on GNU/Linux](https://docs.gitlab.com/runner/install/linux-manually.html)
 * [Use docker-in-docker with privileged mode](https://docs.gitlab.com/runner/executors/docker.html#use-docker-in-docker-with-privileged-mode)
 * [Use docker-in-docker workflow with Docker executor](https://docs.gitlab.com/ee/ci/docker/using_docker_build.html#use-docker-in-docker-workflow-with-docker-executor)
 * [Install Docker Compose](https://docs.docker.com/compose/install/)