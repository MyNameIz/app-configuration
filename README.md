Introduction
------------

Parsed-config package is a fork of [config](https://www.npmjs.com/package/config) package, but with addition of non-string environment values support (described below).
The library is almost the same as config package, but it parses values from environment variables by default. This feature makes possible geting environment variables already parsed to their types and grouped into configuration objects.

Parser method was gotten from [dotenv-parse-variables](https://www.npmjs.com/package/dotenv-parse-variables) package.

Documentation
--------
Full version of documentation is available in the [origin](https://github.com/lorenwest/node-config/wiki).

## Parsing feature explained

Imagine you have a configuration file at `.env` with the following:

```bash
FOO=bar
BAZ=2
BEEP=false
BOOP=some,thing,that,goes,wow
# note how we use an asterisk here to turn off the parsing for this variable
BLEEP=false*
# note how we use an asterisk in the array to turn off parsing for an array key value
PING=ping,true*,2,100
# note a string between bacticks won't be parsed
PONG=`some,thing,that,goes,wow`
```

After using this plugin, the environment variables are parsed to their proper types.

To test it out, simply log the returned object in your console:

```js
console.log(env);
```

And you'll see that it outputs the properly parsed variable types:

```js
{
  // String
  FOO: 'bar',
  // Number
  BAZ: 2,
  // Boolean
  BEEP: false,
  // Array
  BOOP: [ 'some', 'thing', 'that', 'goes', 'wow' ],
  // NOTE: this was not parsed due to the * asterisk override above
  BLEEP: 'false',
  // NOTE: only the "true*" above was opted out through the use of an asterisk
  PING: [ 'ping', 'true', 2, 100 ],
  // NOTE: this was not parsed because the string was between bacticks
  PONG: 'some,thing,that,goes,wow'
}
```

If your configuration line ends in `*` it will not be parsed by this package, which allows you to keep values as the `String` variable type if needed. Also when you encapsulate a value between bacticks e.g. \`value\`, the value won't be parsed and it will return as a `String` variable. This can be used in situations where you for example have a `,` inside your string and it should not be parsed as an array.

Quick Start
---------------
The following examples are in JSON format, but configurations can be in other [file formats](https://github.com/lorenwest/node-config/wiki/Configuration-Files#file-formats).

**Install in your app directory, and edit the default config file.**

```shell
$ npm install config
$ mkdir config
$ vi config/default.json
```
```js
{
  // Customer module configs
  "Customer": {
    "dbConfig": {
      "host": "localhost",
      "port": 5984,
      "dbName": "customers"
    },
    "credit": {
      "initialLimit": 100,
      // Set low for development
      "initialDays": 1
    }
  }
}
```

**Edit config overrides for production deployment:**

```shell
 $ vi config/production.json
```

```json
{
  "Customer": {
    "dbConfig": {
      "host": "prod-db-server"
    },
    "credit": {
      "initialDays": 30
    }
  }
}
```

**Use configs in your code:**

```js
const config = require('config');
//...
const dbConfig = config.get('Customer.dbConfig');
db.connect(dbConfig, ...);

if (config.has('optionalFeature.detail')) {
  const detail = config.get('optionalFeature.detail');
  //...
}
```

`config.get()` will throw an exception for undefined keys to help catch typos and missing values.
Use `config.has()` to test if a configuration value is defined.

**Start your app server:**

```shell
$ export NODE_ENV=production
$ node my-app.js
```

Running in this configuration, the `port` and `dbName` elements of `dbConfig`
will come from the `default.json` file, and the `host` element will
come from the `production.json` override file.
