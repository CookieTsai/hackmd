---
title: Day 16 - GCP Google Cloud SDK 快速入手模式 (下)
tags: 鐵人賽
description: |
  整理一年來的實務經驗並在三十天的自我挑戰中，找出 GitLab 與 GCP 眾多服務的一個最佳實踐方式
---

# Best Practice for DevOps on GitLab and GCP : GCP Google Cloud SDK 快速入手模式 (下) - Day 15 -

## 前言

通過前兩篇 [GCP Google Cloud SDK 快速入手模式 (上)]() 和 [GCP Google Cloud SDK 快速入手模式 (中)]() 設定都已經就定位再來就是實際操作，漫無目的的操作不是個辦法，因應本系列是要整合 GitLab 和 GCP，所以就以設定讓 GitLab CI 可以順利存取 GCP 為目標吧！話不多說立即開始吧！

## 設定身分與存取權管理 (IAM)

> IAM 是 Google Cloud 中管理腳色、帳號和權限的一個功能，為了讓 GitLab 有存取的權限，要建立一個 Service Account

![create-gitlab-ci-service-account](https://i.imgur.com/sgx22De.png)

> 賦予 GitLab Service Account 腳色權限

![gitlab-account-add-policy](https://i.imgur.com/tMLz0xz.png)

> 建立一把 GitLab CI 在登入可以使用的 Key 並存入 gitlab-ci.json

![Imgur](https://i.imgur.com/g21Xo0E.png)

這時我們就完成了權限上的設定，並且產出了一個以 JSON 為格式的金鑰，可以在運行 GitLab CI 時使用。

## 測試金鑰

> 使用 `gcloud config configrations create gitlab-ci` 建立一組先的設定

![Imgur](https://i.imgur.com/8pcAwtI.png)

> 使用 `gitlab-ci.json` 進行登入，並查看登入後設定檔列表

![Imgur](https://i.imgur.com/CfxiZRc.png)

> 取得 Google Compute 設備列表 (如果第一次操作到此 API 會初始化)

![Imgur](https://i.imgur.com/sappPEG.png)

## 結語

`gcloud` 指令功能眾多推薦讀者在練習時，可以如同本篇假定一些想要實現的事情，再根據 GCP 所提供的文件按部就班的試驗，相信經過一小段時間的練習後，就可以完全掌握所需的功能指令。

## 參考

* [將角色授予服務帳戶](https://cloud.google.com/iam/docs/granting-roles-to-service-accounts)