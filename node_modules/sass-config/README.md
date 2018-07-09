# sass-config

[![sass-config](https://i.imgsafe.org/fbbe4c7bb5.png)](https://github.com/zgabievi/sass-config)

[![Bower](https://img.shields.io/bower/v/zgabievi/sass-config.svg?style=flat-square)](http://bower.io/search/?q=sass-config)
[![NPM](https://img.shields.io/npm/v/sass-config.svg?style=flat-square)](https://www.npmjs.com/package/sass-config)

DotConfig for SASS

+ [Installation](#installation)
+ [Usage](#usage)
+ [Example](#example)
+ [License](#license)

## Installation
### Install With [Bower](http://bower.io/search/?q=sass-config)
```
$ bower install sass-config --save
```

### Install With [NPM](https://www.npmjs.com/package/sass-config):
```
$ npm install sass-config --save
```

### Install Manually
- [Download Package Archive](https://github.com/zgabievi/sass-config/archive/master.zip)
- Extract zip to your project.

## Usage
Insert following code in top of your file:

```scss
@import "/bower_components/sass-config/config";
```

## Example

```scss
@import "bower_components/sass-config/config";

// configuration #1
$config-example: (
  foo: 'bar',
  baz: 'qux'
);

// configuration #2
$config-example2: (
  abc: 'def'
);

// set configuration
@include config('example', $config-example);
@include config('example2', $config-example2);

// get config value
@debug config('example.baz'); // Output: "qux"
@debug config('example2.abc'); // Output: "def"
```

## License
The sass-config package is open-sourced software licensed under the [MIT license](http://opensource.org/licenses/MIT).
