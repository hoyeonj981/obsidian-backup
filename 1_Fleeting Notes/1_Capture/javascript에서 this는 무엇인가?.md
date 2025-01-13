created at : 2024-09-06 11:24

#### tags

#

--- 

```
function hello(arg) {
	console.log(this + " " + arg);
}

hello("world");

// same
hello.call(window, "world");

// same
hello.call(undefined, "world");

```

```
let person = {
	name: "tester",
	hello: function(arg) {
		console.log(this + " " + arg);
	}
}

person.hello("world");

//same
person.hello.call(person, "world");

```
### References
---
[]()
