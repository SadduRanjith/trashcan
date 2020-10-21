The `Math` object has many useful methods to facilitate various mathematical calculations.

The `Math.pow()` method is used for exponential calculations. For example:
	var value = Math.pow(2,3) // 8 (2^3)

The `Math.sqrt()` method is used to calculte square roots. For example:
	var value = Math.sqrt(9); //3

The `Math.round()` method rounds the number to nearest Integer. For example:

	var value = Math.round(3.9); // 4

The `Math.floor()` & `Math.ceil()` methods round the number to the greatest integer less than the number & smallest integer greater than the number respectively. For example:

	var gi = Math.floor(3.6); //3
	var si = Math.ceil(3.6); //4

The `Math.max()` & `Math.min` methods return the maximum & the minimum number from the list of passed arguments respectively.

The `Math.abs()` method returns the absolute value of a number.

The `Math.sin()` & `Math.cos()` methods are used to calculate sine & cosine of a number.

The `Math.random()` method generates a random fraction between zero & one.

The `Math.PI` , `Math.SQRT2` & `Math.SQRT1_2` contain the value of pi, root two & root one by two respectively.

------

JavaScript provides the "Date" object datatype to work with date-time efficiently. A "Date" object can be created by the following ways:

	var date = new Date(); // assigns the Date & time of the users system clock
		date = new Date(1997,0,1); // assign January first 1997 midnight. Notice that the months start from zero
		date = new Date(1997,0,1,22,5); // assigns January first 1997 22:05. we can also add seconds & milliseconds
		date - new Date(0); // assigns January first 1970 midnight (the Unix epoch). The argument(timestamps) represents the time in milliseconds from the Unix epoch.

The Date objects have the following methods:

###Get methods
* `getFullYear()` returns the full 4 digit year.
* `getMonth()` returns the month ( zero based ).
* `getDate()` returns the day of the month.
* `getDay()` returns a zero based day of the week. zero being Sunday.
* `getHours()`
* `getMinutes()`
* `getSeconds()`
* `getMilliSeconds()`
* `All these` methods have a UTC versions. For example: getUTCDate().
* `getTime()` return the timestamps in milliseconds.
* `getTimezoneOffset()` returns offset in hours.

###Set methods

* `setMonth()` sets month **zero based**
* `setFullYear()`
* `setMonth()`
* `setDate()`
* `setDay()`
* `setHours()`
* `setMinutes()`
* `setSeconds()`
* `setMilliSeconds()`

We compare if a date object is less or greater than the other.
But to check equality we have to compare their timestamps because they are different objects.


A function declarations loads before any code executes i.e. the javascript engine hoists the function declaration to the top.
This means you can call functions anywhere in your code i.e. both before & after the function declaration.
But function expression does not allow to call the function before their declaration.

Every function has an `arguments` object which provides some very useful information. It behaves like an array.
`arguments.length` returns the number of arguments passed to the function.
`arguments[i]` returns the value of the (`i`-1)th argument.
`arguments.callee` returns a reference to the function currently being executed. This is very useful in recursive functions.

JavaScript manages memory in garbage-collection fashion i.e. it frees the memory representing an object if a reference to it is no longer available.
The concept of "closures" is very important in such environment. For example:

	function doSomething(arg) {
		var t = "jibberish";
		return function() {
			alert(arg);
		};
	}

	var func1 = doSomething("closure"),
		func2 = doSomething("closure2");

	func1();
	func2();

Because the function returned by `doSomething` contains a reference to a variable i.e. `arg` of `doSomething`,
The JS engine keeps a copy of `doSomething` in memory for each time it's called,
this copy is called a new "executional context".
This memory can be freed easily as follows:

	func1 = null;
	func2 = null;

The concept of "closure" can be used to your advantage. For example:

	var utility = (function() {
		var i = 0;
		return {
			nameGen : function() {
				var name = "customName" + i;
				i = i + 1;
				return name;
			}
		}
	} ());

	var name1 = utility.nameGen();
		name2 = utility.nameGen();

Here the "nameGen" method has a closure on the variable "i" which makes sure that each name generated is unique.


