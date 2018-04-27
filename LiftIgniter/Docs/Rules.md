## 特定のアイテムやコンテンツの種類に対してブーストを行うことはできますか？

LiftIgniterのアルゴリズムは、ユーザに対して最適なコンテンツを提供することを目的に作られています。しかしながら、ビジネスや編集の方針によっては特定のコンテンツをユーザに対して提供する必要が有ることもあります。そういったニーズに対しても、LiftIgniterではいくつかの方法を用意しています。

なぜアイテムをブーストするのか？

ブーストの方法は、そのアイテムをブーストする理由によって最適な方法を考える必要があります。

* 新しく投稿したコンテンツをハイライトしたい

* 特定のカテゴリのアイテムにもっと視聴者を集めたい（新製品、新しい記事のシリーズなど）

* 収益性の高いページをより頻繁に見せたい

### ブーストの方法

### 1. 個別のアイテムをブーストする
これは、特定のアイテムのインベントリメタデータにフラグを付加することでブーストを行う方法で、以下の値をLiftIgniterのJSONオブジェクトに追加します。

```
"value" : "high"
```

### 2. 最近投稿されたアイテムをブーストする
これは、レコメンデーションアイテムの有効期限を設定する方法です（詳しくは、このドキュメントの「最近投稿された記事やアイテムのみを表示する方法」を参照してください）。特定の期間を過ぎたアイテムはリコメンデーションに表示されなくなります。

さらに、投稿から時間が経つことでブーストを弱める方法もあります（詳しくは、このドキュメントの「最近投稿されたアイテムをブーストして時間経過とともに減衰させる方法」を参照してください）。この方法では、最近投稿されたドキュメントをブーストして、以降徐々にブーストを弱め、特定の時間画を経過するとブーストの効果がなくなり、通常のリコメンデーションと同じように扱われるようになります。

### 3. 収益性の高いページをブーストする
注意事項: この方法はAPIを使ってインベントリアイテムを送る必要があります。Javascriptでは、ページのDOMをスクレーピングしてページの値を取得する必要があります。

LiftIgniterのモデルサーバは、ページの経済的価値を学習して、CTRとコンバージョンをミックスして最適化を行います。それぞれのアイテムのメタデータに値を含めることで、お客様のアカウントのアルゴリズムに調整を加え、新たな情報を処理に含めるようにします。この実装を行いたい場合は、サポートにお問合わせください。

### 4. その他のカスタムルール
この他にカスタムのルールを適用してブーストを行いたい場合は、サポートにお問合わせください。


## LiftIgniterではどのようなルールを適用することができますか？

LiftIgniterでは、フィルタリングとブーストの2つの方法でリコメンデーションで返すアイテムの調整を行うことができます。これらは、リコメンデーション全体、またはウィジェットベースで設定することができます。

* グローバルフィルタリング: 特定の条件を満たすアイテム（特定のタグ、特定のスコア数値など）を常にリコメンデーションに含めない

* オプションのパラメータをもとにフィルタリング: SDKのクエリで渡すオプションのパラメータに特定の値を含むアイテムをリコメンデーションに含めない

* 全体のブースト: 特定の条件を満たすアイテム（特定のタグ、カテゴリ、タイプなど）をブーストする

* オプションのパラメータをもとにフィルタリング: 特定のパラメータを渡す場合のみブーストする

デフォルトでのアイテム除外ルールとして、LiftIgniterのJSONオブジェクトで、"noShow"のフィールドに"true"の値を追加する方法があります。これを行うことでアイテムはインベントリに追加されますが、リコメンデーションには表示されなくなります。

ルールの適用はLiftIgniter側で手動で追加をする必要があり、これには数日を要します。これらのルールを設定するためのWebインターフェースは、現在準備中です。


## 同じウィジェット上でフィルタリングと非フィルタリングのリコメンデーションの比較テストを行うことはできますか？

可能です。このテストを行いたい場合はサポートにご連絡ください。お客様のアカウントにこのようなテストの開始日と終了日の注釈を追記して、データの比較時に参照できるようにします。

### ソース

通常では、LiftIgniter標準のソース名、"base"および"LI"を使用します。 アナリティクスのグラフでは、"Base"はオレンジのラインで記され、"LI"は青色のラインで記されます。テスト実施時には、LiftIgniterのリコメンデーションはすべてのスライスにおいて使用され、この他にカスタムのソース名を使用することもでき、この場合はアナリティクスのグラフでは、紫色のラインで記されます。

