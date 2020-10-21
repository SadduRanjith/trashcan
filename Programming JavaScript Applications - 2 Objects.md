#Objects

> Primitive types behave like objects when you use the property access notations, but you can’t assign new properties to them. Primitives get wrapped with an object temporarily, and then that object is immediately thrown away. Any attempt to assign values to properties will seem to succeed, but subsequent attempts to access that new property will fail.

JavaScript’s object system is so powerful and expressive that most of the complexity in common OO patterns melts away when you reproduce them in JavaScript. For example, the singleton is reduced to an object literal:

```js
var highlander = {
	name: 'McLeod',
	catchphrase: 'There can be only one.'
};
```

##Classical Inheritance Is Obsolete

In Design Patterns: Elements of Reusable Object Oriented Software, the Gang of Four opened the book with two foundational principles of object-oriented design:
1. Program to an interface, not an implementation.
2. *Favor object composition over class inheritance.*

In a sense, the second principle could follow from the first, because inheritance exposes the parent class to all child classes. The child classes are all programming to an implementation, not an interface. Classical inheritance breaks the principle of encapsulation and tightly couples the child class to its ancestors.

*classical inheritance is like Ikea furniture*. You have a bunch of pieces
that are designed to fit together in a very specific way. If anything at all goes wrong or deviates from the preplanned specification, there is
little room for adjustment or flexibility.

Composition is more like Lego blocks. The various pieces aren't designed to fit with any specific piece. Instead, they are all designed to fit together with any other piece, with few exceptions.

Inheritance causes several problems:

 * *Tight coupling*
Inheritance is the tightest coupling available in OO design. Descendant classes have an intimate knowledge of their ancestor classes.
 * *Inflexible hierarchies*
Single-parent hierarchies are rarely capable of describing all possible use cases. Eventually, all hierarchies are “wrong” for new uses—a problem that necessitates code duplication.
 - *Multiple inheritance is complicated*
It’s often desirable to inherit from more than one parent. That process is inordinately complex, and its implementation is inconsistent with the process for single inheritance, which makes it harder to read and understand.
 - *Brittle architecture*
With tight coupling, it’s often difficult to refactor a class with the “wrong” design,
because much existing functionality depends on the existing design.
 - *The gorilla/banana problem*
There are often parts of the parent that you don’t want to inherit. Subclassing allows you to override properties from the parent, but it doesn't allow you to select which properties you want to inherit.

Inheritance works beautifully for a short time, but eventually the app architecture becomes arthritic. When you’ve built up your entire app on a foundation of classical inheritance, the dependencies on ancestors run so deep that even reusing or changing trivial amounts of code can turn into a gigantic refactor. Deep inheritance trees are brittle, inflexible, and difficult to extend.

> When you do encounter situations in which you’re forced to subclass by other programmers, keep in mind that inheritance hierarchies should be kept as small as possible. Avoid subclassing subclasses, remember that you can mix and match different code reuse styles, and things will go more smoothly.

##Fluent-Style JavaScript

*Programmers with a background in other languages are like immigrants to JavaScript.* They often code with an *accent*—preconceived notions about how to solve problems. For example, using constructor functions is a clear and strong accent, because they are completely unnecessary in JavaScript. They are a waste of time and energy.

There are serious limitations to this way of doing things. The first is that you must always call a constructor using the `new` keyword. Failing to do so will pass the global object in as `this` , resulting in pollution of the global namespace and objects which are not instance safe. When you forget `new` is a little different: `this` is useless, and trying to assign properties to it will throw an error.

There is a workaround by checking for same in the constructor. A convention has sprung up in JavaScript to help you remember when to use  new and when not to. Constructor functions always begin with a capital letter. But some libraries uppercase the first letter of their library namespace regardless if it's a constructor or not. But that's not the real problem.

The real problem is that using constructors will almost always get you stuck thinking in classical OO mode. The constructor becomes analogous to a class. You might start to think, “I want to subclass x...” and that’s where you get into trouble. You’re ignoring two of the best features of JavaScript: dynamic object extension (you can add properties to any object in JavaScript after instantiation) and prototypal inheritance. The two combined are a much more powerful and flexible way to reuse code than classical inheritance.

