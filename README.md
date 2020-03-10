# @caijs/eval

[![Build Status](https://travis-ci.com/CAI-js/eval.svg?branch=master)](https://travis-ci.com/CAI-js/eval)
[![Coverage Status](https://coveralls.io/repos/github/CAI-js/eval/badge.svg?branch=master)](https://coveralls.io/github/CAI-js/eval?branch=master)
[![NPM version](https://img.shields.io/npm/v/@caijs/eval.svg?style=flat)](https://www.npmjs.com/package/@caijs/eval)
[![NPM downloads](https://img.shields.io/npm/dm/@caijs/eval.svg?style=flat)](https://www.npmjs.com/package/@caijs/eval)

@caijs/eval is like eval, but executed with a context.
It is intended for use in build scripts and code transformations, doing some evaluation at build time—it is NOT suitable for handling arbitrary untrusted user input. Malicious user input can execute arbitrary code.

## Installation

In your project folder run:

```bash
$ npm install @caijs/eval
```

## Examples of use

You can evaluate expressions

```javascript
const { evaluate } = require('@caijs/eval');

const src = '[1, 2, 3].map(function(x) { return x * 2 })';
const result = evaluate(src);
console.log(result); // [2, 4, 6]
```

You can add a context

```javascript
const { evaluate } = require('@caijs/eval');

const src = '[1, 2, 3].map(function(x) { return x * n })';
const context = { n: 3 };
const result = evaluate(src, context);
console.log(result); // [3, 6, 9]
```

You can also declare functions in the context:

```javascript
const { evaluate } = require('@caijs/eval');

const src = '[1,2,3+4*10+n,foo(3+5),obj[""+"x"].y]';
const context = {
  n: 6,
  foo(x) {
    return x * 100;
  },
  obj: { x: { y: 555 } },
};
const result = evaluate(src, context);
console.log(result); // [ 1, 2, 49, 800, 555 ]
```

You can use a "this" if you define it in the context:

```javascript
const { evaluate } = require('./src/evaluator');

const src = 'this.a + this.b + n';
const context = {
  this: {
    a: 1,
    b: 2
  },
  n: 6,
};
const result = evaluate(src, context);
console.log(result);
```

You can modify context variables:

```javascript
const { evaluate } = require('@caijs/eval');

const src = 'this.a += this.b; this.a';
const context = {
  this: {
    a: 1,
    b: 2,
  },
  n: 6,
};
const result = evaluate(src, context);
console.log(result); // 3
console.log(context); // { this: { a: 3, b: 2 }, n: 6 }
```

If you don't want the code to modify your context, pass a "false" as the last parameter:

```javascript
const { evaluate } = require('@caijs/eval');

const src = 'this.a += this.b; this.a';
const context = {
  this: {
    a: 1,
    b: 2,
  },
  n: 6,
};
const result = evaluate(src, context, false);
console.log(result); // 1
console.log(context); // { this: { a: 1, b: 2 }, n: 6 }
```

You can even create your own context variables:

```javascript
const { evaluate } = require('@caijs/eval');

const src = 'z = this.a + this.b; z';
const context = {
  this: {
    a: 1,
    b: 2,
  },
  n: 6,
};
const result = evaluate(src, context);
console.log(result); // 3
console.log(context); // { this: { a: 1, b: 2 }, n: 6, z: 3 }
```
