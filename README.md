## Automatic Pjax

The `Pjax.js` is <s>a jQuery plugin</s> script that uses ajax, `pushState` and automatically detects links and forms, for configure page you need use `id="pjax-container"` in all pages, example:

```html
<div id="pjax-container">
    ...
</div>
```

Pjax load all content from pages, but uses only contents from first element using `pjax-container`, if you don't need the Pjax in a spefic link or form then use `data-pjax-ignore` attribute, example:

```html
<p>
    Hello world!
    Example <a data-pjax-ignore href="/page.html">page</a>.
</p>
```

## Support

The `pjax.js` support links, forms with method `GET`, forms with method `POST` and support files and multiple files (`<input type="file" multiple>`).

Requirements:

- `DOMParser` or `document.implementation`
- `pushState`, `replaceState` and `popstate` (DOM History manipulation)
- `FormData` (XMLHttpRequest Level 2) - if need upload files using PJAX (`enctype="multipart/form-data"`)

Tested in:

- Internet Explorer 10
- Firefox
- Google Chrome
- iOS 10
- Android 2.3+

## Usage

For use:

```js
Pjax.start();
```

## Pjax API

Method | Description
--- | ---
`Pjax.supported` | Return `true` if support this lib, otherwise return `false`
`Pjax.remove("remove");` | Remove PJAX requests and events
`Pjax.on(event, function(...) {...});` | Define an event

## Pjax Events

Example | Description
--- | ---
`Pjax.on("initiate", function(url, config) {...});` | Trigged when clicked in a link or submit a form
`Pjax.on("done", function(url) {...});` | Trigged when page loaded using `$.jax`
`Pjax.on("fail", function(url, status, error) {...});` | Trigged when page failed to load, `status` return HTTP code and `error` return message error
`Pjax.on("then", function(url) {...});` | Executes every time a request is completed, even if it fails or success.
`Pjax.on("history", function(url, stateData) {...});` | Executes every use back or forward
`Pjax.on("handler", function(data, config, callbackDone, callbackFail) {...});` | Create your owner response to Pjax.js request
`Pjax.on("dom", function(url, newdocument) {...});` | Allows you to manipulate an element change manually, for example you can create transitions or change newdocument

> Tip: You can use `Pjax.on("dom", ...);` for remove events in DOM

## Pjax configs

Property | type | default | description
--- | --- | --- | ---
`containers:` | `array` | `[ "#pjax-container" ]` | Informs which elements to update on the page
`updatecurrent:` | `bool` | `false` | If `true` request same url in used by current page is executed `history.replaceState`, otherwise nothing will be executed
`updatehead:` | `bool` | `true` | The "pjax.js" has an intelligent update system that helps avoid the "blink" effect, because instead of updating everything it only updates what has been changed, however if you are sure that nothing will change as you page, you can set it to false, the only one that will continue to be updated will be the `<title>` tag
`insertion` | `string` | `undefined` | Support values `append` and `prepend`, if omitted it will replace the content
`scrollLeft:` | `number` | `0` | After loading a page via PJAX you can define where scrollLeft should scroll
`scrollTop:` | `number` | `0` | After loading a page via PJAX you can define where scrollTop should scroll
`loader:` | `bool` | `true` | Adds the native Pjax loader, if you want to create a loader of your own, set it to `false`
`nocache:` | `bool` | `false` | Prevent cache
`proxy:` | `string` | `""` | Set Web Proxy
`formSelector:` | `string` | `form:not([data-pjax-ignore]):not([action^='javascript:'])"` | Set form selector, set empty for prevent forms uses Pjax
`linkSelector:` | `string` | `a:not([data-pjax-ignore]):not([href^='#']):not([href^='javascript:'])"` | Set form selector, set empty for prevent links uses Pjax

## PJAX with HTML5

If need overwrite properties for specific link or form you can config using HTML attributes:

