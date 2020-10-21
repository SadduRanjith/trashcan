#Modules

Modules are reusable software components that form the building blocks of applications. Modularity satisfies some very important design goals, perhaps the most important of which is simplicity.

When you design an application with a lot of inter-dependencies between different parts, it becomes more difficult to fully understand the impact of your changes across the whole system.

If you design parts of a system to a modular interface contract instead, you can safely make changes without having a deep understanding of all of the related modules.

Another important goal of modularity is the ability to reuse your module in other applications. Well-designed modules built on similar frameworks should be easy to transplant into new applications with few (if any) changes.

JavaScript modules are encapsulated, meaning that they keep implementation details private and expose a public API. Encapsulation also provides protection, meaning that it prevents outside code from interfering with the functionality of the module.

There are several ways to define modules in JavaScript. The most popular and common are the module pattern, CommonJS modules (the inspiration for Node modules), and AMD (Asynchronous Module Definition).


##Principles of Modularity
You can think of modules as small, independent applications that are fully functional and fully testable in their own right. Keep them as small and simple as possible to do the job that they are designed to do.

Modules should be:

 * Specialized
Each module should have a very specific function. The module’s parts should be integral to solving the single problem that the module exists to solve. The public API should be simple and clean.
 * Independent
Modules should know as little as possible about other modules. Instead of calling other modules directly, they should communicate through mediators, such as a central event-handling system or command object.
 * Decomposable
It should be fairly simple to test and use modules in isolation from other modules. It is common to compare them to components in an entertainment system. You could have a disc player, radio receiver, TV, amplifier, and speakers, all of which can operate independently. If you remove the disc player, the rest of the components continue to function.
 * Recomposable
It should be possible to fit various modules together in different ways to build a different version of the same software or an entirely different application.
 * Substitutable
It should be possible to completely substitute one module with another, as long is it supplies the same interface. The rest of the application should not be adversely impacted by the change. The substitute module doesn't have to perform the same function. For example, you might want to substitute a data module that uses REST endpoints as its data source with one that uses a local storage database.

>  The *Open Closed Principle* states that a module interface should be open to extension but closed to modification. Changing an interface that a lot of software relies on can be a daunting task. It’s best if you can avoid making changes to an existing interface once it has been established. However, software should evolve, and as it does, it should be easy to extend existing interfaces with new functionality.

##Interfaces

> Program to an interface, not an implementation.
>	  ---  The GoF, Design Patterns

*Interfaces* are one of the primary tools of modular software design. Interfaces define a contract that an implementing module will fulfill. For instance, a common problem in JavaScript applications is that the application stops functioning if the Internet connection is lost. In order to solve that problem, you could use local storage and sync changes periodically with the server. Unfortunately, some browsers don’t support local storage, so you may have to fall back to cookies or even Flash (depending on how much data you need to store).


Other languages have native support for interfaces that may enforce the requirements of an interface. You might know them as abstract base classes or pure virtual functions.

In JavaScript, there is no distinction between a class, interface, or object instance. There are only object instances, and that simplification is a good thing. You may be wondering, if there’s no native support for interfaces in JavaScript, why bother to write one at all?

When you need multiple implementations of the same interface, it’s good to have a canonical reference in the code that explicitly spells out exactly what that interface is. It’s important to write code that is self-documenting. For example, a storage interface might have a required `.save()` method. You can write the default implementation so that it will throw if you forget to implement it. Since it’s a working prototype, you could even write a sensible default implementation that doesn't throw. In this case, the factory will throw an error if the `.save()` method is not implemented.

Using Stampit to define the factory:

