# Dependencies Linker

Link your npm dependencies to serve them to the front-end.

Automatically link your project's dependency files to a folder like `public/scripts`
so that you can serve them to the front end.

## Install
```
npm install --save-dev dep-linker
```

## Use
``` javascript

var depLinker = require('dep-linker');
depLinker.linkDependenciesTo('./public/scripts')
  .then(() => console.log('Finished.'));
  // Done! All your dependencies ready to be served to the front-end
```
*dev-dependencies are not linked*.

## Task runners
There still isn't a specific wrapper for any task runner, but given that this
is just javascript for now you can use it like this and it will work like a charm:

### Gulp
``` javascript
var depLinker = require('dep-linker');

gulp.task('link-dependencies', function () {
  return depLinker.linkDependenciesTo('./public/scripts');
});
```

### Grunt
``` javascript
var depLinker = require('dep-linker');

grunt.task.registerTask('link-dependencies', 'Copy npm dependencies', function () {
    var done = this.async(); // <-- must be async
    return depLinker.linkDependenciesTo('public/scripts')
      .then(() => done())
      .catch(() => done());
});

```

## Options

### Set root folder
`dep-linker` gets your dependencies from your `package.json` which is in your project's
root folder. If the script is not being run from the root folder, specify the path
to it:

``` javascript
var depLinker = require('dep-linker');
depLinker.setRoot('../../');
```

## Windows

On Windows the default 'Dir' symlink requires Administrator rights. Event if the executing User 
is in the Administrator group it refuses to create a 'Dir' symlink. The module could technically
cause a UAC prompt by using spawning a child process with Verb: RunAs, however it becomes difficult
to manage the inputs and outputs of the spawned process. In Window, a Junction symbolic link works
exactly the way Developers would expect and does not require the same elevated prompt as a 'Dir' symlink.

The latest update allows this npm module to run on windows by creating Junction links instead of Dir links
by default. If you are running on Windows and want the old functionality back, perhaps you've setup your
scripts to run in elevated mode, you can use the new options object.

### Gulp
``` javascript
var depLinker = require('dep-linker');

// Enable Legacy symlinks for Windows only. Linux and OS-X are not affected
// by the options object at this time.
gulp.task('link-dependencies', function () {
  return depLinker.linkDependenciesTo('./public/scripts', {type: 'dir'});
});

// Default way on Windows only. Linux and OS-X are not effected by options at this time.
gulp.task('link-dependencies', function () {
  return depLinker.linkDependenciesTo('./public/scripts', {type: 'junction'});
});

// Is the same as: (The following task works on all platforms now and
// creates symbolic links as expected)
gulp.task('link-dependencies', function () {   
  return depLinker.linkDependenciesTo('./public/scripts');
});  
```