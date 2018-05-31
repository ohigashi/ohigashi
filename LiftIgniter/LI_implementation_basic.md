# LiftIgniterの実装

LiftIgniterの実装には、Webサイトのデータ収集用のビーコンスクリプト、リコメンデーションを取得して表示するためのスクリプトと、リコメンデーションを表示するエリアのHTMLテンプレートの実装が必要です。また、A/Bテストを実施する場合にはリコメンデーションの表示領域のアイテムを書き換えるためのスクリプトも追加して実装してください。

---

## ビーコンでWebサイトのデータを収集する

サイト内のすべてのページにこのビーコンのコードを貼ります。通常のコンテンツ記事、動画、商品ページなどどこにでも貼れます。このコードを貼ったページに訪問があると、データを自動的に収集します。

このビーコンがLIのJS SDKを呼び出して、$pを使えるようにする。同時に、トラッキング目的でユニークユーザを特定するためのファーストパーティーCookieを読み込みます。

```
if (typeof $igniter_var === 'undefined') {
  // クライアントコードをアップデート
  (function(w, d, s, p, v, e, r) {
    w.$ps = (w.performance && w.performance.now && typeof(w.performance.now) == "function") ? w.performance.now() : undefined;
    w['$igniter_var'] = v;
    w[v] = w[v] || function() {
      (w[v].q = w[v].q || []).push(
        arguments)
    };
    w[v].l = 1 * new Date();
    e = d.createElement(s), r = d.getElementsByTagName(s)[0];
    e.async = 1;
    e.src = p + '?ts=' + (+new Date() / 3600000 | 0);
    r.parentNode.insertBefore(e, r)
  })(window, document, 'script', '//cdn.petametrics.com/JS_KEY.js', '$p');
  // "JS_KEY"の部分は、自身のアカウントのJS_KEYに差し替えます。$p("init")関数の値も変更してください。

  $p("init", "JS_KEY"); // JS_KEYを差し替える
  $p("send", "pageview");
}
```

`//cdn.petametrics.com/`の後と、`$p("init")` 関数のJS_KEYを必ず差し替えてください。

実際にデータが収集されているか確かめる場合は、ネットワークログに`_inventory.gif`があるか確認してください。

### ビーコンいろいろ

ビーコンが収集するmetadataは下記のとおりです。LI用のJSONとOpenGraphタグで重複がある場合は、基本的にLI用のJSONの値が優先されます。

* LI用のJSON
```
<script id="liftigniter-metadata" type="application/json">
{ "tags" : ["apple", "nutrition"]}
</script>
```
* OpenGraphタグ
```
<meta property="og:title" content="sample-title"/>
```

データの収集は、ページに訪問があってこのビーコンが呼び出されてはじめて行われます。デフォルトでは、収集されたデータは最後に訪問があってから30日を経過すると無効になります。

ビーコンを貼ったページをリコメンデーションの対象にしたくない場合は、下記のコードを`<head>`部内に追加してください。

```
<script id="liftigniter-metadata" type="application/json">
{
  "noShow" : "true",
}
</script>
```

タイムスタンプを使用してルールの適用を行う場合は、`article:published_time`のフィールドを追加してください。`article:modified_time`や、`article:expiration_time`もルールの中で使用することができます。ここでのタイムスタンプのフォーマットは、[ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html)に準ずる形式で指定してください。

ページ閲覧ごとにユーザIDを追加して送りたい場合は、下記の通り`$p("init")`と`$("send") `の間に`$p("setUserId")`を追加してください。異なるデバイス間のユーザの行動をトラックすることができます。

```
$p("init", "JS_KEY");
$p("setUserId", USER_ID); // USER_ID should be a string.
$p("send", "pageview");
```

---

## リコメンデーションを表示するウィジェット

LIのリコメンデーションを表示するエリアのHTMLサンプルです。A/Bテストを行う場合は、下記のA/Bテスト用ウィジェットを参照してください。

各ウィジェットには`div id`を指定してください。

`{{}}`のテンプレート内のパラメータは、インベントリのmetadataのパラメータと一致するようにしてください。

```
<h1>LiftIgniter Recommendations</h1>

<!--リコメンデーションの表示エリア-->
<div id="li-recommendation-unit">
  <div class='recommended_item li-widget-item'>
    <a class='headline' href='//url-1'>Title 1</a>
  </div>

  <div class='recommended_item li-widget-item'>
    <a class='headline' href='//url-2'>Title 1</a>
  </div>

  <div class='recommended_item li-widget-item'>
    <a class='headline' href='//url-3'>Title 3</a>
  </div>

  <div class='recommended_item li-widget-item'>
    <a class='headline' href='//url-4'>Title 4</a>
  </div>
</div>

<script type="application/mustache" id="li-recommendation-template"">
  <div class='recommended_item'>
    <a href='{{url}}'>
      <div>{{title}}</div>
      <div><img src="{{thumbnail}}"></div>
    </a>
  </div>
</script>
```

---

## リコメンデーションを取得するスクリプト

実際にリコメンデーションアイテムを呼び出して表示します。

