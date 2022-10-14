# Configuring StoryBook

Follow instructions for adding the ApolloClient Storybook Addon

{% embed url="https://storybook.js.org/addons/storybook-addon-apollo-client/" %}



Install React Testing Library:



{% code title="Terminal / Command Propt" %}
```bash
npm install -D @testing-library/react
npm install -D @storybook/testing-react
npm install -D @testing-library/user-event
npm install -D @storybook/addon-jest
npm install -D storybook-zeplin
npm install -D storybook-addon-customize-antd-theme
```
{% endcode %}

In main.js add the following (note the port number must match..)\




{% code title="Terminal / Command Propt" %}
```bash
npm install -D storybook-addon-playwright
npm i -D @storybook/addon-controls
```
{% endcode %}

{% code title="/ui/.storybook/main.js" %}
```bash
...
const path = require('path');
const webpack = require('webpack');

module.exports = {
  ..
  staticDirs: ['../public'],
  "stories": [
    "../src/**/*.stories.mdx",
    "../src/**/*.stories.@(js|jsx|ts|tsx)",
    '../node_modules/storybook-addon-customize-antd-theme/dist/esm/stories/index.js'
  ],
  "addons": [
    "@storybook/addon-links",
    "@storybook/addon-essentials",
    "storybook-addon-customize-antd-theme",
    { 
      name: "@storybook/preset-create-react-app",
      options:{ 
        craOverrides: {
          fileLoaderExcludes: ['less','css']
        }
      }
    }
  ],
  "framework": "@storybook/react",
  "core": {
    "builder": "webpack5"
  },
  "webpackFinal": async (config, { configType }) => {

    config.resolve.fallback = { ...config.resolve.fallback, 
      process: require.resolve("process/browser"),
      zlib: require.resolve("browserify-zlib"),
      stream: require.resolve("stream-browserify"),
      util: require.resolve("util"),
      buffer: require.resolve("buffer"),
      asset: require.resolve("assert"),
    };
    
    config.plugins.push(
      new webpack.ProvidePlugin({
        Buffer: ["buffer", "Buffer"],
        process: "process/browser",
      }),
    );

    config.module.rules.push(
      {
        test: /\.less$/,
        use: [
          { loader: 'style-loader' },
          { loader: 'css-loader' },
          {
            loader: 'less-loader',
            options: { 
              
              lessOptions: { 
                modifyVars: {
                  hack: `true; @import "${path.resolve(
                    __dirname,
                    "../src/styles",
                    "theme.less"
                  )}";`
                },
                javascriptEnabled: true } 
              },
          }
        ],
        include: path.resolve(__dirname, '../src/'),
      }
    );

    // Return the altered config
    return config;
  }
  ...
};
```
{% endcode %}

middleware.js

{% code title="/ui/.storybook/middleware.js" %}
```bash
const middleware = require('storybook-addon-playwright/middleware');
module.exports = middleware;
```
{% endcode %}

