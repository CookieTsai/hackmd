---
title: Day 5 - GitLab 前世今生
tags: 鐵人賽
description: |
  整理一年來的實務經驗並在三十天的自我挑戰中，找出 GitLab 與 GCP 眾多服務的一個最佳實踐方式
slideOptions:
  ## choose from none, fade, slide, convex, concave and zoom.
  transition: zoom
  ## https://github.com/hakimel/reveal.js/tree/master/css/theme
  theme: league
---

# Best Practice for DevOps on GitLab and GCP : GitLab 前世今生 - Day 5 -

![image alt](https://i.imgur.com/2Cx8yGg.png)

> GitLab 是一個基於網頁的 DevOps 生命週期工具。最初是由 Ruby on Rails 編寫而成的開源應用軟體，後來有部分使用 Go 重構，實現可自托管控的 Git 專案存放管理功能，能夠瀏覽、管控、註解與共同開發程式碼。

## 歷史演進

![Imgur](https://i.imgur.com/AQZbZBW.png)

* 最初 - 命名為 GitLab 是一種完全免費的軟體
* 2013年 7月 - 拆分為 GitLab CE 和 GitLab EE
* 2015年 1月 - GitLab 發佈了用 Go 寫的 GitLab Runner 作為持續整合工具
* 2018年 4月 - GitLab 宣布與 GKE 整合簡化佈署流程
* 2018年 6月 - GitLab 宣布 Auto DevOps in GitLab 11.0

通過歷史可以了解到一個軟體的發展，從而更加認識一個軟體該如何切入。現在的 GitLab 分成開源版本的 GitLab CE 和商用版本的 GitLab EE，在一個完整 GitLab 中會需要安裝 GitLab Runner 作為 GitLab CI/CD 的一部分。

當前 GitLab 整朝向專案無須設定即可自動建置、測試、佈署及監控的方向。由此可見自 GitLab 上路後以有數年時間，在 CI/CD 這條路上越走越遠。

## Reference

* [GitLab 8.0 released with new looks and integrated CI!](https://about.gitlab.com/2015/09/22/gitlab-8-0-released/)
* [敏捷開發與Gitlab CI/CD持續集成](https://kknews.cc/code/bj8plym.html)
* [GitLab wikipedia](https://en.wikipedia.org/wiki/GitLab)
* [Auto DevOps | GitLab](https://docs.gitlab.com/ee/topics/autodevops/)
