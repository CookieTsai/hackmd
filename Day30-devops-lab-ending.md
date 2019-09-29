---
title: Day 30 - DevOps Lab 完結篇
tags: 鐵人賽
description: |
  整理一年來的實務經驗並在三十天的自我挑戰中，找出 GitLab 與 GCP 眾多服務的一個最佳實踐方式
---

# Best Practice for DevOps on GitLab and GCP : DevOps Lab 完結篇 - Day 30 -

![Imgur](https://i.imgur.com/9BpYsWN.jpg)

## 前言

子系列文章 DevOps Lab 已經接近尾聲，相信能一路跟著做的朋友，已經能更清楚的感受出，通過 GitLab CI/CD 來實現 DevOps 的過程，真的就是一個如何配置環境與撰寫腳本的過程。在這完結篇將通過一些圖，來回顧一下 DevOps Lab 到底完成些什麼。

## DevOps Lab 配置圖

> 在 GCP 上新增四個計算實例並在其中的一個安裝 GitLab Runner，通過這台 GitLab Runner 可以向另外三台進行自動化佈署

![Imgur](https://i.imgur.com/ISuRlzn.jpg)

## DevOps Lab 管線流程

> 簡單的三個階段 `建置` > `測試` > `佈署` 

![Imgur](https://i.imgur.com/7gz8PiG.png)

## DevOps Lab 自動化腳本

**建置階段**

```yaml
build:
  stage: build
  script:
    - docker build -t $IMAGE_TAG .
    - docker push $IMAGE_TAG
  tags:
    - devops-runner
```

**測試階段**

```yaml
test:
  stage: test
  script:
    - docker pull $IMAGE_TAG
    - docker run -i --rm $IMAGE_TAG bash test.sh
  tags:
    - devops-runner
```

**佈署階段**

```yaml
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

## 結語

DevOps Lab 系列文章就到這告一個段落了，回想開始寫時真的是毫無頭緒，不知道能寫多少篇，也不知道能會不會順利寫完，要一路不中斷的每天發文章又要文章的頭尾能被串的起來，真的是在完稿的前一刻都還是膽戰心驚。在創作的過程之中，也確實一度有問題而導致串不起來的情況發生，是在過去經驗的加持下才勉強，在有限的時間內邊修正文章邊找出錯誤點加以修正。

最後，想提在 DevOps Lab 是忽然萌發的一個想法，當時為此選擇了一張萌芽圖，而現在在完結篇時同時也是全系列文章的結束，所以就選擇了一張近黃昏的大樹圖。這一路花費了許多心血，雖然還是有很多很多沒設計好的地方，但仍然期許這次鐵人賽的系列文章，能幫助到更多的人有所成長，未來如果還有再次參賽也期望能有更多的人可以支持。