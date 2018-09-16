title: 'Professional Codes Reader : 像專業程序員一樣地瀏覽網頁'
tags:
  - Tools
  - Software
  - Extension
  - JavaScript
  - 'C#'
categories:
  - 個人開發
author: Jasonnor
date: 2016-02-03 15:51:00
updated: 2017-06-16 00:00:00
---
{% asset_img browser.jpg %}

## 發想

最近我在實習閒暇之餘，會逛一些科技網站來充實自己，不過身在一個拚勁十足的上市公司，隨時都有人能看到你的螢幕，我這個人臉皮薄，不好意思讓別人看到自己沒在工作，於是想了想，看什麼類型的文章才不會顯得特別突兀？

瞬間想到的答案：**含有程式的文章**。

不管是查資料、Debug、找API，畫面上似乎都會有一行行的程式來顯示這個頁面的專業性，想想如果看到同事在瀏覽Stack Overflow，你應該不會認為這傢伙是在偷懶，反而會同情他又有哪個專案趕不完正努力著──這就是我想要的效果！

Repo：[https://github.com/Jasonnor/Professional-Codes-Reader](https://github.com/Jasonnor/Professional-Codes-Reader)

<!-- more -->

## 實作

要讓文章含有程式碼片段，直覺來想就是：「每隔幾個段落，插入隨機的程式碼」，其中比較麻煩的是如何定義段落。於是我先用C#寫了個簡單的WinForms Application作為模擬，基本流程如下：
1. 輸入文字檔並生成隨機程式陣列
2. 讀取段落（以行為單位），插入程式陣列
3. 輸出專業化HTML（含程式碼）

其中程式碼部分使用了Google的[code-prettify](https://github.com/google/code-prettify)來美化，具體內容可以到[這裡](https://github.com/Jasonnor/Professional-Codes-Reader/tree/master/WinForms-Application)觀看原始碼。

緊接著開始研究怎麼在瀏覽器實現這功能，粗略的看過了Chrome Extension的開發者指南，參考範例並把C#改寫成JavaScript，在瀏覽器端需要考慮的是段落的判斷，一篇文章可能會用	&lt;p&gt;、	&lt;br&gt;甚至	&lt;div&gt;來分段，所以我額外寫了一個Options頁面（套用了[Materialize](https://github.com/dogfalo/materialize/) CSS Framework）來讓使用者根據需求選擇要抓取的元素，畢竟每個網站的格式不太相同。

*此處尚有改進之處，如果有任何好的做法歡迎[聯絡我](mailto:wujason810@gmail.com)或發送[Pull Request](https://github.com/Jasonnor/Professional-Codes-Reader/pulls)！*

## 演示
### Classic Reader使用前
![Before](https://raw.githubusercontent.com/Jasonnor/Professional-Codes-Reader/master/assets/novel-before.png)
### Classic Reader使用後
![After](https://raw.githubusercontent.com/Jasonnor/Professional-Codes-Reader/master/assets/novel-after.png)
### Hacker News使用前
![Before](https://raw.githubusercontent.com/Jasonnor/Professional-Codes-Reader/master/assets/article-before.png)
### Hacker News使用後
![After](https://raw.githubusercontent.com/Jasonnor/Professional-Codes-Reader/master/assets/article-after.png)
### Feedly
![Feedly](https://raw.githubusercontent.com/Jasonnor/Professional-Codes-Reader/master/assets/demo-feedly.gif)

更多Demo可以到[Repo頁面](https://github.com/Jasonnor/Professional-Codes-Reader)觀看。

## 使用說明
![Logo](https://raw.githubusercontent.com/Jasonnor/Professional-Codes-Reader/master/assets/logo440x280.png)
### Chrome Extension版本
  1. 到[這裡](https://chrome.google.com/webstore/detail/professional-codes-reader/bmiklhlglhkagnpamkmdhgpbiolnbgac)安裝擴充功能，然後跳到第六步
  2. 或者也可以到[Releases頁面](https://github.com/Jasonnor/Professional-Codes-Reader/releases)下載最新版.crx檔案
  3. 開啟Chrome的[擴充功能頁面](chrome://extensions/)（可以複製到網址列開啟）
  4. 把第一步下載的crx檔案拖曳到擴充功能畫面中
  5. 詢問視窗選擇「新增擴充功能」
  {% asset_img add-extension.png Add-Extension %}
  6. 此時就能在右上角工具列看到一隻羽毛筆的圖標，代表安裝成功
  7. 點擊圖標就能在當前頁面插入程式碼
  8. 點擊圖標右鍵可以打開選項畫面，進行動作設定

  ![Options](https://raw.githubusercontent.com/Jasonnor/Professional-Codes-Reader/master/assets/options.png)

## Todo-List
  + Winform版本支援讀取PDF功能
  + Winform版本用Mono重寫，實現跨平台應用
  + Extension版本自定義抓取Element功能
  + Extension版本能在右鍵選單使用（可以透過Options關閉）
  + Extension版本點擊第二次按鈕，讓產生的程式碼消失
  + Extension版本自訂每次產生多少段程式碼
  + More ...

## 結語
這個業餘項目做得挺開心的，而且實際使用覺得非常有幫助（喂），也是第一次接觸Chrome Extension開發，個人感覺因為文檔完整，社群活躍度又高，基本上入門門檻很低。目前將代辦事項完成後這個項目會告一個段落，有興趣維護改進的朋友也歡迎提出PR！

##### Update at 2017/6/16
此插件已經正式在Chrome Wedstore上架，可以到[此處](https://chrome.google.com/webstore/detail/professional-codes-reader/bmiklhlglhkagnpamkmdhgpbiolnbgac)進行安裝！