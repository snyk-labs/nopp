# nopp

`NoPP` (No Prototype Pollution) – tiny helper to prevent Prototype Pollution vulnerabilities in your app regardless if they introduced in your own code or in 3rd-party code.

## When to use?

You need to use this package in:

- CLI applications.
- Web applications.
- Front-end application.

You should not use this package if you implement a package because it can cause a side effects.

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

## Why?

The Prototype Pollution vulnerabilities are about 25% of all discovered vulnerabilities in NPM ecosystem and probably the most popular ones.

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

In some rare cases attempts to exploit the Prototype Pollution vulnerability can cause `TypeError: Cannot redefine property` exception and cause DoS vulnerability. Please make sure you have `uncaughtException` handler implemented.
