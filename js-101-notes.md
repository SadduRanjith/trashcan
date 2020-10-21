`<script></script>` element embeds js in html.

It's preferred to be inserted right before the closing body tag rather than inside the head element, so users get their content faster, also we are sure that the DOM is fully loaded. For example:  `<script src="helloworld.js"></script>`

No self closing script element.

Double quoted & single quoted strings are identical.
Statements end with a semicolon.
`alert()` to generate popup messages.


`var` keyword is used for declaring variables.
Variable names must start with a letter(lowercase or uppercase), an underscore or a dollar sign & can contain all these & numbers.

Undefined variables have the default value `undefined` of datatype `undefined`.

Variables can be declared & defined in the same statement, example:

	var helloWorld = "Hello, World!";

camelCasing is javascript naming convention.
some datatypes are Strings, Numbers, Boolean.
Number can contain both integer & floats.
Boolean can contain only "true" or "false".

Multiple variable declaration:

	var helloWorld = "Hello, World!", ten = 10;

	//single line comment /*multiline comment*/

`+` is used both as concatenation operator & addition operator, so `3 + 4` = `5` also `"7" + 6` = `"76"`.

	parseInt("34d5",10) = 34, parseInt(3.14,10) = 3.

`parseInt()` only parses the argument till it's a valid number.

	parsefloat("3.14",10) = 3.14

functions are defined as follows:

	function func_name(p1,p2) { //function declaration syntax
		return p1+p2;
	}

OR

	var func_name = function (p1,p2) { //function expression syntax
		return p1+p2;
	};

Functions with no names are called anonymous functions.

The parameters can themselves be functions. For example:

	function sum(p1,p2){ return p1+p2;}
	var func_name = function(p1,p2,fn){ return fn(p1,p2);}
	var foo = func_name(1,2,sum);

OR

	var func_name = function(p1,p2,fn){ return fn(p1,p2);}
	var foo = func_name(1,2,function(p1,p2){
	 return p1+p2;
	});

The js engine look for the variable-identifier in incremental-scope-fashion i.e. you can override variables & functions.

Everything is global by default.

javaScript is Object-based i.e. everything is an object.

Loops

	for (var i=0; i<10; i++) {
		//iterate
	}

	while (i<10) {
		//iterate
	}

	do {
		//iterate
	} while(i<10);

`.` operator is used to access object properties & methods. For example:

	var str="This is a string object.",
		length = str.length; //24

`length` is a property of the `String` object.

`Array.prototype.pop()` removes and returns the last element from the array.

The `indexOf` method returns the index of the specified sub-string in a String object. For example:

	var str="This is a string object.",
		index = str.indexOf("is"), //2
		index2 = str.indexOf("is", index + 1), //5
		index3 = str,indexOf("this"); //-1

The second parameter(optional) tells the method, the index where it should start searching.
The method always retuns -1 if it didn't find the string.

The method `lastIndexOf` is used to search for the string right to left. For example:

	str="This is a string object.",
	index = str.lastIndexOf("is"), //5
	index2 = str.lastIndexOf("is", index - 1), //2
	index3 = str.indexOf("this"); //-1

None of the methods `substr`, `substring`, `replace`, `toUpperCase` & `toLowerCase` changes the original String object.

The `substr` & `substring` methods are used to extract a sub-string from a String object. For example:

	var str="This is a string object.",
		substr = str.substr(10,6), //string
		substring = str.substring(0,4); //This

Both these methods work differently as follows:

	substr(startIndex,subSringLength)
	substring(startIndex,endIndex +1)

It's clear `substr` is the one to go for,

The `replace` method creates a new String object  by replacing a sub-string with another as follows:

	var str="This is a string object.",
		newString = str.replace("object","value"); //This is a string value.

If it does not find the specfied sub-string it does not change anything.

The `toUpperCase` & `toLowerCase` methods create a new String  by  changing all characters to upper-case & lower-case respectively:

	var str="This is a string object.",
		upperCase = str.toUpperCase(), //THIS IS A STRING OBJECT.
		lowerCase = str.toLowerCase(); //this is a string object.

Datatypes are defined using PascalCasing, the default one's are `Object`, `String`, `Number`, `Boolean` & `Array`.

Every datatype inherits from the `Object` datatype.

The `toString` method belongs to the `Object` datatype. Hence every object has it.

The `this` keyword is used to refer to the calling object.


javaScript allows you to create custom objects as follow:

	// Old way
	var person = new Object();
	person.firstName = "Jeremy";
	person.lastName = "McPeak";
	person.getFullName = function() {
		return this.firstName + " " + this.lastName;
	};

	// New Way
	var person = {
		firstName : "Jeremy",
		lastName : "McPeak",
		getFullName : function() {
			return this.firstName + " " + this.lastName;
		}
	};