```js
(function (exports) {
	'use strict';
	// Make sure local storage is supported.
	var ns = 'post',
		supportsLocalStorage =
			(typeof localStorage !== 'undefined')
				&& localStorage !== null,
		storage,
		storageInterface = stampit().methods({
			save: function saveStorage() {
				throw new Error('.save() method not implemented.');
			}
		}),
	localStorageProvider = stampit
		.compose(storageInterface)
		.methods({
			save: function saveLocal() {
				localStorage.storage = JSON.stringify(storage);
			}
		}),
	cookieProvider = stampit
		.compose(storageInterface)
		.methods({
			save: function saveCookie() {
				$.cookie('storage', JSON.stringify(storage));
			}
		}),
	post = stampit().methods({
			save: function save() {
				storage[this.id] = this.data;
				storage.save();
				return this;
			},
			set: function set(name, value) {
				this.data[name] = value;
				return this;
			}
	})
	.state({
		data: {
			message: '',
			published: false
		},
		id: undefined
	})
	.enclose(function init() {
		this.id = generateUUID();
		return this;
	}),

	api = post;

	storage = (supportsLocalStorage)
		? localStorageProvider()
		: cookieProvider();

	exports[ns] = api;
}((typeof exports === 'undefined')
		? window
		: exports
));


$(function () {
	'use strict';
	var myPost = post().set('message', 'Hello, world!');
	
	test('Interface example', function () {
		var storedMessage,
			storage;
		
		myPost.save();
		storage = JSON.parse(localStorage.storage);
		storedMessage = storage[myPost.id].message;
		
		equal(storedMessage, 'Hello, world!',
			'.save() method should save post.');
	});
});
```

The important part here is the storage interface. First you create the factory (using Stampit in this case, but it can be any function that returns an object that demonstrates the interface). This example has only one method, `.save()` :

```js
storageInterface = stampit().methods({
	save: function saveStorage() {
		throw new Error('.save() method not implemented.');
	}
}),
```

Create a concrete implementation that inherits from the interface. If the interface is especially large, you might want to put each implementation in a separate file. In this case, that isn't required. Notice that the concrete implementations are using named function expressions. During debugging, you’ll be able to see which concrete implementation you’re using by looking at the function name in the call stack.

Stampit’s `.compose()` method allows you to inherit from any number of sources, and it returns a stamp that you can further extend with `.methods()` , `.state()` , or `.enclose()` . You can use those features to flesh out the concrete implementations.

There are alternative ways to define interfaces in JavaScript. You could simply define object literals and use something like `jQuery.extend()` to build the desired concrete implementation. The downside there is that you won’t be able to take advantage of prototype delegation or data privacy.

You could also define the concrete implementations as prototype objects and then pass the appropriate prototype into Stampit or Object.create() during the final step. But stamps give you a lot of composability.

##Module Pattern

Modules in the browser use a wrapping function (IIFE) to encapsulate private data in a closure.

Most libraries, such as jQuery and Underscore, are encapsulated in modules.

The module pattern encapsulates module contents in an immediately invoked function expression (IIFE) and exposes a public interface by assignment. Douglas Crockford gave the module pattern its name, and Eric Miraglia popularized it

The original module pattern assigns the result of the IIFE to a predefined namespace variable. The problem with this pattern is that you have no choice but to expose at least one global variable for each module. If you’re building an application with a lot of modules, that is not a good option. Instead, it’s possible to pass in an existing variable to extend with your new module.

Here, that variable is called `exports` , for compatibility with CommonJS. If `exports` does not exist, you can fall back on `window` :

```js
(function (exports) {
	var api = {
		moduleExists: function test() {
			return true;
		}
	};
	$.extend(exports, api);
}((typeof exports === 'undefined') ?
	window : exports));

test('Pass in exports.', function () {
	ok(moduleExists(),
		'The module exists.');
});
```

A common mistake is to pass in a specific application namespace inside your module’s source file (instead of using a globally defined exports). Normally, that will not harm anything. However, if you wish to reuse the module in another application, you’ll have to modify the source of the module in order to attach it to the correct namespace.
Instead, you can pass your application object in as `exports` . It’s common in client-side code to have a build step that wraps all of your modules together in a single outer function. If you pass your application object into that wrapper function as a parameter called `exports` , you’re in business

```js
var app = {};

(function (exports) {
	(function (exports) {
		var api = {
			moduleExists: function test() {
				return true;
			}
		};
		$.extend(exports, api);
	}((typeof exports === 'undefined') ?
		window : exports));
}(app));

test('Pass app as exports.', function () {
	ok(app.moduleExists(),
		'The module exists.');
});
```

An upside to this version of the module pattern is that the code you write with it can be easily run and tested in Node. From this point on, when the module pattern gets mentioned, this is the version that should spring to mind. **Its ancestor is obsolete**.


##Asynchronous Module Definition

