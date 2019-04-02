autoscale: true
theme: Plain Jane, 3
code: Hack
slidenumbers: true
footer: Ehden Sinai @ charmCityJs - 4/3/2019

[.header: alignment(center)]

# [fit] Playing with Proxy

## (And Reflect)

### (But Reflect Does Not Alliterate)

#### (So It Kinda Ruins the Title)

---

[.autoscale: true]

[.header: alignment(center)]
# Hello, I'm Ehden

node.js agent engineer @ Contrast Security

@ehden (CCJS slack)
[github.com/cixel](https://github.com/cixel)
[ehdens@gmail.com](mailto:ehdens@gmail.com)
[ehden@contrastsecurity.com](mailto:ehden@contrastsecurity.com)
![inline original 200%](Contrast-Logo-4C.pdf)

^ I'm an agent engineer at Contrast Security

^ Contrast makes web applications automatically detect vulnerabilities, identify attacks, and protect themselves

^ And it does this through runtime instrumentation

^ basically run inside customer's applications, watch what's going on

^ by adding behavior to existing code, as it's running

^ And the goal is to be as invisible as possible

^ We do this in 5 different languages right now, and I'm on the team that does this for Node.js

^ Now you might imagine that the implementation details differ a bit from language to language and it's up to each language team to fill up our own toolboxes with different ways to see what we need to see and do what we need to do

^ I'm going to talk about one of my favorite tools, Proxy

---

# Proxy

*Used to define custom behavior for fundamental operations (e.g. property lookup, assignment, enumeration, function invocation, etc).*[^1]

[^1]: [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)

^ When we hear proxy we may think of like a proxy server. It's not that.

^ here's the quick MDN definition

^ basically proxy lets you wrap objects and define custom behavior for a bunch of the basic operations that might be performed on that object

^ first appeared in ES6, so it's pretty new as far as the spec is concerned

^ and that means that people are still figuring out what the hell to do with it

^ it hasn't become very popular yet and

![right fit](Proxy_concept_en.png)

---
[.text: alignment(right)]
[.header: alignment(right)]
[.footer-style: alignment(right)]
[.slidenumber-style: alignment(left)]

# Reflect

*Reflect is a built-in object that provides methods for interceptable JavaScript operations. The methods are the same as those of proxy handlers. Reflect is not a function object, so it's not constructible.*[^2]

![left fit](Proxy_concept_en_mirror.png)

[^2]: [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Reflect)

^ Reflect is Proxy's sister object.

^ It's a collection of static methods which let you do all the stuff you need to do inside of a proxy

^ you can use it anywhere, not just with Proxy, but its methods are meant to mirror Proxy traps behavior

^ if that doesn't immediately make sense, it will when you see it in action

---

## Terminology 📖

### **target**: the object being wrapped (_must_ be an object)

### **handler**: object which holds traps

[.code-highlight: all]
[.code-highlight: 2-4]
[.code-highlight: 5-9]

```javascript
const proxy = new Proxy(
	{
		foo: 'bar'
	},
	{
		get(tar, prop, recv) { /* ... */ },
		set(tar, prop, val) { /* ... */ },
		// ...
	}
);
```

^ a few definitions to be aware of

^ BUILD

^ [MAYBE DRINK HERE]

^ target is kinda self-explanatory

^ BUILD

^ all the traps for the proxy collectively are the handler

---

## Terminology 📖

### **trap**: method providing intercept behavior for an operation

* `apply()`
* `construct()`
* `defineProperty()`
* `deleteProperty()`
* `get()`
* `getOwnPropertyDescriptor()`
* `getPrototypeOf()`
* `has()`
* `isExtensible()`
* `ownKeys()`
* `preventExtensions()`
* `set()`
* `setPrototypeOf()`

^ traps are the methods that define your custom behavior, name comes from OS traps

---

## Terminology 📖

### **invariant**: condition which must be satisfied by a trap

```javascript
const s = new String('hello');

const p = new Proxy(s, {
	get(target, property, receiver) {
		return 'fish';
	}
});


console.log(p[0]);
// TypeError: 'get' on proxy: property '0' is read-only and you tried to do something PRETTY silly there, friend
```

^ need to mention these

^ invariants are checks which prevent you from doing something really stupid

^ for each trap, there are different sets of invariants

^ MDN does a good job of telling you what these are

^ but they exist to keep you from doing something which makes no sense

^ you can't say, for instance, that the 0th character of a string object is 'fish'

^ this would probably crash v8

^ so instead you get a TypeError

^ so let's see an example of how you can use Proxy

---

[.code-highlight: all]
[.code-highlight: 1]
[.code-highlight: 3-20]
[.code-highlight: 4-19]
[.code-highlight: 22]
[.code-highlight: all]

^ first time I used this slide was for a group of college students, and there reaction was like. there wasn't one. I think the reference was completely lost on them because they weren't old
[BUILD]

^ person is the target
[BUILD]

^ handler is... the handler
[BUILD]

^ and you can see that it holds a couple of methods, get and set. these are *traps* for getting and setting properties
[BUILD]

^ then we call new proxy, passing the target and handler
anything get or set to borg will invoke those traps we defined in the handler

```javascript
const person = new Person('Jean-Luc Picard');

const handler = {
	get(target, property, receiver) {
		if (property === 'name') {
			return 'Locutus of Borg';
		}

		return Reflect.get(...arguments);
	},

	set(target, property, value) {
		if (property === 'name') {
			// don't actually set
			console.log('Resistance is futile.');
			return true
		}
		return Reflect.set(...arguments);
	}
};

const borg = new Proxy(person, handler);
```

![right fit](borg.jpg)

---

#*"Star Trek TNG is cool so Proxy should be added to the spec"*

### --W3C, Ecma, Brendan Eich, and Patrick Stewart

#### (all at the same time and in weird, hive-mind-like unison)

^ so besides making contrived sci-fi references, there's a bunch of cool stuff you can do with Proxy/Reflect

^ and again, they're pretty new and haven't seen a ton of use yet, so much of what those cool things are is still being actively figured out

^ but IMO the best way to see what something can really do is just mess around with it

^ so I'm gonna show a bunch of stuff you can do with Proxy/reflect, ranging from obviously useful to probably useless

^ and especially for the useful ones, some of you might wonder "why the hell isn't this used everywhere"

^ so one last quote

---

# Transparency

[.quote: alignment(left)]
>*“And these are your reasons, my lord?"*

[.quote: alignment(left)]
>*"Do you think I have others?" said Lord Vetinari. "My motives, as ever, are entirely transparent."*

[.quote: alignment(left)]
>*Hughnon reflected that 'entirely transparent' meant either that you could see right through them or that you couldn't see them at all.*
--Terry Pratchett, The Truth

![right](vetinari.jpg)

^ "my motives, as ever..."

^ in software especially, the word transparent can mean two completely different things

^ each definition has its value

^ different uses of proxy can be different kinds of transparent, and even both kinds at once under different lenses

^ and I think it's important to think about the attribute of transparency whenever you're doing any kind of instrumentation

^ which is an exercise that takes a bit of getting used to

^ so as I go through these, try to think for yourself about where semantic changes may occur, and how each use of a proxy may result in unintended interactions with the outside world

---

# [fit] 1. Monkey Patching

![100%](see-no-evil.png) ![100%](hear-no-evil.png) ![100%](speak-no-evil.png)

^ many of you probably know this, but monkey patching is changing or extending the functionality of a function at runtime

^ monkey patching lets you spy on code, change its behavior, add behavior, etc

^ in most connotations it's considered an anti-pattern but it's pretty amazing for doing some things

---

# Monkey Patching Functions (the old way)

```javascript
class MyClass {
	constructor() {}

	someFunction() {
		// do something
	}
}

const someFn = MyClass.prototype.someFunction;
MyClass.prototype.someFunction = function() {
	doSomething(this, arguments);
	const result = someFn.apply(this, arguments);
	doSomethingElse(this, arguments, result);
};
```

^ this is kind of the canonical way to patch

^ save the original function, re-define it, then clobber the reference and throw your own function in

^ this lets you do whatever you want to the this, args, result, etc

---

# Monkey Patching Functions (with Proxy/Reflect)

```javascript
class MyClass {
	constructor() {}

	someFunction() {
		// do something
	}
}

MyClass.prototype.someFunction = new Proxy(MyClass.prototype.someFunction, {
	apply(target, thisArg, args) {
		doSomething(thisArg, args);
		const result = Reflect.apply(...arguments);
		doSomethingElse(thisArg, args, result);
	}
})
```

^ so here is the implementation using a proxy

^ all we're trapping is apply

^ though to be completely correct we'd probably want to trap construct as well

^ nothing super crazy here. so why this vs the old way?

^ well for starters the original function still exists outside of the closure we created

^ but you also get a lot of other things: properties like function length, name, and toString are all perfectly preserved

^ calling .apply directly also makes assumptions about the function never having been clobbered,

^ or having its own apply function which shadows the apply we all know and love

---

# [fit] 2. Spying on Objects

---

# Spying on Objects

```javascript
const p = new Proxy({}, {
	get(tar, prop, recv) {
		console.log(`getting ${prop}`);
		return Reflect.get(...arguments);
	}

	set(tar, prop, value) {
		console.log(`setting ${prop}`);
		return Reflect.set(...arguments);
	}
});

p.a = '!'; // setting a
```

^ one of the canonical uses for monkey patching is to add logging

^ rather than redefining all the properties in an object as a setter or getter, you can do this

^ and it lets you spy on gets/sets of things which don't exist on the object yet

^ spying is nice, but if you want you can perform validation as well


---

# [fit] 3. Revoking Access to an Object

---

# `Proxy.revocable()`

```javascript
const obj = { a: 'test' };

const revocable = Proxy.revocable(obj, {
	// ...
});

revocable.revoke();

console.log(obj.a); // TypeError
```

^ Proxy has this static method revocable that gives you a proxy which has a revoke() method on it

^ when that's called, triggering any trap at all in the proxy results in a type error

^ this lets you create like temporary proxies

^ you can use this for a bunch of stuff. it's good if you may not completely trust a component you're giving object references to

^ but maybe you don't want to fully revoke the proxy

---

# Partially Revocable Proxies

```javascript
class Fickle {
	constructor() { this.revoked = false; }

	proxy(obj) {
		const handler = {
			get: (tar, prop, recv) => {
				console.log(this);
				if (this.revoked) throw new TypeError('nah');

				return Reflect.get(tar, prop, recv);
			}
		};
		return new Proxy(obj, handler);
	}

	revoke() { this.revoked = true; }
	restore() { this.revoked = false; }
}

const fickle = new Fickle();
const revokable = fickle.proxy({ a: 'test' });
fickle.revoke();
console.log(revokable.a); // TypeError: nah
```

^ so this is the first time you'll see us creating an abstraction on Proxy

^ here it's because we need some way to manage this revoked state

^ you can add as many things as you want to Fickle, and revoke them all at once

^

---

# Ideas + delete me

Go through these, progressively getting weirder or more useless. Maybe have a weirdness score and a usefulness score?

- spying
- revokable
- monkey patching
- properties which don't exist; impossible to inspect
- shadow objects - no need to proxy the actual target
- mocking
- no-body functions
- membranes

---

[.autoscale: true]
[.header: alignment(left)]

Contact:

[.list: bullet-character(>)]
- @ehden (CCJS slack)
- [github.com/cixel](https://github.com/cixel)
- [ehdens@gmail.com](mailto:ehdens@gmail.com)
- [ehden@contrastsecurity.com](mailto:ehden@contrastsecurity.com)

Image Credits:

- [Contrast Security](https://www.contrastsecurity.com)
- [H2g2bob \[CC0\]](https://upload.wikimedia.org/wikipedia/commons/b/bb/Proxy_concept_en.svg)
- [Borg](https://www.gettyimages.com/detail/news-photo/patrick-stewart-as-captain-jean-luc-picard-partially-news-photo/468146916)
- [Lord Vetinari by juliedillon](https://www.deviantart.com/juliedillon/art/Lord-Vetinari-92120272)
- [Join the Team](https://github.com/Contrast-Security-OSS/join-the-team)

![right](bw.jpg)

