---
title: Day 12 - GitLab 輕鬆十分鐘入手 Auto DevOps
tags: 鐵人賽
description: |
  整理一年來的實務經驗並在三十天的自我挑戰中，找出 GitLab 與 GCP 眾多服務的一個最佳實踐方式
---

# Best Practice for DevOps on GitLab and GCP : GitLab 輕鬆十分鐘入手 Auto DevOps - Day 12 -

![auto devops video](https://i.imgur.com/1GhEVSy.gif)
![auto devops title](https://i.imgur.com/NpB2yLC.png)

## 前言

大抵上如同標題所言僅需一個設定就可以開啟 Auto DevOps，而 Auto DevOps 就有機會在零設定的情況下完成所有事情。

當然夢想是美好的現實是殘酷的，

## 快速建立專案

1. 建立專案時選擇『Create from template』

![Imgur](https://i.imgur.com/xWdIf3G.png)

2. 輸入專案名稱並點選『Create project』

![Imgur](https://i.imgur.com/SKRPuRT.png)

3. 完成畫面，就擁有了一個類似於 Hello World 的 Spring 專案咯!!

![Imgur](https://i.imgur.com/jramp15.png)


## 開啟 Auto DevOps

1. 開啟 `設定 > CI/CD > 自動 DevOps` 中的『預設使用自動 DevOps 管線』。(其實這樣就可以用，但只會有建置與測試兩個階段)

![open devops](https://i.imgur.com/KizaxtV.png)

2. 新增 Kubernetes Cluster

![image alt](https://i.imgur.com/kO8UDtT.gif)

3. 安裝 Helm 和 Tiller

![install-helm-and-titler](https://i.imgur.com/Wh03oIP.png)

4. 於 Kubernetes Cluster 上安裝 Ingress, Prometheus 和 GitLab Runner

![install-charts](https://i.imgur.com/G1V5wNv.png)

5. 設定 Base Domain 並儲存變更 (待 Ingress 安裝好後會出現紅字部分)

![base-domain](https://i.imgur.com/TTD14Mr.png)

## 成果畫面

> 完成『快速建立專案』和『開啟 Auto DevOps』後，在每次的推版都會自動從建置到佈署

![Imgur](https://i.imgur.com/uQ5Os60.png)

## 結語

Auto DevOps 的啟用確實不難，但還有很多深層次的內容值得去挖掘。在軟體界一個不可控的開發過程，事實上是很不盡理想。因此，雖然有了 Auto DevOps 並不意味著可以省去學習的過程，相反的在很多時候為了能夠有效的調整和優化，需要掌握更多的知識以確保開發出來的東西，不至於變成後人們口中的怪物或恐懼的東西。

## Reference

* [Auto DevOps | GitLab](https://about.gitlab.com/product/auto-devops/)