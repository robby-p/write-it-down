# webpack

> At its core, webpack is a static module bundler for modern JavaScript applications.

## Glossary

_From [Webpack.js.org](https://webpack.js.org/glossary)_

**entry points:** Tells webpack where to start and follows the graph of dependencies to know what to bundle. Isolated execution context or contextual root(s) of what you want bundled

**es5:** The javascript syntax we must translate our modern es6+ syntax to. This is the only syntax compatible with ie11 and leaves very much to be desired. This dumbing down and polyfilling and transpiling leaves no shortage of bugs and adds a great deal of bloat to our resulting bundles

**modules:** Discrete chunks of functionality that provide a smaller surface area than a full program

- A module can be required as a dependency from another module and a resolver is a library which helps in locating a module by its absolute path. Modules are searched for inside all directories specified in resolve.modules
- **commonjs and es6 modules**
  - commonjs - `require()` syntax, most notoriously adopted by `nodejs` synchronous
  - es6 modules - designed to supersede commonjs require syntax. pretty new not widely supported ... _yet_ but transpiled for support. Recommended syntax.

**bundles:** Produced from a number of distinct modules, bundles contain the final versions of source files that have already undergone the loading and compilation process. your outputs of running a webpack build.

**chunks**: chunks directly correspond with the output bundles however, there are some configurations that don't yield a one-to-one relationship.

**lazy Loading** A process of loading parts (chunks) of your application lazily. In other words loading them only when we really need them. Most commonly via dynamic es6 `import()` syntax. _see [More-on-commonJS-and-es6](#More-on-commonJS-and-es6)_

**code splitting**: Refers to dividing your code into various bundles/chunks which you can then load on demand instead of loading a single bundle containing everything. May be asynchronous / Lazy Loading. Or can be accomplished synchronously by controlling what goes into which bundle.

**plugins:** called by the webpack compiler, giving access to the entire compilation lifecycle. These packages will typically extend compilation functionality

- _plugins we use_
  - SourceMap: Used for customization beyond that which is provided by webpack. Specifically so we can host our sourcemaps on our internal storage system and restrict access to them .
  - ContextReplacement: To support locales, _we generate a bundle for every locale_ Each bundle will exclusively only contain relevant translation data for its given locale.
  - MiniCssExtract: This plugin extracts CSS into separate files. It creates a CSS file per JS file which contains CSS
  - HtmlWebpack: simplifies creation of HTML files to serve our webpack bundles. **Currently only utilized internally to the FE team.** [This PR](https://code.squarespace.net/projects/SERVICE/repos/account-service/pull-requests/988/overview) utilizes this plugin so we can serve static html for our acceptance testing, greatly simplifying the old process. Futher development may allow statically generated html builds which prerender the skeleton of the UI for faster load times, actual and perceived
  - CheckES5Webpack: A slightly 'janky' module that acts as a stop gap to detect if any js syntax other than es5 (ie11 compatible) is present in the resulting bundles.

## More on commonJS and es6

- Babel+Webpack support both. Can work interchangeably but _not recommended_

- commonjs `require()` syntax supports synchronous dynamic imports, ie: `require('./translations/'+locale+'.yaml')`

  - Since it is synchronous and you may not have any knowledge of what will be required, webpack accomplishes this by including **everything** in the dynamically required directory. In the above case `./translations/*`

- import also supports dynamic syntax, but it returns a promise / is asynchronous, ie: `import('./translations.'+locale+'.yaml').then(...)`

  - It differs from the static `import` syntax in that it doesn't have to be declared at the top of your module/entry point. It is also apart of the ecma standard
  - webpack inclusion of these dynamically resolve modules can be smarter and more streamlined since they are asynchronous. each `${locale}.yaml` file maybe split into its own separate chunk, then fetched browserside as needed independently.
  - This effect may also be achieved using a [special require syntax](https://webpack.js.org/api/module-methods/#requireensure), but is not standard and is unique to webpack

- Static ES6 modules have two advantages over commonjs syntax:

  1. First, their rigid structure makes them statically analyzable. That enables, e.g., tree shaking (dead code elimination) which can significantly reduce the size of bundled modules.
  2. Second, imports are never accessed directly, which means that cyclic dependencies are always supported.

- further reading, good reference: https://exploringjs.com/es6/ch_modules.html
