リコメンデーションエリアHTML

[ウィジェット](#ウィジェット)  
[スクリプト](#スクリプト)   
[A/Bテスト用ウィジェット](#A/Bテスト用ウィジェット)  
[A/Bテスト用スクリプト](#A/Bテスト用スクリプト)  

---

## ウィジェット

LIのリコメンデーションを表示するエリアのHTMLサンプルです。A/Bテストを行う場合は、[A/Bテスト用ウィジェット](#A/Bテスト用ウィジェット)  を参照してください。

```
<center><h1>
LiftIgniter<br> Recommendations
</h1></center>


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
<script type="application/mustache" id="recommended-item-template">
  <div class='recommended_item'>
    <a class='headline' href='{{url}}'>
      <div>{{title}}</div>
      <div><img src="{{thumbnail}}"></div>
    </a>
  </div>
</script>
```

---

## スクリプト

## ビーコン

リコメンデーションのアイテムとしてデータを収集するページにこのビーコンのコードを貼ります。通常のコンテンツ記事、動画、商品ページなどどこにでも貼れます。

```
// このビーコンがLIのJS SDKを呼び出して、$pを使えるようにする。同時に、トラッキング目的でユニークユーザを特定するためのファーストパーティーCookieを読み込みます。

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
  })(window, document, 'script', '//cdn.petametrics.com/f7tsbsle43k5irci.js', '$p');
  // "f7tsbsle43k5irci"の部分は、自身のアカウントのJS keyに差し替えます。$p("init")関数の値も変更してください。

  $p("init", "f7tsbsle43k5irci");
}
```

## ウィジェットコード

実際にリコメンデーションアイテムを呼び出して表示します。

```
//-- 1. Define Rendering Area --
//$p('render') は、HTMLで指定されたテンプレートで、ターゲットのエリアをLIのリコメンデーションアイテムで上書きします。 
var rendering_callback = function(resp) {
  var els = document.querySelectorAll('#li-recommendation-unit > div.li-widget-item');
  var template = document.querySelector('#recommended-item-template').innerHTML;

  for (var i = 0; i < els.length && i < resp.items.length; ++i) {
    // Basically Mustache.render(template, resp.items[i]); 
    els[i].innerHTML = $p('render', template, resp.items[i]);
  }
}

//-- 2. Define Items to be Tracked --
//$p('track') は、イベントリスナーと、クエリ文字列のパラメータをリコメンデーションエリアの各URLにアタッチします。
var trackAlgo = function(algorithm) {
  $p('track', {
    elements: document.querySelectorAll('#li-recommendation-unit > div.li-widget-item'),
    //ここで指定する"name"の値は、$p("register") 関数の"widget"の値と一致するようにしてください。ここが一致していると、ウィジェットへのリコメンデーションリクエストをダッシュボード上のウィジェットアクティビティに紐付けることができます。
    name: 'default-widget',
    source: algorithm
  });
}

//-- 3. Request Recommendations --
$p('register', {
  max: 4,
  widget: 'default-widget',
  callback: function(resp) {
    // このcallbackのコードはの中にwrapすることで、ロードの順番の問題を解消することができます。
    // リコメンデーションが返されたら、renderingを一度呼び出す。
    rendering_callback(resp);
    // リコメンデーションエリアのアイテムを”LI"のソース名でトラックする。
    trackAlgo('LI');
  }
});


$p('fetch');
```

---

## A/Bテスト用ウィジェット

LIのリコメンデーションをA/Bテストしながら表示するエリアのHTMLサンプルです。A/Bテストを行わない場合は、[ウィジェット](#ウィジェット)  を参照してください。

```
<center><h1>
LiftIgniter<br> Recommendations
</h1></center>

<!--リコメンデーションの表示エリア-->
<div style="height:100px;width:470px" id="li-recommendation-unit">
  <div style="text-align: center;"  class='recommended_item li-widget-item'>
    <a class='headline' href='//url-1'>Title 1</a>
  </div>

  <div style="text-align: center;"  class='recommended_item li-widget-item'>
  <a style="text-align: center;" class='headline' href='//url-2'>Title 1</a>
  </div>

  <div style="text-align: center;"  class='recommended_item li-widget-item'>
    <a class='headline' href='//url-3'>Title 3</a>
  </div>

  <div class='recommended_item li-widget-item'>
    <a class='headline' href='//url-4'>Title 4</a>
  </div>
</div>

<!--Widget Display Template-->
<div id="recommended-item-template">
  <div style="text-align: center;"  class='recommended_item'>
    <a class='headline' href='{{url}}'>
      <div>{{title}}</div>
      <div><img src="{{thumbnail}}"></div>
    </a>
  </div>
</div>
```

---

## A/Bテスト用スクリプト

## A/Bテスト用ビーコン

リコメンデーションのアイテムとしてデータを収集するページにこのビーコンのコードを貼ります。通常のコンテンツ記事、動画、商品ページなどどこにでも貼れます。

```
// このビーコンがLIのJS SDKを呼び出して、$pを使えるようにする。同時に、トラッキング目的でユニークユーザを特定するためのファーストパーティーCookieを読み込みます。

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
  })(window, document, 'script', '//cdn.petametrics.com/f7tsbsle43k5irci.js', '$p');
  // "f7tsbsle43k5irci"の部分は、自身のアカウントのJS keyに差し替えます。$p("init")関数の値も変更してください。

  $p("init", "f7tsbsle43k5irci");
}
```

## A/Bテスト用ウィジェットコード

実際にリコメンデーションアイテムを呼び出して表示します。ここでは、A/Bテストとして、既にあるリコ、リコメンデーションを上書きする場合を想定しています。

```
//$p('render') は、HTMLで指定されたテンプレートで、ターゲットのエリアをLIのリコメンデーションアイテムで上書きします。 
var rendering_callback = function(resp) {
  var els = document.querySelectorAll('#li-recommendation-unit > div.li-widget-item');
  var template = document.querySelector('#recommended-item-template').innerHTML;

  for (var i = 0; i < els.length && i < resp.items.length; ++i) {
    // Basically Mustache.render(template, resp.items[i]); 
    els[i].innerHTML = $p('render', template, resp.items[i]);
  }
}

//$p('track') は、イベントリスナーと、クエリ文字列のパラメータをリコメンデーションエリアの各URLにアタッチします。
var trackAlgo = function(algorithm) {
  $p('track', {
    elements: document.querySelectorAll('#li-recommendation-unit > div.li-widget-item'),
    //ここで指定する"name"の値は、$p("register") 関数の"widget"の値と一致するようにしてください。ここが一致していると、ウィジェットへのリコメンデーションリクエストをダッシュボード上のウィジェットアクティビティに紐付けることができます。
    name: 'default-widget',
    source: algorithm
  });
}

//-------- SWITCHING BASE AND LI RECOMMENDATIONS IN THIS FIDDLE ------------
// To switch between showing base and LI recommendations, change the > or < sign in line 51 to the opposite, then Run the fiddle again.
var abTestHandler = function(slice) {
  // Slice is modulo 100 of the cookie hash.
  if (slice < 50) {
    // Register call to overwrite widget being tested with LI recommendations,
    // and track metrics appropriately.
    $p('register', {
      max: 4,
      widget: 'default-widget',
      callback: function(resp) {
    // このcallbackのコードはの中にwrapすることで、ロードの順番の問題を解消することができます。
    // リコメンデーションが返されたら、renderingを一度呼び出す。
        rendering_callback(resp);
    // LIから提供されたリコメンデーションエリアのアイテムを”LI"のソース名でトラックする。
        trackAlgo('LI');
      }
    });
  } else {
    // A/Bテスト対象のリコメンデーションエリアのアイテムを”base"のソース名でトラックする。
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
