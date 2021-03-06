<img src="https://s3.amazonaws.com/web-artefacts/newman-128.png" />

# Newman [![Build Status](https://travis-ci.org/postmanlabs/newman.svg?branch=master)](https://travis-ci.org/postmanlabs/newman) [![Built with Grunt](https://cdn.gruntjs.com/builtwith.png)](http://gruntjs.com/) <a href="https://gitter.im/postmanlabs/newman" target="_blank"><img src="https://badges.gitter.im/Join%20Chat.svg" /></a>

Newman is a command-line collection runner for [Postman](http://getpostman.com). It allows you to effortlessly run and test a Postman collection directly from the command-line. It is built with extensibility in mind so that you can easily integrate it with your continuous integration servers and build systems.

Newman maintains feature parity with Postman and allows you to run collections just the way they are executed inside the collection runner in Postman.

[![NPM](https://nodei.co/npm/newman.png?downloads=true)](https://nodei.co/npm-dl/newman/)


## Supported Node Versions

| Node Version    | Newman Version | Installation Command         |
|-----------------|----------------|------------------------------|
| 0.10.x - 0.12.x | 1.x.x          | `npm install -g newman`      |
| 4.0+            | 2.x.x+         | `npm install -g newman@beta` |

##### Important Notice about the Beta
Keeping in line with the [Postman Sandbox](https://www.getpostman.com/docs/sandbox) roadmap, the next major version of Newman Beta (`v3.0.0+`) will drop support for jQuery and Backbone inside the tests. Any tests that you might have, which use these two libraries might break.

## Getting Started
Newman is built on Node.js. To run Newman, make sure you have Node.js installed. Node.js can be downloaded and installed from [here](http://nodejs.org/download/) on Linux, Windows and Mac OSX.

#### Newman Stable
With that done, Newman is just one command away.
```bash
$ npm install -g newman # installs Newman globally on your system allowing you to run it from anywhere.
```
#### Newman Beta

We've released a beta of Newman v2.0, which supports Node v4.0+ as well. If you wish to install the beta,
```bash
$ npm install -g newman@beta # installs Newman globally on your system allowing you to run it from anywhere.
```
Notes on the Beta:
* The [``jsdom``](https://github.com/tmpvar/jsdom) dependency is no longer listed in package.json. The installation of this dependency has been moved to the postinstall script. This is because a different version of ``jsdom`` is required for node v0.x.x and Node v4.0+.
* The absence of ``jsdom`` from package.json should be specially noted. If you use any automated tools to audit dependencies, etc, this change can cause issues.
* For Windows users, if you are using Node v4.0+, you no longer need to install the Visual Studio Runtime. You do however need to ensure that the Node binary (node.exe) is on your system ``%PATH``.
* In the future, Newman will stop supporting Node v0.x.x, and only support Node version 4.0+. This will considerably reduce installation woes, especially on Windows.

#### Updating Newman
If you already have Newman, you can update with a simple command
```bash
$ npm update -g newman
```

#### Running Newman
The easiest way to run Newman is to run it with a collection. With the `-c` flag you can run any collection file lying on your file-system. Refer [the collection documentation](http://www.getpostman.com/docs/collections) to learn how to use and download collections.

```bash
$ newman -c mycollection.json
```

The `-u` flag allows you to pass a postman collection as a URL. Your collection probably uses environment variables. To provide an accompanying set of environment variables, [export them from Postman](http://www.getpostman.com/docs/environments)  and run them with the `-e` flag.
```bash
$ newman -u https://www.getpostman.com/collections/cb208e7e64056f5294e5 -e devenvironment.json
```

## Options
Newman provides a rich set of options to customize a run. A list of options can be retrieved by running it with the `-h` flag.

```bash
$ newman -h

Options:

Utility:
-h, --help                  output usage information
-V, --version               output the version number

Basic setup:
-c, --collection [file]     	Specify a Postman collection as a JSON [file]
-u, --url [url]             	Specify a Postman collection as a [url]
-f, --folder [folderName]   	Specify a single folder to run from a collection. To be used with -c or -u.
-e, --environment [file]    	Specify a Postman environment as a JSON [file]
-d, --data [file]           	Specify a data file to use either json or csv
-g, --global [file]         	Specify a Postman globals file as JSON [file]
-n, --number [number]       	Define the number of iterations to run
-i, --import [file]         	Import a Postman backup file, and save collections, environments, and globals. [file]
-p, --pretty                	(Use with -i) Enable pretty-print while saving imported collections, environments, and globals
-G, --exportGlobals [file]      Specify an output file to dump Globals before exiting [file]
-E, --exportEnvironment [file]  Specify an output file to dump the Postman environment before exiting [file]

Request options:
-y, --delay [number]            Specify a delay (in ms) between requests [number]
-r, --requestTimeout [number]   Specify a request timeout (in ms) for a request

Misc.:
-s, --stopOnError           Stops the runner when a test case fails
-j, --noSummary             Doesn't show the summary for each iteration
-C, --noColor               Disable colored output
-k, --insecure              Disable strict ssl
-l, --tls                   Use TLSv1
-x, --exitCode              Continue running tests even after a failure, but exit with code=1
-W, --whiteScreen           Black text for white screen


Output:
-o, --outputFile [file]                 Path to file where output should be written. [file]
-t, --testReportFile [file]             Path to file where results should be written as JUnit XML [file]
-H, --html                              Export a HTML report to a specified file [file]
-O, --outputFileVerbose [file]          Path to file where full request and responses should be logged [file]

```

Use the `-n` option to set the number of iterations you want to run the collection for.

```bash
$ newman -c mycollection.json -n 10  # runs the collection 10 times
```

To provide a different set of data i.e. variables for each iteration you can use the `-d` to specify a `json` or `csv` file. For example, a data file such as the one shown below will run *2* iterations, with each iteration using a set of variables.
```javascript
[{
	"url": "http://127.0.0.1:5000",
	"user_id": "1",
	"id": "1",
	"token_id": "123123",
},
{
	"url": "http://dump.getpostman.com",
	"user_id": "2",
	"id": "2",
	"token_id": "899899",
}]
```

```bash
$ newman -c mycollection.json -d data.json
```

The csv file for the above set of variables would look like
```
url, user_id, id, token_id
http://127.0.0.1:5000, 1, 1, 123123123
http://dump.getpostman.com, 2, 2, 899899
```

Newman, by default exits with a status code of 0 if everything runs well i.e. without any exceptions. Continuous integration tools respond to these exit codes and correspondingly pass or fail a build. You can use `-s` flag to tell Newman to halt on a test case error with a status code of 1 which can then be picked up by a CI tool or build system.

```bash
$ newman -c PostmanCollection.json -e environment.json -s

Iteration 1 of 1
200 17ms Blog posts http://127.0.0.1:5000/blog/posts
    ✔ Status code is 200
404 5ms Blog post http://127.0.0.1:5000/blog/posts/1
200 4ms New post without token http://127.0.0.1:5000/blog/posts
    ✔ Body has a message
    ✔ invalid credentials
Test case failed: Status code is 404
```

The results of all tests and requests can be exported into file and later imported in Postman for further analysis. Use the `-o` flag and a file name to save the runner output into a file.

```bash
$ newman -c mycollection.json -o outputfile.json
```

Newman can also be used to import a Postman backup file. The collections, environments, and globals will be saved to the 'data' folder. (Use the -p option to enable pretty-print)
```bash
newman -i /path/to/Backup.json -p
```



**NOTE** Newman allows you to use all [libraries](http://www.getpostman.com/docs/jetpacks_writing_tests) that Postman supports for running tests. For [x2js](https://code.google.com/p/x2js/) however, only  function `xmlToJson` is supported.

## Library
Newman has been built as a library from the ground-up so that it can be extended and put to varied uses. You can use it like so -

```javascript
var Newman = require('newman');

// read the collectionjson file
var collectionJson = JSON5.parse(fs.readFileSync("collection.json", 'utf8'));

// define Newman options
newmanOptions = {
	envJson: JSON5.parse(fs.readFileSync("envjson.json", "utf-8")), // environment file (in parsed json format)
	dataFile: data.csv,                    // data file if required
	iterationCount: 10,                    // define the number of times the runner should run
	outputFile: "outfile.json",            // the file to export to
	responseHandler: "TestResponseHandler", // the response handler to use
	asLibrary: true,         				// this makes sure the exit code is returned as an argument to the callback function
	summary: true							// displays the summary at the end of the iteration
	stopOnError: true,
}

// Optional Callback function which will be executed once Newman is done executing all its tasks.
Newman.execute(collectionJson, newmanOptions, callback);
```


## Cron
Want your test suite to run every hour? Newman can be used to schedule tests to run hourly, daily or weekly automatically in combination with the awesome Unix scheduler **CRON**.

Lets setup a simple script called `run_newman` to run our tests
```bash
#!/bin/bash

timestamp=$(date +"%s")
collection=/var/www/myapp/tests/collection.json
env=/var/www/myapp/tests/envfile.json

# create separate outfile for each run
outfile=/var/www/myapp/tests/outfile-$timestamp.json

# redirect all output to /dev/null
newman -c $collection -c $env -o $outfile > /dev/null2>&1
```
Make it an executable
```bash
$ chmod +x run_newman
```

To run Newman every hour, run `crontab -e` and enter the following -
```bash
0 * * * * /path/to/run_newman
```
Check your `cron` if it has been setup
```bash
$ crontab -l
0 * * * * /path/to/run_newman
```
With this, your Newman is set to run automatically every hour.

Note: Exact location for `cron` is dependent on the linux distribution you are running. See specific `cron` instructions for your distribution. For an introduction to `cron` checkout [this](http://code.tutsplus.com/tutorials/scheduling-tasks-with-cron-jobs--net-8800) article.

## License
Apache-2.0. See the LICENSE file for more information

[![Analytics](https://ga-beacon.appspot.com/UA-43979731-9/newman/readme)](https://www.getpostman.com)
