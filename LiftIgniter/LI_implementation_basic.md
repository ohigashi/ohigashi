# リコメンデーションエリアHTML

[ウィジェット](#ウィジェット)  
[スクリプト](#スクリプト)   
[A/Bテスト用ウィジェット](#A/Bテスト用ウィジェット)  
[A/Bテスト用スクリプト](#A/Bテスト用スクリプト)  

---
## ウィジェット

```
<center><h1>
LiftIgniter<br> Recommendations
</h1></center>


<!--The Recommendation Area where items will be displayed-->
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

```
//----------Widget Display Template------------

//---------- BEACON SNIPPET START------------
// The Beacon calls our JS SDK, which allows the usage of the other $p functions seen below. It also applies a first-party tracking cookie to identify unique users.

if (typeof $igniter_var === 'undefined') {
  // Ensures that our client code is updated.
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
  // If you have a LiftIgniter account, you can replace "f7tsbsle43k5irci" with your own JS key to get recommendations from your own inventory. Make sure to replace it in the URL above, and in the $p("init") function below.

  $p("init", "f7tsbsle43k5irci");
  //This demo account only has 4 items in the inventory, so that is the maximum number of items it can return.
}
//---------- BEACON SNIPPET END------------

//---------- WIDGET CODE START------------

//-- 1. Define Rendering Area --
//$p('render') uses the Template defined in the HTML above to overwrite the Recommendation Area with the items recommended by LiftIgniter.
var rendering_callback = function(resp) {
  var els = document.querySelectorAll('#li-recommendation-unit > div.li-widget-item');
  var template = document.querySelector('#recommended-item-template').innerHTML;

  for (var i = 0; i < els.length && i < resp.items.length; ++i) {
    // Basically Mustache.render(template, resp.items[i]); 
    els[i].innerHTML = $p('render', template, resp.items[i]);
  }
}

//-- 2. Define Items to be Tracked --
//$p('track') attaches event listeners and query string parameters to each href in the recommendation area.
var trackAlgo = function(algorithm) {
  $p('track', {
    elements: document.querySelectorAll('#li-recommendation-unit > div.li-widget-item'),
    //make sure that the "name" value selected here matches the value of "widget" in the $p("register") function. This allows us to match recommendation requests to the widget activity reported in our dashboard.
    name: 'default-widget',
    source: algorithm
  });
}

//-- 3. Request Recommendations --
$p('register', {
  max: 4,
  widget: 'default-widget',
  callback: function(resp) {
    // You might wish to wrap the code in this callback inside jQuery, to handle load order issues
    // Trigger rendering once recommendations are returned
    rendering_callback(resp);
    // Track items in the Recommendation Area with a source of "LI" to identify where the recommendations came from.
    trackAlgo('LI');
  }
});


$p('fetch');

//---------- WIDGET CODE END------------
```

---
## A/Bテスト用ウィジェット

```
<center><h1>
LiftIgniter<br> Recommendations
</h1></center>

<!--The Recommendation Area where items will be displayed-->
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

```
//---------- BEACON SNIPPET START------------
// The Beacon calls our JS SDK, which allows the usage of the other $p functions seen below. It also applies a first-party tracking cookie to identify unique users.

if (typeof $igniter_var === 'undefined') {
  // Ensures that our client code is updated.
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
  // If you have a LiftIgniter account, you can replace "f7tsbsle43k5irci" with your own JS key to get recommendations from your own inventory. Make sure to replace it in the URL above, and in the $p("init") function below.

  $p("init", "f7tsbsle43k5irci");
  //This JS key will return a max of 4 test items from LiftIgniter.
}
//---------- BEACON SNIPPET END------------

//$p('render') uses the template defined in the HTML to overwrite the target area with the items recommended by LiftIgniter.
var rendering_callback = function(resp) {
  var els = document.querySelectorAll('#li-recommendation-unit > div.li-widget-item');
  var template = document.querySelector('#recommended-item-template').innerHTML;

  for (var i = 0; i < els.length && i < resp.items.length; ++i) {
    // Basically Mustache.render(template, resp.items[i]); 
    els[i].innerHTML = $p('render', template, resp.items[i]);
  }
}

//$p('track') attaches event listeners and query string parameters to each URL in the recommendation area.
var trackAlgo = function(algorithm) {
  $p('track', {
    elements: document.querySelectorAll('#li-recommendation-unit > div.li-widget-item'),
    //make sure that the "name" value selected here matches the value of "widget" in the $p("register") function. This allows us to match recommendation requests to the widget activity reported in our dashboard.
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
        // You might wish to wrap the code in this callback inside jQuery, to handle load order issues
        // Trigger rendering once recommendations are returned
        rendering_callback(resp);
        // track the source of the recommendations as 'LI' because they were recommended by LiftIgniter.
        trackAlgo('LI');
      }
    });
  } else {
    // track the source of the recommendations as 'base' for the control slice of the A|B test.
    trackAlgo('base');
  }
  // Executes $p("register") and all arguments to get the recommendations.
  $p('fetch');
}

// Decide which slice the current user is on.
// $p('userHash') hashes user cookie id to a random 32-bit integer.
// $p('abTestSlice') returns the absolute value of this number mod 100
$p('abTestSlice', {
  callback: abTestHandler
});
```
