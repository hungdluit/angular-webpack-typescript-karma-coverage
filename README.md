# angular-webpack-typescript-karma-coverage
An exemplary project using Angular 1.x, Webpack, TypeScript and Karma (with Coverage).

![Code coverage report using TypeScript files as the analyzed source](https://raw.githubusercontent.com/zbicin/angular-webpack-typescript-karma-coverage/master/coverage.png)

## How to run?
0. `npm install -g typings` (if you don't have it installed globally yet).
1. Clone the repository (i.e. `git clone https://github.com/zbicin/angular-webpack-typescript-karma-coverage.git && cd angular-webpack-typescript-karma-coverage`).
2. `npm install`.
3. `typings install`.
4. `npm start` to see the simple app running.
5. `npm test` to run karma and generate coverage tests. The latter are located in the `coverage/` directory.

## The most interesting parts:
* `webpack.config.js` with `devtool` set to `inline-source-map` and an appropriate entry point:
```javascript
// webpack.config.js
module.exports = {
    entry: 'index.ts',
    devtool: 'inline-source-map',

    // ...
}
```
* `webpack.config.karma.js` - slightly modified webpack config which is used exclusively by karma:
```javascript
// webpack.config.karma.js
var webpackConfig = require('./webpack.config.js');

webpackConfig.entry = {};
webpackConfig.module.postLoaders = [
    {
        test: /\.ts$/,
        loader: 'istanbul-instrumenter-loader',
        exclude: [
            'node_modules',
            /\.spec\.ts$/
        ]
    }
]

module.exports = webpackConfig;
```
* `karma.conf.js` using `source-map-support` framework and generating a json coverage report:
```javascript
// karma.conf.js
var webpackKarmaConfig = require('./webpack.config.karma.js');

module.exports = function (config) {
  config.set({
    frameworks: ['jasmine', 'source-map-support'],

    files: [
      './index.ts',
      './node_modules/angular-mocks/angular-mocks.js',
      './src/**/*.spec.ts'
    ],

    preprocessors: {
      './index.ts': ['webpack'],
      './src/**/*.spec.ts': ['webpack']
    },

    reporters: ['progress', 'coverage'],

    webpack: webpackKarmaConfig,

    coverageReporter: {
      reporters: [
        {
          type: 'html',
          dir: 'coverage/html-js',
          subdir: '.'
        },
        {
          type: 'json',
          dir: 'coverage/json',
          subdir: '.'
        }
      ]
    }

    // ...
  })
}
```
* `tsconfig.json` with `inlineSourceMap` set to `true` and `sourceMap` set to `false`:
```javascript
// tsconfig.json
{
    "compilerOptions": {
        "inlineSourceMap": true,
        "sourceMap": false
    },

    // ...
}

```
* `remap-istanbul -i coverage/json/coverage-final.json -o coverage/html-ts -t html` being run by `npm test`. It generates an HTML report containing original TypeScript source files, basing on the json report generated by karma-coverage:
```javascript
// package.json
{
  "scripts": {
    "start": "http-server -o",
    "test": "npm run karma && npm run ts-coverage-remap",
    "karma": "karma start",
    "ts-coverage-remap": "remap-istanbul -i coverage/json/coverage-final.json -o coverage/html-ts -t html"
  },

  // ...
}
```

## License
[WTFPL](http://www.wtfpl.net/)


