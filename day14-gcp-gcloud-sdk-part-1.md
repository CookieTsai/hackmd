---
title: Day 14 - GCP Google Cloud SDK 快速入手模式 (上)
tags: 鐵人賽
description: |
  整理一年來的實務經驗並在三十天的自我挑戰中，找出 GitLab 與 GCP 眾多服務的一個最佳實踐方式
---

# Best Practice for DevOps on GitLab and GCP : GCP Google Cloud SDK 快速入手模式 (上) - Day 14 -

![Imgur](https://i.imgur.com/jePDgXz.jpg)

## 前言

先快速說明什麼是 Google Cloud SDK 呢？簡單來說是一個軟體，可以通過指令來控制在 GCP 上的各種服務。既然是一種軟體那就有安裝問題，沒錯各種平台各種裝法，外加文件到底在哪也不是這麼好找，更可怕的事情是用到後期，還可能要安裝其他工具才能完整體驗功能。最後，作者覺得最可怕的還是想要重複測試使用，很怕把各種東西給玩壞！畢竟在沒有足夠經驗前根本不知道會發生甚麼事情，說道這或許有人已經能猜到，沒錯今天要使用的快速模式是搭配 Google 提供的 Docker Image。先生/小姐看到這別急著走，雖然 Docker 使用起來很簡單但還是有使用上的優化空間，走過路過別錯過，話不多說開始準備環境。

## 安裝 Docker CE

Ubuntu 16.04 LTS 指令安裝為例

```bash
# 如果有舊版本請先服用此行指令移除
$ sudo apt-get remove docker docker-engine docker.io containerd runc

# 取得最新 apt-get 內容
$ sudo apt-get update
# 安裝 Docker CE 相依套件
$ sudo apt-get install \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg-agent \
    software-properties-common

# 新增 Repo 需要的金耀
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
# 新增 stable 的 Repo
$ sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
  
# 取得最新 apt-get 內容
$ sudo apt-get update
# 安裝 Docker CE 相關工具
$ sudo apt-get install docker-ce docker-ce-cli containerd.io
```

## 運行並進入 Google Cloud SDK Container

```bash
# 啟動一次終身使用版指令
$ docker run -dt --name gsdk --hostname gsdk --restart always google/cloud-sdk

# 進入 Google Cloud SDK Container
$ docker exec -it gsdk bash
```

如果有權限錯誤請參考文章後面的錯誤處理，成功後將獲得一個安裝有 `gcloud`, `kubectl` 的一個環境，而且會是當前最新版，如果真的版本不行也隨時可以切換，真的是非常方便。但以為到這就結束了嗎？不！

在這情況下雖也可以使用但是沒能使用 Tab 鍵幫助自動完成，非常的不方便不好用。因此，在容器內執行以下指令

```bash
# 安裝一些常用的工具
$ apt update && apt install -y bash-completion vim wget jq
# 將
$ sed -i -E 's/^# (alias|export)/\1/' ~/.bashrc

$ cat << EOF >> ~/.bashrc
[ -f /usr/lib/google-cloud-sdk/completion.bash.inc ] && source /usr/lib/google-cloud-sdk/completion.bash.inc
[ -f /usr/share/bash-completion/bash_completion ] && source /usr/share/bash-completion/bash_completion

source <(kubectl completion bash)
EOF

$ source ~/.bashrc
```

然後重新再一次進入容器內

```bash
# 進入 Google Cloud SDK Container
$ docker exec -it gsdk bash
```

這時的你使用久了後會發現所有工具一應俱全，與在網頁板使用 GCP Cloud Shell 幾乎是完全相同沒有差異感，對於入門者這絕對是一帖良藥而且不用怕壞掉，哪天真的壞了也只需要執行一行 `docker rm gsdk `，將舊的容器刪除，就可以再重新建立一個了。

接下來要體驗一下使用 Google Cloud SDK 所以就讓我們來登入吧！

## 使用 gcloud 登入

```bash
$  gcloud auth login
Go to the following link in your browser:

    https://accounts.google.com/o/oauth2/auth?(略...)

Enter verification code: 
```

使用這行指令後如果 gcloud 的執行環境，在一個沒有辦法打開瀏覽器的環境會得到一個網址，需要將其貼到網頁上登入後得到一行字串，再回貼回來才能完成登入。

## 錯誤處理

自己在執行時發現有這個常見的錯誤，一般 Docker 指令預設權限是僅有 root 可以使用，如果要特定使用者可以使用，需要使用 `sudo usermod -aG docker {USER}` 將指定的使用者加入 docker 群組並重新登入。

**錯誤訊息**

```
docker: Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Post http://%2Fvar%2Frun%2Fdocker.sock/v1.40/containers/create?name=gsdk: dial unix /var/run/docker.sock: connect: permission denied.
See 'docker run --help'.

```

## 參考

* [Get Docker Engine - Community for Ubuntu](https://docs.docker.com/install/linux/docker-ce/ubuntu/)


