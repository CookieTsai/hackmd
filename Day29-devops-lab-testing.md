---
title: Day 29 - DevOps Lab 自動化腳本 - 測試篇
tags: 鐵人賽
description: |
  整理一年來的實務經驗並在三十天的自我挑戰中，找出 GitLab 與 GCP 眾多服務的一個最佳實踐方式
---

# Best Practice for DevOps on GitLab and GCP : DevOps Lab 自動化腳本 - 測試篇 - Day 29 -

![Imgur](https://i.imgur.com/SftnGs4.png)

## 前言

測試其實包含的內容非常多，從專案未打包到打包好，從上線前到上線後，有千千萬萬種測試辦法。在 [專案篇](https://ithelp.ithome.com.tw/articles/10220940) 時曾經提及過 Spring 專案，內含 Junit Test 會在 `mvn clean package` 的中間過程被執行到，這種測試通常屬於白箱類型的測試，也就是在知道邏輯的情況去做相關的測試。

而這篇文章主要是要做黑箱形式的測試，也就是當專案已經打包好後，通過使用者會使用的介面，模擬使用者行為來訪問服務，並確認服務能如同預期的給出回應。由於測試工具百百種難以抉擇，經過一番思考後決定使用 Shell 腳本來進行一場模擬的測試，而這個測試將介於 `build` 和 `deploy` 之間。

## 撰寫測試腳本

> 專案中加入一個名為 test.sh 其內容如下：

```bash
#/bin/bash

echo "Testing Started"

# Start Application Server and Listen 5000 port.
mvn -Dserver.port=${PORT:-5000} spring-boot:run > server.log 2>&1 &

# Waiting for Application Server Initialize.
sleep 25s

# Try to Visit Root API.
RESPONSE_CONTENT=$(curl -s http://localhost:${PORT:-5000})

# Check the API Success or Not.
if [ "$RESPONSE_CONTENT" = "Spring is here!" ]; then 
    echo "Application is OK."
else 
    echo "Application is not OK."
    exit -1
fi

echo "Testing Stopped"
```

## 自動化腳本

> 新增 Stage

```yaml
stages:
  - build
  - test
  - deploy
```

> 在建置及發佈之間新增一個 test job，內容如下

```yaml
test:
  stage: test
  script:
    - docker pull $IMAGE_TAG
    - docker run -i --rm $IMAGE_TAG bash test.sh
  tags:
    - devops-runner
```

> 完整的自動化腳本

```yaml
image: google/cloud-sdk:latest

stages:
  - build
  - test
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

test:
  stage: test
  script:
    - docker pull $IMAGE_TAG
    - docker run -i --rm $IMAGE_TAG bash test.sh
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

> 管線流程

![Imgur](https://i.imgur.com/eK6ycX0.png)

> 執行成功

![Imgur](https://i.imgur.com/33KmBMi.png)
