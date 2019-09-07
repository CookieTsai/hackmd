---
title: Day 6 - GitLab CI/CD
tags: 鐵人賽
description: |
  整理一年來的實務經驗並在三十天的自我挑戰中，找出 GitLab 與 GCP 眾多服務的一個最佳實踐方式
---

# Best Practice for DevOps on GitLab and GCP : GitLab CI/CD - Day 6 -

![GitLab Basic Workflow](https://i.imgur.com/WDhnRpx.png)

## 簡介

GitLab CI/CD 是 GitLab 內建的強大工具，使您將所有持續性方法（持續整合、交付和部署）應用在軟體開發上，且無需與第三方應用軟體整合。

## 方法簡介

GitLab CI/CD 為了使軟體開發時能以最大限度地減少在開發
輸入錯誤的可能性，是以自動化執行腳本的模式，在每次推版過程中持續性的執行建構、測試和部署。藉由每一個階段所執行的結果，不斷優化軟體從而減少存在缺陷的可能性。

這種模式有三種主要的階段，每個階段都可以根據合適的方式進行調整。

### 持續整合

一個正處於開發或為運中的軟體，它被儲存在 GitLab 的儲存庫中，開發人員每天辛勞的進行編碼，然後推送到儲存庫中。GitLab CI 提供開發人員在每次推送後，自動化的依照設定好的腳本進行建構和測試，從而減少開發中的軟體被引入錯誤的可能性。

這種做法被稱為持續整合; 對於提交給應用程序的每個更改 - 甚至是開發分支 - 它都是自動且持續地建構和測試的，確保新加入的異動，符合您為專案建立的所有測試。

### 持續交付

持續交付是持續整合中的一個步驟。開發中的應用程式將在每一次更改時，不僅進行建構和測試。並且，會額外觸發一個持續性交付，將可佈署的應用程式準備好，等待人為手動觸發進行持續性的佈署。

### 持續佈署

持續部署也是持續整合的一個步驟，類似於持續交付。不同之處在於，不再需要手動部署應用程式，而是將其設置為自動化佈署。完全不再需要人工干涉，就可以將應用程式佈署完成。

## 工作原理

使用 GitLab CI / CD，需要一個託管在存儲庫中的應用程式代碼庫，並在根目錄中新增一個名為 `.gitlab-ci.yml` 的文件，將所需的建構、測試和佈署的腳本，預先編寫完成。

在此文件中，可以定義緩存的規則、執行命令的先後順序、部署應用程式的位置以及指定是否將要自動運行或手動觸發腳本...等。熟悉 GitLab CI/CD 後，可以更有彈性的進行配置。

將 `.gitlab-ci.yml` 文件新增到存儲庫後，GitLab 將發現它並使用名為 GitLab Runner 的工具運行腳本，此工具可獨立於 GitLab CE/EE 之外。

一個簡單的 `.gitlab-ci.yml` 文件範例：

```yaml
run-test:
  before_script:
    - apt-get install rubygems ruby-dev -y
  script:
    - ruby --version
```

設定中 run-test 被視為一個 Job，before_script 將被先行執行，而後才執行 script 的內容。在此範例中 before_script 和 script 皆只執行一行指令，在實際運行的腳本中，通常會見到多行的指令被依序執行。

GitLab CI/CD 不僅可以執行您設置的作業，還可以顯示執行過程中發生的情況，如圖所示：

![run job](https://i.imgur.com/3lY4SYQ.png)

此外還可以設置管道作業，並藉由 GitLab Pipeline 顯示：

![pipeline](https://i.imgur.com/LQbdDeH.png)

最後，如果出現任何問題，可以輕鬆進行回滾所有更改：

![roll back](https://i.imgur.com/UzwCzay.png)

## 結語

GitLab CI/CD 通過設置 `.gitlab-ci.yml` 在每一次推送或合併時，觸發已被邊寫好的腳本，被運行的結果可以通過 GitLab Pipeline 看到執行的狀況與最終的結果。

經過上一篇 [GitLab 前世今生](https://ithelp.ithome.com.tw/articles/10213937) 知道 GitLab Runner 於 2015年1月加入，在持續近五年多的發展下，GitLab CI/CD 已日趨成熟完善而穩定，在後續將帶來 GitLab Runner 更詳細的介紹，以及如何設置腳本的方法與技巧，敬請期待。

## Reference

* [GitLab CI/CD | GitLab](https://docs.gitlab.com/ee/ci/)
* [Introduction to CI/CD with GitLab | GitLab](https://docs.gitlab.com/ee/ci/introduction/index.html#introduction-to-cicd-methodologies)