1. 下記コードをHTMLの`<head>`タグ内に追加する。

```
<script type="text/javascript">
  var _sf_async_config = _sf_async_config || {};
  /** CONFIGURATION START **/
  _sf_async_config.uid = Chartbeatアカウント名;
  _sf_async_config.domain = 'Webサイトドメイン名';
  _sf_async_config.flickerControl = false;
  _sf_async_config.useCanonical = true;
  _sf_async_config.useCanonicalDomain = true;
  /** CONFIGURATION END **/
</script>
<script async src="//static.chartbeat.com/js/chartbeat_mab.js"></script>
```

2. 下記コードをHTMLの`<body>`タグ内のなるべく上の部分に追加する。

```
<script type='text/javascript'>
  (function() {
      /** CONFIGURATION START **/
      var _sf_async_config = window._sf_async_config = (window._sf_async_config || {});

      _sf_async_config.sections = 'セクション名'; // 複数ある場合はコロンで区切る
      _sf_async_config.authors = '著者名'; // 複数ある場合はコロンで区切る
      /** CONFIGURATION END **/
      function loadChartbeat() {
          var e = document.createElement('script');
          var n = document.getElementsByTagName('script')[0];
          e.type = 'text/javascript';
          e.async = true;
          e.src = '//static.chartbeat.com/js/chartbeat.js';
          n.parentNode.insertBefore(e, n);
      }
      loadChartbeat();
   })();
</script>
```
