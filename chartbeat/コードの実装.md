# Chartbeatコードの実装

1. 下記コードをWebサイトの<head>部内に追加する。 

```
<script type="text/javascript">
    var _sf_async_config = _sf_async_config || {};
    /** CONFIGURATION START **/
    _sf_async_config.uid = ####; /** ここにChartbeatのUIDを入れる **/
    _sf_async_config.domain = 'domain.com'; /** ここにWebサイトのドメイン名を入れる **/
    _sf_async_config.useCanonical = true;
    /** CONFIGURATION END **/
    var _sf_startpt = (new Date()).getTime();
</script>
<script async src="//static.chartbeat.com/js/chartbeat_mab.js"></script>
```

Canonicalリンクを使用する場合は、下記のように<head>部内のスクリプトの前に設定しておく。
```
<link rel="canonical" href="http://www.seomoz.org/blog">
```

2. 下記コードをWebサイトの</body>タグ前に追加する。 

```
<script type='text/javascript'>
    var _sf_async_config = _sf_async_config || {};
    /** CONFIGURATION START **/
    _sf_async_config.sections = 'Change this to your Section name'; //CHANGE THIS
    _sf_async_config.authors = 'Change this to your Author name'; //CHANGE THIS
    /** CONFIGURATION END **/
    (function() {
        function loadChartbeat() {
            var e = document.createElement('script');
            e.setAttribute('language', 'javascript');
            e.setAttribute('type', 'text/javascript');
            e.setAttribute('src', '//static.chartbeat.com/js/chartbeat.js');
            document.body.appendChild(e);
        }
        var oldonload = window.onload;
        window.onload = (typeof window.onload != 'function') ?
            loadChartbeat : function() {
                oldonload();
                loadChartbeat();
            };
    })();
</script>
```
