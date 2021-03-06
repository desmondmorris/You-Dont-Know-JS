# You Don't Know JS: Up & Going
# Chapter 2: Into JavaScript

In the previous chapter, I introduced the basic building blocks of programming, such as variables, loops, conditionals, and functions. Of course, all the code shown has been in JavaScript. But in this chapter, we want to focus specifically on things you need to know about JavaScript to get up and going as a JS developer.

You should be aware that we will introduce quite a few concepts in this chapter which we will not fully explore here, but rather give you just a preview of them. You can think then of this chapter as an overview for the topics covered in detail throughout the rest of this book series.

Especially if you're new to JavaScript, you should expect to spend quite a bit of time going over the concepts and code examples here multiple times. Any good foundation is laid brick by brick, so don't expect that you'll get it all at once the first pass through.

Your journey to deeply learn JavaScript starts here.

## Values & Types

As we asserted in Chapter 1, JavaScript has typed values, not typed variables. The following built-in types are available:

* `string`
* `number`
* `boolean`
* `null` and `undefined`
* `object`
* (new to ES6) `symbol`

JavaScript provides a `typeof` operator which can examine a value and tell you what type it is.

```js
var a;
typeof a;				// "undefined"

a = "hello world";
typeof a;				// "string"

a = 42;
typeof a;				// "number"

a = true;
typeof a;				// "boolean"

a = null;
typeof a;				// "object" -- weird, bug

a = undefined;
typeof a;				// "undefined"

a = { b: "c" };
typeof a;				// "object"
```

The return value from the `typeof` operator is always one of six (seven as of ES6!) string values. That is, `typeof "abc"` returns `"string"`, not `string`.

Notice how the `a` variable holds every different type of value, and that despite appearances, `typeof a` is not asking for the "type of `a`", but rather for the "type of the value currently in `a`". That's because only values have types in JavaScript; variables are just simple containers for those values.

`typeof null` is an interesting case, because it errantly returns `"object"`, when you'd expect it to return `"null"`.

Also, note `a = undefined`. There we're explicitly setting `a` to this `undefined` value, but that is behaviorally no different from a variable that has no value set yet, like with the `var a;` line. There's actually several ways that a variable can get to this "undefined" value state, including functions which return no values and usage of the `void` operator.

The `object` type refers to a compound value where you can set properties that each hold their own values of any type. This is perhaps one of the most useful value types in all of JavaScript.

```js
var obj = {
	a: "hello world",
	b: 42,
	c: true
};

obj.a;		// "hello world"
obj.b;		// 42
obj.c;		// true

obj["a"];	// "hello world"
obj["b"];	// 42
obj["c"];	// true
```

Properties can either be accessed with "dot notation" `obj.a` or "bracket notation" `obj["a"]`. Dot notation is shorter and generally easier to read, and is thus preferred. Bracket notation is useful if you have a property name that has special characters in it, like `obj["hello world!"]` -- such properties are often referred to as *keys* when accessed via bracket notation.

Of course, bracket notation is also required if you want to access a property/key but the name is stored in another variable, such as:

```js
var obj = {
	a: "hello world",
	b: 42
};

var b = "a";

obj[b];			// "hello world"
obj["b"];		// 42
```

**Note:** For more information on JavaScript `object`s, see the *"this & Object Prototypes"* title of this book series, specifically Chapter 3.

There are a couple of other values that you will commonly interact with in JavaScript programs: *array* and *function*. But rather than being proper built-in types, these should be thought of more like sub-types, specialized versions of the `object` type.

An *array* is an `object` that holds values (of any type) not just in named properties/keys, but in numerically indexed positions. For example:

```js
var arr = [
	"hello world",
	42,
	true
];

arr[0];			// "hello world"
arr[1];			// 42
arr.length;		// 3

typeof arr;		// "object"
```

Since *array*s are special objects (as `typeof` implies), they can also have properties, including the automatically updated `length` property.

