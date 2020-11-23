./src/index.js

```javascript
import React from "react";
import ReactDOM from "react-dom";

class App extends React.Component {
  render() {
    return <div>Hello World</div>;
  }
}

ReactDOM.render(<App />, document.getElementById("root"));
```

./src/index.html

```javascript
<!DOCTYPE html>
<html>
  <head>
    <title>react app</title>
  </head>
  <body>
    <div id="root"></div>
  </body>
</html>
```

.babelrc

```javascript
{
  "presets": ["@babel/preset-env", "@babel/preset-react"]
}
```

webpack.config.js

```javascript
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");

module.exports = {
  entry: "./src/index.js",
  output: {
    path: path.resolve(__dirname, "dist"),
    filename: "bundle.js",
  },
  mode: process.env.NODE_ENV,
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: "babel-loader",
        },
      },
    ],
  },
  plugins: [new HtmlWebpackPlugin({ template: "./src/index.html" })],
};
```

package.json

```javascript
"scripts": {
  "start": "webpack serve --mode development",
  "build": "webpack"
},
```

webpack.config.js

```javascript
// webpack-dev-server setting
devServer: {
  hot: true,
  open: true,
},
```
