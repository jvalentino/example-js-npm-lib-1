# Example JavaScript Library Part 1

The purpose of this project is to be a basic JavaScript library, that is built using NPM with the intention of adding other software utilities step by step.

Prerequisites

- Git Setup: https://github.com/jvalentino/setup-git

# How does NPM work?

## Metadata

package.json

```json
{
  "name": "metaverse", // The name of your project
  "version": "0.92.12", // The version of your project
  "description": "The Metaverse virtual reality. The final outcome of all virtual worlds, augmented reality, and the Internet.", // The description of your project
  "main": "index.js"
  "license": "MIT" // The license of your project
}
```

- https://nodesource.com/blog/an-absolute-beginners-guide-to-using-npm/



## Dependency Management

package.json

```json
{
  "name": "metaverse",
  "version": "0.92.12",
  "description": "The Metaverse virtual reality. The final outcome of all virtual worlds, augmented reality, and the Internet.",
  "main": "index.js"
  "license": "MIT",
  "devDependencies": {
    "mocha": "~3.1",
    "native-hello-world": "^1.0.0",
    "should": "~3.3",
    "sinon": "~1.9"
  },
  "dependencies": {
    "fill-keys": "^1.0.2",
    "module-not-found-error": "^1.0.0",
    "resolve": "~1.1.7"
  }
}
```

- https://nodesource.com/blog/an-absolute-beginners-guide-to-using-npm/



![](https://git.sr.ht/~jplatte/cargo-depgraph/blob/HEAD/graph_all.png)

- https://git.sr.ht/~jplatte/cargo-depgraph/

## Scripting

package.json

```json
{  
"scripts": {
    "dostuff": "echo doing stuff"
  }
}
```

You can then use this as a shortcut via npm:

```bash
 $ npm run dostuff

> example-js-npm-lib-1@1.0.0 dostuff
> echo doing stuff

doing stuff
```

# Understanding Rollup

> Rollup is a module bundler for JavaScript which compiles small pieces of code into something larger and more complex, such as a library or application. It uses the standardized ES module format for code, instead of previous idiosyncratic solutions such as CommonJS and AMD. ES modules let you freely and seamlessly combine the most useful individual functions from your favorite libraries. Rollup can optimize ES modules for faster native loading in modern browsers, or output a legacy module format allowing ES module workflows today.

- https://www.npmjs.com/package/rollup

rollup.config.js

```json
import resolve from "@rollup/plugin-node-resolve";
import commonjs from "@rollup/plugin-commonjs";
import babel from "@rollup/plugin-babel";
import pkg from "./package.json";

export default [
  {
    input: "src/index.js", // your entry point
    output: {
      name: "example-js-npm-lib-1", // package name
      file: pkg.browser,
      format: "umd",
    },
    plugins: [
      resolve(),
      commonjs(),
      babel({
        exclude: ["node_modules/**"],
      }),
    ],
  },
  {
    input: "src/index.js", // your entry point
    output: [
      { file: pkg.main, format: "cjs" },
      { file: pkg.module, format: "es" },
    ],
    plugins: [
      babel({
        exclude: ["node_modules/**"],
      }),
    ],
  },
];
```

The result is that you get three versions of your library:

## (1) CommonJS

dist/example-js-npm-lib-1.cjs.js

```javascript
'use strict';

var fs = require("fs");
function hello(name) {
  var message = "Hi ".concat(name, "!");
  fs.writeFileSync("hello.txt", message);
  return message;
}
function another() {
  return 0;
}
module.exports = {
  hello: hello,
  another: another
};
```

## (2) ES Module

dist/example-js-npm-lib-1.esm.js

```javascript
var fs = require("fs");
function hello(name) {
  var message = "Hi ".concat(name, "!");
  fs.writeFileSync("hello.txt", message);
  return message;
}
function another() {
  return 0;
}
module.exports = {
  hello: hello,
  another: another
};
```

## (3) UMD

> Basically, a UMD module is a JavaScript file that tries to guess at runtime which module system it’s being used in, and then it acts as that kind of module. So you can load the file in a plain `<script>`, or you can load it from an AMD module loader, or you can load it as a Node.js module, and it will always do something sensible.

- https://jameshfisher.com/2020/10/04/what-are-umd-modules/

dist/example-js-npm-lib-1.umd.js

```javascript
(function (global, factory) {
  typeof exports === 'object' && typeof module !== 'undefined' ? module.exports = factory(require('fs')) :
  typeof define === 'function' && define.amd ? define(['fs'], factory) :
  (global = typeof globalThis !== 'undefined' ? globalThis : global || self, global["example-js-npm-lib-1"] = factory(global.require$$0));
})(this, (function (require$$0) { 'use strict';

  function _interopDefaultLegacy (e) { return e && typeof e === 'object' && 'default' in e ? e : { 'default': e }; }

  var require$$0__default = /*#__PURE__*/_interopDefaultLegacy(require$$0);

  var fs = require$$0__default["default"];
  function hello(name) {
    var message = "Hi ".concat(name, "!");
    fs.writeFileSync("hello.txt", message);
    return message;
  }
  function another() {
    return 0;
  }
  var src = {
    hello: hello,
    another: another
  };

  return src;

}));

```



# Building It

FIrst you have to install the dependencies, which can be done by running:

```bash
npm install
```

Then you can run this command, to create the distribution packaging:

```bash
$ npm run build

> example-js-npm-lib-1@1.0.0 build
> rollup -c

src/index.js → dist/example-js-npm-lib-1.umd.js...
babelHelpers: 'bundled' option was used by default. It is recommended to configure this option explicitly, read more here: https://github.com/rollup/plugins/tree/master/packages/babel#babelhelpers
(!) Missing global variable name
Use output.globals to specify browser global variable names corresponding to external modules
fs (guessing 'require$$0')
created dist/example-js-npm-lib-1.umd.js in 222ms

src/index.js → dist/example-js-npm-lib-1.cjs.js, dist/example-js-npm-lib-1.esm.js...
babelHelpers: 'bundled' option was used by default. It is recommended to configure this option explicitly, read more here: https://github.com/rollup/plugins/tree/master/packages/babel#babelhelpers
created dist/example-js-npm-lib-1.cjs.js, dist/example-js-npm-lib-1.esm.js in 14ms
```

The result is the several distribution packages:

1. dist/example-js-npm-lib-1.cjs.js
2. dist/example-js-npm-lib-1.esm.js
3. dist/example-js-npm-lib-1.umd.js

# Manually Testing it

To verify that we can use the library (in ESM format), I create the following code at manual-testing/manual-test.js:

```javascript
const lib = require("../dist/example-js-npm-lib-1.esm");

const result = lib.hello("John");
console.log(result);
```

You can run this directly using Node, and see that it is using the build ESM library:

```bash
$ node manual-test.js
Hi John!
```