You can learn a lot about JavaScript by reading the code of experienced JavaScript natives who have not been influenced by classical inheritance.

In his famous book, The Tao of Jeet Kun Do, Bruce Lee advised that you
develop your own style. Study and take in all the good things about  different styles, pick what you like, and discard the rest to create your own unique style. It’s good advice that lies at the heart of mixed martial arts—a discipline that combines the best features of several fighting styles.

The same is true with programming languages. JavaScript itself is a fusion of the best ideas from Scheme (lambda), Self (prototypal inheritance), and Java (syntax).

To coin a phrase, *fluent style* simply discards inefficient constructs and takes advantage of JavaScript’s strengths and efficiencies:

 * Lambdas and Closures
These are a language unto themselves. Lambda expressions are simple, elegant, and powerful. Literally anything that can be expressed as a computable algorithm can be expressed through lambdas. Lambdas are an essential ingredient of functional programming.

 * Object-literal notation
This is the fastest route to a working object instance from scratch. Also take advantage of its sibling, array literal notation.

 * Dynamic object extension
This allows you to easily use mixins, composition, and aggregation for code reuse, even after object instantiation. Subclassing requires a lot of extra typing and gains you nothing. It makes your code brittle and inflexible.

 * Prototypes
These allow you to clone instances of existing objects to create new ones and share generic methods on a delegate object.

 * Factories
These are a more flexible and less verbose alternative to constructor functions in JavaScript. You don’t need constructors to create new objects. Any function in Java Script can return a new object, and with less code than a constructor requires. Unlike constructors, factories hide instantiation details from the caller, and there’s no need to use the awkward and superfluous new keyword when invoking a factory. Factories make it easy to combine any of the above techniques, and even change implementations at runtime without changing the way that objects are instantiated from outside the factory.

 * Fluent APIs (not to be confused with fluent-style JavaScript)
A fluent API is an interface that reads a bit like natural language. Fluent APIs are usually chainable but don’t need to be chained to be fluent. The defining characteristic is that each method returns an object that has additional methods on it that make sense as a next step. In this way, methods can be strung together in short sentences, each method acting on the return value of the last. jQuery and Jasmine are examples of popular fluent APIs in JavaScript.

Some of the techniques used in fluent-style JavaScript were popularized by the Prototype and jQuery libraries, but fluent style wasn't invented by anybody in particular. It is not a style in itself so much as the shedding of unnecessary styles and techniques that are often habits left over from other languages.

In short, fluent style isn't really a particular formal style—*it’s just what fluent JavaScripters do*.

##Prototypes

A *prototype* is an object intended to model other objects after. It is similar to a class in that you can use it to construct any number of object instances, but different in the sense that it is an object itself. There are two ways that prototypes can be used: you can delegate access to a single, shared prototype object (called a delegate), or you can make clones of the prototype.

###Delegate Prototypes

In JavaScript, objects have an internal reference to a delegate prototype. When an object is queried for a property or method, the JavaScript engine first checks the object. If the key doesn't exist on that object, it checks the delegate prototype, and so on up the prototype chain. The prototype chain typically ends at the `Object` prototype.

