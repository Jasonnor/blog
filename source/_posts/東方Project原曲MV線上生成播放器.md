title: 東方Project原曲MV線上生成播放器
author: Jasonnor
tags:
  - JavaScript
categories:
  - 個人開發
date: 2018-09-09 20:17:00
---
![preview](https://github.com/Jasonnor/th-music-video-generator/raw/master/images/demo/main.gif)

項目地址：https://github.com/Jasonnor/th-music-video-generator

連結：https://jasonnor.github.io/th-music-video-generator/

## 動機

最近想做個東方原曲個人向Top50，編排MV過程中，覺得部分元素可以自動化產生（例如進場配圖、遊戲畫面皆能爬蟲取得），而且比較少有同時包含配圖和彈幕畫面的原曲MV（個人認為彈幕也是欣賞東方原曲不可缺少的要素之一），於是萌生了這個項目的概念。

## 實作

基本想法是曲子開始後，先顯示兩張隨機圖片，每張各6秒，之後無縫接上遊戲對應的關卡影片，中間皆以淡入淡出來轉場。事前我先建立了簡單的資料庫，包含了曲名、對應角色（標簽）和關卡等，根據標簽從pixiv爬好並篩選了近兩千張高收藏數的作品，使用Google Data API來爬Youtube上的遊戲影片ID，之後逐漸完善播放器和生成邏輯。目前成品Demo（加速版）如上圖。

標題下面會標明該曲所屬的系列以及當前顯示作品的Pixiv ID或Youtube ID，另外本項目支援行動裝置使用，下圖為平板環境：

![image01](https://github.com/Jasonnor/th-music-video-generator/raw/master/images/demo/image01.png)

桌面環境：

![image02](https://github.com/Jasonnor/th-music-video-generator/raw/master/images/demo/image02.png)

移動環境為了降低運算資源，會把音頻波型動畫改為靜態波型（未來會改為可設定）：

![image-mobile01](https://github.com/Jasonnor/th-music-video-generator/raw/master/images/demo/image-mobile01.png)
![image-mobile02](https://github.com/Jasonnor/th-music-video-generator/raw/master/images/demo/image-mobile02.png)

遊戲剪影的播放清單（未來會增加收藏功能，顯示已收藏曲在上方）：

![Menu](https://github.com/Jasonnor/th-music-video-generator/raw/master/images/demo/menu.png)

MV中遊戲彈幕展示：

![Video](https://github.com/Jasonnor/th-music-video-generator/raw/master/images/demo/video.gif)

## 結語

本項目[開源於Github](https://github.com/Jasonnor/th-music-video-generator)，未來也將繼續擴充更新，喜歡的話給個Star我會很開心 😄

使用中遇到問題也歡迎在Github提出issues或PR協助這個項目，已知Bug清單在這裡可以查看：
https://github.com/Jasonnor/th-music-video-generator/issues/5