[![Build Status](https://travis-ci.org/seanstrom/webdriverio-selenium-harness.svg?branch=master)](https://travis-ci.org/seanstrom/webdriverio-selenium-harness)
[![codecov.io](http://codecov.io/github/seanstrom/webdriverio-selenium-harness/coverage.svg?branch=master)](http://codecov.io/github/seanstrom/webdriverio-selenium-harness?branch=master)
[![Code Climate](https://codeclimate.com/github/seanstrom/webdriverio-selenium-harness/badges/gpa.svg)](https://codeclimate.com/github/seanstrom/webdriverio-selenium-harness)
[![Codacy Badge](https://api.codacy.com/project/badge/a81aa1a83b564898ba0c117c645e99d2)](https://www.codacy.com/app/seanstrom/webdriverio-selenium-harness)

# WebdriverIO & Selenium Test Harness

## Why
This test harness allows you to setup bootstrap your feature/intergration tests with **WebdriverIO** and **Selenium**. It makes setup and teardown trivial, especially for starting and stopping a local selenium server.

## Installation

First you want to install the `peerDependencies` and then the package itself.

```shell
npm i -D webdriverio
npm i -D selenium-standalone
npm i -D webdriverio-selenium-harness
```

## Usage

You can use the test harness in your test framework of choice.  
Just require the module and call `setup` and `teardown` in your `before` and `after` hooks.

#### Example

```javascript
// Mocha

var harness = require('webdriverio-selenium-harness')
var options = {}

describe('Feature', function () {
  before(function () {
    var self = this
    self.harnessState = harness.setup(options)
    return harnessState.then(function (state) {
      self.browser = state.browser
    })
  })
  
  after(function () {
    var self = this
    return self.harnessState.then(harness.teardown)
  })
  
  // Tests ...
})
```

**Caveats**  

* Make sure you have your Browsers or **PhantomJS** installed.
* Install Selenium by running `./node_modules/.bin/selenium-standalone install`.  
  Reference: https://www.npmjs.com/package/selenium-standalone
* Make sure if your tests ever crash that, `teardown` always run.
  Otherwise you may have processes left on.

## API

### Setup
`setup` takes an object of options for both **webdriverio** and **selenium**.  
`setup` returns a **Promise** of harness state which contains the **webdriverio** client and the **selenium** process.

Options are namespaced by **webdriverio** and **selenium**.  
Each of the options will reflect the APIs provided in these documents.

#### References

##### WebdriverIO
[init](http://webdriver.io/api/protocol/init.html)  
[remote](http://webdriver.io/guide/getstarted/configuration.html)  

##### Selenium
[selenium](https://www.npmjs.com/package/selenium-standalone)

##### Custom

* **remoteSelenium**  
Use if you want to the test harness to point to a remote selenium server.

#### Examples

##### Traditional

An example of how to use on your local.

```javascript
var options = {
  custom: {},
  selenium: {
    seleniumArgs: []
  },
  webdriverio: {
    init: {},
    remote: {
      desiredCapabilities: {
        browserName: 'phantomjs' // chrome, firefox
      }
    }
  },
}

harness.setup(options).then(function (state) {
  var browser = state.browser
  var selenium = state.selenium
})
```

##### Continuous Integration

An example of how to use in CI.  

**Note**  
The Selenium instance in the harness state is a stub process when using `remoteSelenium`.  
Make sure you have a Selenium server running when using `remoteSelenium` custom option.

```javascript
var isCI = !!process.env.CI

var options = {
  custom: { remoteSelenium: isCI },
  selenium: {
    seleniumArgs: []
  },
  webdriverio: {
    init: {},
    remote: {
      desiredCapabilities: {
        browserName: 'phantomjs' // chrome, firefox
      }
    }
  },
}

harness.setup(options).then(function (state) {
  var browser = state.browser
  var selenium = state.selenium // stub process - not real.
})
```


### Teardown

`teardown` takes harness state and then closes the **webdriverio** client and **selenium** process.  
`teardown` will return a **Promise** of undefined.

#### Example

```javascript
harness.teardown(state).then(function () {
  // etc
})
```

## Peer Dependencies

* [webdriveio](https://www.npmjs.com/package/webdriverio)
* [selenium-standalone](https://www.npmjs.com/package/selenium-standalone)