Property | equivalent | example | description
--- | --- | --- | ---
`data-pjax-containers` | `containers:` | `<a href="..." data-pjax-containers="[ &quot;#foo&quot;, &quot;#bar&quot;, &quot;#baz&quot; ]"` | Adjusts the containers when the request comes from a specific form or link
`data-pjax-updatehead` | `updatehead:` | `<a href="..." data-pjax-updatehead="false"` | Allow/Disallow head tag update when the request comes from a specific form or link
`data-pjax-insertion` | `insertion` | `<a href="..." data-pjax-insertion="append"` | Append or prepend data to the containers instead of updating
`data-pjax-scroll-left` | `scrollLeft:` | `<form action="..." data-pjax-scroll-left="10"` | custom scroll-left to specific form or link
`data-pjax-scroll-top` | `scrollTop:` | `<form action="..." data-pjax-scroll-top="-1"` | custom scroll-top to specific form or link
`data-pjax-loader` | `loader:` | `<a href="..." data-pjax-loader="false"` | Allow/Disallow loader animate to specific form or link
`data-pjax-done` | - | `<a href="..." data-pjax-done="console.log('success', this);"` | Custom "done" event to specific form or link
`data-pjax-fail` | - | `<a href="..." data-pjax-fail="console.log('error', this);"` | Custom "error" event to specific form or link
`data-pjax-ignore` | - | `<a href="..." data-pjax-ignore="true">` | Normal navigation to specific form or link
`data-pjax-resource` | - | `<link href="..." data-pjax-resource="true">` | Prevent remove element after update DOM (for now it only works in the `<head>`)

For change configs use like this:

```js
Pjax.start({
    containers: [ "#my-container" ], //Change container element
    scrollLeft: -1, //Disable autoscroll
    scrollTop: -1 //Disable autoscroll
});
```

## Update two elements (or more)

You can change the element you want to update or even add more elements, eg.:

```html
<div id="navbar">
    ...
</div>
<div id="my-container">
    ...
</div>
<script>
Pjax.start({
    containers: [ "#navbar", "#my-container" ] //Change containers element
});
</script>
```

You can change configs in `initiate` event, example:

```html
<div id="pjax-container">
    <div id="filter">
        <form>
            ...
        </form>
    </div>
    <div id="search-container">
        ...
    </div>
</div>
<script>
Pjax.start();

Pjax.on("initiate", function (url, configs) {
    if (url.indexOf("/search/") === 0 && window.location.href.indexOf("/search/") === 0) {
        configs.containers = [ "#search-container" ];
    }
});
</script>
```

If you are on a product search page and are doing a new search instead of updating the entire container will only update the element where the products are, other pages will continue to update the entire container.

## Pjax in server-side

It is possible to detect if the request came from the PJAX using the request headers:

Header | Description
--- | ---
`X-PJAX` | Indicates that the page was requested via PJAX
`X-PJAX-Container` | Informs the container selectors used

Example using PHP:

```php
if (isset($_SERVER['HTTP_X_PJAX'])) {
    echo 'You using pjax';
}
```

## Redirect using PJAX

You can use HTTP response header `X-PJAX-URL` in back-end for force Pjax.js redirects, example:

```php
header('X-PJAX-URL: /new-page.html');

...
```

## Change or add container using PJAX

You can use HTTP response header `X-PJAX-URL` in back-end for force Pjax.js redirects, example:

```php
if (isset($_SERVER['HTTP_X_PJAX'])) {
    header('X-PJAX-Container: #foo,#bar');

    echo '<div id="foo"> ... </div>';

    echo '<div id="bar"> ... </div>';
}
```

## Custom loader with Pjax

You can custom CSS, example change color and size, put in new CSS file or `<style>` tag:

```css
.pjax-loader .pjax-progress {
    height: 6px;
    background-color: blue;
}
```

If you need custom "more", first remove default loader:

```javascript
Pjax.start({
    loader: false
});
```

And after use `initiate` and `then` events:

```javascript
Pjax.on("initiate", function () {
    $(".my-custom-loader").css("display", "block");
});

Pjax.on("then", function () {
    $(".my-custom-loader").css("display", "none");
});
```

HTML:

```html
<div class="my-custom-loader">
    <img src="loader.gif">
</div>
```

## Pjax handler response

For create a custom responses for Pjax you can use `handler` event, example:

```javascript
Pjax.start({
    updatehead: false //Prevent remove itens in head
});

Pjax.on("handler", function(hdata, config, done, fail) {
    setTimeout(function () {
        done(hdata.url, '<div id="pjax-container">Foo: ' + new Date() + '</div>', config, hdata.state);
    }, 1000);
});
```