The Array datatype has the following NEW methods:

`indexOf` returns the index of the first occurrence of the specified element in the array.

`lastIndexOf` returns the index of the last occurrence of the specified elements in the array.

The following methods iterate through the array elements to perform required operations using the "callback" function, passed as an argument this function needs to have a specific signature as follows:
function name(value, index, array){}
Here value, index & array represent the element, of the array, currently being operated, the index of that element & the array itself.

The `every` method takes a function & returns true iff the supplied function returns true for every element of the array otherwise it returns false.

The `some` method takes a function & returns false iff the supplied function returns false for every element of the array otherwise it returns truee.
Note that every is like the logical AND whereas some is like the logical OR.

The `filter` method takes a function & returns a new array containing only the elements of the array for which the function returns true.

The `forEach` method takes a function & executes that function on every element of the array.

The "map" method takes a function & returns a new array containing the values returned by the function for the elements of the array.

The "sort" method takes a function & returns a new array of elements sorted in order based on the function, default is alphabetical sorting.

The "reduce" method takes calculates a result using array elements through a function it takes two arguments out of which second is optional. For example:
	[0,1,2,3,4].reduce(function(previousValue, currentValue, index, array){
	  return previousValue + currentValue;
	});
here index & array are supplied by reduce, previousValue is the last value returned by function & currentValue is the current-element, The initialValue parameter
gives first value of previousValue otherwise it takes value of first array element & currentValue starts from second array element.
	var total = [0, 1, 2, 3].reduce(function(a, b) {
		return a + b;
	}); // total == 6

The "reduceRight" method is exactly like the "reduce" method but it parses the array right-to-left.


There are many problems creating objects using object-literal-notation as follows:
-Repetition of code i.e. we have to specify the object properties & methods every time even if the objects are identical.
-Makes the code less flexible & difficult to maintain.

Another way to create objects is to create a "factory function", a function that returns object created using object-literal-notation.
This solves the repetition problem till some extent but not completely.

Making constructor functions is the conventional way to create objects. It increases performance & makes code more robust. For example:
	var Person = function(firstName, lastName) {
	 this.firsName = fistName;
	 this.lastName = lastName;
	};

	Person.prototype.getFullName = function() {
	  return this.firstName + " " + this.lastName;
	};

	Person.prototype.greet = function(person) {
	  if (person instanceof Person) { //checks if the person is an instance of Person datatype
		return "Hello, " + person.getFullName();
	  } else {
		return "Hello, there!";
	  }
	};
By creating a constructor, we are creating a new datatype hence by convention Pascal-casing is used.
The constructor returns the object by default hence no return statement is not needed.
The "instanceof" operator can be used to check if an object is an instance of a certain datatype. But the datatype must be defined.

But the new keyword must be used when creating new objects using the constructor, otherwise the constructor might modify & return the "window" object instead. This can be taken care of by checking "this === window" if this evaluates to true we call the constructor with the new keyword & return the object returned by it.
But by convention the new keyword must be used.

Every datatype has a prototype object of it's own from which all it's instances inherit properties & methods.
Using the prototype makes the code more efficient & flexible. For example in the above example if we defined the functions inside the constructor then a new function objects will be created each time the constructor is called whereas now they inherit the same methods from their prototype saving both memory & overhead.


We can make a method execute as if it were a method of another object. For example:
	var obj = {
	  name : "obj object",
	  doSomething : function() {
		alert(this.name);
	  }
	};

	var foo = {
	  name : "foo object"
	};

	obj.doSomething.apply(foo); // foo object
The "apply" method causes the doSomething method to execute as if it were a method of foo. The apply method has an optional second parameter which takes an array & pass the values in the array as arguments to the method.
The "call" method works exactly like the "apply" method but instead of the array it takes the coma seperated parameter list to pass as arguments to the method.

