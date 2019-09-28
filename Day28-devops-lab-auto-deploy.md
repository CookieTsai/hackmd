---
title: Day 28 - DevOps Lab 自動化腳本 - 佈署篇
tags: 鐵人賽
description: |
  整理一年來的實務經驗並在三十天的自我挑戰中，找出 GitLab 與 GCP 眾多服務的一個最佳實踐方式
---

# Best Practice for DevOps on GitLab and GCP : DevOps Lab 自動化腳本 - 佈署篇 - Day 28 -

![Imgur](https://i.imgur.com/7urFzP2.jpg)

## 前言

終於進入自動佈署篇這個大魔王關卡，會這麼說是因為通常在這個環節佈署的規則，真的是因專案而有很大的差異。在這篇裡面會使用一種相對簡單又常見的佈署方式，也就是將建置好的映像檔以檔案方式放置到期望佈署的機器上，然後在該台設備上使用指令進行更新，那接著我們就開始這次的篇章吧！

## 流程講解

首先，在先前的 [GCP篇]() 中新增了三台計算實例，文章將對其中的 `dev` 環境進行自動佈署。由於要用到映像檔因此第一步將在 `dev` 環境中，補安裝 Docker CE，而後就開始編寫相關佈署腳本。腳本內容會從 [映像檔篇]() 繼續新增修改下去，最終完成時會拆分成 `build` 和 `depoly` 兩個 Stage，並且實現在自動佈署這件事。而在佈署的過程會使用到 權限篇 所設定的三個變數 `GCP_KEY`、`PRI_KEY` 和 `PUB_KEY`，所以這篇文章是一個必須將前面個環節一一完成後才能進行的大魔王篇章！！

## 遠端連線

> 開啟 [Cloud Shell](https://ssh.cloud.google.com/cloudshell/editor) 並設定好環境

```bash
# 設定專案 ID
$ gcloud config set project my-devops-lab

# 設定區域
$ gcloud config set compute/zone asia-east1-a
```

> 遠端連線至 `dev`

```bash
$ gcloud compute ssh cookie@dev
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

## 編寫腳本

> [映像檔篇]() 的腳本

```yaml
image: google/cloud-sdk:latest

services:
  - docker:dind

variables:
  DOCKER_HOST: tcp://docker:2375/
  DOCKER_DRIVER: overlay2
  DOCKER_TLS_CERTDIR: ""
  IMAGE_TAG: $CI_REGISTRY_IMAGE:$CI_COMMIT_REF_SLUG

before_script:
  - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
  
build:
  script:
    - docker build -t $IMAGE_TAG .
    - docker push $IMAGE_TAG
  tags:
    - devops-runner
```

> 首先，在腳本最外層增加 `stages` 拆分成 `build` 和 `deploy` 不同的 stage

```bash
stages:
  - build
  - deploy
```

> 定義新變數

```
variables:
  DEPLOY_TAG: my-app:$CI_COMMIT_REF_SLUG
```

> 設置佈署時 SSH 連線所需要的公私鑰

```yaml
deploy:
  stage: deploy
  script:
    - mkdir ~/.ssh || echo 'ok'
    - cat $PRI_KEY > ~/.ssh/google_compute_engine
    - cat $PUB_KEY > ~/.ssh/google_compute_engine.pub
    - chmod 600 ~/.ssh/google_compute_engine
```

> 使用 `$GCP_KEY` 進行登入並設定區域

```yaml
deploy:
  stage: deploy
  script:
    - gcloud auth activate-service-account --project=my-devops-lab --key-file="$GCP_KEY"
    - gcloud config set compute/zone asia-east1-a
```

> 取得 `build` 階段所建立的映象檔，並以檔案形式傳輸到 `dev` 實例中

```yaml
deploy:
  stage: deploy
  script:
    - docker pull $IMAGE_TAG
    - docker tag $IMAGE_TAG $DEPLOY_TAG
    - docker save $DEPLOY_TAG > my-app.tar
    - gcloud compute scp my-app.tar cookie@dev:~
```

> 遠端執行指令，匯入映像檔 -> 刪除舊容器 -> 建立新容器

```yaml
deploy:
  stage: deploy
  script:
    - gcloud compute ssh cookie@dev --command "cat my-app.tar | docker load"
    - gcloud compute ssh cookie@dev --command "docker rm -f my-app || echo 'ok'"
    - gcloud compute ssh cookie@dev --command "docker run -td -p 5000:5000 --name my-app $DEPLOY_TAG"
```

> 將 deploy 階段限制在 dev 的完整自動化佈署的腳本

```yaml
image: google/cloud-sdk:latest

stages:
  - build
  - deploy

services:
  - docker:dind

variables:
  DOCKER_HOST: tcp://docker:2375/
  DOCKER_DRIVER: overlay2
  DOCKER_TLS_CERTDIR: ""
  IMAGE_TAG: $CI_REGISTRY_IMAGE:$CI_COMMIT_REF_SLUG
  DEPLOY_TAG: my-app:$CI_COMMIT_REF_SLUG

before_script:
  - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
  
build:
  stage: build
  script:
    - docker build -t $IMAGE_TAG .
    - docker push $IMAGE_TAG
  tags:
    - devops-runner

deploy:
  stage: deploy
  script:
    - mkdir ~/.ssh || echo 'ok'
    - cat $PRI_KEY > ~/.ssh/google_compute_engine
    - cat $PUB_KEY > ~/.ssh/google_compute_engine.pub
    - chmod 600 ~/.ssh/google_compute_engine
    - gcloud auth activate-service-account --project=my-devops-lab --key-file="$GCP_KEY"
    - gcloud config set compute/zone asia-east1-a
    - docker pull $IMAGE_TAG
    - docker tag $IMAGE_TAG $DEPLOY_TAG
    - docker save $DEPLOY_TAG > my-app.tar
    - gcloud compute scp my-app.tar cookie@dev:~
    - gcloud compute ssh cookie@dev --command "cat my-app.tar | docker load"
    - gcloud compute ssh cookie@dev --command "docker rm -f my-app || echo 'ok'"
    - gcloud compute ssh cookie@dev --command "docker run -td -p 5000:5000 --name my-app $DEPLOY_TAG"
  tags:
    - devops-runner
  only:
    - dev
```

## 執行結果

> 管線作業

![Imgur](https://i.imgur.com/eXRi2jn.png)

> 佈署成功

![Imgur](https://i.imgur.com/kKlnx3t.png)


## 結語

自動佈署腳本完成咯! `qa` 環境可以依樣畫葫蘆的做出來，留給讀者們自行嘗試跟研究。自動化佈署的方法其實還有許多種實現的辦法，只要想得通的方式其實幾乎都能實現，例如將映像檔先放置到遠端檔案庫中在取回，或是結合現在很流行的 kubernetes 都是不錯的辦法，所以也不要侷限於本篇所提到的方式。