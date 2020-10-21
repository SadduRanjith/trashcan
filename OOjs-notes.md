Everything in javascript is not an object. Some of them are primitive values.
String, Number, Boolean, Undefined & Null are the primitive types. Every other value is a reference type i.e. an object.
Still we are able to call upon methods on the primitive values because javascript makes temporary objects for us behind the scenes. For example:
	"hello".length; // 5
is executed as:
	new String("hello").length; // 5
	
The only way to create objects for primitive types is to use their "wrapper" constructors.
	var obj = new Number(10);
	
	typeof obj; // object
	tupeof obj.valueOf(); // number
	typeof Number(10); // number
These objects can still be used as it is in operations.	
	var sum = obj + 10; // 20
	
	typeof sum; // number
	typeof 0bj; // object
Premitive types cannot be assingned a property, no error will generate but the property will be lost. Where as the wrapper object can be assigned properties.
	var num = 10; // undefined
	
	num.property = "hello"; // hello
	
	num.property; // undefined
	
	num = new Number(10); // 10
	
	num.property = "hello"; // hello
	
	num.property; // hello
But using "wrapper" objects for primitive types is not encouraged. Instead we use the primitive values as it is.


Sometimes we need to execute a function as if it were called by another object this is done using the bind method. It creates a new instance of the function bound to an object.
	var name = "John Doe";
	
	var greetGlobal = function() {
		return this.name;
	};
	
	var janeDoe = {
		name : "Jane Doe",
		greet : greetGlobal
	};
	
	var jeffDoe = {
		name : "Jeff Doe",
		greet : greetGlobal.bind(window);
	};
	
	greetGlobal(); // John Doe
	janeDoe.greet(); // Jane Doe
	jeffDoe.greet(); // John Doe
The bind method is not supported by IE8 & below but shims are available for it.


The ECMAscript5 & above provide more control over properties of objects. The methods used to implement this are defineObjectProperties & defineObjectProperty.
	var createPerson = function (firstName, lastName) {
		var person = {};
		
		Object.defineProperties(person, {
			firstName : {
				value : firstName
			},
			lastName : {
				value : lastName
			},
			fullName : {
				get : function() { return this.firstName + " " + this.lastName }
			}
		});
		
		return person;
	}
	
	var person = createPerson("John", "Doe");
There are 2 different types of descriptors that allow us to define a propeerty namely: data-descriptors & accessor-descriptors.
Data-descriptors & accessor-descriptors are mutually exclusive i.e. only one type of decriptors can be used for a property, whereas different descriptors can be used within the same object.
The data-descriptors create a property with a value, whereas the accessor-descriptors allow us dynamic properties based on two functions namely: get & set.
Data-decriptors:
 -'value' stores the value of the property
 -'writable' defines property write access, false by default

There are two other options apart from descriptors.
 -'configurable' is boolean value specifying if a property can be re-defined after it's initial defination. false by default. For example:
	Object.defineProperty(person, 'firstName', {
		value : "Jeremy"
	});
Gives an error because the configurable option for firstName has not been set to true.
 -'enumerable' specifies if a property is enumerable. false by default.
 There are two ways to enurate an object as follows:
	for (var prop in person){} // using for-in loop
	var props = Object.keys(person); // returns an array of properties


Parasitic inheritance is the phenomena of manipulating a object's properties to match a certain interface. For example:
	var createPerson = function (firstName, lastName) {
		var person = {
			firstName : firstName,
			lastName : lastName
		};
		
		Object.defineProperty(person, "fullName", {
			get : function() {
				return this.firstName + " " + this.lastName;
			},
			configurable : true
		});
		
		return person;
	};
	
	var createEmployee = function (firstName, lastName, position) {
		var employee = createPerson(firstName, lastName);
		
		employee.position = position;
		
		var fullName = Object.getOwnPropertyDescriptor(employee, "fullName");
		var fullNameFunction = fullName.get.bind(employee);
		
		Object.defineProperty(employee, "fullName", {
			get : function() {
				return fullNameFunction() + ", " + this.position;
			},
			configurable : true;
		});
	};
The "getOwnPropertyDescriptor" method returns the descriptors of a property. We could have accessed fullName.enumerable , fullName.configurable or fullName.writable in the same fashion as we accessed fullName.get.


Using the "prototype" object is the default way to implement inheritance in javascript.
Object-literals are fine for a simple object.
Using constructor functions makes our code more memory efficient. It is also faster if no. of objects are more & much more complex, also they can be checked if they are of a certain type using the "instanceof" method.It allows to use prototype inheritance.

The constructor function returns a object automtically hence we don't need to create & return an object.
The object properties are in a constructor function are defined using the 'this' keyword. 'this' in a constructor function refers to the object being created hence the constructor function is an intialiser function hence there is no need to create an initialiser function.

Every function has a prototype object but we only use it with constructor functions for others it's uselesss.
The property defined on the prototype object is shared by all the objects of that type.

It's better to define the methods outside the constructor function using the prototype instead because this saves memory by sharing the same function object instead of creating a new one for each object.

Prefixing a property with an "_" is the convention to notify that it's private. It may or may not be writable the "_" is just to notify.

The property defined on the prototype object does not belong to a particular object hence 'hasOwnProperty' returns false.

When refering to a property the Javascript engine first looks in the object then in it's constructor functions prototype, if it still does not find the property it goes to the next prototype in chain until it finds the property or hits Object.prototype.


The 'Object.create' method allows us to create an object with another object as it's prototype. For example:
	var person = {
		firstName : "John",
		lastName : "Doe",
	};
	
	Object.defineProperty(person, "fullName", {
		get : function() {
			return this.firstName + " " + this.lastName;
		}
	});
	
	var employee = Object.create(person);
	
	person.sayHi = function() {
		return "Hi there";
	}
	
	employee.fullName; // John Doe
	employee.sayHi(); // Hi there
Here the person object is the prototype of the employee object hence it inherits all the properties & method of the person object.
The "Object.create" method has an optional second argument which takes a descriptor object, whose properties will be assigned to the created object.

The "__proto__" property returns the actual prototype of an object. Only mordern browsers allow access to it.

The following example demonstrates prototypical inheritance with constructor functions:
	var Person = function(firstName, lastName) {
		this.firstName : firstName;
		this.lastName : lastName;
	};

	Object.defineProperties(Person.prototype, {
		sayHi : {
			value : function() {
				retur "Hi there";
			},
			enumerable : true
		},
		fullName : {
			get : {
				return this.firstName + " " + this.lastName;
			},
			enumerable : true
		}
	});

	var Employee = function(firstName, lastName, position) {
		Person.call(this, firstName, lastName);
		this.position = position;
	}

	Employee.prototype = Object.create(Person.prototype, {
		sayHi : {
			value : function() {
				return Person.prototype.call(this) + ", My name is " + this.fullName;
			},
			enumerable : true
		},
		fullName : {
			get : (function() {
				var desc = Object.getOwnPropertyDescriptor(Person.prototype, "fullName").get;
				return function() {
					return desc.call(this) + ", " + this.position;
				};
			}()),
			enumerable : true
		}
	});
	
	
Keep the prototype chain smaller so the Javascript engine does not have to look-up at many link to find a property for performance enhancements.


To throw an error the 'throw' keyword is used as follows: throw new Error("message");