一般的には、コントロールスライスに"base"を使用し、変異データに対してはカスタムのソース名を使用することを推奨します。これを行うことで、オレンジと紫色のラインでパフォーマンスの差異を比較しやすくなります。

### コードサンプル

このサンプルでは、”base”は非フィルタリング、変異データには、過去10日間に投稿されたアイテムのみにフィルタリングを行います。（opts: {maxAgeInSeconds: 864000}を使用し、投稿日時のフィールドをもたせることが必要です）

Js

```
// The template for displaying the recommended items.
<script type="application/mustache" id="recommended-item-template">
  <div class='recommended_item'>
    <a class='headline' href='{{url}}'>{{title}}</a>
  </div>
</script>

// Renders the recommendations and overwrites innerHTML of area marked with 'li-widget-item' according to the template above.
var rendering_callback = function(resp) {
    var els = document.querySelectorAll('#li-recommendation-unit > div.li-widget-item');
    var template = document.querySelector('#recommended-item-template').innerHTML;
  
    for (var i = 0; i < els.length &amp;amp;amp;&amp;amp;amp; i < resp.items.length; ++i) {
        // Basically Mustache.render(template, resp.items[i]); 
        els[i].innerHTML = $p('render', template, resp.items[i]);
    }
}

// Tracks each 'li-widget-item' by adding event listener and query string tags to each URL.
var trackAlgo = function(algorithm) {
    $p('track', {
    // marked for replacement during A/B test.
      elements: document.querySelectorAll('#li-recommendation-unit > div.li-widget-item'),
      name: 'my-widget-name',
      source: algorithm,
      _debug: true
    });
}

var abTestHandler = function(slice) {
    // Slice is modulo 100 of the hash.
    // So you can set slice to 0 and 100 to check LI and base slice respectively
    if (slice < 50) {
        // Register call to overwrite widget being tested with LI recommendations,
        // and track metrics appropriately.
        $p('register', {
            max: 4,
            widget: 'my-widget-name',
            callback: function(resp) {
                // You might wish to wrap the code in this callback inside jQuery, to handle load order issues
                //render the recommendations
                rendering_callback(resp);
                // track the recommendation area as the control or 'base' slice.
                trackAlgo('base');
            }
        });
    } else {
        // You might wish to wrap the code in this callback inside jQuery, to handle load order issues
        // track marked areas as the variant name.
        $p('register', {
            max: 4,
            widget: 'my-widget-name',
            opts: {maxAgeInSeconds: 864000},
            callback: function(resp) {
                // You might wish to wrap the code in this callback inside jQuery, to handle load order issues
                //render the recommendations
                rendering_callback(resp);
                // track the recommendation area as the variant slice with a custom source name.
                trackAlgo('filtered');
    }
    // Executes all registered calls.
    $p('fetch');
}

// Decide which slice the current user is on.
// $p('userHash') hashes user cookie id to a random 32-bit integer.
// $p('abTestSlice') returns the absolute value of this number mod 100
$p('abTestSlice', {
                 callback: abTestHandler
               }
);
```

## 最近投稿された記事やアイテムのみを表示する方法

リコメンデーションアイテムの有効期限を設定することができます。この機能は、インベントリアイテムのメタデータに"published_time"のフィールドを追加している場合のみ有効です。このフィールドをインベントリアイテムのメタデータに追加していれば、下記のフィルタリングの機能を使うことができます。 

この有効期限のフィルタリングを実装することで、強い関連性のあるコンテンツでも、せて治された期間をすぎると表示がブロックされ、CTRも低下します。

### 有効期限を設定する

それぞれのウィジェットのコードの"opts"パラメータに、"maxAgeInSeconds"のフィールドを使って有効期限を秒数で指定する必要があります。

例: 

Js
```
$p('register', {
                  max: 10, //the number of items to return
                  widget: 'default-widget', // name of widget
                  opts: {maxAgeInSeconds: 86400},
                  callback: function(resp) {
                    // You might wish to wrap the code in this callback inside jQuery, to handle load order issues
                //render the recommendations
                rendering_callback(resp);
                // track items in the recommendation area
                trackAlgo('LI');
               }
);
```

ここでは、published_timeの値を参照して、過去一日の間（86,400秒）の間に投稿されたアイテムだけを返します。

### 有効期限をどのように使用するべきか？

