# Build Guide

- [Quickstart](#quickstart)
- [Get the Code](#get-the-code)
- [Build the Code](#build-the-code)
  - [Development Server](#development-server)
  - [Build Output](#build-output)
- [Build Scripts](#build-scripts)

## Quickstart

1. [Clone the repository](#get-the-code)

2. Install the dependencies

   ```bash
    npm install
   ```

3. Build the project

   ```bash
   npm run build
   ```

4. Start the [server](#development-server)

   ```bash
   npm start
   ```

5. Navigate to : [`http://localhost:8080/`](http://localhost:8080)

## Get the Code

- [Setup Git](https://help.github.com/articles/set-up-git/#platform-all) if it isn't already.
  - New to git or need a refresher? Now's a good time to learn! [Easy tutorials here.](https://guides.github.com/)
  - Make sure your SSH keys are configured ([linux](https://help.github.com/articles/generating-ssh-keys#platform-linux) | [mac](https://help.github.com/articles/generating-ssh-keys#platform-mac) | [windows](https://help.github.com/articles/generating-ssh-keys#platform-windows)).
  - Double-check your settings for name and email: `git config --get-regexp user.*`.
  - Recommended Git settings:
    - `git config --global fetch.prune true` - when fetching remote changes, remove any remote branches that no longer exist on the remote.
- Have [commit access](https://github.com/CesiumGS/cesium/blob/main/Documentation/Contributors/CommittersGuide/README.md) to CesiumJS?
  - No
    - Fork [cesium](https://github.com/CesiumGS/cesium).
    - Use the [GitHub website](https://github.com/CesiumGS/cesium/branches/all) to delete all branches in your fork except `main`.
    - Clone your fork, e.g., `git clone git@github.com:yourusername/cesium.git`.
    - Make changes in a branch, e.g., `git checkout -b my-feature`.
  - Yes
    - Clone the cesium repo, e.g., `git clone git@github.com:CesiumGS/cesium.git`
    - Make changes in a branch, e.g., `git checkout -b my-feature`.

## Build the Code

Prerequisites:

- Install [Node.js](http://nodejs.org/) on your system. Building Cesium requires Node 20.x or newer.

Cesium uses [npm modules](https://docs.npmjs.com/getting-started/what-is-npm) for development, so after syncing, you need to run `npm install` from the Cesium root directory:

```bash
npm install
```

Cesium ships with a simple HTTP server for testing. Once all modules have been installed, run `npm run build` to build the project:

```bash
npm run build
```

Then, run the development server:

```bash
npm start
```

Then browse to [http://localhost:8080/](http://localhost:8080/). The landing page includes apps and tools commonly used during development, including:

- **Hello World** : an example for how to create a 3D globe. [Tutorial here](https://cesium.com/learn/cesiumjs-learn/cesiumjs-quickstart/)
- **Sandcastle** : an app for viewing and creating [code examples](https://sandcastle.cesium.com?src=Hello%20World.html&label=Showcases), complete with a live preview
- **Test Suites** : tests using [Jasmine](https://jasmine.github.io/). [Testing guide here.](https://github.com/CesiumGS/cesium/blob/main/Documentation/Contributors/TestingGuide/README.md#testing-guide)
- **Documentation** : reference documentation built from source. [Documentation guide here.](https://github.com/CesiumGS/cesium/blob/main/Documentation/Contributors/DocumentationGuide/README.md#documentation-guide)

### Development Server

By default, the server only allows connections from your local machine. To allow connections from other machines, pass
the `--public` option to npm. Note the extra `--` is intentional and required by npm.

```bash
npm start -- --public
```

The development server has a few other options as well, which you can see by passing the `--help` parameter:

```bash
npm start -- --help
```

### Build Output

Cesium offers a few different distributions. When developing, make sure to pick the one that fits your app's architecture.

- [IIFE (immediately-invoked function expression)](https://developer.mozilla.org/en-US/docs/Glossary/IIFE) - A pre-processed bundle, optimized for the browser, and with web worker code inlined. It defines a `Cesium` global variable upon loading `Build/Cesium/Cesium.js`. This is what is available via the CDN. While much of our documentation uses IIFE-style globals for ease-of-use, we do not recommend this approach for production apps.
- [ESM (ECMAScript modules)](https://nodejs.org/api/esm.html) - Standard for packaging JS code which is supported by most browsers and NodeJS. Modules use `import` and `export` statements. Unprocessed, individual modules are available in the `Source` directory, accessible by importing `Source/Cesium.js`; A single pre-processed bundle by importing `Build/Cesium/index.js`.
- [CJS (CommonJS)](https://nodejs.org/api/modules.html) - A pre-processed, bundled module packaged for running in NodeJS accessible by requiring `index.cjs`.

In much of our documentation, we use IIFE as it can be easily loaded with a CDN and defines a global _Cesium_ variable with all modules attached.

For a production app, we recommend using the `Source` modules directly which will allow your build tool of choice to reduce the final release's size using tree shaking.

Read the complete list of build scripts and options below for more details.

While you can use the editor of your choice to develop Cesium, certain files, such as `glsl` and new tests, require that
the `build` task be executed in order for the changes to take effect. You can use the `build-watch` script to have this
happen automatically.

## Build Scripts

Cesium uses [gulp](http://gulpjs.com/) for build tasks, but they are all abstracted away by [npm run scripts](https://docs.npmjs.com/cli/run-script).

Specify the target(s) at the command line:

```bash
npm run [target-name]
```

Here's the full set of scripts and what they do.

- **Build scripts** -- build and package the source code and documentation
  - `build` - A fast, developer-oriented build that bundles the source modules to produce all-in-one files in the `Build/CesiumUnminified` directory that exposes the entire Cesium API attached to a single global `Cesium` object. Run this when a GLSL shader is changed since the .glsl file is converted to a .js file with a string for the GLSL source.
    - `--minify` - [Minifies](<http://en.wikipedia.org/wiki/Minification_(programming)>) the output for optimized loading. Specifying this option will output to `Build/Cesium`.
    - `--removePragmas` - Optimizes the output by removing debugging code that validates function input and throws `DeveloperError`s. The removed sections are marked with `//>>includeStart('debug', pragmas.debug);` blocks in the code.
    - `--node` - Bundles an `index.cjs` module targeted for use in NodeJS
  - `build-watch` - A never-ending task that watches your file system for changes to Cesium and builds the source code as needed. All `build` options are also available for this task.
  - `build-apps` - Builds the example applications (such as Cesium Viewer) to produce self-contained, minified, deployable versions in the `Build` directory.
  - `build-docs` - Generates HTML documentation in `Build/Documentation` using [JSDoc 3](https://github.com/jsdoc3/jsdoc). See the [Documentation Guide](../DocumentationGuide/README.md) for more details.
  - `build-ts` - Generates a TypeScript definitions file for the Cesium library
  - `build-third-party` - Generates `ThirdParty.json`, a file which lists the latest licensing information of installed third party modules
  - `release` - A full release build that creates a shippable product, including generating documentation.
  - `make-zip` - Builds a zip file containing all release files. This includes the source ESM modules, bundled ESM and IIFE format `Cesium.js`, plus the bundled minified versions of ESM and IIFE, the generated documentation, the test suite, and the example applications (in both built and source form).
- **Utility scripts** -- code coverage, static code analysis, and other utilities
  - `clean` - Removes all generated build artifacts
  - `cloc` - Runs [CLOC](https://github.com/AlDanial/cloc) to count the lines of code on the Source and Specs directories. This requires [Perl](http://www.perl.org/) to execute.
  - `coverage` - Runs coverage and opens the default browser with the results
  - `eslint` - Runs [ESLint](http://eslint.org/), a static code analysis tool, on the entire source tree
  - `prettier` - Formats the code base using [Prettier](https://prettier.io/)
  - `prettier-check` - Verifies prettier formatting, but does not write the output
- **Testing scripts** -- build and run the unit tests
  - `test` - Runs all tests with [Karma](http://karma-runner.github.io/0.13/index.html) using the default browser specified in the Karma config file.
  - `test-all` - Runs all tests with Karma using all browsers installed on the current system
  - `test-non-webgl` - Runs only non-WebGL tests
  - `test-webgl` - Runs only WebGL tests
  - `test-webgl-stub` - Runs all tests using the WebGL stub, which WebGL calls a noop and ignores related test expectations
  - `test-webgl-validation` - Runs all tests with Karma and enables low-level WebGL validation
  - `test-release` - Runs all tests on the minified release version of built Cesium
- **Deployment scripts**
  - `deploy-status` - Sets the deployment statuses in GitHub, for use in CI
  - `deploy-set-version` - Sets the version of `package.json`, for use in CI
