# babel-plugin-import

Modular import plugin for babel, compatible with [antd](https://github.com/ant-design/ant-design), [antd-mobile](https://github.com/ant-design/ant-design-mobile), and so on.

[![NPM version](https://img.shields.io/npm/v/babel-plugin-import.svg?style=flat)](https://npmjs.org/package/babel-plugin-import)
[![Build Status](https://img.shields.io/travis/ant-design/babel-plugin-import.svg?style=flat)](https://travis-ci.org/ant-design/babel-plugin-import)

----

## Why babel-plugin-import

- [English Instruction](https://ant.design/docs/react/getting-started#Import-on-Demand)
- [中文说明](https://ant.design/docs/react/getting-started-cn#%E6%8C%89%E9%9C%80%E5%8A%A0%E8%BD%BD)

## Where to add babel-plugin-import

- [babelrc](https://babeljs.io/docs/usage/babelrc/)
- [babel-loader](https://github.com/babel/babel-loader)

## Example

#### `{ "libraryName": "antd" }`

```javascript
import { Button } from 'antd';
ReactDOM.render(<Button>xxxx</Button>);

      ↓ ↓ ↓ ↓ ↓ ↓
      
var _button = require('antd/lib/button');
ReactDOM.render(<_button>xxxx</_button>);
```

#### `{ "libraryName": "antd", style: "css" }`

```javascript
import { Button } from 'antd';
ReactDOM.render(<Button>xxxx</Button>);

      ↓ ↓ ↓ ↓ ↓ ↓
      
var _button = require('antd/lib/button');
require('antd/lib/button/style/css');
ReactDOM.render(<_button>xxxx</_button>);
```

#### `{ "libraryName": "antd", style: true }`

```javascript
import { Button } from 'antd';
ReactDOM.render(<Button>xxxx</Button>);

      ↓ ↓ ↓ ↓ ↓ ↓
      
var _button = require('antd/lib/button');
require('antd/lib/button/style');
ReactDOM.render(<_button>xxxx</_button>);
```

Note : with `style: true` css source files are imported and optimizations can be done during compilation time. With `style: "css"`, pre bundled css files are imported as they are.  

`style: true` can reduce the bundle size significantly, depending on your usage of the library.

## Usage

```bash
npm install babel-plugin-import --save-dev
```

Via `.babelrc` or babel-loader.

```js
{
  "plugins": [["import", options]]
}
```

### options

`options` can be object.

```javascript
{
  "libraryName": "antd",
  "style": true,   // or 'css'
}
```

```javascript
{
  "libraryName": "lodash",
  "libraryDirectory": "",
  "camel2DashComponentName": false,  // default: true
}
```

```javascript
{
  "libraryName": "material-ui",
  "libraryDirectory": "components",  // default: lib
  "camel2DashComponentName": false,  // default: true
}
```

~`options` can be an array.~ It's not available in bable@7+

For Example: 

```javascript
[
  {
    "libraryName": "antd",
    "libraryDirectory": "lib",   // default: lib
    "style": true
  },
  {
    "libraryName": "antd-mobile"
  },
]
```
`Options` can't be an array in babel@7+, but you can add plugins with name to support multiple dependencies.

For Example:

```javascrit
// .babelrc
"plugins": [
  ["import", { "libraryName": "antd", "libraryDirectory": "lib"}, "ant"],
  ["import", { "libraryName": "ant-mobile", "libraryDirectory": "lib"}, "ant-mobile"]
]
```

### style

- `["import", { "libraryName": "antd" }]`: import js modularly
- `["import", { "libraryName": "antd", "style": true }]`: import js and css modularly (LESS/Sass source files)
- `["import", { "libraryName": "antd", "style": "css" }]`: import js and css modularly (css built files)

If option style is a `Function`, `babel-plugin-import` will auto import the file which filepath equal to the function return value. This is useful for the components library developers.

e.g. 
- ``["import", { "libraryName": "antd", "style": (name) => `${name}/style/2x` }]``: import js and css modularly & css file path is `ComponentName/style/2x`

If a component has no style, you can use the `style` function to return a `false` and the style will be ignored.

e.g. 
```js
[
  "import", 
    { 
      "libraryName": "antd", 
      "style": (name: string, file: Object) => {
        if(name === 'antd/lib/utils'){
          return false;
        }
        return `${name}/style/2x`;
      }
    }
]
```

### Note

babel-plugin-import will not work properly if you add the library to the webpack config [vendor](https://webpack.github.io/docs/code-splitting.html#split-app-and-vendor-code). 
