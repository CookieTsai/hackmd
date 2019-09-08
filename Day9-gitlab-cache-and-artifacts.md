---
title: Day 9 - GitLab CI 一定要懂的緩存機制
tags: 鐵人賽
description: |
  整理一年來的實務經驗並在三十天的自我挑戰中，找出 GitLab 與 GCP 眾多服務的一個最佳實踐方式
---

# Best Practice for DevOps on GitLab and GCP : GitLab 持續整合一定要懂的緩存機制 - Day 9 -

![Imgur](https://i.imgur.com/twtcZef.png)

## 前言

延續前兩篇文章，所提到的 `cache` 和 `artifacts` 兩個腳本屬性。兩種屬性都有一個特性，就是會將指定的內容存放在特定的位置。但使用上和用途面是完全不同的，接著我們來看一下他們的差異。

## Cache vs Artifacts

| 屬性 | 功能描述 |
| --- | --- | --- |
| Cache | 將 Job 運行過程中的檔案以壓縮方式`存放在本地 GitLab Runner` 中，供給其他 Job 在後續或他次於此運行時，可以依據 key 來找到先前緩存的檔案，其目的為`降低持續整合所需的時間` |
| Artifacts | 將 Job 運行過程中的檔案以壓縮方式`存放在遠端 GitLab Server` 上，供給給同次不同 Stage 間的 Job 可以獲取先前已產生的緩存檔案，其目的為`解決持續整合過程間檔案相依的問題` |

## 使用建議

### Cache

由於每次運行腳本時，Job 預設是隨機指定 GitLab Runner 執行，有高機率無法使用先前所準備的檔案，或是在不同台設備上跑完後，都存放了一份 Cache 導致空間的浪費，建議搭配 Tags 可以指定特定標籤的 Executor，如此就能準確命中緩存的檔案實現加速效果。 

### Artifacts

作者自己在使用 Artifacts 只有遇到兩個問題。

1. 早期 GitLab 8.7 左右的版本，對於 `expire_in` 的標籤沒有正常的支援，會造成檔案在不知何時刪除，或是根本不刪除的困擾，GitLab 9 之後的版本有對這件事修正。

2. Artifacts 會使用到網路進行傳輸，有機率在伺服器網路平寬不足或伺服器過於忙碌時，導致即便 Retry 數次也失敗的情況，最終造成該次整合測試失敗，建議使用時留意相關的配置。

## 定期清理

定期清理 Cache 與 Artifacts 是一件重要的事情，尤其是在自建的 GitLab Server 或 Runner 沒有足夠大空間時，這件事情變的尤為重要。

**Cache 存放路徑**

| Executor 類型 | 預設路徑  |
| ------------ | -------- |
| Shell        | 存放在運行 gitlab-runner 服務的使用者加目錄中，參考路徑如下: `/home/gitlab- runner/cache/<user>/<project>/<cache-key>/cache.zip` |
| Docker       | `/var/lib/docker/volumes/<volume-id>/_data/<user>/<project>/<cache-key>/cache.zip` |

**Artifacts 存放路徑**

Artifacts 預設存放在 `/home/git/gitlab/shared/artifacts` 目錄中，詳情參考[連結](https://docs.gitlab.com/ee/administration/job_artifacts.html#using-local-storage)。

## 範例

**腳本內容**

```yaml=
image: docker:stable

stages:
  - build
  - test

job1:
  stage: build
  cache:
    key: "$CI_JOB_NAME-cache"
    paths:
      - cache.txt
  script: 
    - echo "this is cache file" > cache.txt
    - echo "this is artifacts file" > artifacts.txt
    - echo "build, ok"
  artifacts:
    name: "$CI_JOB_NAME-artifacts"
    paths:
      - artifacts.txt
    expire_in: 30 mins

job2:
  stage: test
  script:
    - cat artifacts.txt
    - echo "test, ok"
  dependencies:
    - job1
```

**執行結果**

> 運行第二次的 Pipeline 畫面

![Imgur](https://i.imgur.com/IJ2USv1.png)

> 運行第二次的 Job1 詳細內容

![Imgur](https://i.imgur.com/2AxFO76.png)

> 運行第二次的 Job2 詳細內容

![Imgur](https://i.imgur.com/kennuxg.png)

## 結語

通過範例中的 Job1 詳細內容，可以看到運行第二次時，有獲取到第一次所產生的 Cache。而 Job2 詳細內容中，則可以看到成功獲取到 Job1 所產生的 Artifacts。經過一連串的介紹期望能使讀者在運用到相關功能時，能減少踩雷的機會。

如果嫌 Cache 和 Artifacts 麻煩的讀者，不妨試試存放在遠端的 S3 或其他可以上傳的空間，通過一些命名規則也能自行實現相關的功能，在檔案清理上也會更加的簡潔，缺點大概就是指令會變多，再請自行評估囉。

## Reference

* [Cache dependencies in GitLab CI/CD | GitLab](https://docs.gitlab.com/ee/ci/caching/)
* [Jobs artifacts administration | GitLab](https://docs.gitlab.com/ee/administration/job_artifacts.html)