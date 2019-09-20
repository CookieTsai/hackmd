---
title: Day 22 - DevOps Lab 專案環境建置
tags: 鐵人賽
description: |
  整理一年來的實務經驗並在三十天的自我挑戰中，找出 GitLab 與 GCP 眾多服務的一個最佳實踐方式
---

# Best Practice for DevOps on GitLab and GCP : DevOps Lab 專案環境建置 - Day 21 -

![Imgur](https://i.imgur.com/riS7LgF.jpg)

## 前言

為完成 DevOps Lab 首要是要能有一個專案。專案是為了能夠持續的被開發並且交付給客戶，以持續的為公司獲取利益為首要。所以無論是否有實現 CI/CD 都應該以建立專案為第一步。而此篇文章就將開始引導各位建立一個小型的 Java 專案。

## 第一步：安裝 Git

點選[連結](https://git-scm.com/book/zh-tw/v2/%E9%96%8B%E5%A7%8B-Git-%E5%AE%89%E8%A3%9D%E6%95%99%E5%AD%B8)後，請依照作業系統類型安裝 Git

## 第二步：安裝 JDK (以 Windows 安裝 JDK 8 為範例)

**下載 JDK**

> 點擊[連結](https://www.oracle.com/technetwork/java/javase/downloads/index.html)進入 Orcle JDK 下載頁面，選擇 JDK 8 的 Download 下載連結

![Imgur](https://i.imgur.com/dNaAaSJ.png)

> 點選『Accept License Agreement』後，下載適合自己的版本

![Imgur](https://i.imgur.com/jnsoTLh.png)

**安裝 JDK**

> 雙擊下載下來的檔案，依指示安裝即可

![Imgur](https://i.imgur.com/ZA61Sdq.png)

## 第三步：建立專案

> 1. 在 GitLab 專案中，點擊『新增專案』

![Imgur](https://i.imgur.com/FFlLHbp.png)

> 2. 選擇『Create from template』並使用 Spring 的模板

![Imgur](https://i.imgur.com/GbG2prP.png)

> 3. 輸入`專案名稱`後，點擊『Create project』

![Imgur](https://i.imgur.com/LQ8yXzx.png)

> 4. 取得 Clone with HTTPS 的網址

![Imgur](https://i.imgur.com/SBa4IlE.png)

> 5. 開啟終端機工具，使用 `git clone {第四部取得的網址}`，參考如下：

![Imgur](https://i.imgur.com/JTE8nlc.png)

> 6. 輸入 `cd {專案名稱}` 進入專案目錄

```bash
$ cd devops-lab/
```

> 7. 建立 `dev` 和 `prod` 兩個 branch 並推送至伺服器，參考如下：

![Imgur](https://i.imgur.com/95dxDnH.png)

> 8. (Option) 至`設定 > 版本庫 > Protected Branches` 將 `prod` 設定為僅有 Maintainers 可以 push 和 merge

![Imgur](https://i.imgur.com/3ozt1KL.png)

> 9. 查看當前有的 branch

![Imgur](https://i.imgur.com/J6RDZE2.png)

## 結語

至此成功建立好一個專案，並且以依照[DevOps Lab 開篇 - 環境與配置]()中的 Git 規則訂定，將所需的 `dev`、`master` 和 `prod` 建立起來。在建立自動化腳本前，還需要將部屬的設備設定完成，因此，下一篇將介紹如何在 GCP 環境上建立 `Dev`、`QA`和`Prod`等環境。