```
//-- 1. リコメンデーションの表示領域を定義する --
//$p('render') は、HTMLで指定されたテンプレートで、ターゲットのエリアをLIのリコメンデーションアイテムで上書きします。 
var rendering_callback = function(resp) {
  var els = document.querySelectorAll('#li-recommendation-unit > div.li-widget-item');
  var template = document.querySelector('#li-recommendation-template"').innerHTML;

  for (var i = 0; i < els.length && i < resp.items.length; ++i) {
    // Basically Mustache.render(template, resp.items[i]); 
    els[i].innerHTML = $p('render', template, resp.items[i]);
  }
}

//-- 2. トラックするアイテムを定義する --
//$p('track') は、イベントリスナーと、クエリ文字列のパラメータをリコメンデーションエリアの各URLにアタッチします。
var trackAlgo = function(algorithm) {
  $p('track', {
    elements: document.querySelectorAll('#li-recommendation-unit > div.li-widget-item'),
    //ここで指定する"name"の値は、$p("register") 関数の"widget"の値と一致するようにしてください。ここが一致していると、ウィジェットへのリコメンデーションリクエストをダッシュボード上のウィジェットアクティビティに紐付けることができます。
    name: 'default-widget',
    source: algorithm
  });
}

//-- 3. リコメンデーションのリクエスト --
$p('register', {
  max: 4, // 表示するアイテムの数
  widget: 'default-widget',
  callback: function(resp) {
    // リコメンデーションが返されたら、renderingを一度呼び出す。
    rendering_callback(resp);
    // リコメンデーションエリアのアイテムを”LI"のソース名でトラックする。
    trackAlgo('LI');
  }
});

$p('fetch');
```

### リコメンデーションいろいろ

ここでアイテムのフィルタリングを行いたい場合は、ウィジェット単位で、下記のように`opts:`で条件を指定して、アイテムのフィルタリングを行うことができます。

24時間（86400秒）以内にPublishされたアイテムだけをリクエストする。
```
$p("register",{
  max:5,
  widget: 'default-widget',
  opts: {maxAgeInSeconds: 86400},
  callback: function(resp){
    console.log(resp)
  }
})
```

`news`のカテゴリのアイテムだけをリクエストする。
```
$p("register",{
  max:5,
  widget: 'default-widget',
  opts: {category: "news"},
  callback: function(resp){
    console.log(resp)
  }
})
```

---

## A/Bテスト用のウィジェット

LIのリコメンデーションをA/Bテストしながら表示するエリアのHTMLサンプルです。

```
<h1>LiftIgniter Recommendations</h1>

<!--リコメンデーションの表示エリア-->
<div id="li-recommendation-unit">
  <div class='recommended_item li-widget-item'>
    <a class='headline' href='//url-1'>Title 1</a>
  </div>

  <div class='recommended_item li-widget-item'>
  <a style="text-align: center;" class='headline' href='//url-2'>Title 1</a>
  </div>

  <div class='recommended_item li-widget-item'>
    <a class='headline' href='//url-3'>Title 3</a>
  </div>

  <div class='recommended_item li-widget-item'>
    <a class='headline' href='//url-4'>Title 4</a>
  </div>
</div>

<!--Widget Display Template-->
<div id="li-recommendation-template"">
  <div class='recommended_item'>
    <a href='{{url}}'>
      <div>{{title}}</div>
      <div><img src="{{thumbnail}}"></div>
    </a>
  </div>
</div>
```

---

## A/Bテスト用のリコメンデーションを取得するスクリプト

実際にリコメンデーションアイテムを呼び出して表示します。ここでは、A/Bテストとして、既にあるリコメンデーションを上書きする場合を想定しています。

```
//-- 1. リコメンデーションの表示領域を定義する --
//$p('render') は、HTMLで指定されたテンプレートで、ターゲットのエリアをLIのリコメンデーションアイテムで上書きします。 
var rendering_callback = function(resp) {
  var els = document.querySelectorAll('#li-recommendation-unit > div.li-widget-item');
  var template = document.querySelector('#li-recommendation-template"').innerHTML;

  for (var i = 0; i < els.length && i < resp.items.length; ++i) {
    // Basically Mustache.render(template, resp.items[i]); 
    els[i].innerHTML = $p('render', template, resp.items[i]);
  }
}

//-- 2. トラックするアイテムを定義する --
//$p('track') は、イベントリスナーと、クエリ文字列のパラメータをリコメンデーションエリアの各URLにアタッチします。
var trackAlgo = function(algorithm) {
  $p('track', {
    elements: document.querySelectorAll('#li-recommendation-unit > div.li-widget-item'),
    //ここで指定する"name"の値は、$p("register") 関数の"widget"の値と一致するようにしてください。ここが一致していると、ウィジェットへのリコメンデーションリクエストをダッシュボード上のウィジェットアクティビティに紐付けることができます。
    name: 'default-widget',
    source: algorithm
  });
}

//-- 3. リコメンデーションのリクエスト --
//-------- ここでA/Bテスト対象とLIのリコメンデーションを制御する ------------
// To switch between showing base and LI recommendations, change the > or < sign in line 51 to the opposite, then Run the fiddle again.
var abTestHandler = function(slice) {
  // Slice is modulo 100 of the cookie hash.
  if (slice < 50) {
    // Register call to overwrite widget being tested with LI recommendations,
    // and track metrics appropriately.
    $p('register', {
      max: 4, // 表示するアイテムの数
      widget: 'default-widget',
      callback: function(resp) {
    // リコメンデーションが返されたら、renderingを一度呼び出す。
        rendering_callback(resp);
    // LIから提供されたリコメンデーションエリアのアイテムを”LI"のソース名としてトラックする。
        trackAlgo('LI');
      }
    });
  } else {
    // A/Bテスト対象のリコメンデーションエリアのアイテムを”base"のソース名としてトラックする。
    trackAlgo('base');
  }
  // $p("register") とすべての引数を実行してリコメンデーションを取得する。
  $p('fetch');
}

// Decide which slice the current user is on.
// $p('userHash') hashes user cookie id to a random 32-bit integer.
// $p('abTestSlice') returns the absolute value of this number mod 100
$p('abTestSlice', {
  callback: abTestHandler
});
```
