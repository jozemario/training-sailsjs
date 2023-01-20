```
# install sails globally
npm install sails -g

# create test project using sails
# when prompted select option 1/2 depending
# on your project requirements
sails new test-project

# once sails installed go to test project
cd test-project

# run sails app
sails lift​

# adding react and react dom
npm install --save react react-dom

# install babel presets for react
npm add -D @babel/core babel-loader @babel/preset-env @babel/preset-react

# add webpack plugins we need
npm install --save-dev webpack webpack-cli
npm install --save-dev html-webpack-plugin

# install other dependencies
npm install --save-dev rimraf
npm install npm-run-all --save-dev

# install webpack server
npm install --save-dev webpack-dev-server​

fix
---------
After installing the newer packages, remember to update your .babelrc presets from react to @babel/preset-react. Here is a simple example

{
    "presets": [
        "@babel/preset-env",
        "@babel/preset-react"
    ]
}
---------

Add index.html file
Once we installed all the necessary dependencies we need to create entry point for our app. Let's create index.html file in assets/src folder.

<!DOCTYPE html>
<html>
  <head>
    <meta charset="UTF-8">
    <title>Sails React App</title>
  </head>
  <body>
    <div id="root"></div>
  </body>
</html>​
 Add index.js file
Next, we need to create a sample index.js file containing our react code. Let's create index.js file in assets/src folder that prints header tag in our html page.

import React from 'react';
import ReactDOM from 'react-dom';
const App = () => {
  return (
    <h1>Sample React and Sails App</h1>
  );
};

ReactDOM.render(<App />, document.getElementById('root'));​
 Linking react with sails using webpack
In order to link react with sails js we need to link through webpack.

Webpack would allow us to compile jsx or react code. Let's create webpack.config.js file in our project root directory with following contents.

const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: {
    entry: './assets/src/index.js'
  },
  output: {
    path: __dirname + '/.tmp/public',
    filename: 'bundle.js'
  },
  module: {
    rules: [
      {
        use: 'babel-loader',
        test: /\.js$/,
        exclude: /node_modules/
      },
      {
        use: ['style-loader', 'css-loader'],
        test: /\.css$/
      }
    ]
  },

  plugins: [
    new HtmlWebpackPlugin({
      template: 'assets/src/index.html'
    })
  ]
};​
Let's understand how webpack will handle our project:

we define entry point for our react app using entry block
a bundled file will be placed in .tmp/public folder containing bundled js file with react code
HtmlWebpackPlugin will take our index.html file and would inject script tag containing bundled js file
babel will transpile our es6 code to proper js code
style and css loader will be used to inject css and javascript files into our project
 Modify default layout file
Now, we need to modify our parent layout file provided by sails.

Open  views/layouts/layouts.ejs file and modify its contain as below:

<%- body %>​
Now, we need to modify initial home page so that it gets html from webpack generated html. Open views/pages/homepage.ejs and modify as below:

<%- partial('../../.tmp/public/index.html') %>​


 Getting our app to run using webpack
Finally, we need to modify our package.json file replace script tag with following contents.

"scripts": {
    "start": "npm-run-all --parallel open:client lift",
    "start:debug": "npm-run-all --parallel open:client debug",
    "open:client": "webpack-dev-server --mode development --open",
    "build": "npm run build:prod",
    "build:dev": "webpack --mode development",
    "build:prod": "webpack --mode production",
    "clean": "rimraf .tmp && mkdirp .tmp/public",
    "lift": "sails lift",
    "debug": "node --inspect app.js"
  }​
Now, that we use webpack instead of grunt in our sails project let's disable hooks for grunt.

Open .sailsrc file and add following line of code to disable grunt support.

"hooks": {
    "grunt": false
}​
 Running our server
To start our development server run following command:

# to build assets
npm run build

# to run our server
npm run start

# running app using lift 
# similar as sails lift
npm run lift​

```