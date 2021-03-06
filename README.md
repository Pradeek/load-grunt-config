#load-grunt-config

load-grunt-config is a Grunt library that allows you to break up your Gruntfile config by task.  For most small projects a single Gruntfile.js is perfect. But as a project grows, the Gruntfile.js can quickly become unmanagable; this is where load-grunt-config comes in handy.  It was heavily inspired by [Thomas Boyt's "More Maintainable Gruntfiles"](http://www.thomasboyt.com/2013/09/01/maintainable-grunt.html).

##Features

- Each task has its own config file. Example: jshint.js, mocha.js, etc.
- Auto load all grunt plugins.  Uses [load-grunt-tasks](https://github.com/sindresorhus/load-grunt-tasks).
- Auto expose package.json (`<%= package.name %>`).
- Support for YAML files.
- Support for coffeescript files.
- Support for returning a function.
- Easily register task aliases with `aliases.(js|yaml|coffee)`.

##Installation

```bash
npm install -D load-grunt-config
```

##Example

Basic Gruntfile.js
```javascript
module.exports = function(grunt) {

	require('load-grunt-config')(grunt);

};
```

Gruntfile.js with options
```javascript
module.exports = function(grunt) {
    var gruntTasks = path.join(process.cwd(), 'grunt');
	require('load-grunt-config')(grunt, {
		configPath: path.join(gruntTasks, 'custom'), //path to task.js files, defaults to grunt dir
		defaultPath: path.join(gruntTasks, 'vendor'), // can be used for shared config
		init: true, //auto grunt.initConfig
		config: { //additional config vars
			test: false
		},
		loadGruntTasks: { //can optionally pass options to load-grunt-tasks.  If you set to false, it will disable auto loading tasks.
			pattern: 'grunt-',
			config: require('./package.json'),
			scope: 'devDependencies'
		}
	});

};
```

###Grunt tasks files

Here's what the files in your `grunt/` folder could look like.  You can use either .js, .yaml, or .coffee - whatever you prefer and you can mix and match as you see fit.

Example js file returning an object - `grunt/watch.js`
```javascript
module.exports = {
  all: {
    files: [
      '<%= jshint.all %>',
      'grunt/*.yaml'
    ],
    tasks: [
      'default'
    ]
  }
};
```

Example js file returning a function - `grunt/jshint.js`
```javascript
module.exports = function (grunt) {
  return {
    all: [
      'Gruntfile.js',
      'grunt/*.js',
      'lib/*.js',
      'test/*.js'
    ]
  };
};
```

Example yaml file - `grunt/notify.yaml`
```yaml
default:
  options:
    message: 'Default finished'
```

Example coffee file - `grunt/task.coffee`
```coffee
module.exports =
  options:
    bare: true
```

###Aliases

If your `grunt/` folder contains an `aliases.(js|yaml|coffee)` file, `load-grunt-config` will use that to define your tasks aliases (like `grunt.registerTask('default', ['jshint']);`).

grunt/aliases.yaml
```yaml
default:
	- 'jshint'
	- 'mocha'
	- 'notify'
```
