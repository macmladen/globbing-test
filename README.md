# globbing-test

## Using this repository for testing
 
There are branches that contains different states that makes compiling successful:

* `master` — fails, it has all of three things that breaks with **Error: write after end**
* `gulp-glob` — passes if only `gulp` globbing is turned off for directories
* `css-glob` — passes if we use `gulp` globbing but not `gulp-css-globbing`
* `mixin-in-scss` — both globbing there but no mixins in imported partials

## `mixin-in-scss`

This is the weird one: both globbing are there but no mixins in imported partials.

With the mixin `on-event` in the `tools/mixin` it failed (**update:** nearly) every time. But when I just commented out lines 12-13 not to import base in `main.scss` it started to pass and fail.

In consecutive runs it appeared that it randomly passes and fails without any change to code or anything at all, just repeating commands to compile in succession, like the following terminal dump. The difference is just two seconds so it is obvious that the code is not changed.

Repeating various deletion of lines in `base/base`, `tools/mixin` and `main` revealed very strange results, passing and failing randomly on 20+ consecutive starting.

So I am not sure what is happening here but poking like this does not help.

**UPDATE 2:** Apparently, it seems to me that the problem lies in usage of abstractions, that is variables and mixins. Although I am sure I had success with moving mixin to Sass, subsequent test failed to confirm that.
 
It should be tested why and how this triggers `Error: write after end` and where problem lies, in which module and how it should be fixed.

```
mladen:~/Sites/globbing-test 16:26:16 $ gulp sass
node-sass	3.3.3	(Wrapper)	[JavaScript]
libsass  	3.2.5	(Sass Compiler)	[C/C++]
[16:26:18] Using gulpfile ~/Sites/globbing-test/gulpfile.js
[16:26:18] Starting 'sass'...
[16:26:18] Finished 'sass' after 18 ms
events.js:141
      throw er; // Unhandled 'error' event
      ^

Error: write after end
    at writeAfterEnd (/Users/mladen/Sites/globbing-test/node_modules/gulp-sass/node_modules/through2/node_modules/readable-stream/lib/_stream_writable.js:144:12)
    at DestroyableTransform.Writable.write (/Users/mladen/Sites/globbing-test/node_modules/gulp-sass/node_modules/through2/node_modules/readable-stream/lib/_stream_writable.js:192:5)
    at write (/Users/mladen/Sites/globbing-test/node_modules/gulp-css-globbing/node_modules/vinyl-map/node_modules/through2/node_modules/readable-stream/lib/_stream_readable.js:623:24)
    at flow (/Users/mladen/Sites/globbing-test/node_modules/gulp-css-globbing/node_modules/vinyl-map/node_modules/through2/node_modules/readable-stream/lib/_stream_readable.js:632:7)
    at DestroyableTransform.<anonymous> (/Users/mladen/Sites/globbing-test/node_modules/gulp-css-globbing/node_modules/vinyl-map/node_modules/through2/node_modules/readable-stream/lib/_stream_readable.js:613:7)
    at emitNone (events.js:67:13)
    at DestroyableTransform.emit (events.js:166:7)
    at onwriteDrain (/Users/mladen/Sites/globbing-test/node_modules/gulp-sass/node_modules/through2/node_modules/readable-stream/lib/_stream_writable.js:300:12)
    at afterWrite (/Users/mladen/Sites/globbing-test/node_modules/gulp-sass/node_modules/through2/node_modules/readable-stream/lib/_stream_writable.js:288:5)
    at onwrite (/Users/mladen/Sites/globbing-test/node_modules/gulp-sass/node_modules/through2/node_modules/readable-stream/lib/_stream_writable.js:281:7)
mladen:~/Sites/globbing-test 16:26:18 $ gulp sass
node-sass	3.3.3	(Wrapper)	[JavaScript]
libsass  	3.2.5	(Sass Compiler)	[C/C++]
[16:26:19] Using gulpfile ~/Sites/globbing-test/gulpfile.js
[16:26:19] Starting 'sass'...
[16:26:19] Finished 'sass' after 32 ms
mladen:~/Sites/globbing-test 16:26:20 $ 
```

## Reproducing the error

Testing for globbing problems in gulp-sass and gulp-css-globbing.

After pulling this repository, perform installation:

```shell
git clone https://github.com/macmladen/globbing-test.git
npm install
```

Execute this to get the error:

```
gulp sass
```

It throws

```
$ gulp sass
node-sass	3.3.3	(Wrapper)	[JavaScript]
libsass  	3.2.5	(Sass Compiler)	[C/C++]
[15:06:50] Using gulpfile ~/Sites/globbing-test/gulpfile.js
[15:06:50] Starting 'sass'...
[15:06:50] Finished 'sass' after 18 ms
events.js:141
      throw er; // Unhandled 'error' event
      ^

Error: write after end
    at writeAfterEnd (/Users/mladen/Sites/globbing-test/node_modules/gulp-sass/node_modules/through2/node_modules/readable-stream/lib/_stream_writable.js:144:12)
    at DestroyableTransform.Writable.write (/Users/mladen/Sites/globbing-test/node_modules/gulp-sass/node_modules/through2/node_modules/readable-stream/lib/_stream_writable.js:192:5)
    at write (/Users/mladen/Sites/globbing-test/node_modules/gulp-css-globbing/node_modules/vinyl-map/node_modules/through2/node_modules/readable-stream/lib/_stream_readable.js:623:24)
    at flow (/Users/mladen/Sites/globbing-test/node_modules/gulp-css-globbing/node_modules/vinyl-map/node_modules/through2/node_modules/readable-stream/lib/_stream_readable.js:632:7)
    at DestroyableTransform.<anonymous> (/Users/mladen/Sites/globbing-test/node_modules/gulp-css-globbing/node_modules/vinyl-map/node_modules/through2/node_modules/readable-stream/lib/_stream_readable.js:613:7)
    at emitNone (events.js:67:13)
    at DestroyableTransform.emit (events.js:166:7)
    at onwriteDrain (/Users/mladen/Sites/globbing-test/node_modules/gulp-sass/node_modules/through2/node_modules/readable-stream/lib/_stream_writable.js:300:12)
    at afterWrite (/Users/mladen/Sites/globbing-test/node_modules/gulp-sass/node_modules/through2/node_modules/readable-stream/lib/_stream_writable.js:288:5)
    at onwrite (/Users/mladen/Sites/globbing-test/node_modules/gulp-sass/node_modules/through2/node_modules/readable-stream/lib/_stream_writable.js:281:7)
```

This branch is in error

## [The original comment](https://github.com/dlmanning/gulp-sass/issues/274#issuecomment-145564899)

This happens with `gulp-css-globbing` and `gulp.src()` globbing with mixin.

It is introducing problem if some `.scss` file has `@import` of some partial (file starting with underscore like `_helpers.scss`) contains mixins.

If you move mixin to the main `.scss` file it will compile with everything else just the same.

Another solution (and probably the source to the problem) is the globing in `gulp.src()` argument with globbing so it could be that globbings are not playing together well.

This fails

```javascript
gulp.task('sass', function() {
  gulp.src('./sass/**/*.{sass,scss}')
    .pipe(cssGlobbing({
      extensions: ['.css', '.scss']
    }))
    ...
```

while this works

```javascript
gulp.task('sass', function() {
  gulp.src('./sass/main.scss')
```

with everything else exactly the same.
