# 指標

## Page-level Metrics

上位レベルのテスト指標であり、全てのテスト結果ページで表示される。

### Load Time

Load Timeはイニシャルナビゲーション接続からonloadイベントの発火直前までの時間。

### Fully Loaded

イニシャルナビゲーション接続からDocument Complete後2秒間ネットワークアクティビティが何もない状態までの時間。

### First Byte

TTFBと呼ばれるFirst Byteはイニシャルナビゲーション接続からブラウザがHTMLの最初のバイトを受信するまでの時間（リダイレクト後）。

### Start Render

Start Renderはイニシャルナビゲーション接続からブラウザに白くないコンテンツが表示されるまでの時間。

### Speed Index

The Speed Index is a calculated metric that represents how quickly the page rendered the user-visible content (lower is better).  More information on how it is calculated is available here: Speed Index

Speed Indexはユーザーが確認できるコンテンツがいかに速くレンダリングされたか表す指標（低い方が良い）。どのように計算されるのか詳細はこちらを参照：[Speed Index](/using-webpagetest/metrics/speed-index/index.md)

### DOM Elements

DOM Elementsはテストするページで計測までにカウントされるDOM要素の個数。

## Request-level Metrics

個々のリクエスト毎に計測され表示される指標。