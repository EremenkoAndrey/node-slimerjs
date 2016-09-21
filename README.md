# node-slimerjs


---------------

This is a bridge between [SlimerJs](http://slimerjs.org/) and
[Node.js](http://nodejs.org/).

This project is inspired from the excellent [node-phantom-simple](https://github.com/baudehlo/node-phantom-simple) project.



Requirements
------------
You will need to install SlimerJS first. The bridge assumes that the
"slimerjs" binary is available in the PATH, or you will need to pass its path
into the `slimer.create() method.

For running the tests you will need [Expresso](http://visionmedia.github.com/expresso/).
The tests require SlimerJS 0.9.2 or newer to pass.

Installing
----------

    npm install node-slimerjs


Usage
-----
You can use it exactly like you would use Node-Phantom, and the entire API of
SlimerJs should work, with the exception that every method call takes a
callback (always as the last parameter) instead of returning values.

For example this is an adaptation of a
[web scraping example](http://net.tutsplus.com/tutorials/javascript-ajax/web-scraping-with-node-js/) :

```javascript
var slimer =require('node-slimerJs');
slimer.create(function(err,sl) {
  return sl.createPage(function(err,page) {
    return page.open("http://tilomitra.com/repository/screenscrape/ajax.html", function(err,status) {
      console.log("opened site? ", status);
      page.includeJs('http://ajax.googleapis.com/ajax/libs/jquery/1.7.2/jquery.min.js', function(err) {
        //jQuery Loaded.
        //Wait for a bit for AJAX content to load on the page. Here, we are waiting 5 seconds.
        setTimeout(function() {
          return page.evaluate(function() {
            //Get what you want from the page using jQuery. A good way is to populate an object with all the jQuery commands that you need and then return the object.
            var h2Arr = [],
            pArr = [];
            $('h2').each(function() {
              h2Arr.push($(this).html());
            });
            $('p').each(function() {
              pArr.push($(this).html());
            });

            return {
              h2: h2Arr,
              p: pArr
            };
          }, function(err,result) {
            console.log(result);
            ph.exit();
          });
        }, 5000);
      });
	});
  });
});
```

### slimer.create(callback, options)

`options` is an optional object with options for how to start PhantomJS.
`options.parameters` is an array of parameters that will be passed to PhantomJS
on the commandline.

For example

```javascript
slimer.create(callback, {parameters: {'load-images': 'yes'}})
```

will start slimer as:

```bash
slimerjs --load-images=yes
```

You may also pass in a custom path if you need to select a specific instance
of PhantomJS or it is not present in PATH environment. This can for example
be used together with the [SlimerJS package](https://npmjs.org/package/slimerjs)
like so:

```javascript
slimer.create(callback, {slimerPath: require('slimerjs').path})
```


`options.ignoreErrorPattern` is a regular expression that can be used to silence spurious
warnings generated by slimerJs.

On Mavericks, you can use: `{ignoreErrorPattern: /CoreText/}` to suppress some common annoying font-related warnings.

WebPage Callbacks
-----

All of the WebPage callbacks have been implemented including `onCallback`, and
are set the same way as with the core slimerJs library:

```javascript
page.onResourceReceived = function(response) {
    console.log('Response (#' + response.id + ', stage "' + response.stage + '"): ' + JSON.stringify(response));
};
```

This includes the `onPageCreated` callback which receives a new `page` object.

Properties
-----

Properties on the [WebPage](https://github.com/laurentj/slimerjs)
and [SlimerJs](https://github.com/laurentj/slimerjs)
objects are accessed via the `get()/set()` method calls:

```javascript
page.get('content', function (err,html) {
  console.log("Page HTML is: " + html);
})
page.set('zoomfactor', 0.25, function () {
  page.render('capture.png');
})
```

License - MIT
-----

Copyright (c) 2013 Matt Sergeant

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.



Other
-----
Made by Jean-Baptiste Blanc for [Mango Tools](http://mango.tools).
Made by Adrien Boscfor [Mango Tools](http://mango.tools).
