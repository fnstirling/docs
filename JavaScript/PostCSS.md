# PostCSS

## Resources
- [Website](https://postcss.org/)
- [postcss/postcss](https://github.com/postcss/postcss)

## Plugins

### Used
- [autoprefixer](https://github.com/postcss/autoprefixer#browsers)
- [cssnano](https://github.com/cssnano/cssnano)
- [postcss-preset-env](https://github.com/csstools/postcss-preset-env)
- [postcss-nested](https://github.com/postcss/postcss-nested)
- [postcss-simple-vars](https://github.com/postcss/postcss-simple-vars)
- [postcss-use](https://github.com/postcss/postcss-use)
- [postcss-utilities](https://github.com/ismamz/postcss-utilities)

### Potential
- [Popular plugins](https://github.com/postcss/postcss#plugins)
- [Plugins](https://github.com/postcss/postcss/blob/main/docs/plugins.md)
- [postcss-custom-properties](https://github.com/postcss/postcss-custom-properties)
- [postcss-modules](https://github.com/css-modules/postcss-modules)

## Example Files

package.json
```json
{
  "name": "shopify-theme",
  "version": "2.0.0",
  "description": "Nude by Nature shopify theme",
  "main": "",
  "scripts": {
    "theme:deploy:dev": "theme deploy --env=dev",
    "theme:deploy:prod:au": "theme deploy --env=au",
    "theme:watch:dev": "theme open --env=dev && theme watch --env=dev",
    "theme:watch:all": "theme open --env=au && theme watch -a"
  },
  "author": "Fraser Stirling",
  "license": "UNLICENSED",
  "private": true,
  "devDependencies": {
    "@snowpack/plugin-postcss": "^1.1.0",
    "autoprefixer": "^10.2.4",
    "cssnano": "^4.1.10",
    "postcss": "^8.2.6",
    "postcss-cli": "^8.3.1",
    "postcss-import": "^14.0.0",
    "postcss-nested": "^5.0.3",
    "postcss-preset-env": "^6.7.0",
    "postcss-simple-vars": "^6.0.3",
    "postcss-use": "^3.0.0",
    "postcss-utilities": "^0.8.4",
    "snowpack": "^3.0.13"
  },
  "browserslist": [
    ">0.2%",
    "last 2 versions",
    "Firefox ESR",
    "not dead",
    "not IE 11"
  ]
}
```

postcss.config.js
```javascript
module.exports = {
  plugins: [
    require("postcss-import"),
    require('postcss-use'),
    require('postcss-utilities'),
    require('postcss-simple-vars'),
    require('postcss-nested'),
    require('postcss-preset-env'),
    require('autoprefixer'),
    require('cssnano'),
  ],
};
```