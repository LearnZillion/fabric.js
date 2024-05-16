# ILC customization of fabric.js package

The `EraserBrush` mixin from fabric.js is not included in the default package build. In order to use it, we have to rebuild the package. In order to prevent `postinstall` scrips on prod (which are unsafe), we have to host our rebuilt package.

## HOWTO

### Part A: in `learnzillion` repo

* Set new version in package.json: (e.g.:) `"fabric": "5.3.0",`
* Install new version: `yarn install`
* Go to installation: `cd node_modules/fabric`
* Install uglify-js for command line use: `sudo npm install -g uglify-js` (despite being in our dependencies)
* (?) Edit `build.js` L45 to include `url=` in the `source-map` arg: `--source-map url=fabric.min.js.map` (was needed for 5.3 - see https://github.com/mishoo/UglifyJS/issues/1905#issuecomment-300485490)
* Rebuild Fabric: `node build.js modules=ALL requirejs sourcemap exclude=gestures,accessors`

### Part B: in `fabric.js` repo

* Get our Fabric repo if not local already: `git clone git@github.com:LearnZillion/fabric.js.git && cd fabric.js`
* Add upstream: `git remote add upstream https://github.com/fabricjs/fabric.js`
* Check out new version: (e.g.:) `git switch -c upstream v5.3.0`
* Copy `learnzillion/node_modules/fabric/dist/*` to `fabric.js/dist`
* Copy this README into the new branch for the next poor soul who has to do this
* Check in and push new version of fabric.js: `git push -u origin v5.3.0`

### Part C: back in `learnzillion` repo

* Back out new version changes from Part A: `git reset --hard HEAD`
* Update Fabric source in package.json with SHA of new commit: (e.g.:) `"fabric": "LearnZillion/fabric.js#7db80e5bb674b52b0c2f39aa5a124e6c4a77627a",`
* Install new compiled version: `yarn install`
* Check in changes
