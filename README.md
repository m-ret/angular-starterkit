# angular-starterkit
### Clone it and run this in your terminal:
## `$ npm install; bower install; gulp; gulp serve`
------------------
### Tips:

# How to add fontawesome?

## [#337](https://github.com/Swiip/generator-gulp-angular/issues/337)

First, you need install fontawesome through bower
```bash
bower install fontawesome
```

Next, you need add to your scss file this lines (please, check the paths if you
change location of generator main.scss file):

```scss
$fa-font-path: "../../bower_components/font-awesome/fonts";
@import "../../bower_components/font-awesome/scss/font-awesome.scss";
```

Finally, you need add this pipeline in `gulp/build.js`, in `html` task:

```js
.pipe($.replace('../../bower_components/font-awesome/fonts', '../fonts'))
```
------------------
# Accessing 'bower_components' in .scss files without having to path ../../bower_components/

## [#550](https://github.com/Swiip/generator-gulp-angular/issues/550)

```javascript
var sassOptions = {
  loadPath: [ options.src + '/../bower_components/my-styles/'],
  style: 'expanded'
};
```

For those who use Foundation 5:

```javascript
var sassOptions = {
  loadPath: [options.src + '/../bower_components/foundation/scss'],
  style: 'expanded'
};
```

And then you can write this in the index.scss

```javascript
@import 'foundation';
```
---------------------
# Is there a way to change the output directory of fonts, scripts and styles?

## [#527](https://github.com/Swiip/generator-gulp-angular/issues/527) 

In your index.html search for the comments starting with <-- build. At the end, there is the target file name for the bundle, you can change it there.

Example:
<!-- build:css({.tmp/serve,src}) styles/vendor.css --> ->
<!-- build:css({.tmp/serve,src}) assets/styles/vendor.css -->

About images and fonts, you have to change paths in the gulp/build.js
---------------------
# How can I build for a specific environment?

## [#460](https://github.com/Swiip/generator-gulp-angular/issues/460) [#650](https://github.com/Swiip/generator-gulp-angular/issues/650)

`generator-gulp-angular` does not yet have a way to build for different environments.

One way to do this is with [gulp-ng-constant](https://www.npmjs.com/package/gulp-ng-constant):

#### 1) Create a new gulp task called `config`.

This reads in the configuration in either `config.json` or `configDev.json`, based on the `NODE_ENV` environment variable and generates an Angular module with the contents to be included in your application.

```javascript
gulp.task('config', function () {
  var configPath = 'config.json';
  if (process.env.NODE_ENV === 'dev') {
    configPath = 'configDev.json';
  }
  return gulp.src(configPath)
    .pipe($.ngConstant())
    .pipe(gulp.dest('src/app/'));
});
```

*configDev.json*
```json
{
  "name": "myApp.config",
  "constants": {
    "config": {
      "apiBase": "https://localhost/v1"
    }
  }
}
```

*config.json*
```json
{
  "name": "myApp.config",
  "constants": {
    "config": {
      "apiBase": "https://api.example.com/v1"
    }
  }
}
```

#### 2) Add the `config` gulp task as a dependency of `scripts`.

```javascript
module.exports = function(options) {
  gulp.task('scripts', ['config'], function () {
    return gulp.src(options.src + '/app/**/*.js')
      .pipe($.eslint())
      .pipe($.eslint.format())
      .pipe(browserSync.stream())
      .pipe($.size());
  });
};
```

#### 3) Use the generated module in your app.

```javascript
angular.module('myApp', ['myApp.config'])
  .service('apiService', ['config', function (config) {
    this.base = config.apiBase;
  });
```

#### 4) Run gulp with environment variable to build for the `dev` environment.

```bash
$ NODE_ENV=dev gulp serve
$ NODE_ENV=dev gulp
```
---------------------
These notes were taken from [Swiip/generator-gulp-angular](https://github.com/Swiip/generator-gulp-angular)