The following example demonstrates how powerful this technique is:
	var calculate = function() {
	  var fn = Array.prototype.pop.apply(arguments); // arguments is an object
	  return fn.apply(null,arguments);
	};

	var sum = function() {
	  var total = 0;

	  for (var i = 0, l = arguments.length; i < l; i = i + 1) {
		total = total + arguments[i];
	  }

	  return total;
	};

	var diff = function() {
	  var total = Array.prototype.shift.apply(arguments); // shift removes an element from the front of an array

	  for (var i = 0, l = arguments.length; i < l; i = i + 1) {
		total = total - arguments[i];
	  }

	  return total;
	};

	var sumResult = calculate(1, 2, 3, 4, 5, sum),
		diffResult = calculate(1, 2, 3, 4, 5, diff);

	alert(sumResult);
	alert(diffResult);


There are 3 main keyboard events as follows:
-"keydown" occurs when the user press a key.
-"keyup" occurs when the user releases a key.
-"keypress" occurs when the user press a key.

The "keypress" event does not fire up for modifier keys i.e. ctrl, alt, shift etc. whereas "keydown" & "keyup" events fire up for these keys.
	var txtbox = document.getElementsById("txtInput");

	eventUtility.addEvent(txtbox, "keypress",
		function(evt) {
		    var code;
		    if (evt.charCode === "number") { // standard
		         code = evt.charCode;
		    } else { // legacy
		        code = evt.keyCode;
		    }
		    alert(code);
		});
The charCode or the keyCode contain the ASCII value of the key that is pressed by the user i.e. 65 for 'A', 97 for 'a' etc.
We can allow only a certain range of characters to be entered in a textbox by checking the ASCII value of the key pressed by the user & using eventUtility.preventDefault(evt).

The keydown & keyup events are almost identical.
They both fire up for modifier keys.
They return same ASCII values for both upper & lower case letter i.e. 65 for both 'a' & 'A'.
They don't have the charCode property i.e. we can always use keyCode.
They provide boolean values i.e. evt.altKey, evt.shiftKey & evt.ctrlKey to check if the alt, shift or the ctrl key is pressed respectively.


A "form" element has some special methods, properties & events.
    -The "submit" event is triggered when the user tries to submit the form.
    -The "reset" method is used to reset the form.
    -The "submit" method submits the form. Note that this does not fire the "submit" event.
    -The "elements" collection contains the elements in form which take input i.e. textboxes, radio-buttons etc. hence divs are not included.

All input elements have some common features as follows:
    -A value property.
    -A type property.
    -A disabled property.
    -A "focus" method which the focus shift to them.

The text-field elements have a "select" method which selects the text in them.

The "select" elements have some special features as follows:
    -selectedIndex property returns the index of the selected option.
    -options property returns a collection of all the options of the select element. A particular option can be accessed using []s.
    -"remove" method takes an index & removes the option at that index.
There's a new way to get the selected items but it is not supported by many browsers. It's as follows:
var option = select.selectedOptions[0];
"selectedOptions" is a collection because there can be multiple selected options.

A new option can be added as follows:
var wedOption = new Option("wednesday",4);
select.add(webOption, select.options[3]);
The first argument in the Option constructor is the text to be displayed, the second is the value.
The first argument in add method is the option to be added, the second is the option before it is to be added.

checkboxes & radio-buttons both have a "checked" property which is boolean & tells if the control is checked.
They are scripted almost identically but we use array with checkboxes & a varible with radio-buttons because only one value can be selected with a radio button whereas a multiple values can be selected using checkboxes.


The "XMLHttpRequest" object is used for making requests to the server. The XML is there just because it was the default format but this object can be used to request any type of content including JSON.
It was invented by Microsoft and was called XmlHttp.
The following example shows how to make synchronous requests to the server.
	var xhr = new XMLHttpRequest();

	xhr.open("GET", "textfile.txt", false);

	xhr.send(null);

	alert(xhr.responseText);
The first step is to create the XMLHttpRequest object.
The "open" method takes 3 arguments: Request type, URL, synchronous mode(false for synchronous)
The "send" method is used to send data with the request to the server.
The "responseText" property contains the response. There is also a "responseXML" property which returns the XML DOM it's used when request was for a XML file.

