# Rapid7 JavaScript Style Guide() {

*A mostly reasonable approach to JavaScript*

The goal of this style guide is not to proclaim that these are the best ways, nor the only ways, to accomplish well-structured code. Rather, the goal is to ensure the code follows a consistent and predictable structure, so that all developers within the ecosystem can easily jump into the codebase and hit the ground running. This makes everything simpler, from application maintenance to submitting PRs for shared packages on our private NPM structure.

Like all code, this style guide is intended to be community-driven, and evolve as the times do. If you feel something is missing or needs tweaking, all PRs are welcome.

Other Style Guides
 - [ES5](es5/)
 - [React](react/)

IDE configuration
 - [.editorconfig](ide/.editorconfig)
 
Building applications
 - [Getting started](cheatsheets/getting-started.md)

Coding convention cheatsheets
 - [Babel](cheatsheets/babel.md)
 - [Component](cheatsheets/component.md)
 - [NPM Package](cheatsheets/npm-package.md)
 - [Redux](cheatsheets/redux.md)
 - [Services](cheatsheets/services.md)

ESLint rules (applies this guide as linting rules):
 - [eslint-config-rapid7](https://github.com/rapid7/eslint-config-rapid7)

## Table of Contents

  1. [Types](#types)
  1. [References](#references)
  1. [Objects](#objects)
  1. [Arrays](#arrays)
  1. [Destructuring](#destructuring)
  1. [Strings](#strings)
  1. [Functions](#functions)
  1. [Constructors](#constructors)
  1. [Modules](#modules)
  1. [Iterators and Generators](#iterators-and-generators)
  1. [Properties](#properties)
  1. [Variables](#variables)
  1. [Hoisting](#hoisting)
  1. [Comparison Operators & Equality](#comparison-operators--equality)
  1. [Blocks](#blocks)
  1. [Comments](#comments)
  1. [Whitespace](#whitespace)
  1. [Commas](#commas)
  1. [Semicolons](#semicolons)
  1. [Type Casting & Coercion](#type-casting--coercion)
  1. [Naming Conventions](#naming-conventions)
  1. [Accessors](#accessors)
  1. [Events](#events)
  1. [jQuery](#jquery)
  1. [Testing](#testing)
  1. [Contributors](#contributors)
  1. [License](#license)

## Types

  - [1.1](#1.1) <a name='1.1'></a> **Primitives**: When you access a primitive type you work directly on its value.

    + `string`
    + `number`
    + `boolean`
    + `null`
    + `undefined`

  ```javascript
  const foo = 1;

  let bar = foo;

  bar = 9;

  console.log(foo, bar); // => 1, 9
  ```

  - [1.2](#1.2) <a name='1.2'></a> **Complex**: When you access a complex type you work on a reference to its value.

    + `Object`
    + `Array`
    + `function`

  ```javascript
  const foo = [1, 2];

  let bar = foo;

  bar[0] = 9;

  console.log(foo[0], bar[0]); // => 9, 9
  ```

**[⬆ back to top](#table-of-contents)**

## References

  - [2.1](#2.1) <a name='2.1'></a> Use `const` for variables that will not be changed and `let` for variables that will change; avoid using `var` entirely.

  > Why const? This ensures that you can't reassign your references, which can lead to bugs and difficult to comprehend code. Also, when used beyond the spec to include variables that will not change, it serves as self-documenting code.

  eslint rules: [`prefer-const`](https://eslint.org/docs/rules/prefer-const), [`no-const-assign`](https://eslint.org/docs/rules/no-const-assign).

  ```javascript
  // bad
  var a = 1;
  var b = 2;

  // good
  const a = 1;
  const b = 2;
  ```

  - [2.2](#2.2) <a name='2.2'></a> If you must reassign references, use `let` instead of `var`.

  > Why let? `let` is block-scoped rather than function-scoped like `var`.

  eslint rules: [`no-var`](https://eslint.org/docs/rules/no-var).

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

  - [2.3](#2.3) <a name='2.3'></a> Note that both `let` and `const` are block-scoped.

  ```javascript
  // const and let only exist in the blocks they are defined in.
  {
    let a = 1;
    const b = 1;
  }
  console.log(a); // ReferenceError
  console.log(b); // ReferenceError
  ```

  - [2.4](#2.4) <a name='2.4'></a> Split out all `let` and `const` statements onto separate lines

  eslint rules: [`one-var`](https://eslint.org/docs/rules/one-var). _(only applied with rapid/strict)_

  ```javascript
  // bad
  {
    let a = 'hi', c, d;
    const b = 43;
    
    if (b > 1) {
      a = 'no';
    }
    c = a + b;
  }

  // good
  {
    let a = 'hi';
    let c;
    let d;
    const b = 43;

    if (b > 1) {
      a = 'no';
    }

    c = a + b;
  }
  ```



**[⬆ back to top](#table-of-contents)**

## Objects

  - [3.1](#3.1) <a name='3.1'></a> Use the literal syntax for object creation.

  eslint rules: [`no-new-object`](https://eslint.org/docs/rules/no-new-object).

  ```javascript
  // bad
  const item = new Object();

  // good
  const item = {};
  ```

  <a name="es6-computed-properties"></a>
  - [3.2](#3.2) <a name='3.2'></a> Use computed property names when creating objects with dynamic property names.

  > Why? They allow you to define all the properties of an object in one place.

  ```javascript

  function getKey(k) {
    return `a key named ${k}`;
  }

  // bad
  let obj = {
    id: 5,
    name: 'San Francisco',
  };

  obj[getKey('enabled')] = true;

  // good
  const obj = {
    id: 5,
    name: 'San Francisco',
    [getKey('enabled')]: true,
  };
  ```

  <a name="es6-object-shorthand"></a>
  - [3.3](#3.3) <a name='3.3'></a> Use object method shorthand.

  eslint rules: [`object-shorthand`](https://eslint.org/docs/rules/object-shorthand).

  ```javascript
  // bad
  const atom = {
    value: 1,

    addValue: function (value) {
      return atom.value + value;
    },
  };

  // good
  const atom = {
    value: 1,

    addValue(value) {
      return atom.value + value;
    },
  };
  ```

  <a name="es6-object-concise"></a>
  - [3.4](#3.4) <a name='3.4'></a> Use property value shorthand.

  > Why? It is shorter to write and descriptive.

  eslint rules: [`object-shorthand`](https://eslint.org/docs/rules/object-shorthand).

  ```javascript
  const lukeSkywalker = 'Luke Skywalker';

  // bad
  const obj = {
    lukeSkywalker: lukeSkywalker
  };

  // good
  const obj = {
    lukeSkywalker
  };
  ```

  - [3.5](#3.5) <a name='3.5'></a> Alphabetize your object properties.

  > Why? It's easier to find a specific property in a large property list.

  eslint rules: [`rapid7/sort-object-keys`](https://github.com/rapid7/eslint-plugin-rapid7/blob/master/docs/sort-object-keys.md).

  ```javascript
  const anakinSkywalker = 'Anakin Skywalker';
  const lukeSkywalker = 'Luke Skywalker';

  // bad
  const obj = {
    episodeOne: 1,
    twoJediWalkIntoACantina: 2,
    lukeSkywalker,
    episodeThree: 3,
    mayTheFourth: 4,
    anakinSkywalker,
  };

  // good
  const obj = {
    anakinSkywalker,
    episodeOne: 1,
    episodeThree: 3,
    lukeSkywalker,
    mayTheFourth: 4,
    twoJediWalkIntoACantina: 2
  };
  ```

**[⬆ back to top](#table-of-contents)**

## Arrays

  - [4.1](#4.1) <a name='4.1'></a> Use the literal syntax for array creation.

  eslint rules: [`no-array-constructor`](https://eslint.org/docs/rules/no-array-constructor).

  ```javascript
  // bad
  const items = new Array();

  // good
  const items = [];
  ```

  - [4.2](#4.2) <a name='4.2'></a> Prefer use of spread operator instead of push or direct assignment to add items to an array.

  eslint rules: [`prefer-spread`](https://eslint.org/docs/rules/prefer-spread).

  ```javascript
  let someStack = [];

  // bad
  someStack[someStack.length] = 'abracadabra';

  // bad
  someStack.push('abracadabra');

  // good
  let someStack = ['foo'];

  someStack = [
   ...someStack,
   'bar'
  ];
  ```

  <a name="es6-array-spreads"></a>
  - [4.3](#4.3) <a name='4.3'></a> Use the spread operator `...` to copy arrays.

  ```javascript
  // bad
  const length = items.length;
  const itemsCopy = [];

  let index = -1;

  while (++index < length) {
    itemsCopy[index] = items[index];
  }

  // good
  const itemsCopy = [...items];
  ```
- [4.4](#4.4) <a name='4.4'></a> To convert an array-like object to an array, use the spread operator.

  ```javascript
  const nodes = document.querySelectorAll('.foo');
  const arrayOfNodes = [...nodes];
  ```

**[⬆ back to top](#table-of-contents)**

## Destructuring

  - [5.1](#5.1) <a name='5.1'></a> Use object destructuring when accessing and using multiple properties of an object.

  eslint rules: [`prefer-destructuring`](https://eslint.org/docs/rules/prefer-destructuring). _(only applied on rapid7/strict)_

  > Why? Destructuring saves you from creating temporary references for those properties., and when the property is used multiple times it minifies better.

  ```javascript
  // bad
  function getFullName(user) {
    const firstName = user.firstName;
    const lastName = user.lastName;

    return `${firstName} ${lastName}`;
  }

  // good
  function getFullName(obj) {
    const {
     firstName,
     lastName
    } = obj;

    return `${firstName} ${lastName}`;
  }

  // best
  function getFullName({firstName, lastName}) {
    return `${firstName} ${lastName}`;
  }
  ```

  - [5.2](#5.2) <a name='5.2'></a> Use object destructuring for multiple return values, not array destructuring.

  > Why? You can add new properties over time or change the order of things without breaking call sites.

  ```javascript
  // bad
  function processInput(input) {
    // then a miracle occurs
    return [
     left,
     right,
     top,
     bottom
    ];
  }

  // the caller needs to think about the order of return data
  const [
   left,
   __,
   top
  ] = processInput(input);

  // good
  function processInput(input) {
    // then a miracle occurs
    return {
     left,
     right,
     top,
     bottom
    };
  }

  // the caller selects only the data they need
  const {
   left,
   right
  } = processInput(input);
  ```

  - [5.3](#5.3) <a name='5.3'></a> When destructuring in variable assignment, newline all values

  > Why? This helps with readability and creates an extensible style for any size destructuring

  eslint rules: [`object-curly-newline`](http://eslint.org/docs/rules/object-curly-newline.html).

  ```javascript
  // bad
  const {one, two, ...rest} = obj;

  // good
  const {
   one,
   two,
   ...rest
  } = obj;

  // bad
  const [first, ...middle, last] = arr;

  // good
  const [
   first,
   ...middle,
   last
  ] = arr;
  ```


**[⬆ back to top](#table-of-contents)**

## Strings

  - [6.1](#6.1) <a name='6.1'></a> Use single quotes `''` for strings.

  eslint rules: [`quotes`](http://eslint.org/docs/rules/quotes.html).

  ```javascript
  // bad
  const name = "Capt. Janeway";

  // good
  const name = 'Capt. Janeway';
  ```

  - [6.2](#6.2) <a name='6.2'></a> Strings longer than 120 characters should be written across multiple lines using string concatenation.
  
  - [6.3](#6.3) <a name='6.3'></a> Note: If overused, long strings with concatenation could impact performance. [jsPerf](http://jsperf.com/ya-string-concat) & [Discussion](https://github.com/rapid7/javascript-style-guide/issues/40).

  ```javascript
  // bad
  const errorMessage = 'This is a super long error that was thrown because of Batman. When you stop to think about how Batman had anything to do with this, you would get nowhere fast.';

  // bad
  const errorMessage = 'This is a super long error that was thrown because \
  of Batman. When you stop to think about how Batman had anything to do \
  with this, you would get nowhere \
  fast.';

  // good
  const errorMessage = 'This is a super long error that was thrown because ' +
    'of Batman. When you stop to think about how Batman had anything to do ' +
    'with this, you would get nowhere fast.';

  // also good
  const errorMessage = [
   'This is a super long error that was thrown because ',
   'of Batman. When you stop to think about how Batman had anything to do ',
   'with this, you would get nowhere fast.'
  ].join('');
  ```

  <a name="es6-template-literals"></a>
  - [6.4](#6.4) <a name='6.4'></a> When programmatically building up strings, use template strings instead of concatenation.

  > Why? Template strings give you a readable, concise syntax with proper newlines and string interpolation features.

  eslint rules: [`prefer-template`](http://eslint.org/docs/rules/prefer-template.html).

  ```javascript
  // bad
  function sayHi(name) {
    return 'How are you, ' + name + '?';
  }

  // bad
  function sayHi(name) {
    return ['How are you, ', name, '?'].join();
  }

  // good
  function sayHi(name) {
    return `How are you, ${name}?`;
  }
  ```

  - [6.5](#6.5) <a name='6.5'></a> Never use `eval()` on a string, it opens too many vulnerabilities.

**[⬆ back to top](#table-of-contents)**


## Functions

  - [7.1](#7.1) <a name='7.1'></a> Use function expressions instead of function declarations when creating standalone functions. Functions not requiring usage of `this` should be arrow (`=>`) functions.

  > Why? Prevents memory from being allocated when not necessary, and forces habit of writing pure functions.

  eslint rules: [`func-style`](https://eslint.org/docs/rules/func-style). _(only applied on rapid7/strict)_

  ```javascript
  // bad
  function foo() {
  };

  // ok
  const foo = function foo() {
  };

  // good
  const foo = () => {
  };
  ```

  - [7.2](#7.2) <a name='7.2'></a> When you use function expressions, use arrow function notation if at all possible.

  > Why? It creates a version of the function that executes in the context of `this`, or with no context if standalone. This is usually what you want, and it is a more concise syntax.

  > Why not? If you have a fairly complicated function, you might move that logic out into its own function declaration.

  eslint rules: [`prefer-arrow-callback`](https://eslint.org/docs/rules/prefer-arrow-callback), [`arrow-spacing`](https://eslint.org/docs/rules/arrow-spacing).

  ```javascript
  // bad
  [1, 2, 3].map(function (x) {
    const y = x + 1;
    return x * y;
  });

  // good
  [1, 2, 3].map((x) => {
    const y = x + 1;
    return x * y;
  });
  ```

  - [7.3](#7.3) <a name='7.3'></a> Immediately-Invoked Function Expressions (IIFE):
  
  > NOTE: This paradigm is rarely (if ever) needed with proper use of a build system, so it should be avoided wherever possible. If you do find yourself using this to solve a problem, you should take a step back and re-examine the problem as it can likely solve in in a different way.

  ```javascript
  (() => {
    console.log('Welcome to the Internet. Please follow me.');
  }());
  ```

  - [7.4](#7.4) <a name='7.4'></a> Never declare a function in a non-function block (if, while, etc). Assign the function to a variable instead. Browsers will allow you to do it, but they all interpret it differently.
  
  ```javascript
  // bad
  const doThing = (stuff) => {
    // computations
  
    const doOtherThing = (stuff, otherStuff) => {
      // other computations
      
      return result;
    };
    
    return doOtherThing(stuff, 'some value');
  };
  
  // good
  const doOtherThing = (otherStuff) => {
    // other computations
    
    return result;
  };
  
  const doThing = (stuff) => {
    // computations
    
    return doOtherThing(stuff, 'some value');
  };
  ```
  
  - [7.5](#7.5) <a name='7.5'></a> **Note:** ECMA-262 defines a `block` as a list of statements. A function declaration is not a statement. [Read ECMA-262's note on this issue](http://www.ecma-international.org/publications/files/ECMA-ST/Ecma-262.pdf#page=97).

  ```javascript
  // bad
  if (currentUser) {
    function test() {
      console.log('Nope.');
    }
  }

  // ok
  let test = () => {};

  if (currentUser) {
    test = () => console.log('Yup.');
  }
  
  // good
  const test = currentUser
    ? () => console.log('Yup.')
    : () => {};
  ```

  - [7.6](#7.6) <a name='7.6'></a> Never name a parameter `arguments`. This will take precedence over the `arguments` object that is given to every function scope.

  ```javascript
  // bad
  function nope(name, options, arguments) {
    // ...stuff...
  }

  // good
  function yup(name, options, args) {
    // ...stuff...
  }
  ```

  <a name="es6-rest"></a>
  - [7.7](#7.7) <a name='7.7'></a> Never use `arguments` directly, opt to use rest syntax `...` instead.

  eslint rules: [`prefer-rest-params`](https://eslint.org/docs/rules/prefer-rest-params).

  > Why? `...` is explicit about which arguments you want pulled. Plus rest arguments are a real Array and not Array-like like `arguments`. Plus, you can only use `arguments` if using a `function`, it is not available in fat-arrow functions.

  ```javascript
  // bad
  function concatenateAll() {
    return Array.prototype.slice.call(arguments).join('');
  }

  // good
  const concatenateAll = (...args) => args.join('');
  ```

  <a name="es6-default-parameters"></a>
  - [7.8](#7.8) <a name='7.8'></a> Use default parameter syntax rather than mutating function arguments.

  eslint rules: [`no-param-reassign`](https://eslint.org/docs/rules/no-param-reassign). _(only applied on rapid7/strict)_

  ```javascript
  // really bad
  const handleThings = (opts) => {
    // No! We shouldn't mutate function arguments.
    // Double bad: if opts is falsy it'll be set to an object which may
    // be what you want but it can introduce subtle bugs.
    opts = opts || {};
    // ...
  }

  // still bad
  const handleThings = (opts) => {
    if (opts === void 0) {
      opts = {};
    }
    // ...
  }

  // good
  const handleThings = (opts = {}) => {
    // ...
  }
  ```

  - [7.9](#7.9) <a name='7.9'></a> Avoid side effects with default parameters.

  > Why? They are confusing to reason about.

  ```javascript
  // bad
  let b = 1;

  const count = (a = b++) => console.log(a);
  
  count();  // 1
  count();  // 2
  count(3); // 3
  count();  // 3
  ```

  - [7.10](#7.10) <a name='7.10'></a> Always put default parameters last.

  ```javascript
  // bad
  const handleThings = (opts = {}, name) => {
    // ...
  }

  // good
  const handleThings = (name, opts = {}) => {
    // ...
  }
  ```

- [7.11](#7.11) <a name='7.11'></a> Never use the Function constructor to create a new function.

  > Why? Creating a function in this way evaluates a string similarly to eval(), which opens vulnerabilities.

  eslint rules: [`no-new-func`](https://eslint.org/docs/rules/no-new-func).

  ```javascript
  // bad
  var add = new Function('a', 'b', 'return a + b');

  // still bad
  var subtract = Function('a', 'b', 'return a - b');

  // good
  let subtract = (a, b) => {
    return a - b;
  }
  ```

- [7.12](#7.12) <a name="7.12"></a> Spacing in a function signature.

  > Why? Consistency is good, and you shouldn’t have to add or remove a space when adding or removing a name.

  eslint rules: [`space-before-function-paren`](https://eslint.org/docs/rules/space-before-function-paren).

  ```javascript
  // bad
  const f = function(){};
  const g = function (){};
  const h = function() {};

  // good
  const x = function () {};
  const y = function a() {};
  ```

- [7.13](#7.13) <a name="7.13"></a> Avoid long functions.  If your function is over 50 lines, or performs multiple operations, it should likely be refactored.

  > Why? Long functions are difficult to test, more likely to break, are not single purpose

   eslint rules: [`max-statements`](https://eslint.org/docs/rules/max-statements).
   
  > NOTE: The maximum statements will be limited to _25 lines_ when using _rapid7/strict_

- [7.14](#7.14) <a name="7.14"></a> Avoid deeply nested code

  > Why? Deeply nested code makes testing difficult and is usually a sign that that your function is doing too much

   eslint rules: [`max-depth`](https://eslint.org/docs/rules/max-depth).
   
  > NOTE: The maximum depth will be limited to _1_ when using _rapid7/strict_

- [7.15](#7.15) <a name='7.15'></a> Instance methods or pure functions are preferred to anonymous functions

  > Why? Anonymous functions are difficult to test, and they make code difficult to read.  Future-proofs code in some cases be separating functionality.  Helps limit code depth

  > Why not? Very short callbacks that are unlikely to need to be tested, reused, or changed

  eslint rules: [`max-nested-callbacks`](https://eslint.org/docs/rules/max-nested-callbacks)

  ```javascript
  // bad
  <SomeComponent onClick={(event) => someAction(event.currentTarget.value)}/>
  
  // good
  onClickSomeComponent = (event) => someAction(event.currentTarget.value);
  
  <SomeComponent onClick={this.onClickSomeComponent}/>
  ```

**[⬆ back to top](#table-of-contents)**


## Classes

  > NOTE: Except for React class components, the use of classes are generally discouraged, as functional composition is the vastly preferred approach. If you must use a class, these are general guidelines for working with them, however you should attempt to solve the problem in a more functional way if possible.

  - [9.1](#9.1) <a name='9.1'></a> Always use `class`. Avoid manipulating `prototype` directly.

  > Why? `class` syntax is more concise and easier to reason about.

  ```javascript
  // bad
  function Queue(contents = []) {
    this._queue = [...contents];
  }

  Queue.prototype.pop = function () {
    const value = this._queue[0];
    this._queue.splice(0, 1);
    return value;
  }


  // good
  class Queue {
    constructor(contents = []) {
      this._queue = [...contents];
    }

    pop() {
      const value = this._queue[0];
      this._queue.splice(0, 1);
      return value;
    }
  }
  ```

  - [9.2](#9.2) <a name='9.2'></a> Use `extends` for inheritance.

  > Why? It is a built-in way to inherit prototype functionality without breaking `instanceof`.

  ```javascript
  // bad
  const inherits = require('inherits');

  function PeekableQueue(contents) {
    Queue.apply(this, contents);
  }

  inherits(PeekableQueue, Queue);

  PeekableQueue.prototype.peek = function () {
    return this._queue[0];
  }

  // good
  class PeekableQueue extends Queue {
    peek() {
      return this._queue[0];
    }
  }
  ```

  - [9.3](#9.3) <a name='9.3'></a> Methods can return `this` to help with method chaining.

  > NOTE: If this is the only basis for creating a class, the same approach can be accomplished through the use of [`Object.create()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/create), which automatically binds `this` in the prototype functions to the object being created.

  ```javascript
  // bad
  Jedi.prototype.jump = function () {
    this.jumping = true;
    return true;
  };

  Jedi.prototype.setHeight = function (height) {
    this.height = height;
  };

  const luke = new Jedi();

  luke.jump(); // => true
  luke.setHeight(20); // => undefined

  // good
  class Jedi {
    jump() {
      this.jumping = true;
      return this;
    }

    setHeight(height) {
      this.height = height;
      return this;
    }
  }

  const luke = new Jedi();

  luke
    .jump()
    .setHeight(20);
    
  // better
  const createJedi = () => {
    const jedi = Object.create({
      jump() {
        this.jumping = true;
        return this;
      },
      setHeight(height) {
        this.height = height;
        return this;
      }
    });
    
    jedi.jumping = false;
    jedi.height = 0;
   
    return jedi;
  };
  
  const luke = createJedi();
  
  luke
    .jump()
    .setHeight(20);
  ```


  - [9.4](#9.4) <a name='9.4'></a> It's okay to write a custom toString() method, just make sure it works successfully and causes no side effects.

  ```javascript
  class Jedi {
    constructor(options = {}) {
      this.name = options.name || 'no name';
    }

    getName() {
      return this.name;
    }

    toString() {
      return `Jedi - ${this.getName()}`;
    }
  }
  ```

**[⬆ back to top](#table-of-contents)**


## Modules

  - [10.1](#10.1) <a name='10.1'></a> Always use modules (`import`/`export`) over a non-standard module system when working in the browser environment.

  > Why? Modules are the current JS standard, and are supported by all modern build systems.

  eslint rules: [`import/no-commonjs`](https://github.com/benmosher/eslint-plugin-import/blob/master/docs/rules/no-commonjs.md) _(only applied on rapid7/browser)_

  ```javascript
  // bad
  const Rapid7StyleGuide = require('./Rapid7StyleGuide');
  module.exports = Rapid7StyleGuide.es6;

  // ok
  import Rapid7StyleGuide from './Rapid7StyleGuide';
  export default Rapid7StyleGuide.es6;

  // best
  import { es6 } from './Rapid7StyleGuide';
  export default es6;
  ```
  
  **Note:** only use `import/export` when developing for the browser as Node ([less than Node 10](https://nodejs.org/api/esm)) does not support this syntax. When developing in Node versions lower than 10 use `require/modules.export`.
  
  ```javascript
  const Rapid7StyleGuide = require('./Rapid7StyleGuide');
  module.exports = Rapid7StyleGuide.node;
  ```

  - [10.2](#10.2) <a name='10.2'></a>And do not export directly from an import.

  > Why? Although the one-liner is concise, having one import definition and one clear export definition makes things consistent.

  ```javascript
  // bad
  // filename es6.js
  export {
   es6 as default
  } from './rapid7StyleGuide';

  // good
  // filename es6.js
  import {
   es6
  } from './Rapid7StyleGuide';

  export default es6;
  ```

  - [10.3](#10.3) <a name='10.3'></a>Place all imports at the top of the file and all exports at the end of the file

  eslint rules: [`import/first`](https://github.com/benmosher/eslint-plugin-import/blob/master/docs/rules/first.md)

  > Why? Makes it easy to keep track of all imports and exports.
  
  > NOTE: This does not apply to named exports declared inline upon instantiation.

**[⬆ back to top](#table-of-contents)**

## Iterators, Generators, and Asynchronisity

  - [11.1](#11.1) <a name='11.1'></a> Don't use the custom `__iterator__` property, and avoid loops in general. Prefer JavaScript's higher-order functions like `map()` and `reduce()` instead of loops like `for-of`.

  > Why? This enforces our immutable rule. Dealing with pure functions that return values is easier to reason about than side-effects.

  eslint rules: [`no-iterator`](https://eslint.org/docs/rules/no-iterator).

  ```javascript
  const numbers = [1, 2, 3, 4, 5];

  // bad
  let sum = 0;

  for (let num of numbers) {
    sum += num;
  }

  sum === 15;

  // good
  let sum = 0;

  numbers.forEach((num) => {
   return num += sum;
  });

  sum === 15;

  // best (use the functional force)
  const sum = numbers.reduce((total, num) => {
   return total + num;
  }, 0);

  sum === 15;
  ```

  - [11.2](#11.2) <a name='11.2'></a> For methods that perform asynchronous actions, use async functions instead of Promises (assuming you develop in an environment that supports it).

  > Why? `async` / `await` is officially part of the JS spec as of 2017, and allows for better control flow and error handling, plus improved readability. Additionally, they return promises so they can be handled in the same way.

  ```javascript
  // bad
  const getStuff = (handleSuccess, handleError) => {
   return axios.get('/foo')
    .then(handleSuccess)
    .catch(handleError);
  };

  // good
  const getStuff = async (handleSuccess, handleError) => {
   try {
    const response = await axios.get('/foo');

    return handleSuccess(response);
   } catch (error) {
    return handleError(error);
   }
  };
  ```
  
  > NOTE: If you need to parallelize asynchronous operations in an `async` function, you should make use of [`Promise.all()`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all).
  
  ```javascript
  const getLotsOfStuff = async (handleSuccess, handleError) => {
   try {
    const response = await Promise.all([
      axios.get('/foo'),
      axios.get('/bar'),
      axios.get('/baz'),
    ]);

    return handleSuccess(response);
   } catch (error) {
    return handleError(error);
   }
  };
  ```

  - [11.3](#11.3) <a name='11.3'></a> Prefer the use of `async` / `await` over the use of generators.

  > Why? The use of generators is for better control flow management, and can usually be solved with function modularity and use of async functions.

**[⬆ back to top](#table-of-contents)**


## Properties

  - [12.1](#12.1) <a name='12.1'></a> Use dot notation when accessing properties.

  eslint rules: [`dot-notation`](https://eslint.org/docs/rules/dot-notation).

  ```javascript
  const luke = {
    jedi: true,
    age: 28,
  };

  // bad
  const isJedi = luke['jedi'];

  // good
  const isJedi = luke.jedi;
  ```

  - [12.2](#12.2) <a name='12.2'></a> Use subscript notation `[]` when accessing properties with a variable.

  ```javascript
  const luke = {
    jedi: true,
    age: 28,
  };

  const getProp = (prop, object) => {
    return object[prop];
  };

  const isJedi = getProp('jedi', luke);
  ```
  
  > NOTE: There are also several libraries that provide the above `getProp` functionality as a standard getter. Examples:
    - [Ramda](https://ramdajs.com/docs/#prop)
    - [lodash](https://lodash.com/docs/4.17.10#get)
    - [unchanged](https://github.com/planttheidea/unchanged/blob/master/README.md#get)

  - [12.3](#12.3) <a name='12.3'></a> Avoid the use of 'Magic Numbers' and 'Magic Strings' especially when they are reused.  Use static `const` declarations in SCREAMING_SNAKE_CASE instead

  > Why? Makes it easy to change values when necessary, makes values available in testing and other classes, and adds clarity to what the values represent

  eslint rules: [`no-magic-numbers`](https://eslint.org/docs/rules/no-magic-numbers). _(only applied on rapid7/strict)_

  ```javascript
  // bad
  class Person {
    age = 12;

    validAge() {
      return this.age > 18 && this.age < 35;
    }

    onClickSubmit() {
      if (!this.validAge()) {
        console.error('Your age is not valid');
      }
    }
  }

  // good
  const AGE_MINIMUM = 18;
  const AGE_MAXIMUM = 35;
  const DEFAULT_PERSON_AGE = 12;
  const ERROR_ARE_INVALID = 'Your age is not valid';

  class Person {    
    age = DEFAULT_PERSON_AGE;

    validAge() {
      return this.age > AGE_MINIMUM && this.age < AGE_MAXIMUM;
    }

    onClickSubmit() {
      if (!this.validAge()) {
        console.error(Person.ERROR_AGE_INVALID);
      }
    }
  }
  ```
**[⬆ back to top](#table-of-contents)**

## Variables

  - [13.1](#13.1) <a name='13.1'></a> Always use `const` or `let` to declare variables, never `var`. All the same goals `var` has can be accomplished with `let`, without providing the same implicit hoisting / access issues that `var` can provide.

  eslint rules: [`no-var`](https://eslint.org/docs/rules/no-var).

  ```javascript
  // bad
  superPower = new SuperPower();

  // good
  const superPower = new SuperPower();
  ```

  - [13.2](#13.2) <a name='13.2'></a> Use one `const` / `let` declaration per variable.

  > Why? It's easier to add new variable declarations this way.
  
  > NOTE: 

  eslint rules: [`one-var`](https://eslint.org/docs/rules/one-var). _(the only separation enforced by default is for `const` declarations. The enforcement is also applied for `let` in rapid7/strict)_

  ```javascript
  // bad
  const items = getItems(),
      goSportsTeam = true,
      dragonball = 'z';
  
  let foo = 'foo',
      bar = 'bar';

  // good
  const items = getItems();
  const goSportsTeam = true;
  const dragonball = 'z';
  
  let foo = 'foo';
  let bar = 'bar';
  ```

  - [13.3](#13.3) <a name='13.3'></a> Assign variables where you need them, but place them in a reasonable place.

  > Why? `let` and `const` are block scoped and not function scoped, so memory will only be allocated when that code stage is reached.

  ```javascript
  // bad
  const getNameAndMessage = (name) => {
    const message = `doing stuff with ${name}`;

    if (name === 'test') {
      return {
       isValid: false
      };
    }

    return {
     isValid: true,
     message,
     name
    };     
  };

  // good
  const getNameAndMessage = (name) => {
    if (name === 'test') {
      return {
       isValid: false
      };
    }

    const message = `doing stuff with ${name}`;

    return {
     isValid: true,
     message,
     name
    };
  };
  ```
  
  > NOTE: You should group all your `const`s and then afterward group all your `let`s if declared in vicinity of one another. This is helpful when later on you might need to assign a variable depending on one of the previous assigned variables.

  ```javascript
  // bad
  let index;
  const items = getItems();
  let dragonball;
  const goSportsTeam = true;
  let len;

  // good
  const goSportsTeam = true;
  const items = getItems();

  let dragonball;
  let index;
  let len;
  ```

**[⬆ back to top](#table-of-contents)**


## Hoisting

  - [14.1](#14.1) <a name='14.1'></a> Function expressions hoist their variable name, but not the function assignment.

  ```javascript
  console.log(fn); // => undefined

  fn(); // => TypeError fn is not a function

  var fn = () => {
    console.log('function expression');
  };
  ```

  - [14.2](#14.2) <a name='14.2'></a> If you need to access a function prior to its declaration in the code, then it must be defined as a named `function`, so that it is correctly hoisted.

  ```javascript
  // bad
  const usesFn = (thing) => fn(thing);
  
  const fn = (thing) => console.log(thing);
  
  // good
  // eslint-disable-next-line no-use-before-define
  const usesFn = (thing) => fn(thing);
  
  function fn(thing) {
    console.log(thing);
  }
  ```

  - [14.3](#14.3) <a name='14.3'></a> Do not use a function prior to its declaration (unless specifically in scenarios described in [14.2](#14.2))

  eslint rules: [`no-use-before-define`](https://eslint.org/docs/rules/no-use-before-define).
  
  ```javascript
  // bad
  fn('thing');
  
  const fn = (thing) => console.log(thing);
  
  // good  
  const fn = (thing) => console.log(thing);
  
  fn('thing');
  ```

  - For more information refer to [JavaScript Scoping & Hoisting](http://www.adequatelygood.com/2010/2/JavaScript-Scoping-and-Hoisting/) by [Ben Cherry](http://www.adequatelygood.com/).

**[⬆ back to top](#table-of-contents)**


## Comparison Operators & Equality

  - [15.1](#15.1) <a name='15.1'></a> Always use `===` and `!==` over `==` and `!=`.

  eslint rules: [`eqeqeq`](https://eslint.org/docs/rules/eqeqeq).
  
  - [15.2](#15.2) <a name='15.2'></a> Conditional statements such as the `if` statement evaluate their expression using coercion with the `ToBoolean` abstract method and always follow these simple rules:

  + `{}` evaluates to `true`
  + `[]` evaluates to `true`
  + `function(){}` or `() => {}` evaluates to `true`
  + strings that are populated (e.g., `'foo'`) evaluate to `true`
  + numbers that are non-zero (e.g., `8`, or `-12`) evaluate to `true`
  + `undefined` evaluates to `false`
  + `null` evaluates to `false`
  + empty strings (e.g., `''`) evaluate to `false`
  + `0` or `NaN` evaluates to `false`
  + booleans evaluate to their value (e.g. `true` or `false`)

  ```javascript
  if ([0]) {
    // true
    // An array is a complex object, and all complex objects are truthy
  }
  ```

  - [15.3](#15.3) <a name='15.3'></a> Use shortcuts.

  ```javascript
  // bad
  if (name !== '') {
    // ...stuff...
  }

  // good
  if (name) {
    // ...stuff...
  }

  // bad
  if (collection.length > 0) {
    // ...stuff...
  }

  // good
  if (collection.length) {
    // ...stuff...
  }

  // bad - potentially confusing since 0 might be a valid id.
  if (id) {
    // ...stuff...
  }
  ```

  - [15.4](#15.4) <a name='15.4'></a> For more information see [Truth Equality and JavaScript](http://javascriptweblog.wordpress.com/2011/02/07/truth-equality-and-javascript/#more-2108) by Angus Croll.

**[⬆ back to top](#table-of-contents)**

## Blocks

  - [16.1](#16.1) <a name='16.1'></a> Use braces and newlines with all blocks.

  eslint rules: [`curly`](https://eslint.org/docs/rules/curly).

  ```javascript
  // bad
  if (test)
    return false;

  // really bad
  if (test) return false;

  // good
  if (test) {
    return false;
  }

  // bad
  const foo = () => { return false; }

  // good
  const foo = () => {
    return false;
  }
  ```

  - [16.2](#16.2) <a name='16.2'></a> If you're using blocks with `if` and `else`, put `else` on the same line as your
    `if` block's closing brace.

  eslint rules: [`brace-style`](https://eslint.org/docs/rules/brace-style).

  ```javascript
  // bad
  if (test) {
    thing1();
    thing2();
  }
  else {
    thing3();
  }

  // good
  if (test) {
    thing1();
    thing2();
  } else {
    thing3();
  }
  ```


**[⬆ back to top](#table-of-contents)**


## Comments

  - [17.1](#17.1) <a name='17.1'></a> Use `/** ... */` for jsdoc comments, and `/* ... */` for multi-line comments. Include a description, specify types and values for all parameters and return values.  Follow [jsdoc syntax](http://usejsdoc.org/) in jsdoc comments.
  
  > NOTE: The exception to this rule is for ESLint comments, which can be whatever is best suited for the usage.

  ```javascript
  // bad
  // make() returns a new element
  // based on the passed in tag name
  //
  // @param {String} tagName
  // @return {Element} element
  const make = (tagName) => {
    return document.createElement(tagName);
  };

  // good
  /**
   * @function make
   *
   * @description
   * returns a new element based on the passed in tag name
   *
   * @param {string} tagName the type of element to create
   * @return {HTMLElement} the constructed element
   */
  const make = (tagName) => {
    return document.createElement(tagName);
  };
  ```

  - [17.2](#17.2) <a name='17.2'></a> Use `//` for single line comments. Place single line comments on a newline above the subject of the comment. Put an empty line before the comment unless it's on the first line of a block.
  
  > NOTE: The exception to this rule is for ESLint comments, which can be whatever is best suited for the usage.

  eslint rules: [`line-comment-position`](https://eslint.org/docs/rules/line-comment-position). _(only applied on rapid7/strict)_

  ```javascript
  // bad
  const active = true;  // is current tab

  // good
  // is current tab
  const active = true;

  // bad
  const getType = () => {
    console.log('fetching type...');
    // set the default type to 'no type'
    const type = this._type || 'no type';

    return type;
  };

  // good
  const getType = () => {
    console.log('fetching type...');

    // set the default type to 'no type'
    const type = this._type || 'no type';

    return type;
  };

  // also good
  const getType = () => {
    // set the default type to 'no type'
    const type = this._type || 'no type';

    return type;
  };
  ```

  - [17.3](#17.3) <a name='17.3'></a> Prefixing your future-work comments with `@todo` helps other developers quickly understand if you're pointing out a problem that needs to be revisited, or if you're suggesting a solution to the problem that needs to be implemented. These are different than regular comments because they are actionable

  - [17.4](#17.4) <a name='17.4'></a> Use `// @todo` to annotate problems.

  ```javascript
  class Calculator extends Abacus {
    constructor() {
      super();

      // @todo shouldn't use a global here
      total = 0;
    }
  }
  ```


  - [17.5](#17.5) <a name='17.5'></a> Use jsdoc comments with function name, description, and type + name= for all properties and return values on all methods. Optionally, you can provide a description for each `@param` and `@returns`.

  eslint rules: [`valid-jsdoc`](https://eslint.org/docs/rules/valid-jsdoc). _(only applied on rapid7/strict)_

  ```javascript
  class Calculator {
    /**
     * @function add
     *
     * @description
     * adds the firstNumber to the secondNumber
     *
     * @param {number} firstNumber
     * @param {number} secondNumber
     * @returns {number} The result of adding firstNumber and secondNumber
     */
    add(firstNumber, secondNumber) {
      return firstNumber + secondNumber;
    }
  }
  ```

**[⬆ back to top](#table-of-contents)**


## Whitespace

  - [18.1](#18.1) <a name='18.1'></a> Use soft tabs set to 2 spaces.

  eslint rules: [`indent`](https://eslint.org/docs/rules/indent).

  ```javascript
  // bad
  function () {
  ∙∙∙∙const name;
  }

  // bad
  function () {
  ∙const name;
  }

  // good
  function () {
  ∙∙const name;
  }
  ```

  - [18.2](#18.2) <a name='18.2'></a> Place 1 space before the leading brace.

  eslint rules: [`space-before-blocks`](https://eslint.org/docs/rules/space-before-blocks).

  ```javascript
  // bad
  function test(){
    console.log('test');
  }

  // good
  function test() {
    console.log('test');
  }

  // bad
  dog.set('attr',{
    age: '1 year',
    breed: 'Bernese Mountain Dog',
  });

  // good
  dog.set('attr', {
    age: '1 year',
    breed: 'Bernese Mountain Dog',
  });
  ```

  - [18.3](#18.3) <a name='18.3'></a> Place 1 space before and after the parenthesds in control statements (`if`, `while` etc.). Place no space before the argument list in function calls and declarations.

  eslint rules: [`space-after-keywords`](https://eslint.org/docs/rules/space-after-keywords), [`space-before-keywords`](https://eslint.org/docs/rules/space-before-keywords).

  ```javascript
  // bad
  if(isJedi) {
    fight ();
  }

  // good
  if (isJedi) {
    fight();
  }

  // bad
  function fight () {
    console.log ('Swooosh!');
  }

  // good
  function fight() {
    console.log('Swooosh!');
  }
  ```

  - [18.4](#18.4) <a name='18.4'></a> Place exactly 1 space between operators.

  eslint rules: [`space-infix-ops`](https://eslint.org/docs/rules/space-infix-ops).

  ```javascript
  // bad
  const x=y+5;

  // good
  const x = y + 5;
  ```

  - [18.5](#18.5) <a name='18.5'></a> End files with a single newline character.

  ```javascript
  // bad
  (function (global) {
    // ...stuff...
  })(this);
  ```

  ```javascript
  // bad
  (function (global) {
    // ...stuff...
  })(this);↵
  ↵
  ```

  ```javascript
  // good
  (function (global) {
    // ...stuff...
  })(this);↵
  ```

  - [18.6](#18.6) <a name='18.6'></a> Use indentation when making long method chains. Use a leading dot, which
    emphasizes that the line is a method call, not a new statement.  Chaining should use a 2 character continuation indent

  ```javascript
  // bad
  $('#items').find('.selected').highlight().end().find('.open').updateCount();

  // bad
  $('#items').
    find('.selected').
      highlight().
      end().
    find('.open').
      updateCount();

  // good
  $('#items')
    .find('.selected')
    .highlight()
    .end()
    .find('.open')
    .updateCount();

  // bad
  const leds = stage.selectAll('.led').data(data).enter().append('svg:svg').class('led', true)
      .attr('width', (radius + margin) * 2).append('svg:g')
      .attr('transform', 'translate(' + (radius + margin) + ',' + (radius + margin) + ')')
      .call(tron.led);

  // good
  const leds = stage
    .selectAll('.led')
    .data(data)
    .enter()
    .append('svg:svg')
    .classed('led', true)
    .attr('width', (radius + margin) * 2)
    .append('svg:g')
    .attr('transform', 'translate(' + (radius + margin) + ',' + (radius + margin) + ')')
    .call(tron.led);
  ```

  - [18.7](#18.7) <a name='18.7'></a> Leave a newline after blocks and before the next statement.

  eslint rules: [`newline-before-return`](https://eslint.org/docs/rules/newline-before-return)

  ```javascript
  // bad
  if (foo) {
    return bar;
  }
  return baz;

  // good
  if (foo) {
    return bar;
  }

  return baz;
  ```

  - [18.8](#18.8) <a name='18.8'></a> Do not pad your blocks with blank lines.

  eslint rules: [`padded-blocks`](https://eslint.org/docs/rules/padded-blocks).

  ```javascript
  // bad
  function bar() {

    console.log(foo);

  }

  // also bad
  if (baz) {

    console.log(qux);
  } else {
    console.log(foo);

  }

  // good
  function bar() {
    console.log(foo);
  }

  // good
  if (baz) {
    console.log(qux);
  } else {
    console.log(foo);
  }
  ```

  - [18.9](#18.9) <a name='18.9'></a> Do not add spaces inside parentheses.

  eslint rules: [`space-in-parens`](https://eslint.org/docs/rules/space-in-parens).

  ```javascript
  // bad
  function bar( foo ) {
    return foo;
  }

  // good
  function bar(foo) {
    return foo;
  }

  // bad
  if ( foo ) {
    console.log(foo);
  }

  // good
  if (foo) {
    console.log(foo);
  }
  ```

  - [18.10](#18.10) <a name='18.10'></a> Do not add spaces inside brackets.

  eslint rules: [`array-bracket-spacing`](https://eslint.org/docs/rules/array-bracket-spacing).

  ```javascript
  // bad
  const foo = [ 1, 2, 3 ];
  console.log(foo[ 0 ]);

  // good
  const foo = [1, 2, 3];
  console.log(foo[0]);
  ```

  - [18.11](#18.11) <a name='18.11'></a> Do not add spaces inside curly braces.

  eslint rules: [`object-curly-spacing`](https://eslint.org/docs/rules/object-curly-spacing).

  ```javascript
  // bad
  const foo = { clark: 'kent' };

  // good
  const foo = {clark: 'kent'};
  ```

  - [18.12](#18.12) <a name='18.12'></a> Maximum line length is 120 characters

  > Why? Long lines are difficult to read themselves and in diffs, and they make viewing code side-by-side difficult

  eslint rules: [`max-len`](https://eslint.org/docs/rules/max-len).
  
  - [18.13](#18.13) <a name='18.13'></a> In newlined operations, the operator should be placed at the end of the newline unless an logical operator (`&&`, `||`) or ternary operator (`?`, `:`).

  eslint rules: [`operator-linebreak`](https://eslint.org/docs/rules/operator-linebreak).
  
  ```javascript
  // bad
  const result = ifThing() ?
    'yes' :
    'no';
   
  // good
  const result = ifThing()
    ? 'yes'
    : 'no';
  ```

**[⬆ back to top](#table-of-contents)**

## Commas

  - [19.1](#19.1) <a name='19.1'></a> Leading commas: **Nope.**

  eslint rules: [`comma-style`](https://eslint.org/docs/rules/comma-style).

  ```javascript
  // bad
  const story = [
      once
    , upon
    , aTime
  ];

  // good
  const story = [
    once,
    upon,
    aTime
  ];

  // bad
  const hero = {
      firstName: 'Ada'
    , lastName: 'Lovelace'
    , birthYear: 1815
    , superPower: 'computers'
  };

  // good
  const hero = {
    firstName: 'Ada',
    lastName: 'Lovelace',
    birthYear: 1815,
    superPower: 'computers'
  };
  ```

  - [19.2](#19.2) <a name='19.2'></a> Additional trailing comma: **Yup.**

  eslint rules: [`comma-dangle`](https://eslint.org/docs/rules/comma-dangle).

  > Why? A trailing comma can lead to cleaner git diffs, and makes movement of newlined properties easier.

  ```javascript
  // bad
  const hero = {
    firstName: 'Dana',
    lastName: 'Scully'
  };

  const heroes = [
    'Batman',
    'Superman'
  ];

  // good
  const hero = {
    firstName: 'Dana',
    lastName: 'Scully',
  };

  const heroes = [
    'Batman',
    'Superman',
  ];
  ```

**[⬆ back to top](#table-of-contents)**


## Semicolons

  - [20.1](#20.1) <a name='20.1'></a> **Yup.**

  eslint rules: [`semi`](https://eslint.org/docs/rules/semi).

  ```javascript
  // bad
  (function () {
    const name = 'Skywalker'
    return name
  })()

  // good
  (() => {
    const name = 'Skywalker';
    return name;
  })();

  // good (guards against the function becoming an argument when two files with IIFEs are concatenated)
  ;(() => {
    const name = 'Skywalker';
    return name;
  })();
  ```

  [Read more](http://stackoverflow.com/questions/7365172/semicolon-before-self-invoking-function/7365214%237365214).

**[⬆ back to top](#table-of-contents)**


## Type Casting & Coercion

  - [21.1](#21.1) <a name='21.1'></a> Use template literals for string coercion:

  ```javascript
  //  => this.reviewScore = 9;

  // bad
  const totalScore = this.reviewScore + '';

  // good
  const totalScore = `${this.reviewScore}`;
  ```

  - [21.2](#21.2) <a name='21.2'></a> Numbers: Use the `+` unary operator for type casting and `parseInt` always with a radix for parsing strings.

  ```javascript
  const inputValue = '4';

  // bad
  const val = new Number(inputValue);

  // bad
  const val = inputValue >> 0;

  // bad
  const val = parseInt(inputValue);

  // good
  const val = +inputValue;

  // good
  const val = parseInt(inputValue, 10);
  ```

  - [21.3](#21.3) <a name='21.3'></a> If for whatever reason you are doing something wild and `parseInt` is your bottleneck and need to use Bitshift for [performance reasons](http://jsperf.com/coercion-vs-casting/3), leave a comment explaining why and what you're doing.

  ```javascript
  // good
  /**
   * parseInt was the reason my code was slow.
   * Bitshifting the String to coerce it to a
   * Number made it a lot faster.
   */
  const val = inputValue >> 0;
  ```
  
  > NOTE: Be careful when using bitshift operations. Numbers are represented as [64-bit values](http://es5.github.io/#x4.3.19), but Bitshift operations always return a 32-bit integer ([source](http://es5.github.io/#x11.7)). Bitshift can lead to unexpected behavior for integer values larger than 32 bits. [Discussion](https://github.com/rapid7/javascript-style-guide/issues/109). Largest signed 32-bit Int is 2,147,483,647:

  ```javascript
  2147483647 >> 0 //=> 2147483647
  2147483648 >> 0 //=> -2147483648
  2147483649 >> 0 //=> -2147483647
  ```

  - [21.4](#21.4) <a name='21.4'></a> Use "double-bang" when coercing objects to booleans:

  ```javascript
  const age = 0;

  // bad
  const hasAge = new Boolean(age);

  // bad
  const hasAge = Boolean(age);

  // good
  const hasAge = !!age;
  ```

**[⬆ back to top](#table-of-contents)**


## Naming Conventions

  - [22.1](#22.1) <a name='22.1'></a> Avoid single letter names and abbreviations. Be descriptive with your naming.

  > Why? Modern autocomplete and minification makes clarity more valuable than keystrokes

  ```javascript
  // bad
  function q() {
    // ...stuff...
  }

  // good
  function query() {
    // ..stuff..
  }
  ```

  - [22.2](#22.2) <a name='22.2'></a> Use camelCase when naming objects, functions, and instances.

  eslint rules: [`camelcase`](https://eslint.org/docs/rules/camelcase).

  ```javascript
  // bad
  const OBJEcttsssss = {};
  const this_is_my_object = {};
  function c() {}

  // good
  const thisIsMyObject = {};
  function thisIsMyFunction() {}
  ```

  - [22.3](#22.3) <a name='22.3'></a> Use PascalCase exclusively when naming constructors, classes, or React components.

  ```javascript
  // bad
  function user(options) {
    this.name = options.name;
  }

  const bad = new user({
    name: 'nope',
  });

  // good
  class User {
    constructor(options) {
      this.name = options.name;
    }
  }

  const good = new User({
    name: 'yup',
  });
  
  // bad
  const myComponent = () = <div />;
  
  // good
  const MyComponent = () => <div />;
  ```

  - [22.4](#22.4) <a name='22.4'></a> If you want to denote a private property for an object / class, you can use a leading underscore `_`.

  eslint rules: [`rapid7/no-trailing-underscore`](https://github.com/rapid7/eslint-plugin-rapid7/blob/master/docs/no-trailing-underscore.md).
  
  > NOTE: For now, the concept of private properties is entirely by convention, and has the potential to confuse users and make refators more challenging, so it is generally discouraged.

  ```javascript
  // bad
  this.__firstName__ = 'Panda';
  this.firstName_ = 'Panda';

  // good
  this._firstName = 'Panda';
  ```

  - [22.5](#22.5) <a name='22.5'></a> Don't save references to `this`. Use arrow functions or Function#bind.

  ```javascript
  // bad
  function foo() {
    const self = this;
    return function () {
      console.log(self);
    };
  }

  // bad
  function foo() {
    const that = this;
    return function () {
      console.log(that);
    };
  }

  // good
  function foo() {
    return () => {
      console.log(this);
    };
  }
  ```

  - [22.6](#22.6) <a name='22.6'></a> If your file exports a single class, your filename should be exactly the name of the class.

  ```javascript
  // file contents
  class CheckBox {
    // ...
  }
  export default CheckBox;

  // in some other file
  // bad
  import CheckBox from './checkBox';

  // bad
  import CheckBox from './check_box';

  // good
  import CheckBox from './CheckBox';
  ```

  - [22.7](#22.7) <a name='22.7'></a> Use camelCase when you export-default a function. Your filename should be identical to your function's name.

  ```javascript
  function makeStyleGuide() {
  }

  export default makeStyleGuide;
  ```


  - [22.8](#22.8) <a name='22.8'></a> Method names should be verbs

  ```javascript
  // bad
  function utcTime(date) {}
  function timeInUtc(date) {}

  // good
  function getTimeInUtc(date) {}
  ```

**[⬆ back to top](#table-of-contents)**


## Accessors

  - [23.1](#23.1) <a name='23.1'></a> Accessor functions for properties are not recommended, as it encourages mutable structures with internal state. It is preferred to use immutable, standard data structures instead.
  
  > NOTE: This can be made both safer and cleaner with the use of helpers in libraries like `lodash`, `ramda`, `unchanged`, etc.
  
  ```javascript
  // bad
  const dragonAge = dragon.get('age');
  
  // good
  const {age: dragonAge} = dragon;
  
  // better
  const dragonAge = get(['age'], dragon);
  ```

**[⬆ back to top](#table-of-contents)**


## Events

  - [24.1](#24.1) <a name='24.1'></a> When attaching data payloads to events (whether DOM events or something more proprietary like Backbone events), pass an object instead of a raw value. This allows a subsequent contributor to add more data to the event payload without finding and updating every handler for the event. For example, instead of:

  ```javascript
  // bad
  $(this).trigger('listingUpdated', listing.id);

  ...

  $(this).on('listingUpdated', function (e, listingId) {
    // do something with listingId
  });
  ```

  prefer:

  ```javascript
  // good
  $(this).trigger('listingUpdated', { listingId: listing.id });

  ...

  $(this).on('listingUpdated', function (e, data) {
    // do something with data.listingId
  });
  ```

  **[⬆ back to top](#table-of-contents)**


## jQuery
  
  > NOTE: The use of jQuery is generally discouraged, as modern JS standards has basically negated the normalization that jQuery provides. If you must use jQuery, these are general guidelines for working with them, however you should attempt to solve the problem using standard JS if possible.

  - [25.1](#25.1) <a name='25.1'></a> Prefix jQuery object variables with a `$`.

  ```javascript
  // bad
  const sidebar = $('.sidebar');

  // good
  const $sidebar = $('.sidebar');

  // good
  const $sidebarBtn = $('.sidebar-btn');
  ```

  - [25.2](#25.2) <a name='25.2'></a> Cache jQuery lookups.

  ```javascript
  // bad
  function setSidebar() {
    $('.sidebar').hide();

    // ...stuff...

    $('.sidebar').css({
      'background-color': 'pink'
    });
  }

  // good
  function setSidebar() {
    const $sidebar = $('.sidebar');
    $sidebar.hide();

    // ...stuff...

    $sidebar.css({
      'background-color': 'pink'
    });
  }
  ```

  - [25.3](#25.3) <a name='25.3'></a> For DOM queries use Cascading `$('.sidebar ul')` or parent > child `$('.sidebar > ul')`. [jsPerf](http://jsperf.com/jquery-find-vs-context-sel/16)

  - [25.4](#25.4) <a name='25.4'></a> Use `find` with scoped jQuery object queries.

  ```javascript
  // bad
  $('ul', '.sidebar').hide();

  // bad
  $('.sidebar').find('ul').hide();

  // good
  $('.sidebar ul').hide();

  // good
  $('.sidebar > ul').hide();

  // good
  $sidebar.find('ul').hide();
  ```

**[⬆ back to top](#table-of-contents)**

## Testing

  - [26.1](#26.1) <a name="26.1"></a> **Yup.**

  ```javascript
  function () {
    return true;
  }
  ```

  - [26.2](#26.2) <a name="26.2"></a> **No, but seriously**:
   - Whichever testing framework you use, you should be writing tests!
   - Strive to write many small pure functions, and minimize where mutations occur.
   - Be cautious about stubs and mocks - they can make your tests more brittle.
   - 100% test coverage is a good goal to strive for, even if it's not always practical to reach it.
   - If you touch code that is untested, then write tests for it. If the scope of the code touched is too large to test alone, at minimum test your changes.
   - Whenever you fix a bug, _write a regression test_. A bug fixed without a regression test is almost certainly going to break again in the future.

**[⬆ back to top](#table-of-contents)**

## Contributors

  - [View Contributors](https://github.com/rapid7/javascript-style-guide/graphs/contributors)


## License

(The MIT License)

Copyright (c) 2015 Rapid7

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

**[⬆ back to top](#table-of-contents)**

# };
