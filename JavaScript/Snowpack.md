# Snowpack

- [Snowpack](https://www.snowpack.dev/)

snowpack.config.js
```javascript
/*
 * [Snowpack Configuration File](https://www.snowpack.dev/reference/configuration).
 */
module.exports = {
  /*
   * [Documentation on exclude](https://www.snowpack.dev/reference/configuration#exclude).
   */
  exclude: [
    ".idea",
    ".git",
    ".gitignore",
    "config.yml",
    "config.codekit3",
    "package.json",
    "package-lock.json",
    "postcss.config.js",
    "readme.md",
    "snowpack.config.js",
  ],
  /*
   * Don't need to prefix the build folder because it is set in `buildOptions.out`.
   * If `mount` is not set then will build off `root`.
   *
   * [Documentation on mount](https://www.snowpack.dev/reference/configuration#mount).
   *
   * If we wanted to copy everything from the source fold we could do something similar to this `"src" : "/"`.
   */
  mount: {
    "src/assets": { url: "/assets", static: true, resolve: false },
    "src/config": { url: "/config", static: true, resolve: false },
    "src/layout": { url: "/layout", static: true, resolve: false },
    "src/locales": { url: "/locales", static: true, resolve: false },
    "src/sections": { url: "/sections", static: true, resolve: false },
    "src/snippets": { url: "/snippets", static: true, resolve: false },
    "src/templates": { url: "/templates", static: true, resolve: false },
    "src/scripts/compiled": { url: "/assets" },
    "src/scripts/global": { url: "/assets" }, // Have to mount all script folders so the compiled files can resolve
    "src/styles/compiled": { url: "/assets" } // This has to be specific because of the unstable state the `optimize` is. Don't have to include other folders like the scripts section because postcss-import will take care of the concatenation.
  },
  /*
   * [Documentation on plugins](https://www.snowpack.dev/reference/configuration#plugins)
   * [Plugin guide](https://www.snowpack.dev/guides/plugins)
   * [Plugin API](https://www.snowpack.dev/reference/plugins)
   * [Plugin catalog](https://www.snowpack.dev/plugins)
   */
  plugins: [ "@snowpack/plugin-postcss" ],
  /*
   * [Documentation on devOptions](https://www.snowpack.dev/reference/configuration#devoptions).
   */
  devOptions: {
    "port": 3000,
    "open": "chrome",
  },
  /*
   * Setting `out` in the build options means we don't need to prefix the mount location.
   *
   * [Documentation on buildOptions](https://www.snowpack.dev/reference/configuration#buildoptions).
   */
  buildOptions: {
    "out": "dist",
    "watch": false,
  },
  /*
   * [Guide for optimize](https://www.snowpack.dev/guides/optimize-and-bundle).
   */
  optimize: {
    entrypoints: ["src/scripts/compiled/theme.js"], // Can't have mixed entries
    bundle: true,
    splitting: false,
    minify: true,
    manifest: false,
    target: 'es2017',
  },
};
```