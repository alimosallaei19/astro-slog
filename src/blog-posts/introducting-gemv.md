---
title: Introducting GEMV
desc: A new website framework, designed for people who love VanillaJS.
date: 2022-02-02T15:14:28.825Z
---
> Before I actually begin this piece, I do not have any public code for this yet.

> Also, a word for the wise: *this method may still leave a lot of vulnerabilities in your code. This should NOT be adopted for enterprise-scale projects.

### Meet GEMV (pronounced *jem-vee*).

This is the website framework for people who really love the HTML/CSS/JS stack, but hate its security vulnerabilities. Here's what it stands for:

* **G**: [Grunt](https://gruntjs.com/)
* **E**: [ExpressJS](https://expressjs.com/)
* **M**: [MongoDB](https://www.mongodb.com/)
* **V**: [VanillaJS](http://vanilla-js.com/)

I have always disliked front-end web frameworks, like React, Vue and NextJS: they all have very non-intuitive and complex ways of doing simple things, like binding variables and submitting forms. 

Guilty as charged: I really like the simplicity of VanillaJS with HTML. Yes, the same thing could be done with less lines, but it makes *sense*. When did `useEffect` ever become a thing in React?

> *(The best option out of all of SSR frameworks is Svelte, hands down, but that's a story for later)*

Here's a snippet of code from a React application to build a simplified calculator:

```
import React, { useState } from 'react';

export default () => {
	const [a, setA] = useState(1);
	const [b, setB] = useState(2);

	function handleChangeA(event) {
		setA(+event.target.value);
	}

	function handleChangeB(event) {
		setB(+event.target.value);
	}

	return (
		<div>
			<input type="number" value={a} onChange={handleChangeA}/>
			<input type="number" value={b} onChange={handleChangeB}/>

			<p>{a} + {b} = {a + b}</p>
		</div>
	);
};
```

There is a lot of, in my opinion, really boilerplate code that needs to go in to get such an application working. This could be done much more simply with some `window.events` in VanillaJS, and it, to me, makes a lot more sense.

### So, what does each part in GEMV do?

The **Grunt** part of this manages and obfuscates all of the JS files on build, so as to provide a layer of protection against people digging around for any APIs or *secret password* stuffs you have hidden around. Grunt uses the widely-used [javascript-obfuscator](https://github.com/javascript-obfuscator/javascript-obfuscator), and the `Gruntfile` looks a little like this:

```
'use strict';
module.exports = function (grunt) {
    grunt.loadNpmTasks('grunt-contrib-obfuscator');
    grunt.initConfig({
        obfuscator: {
            options: {
                banner: '// Hello :).\n',
                debugProtection: true,
                debugProtectionInterval: true
            },
            task3: {
                files: {
                    'public/': 'scripts/*.js'
                }
            }
        }
    });
    grunt.registerTask('default', ['obfuscator']);
};
```

The **Express** portion of the app is your middleware. It takes care of loading your API, while also serving your public pages. Here's an example of that:

```
app.use(express.static('public',{extensions:['html']}));

app.post("/api", async (req, res) => {
	console.log(req.body)
	try {
		var enda = `./api/${req.body.api}.js`
		let commandFile = require(enda);
		var tooez = await commandFile.run(req.body);
		res.json(tooez)
	} catch (err) {
		console.log(err);
		if (err.code === "MODULE_NOT_FOUND") {
			return;
		}
	}
})
```

The **MongoDB** portion allows you to have a database. I high suggest using the free MongoDB Atlas tier to host a database for developmental purposes. It gives you 512mb of storage, and 500 connections which is *plenty*.

Lastly, the **VanillaJS** portion hydrates your client-side HTML. It allows you to interact with the HTML page, and is what is obfuscated by Grunt.

### Why?

Because it's fun to see all the different possibilities that exist. This is a stack I want to create a project with in the near future, and I want to see how it scales. So far, it has been great to see that there are systems out there to make these a reality. Let me know if you want a template for a GEMV project; I'll post it here!