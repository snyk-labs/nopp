# nopp

`NoPP` (No Prototype Pollution) – tiny helper to protect against Prototype
Pollution vulnerabilities in your application, regardless if they introduced in
your own code or by 3rd-party code.

## How this package works?

By calling [Object.freeze](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/freeze)
for some built-in JavaScript objects.

> The `Object.freeze()` method freezes an object. A frozen object can no longer be changed; freezing an object prevents new properties from being added to it, existing properties from being removed, prevents changing the enumerability, configurability, or writability of existing properties, and prevents the values of existing properties from being changed.

We believe that there are legitimate cases of prototype changes, but they should
happen only during the initialization step. Hence, we recommend including
this package as the last one in your application code.

## Why should you use this package?

Prototype Pollution vulnerabilities are about 25% of all discovered
vulnerabilities in the JS ecosystem and probably the most popular ones.

While ~25% of them are not fixable by upgrading to a newer version, this
package will protect you even in case you're using a package that contains
a Prototype Pollution vulnerability.

## What type of applications should use this package?

- CLI applications
- Web applications
- Front-end application

## You should not use this package in case

- Your code is a library (used as a dependency for other projects)
- You do modify prototypes of JavaScript built-in objects in run time

## How to use

```shell
npm install nopp
```

```javascript
// ... all your require calls ...
require('nopp');
```

or if you use mjs syntax

```javascript
// ... all your import calls ...
import 'nopp';
```

## Example

```javascript
const _ = require('lodash'); // Version 4.17.4 is vulnerable: https://security.snyk.io/vuln/npm:lodash:20180130

_.merge({}, JSON.parse('{"__proto__":{"foo":"polluted"}}'));
console.log(({}).foo); // polluted

require('nopp');

_.merge({}, JSON.parse('{"__proto__":{"bar":"polluted"}}'));
console.log(({}).bar); // undefined

```

## Edge cases

In some rare cases, attempts to exploit the Prototype Pollution vulnerability
can cause `TypeError: Cannot redefine property` or
`TypeError: Cannot assign to read only property` exception and cause DoS
vulnerability. Please make sure you have `uncaughtException` handler
implemented.

## FAQ

### Should I prefer `nopp` instead of the `--frozen-intrinsics` Node.js flag?

[`--frozen-intrinsics`](https://nodejs.org/docs/latest-v17.x/api/cli.html#--frozen-intrinsics) added in Node.js v11.12.0 and currently has experimental stability level.

The main purpose of the flag is exactly the same as of this package – to protect runtime from unintended modifications of prototypes.

We believe there are numerous reasons why you may prefer using `nopp`:

1. You control when to import the package hence when to freeze prototypes. In many cases application actually modify prototypes a bit to add some tweaks or polyfills. In such cases usage of `--frozen-intrinsics` will be not possible without significant application code refactoring. Unlike `nopp` which should be imported after all other packages and in most of the cases cause no backward compatibility issues.
2. `nopp` is also applicable for client-side applications. You may prefer to use it for consistency between backend Node.js code and client-side application code.
3. If you are a [Snyk user](https://snyk.io), we are able to detect the usage of the `nopp` package as part of your application and ignore prototype pollution vulnerabilities in your application code automatically, and help reduce the noise level of your overall security alerts.

### Is the `--disable-proto` Node.js flag enough to be protected?

No.

[`--disable-proto`](https://nodejs.org/docs/latest-v17.x/api/cli.html#--disable-protomode) added in Node.js v12.17.0. It is able to delete `__proto__` property from the runtime completely and prevent some prototype pollution attack payloads.

Unfortunately, unlike `nopp`, it doesn't protect your application against `constructor.prototype` type of payloads.
