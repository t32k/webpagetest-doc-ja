# Speed Index

Speed Indexは表示されたページの見える部分、つまりATF/ファーストビューの平均時間です。それはビューポートサイズに依存し、ミリ秒で表わされます。

Speed Indexの指標がWebPagetestに追加されたのは2012年の4月で、いかに速くページの見える部分がレンダリングされたのか計測します（数値が低いほうが良いです）。これはUXを比較（改善前後、自社サイトと競合サイトなど）するのに大いに役立ちますし、他の指標（読み込み時間、Start Renderなど）と合わせて見るべきでWebパフォーマンスを理解するのに良いでしょう。

## これまでの問題の背景

これまで私達はWebページが速かったのか、遅かったのか決めるためにある種の段階的なタイミングに頼らざるをえませんでした。これのタイミングの中で最も一般的なのは、ブラウザがメインのドキュメントのloadイベントに達するまでの時間、つまりonloadでしょう。loadイベントはラボ環境や実際のネット環境であろうとも計測自体は簡単です。しかし、不幸にもこれは実際のエンドユーザー体験を理解するための良い指標とは言えません。ページのコンテンツが増えていけば、当然、ユーザーには見えない部分やスクリーン外（below the fold）のコンテンツを読み込まなければならず、たとえユーザーに見る部分がはるか昔にレンダリングされていたとしても、読み込み時間は長くなってしまいます。私達は他のより良いタイミング（first paintまでの時間や、DOM content readyの時間など）についても検討してきましたが、結局それらはひとつのポイントでしかなく、実際のエンドユーザーの体験を理解することはできないのです。

## Speed Indexの登場

Speed Indexは読み込んでいるページの見える部分のビジュアル的進捗状況（visual progress）を必要とし、ページのコンテンツがいかに速くレンダリングされたのか総合的なスコアが計算されます。このため、まずページの読み込みでいる間にあるさまざまなタイミングの中で、どの時点で『完了』と見なすのか判断する必要があります。WebPagetestにおいては、ページ読み込みをビデオキャプチャーし、毎フレームの画像をチェックすることにで可能にしています（現在の実装では1秒間に10フレーム記録でし、ビデオキャプチャーが有効になってるテストの時だけ計算されます）。各フレームの進捗率を計算する現在のアルゴリズムは以後で説明しますが、とりあえず今は各フレームの進捗率を%で表しています（各フレームの下にある数字）。