On the client side, there is often a need to load modules asynchronously at runtime in order to avoid the need for the client to download the entire codebase every time the app is loaded. Imagine you have an app, the core of the application is always need to be loaded. However, you also have a large module that is completely unused 99%  of time. What you need is a way to defer the loading of the module until the user actually needs it. You could just make it a separate page, but then the user has to endure a page refresh. It would be a much better experience to keep this all on one page, with no new page load. The module pattern doesn’t solve this problem. CommonJS modules (like those used by Node) are not asynchronous. In the future, JavaScript will have a native module system that works in the browser, but it’s very young technology that may not be widely implemented in all major browsers in the foreseeable future.

AMD is an interim solution to the problem. It works by wrapping the module inside a function called `define()` . The call signature looks like this:

```js
define([moduleId,] dependencies, definitionFunction);
```

The `moduleId` parameter is a string that will identify the module. However, this parameter has fallen out of favor because changes in the application or module structure can necessitate a refactor, and there really is no need for an ID in the first place. If you leave it out and begin your define call with the dependency list, you’ll create a more adaptable *anonymous module*:

```js
define(['ch04/amd1', 'ch04/amd2'],
	function myModule(amd1, amd2) {
		var testResults = {
				test1: amd1.test(),
				test2: amd2.test()
			},
			// Define a public API for your module:
			api = {
				testResults: function () {
					return testResults;
				}
			};
		return api;
	});
```

To kick it off, call `require()` . You specify dependencies similar to `define()` :

```js
require(['ch04-amd'], function (amd) {
	var results = amd.testResults();
	
	test('AMD with Require.js', function () {
		equal(results.test1, true,
			'First dependency loaded correctly.');
		equal(results.test2, true,
			'Second dependency loaded correctly.');
	});
});
```

> Use anonymous modules wherever possible in order to avoid refactors.

The problem with this approach is that if you define your module this way, it can only
be used with an AMD loader, such as Require.js or Curl.js. However, it is possible to get the best of both AMD and module pattern modules. Simply create a module using the module pattern, and at the end of the wrapping function, add this:

```js
if (typeof define === 'function') {
	define([], function () {
		return api;
	});
}
```

That way, it will be possible to load your module asynchronously if you want to, but your module will still function properly if it’s loaded with a simple script tag, or compiled together with a bunch of other modules. This is the pattern that jQuery uses to add AMD loader support. The only trouble with this pattern is that dependency timing is a little more complicated. You’ll need to ensure that your dependencies have loaded before you try to use them.

UMD (Universal Module Definition) is another alternative. My favorite way to create a UMD is to bundle the module using Browserify in standalone mode.

###Plug-Ins

Loader plug-ins are an AMD mechanism that allow you to load non-JavaScript resources, such as templates and CSS. Require.js supplies a `text!` plug-in that you can use to load your HTML templates. To use a plug-in, simply prefix the file path with the plug-in name:

```js
'use strict';

require(['ch04/mymodule.js', 'text!ch04/mymodule.html'],
	function (myModule, view) {
		var container = document.body,
			css = 'ch04/mymodule.css';
		
		myModule.render(container, view, css);

		test('AMD Plugins', function () {
			equal($('#mymodule').text(), 'Hello, world!',
				'Plugin loading works.');
		});
});
```

Here’s what *mymodule.js* looks like:

```js
define(function () {
	'use strict';
	var api = {
		render: function render(container, view, css) {
			loadCss('ch04/mymodule.css');
		
			$(view).text('Hello, world!')
				.appendTo(container);
		}
	};

	return api;
});
```

And the *mymodule.html* template:
	
	<div id="mymodule"></div>

The stylesheet is simple:

```js
#mymodule {
	font-size:2em;
	color: green;
}
```

Note that the CSS is not loaded as a plug-in. Instead, the URL is assigned to a variable and passed into the `.render()` method for manual loading. The `loadCSS()` function looks like this:

```js
function loadCss(url) {
	$('<link>', {
		type: 'text/css',
		rel: 'stylesheet',
		href: url,
	}).appendTo('head');
}
```

This obviously isn't an ideal solution, but as of this writing, there is no standard recommended `css!` plug-in for Require.js. There is a `css!` plug-in for Curl.js, and you might want to try Xstyle. Use them the same way you define the HTML template.

AMD has a couple of serious drawbacks. First, it requires you to include a boilerplate wrapper function for every module. Second, it forces you to either compile your whole application in a compile step, or asynchronously load every single module on the client side, which, in spite of advertising to the contrary, could actually slow down the load and execution of your scripts due to simultaneous download limits and connection latency.