You theoretically could use an *array* as a normal object with named properties, or you could use an `object` but only give it numeric properties (`0`, `1`, etc) similar to an *array*. However, such would generally be considered improper usage of the respective types.

Use *array*s for numerically positioned values and use `object`s for named properties.

The other `object` sub-type you'll use all over your JS programs is *function*:

```js
function foo() {
	return 42;
}

foo.bar = "hello world";

typeof foo;			// "function"
typeof foo();		// "number"
typeof foo.bar;		// "string"
```

Again, *function*s are `objects` -- in this case `typeof` strangely gives `"function"` implying top-level status -- and can thus have properties, but you typically will not use function object properties broadly, just in limited cases.

**Note:** For more information on JS values and their types, see the first two chapters of the *"Types & Grammar"* title of this book series.

### Built-in Type Methods

The built-in types and sub-types we've just discussed have built-in behaviors exposed as properties and methods that are quite powerful and useful.

For example:

```js
var a = "hello world";
var b = 3.14159;

a.length;				// 11
a.toUpperCase();		// "HELLO WORLD"
b.toFixed(4);			// "3.1416"
```

The "how" behind being able to call `a.toUpperCase()` is more complicated than just that method existing on the value.

Briefly, there is a `String` (capital `S`) object-wrapper form, typically called a "native", that pairs with a primitive `string` value; it's this object-wrapper that defines the `toUpperCase()` method on its prototype. When you use a primitive value like `"hello world"` as an `object` by referencing a property or method, JS automatically "boxes" the value to that object-wrapper counterpart.

A `string` value can be wrapped by a `String`, a `number` can be wrapped by a `Number`, a `boolean` can be wrapped by a `Boolean`, etc. For the most part, you don't need to worry about or directly use these object-wrapper forms of the values -- prefer the primitive value forms in practically all cases and JavaScript will take care of the rest for you.

**Note:** For more information on JS natives and "boxing", see Chapter 3 of the *"Types & Grammar"* title of this book series. To better understand the prototype of an object, see Chapter 5 of the *"this & Object Prototypes"* title of this book series.

### Comparing Values

There two main types of value comparison that you will need to make in your JS programs: *equality* and *inequality*. The end result of any comparison is a strictly `boolean` value (`true` or `false`), regardless of what value types are compared.

#### Equality

There are four *equality* operators: `==`, `===`, `!=`, and `!==`. The `!` forms are of course the symmetric "not equal" versions of their counterparts; non-*equality* should not be confused with *inequality*.

The difference between `==` and `===` is usually characterized that `==` checks for value equality and `===` checks for both value and type equality. However, this is inaccurate. The proper way to characterize them is that `==` checks for value equality with *coercion* allowed, and `===` checks for value equality without allowing *coercion*; `===` is often called "strict equality" for this reason.

We talked briefly about *coercion* in Chapter 1, but let's revisit it here.

Coercion comes in two forms in JavaScript: *explicit* and *implicit*. Explicit coercion is simply that you can see obviously from the code that a conversion from one type to another will occur, whereas implicit coercion is when the type conversion can happen as more of a non-obvious side effect of some other operation.

You've probably heard sentiments like "coercion is evil" drawn from the fact that there are clearly places where coercion can produce some surprising results. Perhaps nothing evokes ire from developers more than when the language surprises them.

Coercion is not evil, nor does it have to be surprising. In fact, the majority of cases you can construct around type coercion are quite sensible and understandable, and can even be used to **improve** the readability of your code. But we won't go much further into that debate -- Chapter 4 of the *"Types & Grammar"* title of this book series covers all sides.

Explicit coercion:

```js
var a = "42";

var b = Number( a );

a;				// "42"
b;				// 42 -- the number!
```

Implicit coercion:

```js
var a = "42";

var b = a + 0;	// "42" implicitly coerced to 42 here

a;				// "42"
b;				// 42 -- the number!
```