This is called the "object literal notation", a more simple form is: var person = {};
It's identical to: `var person = new Object();`

Arrays provide functionality to group data in a single object. It can be done as follows:

	// Old way
	var foo = new Array(11, "hello", true);
	// New way
	var foo = [11, "hello", true];

This is called array literal notation.
Array can contain different datatypes but it's strongly discouraged.

The array datatype also has the property `length`, it gives number of elements in array.

The array elements can be accessed as follows: `var value = foo[0];`

A new element can be added to an array as follows: `foo[3] = 2; //number of elements in the array weere three`
It's desirable to keep the array element indexes contagious, we do this as follows: `foo[foo.length] = "value";`
A better way to do this is: `foo.push("value");`

As you might expect a `.pop()` method also exists, it removes the last array element & returns it.

The `.concat()` method is used to concatenate arrays. For example:

	var name = ["Jeremy", "Jeffrey"],
		name2 = ["Jennifer", "Jackie"];
	var people = name.concat(name2); // ["Jaremy", "Jeffrey", "Jennifer", "Jackie"]

The `.concat()` method can take more than 1 parameter i.e. you can concatenate more than two arrays.

The `.join()` method creates a string, using the array elements separated by the specified delimeter. For example:

	var names = ["Jeremy", "Jeffrey"],
		names2 = ["Jennifer", "Jackie"];
	var people = name.concat(name2); // Jaremy Jeffrey Jennifer Jackie
	var joined = people.join(", "); // "Jeremy, Jeffrey, Jennifer, Jackie"

The `reverse()` method creates an array with order of the alements reversed.

The `.sort()` method creates an array of elements sorted in order by the function passed as argument, default is alphabetical sorting.

The `.split()` method is used to convert a string into an array, devided by a specified delimiter.

The `.every()` method takes a function & returns true or false based on if all the elements in the array pass that test.

The `.some()` method takes a function & returns true or false based on if any of the elements in the array pass that test.

The `.reduce()` method takes calculates a result using array elements through a function it takes two arguments out of which second is optional. For example:

	[0,1,2,3,4].reduce(function(previousValue, currentValue, index, array){
	  return previousValue + currentValue;
	});

here index & array are supplied by reduce, previousValue is the last value returned by function & currentValue is the current-element, The initialValue parameter
gives first value of previousValue otherwise it takes value of first array element & currentValue starts fom second array element.

	var total = [0, 1, 2, 3].reduce(function(a, b) {
		return a + b;
	}); // total == 6

The `.reduceRight()` method is exactly like the `.reduce()` method but it parses the array right-to-left.

The `.filter()` method  returns an array of elements which pass the test of the function passed as argument.


Comparison operators, used to compare two or more values, return true or false. These are: `<`, `>`, `<=`, `>=`, `==`, `!=`, `===` and `!==`.

Here the `===` is known as the identity operator it also conciders the datatype while comparing values. For example:

	var foo = "6" == 6; // true
		foo = "6" === 6; // false

Similarly `!==` is called the non-identity operator.

The if statement executes the block of code enclosed by it iff the condition is true.
The else block complements the if block. For example:

	if (foo > bar) {
		alert("foo is greater than bar");
	} else {
		alert("foo is not greater than bar");
	}

swich statement is used when multiple branches of execution exist & depend on a single value. For example:

	switch(el) {
		case value1: //statements
			break;
		.
		.
		default: //statements;
	}

Logical operators, used to create logical statements, return true or false. These are: &&, ||, ! For example:

	if (!(foo > bar || bar < 6)) {
		alert("foo is not greater than bar and bar is not less than 6");
	}

javaScript has truthy & falsy values.
Falsy values are 0, null, undefined & undefined variables, Empty strings. Don't use undefined, you can change it's value.
Truthy values are non-zero numbers, non-empty strings, all objects even {} (except null), all arrays even [].


There are 3 major loops: `for`, `while` & `do while` loops. The syntax is as follows:

	for (var i=0; i < 10; i = i+1){
		// statements
	}

	while(condition){
		// statements
	}

	do {
		//statements
	} while(condition);


The `window` object represents the webpage & is the global scope i.e. all global variables are it's properties.

Global scope is bad because it can be modified very easily. To avoid this, we use "immediately invoked functions", for example:

	(function() {
		// code
	}());

The `prompt` method facilitates getting input from user but there are other far better ways for that.

The `confirm` merthod is used to ask the user for a binary decision, based on which it returns true or false. It's syntax is as follows: `confirm("message");`

The `location` property of the `window` object allows us to get the current page address or to modify it i.e. redirect the user.


The `document` object is the direct child of the `window` object. It is the object representing the DOM.
Everything is a "node" in the DOM.
Some Node properties are: `parentNode`, `tagName`.

