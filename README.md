# nopp

`NoPP` (No Prototype Pollution) – tiny helper to protect against Prototype Pollution vulnerabilities in your application regardless if they introduced in your own code or in 3rd-party code.

## Why should you use this package?

Prototype Pollution vulnerabilities are about 25% of all discovered vulnerabilities in the JS ecosystem and probably the most popular ones.
While ~25% of them are not fixable by upgrading to a newer version, this package will protect you even in case you're using a package that contains a Prototype Pollution vulnerability 

## What type of applications should use this package?

- CLI applications
- Web applications
- Front-end application

## You should not use this package in case

- Your code is a library
- You do <Kiril to add> during run time

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

In some rare cases attempts to exploit the Prototype Pollution vulnerability can cause `TypeError: Cannot redefine property` exception and cause DoS vulnerability. Please make sure you have `uncaughtException` handler implemented.