It's the implicit flavor of coercion that best describes what happens with the coercion-enabled `==` equality. For example:

```js
var a = "42";
var b = 42;

a == b;			// true
a === b;		// false
```

In the `a == b` comparison, JS notices that the types do not match, so it goes through an ordered series of steps to coerce one or both values to a different type until the types match, where then a simple value equality can be checked.

If you think about it, there's two possible ways `a == b` could give `true` via coercion. Either the comparison could end up as `42 == 42` or it could be `"42" == "42"`. So which is it?

The answer: `"42"` becomes `42`, to make the comparison `42 == 42`. In such a simple example, it doesn't really seem to matter which way that process goes, as the end result is the same. There are more complex cases where it matters not just what the end result of the comparison is, but *how* you get there.

The `a === b` produces `false`, because the coercion is not allowed, so the simple value comparison obviously fails. Many developers feel that `===` is more predictable, so they advocate always using that form and staying away from `==`. I think this view is very short-sighted. I believe `==` is a powerful tool that helps your program, **if you take the time to learn how it works.**

We're not going to cover all the nitty gritty details of how the coercion in `==` comparisons works here. Much of it is pretty sensible, but there are some important corner cases to be careful of. You can read the specification (section 11.9.3) (http://www.ecma-international.org/ecma-262/5.1/) to see the exact rules, and you'll be surprised at just how straightforward this mechanism is, compared to all the negative hype surrounding it.

To boil down a whole lot of details to a few simple take-aways, and help you know whether to use `==` or `===` in various situations, here's my simple rules:

1. If either value (aka side) in a comparison could be the literal `true` or `false` value, avoid `==` and use `===`.
2. If either value in a comparison could be of these specific values (`0`, `""`, or `[]` -- empty array), avoid `==` and use `===`.
3. In *all* other cases, you're safe to use `==`. Not only is it safe, but in many cases it simplifies your code in a way that improves readability.

What these rules boil down to is requiring you to think critically about your code and about what kinds of values can come through variables that get compared for equality. If you can be certain about the values, and `==` is safe, use it! If you can't be certain about the values, use `===`. It's that simple.

The `!=` non-equality form pairs with `==`, and the `!==` form pairs with `===`. All the rules and observations we just discussed hold symmetrically for these non-equality comparisons.

You should take special note of the `==` and `===` comparison rules if you're comparing with non-primitive values like `object`s (including `function` and `array`). Since those values are actually held by reference,

**Note:** For more information about the `==` equality comparison rules, see the specification (section 11.9.3) and also consult Chapter 4 of the *"Types & Grammar"* title of this book series.

#### Inequality

The `<`, `>`, `<=`, and `>=` operators are used for inequality, referred to in the specification as "relational comparison". Typically they will be used with ordinally comparable values like `number`s. It's easy to understand that `3 < 4`.

But JavaScript `string` values can also be compared for inequality, using typical alphabetic rules (`"bar" < "foo"`).

What about coercion? Similar (though not exactly identical!) rules to `==` comparison apply to the inequality operators. In fact, there are no "strict inequality" operators which would disallow coercion the same way `===` does with equality.

Consider:

```js
var a = 41;
var b = "42";
var c = "43";

a < b;		// true
b < c;		// true
```

What happens here? The specification says (section 11.8.5) that if both values in the `<` comparison are `string`s, as it is with `b < c`, the comparison is made lexiographically (aka alphabetically like a dictionary). But if one or both is not a `string`, as it is with `a < b`, then both values are coerced to be `number`s, and a typical numeric comparison occurs.

The biggest gotcha you may run into here with comparisons between potentially different value types -- remember, there are no strict inequality forms to use -- is when one of the values cannot be made into a valid number, such as:

```js
var a = 42;
var b = "foo";

a < b;		// false
a > b;		// false
a == b;		// false
```

