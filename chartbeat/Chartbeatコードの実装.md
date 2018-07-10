1. 下記コードをHTMLの`<head>`タグ内に追加する。


```
<script type='text/javascript'>
    (function() {
        /** CONFIGURATION START **/
        var _sf_async_config = window._sf_async_config = (window._sf_async_config || {});

        _sf_async_config.uid = #####; //CHANGE THIS
        _sf_async_config.domain = 'YourDomain.com'; //CHANGE THIS
        _sf_async_config.useCanonical = true;
        _sf_async_config.useCanonicalDomain = true;
        _sf_async_config.sections = ''; //CHANGE THIS TO YOUR SECTION NAME(s)
        _sf_async_config.authors = ''; //CHANGE THIS TO YOUR AUTHOR NAME(s)
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
