---
title: Day 8 - GitLab 之第一次寫腳本就上手
tags: 鐵人賽
description: |
  整理一年來的實務經驗並在三十天的自我挑戰中，找出 GitLab 與 GCP 眾多服務的一個最佳實踐方式
---

# Best Practice for DevOps on GitLab and GCP : GitLab 之第一次寫腳本就上手 - Day 8 -

![Imgur](https://i.imgur.com/5JX4fA3.png)

## 前言

從先前的文章我們知道如果要使用 GitLab CI/CD，需要在專案的根目錄新增一個 `gitlab-ci.yml` 並撰寫腳本，接下來要講的就是該檔案的內容，應當如何設計與編寫。最後，在正式開始前要請各位先自行安裝 [Git](https://git-scm.com/) 才能完成完整的練習。

## 撰寫第一個腳本

1. 首先，先登入 GitLab 後點選新增專案，填寫專案名稱並點擊『Create project』

![Imgur](https://i.imgur.com/qiFnd1j.png)

2. 點選『Clone』後複製網址

![Imgur](https://i.imgur.com/cjxcNu6.png)

3. 在終端機上使用指令`git clone {網址}`，接著 `cd {專案名稱}` 進入專案根目錄，接著就可以開始編寫 `.gtilab-ci.yml`

![Imgur](https://i.imgur.com/XuD4YsM.png)

4. 這是一個簡單的腳本，編寫完成後完成後使用，使用 Git 進行 Push

**腳本範例**

```yaml=
job1:
  script:
    - echo "Hello, GitLab CI !!"
```

![Imgur](https://i.imgur.com/3WwOoJs.png)

5. 最後，就可以在 `GitLab > CI/CD > 管線` 中看到腳本被運行成功或失敗

![Imgur](https://i.imgur.com/12uJiDq.png)

## 進階腳本編寫

### Stage

通常腳本不會這麼簡單，尤其要實現完整的 CI > CD 的完整過程，這時就會利用到腳本中的 `stage`，這可以幫助我們定義每個 job 分布在哪個 stage，不同的 Stage 會依次執行，而存在同一次 Stage 中的 Job 有可能同時執行，示意圖如下：

![Imgur](https://i.imgur.com/twtcZef.png)

需要留意的是不同的 Job 有可能分布在不同的 Executor，所以在沒有利用緩存機制(Cache, Artifacts)的情況下，無論是 Job 或 Stage 之間的資料都是不共通的。

**腳本範例**

```yaml=
stages:
  - build
  - test
  
job1:
  stage: build
  script:
    - echo "Build, ok"

job2:
  stage: test
  script:
    - echo "Test, ok"
```

### Image

在實務上專案在進行建構、測試和佈署時，通常會需要相應的軟體，舉例來說 Java 就需要 JDK。如果每次的建構都要在指令中，撰寫安裝的指令，整體的時間會變得很長，而且腳本會變得難以維護。

這時 Docker 的功效就出來了，使用 `image` 可以指定 Job 在哪個 Docker image 中運行，這樣就變的非常方便，可以選擇相對應的 image 運行。image 屬性可以被設定在最外層，這會使得沒有設定 image 的 Job 預設使用該 image。

**腳本範例**

```yaml=
image: docker:stable

stages:
  - build
  - test
  
job1:
  stage: build
  image: python:3.7
  script:
    - echo "Build, ok"

job2:
  stage: test
  script:
    - echo "Test, ok"
```

**運行結果**

![Imgur](https://i.imgur.com/7rCmPMt.png)


## 結語

通過幾個簡單的練習，是否有覺得 GitLab CI 更加理解了呢？

總結幾個重點：

1. 編寫 .gitlab-ci.yml
2. Push 觸發腳本運行
3. 看 Pipeline 的結果

就是個簡單三步驟

另外一提 GitLab 在管線畫面中有個 CI lint 是個驗證腳本的工具，對於還不熟悉腳本的朋友，可以多試著使用看看會有所幫助。

## Reference

* [Creating and Tweaking GitLab CI/CD for GitLab Pages | GitLab](https://docs.gitlab.com/ee/user/project/pages/getting_started_part_four.html)