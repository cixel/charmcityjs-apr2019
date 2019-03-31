autoscale: true
theme: Plain Jane, 3
code: Hack
slidenumbers: true
footer: Ehden Sinai @ charmCityJs - 4/3/2019

[.header: alignment(center)]

# [fit] Playing with Proxy

## (And Reflect)

### (But Reflect Does Not Alliterate)

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

^ I'm going to talk about one of these tools in our toolbox, Proxy

---

# Proxy

*Used to define custom behavior for fundamental operations (e.g. property lookup, assignment, enumeration, function invocation, etc).*[^1]

[^1]: [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)

^ When we hear proxy we may think of like a proxy server. It's not that.

^ It's a global object, which I think first appeared in ES2015

^ start with a simple definition

![right fit](Proxy_concept_en.png)

---

## Terminology

**target**: the object being wrapped

**trap**: method providing intercept behavior for an operation

**handler**: object which holds traps

^ a few definitions to be aware of

^ [MAYBE DRINK HERE]

^ describe them

^ "now let's see an example of this in action"

---

[.code-highlight: all]
[.code-highlight: 1]
[.code-highlight: 3-20]
[.code-highlight: 4-19]
[.code-highlight: 22]
[.code-highlight: all]

^ here's an example of using a proxy to assimilate
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
- @cixel (Gopher slack)
- [github.com/cixel](https://github.com/cixel)
- [ehdens@gmail.com](mailto:ehdens@gmail.com)
- [ehden@contrastsecurity.com](mailto:ehden@contrastsecurity.com)

Image Credits:

- [Contrast Security](https://www.contrastsecurity.com)
- [H2g2bob \[CC0\]](https://upload.wikimedia.org/wikipedia/commons/b/bb/Proxy_concept_en.svg)

^ ![right](gophercolor.png)

