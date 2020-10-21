[TOC]

#Functions

##Guidelines

 * DRY (Don't Repeat Yourself)
 * DOT (Do One Thing)
 * KISS (Keep It Simple Stupid)
 * Less is More

The best way to ensure that your program contains few unintentional side effects is to avoid them in your functions. If your function operates on outside variables, return a copy instead of the original.

A *pure function* has no side effects. It does not alter existing variables or program state in any way, and always returns the same value given the same inputs. *REST* works this way.

##Function Definition

###Function Declaration

```js
function foo() {
	return 0;
}
```

####Disadvantages
 * Functions cannot be declared conditionally.
 * Encourages large piles of loosely related functions in modules.

###Function Expression

```js
var bar = function () {
	return 0;
}
```

####Advantages
* Allows conditional declaration.
* Can use the function almost everywhere where a variable can be used.
* Can be organized easily using object literals.

####Disadvantages
* Functions are anonymous unless explicitly specified.

> Don't use the `Function()` constructor. Its equivalent to passing a string to `eval()`!

###Named Function Expression

```js
var lightbulbAPI = {
	toggle: function toggle() {},
	getState: function getState() {},
	off: function off() {},
	on: function on() {},
	blink: function blink() {}
};
```

Same as *Function Expressions* but have a name that can be used inside the function for *recursion*.
Has advantages of both Named expressions and function declaration.

> IE8 treats named function expressions as function declarations!

##Lambdas

A lambda is a function that is used as data.

> A function that adds functionality to another function is called a function decorator.

Lambdas are frequently confused with anonymous functions, closures, first-class functions, and higher order functions. The concepts are all similar, but they mean different things.

Lambdas are treated like data that can be passed around as inputs and outputs between other functions, regardless of whether or not they are named.

Not all lambdas are closures, and not all closures are lambdas.

A **Closure** is created when a function references data that is contained outside the function scope.

**First class functions** means that you can use them anywhere you would use a value.

**Higher-order functions** are functions that consume or return functions as data.

##Immediately Invoked Function Expression (*IIFE*)

Pronounced "*iffy*"

*Self Invoked Anonymous Functions* is a misnomer because it implies that the function is recursive.

This technique is often used to create a new scope to encapsulate modules.

Before the IIFE became popular, a common technique was to assign names to the object prototype. This method leads to a lot of repetition. The IIFE lets you encapsulate scope, so you can assign to regular variables, instead of just the prototype. This gives you more flexibility and the ability to hide state inside the function closure.

```js
(function () {
	// create objects using normal variables
})();
```

##Method Context

Two ways for *Method Invocation*.
`object.methodName()` (*dot notation*)
`object['methodName']()` (*square bracket notation*)

When you invoke a method with dot notation, you have access to the object’s properties using `this` .

> Unless you use method invocation (dot notation or square bracket notation), `this` generally refers to the global object. Assignments to properties on `this` will pollute the global namespace. It’s better to make sure you have a valid object before trying to use `this` in your function if you expect it might be invoked on its own.

`.call()` method shared by all functions allows you to call any method or function on any object. In other words, it sets `this` inside the method to refer to the object of your choosing. The signature is:

```js
someMethod.call(context, argument1, argument2, ...);
```

where `context` is the object you want `this` to refer to. If you need to pass an array of arguments, use `.apply()` instead:

```js
someMethod.apply(context, someArray);
```

###`Function.prototype.bind()`

There are 2 limitations with `.call()` and `.apply()` as follows:

 * The context binding is not impermanent i.e. they must be used each time the function has to be called.
 * The context object need to be accessed from the current scope.

`.bind()` method is used to permanently set the value of `this` inside the target function to the passed in context object. It's part of ECMAScript 5, hence a shim is needed in older browsers.

##Function Scope

*Variable scope* is the section of code in which the identifier refers to the expected value. Most C-family languages have *block scope*, but `var` is not block scoped. `var` uses *function scope* instead.

Block scope will be available using the `let` keyword in ES6. But for now it is not implemented in all the browsers.

> Desire to use `let` is a good code smell. It indicates that it may be time to break the function into smaller pieces.

##Hoisting

*Hoisting* is the word most commonly used to describe the illusion that all variable declarations are “hoisted” to the top of the containing function. Technically, that’s not exactly how it happens, but the effect is the same.

JavaScript builds its execution environment in 2 passes:

 1.  **declaration pass** sets up the runtime environment, where it scans for all variable and function declarations and creates the identifiers.
 2. The second pass is the **execution pass**. After the first pass, all declared functions are available, but variables are still undefined.

```js
var x = 1;
(function () {
	console.log(x);
	var x = 2;
}());
```

In effect, this is how JavaScript interprets the code:

```js
var x = 1;
(function () {
	var x; // Declaration is hoisted and x is undefined.
	console.log(x);
	x = 2; // Initialization is still down here.
}());
```

In case of functions both the identifier and the function body are hoisted, whereas the value `2` was not hoisted along with `x`.
This is only in the case of function declaration, function expressions act like variable assignments.

> If you declare all of your variables at the top of your function, and define your functions before you try to use them, you’ll never need to worry about any of this. This practice can substantially reduce scope-related bugs.

##Closures
In a nutshell, a closure stores function state, even after the function has returned. To create a closure, simply define a function inside another function and expose it.

Because the closure variables in the outer function are only in scope within the containing function, you can’t get at the data except through its *privileged methods*. In other languages, a privileged method is an exposed method that has access to private data. In JavaScript, any exposed method defined within the closure scope is privileged.

Example:
###Async Recursion

```js
(function () {
	var arr = [],
		count = 1,
		delay = 20,
		timer,
		complete;
	timer = function timer() {
		setTimeout(function inner() {
			arr.push(count);
			if (count < 3) {
				count += 1;
				timer();
			} else {
				complete();
			}
		}, delay);
	};
	asyncTest('Closure with setTimeout.', function () {
		complete = function complete() {
			equal(
				arr.join(','), '1,2,3',
				'arr should be [1,2,3]'
			);
			start();
		};
		timer();
		equal(
			arr.length, 0,
			'array should be empty until the first timout.'
		);
	});
}());
```

##Method Design

Several techniques exist in JavaScript to design method APIs. JavaScript supports named parameter lists, function polymorphism, method chaining, and lambda expressions.

Don't forget!

 - KISS
 - DOT
 - DRY

###Named Parameters
The number of variables you pass into a function is called its *arity*.
Function arity must be kept small because large parameter list makes it hard to remember the parameters and the order in which they are to be passed.

`extend` method provided by popular libraries makes it easy to keep arity low in typical cases in which a wide range of parameters is required for example, to initialize the configuration of a module or create a new object instance.

###Function Polymorphism

In computer science, *polymorphism* means that something behaves differently based on context.

Polymorphic functions behave differently based on the parameters you pass into them. In JavaScript, those parameters are stored in the array-like `arguments` object, but it’s missing useful array methods.

You can borrow the `.slice()` method from the `Array` prototype using a technique called *method delegation*. You delegate the `.slice()` call to the `Array.prototype` object.

```js
var args = Array.prototype.slice.call(arguments, 0);
//shorthand
var args = [].slice.call(arguments, 0);
//Creating an empty array does not have a noticeable effect on performance.
```

Now `args` has access to all `Array.prototype` methods and contains all the function arguments. Hence `.shift()` method can be used to get the first argument.

####Method dispatch

*Method dispatch* is the mechanism that determines what to do when an object receives a message. 
JavaScript does this by checking if the method exists on the object if not it climbs its prototype chain until the method is found or the chain ends.
This is also known as *behavior delegation* in delegation-based prototypal languages like JavaScript.

*Dynamic dispatch* enables polymorphism by selecting the appropriate method to run based on the parameters that get passed into the method at runtime.
Some languages have special syntax to support dynamic dispatch. In JavaScript, you can check the parameters from within the called method and call another method in response.

###Generics & Collection Polymorphism
*Generic programming* is a style that attempts to express algorithms and data structures in a way that is type agnostic. Generic programming typically starts with one or more type-specific implementations, which then get lifted (abstracted) to create a more generic version that will work with a new set of types.

Generics do not require conditional logic branching to implement an algorithm differently based on the type of data passed in. Rather, the datatypes passed in must support the required features that the algorithm needs in order to work. Those features are called *requirements*, which in turn get collected into sets called *concepts*.

Generics employ *parametric polymorphism*, which uses a single branch of logic applied to generic type parameters. In contrast, *ad-hoc polymorphism* relies on conditional branching to handle the treatment of different parameter types (either built into the language with features like dynamic dispatch or introduced at program design time).

Generic programming is particularly relevant to functional programming because functional programming works best when a simple function vocabulary can express a wide range of functionality, regardless of type.

In most languages, generic programming is concerned with making algorithms work for different types of lists. In JavaScript, any collection (array or object) can contain any type (or mix of types), and most programmers rely on duck typing to accomplish similar goals. (If it walks like a duck and quacks like a duck, treat it like a duck. In other words, if an object has the features you need, assume it’s the right kind of object and use it.) Many of the built-in object methods are generics, meaning that they can operate on multiple types of objects.

JavaScript supports two types of collections: objects and arrays. The principle difference between an object and an array is that one is keyed with names and the other sequentially with numbers. Objects don’t guarantee any particular order; arrays do. Other than that, both behave pretty much the same. It often makes sense to implement functions that work regardless of which type of collection gets passed in.

Since arrays are already objects in JavaScript, hence a lot of functions designed to act on JavaScript objects are truly generic in that they will also work for arrays without any specialized logic (assuming that the array has all of the required features).

Collection polymorphism is a very useful tool for code reuse and API consistency. Many library methods in both jQuery and Underscore work on both objects and arrays.

JavaScript 1.6 introduced a number of new built-in array and string generics such as `.every()` Array method on strings.

You can also use string methods on numbers! For example `.indexOf()`.

###Method Chaining & Fluent APIs

*Method chaining* is using the output of one method call as the context of the next method call. This is used extensively in jQuery.

One of the primary benefits of method chaining is that it can be used to support fluent APIs. In short, a fluent API is one that reads like natural language i.e. they use real verbs as method calls.

jQuery is a great example of a fluent API.

Chaining has its disadvantages. It can encourage you to do too much in a single line of code, it can encourage you to write too much procedural code, and it can be difficult to debug. It’s tough to set a breakpoint in the middle of a chain.

The debugging can be made easy by capture the output at any step in the chain with a variable assignment and resume the chain by calling the next method on that variable. In fact, you don’t have to chain at all to use a fluent API.

The salient point is that fluent methods are made to work together to express functionality in the same way that words in a language work together to express ideas. That means that they output objects with methods that make sense to call on the resulting data.

Building a fluent API is a lot like building a miniature domain-specific language (DSL).

> It’s easy to go too far with fluent APIs. In other languages, fluent APIs are frequently used to configure a new object. Since JavaScript supports object-literal notation, this is almost certainly a bad use of fluency in JavaScript.

> Fluency can also lead to unnecessary verbosity. For example, the *Should.js* API encourages you to write long sentences with strung-together dot notation access. Keep it simple.

##Functional Programming

Functional programming is a style of programming that uses higher-order functions (as opposed to objects and data) to facilitate code organization and reuse.

Higher order functions are very powerful code reuse tools. They can be used to *abstract algorithms from datatypes*. Without this, you might create a special function to operate on a collection of one type, and a similar, but slightly different function to operate on another.

> A series of functions that do essentially the same thing and differ only in the type of data they operate on is a serious code smell. You’re violating the DRY principle, one of the most valuable guidelines available to you in software design.

Higher-order functions are very commonly used to *abstract list iteration boilerplate from algorithm implementation*. For example `.sort()` and `.forEach()`.

Another common use of higher order functions is to support *partial application and currying*.

###Stateless Functions (aka Pure Functions)
*Pure functions* are stateless. This means that they do not use or modify variables, objects, or arrays that were defined outside the function. Given the same inputs, stateless functions will always return the same output. Stateless functions won’t break if you call them at different times.

That feature is particularly useful in JavaScript applications, because you often need to manage a lot of asynchronous events. Consequently, time becomes a major factor in code organization.

Because you don’t have to worry about clobbering shared data, stateless functions are great for high-concurrency applications.

Stateless functions can be chained together for stream processing (i.e., enumerator, processor, [processor], [processor], ...., collector).

Stateless functions can be abstracted and shared as context-agnostic modules.

> To maximize code reuse, try to make as many functions as possible both stateless and generic (or polymorphic). Many jQuery methods satisfy both requirements. Such functions tend to be very useful library methods.


###Partial Application & Currying

*Partial application* wraps a function that takes multiple arguments and returns a function that takes fewer arguments. It uses closures to fix one or more arguments so that you only need to supply the arguments that are unknown.

```js
var multiply = function multiply(x, y) {
		return x * y;
	},
	partial = function partial(fn) {
		// Drop the function from the arguments list and
		// fix arguments in the closure.
		var args = [].slice.call(arguments, 1);
		// Return a new function with fixed arguments.
		return function() {
			// Combine fixed arguments with new arguments
			// and call fn with them.
			var combinedArgs = args.concat(
			[].slice.call(arguments));
			return fn.apply(this, combinedArgs);
	};
},
double = partial(multiply, 2);
test('Partial application', function () {
	equal(double(4), 8,
	'partial() works.');
});
```
As of ES5, you can also use `Function.prototype.bind()` for partial application. The only disadvantage is that you won’t be able to override the value of `this` with `.call()` or `.apply()` . If your function uses `this` , you shouldn’t use `.bind()` . This is how you use `.bind()` for partial application.

```js
var boundDouble = multiply.bind(null, 2); // null context
test('Partial application with bind', function () {
	equal(boundDouble(4), 8,
		'.bind() should allow partial application.');
});
```

This is commonly confused with currying.

Currying is the process of transforming a function that takes multiple arguments into a chain of functions, each of which takes no more than one argument.
An add function add(1, 2, 3) would become add(1)(2)(3) in curried form, where the first call returns a function that returns another function, and so on. This concept is important in lambda calculus. However, since JavaScript supports multiple arguments, it’s not common to see true currying in JavaScript applications.

##Asynchronous Operations

Asynchronous operations are operations that happen outside the linear flow of program execution.

Asynchronous operations are broken up into two phases: call and response. By definition, it’s impossible to know at what point in the program flow you’ll be when you receive an asynchronous response. There are a couple of popular ways to manage that uncertainty.

###Callbacks
*Callbacks* are functions that you pass as arguments to be invoked when the callee has finished its job.

Callbacks work great when you’re only waiting for one operation at a time, or when you only have one job to do when the response comes back, but what if you need to manage multiple asynchronous dependencies or you have several unrelated tasks waiting on the same data (such as a provider authorization)? That’s where promises can be very useful.

###Promises & Deferreds
*Promises* are objects that allow you to add callback functions to success or failure queues.Instead of calling a callback function in response to the completion of an asynchronous (or synchronous) operation, you return a promise, which allows you to register any number of callbacks.

The promise provides access to the state of the operation: whether it’s waiting or finished, and in some cases, what the progress is. You can add callbacks to a promise at any time, which will trigger after the operation is complete and the promise is resolved. If the promise has already resolved, the callback will be invoked immediately.

Promises were popularized in JavaScript by jQuery, inspired by the CommonJS Promises/A design. As of jQuery 1.5, all jQuery Ajax methods return a promise.

The difference between a promise and a callback is that a promise is an object that gets returned from the callee, instead of a function that gets passed into and invoked by the callee.

Promises allows easier callback addition and isolating callback code so it can be organized independently of the initiating call.

A *deferred* is the object that controls the promise, with a few extra methods.

jQuery’s `.Deferred()` returns a new object that does everything a promise does but also provides `.resolve()` and `.reject()` methods that trigger the corresponding callbacks.

Promises really shine when you need to orchestrate a complex sequence of events. It’s sometimes necessary to gather data from multiple sources prior to getting data from yet another source.