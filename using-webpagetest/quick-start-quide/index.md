# Quick Start Guide

基本的には、WebPagetestはWebページのパフォーマンス計測と分析に使用される。非常に多くのオプション設定があり、最初はたじろいでしまうかもしれないが、テストを始めるまでのプロセスは極めてシンプルだ。このガイドはテストの送信からテストの結果の解釈までを解説します。

## パフォーマンステストの実行

### ページのURLを入力:

まずはじめにテストしたいページを決める必要がある。多くの場合、調べたいサイトの主要なページから始めている（だからといって、他のページを疎かにするという意味ではない）。テストしたいページさえ決定すれば、あとは[WebPagetest](http://www.webpagetest.org/)のサイトに行き、URLを入力するだけです。

![](https://sites.google.com/a/webpagetest.org/docs/_/rsrc/1297439328282/using-webpagetest/quick-start-quide/url.png)

### ロケーションを選択する:

次にどこからテストを実行するのか決める必要がある。WebPagetestは物理的に世界中にテストマシーンを配置しており、あなたのサイトの訪問者に近いロケーションでテストすべきである。リストからロケーションを選択することが可能で、`Change`ボタンを押せば地図上からロケーションを選択することもできる（テストしたいロケーションのバルーンをクリックして`OK`ボタンを押すだけ）。またバルーン上でカーソルを置けば、ロケーション情報が表示されます。

![](https://sites.google.com/a/webpagetest.org/docs/_/rsrc/1297440420631/using-webpagetest/quick-start-quide/map.png)

### ブラウザを選択する:

最後に、テストに使用するブラウザを決定する。ロケーションが異なれば、そのロケーションでサポートするブラウザも異なるので、もしあなたが求めているブラウザがなければ、違うローケーションで試してみる。`Dulles, VA USA`ロケーションはWebPagetest上で動作するすべてのブラウザをサポートしている（Internet Explorer 6 ~ 9）。今は`dynaTrace`ブラウザは無視してもらってもかまわない。これはより詳細な分析のために使用するためのものである。基本的に最初のテストはIE7を推奨する。なぜならそれは最悪のシナリオケースなので、簡単に問題を見つけることができるからだ。


![](https://sites.google.com/a/webpagetest.org/docs/_/rsrc/1297440788068/using-webpagetest/quick-start-quide/browser.png)


### テストを送信する

すべてのオプション設定が終われば`START TEST`ボタンを押し、リクエストがテストロケーションに送信される。他の人が先にテストをリクエストしていればそれが終わるまであなたのテスト実行までに時間がかかる（あなたよりの先のテストを実行するのに少なくとも1分はかかること、また1分以上かかることも珍しくないということを理解してほしい）。テストが完了すればテスト結果ページが表示されるだろう。


## テスト結果の解釈

テスト結果画面に最初は多少たじろいでしまうかもしれない、なぜなら理解するには非常に多くの情報があるからだ。しかし、最初に見るべきキーとなる情報がある。

### Optimization Grades:

テスト結果ページの上部にはパフォーマンス最適化に重要な項目のグレード一覧が表示されている。これらはすべてのサイトに当てはまるような基本的な最適化だけであり、AやBという指標はテストの精度を表すものでない。

![](https://sites.google.com/a/webpagetest.org/docs/_/rsrc/1297455667929/using-webpagetest/quick-start-quide/grades.png)

#### Keep-alive Enabled:

Each request for a piece of content on the page (image, javascript, css, flash, etc) needs to be made over a connection to the web server.  Setting up new connections can take a lot of time so it is best to re-use connections when you can and keep-alive is the way that is done.  They are enabled by default on most configurations and are part of the HTTP 1.1 standard but there are times when they will be broken (sometimes unintentionally).  Enabling keep-alives is usually just a configuration change on the server and does not require any changes to the page itself and can usually reduce the time to load a page by 40-50%.

ページ上の各コンテンツ（画像、JavaScript、CSS、Flash等）へのリクエストはWebサーバーとの接続しなければならない。新しくコネクション確立するのは非常に時間のかかることなので、keep-aliveを使用しコネクションの再利用するのが最適な方法です。

#### Compress Text:

Just about everything on a page that isn't an image or video is text of some kind (html, javascript, css).  Text compresses really well and HTTP provides a way to transfer the files in compressed form.  Enabling compression for text resources is usually just a server configuration change without requiring any changes to the page itself and can both improve the performance and reduce the costs of serving the content (by reducing the amount of data transmitted).  Since text resources are usually downloaded at the beginning of the page (javascript and css), delivering them faster has a much larger impact on the user experience than excessive bytes on images or other content.
Compress Images:

The image compression check just looks at photo images (JPEG files) and makes sure the quality isn't set too high.  JPEG images can usually be compressed pretty substantially without any noticeable reduction in visual quality.  We use a standard of compressing the images at a quality level of "50" in Photoshop's "Save for Web" mode but generally you should compress them as much as you can before they start to look bad.  It's also not uncommon for other data to be included in photos, particularly if they came from a digital camera (information about the camera, lens, location, even thumbnail images) and all of that should be removed from images before being published to a web page.
Cache Static Content:

Static Content are the pieces of content on your page that don't change frequently (images, javascript, css).  You can configure them so that the user's browser will store them in a cache so if the user comes back to the page (or visits another page that uses the same file) they can just use the copy they already have instead of requesting the file from the web server.  Successfully caching static content in the user's browser can significantly improve the performance of a repeat view visit (up to 80+% depending on the page) and reduces the load on the web servers.  It can sometimes be tricky to implement caching without breaking a page though so don't just enable it blindly (you need to be able to change the file name for any files that you expect to change).
Combine JS/CSS Files:

Improving performance usually means reducing the number of requests for content and one of the easiest (and most significant) ways to do that is to reduce the number of individual css and javascript files that load at the beginning of the page (in the <head> which blocks the page from displaying to the user).  An easy way to achieve this is to just merge all of the javascript code into a single file and the css into a single file so you have one of each (with the css preferably being loaded before the javascript).  This can have a very substantial impact on the user experience by reducing the amount of time before they see something appear on the screen.
Use of CDN:

Each request for a piece of content to the web server has to travel from the user's browser all the way to the server and back.  As you get further and further from the server this can become a significant amount of time (which adds up quickly as there are more requests on the page).  Ultimately the time it takes is limited by the speed of light so there's not much you can do except to move your server closer to the users.  That is exactly what a Content Distribution Network (CDN) does.  They have servers all over the world that are close to users and they can serve a web site's static content from servers close to users.  The only case where it doesn't make sense to use a CDN is if all of the users for a web site are close to the web server already (like for a community web site).
High-level Metrics:

The data table at the top of the results page gives you some high-level information about the page that was loaded:



First View:
The First View row is a test that was done with a browser that had its cache and cookies cleared out and represents what a first-time visitor to the page will experience.
Repeat View:

The Repeat View row is a test that was done immediately after the First View test without clearing out anything.  The browser window is closed after the First View test and then a new browser is launched to do the Repeat View test.  The Repeat View test represents what someone will see if they are coming back to the page some time after visiting it the first time (and is a best-case scenario since the test is re-visiting the page immediately).
Document Complete:

The metrics grouped together under the Document Complete heading are the metrics collected up until the browser considered the page loaded (onLoad event for those familiar with the javascript events).  This usually happens after all of the images content have loaded but may not include content that is triggered by javascript execution.
Fully Loaded:

The metrics grouped together under the Fully Loaded heading are the metrics collected up until there was 2 seconds of no network activity after Document Complete.  This will usually include any activity that is triggered by javascript after the main page loads.
Load Time:

The Load Time is the time from when the user started navigating to the page until the Document Complete event (usually when all of the page content has loaded).
First Byte:

The First Byte time is the time from when the user started navigating to the page until the first bit of the server response arrived.  The bulk of this time is usually referred to the "back-end time" and is the amount of time the server spent building the page for the user.
Start Render:

The Start Render time is the first point in time that something was displayed to the screen.  Before this point in time the user was staring at a blank page.  This does not necessarily mean the user saw the page content, it could just be something as simple as a background color but it is the first indication of something happening for the user.
DOM Elements:

The DOM Elements metric is the count of the DOM elements on the tested page as measured at the end of the test.


Requests:

This is the number of requests that had to be made by the browser for pieces of content on the page (images, javascript, css, etc).
Bytes In:

This is the amount of data that the browser had to download in order to load the page.  It is also commonly referred to as the "Page Size".