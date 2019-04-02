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


## Network Performance

### 1. Make Few Http Request
### 2. Send as small as possible
### 3. Request as infrequently as possible
### 4. Reduce network latency

## Render Performance

The rule of render performance is **Measure first then optimize**

### 1. HTML Performance

1. `<meta name="viewport" content="width=device-width">` ---> This tells the browser that the width of the viewport should be device width. If this is not provided then the browser will use default viewport width which is 980px. Width of viewport = width of body.
    
### 2. CSS Performance

1. **The more general selector is easy to evaluate** --> This is because the more generic the css rule is the less work the browser needs to do to parse the DOM and apply the CSS. Eg. In the below image if we need to apply the CSS to the p tag then browser will have to parse the DOM to see if the p tag has div as its parent or not. Whereas in the case of h1 it is straight forward.
![Screen Shot 2019-04-01 at 6 07 53 PM](https://user-images.githubusercontent.com/46783722/55328059-234d8580-54a9-11e9-8dbc-b2f8d84a454b.png)

2. **Display: none** ---> This will remove the node and all the child nodes from the render tree. So in the below example the no child node of span will be parsed by the browser.
![Screen Shot 2019-04-01 at 6 32 48 PM](https://user-images.githubusercontent.com/46783722/55329587-9b697a80-54ac-11e9-829a-ca28f429330f.png)

3. **What happens when the parser encounters a script tag but the CSSOM isn’t ready yet?** ---> The Javascript execution will be halted until the CSSOM is ready. Even though the DOM construction stops when a script tag is encountered, that’s not what happens with the CSSOM. With the CSSOM, the JS execution waits. No CSSOM, no JS execution.


### 3. Javascript Performance 

**The entire DOM construction process is halted until the script finishes executing**.This is because JavaScript can alter both the DOM and CSSOM. Since the browser isn’t sure what this particular Javascript will do, it takes precaution by halting the entire DOM construction all together. So it is extremely important to add them to the end of our html page and not at the beginning.

## Miscellaneous

### 1. CRP : 
`Character -----> Tokens(Using Tokenizer) -----> Nodes -----> DOM + CSSOM ----> Render Tree -----> Layout -----> Paint`

The process of handling HTML, CSS and Javascript to render a webpage.
Interaction at 60 frames per second!

![Screen Shot 2019-04-01 at 3 38 56 PM](https://user-images.githubusercontent.com/46783722/55320628-2cccf280-5495-11e9-85ae-3092a73b088b.png)

### 2. Who waits for who? 
    DOM ---> JS Execution ---> CSSOM