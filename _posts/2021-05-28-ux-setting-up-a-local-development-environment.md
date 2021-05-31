---
title: "UX-Setting up a local development environment (Html5-Javascript ES6 -Scss)"
categories:
  - Ux
tags:
  - Html5
  - Javascript ES6
  - Scss
  - Webpack
  - Babel
---

Let's look how to setup a local development environment to make things easier.

## Way 1

### Tools

- npm
- webpack
- babel
- vscode

### Steps

- Init a project

  The project structure looks like this:

```bash
+
|+src
|+public
 |-index.html
|-package.json
|-webpack.config.js

```

- Install required npm packages:

```bash
npm install --save-dev webpack webpack-cli webpack-dev-server html-webpack-plugin
npm install -D babel-loader @babel/core @babel/preset-env core-js
```

- Configure babel: babel.config.json
  (transpile ES6 to ES5)

```json
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "corejs": { "version": 3 },
        "useBuiltIns": "usage",
        "targets": {
          "ie": "11"
        }
      }
    ]
  ]
}
```

- Configre webpack: webpack.config.js

```javascript
var path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");

module.exports = {
  mode: "development",
  entry: ["./src/index.js"], //"./src/index.js",
  target: ["web", "es5"],
  plugins: [
    new HtmlWebpackPlugin({
      title: "Development",
      template: path.resolve(__dirname, "public/index.html"),
    }),
  ],
  output: {
    filename: "main.js",
    path: path.resolve(__dirname, "dist/js"),
    clean: true,
  },

  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /(node_modules)/,
        use: {
          loader: "babel-loader",
          options: {
            presets: [["@babel/preset-env"]],
          },
        },
      },
    ],
  },
  devServer: {
    contentBase: path.join(__dirname, "public"),
    compress: true,
    port: 9000,
  },
};
```

- Configre npm scripts: package.config:

```javascript
 "scripts": {
    "serve": "webpack serve",
  },
```

- Create your src/index.js, import your modules then run the local server:

```bash
 npm run serve
```