有効期限にはどのような値でも使用することができます。しかしながら、新しいコンテンツの格付けと、コンテンツの平均寿命を加味して、有効期間の設定の検討を行うことを推奨します。また、LiftIgniterでは自動的に新しく投稿された記事をより重要視する（この場合も"published_time"のフィールド値が追加されていることが必要です）傾向にあり、古いコンテンツより新しい記事のほうが比較的リコメンデーションが強くなります。

### 新しいコンテンツの格付け

経験則的には、1,000件以上の記事やアイテムがサイトに投稿されていることで、フィルタリングやルールの機能を活用するために十分なアイテムの数が満たされていると考えられます。日に20件の新しい記事を投稿していれば、有効期限60日程度に設定することで、質の高いリコメンデーションを維持することができます。

### コンテンツの平均寿命

お客様のコンテンツが常に新鮮である場合は、コンテンツの時間制限を設けることは必要ない場合もあります。通常のコンテンツより寿命の短いアイテムをブロックするためには、"noShow"のタグを追加することを推奨しています。例えば、1月の公開予定の映画に関連する記事を1月以降にリコメンデーションに表示させなくするためには、有効期限を設定するのではなく、"noShow"のタグを使ってリコメンデーション対象から除外します。

時間経過に大きく影響するニュースサイトなどのコンテンツについては、一日などの短い有効期限を設定してコンテンツをブロックします。

## 有効期限を使用する

有効期限の設定は、すべてのウィジェットに使用するのではなく、特定のリコメンデーションエリアに限定して使用することもできます。複数のウィジェット間では、リコメンデーションの重複削除を行います。

### 例:

「オススメのアイテム」と「最近の投稿」がある場合、両方のエリアにおいてリコメンデーションを使用することができます。この場合、「おすすめのアイテム」にはフィルタリングを行わず、「最近の投稿」においては、過去数日間のアイテムに限定します。

ニュースサイトでは、「緊急ニュース」のセクションにおいて過去8時間に投稿されたアイテムに限定します。

Eコマースのサイトでは、「新しいアイテム」のセクションで、過去数日間に追加されたアイテムに限定します。（アイテムにおいても"published_time"のフィールドをアイテムが追加された日を設定して使用します）

## どうやって特定のタイプまたはカテゴリの記事をリコメンドできますか？

インベントリアイテムのいずれのフィールドもリコメンドのフィルタリングに使用できます。

### 例: 

* type（タイプ） - article, video, gallery, review, product, など

* category（カテゴリ） - sweaters, pants, jackets, hats, など

* keywords（キーワード） - river, ecology, water, frogs, など

フィルタリングは、文字列（ひとつの値のフィールド）または配列（キーワードなどの、複数の値を持つフィールド）をもとに設定することができます。これらは、リコメンデーションのリクエストにパラメータとしてフィールド名を含めることでウィジェット位単位で設定ができ、柔軟にフィルタリングのオン・オフの切替が行なえ、動的に値を入れることでフィルタに使用することもできます。また、ウィジェット単位ではなくアカウント全体に対してフィルタリングを行うことも可能です。

### 新しいフィルタを設定する

1. インベントリのどのフィールドをフィルタに使用するかサポートに連絡してください

2. リクエストを受けてから数日以内に、LiftIgniter側でそのフィールドに対するフィルタを設定します

3. フィルタが有効になった後は、ウィジェットリクエストにフィールド名と値を含めることができます

使用しているフィールドが配列のフィールドである場合は、「いずれかの値が合致する」、または「すべての値が合致する」ケースでルールを適用することができます。 「いずれかの値が合致する」ケースでフィルタリングを行うと、より多いアイテムの中からリコメンデーションを選択することができるため、リコメンデーションの選択肢となるアイテムが少なくなりすぎることを防ぎます。

### いずれかの値が合致するケース:

リクエストキーワード : "river", "water", "frogs"

リコメンドされたアイテムは、 "beach", "water", "sunset" または "animals", "lizards", "frogs"のようなキーワードを持っている場合にリコメンドの対象に含まれます。

### すべての値が合致するケース:

リクエストキーワード : ["river", "water", "frogs"]

リコメンドされたアイテムは、3つのキーワードすべてを持っている必要があります。

### フィルタを使う (JS)

Javascriptでの組み込みを行ってリコメンデーションのリクエストを行う場合は、下記のように$p("register") 関数の"opts"のパラメータにルールを含めます。

