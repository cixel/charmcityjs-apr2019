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

**target**: the object being wrapped

**trap**: method providing intercept behavior for an operation

**handler**: object which holds traps

^ a few definitions to be aware of

^ [MAYBE DRINK HERE]

^ target is kinda self-explanatory

^ traps are the methods that define your custom behavior, name comes from OS traps

^ all the traps for the proxy collectively are the handler

^ "now let's see an example of this in action"

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

	set(target, property, value, receiver) {
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

#*"Star Trek is cool so we should add Proxy to the spec"*

### --W3C, Ecma, and Brendan Eich

#### (at the same time and in weird, hive mind-like unison)

^ so besides making contrived sci-fi references, there's a bunch of cool stuff you can do with Proxy/Reflect

^ and again, they're pretty new and haven't seen a ton of use yet, so much of what those cool things are is still being actively figured out

^ but IMO the best way to see what something can really do is just mess around with it

^ so I'm gonna show a bunch of stuff you can do with Proxy/reflect, ranging from obviously useful to probably useless

^ and especially for the useful ones, some of you might wonder "why the hell isn't this used everywhere"

^ so i'm gonna throw up one last quote 

---

# Transparency

*“And these are your reasons, my lord?"*

*"Do you think I have others?" said Lord Vetinari. "My motives, as ever, are entirely transparent."*

*Hughnon reflected that 'entirely transparent' meant either that you could see right through them or that you couldn't see them at all.*

[.header: alignment(right)]

##### --Terry Pratchett, The Truth

---

## Transparency

[.quote: alignment(left)]
>*“And these are your reasons, my lord?"*

[.quote: alignment(right)]
>*"Do you think I have others?" said Lord Vetinari. "My motives, as ever, are entirely transparent."*

[.quote: alignment(center)]
>*Hughnon reflected that 'entirely transparent' meant either that you could see right through them or that you couldn't see them at all.*
--Terry Pratchett, The Truth

---

---

# Ideas + delete me

Go through these, progressively getting weirder or more useless. Maybe have a weirdness score and a usefulness score?

- spying
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
- [Join the Team](https://github.com/Contrast-Security-OSS/join-the-team)

![right](bw.jpg)

