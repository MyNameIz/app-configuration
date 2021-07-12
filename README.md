Introduction
------------

Parsed-config package is a fork of [config](https://www.npmjs.com/package/config) package.
The library is almost the same as config package, but it parses values from environment variables by default. This feature makes possible geting environment variables already parsed to their types and grouped into configuration objects.

Documentation
--------
Full version of documentation is available in the [origin](https://github.com/lorenwest/node-config/wiki).

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
