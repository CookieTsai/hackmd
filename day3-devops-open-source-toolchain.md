---
title: Day 3 - DevOps 開源工具鍊
tags: 鐵人賽
description: |
  整理一年來的實務經驗並在三十天的自我挑戰中，找出 GitLab 與 GCP 眾多服務的一個最佳實踐方式
slideOptions:
  ## choose from none, fade, slide, convex, concave and zoom.
  transition: zoom
  ## https://github.com/hakimel/reveal.js/tree/master/css/theme
  theme: league
---

# Best Practice for DevOps on GitLab and GCP : DevOps 開源工具鍊 - Day 3 - 

## 前言

從數年前開始有許多公司逐漸在導入敏捷式 (agile) 開發，而如今已有為數不少的公司團隊，將敏捷式開發作為應徵者篩選的條件之一。而 DevOps 則是一種可以將敏捷式開發，更進一步提升到下一個階段的辦法，在這篇文章要來討論 DevOps 開源工具包。

## DevOps 開源工具包

![image alt](https://i.imgur.com/N6mr7Ko.png)
此圖為 2019 Gartner 提出的部分開源工具

在全世界有數也數不清的開源軟體，有些能幫助到專案從計畫到生產的時程縮短，有的能幫助到專案在視覺化狀態下輕鬆維運。在當今有越來越多的企業採用開源軟體作為開發工具，並且試圖以開源軟體來建置 [DevOps 工具鍊]()。

其最主要的原因是成本，開源軟體在費用上比起其方案所提供的工具要便宜得多；第二個原因是創新，隨著新的概念以及技術的演進，開源軟體有著更多隨著時代而改變的可能性。

各種開源軟體可用於自動化應用軟體開發的特定部分。例如，Gradle 構建應用軟體。Jenkins 持續交付應用軟體。Selenium 自動化為應用軟體進行測試。企業 IT 團隊可以利用數十個開源軟體來提高應用軟體開發流程的靈活性。實施 DevOps 的大多數 IT 團隊都使用開源軟體來處理部分或全部的工具鏈。

雖然開源軟體提供了極大的靈活性和創新潛力，但它們並未被預先整合起來。也就是說，使用者需要自行將這些工具拼裝出來。

以開源工具鏈支持敏捷性和創新是一件至關重要的事情，但沒有一步登天的辦法，弄清楚如何使用並維持開源工具鍊就是企業 IT 團隊需要做的項目之一。

## 五個創造開源工具鍊的方法 

### 一、發現你需要一個新的開發佈署流程

意識到當前從開發到生產的流程需要很長的時間時，您應當開始思考如何變更現有的開發佈署流程。但要留意變更開發佈署流程，這並不意味著專案就變成了敏捷式開發，因為這只優化了應用軟體開發生命週期的一部分，整體的生命週期可能仍然很慢。

### 二、從其他組織經驗中獲益

世界各地都有 DevOps Days，以及各式各樣的 DevOps 活動。他們提供了示範、討論的機會，與經驗豐富的人交流，他們通常專注於一件事，那就是如何實施和管理自動化工具鏈。利用其他人的知識並從他們的挫折中學習可以幫助您前進的辦法。

### 三、分析當前的流程

分析開發到生產過程的每個步驟，以確定每個步驟需要多長的時間。這能使您發現自動化所需的區域，並引導您的對於工具的選擇。

### 四、嘗試建構一套工具鍊

通過從他人身上獲取得經驗知識，將其落實在原有的專案中，俗話說的好讀萬卷書不如行萬里路，將知識落實能確保所學的正確性與合適性。但請留意新的流程應當在各項指標都有所提升，如果比原先更慢那就應該有所調整。

### 五、將工具鏈分享出去並文件化

這是一個關鍵，因為您的開發工具鏈將成為您公司未來的基礎。除了提升自己對於工具鍊的熟悉度外，也能確保工具鍊不會因年久而被遺忘，甚至還能因此找出潛在性問題並加以修正。

## Reference

* [Four Steps to Adopt Open-Source Software as Part of the DevOps Toolchain](https://blogs.gartner.com/manjunath-bhat/2019/02/08/four-steps-to-adopt-open-source-software-as-part-of-the-devops-toolchain/)
* [How to build an open-source toolchain for DevOps - TechBeacon](https://techbeacon.com/app-dev-testing/5-steps-building-open-source-toolchain-devops)
