---
title: Day 4 - 淺談 DevOps 過去、現在和未來
tags: 鐵人賽
description: |
  整理一年來的實務經驗並在三十天的自我挑戰中，找出 GitLab 與 GCP 眾多服務的一個最佳實踐方式
slideOptions:
  ## choose from none, fade, slide, convex, concave and zoom.
  transition: zoom
  ## https://github.com/hakimel/reveal.js/tree/master/css/theme
  theme: league
---

# Best Practice for DevOps on GitLab and GCP : 淺談 DevOps 過去、現在和未來 - Day 4 - 

![](https://i.imgur.com/sXL8oqh.jpg)

## 前言

無論你是否曾經聽過，但就在今時今日 DevOps 一詞，已在全世界的軟體界如日中天。在過去、現在和未來同樣是 DevOps，但面臨的問題是不完全相同的，可能有一部分的問題依舊，但隨著時間的和技術的轉變，DevOps 開始逐步地轉化中，那到底差異在哪呢？

> 圖一、敏捷與 DevOps 時間線

![image alt](https://i.imgur.com/vwb8HVH.png)

> 圖二、敏捷式與瀑布式之產品交付與時間的關係圖

![image alt](https://i.imgur.com/G3eSs8L.png)

### 過去 (2009 年)

#### 混亂之牆

![](https://i.imgur.com/zTeUjp3.png)

過去以開發為主的人往往有一種模式就是變革，因為開發人員的價值來自於盡可能地去創造更多的可能，業務也依賴著開發人員提供的變化，來去因應市場的需求。

而維運人員的心態，則是業務依賴他們來保持服務的穩定並以此來獲取利益。變化是一種會破壞穩定性和可靠性的敵人，通常維運會本能地抗拒，根據統計停機的百分之八十，原因源自於那些被稱作變革的變化而產生的自我傷害。

#### 災難

![Imgur](https://i.imgur.com/Zj55lyK.png)

當開發人員發佈新的版本，維運人員會獲取其開始佈署，手動操作著指令或用自己已經寫好的腳本來進行。而當環境有所改變，就經常性的會發現新的問題，即便前一次操作成功，也無法保證下一次的操作成功，此時從開發到 生產就是個大災難。

#### 發展重點

* 為開發與維運建立起溝通的橋樑
* 開發與維運供通的知識與技能
* 使被交付的軟體是高品質的

### 現在 (2019 年)

#### DevOps Tools 百家爭鳴

![](https://i.imgur.com/jjCDV12.jpg)

在每一個階段上開始出現大量的可用工具，等待著開發與維運人員去開墾，如何從現有的專案進入到下一階段，成了每一個開開發維運人員應該要去著墨的事情。

#### 容器化

Docker 容器化技術在近年異軍突起，持續交付到佈署的門檻一下子來到一個低點。目前已經到各大雲端平台都爭先恐後支援 Docker 相關服務的程度。軟體的生產供應鏈格局與效率在不斷的變化加速中，而基於 Docker 的微服務架構也與日俱增日漸成熟。

#### 發展重點

* 開發與維運的接觸越來越多，技術與知識將激出更多的可能
* 各大企業已開始加入 DevOps 行列，更多的企業將逐步邁入
* 不同的 DevOps 工具鍊開始爭先恐後的出現，隨著時間日漸完善

### 未來

DevOps 從史至今就是一種為了讓產品能更加符合客戶需求，同時讓企業擁有更強大競爭力的工具，在未來的數年內依舊會是如此。更多的團隊將加入此團隊也變得更加有效率集競爭力，如：近年興起的 DataOps 就是講 DevOps 團隊與資料工程師、資料分析師的相互整合。

眾多舊有的系統將開始被轉化成 Serverless 形式的容器，佈署在如 kubernetes 的服務上，DevOps 仍會是一種勢不可擋潮流。

## Reference

* [DevOps 的过去、现在，及未来](https://www.jianshu.com/p/293b67c23efa)
* [What is DevOps？ --- dev2ops](http://dev2ops.org/2010/02/what-is-devops/)
* [DevOps 实践指南](https://zhuanlan.zhihu.com/p/33984591)
