# Web Performance

A guide to improve the performance of your site.

## Important Terms

1. **First Meaningful Paint and Start Render Time** ---> **Start Render Time** is the moment something first displays on the user's screen. The webpage goes from a blank white screen and changes.
2. **Incremental HTML delivery**
3. **Incremental DOM constructions or Progressive Rendering**
4. **Why can't we have incremental CSS rendering?** --->
    This is because CSS is cascading in nature ie child node inherits properties from its parent node. Because of this the final CSS for a node can only be defined when we have the complete CSSOM ready. So the browser blocks page rendering till it receives all the CSS. **Hence CSS is render blocking.**
5. **Batch updates in layout**
6. **Critical CSS and Immediate CSS** ---> The CSS that is needed in order to load a page is called the critical CSS. In general, a page load should only be blocked on critical CSS. Uncritical CSS is CSS that the page might need later. Theoretically, the most optimal approach is to inline critical CSS into the `<head>` of the HTML. For example, suppose clicking a button causes a modal to appear. The modal only appears after clicking the button. The modal's style rules are uncritical, because the modal will never be displayed when the page is first loaded.The Coverage tab of Chrome DevTools can help you discover critical and uncritical CSS. There are a lot of tools that help you in deferring uncritical CSS. Eg loadCSS
7. **Execute scripts on browser unload event**
8. **Critical Path Metrics** --->
    1. Number of critical resources.
    2. Total Critical KB.
    3. Minimum Critical Path Length/RoundTrips
11. **TTFB** ---> This is the amount of time it takes after the client sends an HTTP request to receive the first byte of the requested resource from the server. The resource can be HTML,CSS,JS etc. A large TTFB indicates slow server or network issue.
12. **Above the fold view** ---> Above the fold, as it applies to Web design, is the portion of a Web page that is visible in a browser window when the page first loads. The portion of the page that requires scrolling in order to see content is called "below the fold."


## Network Performance

### 1. Make Few Http Request 
1. **Bundling** ---> Bundle multiple files to a single file.
### 2. Send as small as possible
1. **Minify files** --> HTML, CSS and JS
    Html minification includes removal of comments etc. CSS minification means removal of white spaces etc. Js minification means removal of white spaces, renaming variables etc.
2. **Compress files** --> Http Compressions Eg Gzip
### 3. Request as infrequently as possible
Implement the right caching strategy for your app.
### 4. Reduce network latency
1. Faster network speed
2. Http2 protocol

## Render Performance

The rule of render performance is **Measure first then optimize**. It is important to reduce the number and size of critical resources. 

### 1. HTML Performance

1. `<meta name="viewport" content="width=device-width">` ---> This tells the browser that the width of the viewport should be device width. If this is not provided then the browser will use default viewport width which is 980px. Width of viewport = width of body.

**Note** : A page should ideally be under 14KB size. This is because if the size in greater than 14KB then it requires more than 1 round trip for the browser to fetch the resource which delays the CRP.
    
### 2. CSS Performance