Wait, how can all three of those comparisons be `false`? Because the `b` value is being coerced to the "invalid number value" `NaN` in the `<` and `>` comparisons, and the specification says that `NaN` is neither greater-than nor less-than any other value.

The `==` comparison fails for a different reason. `a == b` could fail either as `42 == NaN` or `"42" == "foo"` -- as we explained earlier, the latter is the case.

**Note:** For more information about the inequality comparison rules, see the specification (section 11.8.5) and also consult Chapter 4 of the *"Types & Grammar"* title of this book series.

## Variables

In JavaScript, variable names (as well as function names) must be valid *identifiers*. The strict and complete rules for valid characters in identifiers are a little complex when you consider non-traditional characters such as unicode. If you only consider typical ascii alphanumeric characters, though, the rules are simple.

An identifier must start with `a`-`z`, `A`-`Z`, `$`, or `_`. It can then contain any of those characters plus the numerals `0`-`9`.

Generally, the same rules apply to a property name as to a variable identifier. However, certain words cannot be used as variables, but are OK as property names. These words are called "reserved words", and include the JS keywords (`for`, `in`, `if`, etc) as well as `null`, `true`, and `false`.

**Note:** For more information about reserved words, see Appendix A of the *"Types & Grammar"* title of this book series.

### Scope & Functions

You use the `var` keyword to declare a variable that will belong to the current function (or global, if top-level) scope.

Wherever a `var` appears inside a function, that declaration is taken to belong to the function itself. Metaphorically, developers call this process "hoisting" when a `var` declaration is conceptually "moved" to the top of its enclosing function. Technically, this process is more related to how your code is compiled, but we can skim over those details for now.

When you declare a variable, it is available anywhere inside that scope, as well as any lower/inner scopes.

```js
function foo() {
	var a = 1;

	function bar() {
		var b = 2;

		function baz() {
			var c = 3;

			console.log( a, b, c );	// 1 2 3
		}

		baz();
		console.log( a, b );		// 1 2
	}

	bar();
	console.log( a );				// 1
}

foo();
```

Notice that `c` is not available inside of `bar()`, since it's declared only inside the inner `baz()` scope, and that `b` is not available to `foo()` for the same reason.

If you try to access a variable's value in a scope where it's not available, you'll get a `ReferenceError` thrown. If you try to set a variable that hasn't been declared, you'll either end up creating a global (bad!) or getting an error, depending on "strict mode" (see section below).

```js
function foo() {
	a = 1;	// `a` not formally declared
}

foo();
a;			// 1 -- oops, auto global :(
```

This is a very bad practice. Don't do it! Always formally declare your variables.

In addition to creating declarations for variables at the function level, ES6 *lets* you declare variables to belong to individual blocks (pairs of `{ .. }`), using the `let` keyword. Besides some nuanced details, the scoping rules will behave roughly the same as we just saw with functions:

```js
function foo() {
	var a = 1;

	if (a >= 1) {
		let b = 2;

		while (b < 5) {
			let c = b * 2;
			b++;

			console.log( a + c );
		}
	}
}

foo();
// 5 7 9
```

Because of using `let` instead of `var`, `b` will belong only to the `if` statement and thus not to the whole `foo()` function's scope. Similarly, `c` belongs only to the `while` loop. Block scoping is very useful for managing your variable scopes more fine-grained, which can make your code much easier to maintain over time.

**Note:** For more information about scope, see the first four chapters of the *"Scope & Closures"* title of this book series.

## Conditionals

In addition to the `if` statement we introduced briefly in Chapter 1, JavaScript provides a few other conditionals mechanisms, which we should glance at.

Sometimes you may find yourself writing a series of `if..else..if` statements like this:

```js
if (a == 2) {
	// do something
}
else if (a == 10) {
	// do another thing
}
else if (a == 42) {
	// do yet another thing
}
else {
	// fallback to here
}
```

This structure works but it's a little verbose because you're having to specify the `a` test for each case. Here's another option, the `switch` statement:

