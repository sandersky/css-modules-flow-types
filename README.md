<div align="center">
  <h1>css-modules-flow-types</h1>

  [![MIT License](https://img.shields.io/npm/l/css-modules-flow-types-loader.svg?style=flat-square)](https://github.com/skovhus/css-modules-flow-types-loader/blob/master/LICENSE)
  [![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](http://makeapullrequest.com)
</div>

CLI and Webpack loader for creating [Flow](https://flow.org/) type definitions based on [CSS Modules](https://github.com/css-modules/css-modules) files.

This gives you:
- auto-completing for css files in most editors
- flow type safety showing usage of non existing classes

Read more about the background in this blog post: ["Type safe CSS Modules with Flow"](https://hackernoon.com/type-safe-css-modules-with-flow-dd95e761bbe5).


## Example

Given the following css file using CSS Modules:
```css
@value primary: red;

.myClass {
  color: primary;
}
```

`css-modules-flow-types` creates the following .flow file next to it:

```javascript
// @flow
/* This file is automatically generated by css-modules-flow-types */
declare module.exports: {|
  +'myClass': string;
  +'primary': string;
|};
```


## Usage (Webpack loader)

### style-loader
The `css-modules-flow-types-loader` need to be added right after after `style-loader`:

```sh
$ npm install --dev css-modules-flow-types-loader
$ yarn add -D css-modules-flow-types-loader
```

```javascript
{
  test: /\.css$/,  // or the file format you are using for your CSS Modules
  use: [
    'style-loader',
    'css-modules-flow-types-loader',
    // Other loaders like css-loader after this:
    {
      ...
    }
  ]
}
```

### css-loader

For `css-loader`, `css-modules-flow-types-loader` needs to come _before_
`css-loader`.

```javascript
{
  test: /\.css$/,  // or the file format you are using for your CSS Modules
  use: [
    ExtractTextPlugin.extract({
      use: [
        'css-modules-flow-types-loader',
        {
          loader: 'css-loader',
          options: {}, // Any options for css-loader
        }
      ]
    })
  ]
}
```

## Usage (CLI)

```sh
$ npm install --dev css-modules-flow-types-cli
$ yarn add -D css-modules-flow-types-cli
```

This installs the runner as `css-modules-flow-types`.

And run `css-modules-flow-types <input directory or glob>` command.

For example, if you have .css files under `src` directory, exec the following:

Running,

```sh
css-modules-flow-types src
```

Creates `*.css.flow` files next to all css files.

```text
(your project root)
- src/
    | myStyle.css
    | myStyle.css.flow [created]
```



## Troubleshooting

### Support for other file extensions

To support `.scss`, `.sass`, `.scss.module` or similar files extensions
you need to update your `.flowconfig` file with the following section:

```
[options]

; Extensions
module.file_ext=.js
module.file_ext=.jsx
module.file_ext=.json
module.file_ext=.css
module.file_ext=.scss
module.file_ext=.sass
module.file_ext=.scss.module
// other files used by flow
```

Without this Flow might error out with `Required module not found`.

### Dynamic imports in Webpack

In some cases, attempting to load imports using dynamic references could choke webpack as it attempts to parse `.flow` files and encounters unknown syntax. The solution is to exclude `.flow` files with a `webpackExclude` directive in the import statement.

If you have an import like this:

```javascript
import(
  /* webpackChunkName: "[request]" */
  `/Path/To/Components/${ getComponent()}`
)
```

Add `webpackExclude` like this:

```javascript
import(
  /* webpackChunkName: "[request]" */
  /* webpackExclude: /\.flow$/ */
  `/Path/To/Components/${ getComponent()}`
)
```

## Inspiration

- https://github.com/Quramy/typed-css-modules
- https://github.com/Jimdo/typings-for-css-modules-loader


## Contributing

To get started, run:

  yarn

When developing:

  yarn verify  # (runs lint and unit test)
  yarn lint
  yarn test
  yarn test:cov
  yarn test:watch


## Deployment

On a new branch run:

  npx lerna version --no-git-tag-version
  git commit -m 'chore: bump version to x.x'

And update the CHANGELOG.md file.

Once the branch is merged, the new version is deployed.

## License
This software is released under the MIT License.
