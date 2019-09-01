---
title: DevOps Best Practice for GitLab CI/CD and GCP - Day 1 - DevOps 定義與流程
tags: 鐵人賽
description: |
  整理一年來的實務經驗並在三十天的自我挑戰中，找出 GitLab CI/CD 與 GCP 眾多服務的一個最佳實踐方式
slideOptions:
  ## choose from none, fade, slide, convex, concave and zoom.
  transition: zoom
  ## https://github.com/hakimel/reveal.js/tree/master/css/theme
  theme: league
---

## DevOps Best Practice for GitLab CI/CD and GCP - Day 1 - DevOps 定義與流程

### DevOps 的定義

<center>
<img height="300px" src="https://i.imgur.com/WvyvvhB.png" />

<font color="gray" size="1">圖 1-1. Devops</font>

</center>

#### DevOps 的起源

在近年來敏捷式 (agile) 開發逐步崛起，如大家而熟能詳的 Scrum 和看板就是其中的成員，軟體交付的速度更快、質量更高的同時成本也更低。然而一個主要的缺點是傳統的維運到佈署開始難以有效的配合，敏捷開發所提倡的速度、溝通和協作文化與傳統維運模式的文化不一致，其產生的結果是開發團隊能快速產生企業所需的軟體，但軟體卻無法快速佈署到生產環境。

#### DevOps 是甚麼

在上一段的問題已經被 DevOps 所解決，通過將開發和維運兩個團隊合併成為一個全新的團隊，使知識和技能得以被共享，而此模式則被稱之為 DevOps。

#### DevOps 的共通特性

由於 DevOps 並沒有統一的定義，但從各家所提出的 DevOps 可以找到一些共通的特性，而這些特性則描述了 DevOps 是如何被使用

**人員**

文化、行為、開發、合作、可擴展性和親和力是 DevOps 的主要內容

**方法**

DevOps 的一個重要方面是敏捷化的思考和作法，無論是 Scrum、看板還是其他形式的開發模式中，精益求精的思想和持續改善的流程皆為不可缺少的特點

**資源**

資源方面強調產品的生產方式，如版本控制、集成、佈署和交付等方面會被經常的討論，而最主要側重這方面的就是所謂的「自動化」

### DevOps 的流程

<center>

<img height="250px" src="https://i.imgur.com/ur7cAwv.jpg" />

<font color="gray" size="1">圖 1-2. Devops 循環圖</font>

</center>

圖 1-2 是一個 DevOps 循環圖，這不是唯一或正式的定義，而是表示多數組織機構中，為了實現一個服務而會被循環執行並合乎邏輯順序的一系列階段。

<font color="green">**深色**</font>部分表示開發階段，<font color="lightgreen">**淺色**</font>部分表示維運階段，兩個階段構成了 DevOps 的完整循環。


#### 

#### 

### 結論

DevOps 不是一套工具，也不是一種噱頭。DevOps 更多的是基於一些行動原則，以便能更好的使得團隊是有效的被運作，以及更好的滿足客戶對於時間及附加價值的需求，而其原則包括文化方面、合作方面和通過工具來實現自動化。

### Reference

* [devops 最佳实践 - Bart de Best](https://www.google.com/search?hl=zh-Hant-TW&ei=Q2JrXYfkIqmTr7wPo6qD4A8&q=devops+%E6%9C%80%E4%BD%B3%E5%AE%9E%E8%B7%B5+Bart+de+Best&oq=devops+%E6%9C%80%E4%BD%B3%E5%AE%9E%E8%B7%B5+Bart+de+Best&gs_l=psy-ab.3...1131.7627..7814...0.0..1.184.1138.10j3......0....1..gws-wiz.......0i30j33i160.kGu21LfbJrE&ved=0ahUKEwjHkY7h_K7kAhWpyYsBHSPVAPwQ4dUDCAo&uact=5)
* [DevOps - 維基百科，自由的百科全書](https://zh.wikipedia.org/wiki/DevOps)