```js
switch (a) {
	case 2:
		// do something
		break;
	case 10:
		// do another thing
		break;
	case 42:
		// do yet another thing
		break;
	default:
		// fallback to here
}
```

The `break` is important if you want only the statement(s) in one `case` to run. If you omit `break` from a `case`, and that `case` matches or runs, execution will continue with the next `case`'s statements regardless of that `case` matching. This so called "fall through" is sometimes useful/desired:

```js
switch (a) {
	case 2:
	case 10:
		// some cool stuff
		break;
	case 42:
		// other stuff
		break;
	default:
		// fallback
}
```

Here, if `a` is either `2` or `10`, it will execute the "some cool stuff" code statements.

Another form of conditional in JavaScript is the "conditional operator", often called the "ternary operator". It's like a more concise form of a single `if..else` statement, such as:

```js
var a = 42;

var b = (a > 41) ? "hello" : "world";
```

If the test expression (`a > 41` here) evaluates as `true`, the first clause (`"hello"`) results, otherwise the second clause (`"world"`) results.

The conditional operator doesn't have to be used in an assignment, but that's definitely the most common usage.

**Note:** For more information about testing conditions and other patterns for `switch` and `? :`, see the *"Types & Grammar"* title of this book series.

## Strict Mode

ES5 added a "strict mode" to the language, which tightens the rules for certain behaviors. Generally, these restrictions are seen as keeping the code to a safer and more appropriate set of guidelines. Also, adhering to strict mode makes your code generally more optimizable by the engine. Strict mode is a big win for code, and you should use it for all your programs.

You can opt-in to strict mode for an individual function, or an entire file, depending on where you put the strict mode pragma:

```js
function foo() {
	"use strict";

	// this code is strict mode

	function bar() {
		// this code is strict mode
	}
}

// this code is not stict mode
```

Compare that to:

```js
"use strict";

function foo() {
	// this code is strict mode

	function bar() {
		// this code is strict mode
	}
}

// this code is strict mode
```

One key difference (improvement!) with strict mode is disallowing the implicit auto-global declaration, omitting the `var`:

```js
function foo() {
	"use strict";
	a = 1;			// ReferenceError
}

foo();
```

If you turn on strict mode in your code, and you get errors, or code starts behaving buggy, your temptation may be to avoid strict mode. But that instinct would be a bad idea to indulge. If strict mode causes issues in your program, almost certainly it's a sign that you have things in your program you should fix.

Not only will strict mode keep your code to a safer path, and not only will it make your code more optimizable, but it also represents the future direction of the language. It'd be easier on you to get used to strict mode now than to keep putting it off -- it'll only get harder to convert later!

**Note:** For more information about strict mode, see the Chapter 5 of the *"Types & Grammar"* title of this book series.

## Function Closure

Closure is one of the most important, and often least understood, concepts in JavaScript. I won't cover it in detail here, since the *"Scope & Closures"* title in this book series does so. But we want to say a few things about it so you understand the general concept.

You can think of closure as a way to "remember" and continue to access a function's scope (its inner variables) even once the function has finished running.

The most common example of using closure is with the "module pattern", so that's how we'll illustrate it. This pattern is a way for you to define private implementation details (variables, functions) that are hidden from the outside world (similar to `private` members of a class), as well as a public API that is accessible from the outside.

Consider:

```js
var User = (function(){
	var username, password;

	function doLogin(user,pw) {
		username = user;
		password = pw;

		// do the rest of the login work
	}

	var publicAPI = {
		login: doLogin
	};

	return publicAPI;
})();

User.login( "fred", "12Battery34!" );
```

Let's briefly break down what's going on here.

First, the `(function(){ .. })()` part is called an IIFE (Immediately-Invoked-Function-Expression). For our purposes, it's just serving as the surrounding function so we can get closure with it.

The `username`, `password`, and `doLogin()` are all private inner details of this `User` module; they cannot be accessed from the outside world.

