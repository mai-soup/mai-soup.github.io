---
layout: post
title:  "Array methods"
excerpt: "When the map() isn't clear enough and you need a guide."
---

> A new coder with spirit, not shy,
>
> Finds array methods to untie.
>
> With functions unnamed,
>
> It's a game unrestrained,
>
> But they'll master it by and by.
>
> —ChatGPT

Ah, array methods, beloved. My archnemesis until just a couple months ago. There's a few concepts to understand before it really "clicks", so here we go, one at a time.

## Callback functions

If we look at the MDN docs for any of the array methods (I'll use `Array.prototype.map()` as an example), we notice a few terms explaining the parameters for it:

![Excerpt from MDN talking about the parameters for Array.map()"](/images/array-mapping/mdn-callback.png){:class="img-fluid"}

`callbackFn` - callback function. In Higher Order Functions (functions which can take other functions as a parameter), the parameters that are functions are called callback functions. Imagine the HOF "calling it back", being like hey, I've done my job, now it's up to you. The HOF can invoke any of the callback functions provided to it.

The docs state that the callback function passed to `Array.map()` is executed for each element in the array and the returned value is added to the new array, which will be returned by `Array.map()` itself.

Let's try writing our own mapping function to see what this really looks like in practice. I'll add it as a property on the `Array.prototype` - doesn't really matter what a prototype is right now, in short, I am making sure I can call it like `arr.map()`, same as the original map function. The `this` keyword here refers to the array we call the method on.

For simplicity's sake, right now I will only care about the first argument that `callbackFn` accepts, that is, `element` - the current element we are looking at.

```js
Array.prototype.newMap = function(callbackFn) {
    const newArray = [];
    for (let i = 0; i < this.length; i++) {
        const newElement = callbackFn(this[i]);
        newArray.push(newElement);
    }
    return newArray;
}
```

Right, let's try using this new method on an array - don't worry about the syntax of anything inside the `map` or `newMap` calls just yet, I just want to show that they both do the same thing.


!["A devtools console showing that the two methods produce the same output, mapping the array's elements to their squares."](/images/array-mapping/map-and-newmap.png){:class="img-fluid"}

So far so good. I was trying to square each element of the starting array, and both methods returned the expected result.

## Okay, but what does `callbackFn` look like really?

Just like any other function. You just have to remember the parameters it takes - `element`, `index`, and.... uh whatever the third one was. `array`. Right. Never had to use that one, er.... I'll take a bit of a leap and assume you can not worry about that one either. `element` is the current element the mapping function is working on, and `index` is its index. So we could define our squaring function as something like

```js
function squareElement(element, index) {
    return element * element;
}
```

Or, since we are not actually using the `index` parameter:

```js
function squareElement(element) {
    return element * element;
}
```

The `index` parameter, if also passed to `squareElement`, will just be ignored in the latter case. We can't, however, just pass one parameter and expect it to be treated as `index` not `element` - order matters!

Cool, so, since we have a function, we can check that the map method works with it:

!["A devtools console showing that the map method works with the function we just defined."](/images/array-mapping/map-with-callback.png){:class="img-fluid"}

Notice how I didn't put parentheses when I passed `squareElement` as a parameter to `map`? That's because I want to pass the entire function, not just the value it would return. `squareElement()` would call it and give me its return value. `squareElement` passes the function itself. Neat, right?

## Arrow functions

Small reminder - any function can be written as an arrow function. Let's make a `squareElementArrow` function which does the exact same but in the new syntax:

```js
const squareElementArrow = (element) => {
    return element * element;
}
```

We could make it a bit more concise - if an arrow function has a single argument, it doesn't need parentheses around it, and if the only thing in the body is the return statement, it doesn't need curly braces nor the `return` keyword. So, the more concise version:

```js
const squareElementArrow = element => element * element;
```

Starting to look like you've seen the syntax before? Let's test it out, make sure it again works the same:

!["A devtools console showing that the map method works the same with the arrow syntax."](/images/array-mapping/map-callback-arrow.png){:class="img-fluid"}

Perfect. Onto the last piece of the puzzle...

## Anonymous functions

Okay, this one will be what ties it all together into the syntax you're used to seeing in katas. Anonymous functions are functions that are defined without a name - they'll only need to be used in one place, so there's no point in assigning them one. Sounding like our squaring functions....?

Let's see how we could create an anonymous function from our `squareElementArrow` function:

```js
element => element * element
```

That simple. Just don't give it a name, don't assign it to anything. However, "element" is not very descriptive as a parameter name - if we are squaring something, we are expecting it to be a number. So, new revision:

```js
num => num * num
```

We could call it anything, `wkdjfkwdjf` and it would work the exact same, as, since it is passed to the `map()` function, it'll know that the first parameter the callback function passed to it should take is the current element. So, in the end,

!["A devtools console showing that the map method works the same with the anonymous arrow function."](/images/array-mapping/map-anon.png){:class="img-fluid"}

And, just to stress that we don't _have_ to use the `index` element in the body of our callback function and it works just the same, as well as that the parameter names don't matter but the order does:

!["A devtools console showing that the map method works the same with the anonymous arrow function but different parameter names."](/images/array-mapping/map-anon-proof.png){:class="img-fluid"}

And that's it! Hope that cleared some things up, let me know if you have any questions.