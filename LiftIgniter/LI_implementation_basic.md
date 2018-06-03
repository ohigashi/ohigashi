# LiftIgniterの実装

LiftIgniterの実装には、大きく分けて下記3つの実装が必要です。なお、このドキュメントではJavascriptのみを使用した実装を想定しています。この他にAPIを使用した実装もできます。

* Webサイトのデータ収集用のビーコンスクリプト  
* リコメンデーションを取得して表示するためのスクリプト  
* リコメンデーションを表示するエリアのHTMLテンプレート

A/Bテストを実施する場合には、このドキュメントの下部に記したA/Bテスト用のスクリプトを実装してください。

---

## ビーコンでWebサイトのデータを収集する

LIを実装するサイト内のすべてのページにビーコンのコードを貼ります。通常のコンテンツ記事だけではなく、動画、商品ページなどにも貼れます。このコードを貼ったページに訪問があると、スクリプトがそのページに関連するデータを収集します。収集されるデータの内容は、大きく分けて下記の2つです。

* 訪問したページの情報（metdataその他）  
* ユーザの行動（訪問したページ、エンゲージメントデータ、表示されたリコメンデーションの内容など）  

これらの情報を収集すると同時に、LIからリコメンデーションアイテムが提供されます。なお、デフォルトでは収集されたデータは最後の訪問から30日を経過すると無効になります。

このビーコンが読み込まれると同時にLIのJS SDKを呼び出して、$p関数を使えるようにします。同時に、トラッキング目的でユニークユーザを特定するためのファーストパーティーCookieを読み込ませます。

ビーコンは`<head>`か`<body>`の中で、リコメンデーションの表示部より前に置きます。  

### ビーコンのコード  

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

実際にデータの収集が開始されると、ネットワークログに`_inventory.gif`が表れます。

ビーコンを貼ったページをリコメンデーションの対象にしたくない場合は、下記のコードを`<head>`部内に追加してください。

```
<script id="liftigniter-metadata" type="application/json">
{
  "noShow" : "true",
}
</script>
```

### ビーコンいろいろ（オプション）

ビーコンがページから収集するmetadataは下記の2種類です。LI用のJSONとOpenGraphタグで重複がある場合は、基本的にLI用のJSONの値が優先されます。LI用のJSONを使用して様々なmetadataを追加することができます。

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

### Canonical URLの指定（オプション）

パラメータが追加されたURLなど、URLを変動させることがある場合は、`$p("init")`に`config: customConfig`を追加して、その前の部分に下記の通りコードを追加することで、各ページのCanonical URLをURLとして収集することができます。

```
var customConfig = {
  activity: {
    canonicalUrlTransform: function(url){return url}
  }
}

$p("init", {JAVASCRIPT_KEY}, {config: customConfig}); // REPLACE JAVASCRIPT_KEY
```

### タイムスタンプ（オプション）

タイムスタンプを使用してルールの適用を行う場合は、`article:published_time`のフィールドを追加してください。`article:modified_time`や、`article:expiration_time`もルールの中で使用することができます。ここでのタイムスタンプのフォーマットは、[ISO 8601](https://www.iso.org/iso-8601-date-and-time-format.html)に準ずる形式で指定してください。これらのフィールド名は、`article:`の部分を削除して収集されます。

### カスタムイベントの送信（オプション）

カスタムのイベント（エンゲージメント、コンバージョンなど）を送りたい場合は、下記のように`$p("send")`関数を使ってイベントのデータを送ってください。

```
$p("send", "engaged",{
  FIELD1: "VALUE1",
  FIELD2: "VALUE2"
})

$p('send', 'pageview', {
  u_gender: "male",
  u_type: "subscribed"
})

$p('send', 'conversion', {
  u_gender: "male",
  u_type: "subscribed"
})
```

### ユーザIDの送信（オプション）

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

<!--ウィジェット表示のテンプレート-->
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

### リコメンデーションアイテムのフィルタリング（オプション）

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

### 同一ページで複数のウィジェットのリコメンデーションのリクエストを送る（オプション）

`$p("register")`は、`$p("fetch")`の前に複数回呼ぶことができます。それぞれの`$p("register")`に対して、個別の`opts`のパラメータを指定することができます。

```
// Requests for articles that were published within last 24 hours
$p("register",{
  max:5,
  widget: "new_news", // ここに個別のウィジェット名を入れます
  opts: {maxAgeInSeconds: 60*60*24},
  callback: function(resp){
    console.log(resp)
  }
})

// Requests for articles that's marked as news.
$p("register",{
  max:5,
  widget: "news_rec", // ここに個別のウィジェット名を入れます
  opts: {channel: "news"},
  callback: function(resp){
    console.log(resp)
  }
})

// Calling fetch once executes both registers.
// Results in two different kinds of recommendations.
$p("fetch")
```

### 取得するフィールド名を指定する（オプション）

リコメンデーションアイテムと一緒に返されるフィールドを指定する。
```
$p("setRequestFields",["name","title","url"])
```

指定したすべてのフィールドに値を持つアイテムだけを取得する。
```
$p("setRequestFieldsAON",true)
```

アイテムの必須フィールドを指定する。
```
$p("setMandatoryRequestFields",["title","url"])
```

収集するフィールド名の追加は、上に記した`<script id="liftigniter-metadata" type="application/json">`を使って各ページのHTMLにLI用のJSONとしてデータを作成してください。

### トレンドしているアイテムを取得する（オプション）

`opts`のパラメータを指定してトレンドしているアイテムを取得することができます。

`rankingKey`では、指定した値のランキングの降順でアイテムを取得します。ここで指定できる値は、"click", "visible", "show", "click:visible", "click:show", "visible:show"です。ここでの":"は、各値の比率を表します。

`lookupKey`では、"popular"または"click"を指定できます。それぞれ、閲覧数、クリック数の多いアイテムを取得します。 

`hoursBehind`で指定した時間内での、`rankingKey`および`lookupKey`で指定した値の結果を取得します。

例えば、24時間以内にクリック数が多かったアイテムのランキングを取得する場合は下記のように値を設定します。

```
  opts: {
    "rankingKey": "click",
    "hoursBehind": 24
  },
```

---

## A/Bテスト用のウィジェット

LIのリコメンデーションをA/Bテストしながら表示するエリアのHTMLサンプルです。通常のLIの実装と違ってここではmustache.jsを使います。mustache.jsについて詳しく知りたい場合は[こちらの記事を参照してください](http://coenraets.org/blog/2011/12/tutorial-html-templates-with-mustache-js/)。

```
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

<!--ウィジェット表示のテンプレート-->
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
