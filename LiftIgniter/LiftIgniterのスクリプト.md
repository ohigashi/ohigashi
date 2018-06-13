## Tracking Beacon

```
<script type="text/javascript">
if (typeof $igniter_var === 'undefined') {
// Ensures that our client code is updated.
(function(w,d,s,p,v,e,r) {w.$ps = (w.performance && w.performance.now && typeof(w.performance.now) == "function") ? w.performance.now() : undefined;w['$igniter_var']=v;w[v]=w[v]||function(){(w[v].q=w[v].q||[]).push(
arguments)};w[v].l=1*new Date();e=d.createElement(s),r=d.getElementsByTagName(s)[0];e.async=1;
e.src=p+'?ts='+(+new Date()/3600000|0);
r.parentNode.insertBefore(e,r)})(window,document,'script','//cdn.petametrics.com/{JAVASCRIPT_KEY}.js','$p');
// JAVASCRIPT_KEYを差し替えてください

$p("init", {JAVASCRIPT_KEY}); // JAVASCRIPT_KEYを差し替えてください
$p("send", "pageview");
}
</script>
```

## Mustache.JS Template

```
<script type="application/mustache"
        id="li-recommendation-template">
  {{#items}}
  <div class='recommended_item'>
    <a href="{{url}}">
      <img src="{{thumbnail}}" width="150" height="150" />
      <span class="title">{{title}}</span>
    </a>
  </div>
 {{/items}}
</script>
```

## Widget Request JS

```
$p('register', {
    max: 5, // リコメンデーションに表示するアイテムの数
    widget: 'default-widget',
    callback: function(resp) {
      // Query selector should match div name from Recommendation Area
      var el = document.querySelector('#li-recommendation-unit');
      // Template should match mustache template name from Step 2
      var template = document.querySelector('#li-recommendation-template').innerHTML;
      // Basically Mustache.render(template, resp);
      el.innerHTML = $p('render', template, resp);
      
      $p('track', {
         // Div name from the Recommendation Area and recommendation item div name from Template
         elements: document.querySelectorAll('#li-recommendation-unit > div.recommended_item'),
         name: 'default-widget',
         // Match widget name
         source: 'LI',
         // Source "LI" indicates recommendations are provided by LiftIgniter
      });
    }
   });

// Executes the registered call.
$p('fetch');
```