The `getElementsByTagName` method of the `document` object returns a `Nodelist` of the elements represented by the specified string. For example:

	var pElements = document.getElementsByTagName("p"); // A nodelist containing all the p-elements

Note that the `Nodelist` it returns is live i.e. it changes with the DOM in realtime.

The Nodelist also has the `length` property.

To select a particular element the `document` object provides the `getElementById` method.

The `document` object also provides two methods `querySelector` & `querySelectorAll` which uses CSS selectors but are slower than `getElementsByTagName` & `getElemnentsById`. Also the nodelist returned is not live.


The `createElement` method of the document object is used to create a element node. For example:

	var el = document.createElement("p");

The `appendChild` method is used to append a node to an element. For example:

	var el = document.createElement("p");
	document.body.appendChild(el);

The `createTextNode` method is used to create a text node. The contents of a text node are rendered as pure text i.e. HTML tags are treated as plain text.
For example:

	var el = document.createElement("p"),
		content = document.createTextNode("<strong>text</strong>");
	el.appendChild(content);
	document.body.appendChild(el);

The `setAttribute` method is used to set attributes of elements. For example:

	el.setAttribute("align", "right");

Most basic attributes are defined as properties of elements. For example: `el.id = "foo";`

The `insertBefore` method is used to insert a node before a specified child. For example:

	pFoo.parentNode.insertBefore(el, pFoo);

Here first argument is the element to be inserted & the second is the child before it has to be inserted.

The `replaceChild` method is used to replace a child node with another node. For example:

	pFoo.parentNode.replaceChild(el, pFoo);

Always create->add content->set attribute->insert node in the DOM. Keep DOM interaction to a minimum it's slow.
Use `var doc = document`, this saves the js Engine from locating the `document` object repeatedly, hence increasing performance.

The `innerHTML` property allows access to inner-HTML of an element. Unlike a TextNode the tags here will be treated as HTML code by the browser.


The `style` property is used to edit style attributes of an element. For example:

	divFoo.style.color="blue";
	divFoo.style.backgroundColor="yellow";

Notice that instead of `background-color` we used `backgroundColor`. This is because `-` represents the subtraction operator.

Only the properties set using the style attribute in the HTML tag &  the `style` property itself are aceessible from the `style` property of the element.

The `className` property is preferred over the `style` property as it allows to keep styling in stylesheets & add multiple properties in one go. For example:

	divFoo.className = "css-class css-class2";

Notice there is no `.` also multiple classes can be added.

It gets harder to remove a specific class using this property. One way to counter it is to have a space on both side of every class so you can replace it with an empty string. For example:

	divFoo.className = " css-class  css-class2 " //notice there are 2 spaces between the classes
	divFoo.className = divFoo.className.replace(" css-class2 ", "");

Another way is to use the `classList` property. It has methods like `toggle`, `add` & `remove`. For example:

	divFoo.classList.add("css-class");
	divFoo.classList.remove("css-class2");
	divFoo.classList.toggle("css-class2");

But beware even IE9 does NOT support the `classList` property.

To get all the style properties of an element `getComputedStyle` or `currentStyle` properties are used based on the browser.
The standard browsers & legacy browsers must be handled differently because they don't support the same features. We overcome this by writing cross-browser javaScript code. For example:

	var getStyle= function(e1, cssProperty){
		if (typeof getComputedStyle !== "undefined") { // standard case
			return window.getComputedStyle(el, null).getPropertyValue(cssProperty); //notice the window object
			//the first argument is the element whose property is to be found, second is a pseudo variable not many browsers support this so we use null
		} else { // legacy case
			return el.currentStyle[cssProperty];
		}
	};
	var color = getStyle(divFoo, "color");


The `window` object provides `setTimeout` method which allows us to delay the execution of another method. For example: `setTimeout(doSomething, 2000);`
The first argument is the function to be executed after the delay, specified in miliseconds by the second argument.
Note that the function name is passed i.e. you cannot provide function argument.

The `clearTimeout` method which provides a way to clear the timer i.e. prevent the function from executing. For example:

	var timer = setTimeout(doSomething, 2000);
	clearTimeout(timer);

The `setInterval` & `clearInterval` provide the same functionality as `setTimeout` & `clearTimeout` methods respectively. The only difference is the function is executed repeatedly after the set interval.

The `offsetLeft` property gets the left position of the element with respect to it's nearest positioned ancestor element.
Similarly there is `offsetTop`.

Objects are moved by using the `style` property to manipulate their position.


The javaScript provides events to allow execution of code after a certain event.

`onclick` event-handler is used to handle the `click` event. For example:

	e1.oncliick = function() {
		this.style.backgroundColor = "red";
	};

`onmouseover` event-handler is used to handle the `mouseover` event.

`onload` event handler is used to execute code when the object is loaded. For example:

	window.onload = function() {};

