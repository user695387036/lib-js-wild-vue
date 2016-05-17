# WildVue [![CircleCI](https://circleci.com/gh/WildDogTeam/lib-js-wild-vue.svg?style=svg)](https://circleci.com/gh/WildDogTeam/lib-js-wild-vue) [![npm package](https://img.shields.io/npm/v/wildvue.svg)](https://www.npmjs.com/package/wildvue) [![coverage](https://img.shields.io/codecov/c/github/vuejs/wildvue.svg)](https://codecov.io/github/vuejs/wildvue)

> Vue.js binding for Wilddog.

## Installation

1. If included as global `<script>`: will install automatically if global `Vue` is present.

  ``` html
  <head>
    <!-- Vue -->
    <script src="https://cdn.jsdelivr.net/vue/latest/vue.js"></script>
    <!-- Wilddog -->
    <script src="https://cdn.wilddog.com/sdk/js/current/wilddog.js"></script>
    <!-- WildVue -->
    <script src="https://cdn.wilddog.com/wildvue/1.0.1/wildvue.min.js"></script>
  </head>
  ```

2. In module environments, e.g CommonJS:

  ``` bash
  npm install vue wilddog wildvue --save
  ```

  ``` js
  var Vue = require('vue')
  var WildVue = require('wildvue')
  var Wilddog = require('wilddog')

  // explicit installation required in module environments
  Vue.use(WildVue)
  ```

## Usage

``` js
var vm = new Vue({
  el: '#demo',
  wilddog: {
    // simple syntax, bind as an array by default
    anArray: new Wilddog('url/to/my/collection'),
    // can also bind to a query
    // anArray: new Wilddog('url/to/my/collection').limitToLast(25)
    // full syntax
    anObject: {
      source: new Wilddog('url/to/my/object'),
      // optionally bind as an object
      asObject: true,
      // optionally provide the cancelCallback
      cancelCallback: function () {}
    }
  }
})
```

``` html
<div id="demo">
  <pre>{{ anObject | json }}</pre>
  <ul>
    <li v-for="item in items">{{ item.text }}</li>
  </ul>
</div>
```

The above will bind the Vue instance's `anObject` and `anArray` to the respective Wilddog data sources. In addition, the instance also gets the `$wilddogRefs` property, which holds the refs for each binding:

``` js
// add an item to the array
vm.$wilddogRefs.anArray.push({
  text: 'hello'
})
```

Alternatively, you can also manually bind to a Wilddog ref with the `$bindAsObject` or `$bindAsArray` instance methods:

``` js
vm.$bindAsObject('user', myWilddogRef.child('user'))
vm.$bindAsArray('items', myWilddogRef.child('items').limitToLast(25))
```

## Data Normalization

### Array Bindings

Each record in the bound array will contain a `.key` property which specifies the key where the record is stored. So if you have data at `/items/-Jtjl482BaXBCI7brMT8/`, the record for that data will have a `.key` of `"-Jtjl482BaXBCI7brMT8"`.

If an individual record's value in the database is a primitive (boolean, string, or number), the value will be stored in the `.value` property. If the individual record's value is an object, each of the object's properties will be stored as properties of the bound record. As an example, let's assume the `/items/` node you bind to contains the following data:

``` json
{
  "items": {
    "-Jtjl482BaXBCI7brMT8": 100,
    "-Jtjl6tmqjNeAnQvyD4l": {
      "first": "fred",
      "last": "Flintstone"
    },
    "-JtjlAXoQ3VAoNiJcka9": "foo"
  }
}
```

The resulting bound array stored in `vm.items` will be:

``` json
[
  {
    ".key": "-Jtjl482BaXBCI7brMT8",
    ".value": 100
  },
  {
    ".key": "-Jtjl6tmqjNeAnQvyD4l",
    "first": "Fred",
    "last": "Flintstone"
  },
  {
    ".key": "-JtjlAXoQ3VAoNiJcka9",
    ".value": "foo"
  }
]
```

## Contributing

Clone the repo, then:

```bash
$ npm install    # install dependencies
$ npm test       # run test suite with coverage report
$ npm run dev    # watch and build dist/wildvue.js
$ npm run build  # build dist/wildvue.js and wildvue.min.js
```