When dealing with asynchronous requests i.e. Ajax, we can't rely upon the order of execution i.e. we can't be sure that the response has been received from the server because the browser executes the code without waiting for the response.
The HTTP-request goes through 5 stages represented by the 0-4 values of the "readyState" property of the "XMLHttpRequest" object. If the "readyState" is equal to 4 it means that the response has been received.
Every time the "readyState" changes it fire a "onreadystatechange" event, it's a DOM level zero event.
The "status" property gives the response code returned by the server. 200 - 299 & 304 means success while other values signify an error.
	var xhr = new XMLHttpRequest();

	xhr.open("GET", "textfile.txt", true);

	xhr.onreadystatechange = function() {
		if(xhr.readyState === 4) {
		    var status = xhr.status;

		    if ((status >=200 && status < 300) ||
		        status === 304) {
		            alert(xhr.responseText);
		        } else {
		            alert("Something bad happened!");
		        }
		}
	};

	xhr.send(null);

	The data sent to the server using post is a string of name value pairs separated by '&' sign.

	var form = document.getElementById("theForm"); // get the form element

	eventUtility.addEvent(form, "submit", function(evt) { // hadle the submit event
		var data = getRequestBody(); // get the data-string

		var xhr = new XMLHttpRequest();

		xhr.open("POST", "textfile.txt", true);
		xhr.setRequestHeader("Content-Type",
		    "application/x-www-form-urlencoded"); // setting the content-type header for the server

		xhr.onreadystatechange = function() {
		    if (xhr.readyState === 4) { // check for response
		        var status = xhr.status;

		        if ((status >= 200 && status < 300) ||
		            status === 304) { // check for response status
		                alert(xhr.responseText);
		            } else {
		                alert(status);
		            }
		    }
		};

		xhr.send(data); // send the request

		eventUtility.preventDefault(evt); // prevent default form submission
	});



	var getRequestBody = function() { // function for creating the data string
		var values = [];

		for (var i = 0, l = form.elements.length; i < l; i = i + 1) { // create the data string
		    var el = form.elements[i],
		        name = encodeURIComponent(el.name), // encode the information in the format the server expects
		        value = encodeURIComponent(el.value),
		        complete = name + "=" + value;

		    values.push(complete);
		}

		return values.join("&"); // return data string
	};


JSON stands for javascript object notation. It is format used to send & receive data efficiently. It replaced the role of XML in Ajax. It has found application in various systems to store & transfer data.
It's much more compact than XML because XML needs a closing tag for every opening tag.
It's much more flexible than XML. Each XML document has a structure conforming to it's DTD hence for every new DTD different code needed to be written, This is not the case with JSON.
It's much less time consuming to work with than XML. When working with XML you have to parse the XML DOM to fetch every single element using JSON is much faster.

A JSON object is basically a object-literal in which everything except numbers & delimiters are converted to string.
	var obj = JSON.parse(xhr.responseText); // get the object literal from the response

	alert(obj.item); // The properties & method can be used in normal fashion

	var json = JSON.stringify(obj); // convert object to JSON
The content-type header might be need to be set to "application/json", depends on server.


The localStorage object is used to store data on the client to be used across multiple pages. We can also use cookies but it's very cumbersome to use cookies with javascript. The localStorage object can only be accessed by javascript i.e. server cannot access it so this is a disadvantage as compared to cookies. The localStorage is supported by all browsers IE8 & up. It's a global object i.e. it's window.localStorage. It stores key-value pairs as follows:
	localStorage.firstName = "John"; // storing data using '.' operator
	localStorage.setItem("lastName","Doe"); // storing data using setItem method

	var fullName = localStorage.getItem("firstName") + " " + localStorage.lastName; // getting values in two ways

	localStorage.removeItem("firstName"); // removing a key

	localStorage.clear(); // removing all keys
All the values are stored as strings hence they need to be converted back after retrieval, this makes it difficult to store & retrieve objects from localStorage. To deal with this problem we first convert our object into JSON, store it , retrieve it & convert it back again.

The data stored by one domain in localStorage cannot be accessed by a page of another domain.


The error handling is done using try-catch blocks. The syntax is as follows:
	try{
		// code that might fail
	} catch (er) {
		alert(er.message);
		// handle error
	} finally { // optional
		// always executes
	}