Both the `onclick` & `onload` events are "DOM level zero events" i.e. all browsers supports it but they are volatile & easily override-able.


The "standard event Model" which allows to add, remove & get information about event handlers. Allows to execute multiple pieces of code when an event occurs.

In the early days there were only two browser netscape & IE both inplemented very differently as follows:
Netscape used "capturing" i.e. first for the HTML element then for every element down the DOM chain until the target element the event is executed.
IE used "bubbling" i.e. first for the target element then for all elements above the DOM chain until the HTML element the event is executed.

The `addEventListener` method is used to add functions to be executed  when the event occurs. For example:

	el.addEventListener("click", buttonClick, false);

The first argument specifies the event. Note `click` instead of `onclick`.
The second argument specifies the function to be executed.
The third argument is used to specify behaviour. `false` => bubbling, `true` => capturing.
You would always use `false`. Most new browsers doesn't require the last argument, but older one's do.

The `.removeEventListener()` method is used to remove event-listeners.
For example: `el.removeEventListener("click", buttonClick, false);`
Keep in mind the function you want to remove must have a name i.e. you cannot remove anonymous functions.

An object, containing event related information, is passed to the EventListener functions automatically. For example:

	el.addEventListener("click", function(e){
		e.preventDefaul(); //prevents default behaviour like redirection by a link.
		e.target; // returns the target object
		e.type; // returns the type of event
	}, false);

For legacy browsers the `attachEvent` method is used instead of `addEventListener` methods. For example:

	el.attachEvent("onclick", function(e){
		event.returnValue = false; //prevents default behaviour like redirection by a link.
		event.srcElement; // returns the target object
		event.type; // returns the type of event
	});

Must use `event.srcElement` while using `attachEvent` because `this` will point to `window`.
Notice `onclick` is used instead of `click`.
The `event` element is a global object i.e. `window.event`, which stores all information about all events.
No third argument.

The `detachEvent` method is used to remove instead of `removeEventListener` in legacy IE.
For example: `detachEvent("onclick", buttonClick);`


There are many ways to write cross-browser javascript code. One of which is creating utility objects which we can use to perform general operation without worrying about the browser type. For example following is an object which implement cross-browser event-handling:

	var eventUtility = { // utiliy object
		addEvent : function(el, type, fn) { // function to add events
			if (typeof addEventListener !== "undefined") { // feature detection "addEventListener" i.e. the standard.
				el.addEventListener(type, fn, false); // if the browser supports standard, add event the standard way.
			} else if (typeof attachEvent !== "undefined") { // else check for legacy "attachEvent" support
				el.attachEvent("on" + type, fn); // if supported add event the legacy way.
			} else { // else use zero level DOM event handlers.
				el["on" + type] = fn;
			}
		},  // notice the order standard -> legacy -> zero level DOM

		removeEvent : function(el, type, fn) { // remove event function
			if (typeof removeEventListener !== "undefined") {
				el.removeEventListener(type, fn, false);
			} else if (typeof detachEvent !== "undefined") {
				el.detachEvent("on" + type, fn);
			} else {
				el["on" + type] = null;
			}
		},

		getTarget : function(event) {
			if (typeof event.target !== "undefined") {
				return event.target;
			} else {
				return event.srcElement;
			}
		},

		preventDefault : function(event) {
			if (typeof event.preventDefault !== "undefined") {
				event.preventDefault();
			} else {
				event.returnValue = false;
			}
		}
	};


"Event-delegation" is a special way to handle events using the browsers capability of bubbling events. For example:

	(function(){
		eventUtility.addEvent(document,"click", function(evt){
			var targey = eventUtility.getTarget(evt),
				tagName = target.tagName;
			if (tagName === "A")
			{
				var className = target.innerHTML.toLowerCase();
				eventUtility.preventDefault(evt);
				document.body.className = className;
			}
		})
	}());

Event delegation makes out code flexibile because we take actions based upon the "target",the event has occured on.
Also we no longer need to create & remove identical event handlers for each element we want to target. Instead we can use a single event-handler of the document object & take suitable action based on the target.
It also helps us deal with dynamically generated elements better than the traditional way.

Event delegation improves performance because instead of creating event handlers for each element we use onlu a few event-handler hence saving memory.

The dowside of event-delegation is that it makes the code more complex.

For small application Event delegation is overkill but for larger the application the benefits of event delegation are very much apparent. For example:

	(function() {

		var mouseHandler = function(evt) {
			var target = eventUtility.getTarget(evt),
				classData = target.getAttribute("data-body-class");

			if (classData) {
				eventUtility.preventDefault(evt);

				if (evt.type === "click") {
					document.body.className = "";
				} else {
					document.body.className = classData;
				}
			}
		};

		eventUtility.addEvent(document, "click", mouseHandler);
		eventUtility.addEvent(document, "mouseover", mouseHandler);

	}());