1. **The more general selector is easy to evaluate** --> This is because the more generic the css rule is the less work the browser needs to do to parse the DOM and apply the CSS. Eg. In the below image if we need to apply the CSS to the p tag then browser will have to parse the DOM to see if the p tag has div as its parent or not. Whereas in the case of h1 it is straight forward.
![Screen Shot 2019-04-01 at 6 07 53 PM](https://user-images.githubusercontent.com/46783722/55328059-234d8580-54a9-11e9-8dbc-b2f8d84a454b.png)

2. **Display: none** ---> This will remove the node and all the child nodes from the render tree. So in the below example no child node of span will be parsed by the browser.
![Screen Shot 2019-04-01 at 6 32 48 PM](https://user-images.githubusercontent.com/46783722/55329587-9b697a80-54ac-11e9-829a-ca28f429330f.png)

3. **What happens when the parser encounters a script tag but the CSSOM isn’t ready yet?** ---> The Javascript execution will be halted until the CSSOM is ready. Even though the DOM construction stops when a script tag is encountered, that’s not what happens with the CSSOM. With the CSSOM, the JS execution waits. No CSSOM, no JS execution.

4. **Unblocking CSS with Media Queries** ---> By default all the CSS files are render blocking. But if we provide the media attribute in the `<link>` tag then the browser will not block the rendering for that particular css. We can separate/divide all our Media query css into different files so that the rendering is non blocking.

Eg We can add a print CSS in a separate css file say `style-print.css` and load it as `<link rel="stylesheet" href="style-print.css" media="print">`

5. **Inline Critical CSS** ---> This helps in quick creating of CSSOM and prevent JS execution from getting blocked.

6. **Defer Non-critical CSS** ---> When it comes to page speed, defer loading CSS scripts is most beneficial when your web pages load large CSS scripts. Now you can’t just stick all your CSS in one file, defer load it, and expect your web pages to turn out well. The first view your visitors (especially ones with slow internet connections or on mobile devices) may get of your website will either be a blank page or look horrible because your web pages will not be styled, simply because the CSS hasn't been loaded yet. This is the reason defer loading all your CSS is not an option. Like explained above, the solution to this is finding out which part of your CSS is used for the above-the-fold initial view of your page. Once you know this you should inline this CSS script in the HTML head and defer load the remainder of your CSS.

    **Note** : Do not defer load a small or medium sized CSS script. Only defer load bigger CSS scripts.
    **How to defer load un-critical css?** 
    If html is like this
    ```html
    <html>
        <head>
            <link rel="stylesheet" href="small.css">
        </head>
        <body>
            <div class="blue">
                Hello, world!
            </div>
        </body>
    </html>
    ```
    If CSS is like this
        
    ```css
        .yellow {background-color: yellow;}
        .blue {color: blue;}
        .big { font-size: 8em; }
        .bold { font-weight: bold; }
    ```
    Then you can inline critical CSS and defer non-critical CSS like this
    ```html
        <html>
            <head>
                <style>
                    .blue{color:blue;}
                </style>
            </head>
            <body>
              <div class="blue">
                Hello, world!
              </div>
              <noscript id="deferred-styles">
                <link rel="stylesheet" type="text/css" href="small.css"/>
              </noscript>
              <script>
                var loadDeferredStyles = function() {
                  var addStylesNode = document.getElementById("deferred-styles");
                  var replacement = document.createElement("div");
                  replacement.innerHTML = addStylesNode.textContent;
                  document.body.appendChild(replacement)
                  addStylesNode.parentElement.removeChild(addStylesNode);
                };
                var raf = window.requestAnimationFrame || window.mozRequestAnimationFrame ||
                    window.webkitRequestAnimationFrame || window.msRequestAnimationFrame;
                if (raf) raf(function() { window.setTimeout(loadDeferredStyles, 0); });
                else window.addEventListener('load', loadDeferredStyles);
              </script>
            </body>
        </html>
    ```
    The critical styles needed to style the above-the-fold content are inlined and applied to the document immediately. The full small.css is loaded after initial painting of the page. Its styles are applied to the page once it finishes loading, without blocking the initial render of the critical content.

    `Note : This transformation, including the determination of critical/non-critical CSS, inlining of the critical CSS, and deferred loading of the non-critical CSS, can be made automatically by the PageSpeed Optimization modules(https://developers.google.com/speed/pagespeed/module/) for nginx, apache, IIS, ATS, and Open Lightspeed, when you enable the prioritize_critical_css filter.`

    `Note : See also the loadCSS function to help load CSS asynchronously, which can work with Critical, a tool to extract the critical CSS from a web page`

    `Note : The web platform will soon support loading stylesheets in a non-render-blocking manner, without having to resort to using JavaScript, using [HTML Imports](http://w3c.github.io/webcomponents/spec/imports/#link-type-import).`

### 3. Javascript Performance 

**The entire DOM construction process is halted until the script finishes executing**.This is because JavaScript can alter both the DOM and CSSOM. Since the browser isn’t sure what this particular Javascript will do, it takes precaution by halting the entire DOM construction all together. So it is extremely important to add them to the end of our html page and not at the beginning.

In this example the HTML parsing(Tokenizing ---> Nodes ----> DOM) is stopped when it encounters a script tag and resumed after the scripts execution.

![Screen Shot 2019-04-02 at 10 06 18 AM](https://user-images.githubusercontent.com/46783722/55376677-0d82a380-552f-11e9-9271-86249073906d.png)

1. **Add javascript to the bottom of your html** ---> Since Js is parser blocking, so it is wise to put all our JS files to the bottom of the page so that the HTML parser is not blocked and the DOM is created.

2. **Using async and defer** ---> 
    **async** helps us to load the script without blocking the HTML parser. Also the execution of the script is not blocked by CSSOM. So it doesn't block the CRP. Although the script execution is parser blocking.
    **defer** is similar but the only difference is that the execution of the deferred script only takes place after the DOM is ready. So the execution is also not parser blocking. Also due to this reason the order of execution is the same as the order in which the scripts are ordered in the HTML.

    **Note** : Scripts should be marked as async or defer only if they don't have any interaction with the DOM or CSSOM. Otherwise it may lead to unwanted results.

    async
    ![Screen Shot 2019-04-02 at 10 38 59 AM](https://user-images.githubusercontent.com/46783722/55377661-93a0e900-5533-11e9-9385-cfcf6499f29b.png)

    defer
    ![Screen Shot 2019-04-02 at 10 39 05 AM](https://user-images.githubusercontent.com/46783722/55377663-96034300-5533-11e9-9663-e2f92d9cbc8b.png)

    **Note** : Inline scripts don't have any async or defer tag so they are always parser blocking.

3. **using preload scanner** --->
Analysis:
    1. Ideally the timing.js is loaded after 
        1. The style.css in loaded and CSSOM is prepared.
        2. And the app.js is loaded and executed. This would delay the CRP since the browser has to wait for a long time before it can start loading the timing.js file.
    2. But if we add the `rel="preload"` tag to the script file like `<script src="timing.js" rel="preload">` then the browser preload scanner scans the html for any preload script and loads them in parallel with the other resources.
    ![Screen Shot 2019-04-02 at 1 55 38 PM](https://user-images.githubusercontent.com/46783722/55387417-0e2b3200-554f-11e9-9e54-7d786e89d3aa.png)

    ` Note : This tag work for both <link> and <script> tag. Which means it works for external JS as well as CSS.`

## Miscellaneous

### 1. CRP : 
`Character -----> Tokens(Using Tokenizer) -----> Nodes -----> DOM + CSSOM ----> Render Tree -----> Layout -----> Paint ----> Compose`

Parameters to evaluate a CRP OR **Critical Path Metrics**:
1. Number of critical resources.
2. Total Critical KB.
3. Minimum Critical Path Length/RoundTrips

The process of handling HTML, CSS and Javascript to render a webpage.
Interaction at 60 frames per second!

![Screen Shot 2019-04-01 at 3 38 56 PM](https://user-images.githubusercontent.com/46783722/55320628-2cccf280-5495-11e9-85ae-3092a73b088b.png)


**CRP DIAGRAM** This image shows how CSS blocks JS engine. Which indeed blocks DOM building.
![Screen Shot 2019-04-02 at 10 23 06 AM](https://user-images.githubusercontent.com/46783722/55377167-65220e80-5531-11e9-80d7-d393b62af7d4.png)

In the below example although the number of Critical resources are 3 but it only takes 2 roundtrip's since JS and CSS are downloaded in parallel. `Time taken for all round trips = Time taken to render the page.`
![Screen Shot 2019-04-02 at 11 24 09 AM](https://user-images.githubusercontent.com/46783722/55379213-e4b3db80-5539-11e9-8a20-7de6265242fb.png)

The below html is a perfect example of non render blocking html.
```html
<!DOCTYPE html>
<html>
  <head>
    <meta name="viewport" content="width=device-width,initial-scale=1">
    <link href="style.css" rel="stylesheet" media="print">
  </head>
  <body>
    <p>Hello <span>web performance</span> students!</p>
    <div><img src="awesome-photo.jpg"></div>
    <script src="app.js" async></script>
  </body>
</html>
```
![Screen Shot 2019-04-02 at 3 35 09 PM](https://user-images.githubusercontent.com/46783722/55394454-f5c21400-555c-11e9-8fc9-a0aaf91fea04.png)

**Note** : Images don't block CRP.

### 2. Who waits for who? 

`DOM ---> JS Execution ---> CSSOM`

This means that Javascript block the DOM parsing and CSSOM blocks javascript execution. So if the CSSOM is not ready the JS engine will be blocked. Also CSSOM is render blocking.