`publicAPI` is an object with one property/method on it `login`, which is a reference to the inner `doLogin()` function. When we return `publicAPI` from this outer IIFE being executed, that `object` is assigned to the variable `User`.

At this point, the outer function has finished executing. Normally, you'd think the inner variables like `username` and `password` have gone away. But they have not, because there's a closure keeping them alive.

That's why we can later call `User.login(..)`, which is like calling the inner `doLogin(..)`, and *that* function can still access `username` and `password` inner variables. Why? Because that scope is still around, because of closure.

There's a good chance that with just this brief glimpse at closure and the module pattern, some of it is still a bit confusing. That's OK! It takes some work to wrap your brain around it. From here, go read the *"Scope & Closures"* title of this book series for a much more in-depth exploration.

## `this` Keyword

Another very commonly misunderstood concept in JavaScript is the `this` keyword. Again, there's a whole set of book chapters on it in the *"this & Object Prototypes"* title of this book series, so here we'll just briefly introduce the concept.

If a function has a `this` keyword reference inside it, that `this` keyword usually points to an `object`. But which `object` it points to depends on how the function was called.

It's important to realize that `this` **does not** refer to the function itself, as is the most common misconception.

Here's a quick illustration:

```js
function foo() {
	console.log( this.bar );
}

var bar = "global";

var obj1 = {
	bar: "obj1",
	foo: foo
};

var obj2 = {
	bar: "obj2"
};

// --------

foo();				// "global"
obj1.foo();			// "obj1"
foo.call( obj2 );	// "obj2"
new foo();			// undefined
```

There are four rules for how `this` gets set, and they're shown in those last four lines of that snippet.

1. `foo()` ends up setting `this` to the global object in non-strict mode -- in strict mode, `this` would be `undefined` and you'd get an error -- so `"global"` is the value found.
2. `obj1.foo()` sets `this` to the `obj1` object.
3. `foo.call(obj2)` sets `this` to the `obj2` object.
4. `new foo()` sets `this` to a brand new empty object.

Bottom line: to understand what `this` points to, you have to examine how the function in question was called. It will be one of those four ways just shown, and that will then answer what `this` is.

You'll notice that in none of those cases is `this` referring to the `foo()` function itself. If you wanted to manually force that condition, you could do:

```js
function foo() {
	var a = 1;
	console.log( a, this.a );
}

foo.a = 2;

foo.call( foo ); // 1 2
```

Here, we illustrate that even if we force `this` to point to `foo()`, it's not pointing to the internal scope where the `a` variable of value `1` is declared, but rather to the function object, where we added an `a` property of value `2`.

**There is no way to have `this` point to the internal scope of `foo()`, no matter how hard you try.**

**Note:** For more information about `this`, see the first two chapters of the *"this & Object Prototypes"* title of this book series.

## Prototypes

The prototype mechanism in JavaScript is quite complicated. We will only glance at it here. You will want to spend plenty of time on the last three chapters of the *"this & Object Prototypes"* title of this book series to get all the details down.

When you reference a property on an object, if that property doesn't exist, JavaScript will automatically use that object's internal prototype reference to find another object to look for the property on. You could think of this almost as a fallback if the property is missing.

The internal prototype reference linkage from one object to its fallback happens at the time the object is created. The simplest way to illustrate it is with an ES5 built-in utility called `Object.create(..)`. Consider:

```js
var foo = {
	a: 42
};

// create `bar` and link it to `foo`
var bar = Object.create( foo );

bar.b = "hello world";

bar.a;		// 42
bar.b;		// "hello world"
```

Here, `bar.a` doesn't actually exist, but since `bar` is prototype-linked to `foo`, JS automatically falls back to looking for `a` on the `foo` object, where it's found. Though we're using a property like `a`, the exact same look-up behavior would happen if `a` were a function instead of the value `42`. It's perhaps more common to access functions via this linkage than properties, but the JS engine treats both cases basically identically.

