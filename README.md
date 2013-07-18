# Webmaker Suite bootstrap/installation script

Step 1: Clone this repo

Step 2: Run `node install`

Step 3: Go make some coffee, this'll run for a while...

After installation completes, the whole suite can be fired up with `node run`. Note that this will pipe all output from everything into the same console, so this is great for not-looking-at-the-terminal testing, but not a good idea for doing single-component terminal debugging.

**NOTE** it looks like login.webmaker.org somehow triggers a "too many files open" error when doing an automated `npm install`. Not sure what causes this (yet) but for the moment you'll have to go into the login.webmaker.org dir and type `npm install` manually.

## Script options

### install the suite

`node install-webmaker` can take four runtime options:

1. --username=...
2. --password=...
3. --s3key=...
4. --s3secret=...

These do the obvious thing. If you do not use these, and do not have an .env file (which initially you won't), you will be prompted for them during the bootstrap phase, after `npm install` for the webmaker-suite package itself finishes.


### update the suite

`node update-webmaker` will update all repositories to their current `master` branch. It effectively runs the following commands for each repository:

1. `git fetch mozilla`
2. `git checkout -B master mozilla/master`
3. `rm -rf node_modules`
4. `npm install`
5. `npm cache clean`

### node run

`node run` will run all apps in the webmaker suite. It can take two runtime options:

1. --noes
2. --nomongo
3. --exclude=[comma separated list of names]

The `--exclude` flag takes a comma separated list of app names that should not be started up. If you wish to test only the MakeAPI, for instance, you could issue `node run --exclude=goggles,thimble,popcorn,login,webmaker.org,htmlsanitizer.org`. Note that, for convenience, app names that are of the form "xyz.webmaker.org" can be excluded using only the "xyz" part of the name.


These mostly exist to prevent double-starts for elastic search and mongodb, if you already run these on your machine, since `node run` will try to fire these up for you.


## General requirements

* node.js - http://nodejs.org/
* npm - comes with node.js
* python - http://python.org/ (pretty sure we depend on 2.7)
* pip - `easy_setup pip` (if command unknown, run http://peak.telecommunity.com/dist/ez_setup.py through python, first)
* mongodb - http://www.mongodb.org/downloads (or through your favourite package manager)
* elastic search - http://www.elasticsearch.org (or through your favourite package manager)


### Windows users should note:

Windows users need to install these two things first (in order):

1. Microsoft Visual Studio C++ 2010 Express, http://go.microsoft.com/?linkid=9709949
2. For Windows 7 x64 (which obviously you're on), the Windows 7 64-bit SDK, http://www.microsoft.com/en-us/download/details.aspx?id=8279

(Without this VC++ stack, node-gyp will crash the `npm install` process when it gets to `sqlite3` and tries to build it without having access to the windows C++ compiler and header files. Since several apps rely on sqlite3 for localhost work, you need these).

## OSX convenience

You will need the X-Code command line utilities. These are free through Apple's developer appstore. You will also want `brew`, which is a package manager for OSX.

With these, install mongodb and elasticsearch using `brew`, and you should be all set. Convenient!

(If you elect to run these on startup, you will want to make sure to use `node run --noes --nomongo` when you use the suite runner, or `run.js` will try to double-start ES and MongoDB)

## Windows particulars

If you're on windows, MongoDB and Elastic Search require manual installation, which means putting them in their own folders and extending your PATH:

#### MongoDB on Windows

Download mongodb as a zipfile, create a `MongoDB` folder for it in your program files folder, and copy `mongo.exe`, `mongod.exe` etc. into it. Then add the MongoDB folder to your PATH variable (in windows 7, that's control panel -> system -> advanced system settings -> environment variables -> system variables section).

#### Elastic Search on Windows

Download elastic search, create a folder `C:\elastisearch` (I am not kidding, ES has the worst restrictions), and unpack the .zip package into it. Elastic Search requires Java, so if you don't have that install, grab the most recent JDK and install it. Then symlink the jdk dir as `c:\java`. Again, I am not joking; ES died on spaces anywhere. To do this, open a `cmd` box with administrative rights, to go `C:\` and type:

`C:\> mklink /D java "c:\Program Files (x86)\Java\jdk1.6.0_17"`

(to remove that at some longer point, simply delete the `C:\java` dir. It will unlink, rather than delete your jdk files)

Also add `C:\elasticsearch\bin` to your PATH variable.
