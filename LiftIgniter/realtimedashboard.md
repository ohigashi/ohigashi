# ダッシュボード > リアルタイムダッシュボードについて

https://console.liftigniter.com/realtime

## 各指標の説明

* ダッシュボードのリアルタイムディスプレイでは、granularity（粒度）と time range（対象期間）を変更して様々な指標のデータを見ることができます。ここで対象機関を長く選択することで表示はより時間を要するようになります。また、ここでのデータは過去91日にさかのぼったデータだけを見ることができます。

* pageview, stuck_10s, stuck_3m は、ページ閲覧数と、10秒間または3分間以上滞在した閲覧の数を示します。

* widget_request は、LiftIgniterのシステムに対して送られたリクエストの数です。

* widget_shown は、リコメンデーションが表示された回数です。

* widget_visible は、リコメンデーションがユーザーのブラウザの視認領域に入った回数です。

* widget_click は、リコメンデーションがクリックされた（右クリックおよび左クリック両方）回数です。

* ctr = widget_click/widget_shown.

* vctr = widget_click/widget_visible.

* visibility = widget_visible/widget_shown.