When you create an object literal, it automatically attaches the Object prototype. Alternatively, you can specify a prototype to set when you create an object via the `Object.create()` method. `Object.create()` was added with ES5, so you may need to polyfill it. Here’s the `Object.create()` polyfill from [MDN](http://mzl.la/1pFH3jo):

```js
if (!Object.create) {
	Object.create = function (o) {
		if (arguments.length > 1) {
			throw new Error('Object.create implementation'
			+ ' only accepts the first parameter.');
		}
		function F() {}
		F.prototype = o;
		return new F();
	};
}
```

Simply pass any object into Object.create() , and it will be be set as the delegate prototype for the newly created object. As you can see, the delegate prototype has some special behavior. Properties on the prototype act like defaults. When you set them on the instance, the instance value overrides the value for that instance, only.

It’s important to note though that if you mutate an object or array property on the prototype, that mutation will be shared on the prototype. If you replace the whole property, the change is reflected only on that instance

```js
var switchProto = {
		isOn: function isOn() {
			return this.state;
		},
		toggle: function toggle() {
			this.state = !this.state;
			return this;
		},
		meta: {
			name: 'Light switch'
		},
		state: false
	},
	switch1 = Object.create(switchProto),
	switch2 = Object.create(switchProto);

test('Prototype mutations.', function () {
	switch2.meta.name = 'Breaker switch';
	equal(switch1.meta.name, 'Breaker switch',
		'Object and array mutations are shared.'
	);
	switch2.meta = { name: 'Power switch' };
	equal(switch1.meta.name, 'Breaker switch',
		'Property replacement is instance-specific.'
	);
});
```

> Sharing state (non-method data) on a prototype property is commonly considered an anti-pattern in the JavaScript community, because accidental mutations of shared properties are a common source of bugs when you do it.

###Prototype Cloning

Sometimes you don’t want to share data on a prototype property. Instead, you want each instance to have its own unique copy of the prototype’s properties. Popular JavaScript libraries have been shipping with a suitable method for cloning prototypes for several years. The cloning method is usually called `.extend()` . You pass in an object to extend, followed by any number of objects to extend from.

Unfortunately, `extend()` is not included in the JavaScript specification yet ( `Object.assign()` works very similarly and is in JavaScript ES6). `extend()` is included in both jQuery and Underscore.

The primary difference between cloning and delegation is that cloning will copy the value of each property for every object instance, whereas delegation is more memory efficient. It stores only one copy of each default property setting until you need to override properties at the instance level. As it turns out, it’s often a good idea to employ a mix of both techniques—using the delegate prototype to share public methods between objects and cloning for data that can’t be safely shared between instances.

It’s often a good idea to employ a mix of both techniques—using the delegate prototype to share public methods between objects and cloning for data that can’t be safely shared between instances.


###The Flywieght pattern

The flyweight pattern conserves system resources by storing all reusable properties and methods on a delegate object, as opposed to storing copies of them on every instance. This can save a lot of memory and improve system performance dramatically if there are many objects of the same type.

In JavaScript, the delegate prototype serves as a built-in flyweight delegate. You don’t need to worry about wiring it up yourself, unlike other languages.

Imagine you’re programming a video game and there is a common enemy that has dozens or hundreds of copies in the game world. Each copy stores the enemy’s base stats, such as strength and speed, along with methods for all of its attacks and defenses. It also stores the enemy’s position in the game world and current health. You can optimize these objects in JavaScript by storing all of that data on the enemy prototype. When there is a change to the enemy’s health or position, those changes are made to the enemy instance, while all the other data and methods are delegated to the prototype:

```js
var enemyPrototype = {
		name: 'Wolf',
		position: { // Override this with setPosition
			x: 0,
			y: 0
		},
		setPosition: function setPosition (x, y) {
			this.position = {
				x: x,
				y: y
			};
			return this;
		},
		health: 20, // Overrides automatically on change
		bite: function bite() {
		},
		evade: function evade() {
		}
	},
	spawnEnemy = function () {
		return Object.create(enemyPrototype);
	};
test('Flyweight pattern.', function () {
	var wolf1 = spawnEnemy(),
	wolf2 = spawnEnemy();
	wolf1.health = 5;
	ok(wolf2.health = 20,
		'Primitives override automatically.');
	ok(wolf1.setPosition(10, 10)
		.position.x === 10, 'Object override works.');
	equal(wolf2.position.x, 0,
		'The prototype should remain unchanged.');
});
```

Because moving data to the prototype delegate is so easy in JavaScript, it’s common to employ the flyweight pattern for nearly all object methods.

It’s less common to see the prototype employed for member data, but it’s a perfectly reasonable place to store default values that are commonly reused. Just be mindful that you’ll need to replace member objects and arrays rather than mutate them in place if you want your changes to be instance safe.

##Object Creation

Objects in JavaScript are sometimes created with constructor functions. For example:

```js
function Car(color, direction, mph) {
	this.color = color || 'pink';
	this.direction = direction || 0; // 0 = Straight ahead
	this.mph = mph || 0;
	
	this.gas = function gas(amount) {
		amount = amount || 10;
		this.mph += amount;
		return this;
	};
	
	this.brake = function brake(amount) {
		amount = amount || 10;
		this.mph = ((this.mph - amount) < 0)? 0
			: this.mph - amount;
		return this;
	};
}

var myCar = new Car();

test('Constructor', function () {
	ok(myCar.color, 'Has a color');
	
	equal(myCar.gas().mph, 10,
		'.accelerate() should add 10mph.'
	);
	
	equal(myCar.brake(5).mph, 5,
		'.brake(5) should subtract 5mph.'
	);
});
```

You can get data encapsulation by using private variables:

```js
function Car(color, direction, mph) {
	var isParkingBrakeOn = false;
	this.color = color || 'pink';
	this.direction = direction || 0; // 0 = Straight ahead
	this.mph = mph || 0;
	
	this.gas = function gas(amount) {
		amount = amount || 10;
		this.mph += amount;
		return this;
	};
	
	this.brake = function brake(amount) {
		amount = amount || 10;
		this.mph = ((this.mph - amount) < 0)? 0
			: this.mph - amount;
		return this;
	};
	
	this.toggleParkingBrake = function toggleParkingBrake() {
		isParkingBrakeOn = !isParkingBreakOn;
		return this;
	};
	
	this.isParked = function isParked() {
		return isParkingBrakeOn;
	}
}

var myCar = new Car();

test('Constructor with private property.', function () {
	ok(myCar.color, 'Has a color');
	equal(myCar.gas().mph, 10,
	'.accelerate() should add 10mph.'
	);
	equal(myCar.brake(5).mph, 5,
	'.brake(5) should subtract 5mph.'
	);
	ok(myCar.toggleParkingBrake().isParked(),
	'.toggleParkingBrake works.'
	);
});
```

You can shave a bit of syntax using the object-literal form:

```js
	var myCar = {
	color: 'pink',
	direction: 0,
	mph: 0,
	
	gas: function gas(amount) {
		amount = amount || 10;
		this.mph += amount;
		return this;
	},
	
	brake: function brake(amount) {
		amount = amount || 10;
		this.mph = ((this.mph - amount) < 0)? 0
			: this.mph - amount;
		return this;
	}
};

test('Object literal notation.', function () {
	ok(myCar.color, 'Has a color');
	
	equal(myCar.gas().mph, 10,
		'.accelerate() should add 10mph.'
	);
	
	equal(myCar.brake(5).mph, 5,
		'.brake(5) should subtract 5mph.'
	);
});
```
Notice that because the object-literal form doesn't use a function, the encapsulation is gone.

##Factories

Object literals have great advantages, but they offer no way to create data privacy. Remember the first principle of OO design from the Gang of Four: “Program to an interface, not an implementation.” Encapsulation allows you to enforce that principle in your code, hiding implementation details from the user.

But constructor functions come with some drawbacks that are best avoided. A better solution is to use a factory method.

A *factory* is a method used to create other objects. Its purpose is to abstract the details of object creation from object use. In object-oriented design, factories are commonly used where a simple class is not enough.

Returning to the singleton example, sometimes it is useful to abstract the singleton reference behind a method call. You can make the singleton instance a private variable and return the reference from a closure:

```js
function factory() {
	var highlander = {
		name: 'MacLeod'
	};
	return {
		get: function get() {
			return highlander;
		}
	};
}

test('Singleton', function () {
	var singleton = factory();
	hero = singleton.get(),
	hero2 = singleton.get();
	
	hero.sword = 'Katana';
	
	// Since hero2.sword exists, you know it's the same object.
	ok(hero2.sword, 'There can be only one.');
});
```

As with the constructor function, you get data privacy by encapsulating private data inside the closure, so the only way to manipulate the state of the object is through the privileged methods.

Unlike the constructor function, you don’t have to invoke a factory with the `new` keyword.

you can also take advantage of prototypes to make the whole thing more efficient:

```js
var carPrototype = {
		gas: function gas(amount) {
			amount = amount || 10;
			this.mph += amount;
			return this;
		},
		brake: function brake(amount) {
			amount = amount || 10;
			this.mph = ((this.mph - amount) < 0)? 0
				: this.mph - amount;
			return this;
		},
		color: 'pink',
		direction: 0,
		mph: 0
	},
	
car = function car(options) {
return extend(Object.create(carPrototype), options);
},

myCar = car({
color: 'red'
});

test('Flyweight factory with cloning', function () {
	ok(Object.getPrototypeOf(myCar).gas,
		'Prototype methods are shared.'
	);
});
```

Notice that the factory method itself is now reduced to a one-liner. The arguments list is replaced with an options hash that allows you to specify exactly which values you want to override.

If you take advantage of the prototype feature, you can replace as much or as little of the prototype as you want at runtime. Using the same setup code as before by extending the `carPrototype`.


> You should avoid sharing objects and arrays on the prototype if they need to be instance safe. Instead, you can create new copies of the child object/array in the factory.


##Prototypal 	Inheritance with Stamps

JavaScript’s object capabilities are really flexible, but `Object.create()` isn't the easiest way to create a fully featured object.

Many libraries provide mechanisms to mimic classical inheritance, but there are few widespread libraries that simplify prototypal object creation, and certainly none that stand out as the gold standard. There is sugar for faking classes coming to JavaScript (I strongly discourage using it).

When thinking about object creation in JavaScript, it helps to ask, what are the important features of objects in JavaScript?

 * Delegate prototype
 * Instance state
 * Encapsulation

A *stamp* is a factory function that has public properties that specify a delegate prototype, default instance state, and a function that sets up encapsulation for object instances. Stamps utilize three different types of inheritance to create the new object:

 * Delegate prototype: delegation/differential inheritance
 * Instance state: cloning/concatenative inheritance/mixins
 * Encapsulation: functional inheritance

[Stampit](https://github.com/dilvie/stampit) is a library written for this book to demonstrate how we might use sugar to
simplify prototypal OO. It exports a single function. Here’s the signature:

```js
stampit(methods, state, enclose);
```

Here’s a more detailed look at how you’d use it to create an object from scratch:

```js
var testObj = stampit(
// methods
{
	delegateMethod: function delegateMethod() {
		return 'shared property';
	}
},

// state
{
	instanceProp: 'instance property'
},

// enclose
function () {
	var privateProp = 'private property';
	this.getPrivate = function getPrivate() {
		return privateProp;
	}
}).create();

test('Stampit with params', function () {
	equal(testObj.delegateMethod(), 'shared property',
		'delegate methods should be reachable');
	ok(Object.getPrototypeOf(testObj).delegateMethod,
		'delegate methods should be stored on the ' +
		'delegate prototype');
	equal(testObj.instanceProp, 'instance property',
		'state should be reachable.');
	ok(testObj.hasOwnProperty('instanceProp'),
		'state should be instance safe.');
	equal(testObj.hasOwnProperty('privateProp'), false,
		'should hide private properties');
	equal(testObj.getPrivate(), 'private property',
		'should support privileged methods');
});
```

Notice that the `.create()` method was called on the returned stamp in order to return the `testObj` instance. The `.create()` method simply returns a new object instance from the stamp. As you can see from the tests, all of the great features that make JavaScript’s object system special are available without jumping through all the hoops of setting up your own factory function, figuring out where to store prototypes, and so on.

The stamps returned by `stampit()` also contain methods that can be chained to further define the stamp. This is equivalent to the preceding stamp:

```js
var testObj = stampit().methods({
		delegateMethod: function delegateMethod() {
			return 'shared property';
		}
	})
	.state({
		instanceProp: 'instance property'
	})
	.enclose(function () {
		var privateProp = 'private property';
		this.getPrivate = function getPrivate() {
			return privateProp;
		}
	})
	.create();
```

The new object gets created with `Object.create()` using `methods` as the delegate prototype. Delegate methods are shared among all object instances, which conserve memory resources. If you change a prototype property at runtime, the value change is reflected on every instance of the object. To demonstrate:

```js
var stamp = stampit().methods({
		delegateMethod: function delegateMethod() {
			return 'shared property';
		}
	}),
	obj1 = stamp(),
	obj2 = stamp();

Object.getPrototypeOf(obj1).delegateMethod =
	function () {
		return 'altered';
	};
test('Prototype mutation', function () {
	equal(obj2.delegateMethod(), 'altered',
		'Instances share the delegate prototype.');
});
```
The `.state(`) method uses concatenative inheritance, which creates a copy of each property from the state prototypes to the new object, allowing you to safely store instance state. All stamps take an options hash that will be mixed into the instance properties, so it’s trivial to initialize a new object:

```js
var person = stampit().state({name: ''}),
	jimi = person({name: 'Jimi Hendrix'});
test('Initialization', function () {
	equal(jimi.name, 'Jimi Hendrix',
		'Object should be initialized.');
});
```

The `.enclose()` method uses functional inheritance, which invokes the functions you pass in against the newly created object. You can pass in any number of `.enclose()` functions. Private data will not collide, because a unique closure will be created for each function. Privileged methods will override methods of the same name. Last in wins.

Sometimes it’s useful to initialize an object with parameters that don’t correspond 1:1 with object properties. The way I prefer to do that is to decouple object instantiation and object initialization. You can do that by creating setters on the object:

```js
var person = stampit().enclose(function () {
	var firstName = '',
	lastName = '';
	
	this.getName = function getName() {
		return firstName + ' ' + lastName;
	};
	
	this.setName = function setName(options) {
		firstName = options.firstName || '';
		lastName = options.lastName || '';
		return this;
	};
}),

jimi = person().setName({
	firstName: 'Jimi',
	lastName: 'Hendrix'
});

test('Init method', function () {
	equal(jimi.getName(), 'Jimi Hendrix',
		'Object should be initialized.');
});
```

Of course, creating new objects is just scratching the surface of JavaScript’s OO capabilities.

First, you’ll use a closure to create data privacy:

```js
var a = stampit().enclose(function () {
	var a = 'a';
	this.getA =function () {
		return a;
	};
});
a().getA(); // 'a'
```

It uses function scope to encapsulate private data. Note that the getter  must be defined inside the function in order to access the closure variables. All privileged functions in JavaScript must be defined within the same scope as the private variables they need access to.

```js
var b = stampit().enclose(function () {
	var a = 'b';
	this.getB = function () {
		return a;
	};
});
b().getB(); // 'b'
```

`a` and `b` ’s private variables won’t clash. Here’s where it gets interesting:

```js
var c = stampit.compose(a, b),
	foo = c();

foo.getA(); // 'a'
foo.getB(); // 'b'
```

Stampit’s `.compose()` method allows you to inherit all three types of prototypes from any number of stamps. The preceding example demonstrates inheritance from multiple ancestors, including private data. Classical OO as we know it today has got nothing on this.

Each stamp has a special property called `fixed` , which stores `methods` , `state` , and `enclose` prototypes. When the stamp is invoked, the `state` prototype is copied so that it is instance safe, the `methods` property is used as the delegate prototype, and each `enclose` function is called in the order in which it was created to set up data privacy (last in wins in the case of name collisions).

##Conclusion

Stampit is in production use in web applications with tens of millions of users, so the full source contains several shims for older browsers that enable JavaScript ES5 features. Despite that, it weighs in at about 4 k minified and gzipped. The meat of it is about 90 lines of code (without the shims and comments).

JavaScript’s object system is flexible and powerful enough to do some
really amazing things with very little effort. Imagine how much more fun it could be with a little more built-in language sugar for working with prototypes. `Object.create()` is a good start, but we could do a lot more. By now you should have a whole new respect for prototypes and JavaScript’s object system.

Interest in inheritance seems to be waning as Node.js gains popularity. Part of the reason for that is modules. Modules do not compete with inheritance directly but do supply an alternative to inheritance as a code reuse mechanism. Stampit uses Node-style modules to reuse features from a modular utility library. The fact that the library is modular means that Stampit only gets the functionality it needs, and no more. Unlike classical inheritance, modules allow you to pick and choose which features you require.

> Written by Chirag Jain.