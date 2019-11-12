---
layout: post
title:      "JavaScript and Rails Project: Coffee Cups 3.0"
date:       2019-11-12 02:03:46 -0500
permalink:  javascript_and_rails_project_coffee_cups_3_0
---


For my Single Page Application JavaScript and Rails project, I applied the same domain model used in both my Sinatra and Rails projects. In a nutshell (or a coffee bean...if you will), a user can post about cups of coffee they sip, describing the type of coffee beans used to brew the cup, the brew method itself, the roaster that produced or roasted the coffee, and finally the user rates their cup on a scale of 1-5. Given that `User`, `Cup`, `Coffee`, `Roaster`, and `Rating` are all model classes associated with one another in various ways, my backend is somewhat complex, but I kept my JS frontend pretty simple. A user may sign up, post cups, and view other users' cups, all in a fast, responsive, JS-powered web page. 

Lets turn our focus to a common JS bug that was introduced to us in the JS and Rails module that I actually encountered while building my project: The Lost Context Bug. If we recall that every function has its own execution context, which is essentially the object to the left of the dot when the function is called, or if no such object was used to call the function, the execution context will be something weird like `window` or `undefined`. Furthermore, inside of the function, `this` will point to the function's execution context, kind of mirroring `self` in Ruby. 

Let's take a look at this function from my JS code:
```
fetchSelectOptions(roasterId) {
        let url
        
        if (roasterId) url = `${BASE_URL}/roasters/${roasterId}/${this.selectHelper.type}s`
        else url = `${BASE_URL}/${this.selectHelper.type}s`
    
        function renderObjOptions(objs) {
            objs.forEach((obj) => {
                this.selectHelper.selectNode.appendChild(this.selectHelper.renderOption(obj)) 
            })
        }
        
        fetch(url)
        .then(resp => resp.json())
        .then(json => renderObjOptions.call(this, json))
        .catch(error => console.log(error.message))
    }
```
Here, `fetchSelectOptions` is an instance method in the `ModelSelect` class, a class responsible for building select elements (dropdown menus) populated with options derived from database rows, including `Brew`, `Coffee`, and `Roaster` objects. This function first determines the correct API URL and then makes an asynchronous call to the server for data to form into option elements within the select element; once the promise is resolved this work is carried out by `renderObjOptions`, an inner function within `fetchSelectOptions`. 

`renderObjOptions` will take each API-provided object represented by JSON, and do some work to make it into an option element that will ultimately be appended as a child of the select node we are trying to build out (the HTML select node is a field of the instance of `ModelSelect` that `fetchSelectOptions` is called on). This `ModelSelect` object, whose purpose is to provide a fully functional select element to add to the DOM, in turn, represents the execution context of `fetchSelectOptions`. 

Thus in the outer function `fetchSelectOptions`, `this` points to a `ModelSelect` instance, but recalling that every function gets its own execution context, this outer `this` does not carry into the inner function `renderObjOptions`. To address this, one thing we can do is explicitly pass a `thisArg` to the inner function when calling it. In the second `.then()`, calling `renderObjOptions.call(this, json)` explicitly tells the inner function to use the outer function's execution context as its own by passing a `thisArg` as the first argument. So when the work defined in `renderObjOptions` is executed, the function has the `ModelSelect` object as its context. Even though `this` is seemingly called yet another level deeper, it is done so in a callback function that happens to be an arrow function, which has the unique behavior of retaining the same context of its lexical environment, so the `this` called in the iterator callback is the same `this` that was passed to the iterator's housing function. 

The way in which arrow functions differ from function declarations brings us to another example of how I could fix the lost context bug in the same function:

```
fetchSelectOptions(roasterId) { 
		let url

		if (roasterId) url = `${BASE_URL}/roasters/${roasterId}/${this.selectHelper.type}s`
		else url = `${BASE_URL}/${this.selectHelper.type}s`

		const renderObjOptions = (objs) => {
				objs.forEach((obj) => {
						this.selectHelper.selectNode.appendChild(this.selectHelper.renderOption(obj)) 
				})
		}

		fetch(url)
		.then(resp => resp.json())
		.then(json => renderObjOptions(json)) 
		.catch(error => console.log(error.message))
}
```
As you can see, the inner function `renderObjOptions` is defined with arrow function syntax, which excludes it from the rule that all functions have their own execution context; this particular type of function uses lexical scoping, which basically means that it will use the `this` from the environment in which the arrow function expression lives. Therefore when calling `renderObjOptions(json)` in the second `.then()`, we do not have to explicitly provide a `thisArg` that points to the `ModelSelect` instance given that the lexical environment of the arrow function is a `ModelSelect` instance method.

What made it difficult for me to wrap my head around this bug is the fact that I immediately thought 'closure' when I wrote an inner function within an outer function. It looks as though `this` called in the inner function could be a reference to the function's outer scope, but ultimately `this` points to execution context and thus does not have the same behavior as variables or functions would with respect to closures.

Hopefully it goes without saying, but building this project was a challenging and rewarding process that conveyed to me, in action,  many of the seemingly abstract JavaScript concepts that were covered leading into it. 


