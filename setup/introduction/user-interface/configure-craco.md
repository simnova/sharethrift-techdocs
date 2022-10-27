# Configure Craco

craco config

{% code title="ui/craco.config.js" %}
```
const CracoLessPlugin = require('craco-less');
const webpack = require('webpack');
const path = require('path');
module.exports = {
  
  webpack: {
    configure: {
      resolve: {
        fallback: {
          process: require.resolve("process/browser"),
          zlib: require.resolve("browserify-zlib"),
          stream: require.resolve("stream-browserify"),
          util: require.resolve("util"),
          buffer: require.resolve("buffer"),
          asset: require.resolve("assert"),
        }
      },
      plugins: [
        new webpack.ProvidePlugin({
          Buffer: ["buffer", "Buffer"],
          process: "process/browser.js",
        }),
      ]
    }
  },
  plugins: [
    {
      plugin: CracoLessPlugin,
      options: {
        lessLoaderOptions: {
          lessOptions: {
            modifyVars: {             
              hack: `true; @import "${path.resolve(
                __dirname,
                "./src/styles",
                "theme.less"
              )}";`
            },
            javascriptEnabled: true,
          },
        },
        postcssLoaderOptions:
        {
            plugins: [
                require('tailwindcss')('./tailwind.config.js'),
                require('autoprefixer')
            ]
        },
        /*
        overrideWebpackConfig: ({ webpackConfig, cracoConfig, pluginOptions, context: { env, paths } }) => { 
          webpackConfig.resolve.fallback = { ...webpackConfig.resolve.fallback, 
            process: require.resolve("process/browser"),
            zlib: require.resolve("browserify-zlib"),
            stream: require.resolve("stream-browserify"),
            util: require.resolve("util"),
            buffer: require.resolve("buffer"),
            asset: require.resolve("assert"),
          };
          webpackConfig.plugins.push(
            new webpack.ProvidePlugin({
              Buffer: ["buffer", "Buffer"],
              process: "process/browser",
            }),
          );
          return webpackConfig; 
        },
        */
      },
    },
  ],
}
```
{% endcode %}

Update package.json to use craco instead of react-scripts

(update storybook port number as per your settings)

{% code title="ui/package.json" %}
```
...
  "scripts": {
    "start": "craco start",
    "build": "craco build",
    "test": "craco test",
    "eject": "craco eject",
    "storybook": "start-storybook -p 6006 -s public",
    "build-storybook": "build-storybook -s public"
  },
  
  ...
```
{% endcode %}
