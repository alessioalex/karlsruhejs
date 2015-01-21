## (Top 10) Mistakes Node.js developers make

About me:

  - Alexandru Vladutu, Node.js junkie [since 2011](https://github.com/alessioalex/Nodetuts/commit/e078c760f4775c246c4000166e9d9418da9fa2e3)
  - Full time JavaScript dev for the past 3 years
  - Wrote a book: [Mastering Web Application Development With Express](https://www.packtpub.com/web-development/mastering-web-application-development-express) (PacktPub)

![pic](https://d255esdrn735hr.cloudfront.net/sites/default/files/imagecache/ppv4_main_book_cover/1083OS_Mastering%20Web%20Application%20Development%20with%20Express_0.jpg)

Talk based on the article https://www.airpair.com/node.js/posts/top-10-mistakes-node-developers-make

--

## Should we even talk about Node?

Well somebody's using it:

- [PayPal](http://www.slideshare.net/lennymarkus/node-js-at-paypal) - developed their own web framework called Kracken
- [Wallmart](http://venturebeat.com/2012/01/24/why-walmart-is-using-node-js/) - developed their own web framework called Hapi.js
- [Yahoo](https://www.joyent.com/developers/videos/node-js-on-the-road-boston-node-js-at-yahoo) - open sourced their frontend && backend web framework [Mojito](http://venturebeat.com/2012/04/02/yahoo-node-open-source-mojito/)
- [Linkedin](http://venturebeat.com/2011/08/16/linkedin-node/) - their mobile website
- [Uber](https://www.youtube.com/watch?v=vujVmugFsKc) - one of the first Node adopters
- [The New York Times](http://m.nytimes.com) - their mobile website
- [Groupon](https://engineering.groupon.com/2013/misc/i-tier-dismantling-the-monoliths/) - switched from a monolithic Rails app to Node
- Others: https://github.com/joyent/node-in-the-industry/tree/master/data

--

## #1 Not using development tools

- automatic server restarts: [nodemon](https://www.npmjs.com/package/nodemon), [node-supervisor](https://www.npmjs.com/package/supervisor), [forever](https://www.npmjs.com/package/forever)
- automatic browser restarts

![pic](https://cldup.com/CaTdFR7phj.png)

[EXAMPLE](https://github.com/alessioalex/airpair-nodejs-mistakes/tree/master/1-dev-tools)

--

## #2 Blocking the event loop

![pic](http://i.stack.imgur.com/YCTgK.png)

- blocking the event loop means blocking everything in Node
- examples include: parsing/decoding a lot of data at once, using sync functions
- the solution is to detect time spent between event loop 'ticks' - [blocked](https://www.npmjs.com/package/blocked)

```js
// double trouble example
var fileContent = fs.readFileSync('really-big-file.java', 'utf8');
response.end(syntaxHighlight(fileContent));
```

[EXAMPLE](https://github.com/alessioalex/airpair-nodejs-mistakes/tree/master/2-block-evtloop)

--

## #3 Executing a callback multiple times

- forgetting to stop function execution after invoking the callback function => headache
- when `if (err)` don't forget to `return`

```js
function () {
  if (err) {
    respond(err, { res: res, proxyUrl: proxyUrl });
  }

  respond(null, { res: res, body: body, proxyUrl: proxyUrl });
}
```

[FULL EXAMPLE](https://github.com/alessioalex/airpair-nodejs-mistakes/blob/master/3-multi-callback/proxy-server.js)

--

## #4 The Christmas tree of callbacks (Callback Hell)

- ugly code exists no matter the language (your duty to tidy it up)
- don't nest async callbacks, use control flow modules (eg. [async](http://npm.im/async)) / [promises](http://strongloop.com/strongblog/promises-in-node-js-with-q-an-alternative-to-callbacks/) / [generators](http://strongloop.com/strongblog/how-to-generators-node-js-yield-use-cases/)

```js
fs.readdir(source, function(err, files) {
  if (err) {
    console.log('Error finding files: ' + err)
  } else {
    files.forEach(function(filename, fileIndex) {
      console.log(filename)
      gm(source + filename).size(function(err, values) {
        if (err) {
          console.log('Error identifying file size: ' + err)
        } else {
          console.log(filename + ' : ' + values)
          aspect = (values.width / values.height)
          widths.forEach(function(width, widthIndex) {
            height = Math.round(width / aspect)
            console.log('resizing ' + filename + 'to ' + height + 'x' + height)
            this.resize(width, height).write(destination + 'w' + width + '_' + filename, function(err) {
              if (err) console.log('Error writing file: ' + err)
            })
          }.bind(this))
        }
      })
    })
  }
})
```

Read more on this topic on [callbackhell.com](http://callbackhell.com/).

--

## #5 Creating big monolithic applications

- [large scale !== monoliths](http://www.richardrodger.com/monolithic-nodejs)
- [the Node way === Unix philosophy](http://blog.izs.me/post/48281998870/unix-philosophy-and-node-js) ~ TL;DR tiny things that do one thing and do it well

        "The secret to building large apps is never build large apps.
        Break your applications into small pieces.
        Then, assemble those testable, bite-sized pieces into your big application"

        - Justin Meyer, author JavaScriptMVC
- extract big functions into separate files and maybe even publish them as separate modules

![pic](https://cldup.com/KJfheHD6_T.png)

- ["How I write modules"](http://substack.net/how_I_write_modules) - by Substack

--

## #6 Poor logging

You should use something better than console.log when coding Node apps, and here's why:

- no need to use util.inspect for large, complex objects
- built-in serializers for things like errors, request and response objects
- support multiple sources for controlling where the logs go
- automatic inclusion of hostname, process id, application name
- supports multiple levels of logging (debug, info, error, fatal etc)
- advanced functionality such as log file rotation, etc

![pic](https://cldup.com/Pmrp9ePGff.png)

[EXAMPLE](https://github.com/alessioalex/airpair-nodejs-mistakes/blob/master/6-logging/server.js)

--

## #7 No tests

![pic](http://dearscience.org/wp-content/uploads/2008/04/xkcd-ideas-are-tested.png)

A project cannot be considered 'done' if it doesn't have any tests.

Some tools to help out:

- testing frameworks: [mocha](https://github.com/mochajs/mocha), [jasmine](https://github.com/pivotal/jasmine), [tape](https://github.com/substack/tape)
- assertion libraries: [chai](https://github.com/chaijs/chai), [should.js](https://github.com/shouldjs/should.js)
- mocks, spies, stubs or fake timers: [sinon.js](http://sinonjs.org/)
- code coverage tools: [istanbul](https://github.com/gotwarlost/istanbul), [blanket](https://github.com/alex-seville/blanket)

[EXAMPLE](https://github.com/alessioalex/mastering_express_code/tree/master/chapter11)

--

## #8 Not using static analysis tools

Tools such as ESLint help solve a huge array of problems, such as:

- possible errors, for example: disallow assignment in conditional expressions, disallow the use of debugger
- enforcing best practices, for example: disallow declaring the same variable more then once, disallow use of arguments.callee
- finding potential security issues, such as the use of eval() or unsafe regular expressions
- detecting possible performance problems
- enforcing a consistent style guide

Create your own tool using an AST (abstract syntax tree) parser: [Esprima](http://esprima.org/) / [Acorn](https://github.com/marijnh/acorn).

--

## #9 Zero monitoring or profiling

What to keep an eye on and why?

- event loop delay, because it will block everything
- memory usage, to spot possible memory leaks
- cpu load, so that you know when to scale || identity potential problems (coupled with the event loop delay)
- cpu profiling, to determine the 'hot code' of your applications (so you know what to optimize)
- other data, such as the number of incoming requests per second for example.

Solution: either use a commercial service such as the ones from [New Relic](http://newrelic.com/nodejs), [StrongLoop](http://strongloop.com/node-js/monitoring/), [Concurix](http://concurix.com/), [AppDynamics](https://docs.appdynamics.com/display/PRO39/APM+Overview+-+Node.js) or roll your own.
The [look](https://github.com/baryshev/look) module could be a good starting point.

An interesting read is ['Performance at rest'](http://hueniverse.com/2014/08/20/performance-at-rest/), a blog post from the Walmart guys.

--

## #10 Debugging with console.log

console.log(something) and then delete it, right?

![http://xkcd.com/1163/](http://imgs.xkcd.com/comics/debugger.png)

*NO!*

- what about the next guy?
- what about selectively debugging portions of your code?

Solution: the [debug](http://npm.im/debug) module does that and more, in a fashion resembling the Node core way.

--

## Questions?

Read more on the ['Top 10 mistakes Node.js developers make'](https://www.airpair.com/node.js/posts/top-10-mistakes-node-developers-make): https://www.airpair.com/node.js/posts/top-10-mistakes-node-developers-make

Email: [alexandru.vladutu@gmail.com](mailto:alexandru.vladutu@gmail.com)

Github: https://github.com/alessioalex

Twitter: https://twitter.com/alessioalex

--

## Credits

Images:

- web comics: [xkcd](http://xkcd.com/)
- event loop: http://stackoverflow.com/questions/14795145/how-the-single-threaded-non-blocking-io-model-works-in-node-js
