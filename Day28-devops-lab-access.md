---
title: Day 27 - DevOps Lab 撰寫腳本 - 權限篇
tags: 鐵人賽
description: |
  整理一年來的實務經驗並在三十天的自我挑戰中，找出 GitLab 與 GCP 眾多服務的一個最佳實踐方式
---

# Best Practice for DevOps on GitLab and GCP : DevOps Lab 撰寫腳本 - 權限篇 - Day 27 -

## 前言

映像檔已經被建立完成，接著在進入自動佈署前，還有一件很重要的事情，就是我們需要有足夠的權限，讓腳本在跑時能順利存取 `dev` 和 `qa` 等環境，這時就要來建立權限。話不多說我們開啟 [Cloud Shell](https://ssh.cloud.google.com/cloudshell/editor) 之後就開始瘋狂打指令吧！！

## 服務帳戶

> 建立服務帳戶

```bash
$ gcloud iam service-accounts create --display-name=gitlab-ci gitlab-ci
```

```bash
Updated property [core/project].
```

> 取得服務帳戶列表

```bash
$ gcloud iam service-accounts list
```

```
NAME                                    EMAIL                                               DISABLED
gitlab-ci                               gitlab-ci@my-devops-lab.iam.gserviceaccount.com     False
Compute Engine default service account  934898608672-compute@developer.gserviceaccount.com  False
```

## 腳色權限

> 賦予服務帳戶腳色權限

```bash
$ gcloud projects add-iam-policy-binding \
--member=serviceAccount:gitlab-ci@my-devops-lab.iam.gserviceaccount.com \
--role=roles/editor my-devops-lab
```

```bash
Updated IAM policy for project [my-devops-lab].
bindings:
- members:
  - serviceAccount:service-934898608672@compute-system.iam.gserviceaccount.com
  role: roles/compute.serviceAgent
- members:
  - serviceAccount:934898608672-compute@developer.gserviceaccount.com
  - serviceAccount:934898608672@cloudservices.gserviceaccount.com
  - serviceAccount:gitlab-ci@my-devops-lab.iam.gserviceaccount.com
  role: roles/editor
- members:
  - user:mcacookie@gmail.com
  role: roles/owner
etag: BwWTYR6MW9w=
version: 1
```

## 金鑰

> 建立 GitLab CI 使用的金鑰存放到 gitlab-ci.json

```bash
$ gcloud iam service-accounts keys create --iam-account=gitlab-ci@my-devops-lab.iam.gserviceaccount.com gitlab-ci.json
```

```bash
created key [f4261c64c045a70d33362444654ff28ef3efbf5f] of type [json] as [gitlab-ci.json] for [gitlab-ci@my-devops-lab.iam.gserviceaccount.com]
```

> 測試金鑰開啟新的分頁後使用指令

```bash
$ gcloud auth activate-service-account \
--project=my-devops-lab \
--key-file=/home/mcacookie/gitlab-ci.json
```

```bash
Activated service account credentials for: [gitlab-ci@my-devops-lab.iam.gserviceaccount.com]
```

> 點選『下載檔案』並輸入完整路徑名稱取得 gitlab-ci.json

![Imgur](https://i.imgur.com/QYf1JdI.png)

![Imgur](https://i.imgur.com/jRAFEe4.png)

## Variables 

> `設定` > `CI/CD` > `Variables` 新增一個 `GCP_KEY` 將 gitlab-ci.json 內容存入 value 中 

![Imgur](https://i.imgur.com/GV31D47.png)

## 結語

經過一番設定後當我們運行腳本時，可以在系統環境變數中取得 `GCP_KEY` 並搭配 `gcloud` 相關指令即可完成登入行為並開始操作 GCP 上的各種服務。