---
title: "Use Bootstrap Table in Webpack"
date: 2019-09-22T22:42:40+08:00
categories: [BootstrapTable]
---

Because I saw the problem saying [using the latest version of Bootstrap Table in Webpack will fail](https://github.com/wenzhixin/bootstrap-table/issues/4597), I tried how to use Bootstrap Table in Webpack from scratch.

First, according to the [Getting Started](https://webpack.js.org/guides/getting-started/) documentation of Webpack, we created a test project `webpack-bootstrap-table`, here we use `webpack-dev-server` to start the service:

```bash
mkdir webpack-bootstrap-table
cd webpack-bootstrap-table
yarn init -y
yarn add webpack webpack-cli webpack-dev-server -D
```

Next, add `bootstrap-table` and the required dependencies `jquery` and `bootstrap`:

```bash
yarn add jquery bootstrap popper.js bootstrap-table
```

Since we need to import the `css` file, we need to use the [`css-loader`](https://webpack.js.org/loaders/css-loader/) module:

```bash
yarn add style-loader css-loader -D
```

Add directories and files:

```bash
  webpack-bootstrap-table
  |- package.json
+ |- dist
+   |- index.html
+ |- /src
+   |- index.js
+ |- webpack.config.js
```

**dist/index.html：**

```html
<!doctype html>
<html>
  <head>
    <title>Webpack Bootstrap Table</title>
  </head>
  <body>
    <table id="table"></table>
    <script src="main.js"></script>
  </body>
</html>
```

**src/index.js：**

```js
import 'bootstrap/dist/css/bootstrap.min.css'
import 'bootstrap-table/dist/bootstrap-table.min.css'

import $ from 'jquery'
import 'bootstrap'
import 'bootstrap-table' // dist/bootstrap-table.min.js by default

$('#table').bootstrapTable({
  search: true,
  showColumns: true,
  columns: [{
    field: 'id',
    title: 'Item ID'
  }, {
    field: 'name',
    title: 'Item Name'
  }, {
    field: 'price',
    title: 'Item Price'
  }],
  data: [{
    id: 1,
    name: 'Item 1',
    price: '$1'
  }, {
    id: 2,
    name: 'Item 2',
    price: '$2'
  }]
})
```

**webpack.config.js：**

```js
const path = require('path')

module.exports = {
  entry: './src/index.js',
  output: {
    filename: 'main.js',
    path: path.resolve(__dirname, 'dist')
  },
  module: {
    rules: [
      {
        test: /\.css$/i,
        use: ['style-loader', 'css-loader'],
      },
    ],
  },
  devServer: {
    contentBase: path.join(__dirname, 'dist')
  }
}
```

And then add `build` and `dev` scripts in the **package.json** file:

```json
{
  "name": "webpack-bootstrap-table",
  "version": "1.0.0",
  "main": "index.js",
  "license": "MIT",
  "devDependencies": {
    "css-loader": "^3.2.0",
    "style-loader": "^1.0.0",
    "webpack": "^4.40.2",
    "webpack-cli": "^3.3.9",
    "webpack-dev-server": "^3.8.1"
  },
  "dependencies": {
    "bootstrap": "^4.3.1",
    "bootstrap-table": "^1.15.4",
    "jquery": "^3.4.1",
    "popper.js": "^1.15.0"
  },
  "scripts": {
    "build": "webpack",
    "dev": "webpack-dev-server --inline --progress"
  }
}
```

Run the server (or `yarn build`):

```
yarn dev
```

Open the browser and it will work.