Objects can be linked in an arbitrarily long chain, such as if a `baz = Object.create(bar)` were run in that snippet and then the linkage would be `baz` -> `bar` -> `foo`.

This linkage may seem like a strange feature of the language. The most common way this feature is used -- and I would argue, abused -- is to try to emulate/fake a "class" mechanism with "inheritance".

But a more natural way of applying prototypes is a pattern called "behavior delegation", where you intentionally design your linked objects to be able to **delegate** from one to the other for parts of the needed behavior.

**Note:** For more information about prototypes and behavior delegation, see Chapters 3-6 of the *"this & Object Prototypes"* title of this book series.

## Old & New

Some of the JS features we've already covered, and certainly many of the features covered in the rest of this book series, are newer additions and will not necessarily be available in older browsers. In fact, some of the newest features in the specification aren't even implemented in any stable browsers yet.

So, what do you do with the new stuff? Do you just have to wait around for years or decades for all the old browsers to fade into obscurity?

That's how many people think about the situation, but it's really not a healthy approach to JS.

There's two main techniques you can use to "bring" the newer JavaScript stuff to the older browsers: polyfilling and transpiling.

### Polyfilling

The word "polyfill" is an invented term (by Remy Sharp) (https://remysharp.com/2010/10/08/what-is-a-polyfill) used to refer to taking a newer feature and producing a piece of code that's equivalent to the behavior, but which can run in older JS environments.

For example, ES6 defines a utility called `Number.isNaN(..)` to provide an accurate non-buggy check for `NaN` values, replacing the original global `isNaN(..)` utility. But it's easy to polyfill that utility so that you can start using it in your code regardless of whether the end user is in an ES6 browser or not.

Consider:

```js
if (!Number.isNaN) {
	Number.isNaN = function isNaN(x) {
		return x !== x;
	};
}
```

First, the `if` statement guards against applying the polyfill definition in ES6 browsers where it will already exist. If it's not already present, we define `Number.isNaN(..)`.

The check we do takes advantage of a quick with `NaN` values, which is that they're the only value in the whole language that is not equal to itself. So the `NaN` value is the only one that would make `x !== x` be `true`.

Not all new features are fully polyfillable. Sometimes most of the behavior can be polyfilled, but there are small deviations. You should be really, really careful in implementing a polyfill yourself, to make sure you really are as strictly adhering to the specification as possible.

Or better yet, use an already vetted set of polyfills that you can trust, such as those provided by ES5-Shim (https://github.com/es-shims/es5-shim) and ES6-Shim (https://github.com/es-shims/es6-shim).

### Transpiling

Polyfilling only works for APIs (methods, properties) but there's no way to polyfill when new syntax has been added to the language. The reason is, the new syntax would throw an error in the old JS engine as unrecognized/invalid.

So the better option is to use a tool that converts your newer code into older code equivalents. This process is commonly called "transpiling", a term for transforming + compiling. Essentially, your source code is in the new sytnax form, but what you deploy to the browser is the transpiled code in old syntax form. You typically insert the transpiler into your build process, similar to your code linter or your minifier.

You might wonder why you'd go to the trouble to write new syntax only to have it transpiled away to older code -- why not just write the older code directly?

There's several important reasons you should care about transpiling:

1. The new syntax added to the language is designed to make your code more readable and maintainable. The older equivalents are often much more convoluted. You should prefer writing newer and cleaner syntax, not only for yourself but for all other members of the development team.
2. If you transpile only for older browsers, but serve the new syntax to the newest browsers, you get to take advantage of browser performance optimizations with the new sytnax. This also lets browser makers have more real-world code to test their implementations and optimizations on.
3. Using the new syntax earlier allows it to be tested more robustly in the real world, which provides earlier feedback to the JavaScript committee (TC39). If issues are found early enough, they can be changed/fixed before those language design mistakes become permanent.

You also may wonder what happens with stack traces in your debugging console. The reputable transpilers all support "Source Maps", which is a way for your tool chain to preserve the mapping between the original new syntax code you wrote and the transpiled old syntax code running in the browser.

When a debug break point or error is thrown, the developer tools can use the mapping to show you the original authored source code instead of the actual running transpiled code. That's a big win for productivity!

Here's a quick example of transpiling. ES6 adds a feature called "default parameter values". It looks like this:

```js
function foo(a = 2) {
	console.log( a );
}

foo();		// 2
foo( 42 );	// 42
```

Simple, right? Helpful, too! But it's new syntax that's invalid in pre-ES6 engines. So what will a transpiler do with that code to make it run in older environments?

```js
function foo() {
	var a = arguments[0] !== (void 0) ? arguments[0] : 2;
	console.log( a );
}
```

As you can see, it checks to see if the `arguments[0]` value is `void 0` (aka `undefined`), and if so provides the `2` default value, otherwise it assigns whatever was passed.

In addition to being able to now use the nicer syntax even in older browsers, looking at the transpiled code actually explains the intended behavior more clearly. You may not have realized just from looking at the ES6 version that `undefined` is the only value that can't get explicitly passed in for a default-value parameter, but the transpiled code makes that much more clear.

The last important detail to emphasize about transpilers is that they should now be thought of as a standard part of the JS development ecosystem and process. JS is going to continue to evolve, much more quickly than before, so every few months new syntax and new features will be added. If you use a transpiler by default, you'll always be able to make that switch to newer syntax whenever you find it useful, rather than always waiting for years for today's browsers to phase out.

There are quite a few great transpilers for you to choose from. Here are some good options as of time of writing:

* 6to5 (https://github.com/6to5/6to5): transpiles ES6 into ES5
* Traceur (https://github.com/google/traceur-compiler): transpiles ES6, ES7, .. into ES5.

## Non-JavaScript

So far, the only things we've covered are in the JS language itself. The reality is that most JS is written to run in and interact with environments like browsers. A good chunk of the stuff that you write in your code is, strictly speaking, not directly controlled by JavaScript. That probably sounds a little strange.

The most common non-JavaScript JavaScript you'll encounter is the DOM API. For example:

```js
var el = document.getElementByID( "foo" );
```

The `document` variable exists as a global variable when your code is running in a browser. It's not provided by the JS engine, nor is it particularly controlled by the JavaScript specification. It takes the form of something that looks an awful lot like a normal JS `object`, but it's not really exactly that. It's a special `object,` often called a "host object".

Moreover, the `getElementByID(..)` method on `document` looks like a normal JS function, but it's just a thinly exposed interface to a built-in method provided by the DOM from your browser. In some (newer-generation) browsers, this layer may also be in JS, but traditionally the DOM and its behavior is implemented in something more like C/C++.

Another example is with I/O (input/output). For example: everyone's favorite `alert(..)` that pops up a message box in the user's browser window. `alert(..)` is provided to your JS program by the browser, not by the JS engine itself. The call you make sends that information to the browser internals and it handles drawing and displaying the message box.

The same goes with `console.log(..)`. Your browser provides such mechanisms and hooks them up to the developer tools.

This book, and this whole book series, focuses on JavaScript the language. That's why you don't see any substantial coverage of these non-JavaScript JavaScript mechanisms.

## Review

The first step to learning JavaScript's flavor of programming is to get a basic understandng of its core mechanisms like values, types, function closures, `this`, and prototypes.

Of course, each of these topics deserves much greater coverage than you've seen here, but that's why they have chapters and books dedicated to them throughout the rest of this series. After you feel pretty comfortable with the concepts and code samples in this chapter, the rest of the series awaits you to really dig in and get to know the language.

The final (next) chapter of this book will briefly overview each of the other titles in the series and the other concepts that they tackle, which didn't fit nicely into this chapter's discussions.
