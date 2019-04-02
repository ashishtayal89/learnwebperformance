# Web Performance

A guide to improve the performance of your site.

## Important Terms

1. **Page First Render**
2. **Incremental HTML delivery**
3. **Incremental DOM constructions or Progressive Rendering**
4. **Why can't we have incremental CSS rendering?**
    This is because CSS is cascading in nature ie child node inherits properties from its parent node. Because of this the final CSS for a node can only be defined when we have the complete CSSOM ready. So the browser blocks page rendering till it receives all the CSS. **Hence CSS is render blocking.**
5. **Batch updates in layout**
6. **Critical CSS**
7. **Execute scripts on browser unload event**
8. **Critical Path Metrics**
9. **Immediate CSS**
10. **preload scanner** 
Analysis:
    1. Ideally the timing.js is loaded after 
        1. The style.css in loaded and CSSOM is prepared.
        2. And the app.js is loaded and executed. This would delay the CRP since the browser has to wait for a long time before it can start loading the timing.js file.
    2. But if we add the `rel="preload"` tag to the script file like `<script src="timing.js" rel="preload">` then the browser preload scanner scans the html for any preload script and loads them in parallel with the other resources.
![Screen Shot 2019-04-02 at 1 55 38 PM](https://user-images.githubusercontent.com/46783722/55387417-0e2b3200-554f-11e9-9e54-7d786e89d3aa.png)




## Network Performance

### 1. Make Few Http Request
### 2. Send as small as possible
1. **Minify files** --> HTML, CSS and JS
    Html minification includes removal of comments etc. CSS minification means removal of white spaces etc. Js minification means removal of white spaces, renaming variables etc.
2. **Compress files** --> 
### 3. Request as infrequently as possible
### 4. Reduce network latency

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

### 3. Javascript Performance 

**The entire DOM construction process is halted until the script finishes executing**.This is because JavaScript can alter both the DOM and CSSOM. Since the browser isn’t sure what this particular Javascript will do, it takes precaution by halting the entire DOM construction all together. So it is extremely important to add them to the end of our html page and not at the beginning.

In this example the HTML parsing(Tokenizing ---> Nodes ----> DOM) is stopped when it encounters a script tag and resumed after the scripts execution.
![Screen Shot 2019-04-02 at 10 06 18 AM](https://user-images.githubusercontent.com/46783722/55376677-0d82a380-552f-11e9-9271-86249073906d.png)

1. **Add javascript to the bottom of your html** ---> Since Js is parser blocking, so it is wise to put all our JS files to the bottom of the page so that the HTML parser is not blocked and the DOM is created.

2. **Using async and defer** ---> **async** helps us to load the script without blocking the HTML parser. Also the execution of the script is not blocked by CSSOM. So it doesn't block the CRP. Although the script execution is parser blocking.
**defer** is similar but the only difference is that the execution of the deferred script only takes place after the DOM is ready.So the execution is also not parser blocking. Also due to this reason the order of execution is the same as the order in which the scripts are ordered in the HTML.

**Note** : Scripts should be marked as async or defer only if they don't have any interaction with the DOM or CSSOM. Otherwise it may lead to unwanted results.

async
![Screen Shot 2019-04-02 at 10 38 59 AM](https://user-images.githubusercontent.com/46783722/55377661-93a0e900-5533-11e9-9385-cfcf6499f29b.png)

defer
![Screen Shot 2019-04-02 at 10 39 05 AM](https://user-images.githubusercontent.com/46783722/55377663-96034300-5533-11e9-9663-e2f92d9cbc8b.png)

**Note** : Inline scripts don't have any async or defer tag so they are always parser blocking.

## Miscellaneous

### 1. CRP : 
`Character -----> Tokens(Using Tokenizer) -----> Nodes -----> DOM + CSSOM ----> Render Tree -----> Layout -----> Paint ----> Compose`

Parameters to evaluate a CRP :
1. Number critical resources.
2. Total Critical KB.
3. Minimum Critical Path Length/RoundTrips

The process of handling HTML, CSS and Javascript to render a webpage.
Interaction at 60 frames per second!

![Screen Shot 2019-04-01 at 3 38 56 PM](https://user-images.githubusercontent.com/46783722/55320628-2cccf280-5495-11e9-85ae-3092a73b088b.png)


**CRP DIAGRAM** This image shows how CSS blocks JS engine. Which indeed blocks DOM building.
![Screen Shot 2019-04-02 at 10 23 06 AM](https://user-images.githubusercontent.com/46783722/55377167-65220e80-5531-11e9-80d7-d393b62af7d4.png)

In the below example although the number of Critical resources are 3 but it only takes 2 roundtrip's since JS and CSS are downloaded in parallel. `Time taken for all round trips = Time taken to render the page.`
![Screen Shot 2019-04-02 at 11 24 09 AM](https://user-images.githubusercontent.com/46783722/55379213-e4b3db80-5539-11e9-8a20-7de6265242fb.png)

**Note** : Images don't block CRP.

### 2. Who waits for who? 

`DOM ---> JS Execution ---> CSSOM`

This means that Javascript block the DOM parsing and CSSOM blocks javascript execution. So if the CSSOM is not ready the JS engine will be blocked. Also CSSOM is render blocking.