Js
```
$p('register', {
            max: 4,
            widget: 'my-widget-name',
            opts: {"type": "video"},
            callback: function(resp) {
                // You might wish to wrap the code in this callback inside jQuery, to handle load order issues
                //render the recommendations
                rendering_callback(resp);
                // track the recommendation area as the variant slice with a custom source name.
                trackAlgo('filtered');

```

この例では、メタデータに"type" : "video"を含むアイテムだけが返されます。

複数の値を含めたい場合は、その値をJSONの配列としてフォーマットしてください。

HTML
```
$p('register', {
            max: 4,
            widget: 'my-widget-name',
            opts: {"keywords": ["river", "water", "frogs"]},
            callback: function(resp) {
                // You might wish to wrap the code in this callback inside jQuery, to handle load order issues
                //render the recommendations
                rendering_callback(resp);
                // track the recommendation area as the variant slice with a custom source name.
                trackAlgo('filtered');
```

## フィルタを使う (API)

APIでの組み込みにおいては、/modelエンドポイントへのリクエストのJSON bodyに含めることでフィルタを行うことができます。ここでは、"category"に対するフィルタに、リクエストする値の配列を含めるAPIリクエストの例を示します。

```
curl -X POST \   https://query.petametrics.com/v2/model \   -H 'Cache-Control: no-cache' \   -H 'Content-Type: application/json' \   -d '{   "apiKey" : "yourAPIkeyhere",   "url" : "http://www.domain.com/current-url",   "category" : ["politics", "economy"],   "userId" : "xxxxxxx" }'
```

### グローバルフィルタ

ほとんどの場合はウィジェット単位でフィルタを行いますが、アカウント全体に対してフィルタを設定することもできます。これは、広い範囲でのフィルタを行う場合に有効で、記事やビデオだけをリコメンドする、プレミアムでないコンテンツ、在庫のあるアイテムなどをリコメンドするような場合に使用します。このようなフィルタリングを使用する場合は、サポートにお問合わせください。

## 最近投稿されたアイテムをブーストして時間経過とともに減衰させる方法

これは、より新しいアイテムのリコメンデーションを強める方法です。簡単にまとめると、この方法では新しいアイテムをより強くブーストし、アイテムの投稿から時間が経つほどブーストを弱めていきます。通常では、LiftIgniterの機械学習のシステムがトレンドしているか、新鮮であるかをユーザの行動をもとに参照しているため必要がありません。この推論は通常では、投稿からの時間経過を単純に見るよりもスマートに働きます。例えば、投稿されてから長い時間が経過しているアイテムでも、トピックとして適切であるためにとレンディングアイテムとして取り上げられることがあります。

この方法を使用したい場合は、サポートまでご連絡ください。

単純に、特定の時間を経過したアイテムの表示を停止したい場合は、最近投稿された記事やアイテムのみを表示する方法を参照してください。

時間減衰の機能を使用したい場合は、インベントリアイテムのメタデータに、published_timeを含める必要があります。

### 時間減衰は2つの設定でコントロールします:

* timeDecayInitialScore - これは、投稿されて間もないアイテムに付加される初期スコア地です。本質的には、時間減衰のスコアがその他のスコアに対して、順位付けにおいてどれくらいの重要度をもたせるかを決定します。ここでは、高いスコアでは10、中等のもので4または5を設定します。

* halfLifeInSeconds - これは、スコアが半減するまでの秒数を示します。例えばこれを7日間に設定すると、14日後にはブーストの効果を受けなくなります。

これらの設定は、3つの方法で実装することができます:

* グローバル: 初期のスコア値と半減までの秒数を、サポートまでお知らせください。全てのアイテムとクエリに対してこれをデフォルト値として設定します。

* クエリ単位: リコメンデーションのリクエストに、初期のスコア値と半減までの秒数をパラメータとして含めてください。これを行うことで、サイトのどの部分を閲覧しているかによって、最近投稿されたアイテムに重要性をもたせるかをコントロールすることができます。例えば、Webサイトのトップページからリクエストが送られる場合はより新しい記事を強くリコメンドして、記事やカテゴリのページにおいてはより自然なランキングを適用させることができます。これらのパラメータがクエリに含まれている場合は、全体のデフォルト設定を上書きして処理します。

* アイテム単位: アイテムのインベントリメタデータに、初期のスコア値と半減までの秒数を含めてください。この方法では、アイテムごとに異なる半減までの秒数を設定できるため、柔軟な設定を行えます。この値が指定されているアイテムは、場合は、全体のデフォルト設定およびクエリ単位の設定を上書きして処理します。