I recommend the precompile solution over the asynchronous load solution, and as long as you’re doing that anyway, you may as well be using the simplified CommonJS syntax and a tool like [Browserify](http://browserify.org/).

##Node-Style Modules

*CommonJS* is a set of standards for JavaScript environments that attempts to make JavaScript engine implementations more compatible. CommonJS modules specify an API that modules use to declare dependencies. CommonJS module implementations are responsible for reading the modules and resolving those dependencies.

Before Node.js, there were several other attempts to run JavaScript on the server side, as far back as the late 1990s. Both Netscape and Microsoft allowed JavaScript-compatible scripting in their server environments. However, few people used those capabilities. The first server-side JavaScript solution to gain any real traction was Rhino, but it was too slow and cumbersome to build web-scale applications on top of.
CommonJS was created to solve that problem.

Node-style modules are essentially an implementation of the CommonJS module specification.
The CommonJS module system has a much simpler syntax than either the module pattern or AMD. In CommonJS, the file is the module. There is no need for a function wrapper to contain the scope, because each file is given its own scope. Modules declare  dependencies with a synchronous `require()` function. That means that execution is blocked while the required module is being resolved, so it’s safe to start using the module immediately after you require it.

First, assign to keys on the free variable `exports` to declare your module’s public API:

```js
'use strict';
var foo = function foo () {
	return true;
};

exports.foo = foo;
```

Then use `require()` to import your module and assign it to a local variable. You can specify the name of a module in the list of installed Node modules or specify a path to the module using relative paths.

For example, if you want to use the Flatiron HTTP module, you can `require()` by name (from the Flatiron.js docs):

```js
var flatiron = require('flatiron'),
	app = flatiron.app;

app.use(flatiron.plugins.http, {
	// HTTP options
});

//
// app.router is now available. app[HTTP-VERB] is also available
// as a shortcut for creating routes
//
app.router.get('/version', function () {
	this.res.writeHead(200, { 'Content-Type': 'text/plain' })
	this.res.end('flatiron ' + flatiron.version);
});

app.start(8080);
```

Or specify a relative path:

```js
'use strict';
var mod = require('./ch04-modules.js'),
	result = (mod.foo() === true) ? 'Pass:' : 'Fail:';

console.log(result, '.foo() should return true.');
```

Here, `console.log()` is used to simulate a unit testing framework, but there are several better alternatives for Node, including [tape](https://github.com/substack/tape) and [nodeunit](https://github.com/caolan/nodeunit).


##npm

Node package manager (npm) is a package manager that comes bundled with Node. Contrary to popular belief, npm is not an acronym, according to the npm FAQ, so it’s technically incorrect to capitalize the letters. It provides an easy way to install modules for your application, including all required dependencies. Node relies on the *package.json* specification for package configuration. It’s common to use npm to pull in all of your project’s dependencies on the server side, but there is also a movement forming to use npm to pull in dependencies for the client side as well.

npm has a number of well-documented directives, the ones commonly needed to be modified in order to get a typical app up and running are:

 * `name` - Name of the package
 * `version` - Package version number (npm modules must use semantic versioning).
 * `author` - Some information about the author.
 * `description` - A short description of the package.
 * `keywords` - Search terms to help users find the package.
 * `main` - The path of the main package file.
 * `scripts` - A list of scripts to expose to npm; most projects should define a “test” script that runs with the command npm test , which you can use to execute your unit tests.
 * `repository` - The location of the package repository.
 * `dependencies`, `bundledDependencies` - Dependencies your package `require()`.
 * `devDependencies` - A list of dependencies that developers will need in order to contribute.
 * `engines` - Specifies which version of node to use.

To build a Node app, you’ll need to create a server. Easiest way to do that is to use Express, a minimal application framework for Node. Before you begin, you should look at the latest version available :
`$ npm info express`

```js
// package.json
{
	"name": "simple-express-static-server",
	"version": "0.1.0",
	"author": "Sandro Padin",
	"description": "A very simple static file server. For development use only.",
	"keywords": ["http", "web server", "static server"],
	"main": "./server.js",
	"scripts": {
		"start": "node ./server.js"
	},
	"repository": {
		"type": "git",
		"url": "https://github.com/spadin/simple-express-static-server.git"
	},
	"dependencies": {
		"express": "3.0.x"
	},
	"engines": {
		"node": ">=0.6"
	}
}
```

Notice that the Express version is specified as 3.0.x . The x acts like a wildcard. It will install the latest 3.0 version, regardless of the patch number. It’s another way of saying, “give me bug fixes, but no API changes.”

Now that your package and dependencies are declared, return to the console and run:
`$ npm install`

When you run `npm install` , it will look at *package.json* and install all of the dependencies for you, including those that were declared by the `express` package.

##ES6 Modules

None of the module systems discussed so far are included in the official ECMAScript specifications. In the future, a module system will be built in to all ECMA-compliant JavaScript engines.

The ES6 module specification isn't widely usable yet, but when it is, it should be possible to write modules and compile using build tools to fall back on the module pattern, AMD, and CommonJS where required. ES6 module build tools are already beginning to appear, but you should probably wait a bit longer before you start to rely on them for your production applications.

ES6 modules are similar to Node modules, with subtle differences. Instead of `module.exports`, you get the `export` keyword:

```js
var foo = function foo () {
	return true;
};

export { foo };
```

Instead of `require()`, there’s a new `import` keyword:

```js
import { foo } from 'es6-foo';

let test = foo() === true ? 'Pass' : 'Fail';

console.log(test, 'foo() should import and return true.');
```

In both cases, you can include multiple variables inside the import/export brackets, but I recommend that you try to export one thing from each module. Keep it simple. You can also rename the imported variable using the keyword `as`:
`import { foo as bar } from 'es6-foo';`

When you’re only exporting one thing, you should make it the default export:

```js
var foo = function foo() {
	return true;
};

export default foo; // No braces required
```

Now you can use the simplified import:
`import foo from 'es6-foo'; // No braces required`

If you are interested in experimenting with ES6 modules today, you can use them with
the Browserify transform, [es6ify](https://github.com/thlorenz/es6ify).


##Building Client-Side Code with CommonJS, npm, Grunt, and Browserify

There are a variety of competing standards for module management with JavaScript, but due to the popularity of Node, by far the most established is npm + CommonJS. There have been efforts to create a similar combination of features for the client side, including AMD, Yeoman, and Bower, but none of them can compete with the simplicity and package availability of npm and CommonJS modules.

Let's take a look at how you might build a minimal client-side guest-list app:

###Defining the App

Most new software projects use agile development methods to produce quick software creation and enhancement iterations. In agile software, the time between releases is measured in days or weeks rather than months or years. To learn more about agile, see 'The Art of Agile Development: Pragmatic guide to agile software development by James Shore'.

Typically when you set out to build an app, you’ll start with a list of *user stories*. You can use those scenarios to come up with *acceptance criteria*, which can be further distilled into functional unit tests.

A user story is a short, simple description of some action that your user might want to perform. For example, “As an event organizer, I want to check arriving guests against the guest list.” User stories should always contain a role along with the story.

To express this example story in code, you’ll need a list element with clickable links in list items. The unit tests might look something like:

```js
// guestlist/test/test.js
var $list = $('#guestlist-view'),
	checkedinClass = 'icon-check',
	guestSelector = '.guest';

test('Guestlist', function () {
	ok($list.length,
		'List element should have guests.');
});

test('Guests', function () {

	// Grab the first guest from the list
	var $guest = $($list.find(guestSelector)[0]),
		guestExists = !!$guest[0];
		
	// Simulate click
	$guest.click();
	
	ok($guest.hasClass(checkedinClass),
		'Should be checked on click');

	$guest.click();

	// To avoid a false positive, make sure
	// you have a guest element to test against.
	ok(guestExists && !$guest.hasClass(checkedinClass),
		'Should toggle off when clicked again');
});
```

You’ll need to include that in your QUnit HTML file:

```html
<!-- guestlist/test/index.html -->
<html>
	<head>
	</head>
	<body>
		<section>
		<h1 id="qunit-header">QUnit Test Suite</h1>
		<h2 id="qunit-banner"></h2>
		<div id="qunit-testrunner-toolbar"></div>
		<h2 id="qunit-userAgent"></h2>
		<ol id="qunit-tests"></ol>
		</section>
		
		<section id="container"></section>
		
		<script src="jquery.js"></script>
		<script src="qunit.js"></script>
		<script src="../public/app.js"></script>
		<script src="test.js"></script>
		<link href="qunit.css" rel="stylesheet"></style>
	</body>
</html>
```

These tests will obviously fail at first. That’s a good thing. If you can see your tests fail before you make them pass, you eliminate the possibility that your test results are showing you false positives. TDD forces you to write more modular, testable code and makes APIs easier to use because you’re not worried about implementation details when you write your tests—you won’t accidentally leak implementation details into your APIs.

###Feature Implementation

Normally, it’s wise to break the code up into separate concerns. In this case, you’ll separate presentation concerns from data management. The data will come from a stubbed model module named `guestlistmodel.js`. For now, it will just return some hard-coded names. You can implement loading and saving later, as shown:

```js
// guestlist/src/guestlistmodel.js
var api = {
		load: function load() {
			return [
				'Jimi Hendrix',
				'Billie Holiday',
				'Nina Simone',
				'Jim Morrison',
				'Duke Ellington',
				'John Bonham'
			];
		}
	};

module.exports = api;
```

The code to manipulate the list DOM elements will go into a file named `guestlistview.js`:

```js
// guestlist/src/guestlistview.js
var $ = require('jquery-browserify'),
	checkedinClass = 'icon-check',
	listClass = 'dropdown-menu',
	guestClass = 'guest',

	toggleCheckedIn = function toggleCheckedIn(e) {
		$(this).toggleClass(checkedinClass);
	},

	$listView = $('<ol>', {
		id: 'guestlist-view',
		'class': listClass
	}).on('click', '.' + guestClass, toggleCheckedIn),

	render = function render(guestlist) {
		$listView.empty();

		guestlist.forEach(function (guest) {
			$guest = $('<li class="' + guestClass + '">' +
				'<span class="name">' + guest +
				'</span></li>');
			$guest.appendTo($listView);
		});

		return $listView;
	},

	api = {
		render: render
	};

module.exports = api;
```

This is the file doing all the work. First, it uses `.require()` to get a reference to jQuery and sets a few self-documenting variable names. The `toggleCheckedIn()` function is an event handler for the `click` event.

> By delegating to the parent-ordered list element, you can replace, remove, and add children to the list without worrying about removing and replacing event listeners. If you’re hooking up listeners to DOM elements, most of the time, delegating to an ancestor is the right approach.

The `.render()` method takes an array of guest names, iterates over each one, and adds a corresponding list item to the `$listView` element. It then returns the rendered element to the calling function.

The rest of the code simply defines the public API and exposes it via CommonJS.

> Some developers will intersperse `module.exports` assignments throughout a module. I find that having a single `module.exports` at the bottom of the file more clearly documents the module’s public API.

So far, the modules don’t know about each other, so there’s no way for them to do any work. To bring all the pieces together and manage the initial render, you’ll need a higher level abstraction to kick things off. Enter *app.js*,

```js
// guestlist/src/app.js
var $ = require('jquery-browserify'),
	guestlistModel = require('./guestlistmodel'),
	guestlistView = require('./guestlistview'),
	$container = $('#container');

$(function init() {
	var guestlistData = guestlistModel.load();
	$guestlist = guestlistView.render(guestlistData);
	$container.empty().append($guestlist);
});
```

This one should be fairly simple to follow. It uses `require()` to reference `guestlistModel` and `guestlistView` , loads the `guestlist`, passes the data into `guestlistView.render()` , and adds it to the `container` element.

> The `.append()` line at the end of the `init()` function calls jQuery’s `.empty()` method first for a couple of important reasons. First, if there’s anything in that space already, it should be replaced, but it also releases references to event listeners so that the memory can be garbage collected. This is a better strategy than simply calling `.html()`.

###Bundling and Deployment

None of this is going to work yet, because the modules all need to be compiled together in order of their dependencies. For that, you’ll need Browserify.

Browserify is a Node module that makes CommonJS modules work in the browser, using a server-side build step. The `browserify` command is available to kick off the bundle:
`$ browserify src/app.js -o public/app.js`

That’s a bit too manual, though. You’ll want to use grunt to automate the build. That way you can lint, build, and run your unit tests all in one step. Start with *package.json*:

```js
{
	"name": "guestlist",
	"version": "0.1.0",
	"author": "Eric Elliott",
	"description": "A handy tool for bouncers.",
	"keywords": ["party", "guestlist"],
	"main": "dist/app.js",
	"scripts": {
		"test": "grunt test"
	},
	"dependencies": {
		"jquery-browserify": "*"
	},
	"devDependencies": {
		"traverse": "*",
		"grunt": "*",
		"grunt-browserify": "*",
		"browserify": "*"
	},
	"engines": {
		"node": ">=0.6"
	}
}
```

Since you’ll be deploying this app, you should consider all of the code it uses to be part of the app, including its dependencies. You don’t want those dependency versions shifting around under your feet during your deploy step. The less uncertainty and moving parts you have in a deploy step the better. For that reason, you’re going to want to check in your `node_modules` directory (don’t add it to `.gitignore`).

The "`*`" indicates that you want to use the latest version. This practice assumes that you have been religiously unit testing your code, that your deploy does not require its own `npm install` (which I strongly discourage), and that you block deploys when integration tests fail.

You’ll also need a gruntfile. Versions after 0.4.0 expect *gruntfile.js*:

```js
// guestlist/grunt.js

/*global module*/
module.exports = function(grunt) {
	'use strict';
	grunt.initConfig({
	
		// Read package.json into an object for later
		// reference (for example, in meta, below).
		pkg: '<json:package.json>',
		
		meta: {
	
			// A template to add to the top of the bundled
			// output.
			banner: '\n/*! <%= pkg.title || pkg.name %> ' +
			'- v<%= pkg.version %> - ' +
			'<%= grunt.template.today("yyyy-mm-dd") %>\n ' +
			'<%= pkg.homepage ? "* " + pkg.homepage + "\n' +
			' *\n " : "" %>' +
			'* Copyright (c) ' +
			'<%= grunt.template.today("yyyy") %> ' +
			'<%= pkg.author.name %>;\n' +
			' * Licensed under the <%= ' +
			'_.pluck(pkg.licenses, "type").join(", ") %>' +
			' license */'
		},
		
		// Specify which files to send through JSHint.
		lint: {
			all: ['./grunt.js', './src/**/*.js',
				'./test-src/test.js']
		},
		
		// JSHint configuration options.
		jshint: {
			browser: false,
			node: true,
			strict: false,
			curly: true,
			eqeqeq: true,
			immed: true,
			latedef: true,
			newcap: true,
			nonew: true,
			noarg: true,
			sub: true,
			undef: true,
			unused: true,
			eqnull: true,
			boss: false
		},
	
		// Specify test locations for QUnit.
		qunit: {
			browser: ['test/index.html']
		},
	
		// Configuration for browserify.
		browserify: {
			"public/app.js": {
				requires: ['traverse'],
				entries: ['src/**/*.js'],
				prepend: ['<banner:meta.banner>'],
				append: [],
				hook: function () {
					// bundle is passed in as first param
				}
			}
		}
	});
	
	// Load browserify tasks. Needed for bundling.
	grunt.loadNpmTasks('grunt-browserify');
	
	// Setup command line argument tasks. For e.g.:
	// $ grunt # executes lint, browserify, qunit
	// $ grunt test # runs qunit task, only.
	grunt.registerTask('default', 'lint browserify qunit');
	grunt.registerTask('install', 'browserify');
	grunt.registerTask('test', 'qunit');
};
```

The Grunt configuration file is just a Node JavaScript module, so you can write functions in it, evaluate expressions, and so on.

Browserify requires a little extra configuration sometimes, described in `grunt-browserify` documentation. Just don’t forget to load the grunt tasks with `grunt.loadNpmTasks('grunt-browserify')` .

The `.registerTask()` calls make grunt command-line arguments available. `grunt` runs all the`defualt` tasks if no argument is passed, and `grunt tests` will only run `tests`.

The Guestlist code employs a custom icon font for the checkbox. The font was created using a free custom font app called IcoMoon.

```css

// Icon font CSS

@font-face {
	font-family: 'guestlist';
	src:url('fonts/guestlist.eot');
	src:url('fonts/guestlist.eot?#iefix')
			format('embedded-opentype'),
		url('fonts/guestlist.svg#guestlist')
			format('svg'),
		url('fonts/guestlist.woff') format('woff'),
		url('fonts/guestlist.ttf') format('truetype');
	font-weight: normal;
	font-style: normal;
}

[class^="icon-check"]:before,
[class*=" icon-check"]:before {
	font-family: 'guestlist';
	font-style: normal;
	font-size: .75em;
	speak: none;
	font-weight: normal;
	-webkit-font-smoothing: antialiased;
}
.icon-check:before {
	content: "\e000";
}
.dropdown-menu .icon-check .name {
	padding-left: 1em;
}
```


> Written by Chirag Jain