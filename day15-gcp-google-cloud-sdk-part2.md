---
title: Day 15 - GCP Google Cloud SDK 快速入手模式 (中)
tags: 鐵人賽
description: |
  整理一年來的實務經驗並在三十天的自我挑戰中，找出 GitLab 與 GCP 眾多服務的一個最佳實踐方式
---

# Best Practice for DevOps on GitLab and GCP : GCP Google Cloud SDK 快速入手模式 (中) - Day 15 -

## 前言

通過前一篇 [GCP Google Cloud SDK 快速入手模式 (上)](https://ithelp.ithome.com.tw/articles/10215467) 建置好環境後，要開始進入指令篇。gcloud 指令的特色是可以通過單台機器來管理雲上的多個專案，每一 gcloud 中可以有多組的 config 而每一個 config 可以定義預設的 `project`, `zone` 和 `region`。聽起來是不是有點小複雜，接著我們通過圖片來細細講解吧。


## 帳戶與專案

> 這是一個單帳號多專案的示意圖，意旨單一帳號 (Account) 是可以有多個專案 (Project)

![Imgur](https://i.imgur.com/C1AT9WJ.png)

> 來到 gcloud 使用指令 `gcloud config configurations list` 可以看到所有設定檔的狀態

![Imgur](https://i.imgur.com/ySG0chQ.png)

### 登入帳戶

> 接著我們來進行登入吧！使用 `gcloud auth login`
> 
> 這時我們會取得一個很長的 URL 貼到瀏覽器中

![Imgur](https://i.imgur.com/FQYo5kS.png)

> 選取自己的帳戶並同意後獲得一行可以複製的 Code

![Imgur](https://i.imgur.com/fEMTgvY.png)
![Imgur](https://i.imgur.com/Rnsm1M6.png)
![Imgur](https://i.imgur.com/pxiGXF2.png)

> 將 Code 貼回指令畫面即可完成登入流程開始操作

![Imgur](https://i.imgur.com/GRsSSeu.png)

### 設定專案

> 帳號登入後可以設定要選擇的專案，通過 `gcloud project list` 取得後並指定操作的專案

![Imgur](https://i.imgur.com/Vw3rjZN.png)

> 設定完專案後，再次重新檢視所有設定

![Imgur](https://i.imgur.com/ee9CzYc.png)

這時可以看到目前 `active` 的設定是 `default` 這一組，綁定了 `mcacookie@gmail.com` 帳號中的 `cookie-first-project` 專案。那或許眼尖的朋友會看到 DEFAUTL_ZONE 和 DEFAULT_REGION 怎麼沒有值，不需要嗎？其實通常是會設定的，但在設定前作者想先跟讀者們釐清一下觀念。

## 地區與區域

在一個地區 (region) 中會有多個區域 (zone)，而每個地區是一個真實實際存在的地理區域，在一個地區內兩台機器間的反應時間是相當短暫的。因此，一般最常見的作法是會將機器都佈署在同一個地區中。詳細的地區和區域請直接參考[地區和區域](https://cloud.google.com/compute/docs/regions-zones/)。

> 參考[地區和區域](https://cloud.google.com/compute/docs/regions-zones/)後知道台灣彰化有機房，距離最近所以設定該地區

![Imgur](https://i.imgur.com/CTDTklj.png)

> 設定完地區與區域後，再次重新檢視所有設定

![Imgur](https://i.imgur.com/lcOFs3q.png)

## 結語

至此環境準備好，相關的基礎設定也已經完成。有興趣測試控制多組帳號專案的朋友，可以通過 `gcloud config configurations create {config_name}` ，建立完新的設定檔後會自動切換過去，其他的設定內容是完全相同，而想切換回來可以利用 `gcloud config configurations activate default` 就會再一次回到預設設定。此系列最後一篇將講解基本的 gcloud 的基礎操作，而有過指令操作的朋友就可以直接開始玩咯。

## 參考

* [地理位置與地區](https://cloud.google.com/docs/geography-and-regions)
* [地區和區域](https://cloud.google.com/compute/docs/regions-zones/)




