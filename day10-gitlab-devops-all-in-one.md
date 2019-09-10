---
title: Day 10 - GitLab 淺談單一工具鍊
tags: 鐵人賽
description: |
  整理一年來的實務經驗並在三十天的自我挑戰中，找出 GitLab 與 GCP 眾多服務的一個最佳實踐方式
---

# Best Practice for DevOps on GitLab and GCP : GitLab 淺談單一工具鍊 - Day 10 -

![Imgur](https://i.imgur.com/P4ALcEB.png)

![Imgur](https://i.imgur.com/11fQYbW.png)

## 前言

在通過腳本入門 GitLab CI/CD 後作者想說，也是時候來回看 GitLab 號稱是一個擁有完整 DevOps 的工具。從上圖可以看出 DevOps 在不同環節中通常需要搭配各式工具，而 GitLab 是一個宣稱所有環節都可以在 GitLab 平台上實現。

是否真的這麼厲害呢？在作者的研究中，確實 GitLab 提供一系列相當好用的功能，彼此間甚至有所連動，例如：在使用 GitLab 看板時，可以建立 Branch 與議題連動。或是，使用 GitLab CI/CD 產出的 Docker Image 可以直接存放在 GitLab Container Registry，真的都是些既免費又實用的功能。

那是否就意味著學習 GitLab 一家就好呢？作者認為不盡然，DevOps 工具日新月異各種工具推陳出新，但 GitLab 提供的一整套工具作者認為是有好處的，接下來就跟各位朋友一起來探討，以 GitLab 作為 DevOps 唯一的工具有哪些優缺點。

## 是否適合 學習/導入？

![reading](https://i.imgur.com/RagaLas.jpg)

### 個人

在微軟擁抱 Linux 的現在來看，任何平台的人似乎都能駕馭 Git 但卻未必使用 GitLab，在原先就已經使用其他工具來組成 DevOps 工具鍊的情況下，則在使用 GitLab CI/CD 重新學習重構，就稍嫌浪費時間在學習相同的東西。

而在自身對於指令是不熟的情況下，想徹底入手完整的 GitLab CI/CD 是有些難度的，所以作者建議適合學習的人有，擅長指令、熟悉 Docker 或 Kubenetes 之一的人才考慮入手此技術。 

![team](https://i.imgur.com/h0WkRVs.jpg)

### 企業

作為一間企業會有多個團隊，不同的團隊會使用不同的技術，一般來說不同團隊會自然而然的在 DevOps 工具鍊上，組合不同的結果來。

而使用 GitLab 以單一工具來實現所有內容，有機會使得技術被複製運用，減少使用未知技術而產生的人力成本。因此，以企業觀點來說這是一種不錯的方案，既可以減少成本又有機會提升品質。

## 結語

曾經數月時間在研讀 GitLab 相關的文件，作為一個學習者並使用著 GitLab 的人。對於能開發出一套如此工具的企業/團隊，是有一份深深的感激。這並不限於 GitLab 所提供的服務，而是世界上每一個出色的作品。

GitLab 所提供的工具鍊與 [DevOps 工具鍊](https://ithelp.ithome.com.tw/articles/10213443) 中提及的 Gartner 工具鍊，是不盡相同的但頗有雷同之處足以借見，因此作者也不在此篇重複講述七個不同的階段。後續文章將直接深入了解，每個階段 GitLab 所提供的實際工具為何，該如何使用又有什麼效果。

## Reference

* [The DevOps Lifecycle with GitLab](https://about.gitlab.com/stages-devops-lifecycle/)
* [DevOps Tools](https://about.gitlab.com/devops-tools/)