![](https://sites.google.com/a/webpagetest.org/docs/_/rsrc/1334421426436/using-webpagetest/metrics/speed-index/compare_progress.png)

完了になるまでページのvisual completeをプロットしてみると、以下のようなグラフができました。

![](https://sites.google.com/a/webpagetest.org/docs/_/rsrc/1334422231485/using-webpagetest/metrics/speed-index/chart-line-small.png)

この曲線の下の部分の進捗率を数値に変換することが可能です。

![](https://sites.google.com/a/webpagetest.org/docs/_/rsrc/1334422278220/using-webpagetest/metrics/speed-index/chart-progress-a-small.png)

![](https://sites.google.com/a/webpagetest.org/docs/_/rsrc/1334422264852/using-webpagetest/metrics/speed-index/chart-progress-b-small.png)
  
これは非常に素晴らしい指標となりうるかもしれませんが、1点弱点を挙げるとすれば際限がないことです。もし、visually complete後にも10秒間読み込みのスピナーが回っていたとすればこのスコアは増加し続けます。『グラフ曲線の上の部分』を使用し、ページのレンダリングされてない部分を計算することで、ページが100%完了したなら終了ですし、ページが速いほど0に近づく有限なエリアとなります。

![](https://sites.google.com/a/webpagetest.org/docs/_/rsrc/1334422315127/using-webpagetest/metrics/speed-index/chart-index-a-small.png)

![](https://sites.google.com/a/webpagetest.org/docs/_/rsrc/1334422330956/using-webpagetest/metrics/speed-index/chart-index-b-small.png)

![](https://sites.google.com/a/webpagetest.org/docs/_/rsrc/1336574582211/using-webpagetest/metrics/speed-index/speedindexformula.png)

Speed Indexとはミリ秒で計算された『グラフ曲線の上の部分』であり、visually completeには0.0-1.0の範囲で算出される。この計算方法は、0.1秒のインターバルに設定し、次の計算式で計算する。`IntervalScore = Interval * (1.0 - (Completeness/100))`この時の`Completeness`はそのフレームのvisually completeで%が単位です。また、`Interval`はビデオフレームの経過時間(ミリ秒)で、この場合は100msです。総合スコアは個々のインターバルの合計なので、つまり`SUM(IntervalScore)`です。

比較のために、2つのページのビデオフレームが次です（Aが上、Bが下）。

![](https://sites.google.com/a/webpagetest.org/docs/_/rsrc/1334422690700/using-webpagetest/metrics/speed-index/compare_trimmed.png)

## Visual Progressの算出方法

各ビデオフレームの進捗率というのは波打ったような曲線になり、Speed Indexの計算と進捗率を決定する技術とは独立したものとなります（進捗率の計算は異なる計算方法が使用される可能性があります）。私達は2つのメソッドを現在、提供しています。

### ビデオキャプチャーによるVisual Progress

{{ 念校 }}

単純なアプローチとして画像の各ピクセルを最終的なイメージと比較し、各フレームがどれだけマッチしてるのか%を算出します（おそらく最初と最後のフレームのいくつかのマッチしているピクセルは無視されます）。このアプローチの問題としてはリキッドデザインなページや広告の読み込みなどで、コンテンツが移動してしまう可能性があるからです。ピクセル比較モードにおいては、実際のコンテンツが単一のピクセルにシフトダウンしたとしても、スクリーン上のすべてのピクセルの変化を見ます。

私達が決めたこのテクニックはキャプチャーした画像の（赤、緑、青といった具合の）各色のヒストグラムを作成し、全体の色分布を見ているだけでした。最初のビデオフレームから抽出したヒストグラムと最後のビデオフレームから抽出したヒストグラムを差異を計算し、それを差異のベースラインとして使用します。各ビデオフレームのヒストグラムと最初のフレームのヒストグラムとの差異は、ベースラインと比較され、そのビデオフレームがどれだけ完了しているのか決定されます。時折、正確な状態を表していないこともありますが、私達がテストしてきた結果、多くのページで価値のある結果が出されてきたことを証明していますので安心してください。

これはSpeed Indexが作られたときに visual progressを計算する独自のメカニズムで、まだうまく機能しますが、いくつかのケースで問題があります。ケースというのは、動画を再生するページや、スライドショーなどの大きな要素を含むページです。終了の状態と最終イメージを基準としたビジュアルプログレスの計算というのは繊細なものです。また、ラボでしか計測できず、ビデオキャプチャーが有効の場合のみです。

{{ /念校 }}

### ペイントイベントによるVisual Progress

More recently we have (successfully) experimented with using the Paint Events that are exposed by Webkit through the developer tools timeline (which are also available to extensions and through the remote debugging protocol).  
It is available on all recent webkit-based browsers including desktop and mobile and across all platforms.  It is also very lightweight and does not require capturing video.  It is somewhat sensitive to the renderer implementation in a given browser so it is not as useful for comparing performance across different browsers.

In order to get useful data, it requires a fair bit of filtering and weighting.

The specific algorithm we are using to calculate the speed index from the dev tools paint rects is:

+ In the case of Webkit-based browsers, we collect the timeline data which includes paint rects as well as other useful events.
+ We filter out any paint events that occur before the first layout that happens after the first response data is received.
	+ ResourceReceiveResponse -> Layout -> Paint events.
	+ This is done because the browser does several paint events before any data has actually been processed.
+ We group all paint events by the rectangle that they are updating (frame ID, x, y, width, height).
+ We consider the largest paint rectangle painted to be the "full screen" rectangle.
+ Each rectangle contributes a score towards an overall total.  The available points for a given rectangle is that rectangle's area (width x height).
+ Full screen paints (any paint events for the largest rectangle) are counted as 50% so that they still contribute but do not dominate the progress.
+ The overall total is the sum of the points for each rectangle.
+ The points for a given rectangle are divided evenly across all of the paint events that painted that rectangle.
	+ A rectangle with a single paint event will have the full area as it's contribution.
	+ A rectangle with 4 paint events will contribute 25% of it's area for each paint event.
+ The endTime for any given paint event is used for the time of that paint event.
+ The visual progress is calculated by adding each paint event's contribution to a running total, approaching the overall total (100%).



# 参考： Speed Indexの結果

## 5Mbps Cable

HTTP AchiveによればAlexaの上位300,000のテスト結果は以下。

![](https://sites.google.com/a/webpagetest.org/docs/_/rsrc/1369234119985/using-webpagetest/metrics/speed-index/si-cable.png)

## 1.5Mbps DSL

HTTP AchiveによればAlexaの上位100,000のテスト結果は以下。

![](https://sites.google.com/a/webpagetest.org/docs/_/rsrc/1335895409235/using-webpagetest/metrics/speed-index/distribution.png)