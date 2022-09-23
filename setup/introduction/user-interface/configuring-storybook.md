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
const { setConfig } = require('storybook-addon-playwright/configs');
const playwright = require('playwright');

module.exports = {
  ..
  addons: [
  ...
    '@storybook/addon-controls',
    '@storybook/addon-links',
    '@storybook/addon-essentials',
    '@storybook/addon-interactions',
    '@storybook/addon-knobs',
    '@storybook/preset-create-react-app',
    'storybook-addon-playwright/preset',
    'storybook-addon-playwright/register',
  ],
  ...
};

(async () => {
  let browser = {
    chromium: await playwright["chromium"].launch(),
    firefox: await playwright["firefox"].launch(),
    webkit: await playwright["webkit"].launch(),
  };
  setConfig({
    storybookEndpoint: `http://localhost:6006/`,
    getPage: async (browserType, options) => {
      const page = await browser[browserType].newPage(options);
      return page;
    },
    afterScreenshot: async (page) => {
      await page.close();
    },
  });
})();
```
{% endcode %}

middleware.js

{% code title="/ui/.storybook/middleware.js" %}
```bash
const middleware = require('storybook-addon-playwright/middleware');
module.exports = middleware;
```
{% endcode %}

