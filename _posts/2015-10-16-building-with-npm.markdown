---
layout: post
title:  "Building with npm"
date:   2015-10-16 09:00:00
---
For the past year I've strictly been using [npm](https://github.com/npm/npm) scripts for my client side build process. I was inspired by Keith Cirkel's post: [How to Use npm as a Build Tool](http://blog.keithcirkel.co.uk/how-to-use-npm-as-a-build-tool/).

Before making the switch to npm scripts, I used both [Grunt](https://github.com/gruntjs/grunt) and [Gulp](https://github.com/gulpjs/gulp) with varying levels of enjoyment. The issue for me was that I never really wanted to spend time learning how they worked, I just wanted it to work! That made it difficult to troubleshoot when something went wrong, and with the string of dependencies that occur when you use one of these tools it ends up becoming very time consuming and taking away from actually building stuff.

My client side projects generally consist of the following tools:

  * [Browserify](https://github.com/substack/node-browserify) - Commonjs modules
  * [Watchify](https://github.com/substack/watchify) - Rebundling during development
  * [Sass](https://github.com/sass/node-sass) - Compiling Sass
  * [Nodemon](https://github.com/remy/nodemon) - Watching for file changes and running a command
  * [Parallel Shell](https://github.com/keithamus/parallelshell) - Running multiple npm commands in parallel
  * [HTTP Server](https://github.com/indexzero/http-server) - For a server when you're working on a single page app
  * [UglifyJS](https://github.com/mishoo/UglifyJS2) - Minifying
  * [ESLint](https://github.com/eslint/eslint) - Linting

I've used all of the above tools in at least a dozen different projects. They're not the sexiest, but they do what I need and generally don't have any surprises.

The trickiest part of using npm scripts for your build process is learning the cli for each tool and how to tie them together correctly. However, I've found that the time spent learning these tools is much better than the time spent learning a plugin for Grunt or Gulp. You get a more intimate understanding of the tool that does that actual work, rather than an abstraction.

Below is an example of what my scripts generally look like. As long as you npm install the above tools, you should be able to use this - although you may need to adjust the paths to fit your folder structure.

{% highlight json %}
"scripts": {
  "dev": "parallelshell 'npm run serve -- build/' 'npm run watch'",
  "watch": "parallelshell 'npm run watch:js' 'npm run watch:css' 'npm run watch:lint'",
  "watch:js": "watchify src/scripts/index.js -v -o build/main.js",
  "watch:css": "nodemon -q -w src/styles/ -e scss --exec 'node-sass -q src/styles/main.scss build/main.css'",
  "watch:lint": "nodemon -q -w src/scripts/ -e js --exec 'npm run lint'",
  "lint": "eslint src/scripts/**",
  "build": "npm run lint && npm run build:css && npm run build:js && npm run serve -- dist/",
  "build:js": "browserify src/scripts/index.js | uglifyjs -m -c sequences=true,dead_code=true,conditionals=true,booleans=true,unused=true,if_return=true,join_vars=true,drop_console=true > dist/main.js",
  "build:css": "node-sass -q src/styles/main.scss dist/main.css --output-style compressed",
  "serve": "http-server"
}
{% endhighlight %}

My usage of the above scripts is limited to two commands. For development I use:
{% highlight bash %}
npm run dev
{% endhighlight %}

And when I'm ready to deploy I use:
{% highlight bash %}
npm run build
{% endhighlight %}

The above is a pretty basic example of what can be done using npm. I've found that I'm more productive and have more confidence in my build process since switching. There's a couple of features and steps I don't include above, but you can check out Keith's post for a more detailed walkthrough: [How to Use npm as a Build Tool](http://blog.keithcirkel.co.uk/how-to-use-npm-as-a-build-tool/).
