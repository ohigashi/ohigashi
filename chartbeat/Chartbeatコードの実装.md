## Chartbeat 基本コード


```
<script type='text/javascript'>
    (function() {
        /** CONFIGURATION START **/
        var _sf_async_config = window._sf_async_config = (window._sf_async_config || {});

        _sf_async_config.uid = #####; //ユーザーID
        _sf_async_config.domain = 'YourDomain.com'; //ドメイン名
        _sf_async_config.flickerControl = false;
        _sf_async_config.useCanonical = true;
        _sf_async_config.useCanonicalDomain = true;
        _sf_async_config.sections = ''; //セクションまたはカテゴリ名
        _sf_async_config.authors = ''; //著者名
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

## HLT追加コード（基本コードの下に追加する）

```
<script async src="//static.chartbeat.com/js/chartbeat_mab.js"></script> //HLT用スクリプト
```

## デバイスなどによってURL構造が異なるまたは複数ページにまたがる記事（ページネーション）を同じ記事として分析したい場合

http://support.chartbeat.com/docs/#canonical

http://support.chartbeat.com/docs/#path

http://support.chartbeat.com/docs/#mobile

## AJAX使用サイトの場合

http://support.chartbeat.com/docs/#ajax

## 登録または有料課金ユーザーなどいる場合

http://support.chartbeat.com/docs/#usertype
