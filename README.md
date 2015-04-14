# JavaScript Style Guide

A mostly reasonable approach to JavaScript.

_TL;DR_ Basically, [the npm styleguide](https://docs.npmjs.com/misc/npm-coding-style).

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](http://doctoc.herokuapp.com/)*

- [Best Practices](#best-practices)
  - [Semicolons`;`](#semicolons)
  - [Style Checking](#style-checking)
  - [Linting](#linting)
    - [A few tips when using JSHint](#a-few-tips-when-using-jshint)
  - [Events](#events)
  - [Modules](#modules)
  - [jQuery](#jquery)
  - [ECMAScript 5 Compatibility [es5]](#ecmascript-5-compatibility-es5)
  - [Testing](#testing)
  - [`console` statements [console statements]](#console-statements-console-statements)
  - [Comments](#comments)
    - [Bad](#bad)
    - [Good](#good)
  - [Variable Naming](#variable-naming)
    - [Bad](#bad-1)
    - [Good](#good-1)
  - [Polyfills](#polyfills)
  - [Everyday Tricks](#everyday-tricks)
  - [Performance](#performance)
- [Syntax](#syntax)
  - [Types](#types)
  - [References](#references)
  - [Objects](#objects)
  - [Arrays](#arrays)
  - [Destructuring](#destructuring)
  - [Strings](#strings)
  - [Functions](#functions)
  - [Constructors](#constructors)
  - [Iterators and Generators](#iterators-and-generators)
  - [Properties](#properties)
  - [Variables](#variables)
  - [Hoisting](#hoisting)
  - [Comparison Operators & Equality [comparison-operators-equality]](#comparison-operators-&-equality-comparison-operators-equality)
  - [Blocks](#blocks)
  - [Comments](#comments-1)
  - [Whitespace](#whitespace)
  - [Commas](#commas)
  - [Semicolons](#semicolons)
  - [Type Casting & Coercion](#type-casting-&-coercion)
  - [Naming Conventions](#naming-conventions)
  - [Accessors](#accessors)
  - [Accessors](#accessors-1)
  - [Events](#events-1)
  - [jQuery](#jquery-1)
- [Resources](#resources)
  - [Read This [read-this]](#read-this-read-this)
  - [Performance](#performance-1)
  - [Other Styleguides [other-styleguides]](#other-styleguides-other-styleguides)
  - [Other Styles [other-styles]](#other-styles-other-styles)
  - [Further Reading [further-readingj]](#further-reading-further-readingj)
  - [Books](#books)
  - [Blogs](#blogs)
- [License](#license)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Best Practices
### Semicolons`;`

Automatic Semicolon Insertion _(ASI)_ is a [complicated feature](http://www.2ality.com/2011/05/semicolon-insertion.html). But, writing a `;` at the end of every line is painful. And the rules aren't that complex to learn. Don't bother with the semi-colons.

### Style Checking

Tools like [jscs](https://github.com/jscs-dev/node-jscs) and [eslint](https://eslint.org) can strictly enforce a code style. This makes it easy to ensure that developers can look at code they didn't write and more quickly understand what's going on. However, style-checking failures can be painful to deal with. Leave the implementation up to the team working the the project.

Generally, these work well in small projects. A sample `.jscrc` and `.eslintrc` is in this repo.

### Linting

Linting is **always a great idea**. Don't use a linter that's super opinionated about how the code should be styled, like [`jslint`](http://www.jslint.com/). Instead, use something more lenient like [`jshint`](http://www.jshint.com/) or [`eslint`](https://github.com/eslint/eslint).

#### A few tips when using JSHint

- Declare a `.jshintignore` file and include `node_modules`, `bower_components`, and the like
- You should use a `.eslintrc` file to keep your rules together. See the one in this repo.

### Events

  - When attaching data payloads to events (whether DOM events or something more proprietary like Backbone events), pass a hash instead of a raw value. This allows a subsequent contributor to add more data to the event payload without finding and updating every handler for the event. For example, instead of:

    ```js
    // bad
    $(this).trigger('listingUpdated', listing.id)

    …

    $(this).on('listingUpdated', function(e, listingId){
      // do something with listingId
    })
    ```

    prefer:

    ```js
    // good
    $(this).trigger('listingUpdated', { listingId : listing.id })

    …

    $(this).on('listingUpdated', function(e, data){
      // do something with data.listingId
    })
    ```

### Modules

  - write all modules in the CommonJs style. We'll use browserify to turn it into a UMD module where necessary.
  - The file should be named with camelCase, live in a folder with the same name, and match the name of the single export.
  - Add a method called noConflict() that sets the exported module to the previous version and returns this one.
  - Always declare `'use strict';` at the top of the module.

    ```javascript
    // fancyInput/fancyInput.js
    'use strict'

    var previousFancyInput = global.FancyInput

    function FancyInput(options){
      this.options = options || {}
    }

    FancyInput.noConflict = function noConflict(){
      global.FancyInput = previousFancyInput
      return FancyInput
    }

    module.exports = FancyInput
    ```

### jQuery

  - Prefix jQuery object variables with a `$`.

    ```javascript
    // bad
    var sidebar = $('.sidebar')

    // good
    var $sidebar = $('.sidebar')
    ```

  - Cache jQuery lookups.

    ```javascript
    // bad
    function setSidebar(){
      $('.sidebar').addClass('hidden')

      // …stuff…

      $('.sidebar').css({
        'background-color': 'pink'
      })
    }

    // good
    function setSidebar(){
      var $sidebar = $('.sidebar')
      $sidebar.addClass('hidden')

      // …stuff…

      $sidebar.css({
        'background-color': 'pink'
      })
    }
    ```

  - For DOM queries use Cascading `$('.sidebar ul')` or parent > child `$('.sidebar > ul')`. [jsPerf](http://jsperf.com/jquery-find-vs-context-sel/16)
  - Use `find` with scoped jQuery object queries.

    ```javascript
    // bad
    $('ul', '.sidebar').addClass('hidden')

    // bad
    $('.sidebar').find('ul').addClass('hidden')

    // good
    $('.sidebar ul').addClass('hidden')

    // good
    $('.sidebar > ul').addClass('hidden')

    // good
    $sidebar.find('ul')
    ```

### ECMAScript 5 Compatibility [es5]

  - Refer to [Kangax](https://twitter.com/kangax/)'s ES5 [compatibility table](http://kangax.github.com/es5-compat-table/)
  - Note that you're probably using a library that includes [es5shim](https://github.com/kriskowal/es5-shim), this means that you can probably freely use es5 methods.

### Testing

  - **Yup.**

    ```javascript
    function(){
      return true
    }
    ```

### `console` statements [console statements]

Preferably bake `console` statements into a service that can easily be disabled in production. Alternatively, don't ship any `console.log` printing statements to production distributions.

### Comments

Comments **aren't meant to explain what** the code does. Good **code is supposed to be self-explanatory**. If you're thinking of writing a comment to explain what a piece of code does, chances are you need to change the code itself. The exception to that rule is explaining what a regular expression does. Good comments are supposed to **explain why** code does something that may not seem to have a clear-cut purpose.

#### Bad

```js
// create the centered container
var p = $('<p/>')
p.center(div)
p.text('foo')
```

#### Good

```js
var $container = $('<p/>')
  , contents = 'foo'

$container.center(parent)
$container.text(contents)

megaphone.on('data', function(value){
  // the megaphone periodically emits updates for container
  container.text(value)
})
```

```js
var numeric = /\d+/ // one or more digits somewhere in the string
if (numeric.test(text)){
  console.log('so many numbers!')
}
```

Commenting out entire blocks of code _should be avoided entirely_, that's why you have version control systems in place!

### Variable Naming

Variables must have meaningful names so that you don't have to resort to commenting what a piece of functionality does. Instead, try to be expressive while succinct, and use meaningful variable names.

#### Bad

```js
function a (x, y, z) {
  return z * y / x
}
a(4, 2, 6);
// <- 3
```

#### Good

```js
function ruleOfThree (had, got, have) {
  return have * got / had
}
ruleOfThree(4, 2, 6)
// <- 3
```

### Polyfills

Where possible use the native browser implementation and include [a polyfill that provides that behavior](http://remysharp.com/2010/10/08/what-is-a-polyfill/) for unsupported browsers. This makes the code easier to work with and less involved in hackery to make things just work.

If you can't patch a piece of functionality with a polyfill, then [wrap all uses of the patching code](http://blog.ponyfoo.com/2014/08/05/building-high-quality-front-end-modules) in a globally available method that is accessible from everywhere in the application.

### Everyday Tricks

Use `||` to define a default value. If the left-hand value is [falsy][29] then the right-hand value will be used.

```js
function a(value){
  const defaultValue = 33
  const used = value || defaultValue
}
```

Use `.bind` to [partially-apply](http://ejohn.org/blog/partial-functions-in-javascript/) functions.

```js
const sum = function sum(a, b){
    return a + b
  }
  , addSeven = sum.bind(null, 7)

addSeven(6)
// <- 13
```

Use `Array.prototype.slice.call` to cast array-like objects to true arrays. (Though, it's better to use rest parameters)

```js
// tricky
const args = [].slice.call(arguments)

// better
const fn = (...args) => args
```

Use [event emitters](https://github.com/bevacqua/contra#%CE%BBemitterthing-options) on all the things!

```js
const emitter = new Backbone.Events()

$body.on('click', function onBodyClick(e){
  emitter.trigger('click', e.target)
})

emitter.on('click', function onEmitterClick(elem) {
  console.log(elem)
})

// simulate click
emitter.trigger('click', document.body)
```

Use `Function()` as a _"no-op"_.

```js
function onThingHappended(cb){
  setTimeout(cb || Function(), 2000)
}
```

### Performance

  - [On Layout & Web Performance](http://kellegous.com/j/2013/01/26/layout-performance/)
  - [String vs Array Concat](http://jsperf.com/string-vs-array-concat/2)
  - [Try/Catch Cost In a Loop](http://jsperf.com/try-catch-in-loop-cost)
  - [Bang Function](http://jsperf.com/bang-function)
  - [jQuery Find vs Context, Selector](http://jsperf.com/jquery-find-vs-context-sel/13)
  - [innerHTML vs textContent for script text](http://jsperf.com/innerhtml-vs-textcontent-for-script-text)
  - [Long String Concatenation](http://jsperf.com/ya-string-concat)
  - Loading…



## Syntax
### Types

  - **Primitives**: When you access a primitive type you work directly on its value

    + `string`
    + `number`
    + `boolean`
    + `null`
    + `undefined`

    ```javascript
    const foo = 1
    let bar = foo

    bar = 9

    console.log(foo, bar) // => 1, 9
    ```
  - **Complex**: When you access a complex type you work on a reference to its value

    + `object`
    + `array`
    + `function`

    ```javascript
    const foo = [1, 2]
    const bar = foo

    bar[0] = 9

    console.log(foo[0], bar[0]) // => 9, 9
    ```

### References

  - Use `const` for all of your references; avoid using `var`.

  > Why? This ensures that you can't reassign your references (mutation), which can lead to bugs and difficult to comprehend code.

    ```javascript
    // bad
    var a = 1;
    var b = 2;

    // good
    const a = 1;
    const b = 2;
    ```

  - If you must mutate references, use `let` instead of `var`.

  > Why? `let` is block-scoped rather than function-scoped like `var`.

    ```javascript
    // bad
    var count = 1;
    if (true) {
      count += 1;
    }

    // good, use the let.
    let count = 1;
    if (true) {
      count += 1;
    }
    ```

  - Note that both `let` and `const` are block-scoped.

    ```javascript
    // const and let only exist in the blocks they are defined in.
    {
      let a = 1;
      const b = 1;
    }
    console.log(a); // ReferenceError
    console.log(b); // ReferenceError
    ```

### Objects

  - Use the literal syntax for object creation.

    ```javascript
    // bad
    const item = new Object()

    // good
    const item = {}
    ```

  - Don't use [reserved words](http://es5.github.io/#x7.6.1) as keys. It won't work in IE8. [More info](https://github.com/airbnb/javascript/issues/61)

    ```javascript
    // bad
    const superman = {
      default: {clark: 'kent'}
      , private: true
    }

    // good
    const superman = {
      defaults: {clark: 'kent'}
      , hidden: true
    }
    ```

  - Use readable synonyms in place of reserved words.

    ```javascript
    // bad
    const superman = {
      class: 'alien'
    }

    // bad
    const superman = {
      klass: 'alien'
    }

    // good
    const superman = {
      type: 'alien'
    }
    ```

- Use computed property names when creating objects with dynamic property names.

  > Why? They allow you to define all the properties of an object in one place.

    ```javascript

    function getKey(k) {
      return `a key named ${k}`
    }

    // bad
    const obj = {
      id: 5,
      name: 'San Francisco'
    }
    obj[getKey('enabled')] = true

    // good
    const obj = {
      id: 5,
      name: 'San Francisco',
      [getKey('enabled')]: true
    }
    ```

  - Use object method shorthand.

    ```javascript
    // bad
    const atom = {
      value: 1,

      addValue: function addValue (value) {
        return atom.value + value
      }
    }

    // good
    const atom = {
      value: 1,

      addValue(value) {
        return atom.value + value
      }
    }
    ```

  - Use property value shorthand.

  > Why? It is shorter to write and descriptive.

    ```javascript
    const lukeSkywalker = 'Luke Skywalker'

    // bad
    const obj = {
      lukeSkywalker: lukeSkywalker
    }

    // good
    const obj = {
      lukeSkywalker
    }
    ```

  - Group your shorthand properties at the beginning of your object declaration.

  > Why? It's easier to tell which properties are using the shorthand.

    ```javascript
    const anakinSkywalker = 'Anakin Skywalker'
    const lukeSkywalker = 'Luke Skywalker'

    // bad
    const obj = {
      episodeOne: 1,
      twoJedisWalkIntoACantina: 2,
      lukeSkywalker,
      episodeThree: 3,
      mayTheFourth: 4,
      anakinSkywalker
    }

    // good
    const obj = {
      lukeSkywalker,
      anakinSkywalker,
      episodeOne: 1,
      twoJedisWalkIntoACantina: 2,
      episodeThree: 3,
      mayTheFourth: 4
    }
    ```

### Arrays

  - Use the literal syntax for array creation

    ```javascript
    // bad
    const items = new Array()

    // good
    const items = []
    ```

  - If you don't know array length use Array#push.

    ```javascript
    const someStack = []

    // bad
    someStack[someStack.length] = 'abracadabra'

    // good
    someStack.push('abracadabra')
    ```

  - Use array spreads `...` to shallow copy arrays.

    ```javascript
    // bad
    const len = items.length
    const itemsCopy = []

    for (let i = 0; i < len; i++) {
      itemsCopy[i] = items[i];
    }

    // good
    const itemsCopy = [...items];
    ```

  - To convert an array-like object to an array, use Array#from.

    ```javascript
    const foo = document.querySelectorAll('.foo');
    const nodes = Array.from(foo);
    ```

### Destructuring

  - Use object destructuring when accessing and using multiple properties of an object.

  > Why? Destructuring saves you from creating temporary references for those properties.

    ```javascript
    // bad
    function getFullName(user) {
      const firstName = user.firstName
      const lastName = user.lastName

      return `${firstName} ${lastName}`
    }

    // good
    function getFullName(obj) {
      const { firstName, lastName } = obj
      return `${firstName} ${lastName}`
    }

    // best
    function getFullName({ firstName, lastName }) {
      return `${firstName} ${lastName}`
    }
    ```

  - Use array destructuring.

    ```javascript
    const arr = [1, 2, 3, 4]

    // bad
    const first = arr[0]
    const second = arr[1]

    // good
    const [first, second] = arr
    ```

  - Use object destructuring for multiple return values, not array destructuring.

  > Why? You can add new properties over time or change the order of things without breaking call sites.

    ```javascript
    // bad
    function processInput(input) {
      // then a miracle occurs
      return [left, right, top, bottom]
    }

    // the caller needs to think about the order of return data
    const [left, __, top] = processInput(input)

    // good
    function processInput(input) {
      // then a miracle occurs
      return { left, right, top, bottom }
    }

    // the caller selects only the data they need
    const { left, right } = processInput(input)
    ```

### Strings

  - Use single quotes `''` for strings

    ```javascript
    // bad
    const name = "Bob Parr"

    // good
    const name = 'Bob Parr'

    // bad
    const fullName = "Bob " + this.lastName

    // good
    const fullName = `Bob ${this.lastName}`
    ```

  - Strings longer than 80 characters should use string templates.

    ```javascript
    // bad
    const errorMessage = 'This is a super long error that was thrown because of Batman. When you stop to think about how Batman had anything to do with this, you would get nowhere fast.'

    // bad
    const errorMessage = 'This is a super long error that \
    was thrown because of Batman. \
    When you stop to think about \
    how Batman had anything to do \
    with this, you would get nowhere \
    fast.'

    // bad
    const errorMessage = 'This is a super long error that ' +
      'was thrown because of Batman. ' +
      'When you stop to think about ' +
      'how Batman had anything to do ' +
      'with this, you would get nowhere ' +
      'fast.'

    // good
    const errorMessage = `This is a super long error that
      was thrown because of Batman.
      When you stop to think about
      how Batman had anything to do
      with this, you would get nowhere
      fast.`
    ```

   - When programmatically building up strings, use template strings instead of concatenation.

  > Why? Template strings give you a readable, concise syntax with proper newlines and string interpolation features.

    ```javascript
    // bad
    function sayHi(name) {
      return 'How are you, ' + name + '?'
    }

    // bad
    function sayHi(name) {
      return ['How are you, ', name, '?'].join()
    }

    // good
    function sayHi(name) {
      return `How are you, ${name}?`
    }
    ```


### Functions

- Use function declarations instead of function expressions.

  > Why? Function declarations are named, so they're easier to identify in call stacks. Function declarations that aren't assigned to a variable are hoisted and can lead to expected bugs.

    ```javascript
    // bad
    const foo = function () { }

    // bad
    function foo() { }

    // good
    const foo = function foo () { }
    ```

  - Function expressions:

    ```javascript
    // immediately-invoked function expression (IIFE)
    (() => {
      console.log('Welcome to the Internet. Please follow me.');
    })()
    ```

  - Never declare a function in a non-function block (if, while, etc). Assign the function to a variable instead. Browsers will allow you to do it, but they all interpret it differently, which is bad news bears. It's also slower if in a loop or function call because the function has to be created on each call.

    ```javascript
    // bad
    for (let i = 0, l = 10; i < l; i++){
      const square = (num) => num * num
      console.log(square(i))
    }

    // bad
    [1, 2, 3].forEach((num) => {
      const square = (num) => num * num
      console.log(square(i))
    })

    // good
    const square = (num) => num * num
    for (let i = 0, l = 10; i < l; i++){
      console.log(square(i))
    }
    ```

  - **Note:** ECMA-262 defines a `block` as a list of statements. A function declaration is not a statement. [Read ECMA-262's note on this issue](http://www.ecma-international.org/publications/files/ECMA-ST/Ecma-262.pdf#page=97).

    ```javascript
    // bad
    if (currentUser) {
      function test() {
        console.log('Nope.')
      }
    }

    // good
    let test;
    if (currentUser) {
      test = () => {
        console.log('Yup.')
      }
    }
    ```

  - Never name a parameter `arguments`, this will take precedence over the `arguments` object that is given to every function scope. It's also more efficient to use rest parameters.

  > Why? `...` is explicit about which arguments you want pulled. Plus rest arguments are a real Array and not Array-like like `arguments`.

    ```javascript
    // bad
    const nope = function nope(name, options, arguments) {
      // ...stuff...
    }

    // bad
    const concatenateAll = function concatenateAll() {
      const args = Array.prototype.slice.call(arguments);
      return args.join('')
    }

    // good
    const yup = function yup(name, options, ...args) {
      return args.join('')
    }
    ```

  - Use default parameter syntax rather than mutating function arguments.

    ```javascript
    // really bad
    const handleThings = function handleThings(opts) {
      // No! We shouldn't mutate function arguments.
      // Double bad: if opts is falsey it'll be set to an object which may
      // be what you want but it can introduce subtle bugs.
      opts = opts || {}
      // ...
    }

    // still bad
    const handleThings = function handleThings(opts) {
      if (opts === void 0) {
        opts = {}
      }
      // ...
    }

    // good
    const handleThings = function handleThings(opts = {}) {
      // ...
    }
    ```

  - Never put spaces immediately after an opening parens or immediately before a closing parens.

      ```javascript
      // bad
      var bad = function ( badness, devils, misery ) {

      }

      // good
      var good = function good (happiness, heaven, cheesecake){

      }
      ```

  - Avoid creating functions that accept more than 3 arguments. You should probably pass a single options argument instead.

      ```javascript
      // bad
      var manyThings = function manyThings (first, second, moar, evenMoar){

      }

      // good
      var manyThings = function manyThings (options){
        /*
        options.first
        options.second
        etc…
        */
      }
      ```

  - When you must use function expressions (as when passing an anonymous function), use arrow function notation.

  > Why? It creates a version of the function that executes in the context of `this`, which is usually what you want, and is a more concise syntax.

  > Why not? If you have a fairly complicated function, you might move that logic out into its own function declaration.

    ```javascript
    // bad
    [1, 2, 3].map(function square (x) {
      return x * x
    })

    // good
    [1, 2, 3].map((x) => x * x)

    // good
    const square = function square (x) {
      return x * x
    }
    [1, 2, 3].map(square)
    ```

  - If the function body fits on one line, feel free to omit the braces and use implicit return. Otherwise, add the braces and use a `return` statement.

  > Why? Syntactic sugar. It reads well when multiple functions are chained together.

  > Why not? If you plan on returning an object.

    ```javascript
    // good
    [1, 2, 3].map((x) => x * x)

    // good
    [1, 2, 3].map((x) => {
      return { number: x }
    })
    ```

  - Always use parentheses around the arguments. Omitting the parentheses makes the functions less readable and only works for single arguments.

  > Why? These declarations read better with parentheses. They are also required when you have multiple parameters so this enforces consistency.

    ```javascript
    // bad
    [1, 2, 3].map(x => x * x)

    // good
    [1, 2, 3].map((x) => x * x)
    ```


### Constructors
  - Always use `class`. Avoid manipulating `prototype` directly.

  > Why? `class` syntax is more concise and easier to reason about.

    ```javascript
    // bad
    function Queue(contents = []) {
      this._queue = [...contents]
    }
    Queue.prototype.pop = function() {
      const value = this._queue[0]
      this._queue.splice(0, 1)
      return value
    }


    // good
    class Queue {
      constructor(contents = []) {
        this._queue = [...contents]
      }
      , pop() {
        const value = this._queue[0]
        this._queue.splice(0, 1)
        return value
      }
    }
    ```

  - Use `extends` for inheritance.

  > Why? It is a built-in way to inherit prototype functionality without breaking `instanceof`. Though, you probably want to use composition instead on inheritance.

    ```javascript
    // bad
    const inherits = require('inherits')
    function PeekableQueue(contents) {
      Queue.apply(this, contents)
    }
    inherits(PeekableQueue, Queue)
    PeekableQueue.prototype.peek = function() {
      return this._queue[0]
    }

    // good
    class PeekableQueue extends Queue {
      peek() {
        return this._queue[0]
      }
    }
    ```

  - Methods can return `this` to help with method chaining.

    ```javascript
    // bad
    Jedi.prototype.jump = function() {
      this.jumping = true
      return true
    };

    Jedi.prototype.setHeight = function(height) {
      this.height = height
    }

    const luke = new Jedi()
    luke.jump() // => true
    luke.setHeight(20) // => undefined

    // good
    class Jedi {
      jump() {
        this.jumping = true
        return this
      }

      , setHeight(height) {
        this.height = height
        return this
      }
    }

    const luke = new Jedi()

    luke.jump()
      .setHeight(20)
    ```


  - It's okay to write a custom toString() method, just make sure it works successfully and causes no side effects.

    ```javascript
    class Jedi {
      contructor(options = {name: 'no name'}) {
        this.name = options.name
      }

      , getName() {
        return this.name
      }

      , toString() {
        return `Jedi - ${this.getName()}`
      }
    }
    ```


### Iterators and Generators

  - Don't use iterators. Prefer JavaScript's higher-order functions like `map()` and `reduce()` instead of loops like `for-of`.

  > Why? This enforces our immutable rule. Dealing with pure functions that return values is easier to reason about than side-effects.

    ```javascript
    const numbers = [1, 2, 3, 4, 5]

    // bad
    let sum = 0
    for (let num of numbers) {
      sum += num
    }

    sum === 15

    // good
    let sum = 0
    numbers.forEach((num) => sum += num)
    sum === 15

    // best (use the functional force)
    const sum = numbers.reduce((total, num) => total + num, 0)
    sum === 15
    ```

  - Don't use generators.

  > Why? They don't transpile well to ES5.

### Properties

  - Use dot notation when accessing properties.

    ```javascript
    var luke = {
      jedi: true
      , age: 28
    }

    // bad
    var isJedi = luke['jedi']

    // good
    var isJedi = luke.jedi
    ```

  - Use subscript notation `[]` when accessing properties with a variable.

    ```javascript
    var luke = {
      jedi: true
      , age: 28
    }

    function getProp(prop){
      return luke[prop]
    }

    var isJedi = getProp('jedi')
    ```



### Variables

- Always use `const` to declare variables. Not doing so will result in global variables. We want to avoid polluting the global namespace. Captain Planet warned us of that.

    ```javascript
    // bad
    superPower = new SuperPower()

    // good
    const superPower = new SuperPower()
    ```

  - Use one `const` declaration per variable.

    > Why? It's easier to add new variable declarations this way, and you never have to worry about a `,` or introducing punctuation-only diffs.

    ```javascript
    // bad
    const items = getItems(),
        goSportsTeam = true,
        dragonball = 'z'

    // bad
    // (compare to above, and try to spot the mistake)
    const items = getItems(),
        goSportsTeam = true
        dragonball = 'z'

    // good
    const items = getItems()
    const goSportsTeam = true
    const dragonball = 'z'
    ```

  - Group all your `const`s and then group all your `let`s.

  > Why? This is helpful when later on you might need to assign a variable depending on one of the previous assigned variables.

    ```javascript
    // bad
    let i, len, dragonball,
        items = getItems(),
        goSportsTeam = true

    // bad
    let i
    const items = getItems()
    let dragonball
    const goSportsTeam = true
    let len

    // good
    const goSportsTeam = true
    const items = getItems()
    let dragonball
    let i
    let length
    ```

  - Assign variables where you need them, but place them in a reasonable place.

  > Why? `let` and `const` are block scoped and not function scoped.

    ```javascript
    // good
    const up = function up () {
      test()
      console.log('doing stuff..')

      //..other stuff..

      const name = getName();

      if (name === 'test') {
        return false
      }

      return name
    }

    // bad
    const up = function up () {
      const name = getName()

      if (!arguments.length) {
        return false
      }

      return true
    }

    // good
    const up = function up () {
      if (!arguments.length) {
        return false
      }

      const name = getName()

      return true
    }
    ```



### Hoisting

  - `var` declarations get hoisted to the top of their scope, their assignment does not. `const` and `let` declarations are blessed with a new concept called [Temporal Dead Zones (TDZ)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/let#Temporal_dead_zone_and_errors_with_let). It's important to know why [typeof is no longer safe](http://es-discourse.com/t/why-typeof-is-no-longer-safe/15).

    ```javascript
    // we know this wouldn't work (assuming there
    // is no notDefined global variable)
    function example() {
      console.log(notDefined) // => throws a ReferenceError
    }

    // creating a variable declaration after you
    // reference the variable will work due to
    // variable hoisting. Note: the assignment
    // value of `true` is not hoisted.
    function example() {
      console.log(declaredButNotAssigned) // => undefined
      var declaredButNotAssigned = true
    }

    // The interpreter is hoisting the variable
    // declaration to the top of the scope,
    // which means our example could be rewritten as:
    function example() {
      let declaredButNotAssigned
      console.log(declaredButNotAssigned) // => undefined
      declaredButNotAssigned = true
    }

    // using const and let
    function example() {
      console.log(declaredButNotAssigned) // => throws a ReferenceError
      console.log(typeof declaredButNotAssigned) // => throws a ReferenceError
      const declaredButNotAssigned = true
    }
    ```

  - Anonymous function expressions hoist their variable name, but not the function assignment.

    ```javascript
    function example(){
      console.log(anonymous) // => undefined

      anonymous() // => TypeError anonymous is not a function

      var anonymous = function(){
        console.log('anonymous function expression')
      }
    }
    ```

  - Named function expressions hoist the variable name, not the function name or the function body.

    ```javascript
    function example(){
      console.log(named) // => undefined

      named() // => TypeError named is not a function

      superPower() // => ReferenceError superPower is not defined

      var named = function superPower(){
        console.log('Flying')
      }
    }

    // the same is true when the function name
    // is the same as the variable name.
    function example(){
      console.log(named) // => undefined

      named() // => TypeError named is not a function

      var named = function named(){
        console.log('named')
      }
    }
    ```

  - Function declarations hoist their name and the function body.

    ```javascript
    function example(){
      superPower() // => Flying

      function superPower(){
        console.log('Flying')
      }
    }
    ```

  - For more information refer to [JavaScript Scoping & Hoisting](http://www.adequatelygood.com/2010/2/JavaScript-Scoping-and-Hoisting) by [Ben Cherry](http://www.adequatelygood.com/)




### Comparison Operators & Equality [comparison-operators-equality]

  - Use `===` and `!==` over `==` and `!=`.
  - Conditional expressions are evaluated using coercion with the `ToBoolean` method and always follow these simple rules:

    + **Objects** evaluate to **true**
    + **Undefined** evaluates to **false**
    + **Null** evaluates to **false**
    + **Booleans** evaluate to **the value of the boolean**
    + **Numbers** evaluate to **false** if **+0, -0, or NaN**, otherwise **true**
    + **Strings** evaluate to **false** if an empty string `''`, otherwise **true**

    ```javascript
    if ([0]){
      // true
      // An array is an object, objects evaluate to true
    }
    ```

  - Use shortcuts.

    ```javascript
    // bad
    if (name !== ''){
      // …stuff…
    }

    // good
    if (name){
      // …stuff…
    }

    // bad
    if (collection.length > 0){
      // …stuff…
    }

    // good
    if (collection.length){
      // …stuff…
    }
    ```

  - For more information see [Truth Equality and JavaScript](http://javascriptweblog.wordpress.com/2011/02/07/truth-equality-and-javascript/#more-2108) by Angus Croll



### Blocks

  - Single line blocks don't require braces, but should be easy to read. If your single line extends more than 80 characters, break into two lines, and indent the contents of the block.

    ```javascript
    // bad
    if (test){
      return false
    }

    // bad
    if (test)
      return false

    // good
    if (test) return false


    // bad
    if (test && test.arg === doSomethingWith(5) && puppies() === 'cute') ciderHouse = true

    // good
    if (test && test.arg === doSomethingWith(5) && puppies() === 'cute') {
      ciderHouse = true
    }

    // bad
    function(){ return false }

    // good
    const fn = function fn (){
      return false
    }
    ```



### Comments

  - Use `/** … */` for multiline comments. Include a description, specify types and values for all parameters and return values.

    ```javascript
    // bad
    // make() returns a new element
    // based on the passed in tag name
    //
    // @param <String> tag
    // @return <Element> element
    function make(tag){

      // …stuff…

      return element
    }

    // good
    /**
     * make() returns a new element
     * based on the passed in tag name
     *
     * @param <String> tag
     * @return <Element> element
     */
    function make(tag){

      // …stuff…

      return element
    }
    ```

  - Use `//` for single line comments. Place single line comments on a newline above the subject of the comment. Put an empty line before the comment.

    ```javascript
    // bad
    var active = true;  // is current tab

    // good
    // is current tab
    var active = true

    // bad
    function getType(){
      console.log('fetching type…')
      // set the default type to 'no type'
      var type = this._type || 'no type'

      return type
    }

    // good
    function getType(){
      console.log('fetching type…')

      // set the default type to 'no type'
      var type = this._type || 'no type'

      return type
    }
    ```

  - Prefixing your comments with `FIXME` or `TODO` helps other developers quickly understand if you're pointing out a problem that needs to be revisited, or if you're suggesting a solution to the problem that needs to be implemented. These are different than regular comments because they are actionable. The actions are `FIXME -- need to figure this out` or `TODO -- need to implement`.

  - Use `// FIXME:` to annotate problems

    ```javascript
    function Calculator(){

      // FIXME: shouldn't use a global here
      total = 0

      return this
    }
    ```

  - Use `// TODO:` to annotate solutions to problems

    ```javascript
    function Calculator(){

      // TODO: total should be configurable by an options param
      this.total = 0

      return this
    }
  ```



### Whitespace

  - Use soft tabs set to 2 spaces

    ```javascript
    // bad
    function(){
    ∙∙∙∙const name
    }

    // bad
    function(){
    ∙const name
    }

    // good
    function(){
    ∙∙const name
    }
    ```

  - Place no spaces before the leading brace.

    ```javascript
    // bad
    function test () {
      console.log('test')
    }

    // good
    function test (){
      console.log('test')
    }

    // bad
    dog.set('attr',{
      age: '1 year',
      breed: 'Bernese Mountain Dog'
    })

    // good
    dog.set('attr', {
      age: '1 year',
      breed: 'Bernese Mountain Dog'
    })
    ```

  - Set off operators with spaces.

    ```javascript
    // bad
    const x=y+5

    // good
    const x = y + 5
    ```

  - Place an empty newline at the end of the file.

    ```javascript
    // bad
    (function(global){
      // …stuff…
    })(this)
    ```

    ```javascript
    // good
    (function(global){
      // …stuff…
    })(this)

    ```

  - Use indentation when making long method chains.

    ```javascript
    // bad
    $('#items').find('.selected').highlight().end().find('.open').updateCount()

    // good
    $('#items')
      .find('.selected')
        .highlight()
        .end()
      .find('.open')
        .updateCount()

    // bad
    const leds = stage.selectAll('.led').data(data).enter().append('svg:svg').class('led', true)
        .attr('width',  (radius + margin) * 2).append('svg:g')
        .attr('transform', 'translate(' + (radius + margin) + ',' + (radius + margin) + ')')
        .call(tron.led)

    // good
    const leds = stage.selectAll('.led')
        .data(data)
      .enter().append('svg:svg')
        .class('led', true)
        .attr('width',  (radius + margin) * 2)
      .append('svg:g')
        .attr('transform', 'translate(' + (radius + margin) + ',' + (radius + margin) + ')')
        .call(tron.led)
    ```

- Leave a blank line after blocks and before the next statement

    ```javascript
    // bad
    if (foo) {
      return bar
    }
    return baz

    // good
    if (foo) {
      return bar
    }

    return baz

    // bad
    const obj = {
      foo: function() {
      }
      , bar: function() {
      }
    }
    return obj

    // good
    const obj = {
      foo: function() {
      }

      , bar: function() {
      }
    }

    return obj
    ```

### Commas

  - Leading commas: **Hell yes.**
  - [Read why comma first is better](https://gist.github.com/isaacs/357981/)

    ```javascript
    // bad
    const once,
        upon,
        aTime

    // good
    const once
      , upon
      , aTime

    // bad
    const hero = {
      firstName: 'Bob',
      lastName: 'Parr',
      heroName: 'Mr. Incredible',
      superPower: 'strength'
    }

    // good
    const hero = {
        firstName: 'Bob'
      , lastName: 'Parr'
      , heroName: 'Mr. Incredible'
      , superPower: 'strength'
    }
    ```

  - Additional trailing comma: **Nope.** This can cause problems with IE6/7 and IE9 if it's in quirksmode. Also, in some implementations of ES3 would add length to an array if it had an additional trailing comma. This was clarified in ES5 ([source](http://es5.github.io/#D)):

  > Edition 5 clarifies the fact that a trailing comma at the end of an ArrayInitialiser does not add to the length of the array. This is not a semantic change from Edition 3 but some implementations may have previously misinterpreted this.

    ```javascript
    // bad
    const hero = {
      firstName: 'Kevin'
      , lastName: 'Flynn'
      ,
    }

    const heroes = [
      'Batman'
      , 'Superman'
      ,
    ]

    // good
    const hero = {
      firstName: 'Kevin'
      , lastName: 'Flynn'
    }

    const heroes = [
      'Batman'
      , 'Superman'
    ]
    ```



### Semicolons

  - **NO.**
  - ASI means that you almost never need semicolons. Leave them out and live free! JsHint will catch nearly any error you might make.

    ```javascript
    // bad
    (function(){
      const name = 'Skywalker';
      return name;
    })();

    // good
    (function(){
      const name = 'Skywalker'
      return name
    })()

    // good
    const a

    ;(function(){
      const name = 'Skywalker'
      return name
    })()
    ```

### Type Casting & Coercion

  - Perform type coercion at the beginning of the statement.
  - Strings:

    ```javascript
    //  => this.reviewScore = 9

    // bad
    const totalScore = this.reviewScore + ''

    // good
    const totalScore = '' + this.reviewScore

    // bad
    const totalScore = '' + this.reviewScore + ' total score'

    // good
    const totalScore = this.reviewScore + ' total score'
    ```

  - Use `parseInt` for Numbers and always with a radix for type casting.

    ```javascript
    const inputValue = '4'

    // bad
    const val = new Number(inputValue)

    // bad
    const val = +inputValue

    // bad
    const val = inputValue >> 0

    // bad
    const val = parseInt(inputValue)

    // good
    const val = Number(inputValue)

    // better
    const val = parseInt(inputValue, 10)
    ```

  - If for whatever reason you are doing something wild and `parseInt` is your bottleneck and need to use Bitshift for [performance reasons](http://jsperf.com/coercion-vs-casting/3), leave a comment explaining why and what you're doing.
  - **Note:** Be careful when using bitshift operations. Numbers are represented as [64-bit values](http://es5.github.io/#x4.3.19), but Bitshift operations always return a 32-bit integer ([source](http://es5.github.io/#x11.7)). Bitshift can lead to unexpected behavior for integer values larger than 32 bits. [Discussion](https://github.com/airbnb/javascript/issues/109)

    ```javascript
    // good
    /**
     * parseInt was the reason my code was slow.
     * Bitshifting the String to coerce it to a
     * Number made it a lot faster.
     */
    var val = inputValue >> 0
    ```

  - Booleans:

    ```javascript
    var age = 0

    // bad
    var hasAge = new Boolean(age)

    // good
    var hasAge = Boolean(age)

    // better
    var hasAge = !!age
    ```



### Naming Conventions

  - Avoid single letter names. Be descriptive with your naming.

    ```javascript
    // bad
    function q(){
      // …stuff…
    }

    // good
    function query(){
      // …stuff…
    }
    ```

  - Use camelCase when naming objects, functions, and instances

    ```javascript
    // bad
    const OBJEcttsssss = {}
    const this_is_my_object = {}
    function c(){}
    const u = new user({
      name: 'Bob Parr'
    })

    // good
    const thisIsMyObject = {}
    function thisIsMyFunction(){}
    const user = new User({
      name: 'Bob Parr'
    })
    ```

  - Use PascalCase when naming constructors or classes

    ```javascript
    // bad
    function user(options){
      this.name = options.name
    }

    const bad = new user({
      name: 'nope'
    })

    // good
    function User(options){
      this.name = options.name
    }

    const good = new User({
      name: 'yup'
    })
    ```

  - Use a leading underscore `_` when naming private properties

    ```javascript
    // bad
    this.__firstName__ = 'Panda'
    this.firstName_ = 'Panda'

    // good
    this._firstName = 'Panda'
    ```

  - When saving a reference to `this` use `self`. Avoid using self unless absolutely necessary. Pefer `_.bind()`

    ```javascript
    // bad
    function(){
      const that = this
      return function(){
        console.log(that)
      }
    }

    // bad
    function(){
      const _this = this
      return function(){
        console.log(_this)
      }
    }

    // bad
    function(){
      const self = this
      self.thing = true
    }

    // good
    function(){
      const self = this
      this.thing = true
      return function(){
        console.log(self)
      }
    }
    ```

  - Name your functions. This is helpful for stack traces.

    ```javascript
    // bad
    const log = function(msg){
      console.log(msg)
    }

    // good
    const log = function log (msg){
      console.log(msg)
    }
    ```



### Accessors

  - Accessor functions for properties are not required
  - If you do make accessor functions use getVal() and setVal('hello')

    ```javascript
    // bad
    dragon.age()

    // good
    dragon.getAge()

    // bad
    dragon.age(25)

    // good
    dragon.setAge(25)
    ```

  - If the property is a boolean, use isVal() or hasVal()

    ```javascript
    // bad
    if (!dragon.age()){
      return false
    }

    // good
    if (!dragon.hasAge()){
      return false
    }
    ```

  - It's okay to create get() and set() functions, but be consistent.

    ```javascript
    function Jedi(options){
      options || (options = {})
      var lightsaber = options.lightsaber || 'blue'
      this.set('lightsaber', lightsaber)
    }

    Jedi.prototype.set = function(key, val){
      this[key] = val
    }

    Jedi.prototype.get = function(key){
      return this[key]
    }
    ```

### Accessors

  - Accessor functions for properties are not required.
  - If you do make accessor functions use getVal() and setVal('hello').

    ```javascript
    // bad
    dragon.age()

    // good
    dragon.getAge()

    // bad
    dragon.age(25)

    // good
    dragon.setAge(25)
    ```

  - If the property is a boolean, use isVal() or hasVal().

    ```javascript
    // bad
    if (!dragon.age()) {
      return false
    }

    // good
    if (!dragon.hasAge()) {
      return false
    }
    ```

  - It's okay to create get() and set() functions, but be consistent.

    ```javascript
    class Jedi {
      constructor(options = {}) {
        const lightsaber = options.lightsaber || 'blue'
        this.set('lightsaber', lightsaber)
      }

      set(key, val) {
        this[key] = val
      }

      get(key) {
        return this[key]
      }
    }
    ```

### Events

  - When attaching data payloads to events (whether DOM events or something more proprietary like Backbone events), pass a hash instead of a raw value. This allows a subsequent contributor to add more data to the event payload without finding and updating every handler for the event. For example, instead of:

    ```javascript
    // bad
    $(this).trigger('listingUpdated', listing.id)

    ...

    $(this).on('listingUpdated', function(e, listingId) {
      // do something with listingId
    })
    ```

    prefer:

    ```javascript
    // good
    $(this).trigger('listingUpdated', { listingId : listing.id })

    ...

    $(this).on('listingUpdated', function(e, data) {
      // do something with data.listingId
    })
    ```


### jQuery

  - Prefix jQuery object variables with a `$`.

    ```javascript
    // bad
    const sidebar = $('.sidebar')

    // good
    const $sidebar = $('.sidebar')
    ```

  - Cache jQuery lookups.

    ```javascript
    // bad
    function setSidebar() {
      $('.sidebar').addClass('hidden')

      // ...stuff...

      $('.sidebar').css({
        'background-color': 'pink'
      })
    }

    // good
    function setSidebar() {
      const $sidebar = $('.sidebar')
      $sidebar.addClass('hidden')

      // ...stuff...

      $sidebar.css({
        'background-color': 'pink'
      })
    }
    ```

  - For DOM queries use Cascading `$('.sidebar ul')` or parent > child `$('.sidebar > ul')`. [jsPerf](http://jsperf.com/jquery-find-vs-context-sel/16)
  - Use `find` with scoped jQuery object queries.

    ```javascript
    // bad
    $('ul', '.sidebar').addClass('hidden')

    // bad
    $('.sidebar').find('ul').addClass('hidden')

    // good
    $('.sidebar ul').addClass('hidden')

    // good
    $('.sidebar > ul').addClass('hidden')

    // good
    $sidebar.find('ul').addClass('hidden')
    ```


## Resources


### Read This [read-this]

  - [Annotated ECMAScript 5.1](http://es5.github.com/)

### Performance

  - [On Layout & Web Performance](http://kellegous.com/j/2013/01/26/layout-performance/)
  - [String vs Array Concat](http://jsperf.com/string-vs-array-concat/2)
  - [Try/Catch Cost In a Loop](http://jsperf.com/try-catch-in-loop-cost)
  - [Bang Function](http://jsperf.com/bang-function)
  - [jQuery Find vs Context, Selector](http://jsperf.com/jquery-find-vs-context-sel/13)
  - [innerHTML vs textContent for script text](http://jsperf.com/innerhtml-vs-textcontent-for-script-text)
  - [Long String Concatenation](http://jsperf.com/ya-string-concat)

### Other Styleguides [other-styleguides]

  - [Google JavaScript Style Guide](http://google-styleguide.googlecode.com/svn/trunk/javascriptguide.xml)
  - [jQuery Core Style Guidelines](http://docs.jquery.com/JQuery_Core_Style_Guidelines)
  - [Principles of Writing Consistent, Idiomatic JavaScript](https://github.com/rwldrn/idiomatic.js/)
  - This style guide is based on [AirBnB's style guide](https://github.com/airbnb/javascript)

### Other Styles [other-styles]

  - [Naming this in nested functions](https://gist.github.com/4135065) - Christian Johansen
  - [Conditional Callbacks](https://github.com/airbnb/javascript/issues/52)
  - [Popular JavaScript Coding Conventions on Github](http://sideeffect.kr/popularconvention/#javascript)

### Further Reading [further-readingj]

  - [Understanding JavaScript Closures](http://javascriptweblog.wordpress.com/2010/10/25/understanding-javascript-closures/) - Angus Croll
  - [Basic JavaScript for the impatient programmer](http://www.2ality.com/2013/06/basic-javascript.html) - Dr. Axel Rauschmayer

### Books

  - [JavaScript: The Good Parts](http://www.amazon.com/JavaScript-Good-Parts-Douglas-Crockford/dp/0596517742) - Douglas Crockford
  - [JavaScript Patterns](http://www.amazon.com/JavaScript-Patterns-Stoyan-Stefanov/dp/0596806752) - Stoyan Stefanov
  - [Pro JavaScript Design Patterns](http://www.amazon.com/JavaScript-Design-Patterns-Recipes-Problem-Solution/dp/159059908X)  - Ross Harmes and Dustin Diaz
  - [High Performance Web Sites: Essential Knowledge for Front-End Engineers](http://www.amazon.com/High-Performance-Web-Sites-Essential/dp/0596529309) - Steve Souders
  - [Maintainable JavaScript](http://www.amazon.com/Maintainable-JavaScript-Nicholas-C-Zakas/dp/1449327680) - Nicholas C. Zakas
  - [JavaScript Web Applications](http://www.amazon.com/JavaScript-Web-Applications-Alex-MacCaw/dp/144930351X) - Alex MacCaw
  - [Pro JavaScript Techniques](http://www.amazon.com/Pro-JavaScript-Techniques-John-Resig/dp/1590597273) - John Resig
  - [Smashing Node.js: JavaScript Everywhere](http://www.amazon.com/Smashing-Node-js-JavaScript-Everywhere-Magazine/dp/1119962595) - Guillermo Rauch
  - [Secrets of the JavaScript Ninja](http://www.amazon.com/Secrets-JavaScript-Ninja-John-Resig/dp/193398869X) - John Resig and Bear Bibeault
  - [Human JavaScript](http://humanjavascript.com/) - Henrik Joreteg
  - [Superhero.js](http://superherojs.com/) - Kim Joar Bekkelund, Mads Mobæk, & Olav Bjorkoy
  - [JSBooks](http://jsbooks.revolunet.com/)

### Blogs

  - [DailyJS](http://dailyjs.com/)
  - [JavaScript Weekly](http://javascriptweekly.com/)
  - [JavaScript, JavaScript…](http://javascriptweblog.wordpress.com/)
  - [Bocoup Weblog](http://weblog.bocoup.com/)
  - [Adequately Good](http://www.adequatelygood.com/)
  - [NCZOnline](http://www.nczonline.net/)
  - [Perfection Kills](http://perfectionkills.com/)
  - [Ben Alman](http://benalman.com/)
  - [Dmitry Baranovskiy](http://dmitry.baranovskiy.com/)
  - [Dustin Diaz](http://dustindiaz.com/)
  - [nettuts](http://net.tutsplus.com/?s=javascript)


## License

Based on the style guides from [Airbnb](https://github.com/airbnb/javascript) and [bevacqua/js](https://github.com/bevacqua/js)

(The MIT License)

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
'Software'), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY
CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT,
TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE
SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

**[[⬆]](#TOC)**
