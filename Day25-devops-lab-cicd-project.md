---
title: Day 25 - DevOps Lab 撰寫腳本 - 專案篇
tags: 鐵人賽
description: |
  整理一年來的實務經驗並在三十天的自我挑戰中，找出 GitLab 與 GCP 眾多服務的一個最佳實踐方式
---

# Best Practice for DevOps on GitLab and GCP : DevOps Lab 撰寫腳本 - 專案篇 - Day 25 -

## 前言

通過專案環境建置 GitLab篇、GCP篇 和 GitLab Runner篇下來。已經有了初步的雛型，再來目標就是要產出自動化腳本，在撰寫腳本前首要要先認識專案，釐清要如何編寫腳本，分成幾個 Stage 幾個 Job。

## 認識專案

```
devops-lab
├─.mvn
│  └─wrapper
│      └─maven-wrapper.jar
│      └─maven-wrapper.properties
├─src
│   ├─main
│   │  ├─java
│   │  │  └─com
│   │  │      └─example
│   │  │          └─demo
│   │  │              └─DemoApplication.java
│   │  └─resources
│   └─test
│       └─java
│           └─com
│               └─example
│                   └─demo
│                       └─DemoApplicationTests.java
├─Dockerfile
├─mvnw
├─mvnw.cmd
├─pom.xml
└─README.md
```

這是使用 GitLab Spring Template 建置出來的專案，本身是使用到 Maven 建置出的專案，接著以下對幾種此專案有用到的技術分門別類的介紹。

### 工具篇

> **Apache Maven**

在檔案中有一個 `pom.xml` 這是 Maven 專案的特徵，在檔案中會描述著專案使用的套件、建置方式以及各種與專案建置管理相關的設定，通過使用 `mvn clean package` 指令會進行打包，並預設放置於 `target` 資料夾之中。

在打包的過程中實際上經過 `validate` > `compile` > `test` > `package`，這意味著當使用 Maven 完成打包時是已經通過單元測試。

> **mvnw**

這是一種當作業系統有安裝 JDK 沒有安裝 Maven 時，依舊能夠正常通過 mvnw 指令運行 Maven 相關功能的工具。使用上非常簡單原本使用 `mvn` 執行的內容都改為 `mvnw` 即可。

專案中 `wrapper` 資料夾內的資料就是當發現系統沒有安裝 Maven 時會使用到的相關程式。

> **Docker**

專案中有 `Dockerfile` 內容描寫了如何將專案，建置成 Docker Image 的方法，並在運行 Docker 容器時，會使服務監聽在 5000 Port。想測試建置是否能正常的話，要在本機安裝 Docker 並執行 `docker build -t {repository name} .`。

### Java 函式庫篇

> **Spring Framework**

在專案中主要是利用到 Spring Framework 實現一個 Web Service，並在 `DemoApplication.java` 中撰寫了一個 API 只要當伺服器啟動後訪問根網頁，就會獲得一個 `Spring is here!` 的回應。

> **Junit**

`DemoApplicationTests.java` 使用 junit 及 Spring Framework 中的 Test 工具，實現對根網頁 API 的請求測試並確認回傳內容應為 `Spring is here!`。

## 結語

與讀者們一起查看專案到此，對專案有了一定的了解。接就是正式進入腳本的撰寫，第一階段會先將建置及發佈的部分完成。後續在第二階段再增加自動佈署的腳本。



