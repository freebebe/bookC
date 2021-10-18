# NPM Tips and Tricks  
This information was generated from the egghead.io 
video series [How to Use npm Scripts as Your Build Tool](https://egghead.io/courses/how-to-use-npm-scripts-as-your-build-tool?utm_source=drip&utm_medium=email&utm_campaign=npm-build-tool) 
by Elijah Manor.  See that for a better description and more information. Thanks go to egghead.io and Elijah since this document
would not exist without their work.

While the idea of the video series is to use npm for managing your build process, it also contains
a lot of npm tips and tricks that can be useful in general. Which is why I pulled out some of the key concepts here.
  
You can click on the section titles to go to the original video lessons for each topic.

You can also find more information about these npm commands and features in the npm documentation.
+ [NPM Documentation](https://docs.npmjs.com/)
+ [Using a package.json file](https://docs.npmjs.com/getting-started/using-a-package.json)
+ [npm-config](https://docs.npmjs.com/misc/config)
+ [npm-scripts](https://docs.npmjs.com/misc/scripts)

At the bottom of this document is a list of the various npm packages used in the examples.

## [Create a basic package.json file](https://egghead.io/lessons/tools-create-a-basic-package-json-file)
* **npm init**  (prompts for package info)  
* **npm init --force** (no prompts, use default)  
* **npm init --yes** (same as --force)  
* **npm init -f** (same as --force)  
* **npm init -y** (same as --yes)  

  MIT license, permissive, few restrictions

* **npm run**  (list all scripts)  
* **npm test** (runs the test script)  
* **npm t** (runs the test script)  

## [Run the basic npm scripts](https://egghead.io/lessons/tools-run-the-basic-npm-scripts)
  npm knows about the "test" and "start" scripts. To run them:  
    **npm test** or **npm t**  
    **npm start**

    typical start script:  "node index.js"

## [Create a custom npm script](https://egghead.io/lessons/tools-create-a-custom-npm-script)
  "eslint" : "eslint --cache --fix ./"  
    --cache don't relint files that haven't changed  
    --fix autofix some rules with simple solutions  

  **npm run env** -- list NPM provided environment variables  
  **npm run-script eslint**  (to run the eslint script)  
  **npm run eslint** (same as run-script)  

## [Run npm scripts in series](https://egghead.io/lessons/tools-run-npm-scripts-in-series)
Use **&&** between commands to run them in series.

If any command fails, the rest of the line is aborted.

```json
scripts: {  
  "lint" : "npm run eslint && npm run stylelint",  
  "eslint": "eslint --cache --fix ./",  
  "stylelint": "stylelint '**/*.scss' --syntax scss"  
}  
```

## [Run npm scripts in parallel](https://egghead.io/lessons/tools-run-npm-scripts-in-parallel)
Use **&** between commands to run them in parallel.  
Use **wait** to allow a long running process to be terminated with CTRL-C.

```json
scripts: {  
  "lint" : "npm run eslint & npm run stylelint",  
  "eslint": "eslint --cache --fix ./",  
  "stylelint": "stylelint '**/*.scss' --syntax scss",  
  "watch" : "npm run lint & gulp watch & wait"  
}  
```

## [Use a shorthand syntax for running multiple npm scripts with npm-run-all](https://egghead.io/lessons/tools-use-a-shorthand-syntax-for-running-multiple-npm-scripts-with-npm-run-all)
npm i npm-run-all -D  
By default scripts will run in series, add --parallel switch to run in parallel.  
You don't need **wait** when using npm-run-all.

```json
scripts: {  
  "lint" : "npm-run-all --parallel eslint stylelint",  
  "eslint": "eslint --cache --fix ./",  
  "stylelint" "stylelint '**/*.scss' --syntax scss"  
}  
```
## [Run a set of similar npm scripts with a wildcard](https://egghead.io/lessons/tools-run-a-set-of-similar-npm-scripts-with-a-wildcard)
npm supports a wildcard when running scripts.  
Name scripts with a common prefix, **"lint:"** in the example below.  
Use **"\*"** to match one level, or **"\*\*"** to match multiple levels.

```json
scripts: {  
  "lint" : "npm-run-all lint:**",  
  "lint:js": "eslint --cache --fix ./",  
  lint:css": "stylelint '**/*.scss' --syntax scss",  
  "lint:css:fix": "stylefmt -R src/"  
}  

```
## [Use pre and post npm script lifecycle hooks](https://egghead.io/lessons/tools-use-pre-and-post-npm-script-lifecycle-hooks)
```json
scripts: {  
  "pretest": "npm run lint", 
  "test": "some command to run tests",  
  "lint" : "npm-run-all lint:**",  
  "lint:js": "eslint --cache --fix ./",  
  "lint:css": "stylelint '**/*.scss' --syntax scss",  
  "lint:css:fix": "stylefmt -R src/"  
}  
```

In the example below:  
The **"cover"** script runs tests under the nyc app to collect test coverage data.  
The **"postcover"** script clean up the output generated by nyc.  
The **"cover:open"** script opens the coverage report in a browser.

```json
scripts: {  
  "pretest": "npm run lint",  
  "test": "some command to run tests",   
  "cover": "nyc npm t",  
  "postcover": "rm -rf .nyc_output",  
  "cover:open": "open coverage/index.html",  
  "lint" : "npm-run-all lint:**",  
  "lint:js": "eslint --cache --fix ./",  
  "lint:css": "stylelint '**/*.scss' --syntax scss" , 
  "lint:css:fix": "stylefmt -R src/"  
}  
```


## [Pass arguments to npm scripts](https://egghead.io/lessons/tools-pass-arguments-to-npm-scripts)
Use **--** to pass additional arguments to an npm script.

Say you wanted two versions of some task, perhaps you want a lint script that only lints and a second script to lint and fix.  

```json
scripts: {  
  "lint:js": "eslint --cache ./",  
  "lint:js:fix": "eslint --cache --fix ./"  
}
```  

The issue above that there is now duplicate config for the two commands.

Instead of doing that, the second script can just call the first, passing in the additional flag.
Note you need the additional **--** to tell npm that what follows is the addional flags to be appended to the command.

```json
scripts: {  
  "lint:js": "eslint --cache ./",  
  "lint:js:fix": "npm run lint:js -- --fix"  
}  
```  

## [Pipe data from one npm script to another](https://egghead.io/lessons/tools-pipe-data-from-one-npm-script-to-another)
Use the | character to send the output of one program as the input to another program.  
Use the > character to redirect console output to some file. 

```json
scripts: {  
  "prebuild": "rm -rf public/",
  "build": "npm-run-all build:*",
  "build:html": "pug --obj data.json src/index.pug --out public/", 
  "build:css": "node-sass src/index.scss | postcss -c .postcssrc.json | cssmin > public/index.min.css",
  "build:js": "mustache data.json src/index.mustache.js | uglifyjs > public/index.min.js",
}  
```
## [Run npm scripts when files change with onchange](https://egghead.io/lessons/tools-run-npm-scripts-when-files-change-with-onchange)
Some applications support a --watch flag to look for file changes, but what about those applciations that don't have watch support built in?

Use onchange.

```json
scripts: {
  "test": "script to run tests",
  "watch": "npm-run-all --parallel watch:*",
  "watch:test": "npm t -- -watch",
  "watch:lint": "onchange '**/*.js' '**/*.sass -- npm run lint",
  "lint" : "npm-run-all lint:**",  
  "lint:js": "eslint --cache --fix ./",  
  "lint:css": "stylelint '**/*.scss' --syntax scss" , 
  "lint:css:fix": "stylefmt -R src/"  
}
```

## [Use package variables in npm scripts](https://egghead.io/lessons/tools-use-package-json-variables-in-npm-scripts)
npm exposes the properties defined in the package.json file as enviornment variables you can use in npm scripts.  Things like "name", "version".

**npm run env** -- list all the npm environment variables. package properties begin with **"npm_package_"**

The example below uses the "version" in the package.json file to put the output to a version specific folder in public.  
Note:  These examples use Linux format for using env variables.

```json
scripts: {  
  "prebuild": "rm -rf public/$npm_package_version/",
  "build": "npm-run-all build:*",
  "build:html": "pug --obj data.json src/index.pug --out public/$npm_package_version/", 
  "build:css": "node-sass src/index.scss | postcss -c .postcssrc.json | cssmin > public/index.min.css",
  "build:js": "mustache data.json src/index.mustache.js | uglifyjs > public/$npm_package_version/index.min.js",
}  
```

## [Use custom config settings in npm scripts](https://egghead.io/lessons/tools-use-custom-config-settings-in-your-npm-scripts)

package.json can have a "config" section.  You can use these config settings in your npm scripts.
Config settings are exposed as enviornment variables with an "npm_package_config_" prefix.

npm run env

```json
config: {  
  "port": 1337
},  
scripts: {  
  "poststart" : "npm-run-all buld server",
  "build" : "command to do the build, see examples above",
  "server" : "npm-run-all server:*",  
  "server:start": "http-server public/$npm_package_version -p $npm_package_config_port",
  "server:launch": "open http://localhost:$npm_package_config_port/index.html",
}  
```

You can also override the default config values in the package.json file from the command line.

**npm config set key value** -- override default config setting in package.json.  
**npm config list** -- list current config settings  
**npm config delete key** -- remove override and go back to default config setting.

See [npm-config](https://docs.npmjs.com/cli/config) for more npm config commands for getting and setting config properties.

## [Run npm scripts with git hooks](https://egghead.io/lessons/tools-run-npm-scripts-with-git-hooks)
The npm husky package allows you to create npm scripts that are run before git commands.

**npm install husky -D** 

```json
scripts: {  
  "precommit" : "npm run test",
  "prepush":  "npm run test"
}  
```

Now, when you run your git commit command:

git commit -am "commit message"

husksy will run your tests first and only allow the commit to go through if the tests pass.  You can add a --no-verify flag to the commit command to bypass the tests during the commit.

If you don't want to run the tests before every local commit, use the prepush script instead.  That runs the tests prior to the push to the remote.

## [Change the level of console output when running npm scripts](https://egghead.io/lessons/tools-change-the-level-of-console-output-when-running-npm-scripts)
Ref: [npm-config](https://docs.npmjs.com/misc/config)

When running npm scripts you can pass in additional command line arguments to control the level of output.

npm start --loglevel silent  
npm start -silent  
npm start -s  

Log Levels are:
+    --loglevel silent, -silent, -s
+    --loglevel warn, -quiet, -q
+    --loglevel info, -d
+    --loglevel verbose, -verbose, -dd
+    --loglevel silly, -ddd (can be useful for debugging npm scripts)

## [Make npm scripts cross-environment friendly](https://egghead.io/lessons/tools-make-npm-scripts-cross-environment-friendly)
Need to run your npm scripts in different environment?

### Set an enviornment variable
If you need to set an environment variable in your npm script, the following
only works on Mac and Linux...

```json
scripts: {  
  "test" : "BABEL_ENV=test mocha spec/ --require babel-register",
}  
```
and this only works on windows...

```json
scripts: {  
  "test" : "set BABEL_ENV=test && mocha spec/ --require babel-register",
}  
```

Use **cross-env** to set environment variables in a platform independent manor.
npm install cross-env -d

This now works on Mac, Linux and Windows...
```json
scripts: {  
  "test" : "cross-env BABEL_ENV=test mocha spec/ --require babel-register",
}  
```

### Delete Files
If you need to delete files in your npm script, the following
only works on Mac and Linux...

```json
scripts: {  
  "postcover" : "rm -rf .nyc_output",
}  
```
Use **rimraf** to delete files in a platform independent manor.  
npm install rimraf -d

This now works on Mac, Linux and Windows...
```json
scripts: {  
  "postcover" : "rimraf .nyc_output",
}  
```

### Open Files
If you need to open files, like an index.html file, in your npm script, the following
only works on Mac and Linux...

```json
scripts: {  
  "cover:open" : "open coverage/index.html",
}  
```
Use **opn-cli** to open files in a platform independent manor.  
npm install opn-cli -d

This now works on Mac, Linux and Windows...
```json
scripts: {  
  "cover:open" : "opn coverage/index.html",
}  
```

### Include quotes in script commands
If you need to include quotes in a script command,open files, Linux and Mac will 
accept single quotes...

```json
scripts: {  
  "lint:css": "stylelint '**/*.scss' --syntax scss" , 
}  
```

But on Windows you need to use double quotes and since we are 
already in a string, we need to escape them. This will
also work on Linux and Mac.

```json
scripts: {  
  "lint:css": "stylelint \"**/*.scss\" --syntax scss" , 
}  
```

### Reference an environment variable
If you need to reference an environment variable in an npm script...

On Mac/Linux, use $ to reference an environment variable.
```json
scripts: {  
  "prebuild": "rimraf public/$npm_package_version/",
}  
```

On Windows, use % (before and after) to reference an environment variable.
```json
scripts: {  
  "prebuild": "rimraf public/%npm_package_version%/",
}  
```

Use **cross-var** to reference an environment variable in a platform independent manor.  
npm install cross-var -d

```json
scripts: {  
  "prebuild": "cross-var public/$npm_package_version/",
}  
```

Note: cross-var doesn't work across pipes or output redirects.  
In that case you need to pass cross-var one big string.

```json
scripts: {  
  "build:css": "cross-var \"node-sass src/index.scss | postcss -c .postcssrc.json | cssmin > public/$npm_package_version/index.min.css\"",
}  
```

## [List available npm scripts and support tab completion](https://egghead.io/lessons/tools-list-available-npm-scripts-and-support-tab-completion)
npm run   -- list available scripts

[npm completion](https://docs.npmjs.com/cli/completion) can enable tab completion. See link for Windows. 

Another option is to install the **ntl** (node task list) command...
npm install ntl -g

## [Add comments to your npm scripts](https://egghead.io/lessons/tools-add-comments-to-your-npm-scripts)

Use script name of "//", npm will ignore this script. You can have 
more than one of these. However, "npm run" will only list the last comment.

```json
scripts: {  
  "//": "Helpful comment here!!",
  "start": "node index.js",
  "//": "Another helpful comment here!!",
}  
```
See the [video](https://egghead.io/lessons/tools-add-comments-to-your-npm-scripts) for more options.  (None looked particularly appealing to me.)

## [Pull out npm scripts to another file with p-s](https://egghead.io/lessons/tools-pull-out-npm-scripts-into-another-file-with-p-s)
See video link.

## [Create a bash script to replace a complex npm script](https://egghead.io/lessons/tools-create-a-bash-script-to-replace-a-complex-npm-script)
See video link.

## [Create a node script to replace a complex npm script](https://egghead.io/lessons/tools-create-a-node-script-to-replace-a-complex-npm-script)
See video link.

## npm modules:
These are the npm modules referenced in the lessons.  
**npm install {moduleName} -D**  
> -D add to devDependencies


+ **[eslint](https://www.npmjs.com/package/eslint)**  - lint js file, similar to jslint, jshint
+ **[stylelint](https://www.npmjs.com/package/stylelint)**  - CSS style linter
+ **[npm-run-all](https://www.npmjs.com/package/npm-run-all)**  - Runs multiple npm scripts in series or parallel
+ **[stylefmt](https://github.com/morishitter/stylefmt)**  - Automatically formats CSS files  
+ **[nyc](https://www.npmjs.com/package/nyc)**  - Generate test coverage reports (Babel, Istanbul ???) 
+ **[pug-cli](https://www.npmjs.com/package/pug-cli)**  - Command line interface for Pug, a clean, whitespace-sensitive template language for writing HTML
+ **[node-sass](https://www.npmjs.com/package/node-sass)**  - SASS to CSS compiler
+ **[postcss-cli](https://www.npmjs.com/package/postcss-cli)**  - Command line interface for postcss
+ **[cssmin](https://www.npmjs.com/package/cssmin)**  - CSS minifier
+ **[mustache](https://www.npmjs.com/package/mustache)**  - 
+ **[uglify-js](https://www.npmjs.com/package/uglify-js)**  - Javascript minifier
+ **[onchange](https://www.npmjs.com/package/onchange)**  - Use glob patterns to watch file sets and run an npm script when anything is added, changed or deleted.
+ **[http-server](https://www.npmjs.com/package/http-server)**  - A simple zero-configuration command-line http server for serving up static files.
+ **[husky](https://www.npmjs.com/package/husky)**  - Integrates git hooks with npm scripts
+ **[cross-env](https://www.npmjs.com/package/cross-env)**  - Run commands that set environment variables across platforms
+ **[rimraf](https://www.npmjs.com/package/rimraf)**  - Deleted files across platforms
+ **[opn-cli](https://www.npmjs.com/package/opn-cli)**  - Open files across platforms
+ **[cross-var](https://www.npmjs.com/package/cross-var)**  - Reference environment variables across platform
+ **[ntl](https://www.npmjs.com/package/ntl)**  - Node task list command line app.
+ **[p-s](https://www.npmjs.com/package/p-s)**  - All the benefits of npm scripts without the cost of a bloated package.json and limits of json
  "