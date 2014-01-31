# 結果コード

結果コードの0 (成功) または 99999 (コンテンツエラー) はテストが成功したことを意味し、それ以外のすべての結果はエラーを意味します。

| 結果コード| 説明 |
|------------------|-----------------|
|0            |テスト成功|
|4xx-5xx  |	HTTP 結果コード(ページのエラー)|
|99996    |	Test Failed waiting for specified DOM element/End condition|
|99997    |	テストのタイムアウト (コンテンツエラー無し)|
|99998    |	テストのタイムアウト (コンテンツエラー有り)|
|99999    |	テストは成功したけども個別のリクエストで失敗している（コンテンツエラー）|


Other error codes are Wininet/IE-specific error codes (usually large negative numbers). The best way to identify them is to convert it to hex and search. If you send us new error codes as you find them we will add them to the table.

他のエラーコードはWininet(Win32 インターネット拡張機能)もしくはIE特有のエラーコード（たいていはマイナスの数値）。結果コードを特定する最適な方法はそれを16進数に変換して検索する。もし、新しいエラーコードを見つけたのなら、この表に追加しますので私達に知らせてください。