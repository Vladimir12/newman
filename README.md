[![Build Status](https://travis-ci.org/postmanlabs/newman.svg?branch=master)](https://travis-ci.org/postmanlabs/newman)
[![Dependency Status](https://david-dm.org/postmanlabs/newman.svg)](https://david-dm.org/postmanlabs/newman)
[![devDependency Status](https://david-dm.org/postmanlabs/newman/dev-status.svg)](https://david-dm.org/postmanlabs/newman#info=devDependencies)

<a href="https://www.getpostman.com/"><img src="https://raw.githubusercontent.com/postmanlabs/postmanlabs.github.io/develop/global-artefacts/postman-logo%2Btext-320x132.png" /></a><br />
_Supercharge your API workflow<br/>Modern software is built on APIs. Postman helps you develop APIs faster._

# newman <sub>_the cli companion for postman_</sub>

Using Newman, one can effortlessly run and test a Postman Collections directly from the command-line. It is built with
extensibility in mind so that you can easily integrate it into your continuous integration servers and build systems.

## IMPORTANT NOTICE

> ### **Newman has been recently upgraded to v3.x.** Although most options from v2.x work as expected, some of them have been deprecated and are scheduled to be discontinued soon. We strongly advise you to migrate to the new v3.x CLI options by referring to our [Newman v2 to v3 Migration Guide](MIGRATION.md)
>
> To view documentation of current stable 2.x release of Newman, refer the
> [Newman v2.x README](https://github.com/postmanlabs/newman/blob/release/2.x/README.md) of 2.x release.

## Getting started

To run Newman, ensure that you have NodeJS >= v4. A copy of the NodeJS installable can be downloaded from [https://nodejs.org/en/download/package-manager/](https://nodejs.org/en/download/package-manager).

The easiest way to install Newman is using NPM. If you have NodeJS installed, it is most likely that you have NPM
installed as well.

```terminal
$ npm install newman --global;
```

The `newman run` command allows you to specify a collection to be run. You can easily export your Postman
Collection as a json file from the [Postman App](https://www.getpostman.com/apps) and run it using Newman.

```terminal
$ newman run examples/sample-collection.json;
```

If your collection file is available as an URL (such as from our [Cloud API service](https://api.getpostman.com)),
Newman can fetch your file and run it as well.

```terminal
$ newman run https://www.getpostman.com/collections/631643-f695cab7-6878-eb55-7943-ad88e1ccfd65-JsLv;
```

For the complete list of options, refer the [Commandline Options](#commandline-options) section below.

![terminal-demo](https://raw.githubusercontent.com/postmanlabs/postmanlabs.github.io/develop/global-artefacts/newman-terminal.gif)

### Using Newman as a NodeJS module

Newman can be easily used within your JavaScript projects as a NodeJS module. The entire set of Newman CLI functionality is available for programmatic use as well.

The following example runs a collection by reading a JSON collection file stored on disk.

```javascript
var newman = require('newman'); // require newman in your project

// call newman.run to pass `options` object and wait for callback
newman.run({
    collection: require('./sample-collection.json'),
    reporters: 'cli'
}, function (err) {
	if (err) { throw err; }
    console.log('collection run complete!');
});
```

**Note:** The newman v2.x `.execute` function has been discontinued.

## Commandline Options

### `newman run <collection-file-source> [options]`

- `-e <source>`, `--environment <source>`<br />
  Specify an environment file path or URL. Environments provide a set of variables that one can use within collections.
  [Read More](https://www.getpostman.com/docs/environments)

- `-g <source>`, `--globals <source>`<br />
  Specify file path or URL for global variables. Global variables are similar to environment variables but has a lower
  precedence and can be overridden by environment variables having same name.

- `-d <source>`, `--iteration-data <source>`<br />
  Specify a data source file (CSV) to be used for iteration as a path to a file or as a URL.
  [Read More](https://www.getpostman.com/docs/multiple_instances)

- `-n <number`, `--iteration-count <number>`<br />
  Specifies the number of times the collection has to be run when used in conjunction with iteration data file.

- `--folder <name>`<br />
  Run requests within a particular folder in a collection.

- `--export-environment <path>`<br />
  The path to the file where Newman will output the final environment variables file before completing a run.

- `--export-globals <path>`<br />
  The path to the file where Newman will output the final global variables file before completing a run.

- `--export-collection <path>`<br />
  The path to the file where Newman will output the final collection file before completing a run.

- `--timeout-request <ms>`<br />
  Specify the time (in milliseconds) to wait for requests to return a response.

- `-k --insecure`<br />
  Disables SSL verification checks and allows self-signed SSL certificates.

- `--ignore-redirects`<br />
  Prevents newman from automatically following 3XX redirect responses.

- `--delay-request`<br />
  Specify the extent of delay between requests (milliseconds).

- `--bail`<br />
  Specify whether or not to stop a collection run on encountering the first error.

- `-x --suppress-exit-code`<br />
  Specify whether or not to override the default exit code for the current run.

- `--no-color`<br />
  Newman attempts to automatically turn off color output to terminals when it detects the lack of color support. With
  this property, one can forcibly turn off the usage of color in terminal output for reporters and other parts of Newman
  that output to console.

#### Configuring Reporters

- `-r --reporters <name>`<br />
  Specify one reporter name as `string` or provide more than one reporter name as a comma separated list of reporter names. Available reporters are: `cli`, `json`, `html` and `junit`.

- `--reporter-{{reporter-name}}-{{reporter-options}}`<br />
  When multiple reporters are provided, if one needs to specifically override or provide an option to one reporter, this
  is achieved by prefixing the option with `--reporter-{{reporter-name}}-`.<br /><br />
  For example, `... --reporters cli,html --reporter-cli-silent` would silence the CLI reporter only.

- `--reporter-{{reporter-options}}`<br />
  If more than one reporter accepts the same option name, they can be provided using the common reporter option syntax.
  <br /<br />
  For example, `... --reporters cli,html --reporter-silent` passes the `silent: true` option to both HTML and CLI
  reporter.

##### CLI reporter options
These options are supported by the CLI reporter, use them with appropriate argument switch prefix. For example, the
option `no-summary` can be passed as `--reporter-no-summary` or `--reporter-cli-no-summary`.

CLI reporter is enabled by default, you do not need to specifically provide the same as part of `--reporters` option.

| CLI Option  | Description       |
|-------------|-------------------|
| `--reporter-cli-silent`         | The CLI reporter is internally disabled and you see no output to terminal. |
| `--reporter-cli-no-summary`     | The statistical summary table is not shown. |
| `--reporter-cli-no-failures`    | This prevents the run failures from being separately printed. |
| `--reporter-cli-no-assertions`  | This turns off the request-wise output as they happen. |
| `--reporter-cli-no-console`     | This turns off the output of `console.log` (and other console calls) from collection's scripts. |

##### JSON reporter options
The built-in JSON reporter is useful in producing a comprehensive output of the run summary. It takes the path to the
file where to write the file. The content of this file is exactly same as the `summary` parameter sent to the callback
when Newman is used as a library.

To enable JSON reporter, provide `--reporters json` as a CLI option.

| CLI Option  | Description       |
|-------------|-------------------|
| `--reporter-json-export <path>` | Specify a path where the output JSON file will be written to disk. If not specified, the file will be written to `newman/` in the current working directory. |


##### HTML reporter options
The built-in HTML reporter produces and HTML output file outlining the summary and report of the Newman run. To enable
JSON reporter, provide `--reporters html` as a CLI option.

| CLI Option  | Description       |
|-------------|-------------------|
| `--reporter-html-export <path>` | Specify a path where the output HTML file will be written to disk. If not specified, the file will be written to `newman/` in the current working directory. |

##### JUNIT/XML reporter options
Newman can output a summary of the collection run to a JUnit compatible XML file. To enable JSON reporter, provide
`--reporters html` as a CLI option.

| CLI Option  | Description       |
|-------------|-------------------|
| `--reporter-junit-export <path>` | Specify a path where the output XML file will be written to disk. If not specified, the file will be written to `newman/` in the current working directory. |

Older command line options are supported, but are deprecated in favour of the newer v3 options and will soon be
discontinued. For documentation on the older command options, refer to
[README.md for Newman v2.X](https://github.com/postmanlabs/newman/blob/release/2.x/README.md).

### `newman [options]`

- `-h`, `--help`<br />
  Show commandline help, including a list of options, and sample use cases.

- `--version`<br />
  Displays the current Newman version, taken from [package.json](https://github.com/postmanlabs/newman/blob/master/package.json)


## API Reference

### newman.run(options: _object_ , callback: _function_) => run: EventEmitter
The `run` function executes a collection and returns the run result to a callback function provided as parameter. The
return of the `newman.run` function is a run instance, which emits run events that can be listened to.

| Parameter | Description   |
|-----------|---------------|
| options                   | This is a required argument and it contains all information pertaining to running a collection.<br /><br />_Required_<br />Type: `object` |
| options.collection        | The collection is a required property of the `options` argument. It accepts an object representation of a Postman Collection which should resemble the schema mentioned at [https://schema.getpostman.com/](https://schema.getpostman.com/). The value of this property could also be an istance of Collection Object from the [Postman Collection SDK](https://github.com/postmanlabs/postman-collection).<br /><br />As `string`, one can provide a URL where the Collection JSON can be found (e.g. [Postman Cloud API](https://api.getpostman.com/) service) or path to a local JSON file.<br /><br />_Required_<br />Type: `object|string|`[PostmanCollection](https://github.com/postmanlabs/postman-collection/wiki#Collection) |
| options.environment       | One can optionally pass an environment file path or URL as `string` to this property and that will be used to read Postman Environment Variables from. This property also accepts environment variables as an `object`. Environment files exported from Postman App can be directly used here.<br /><br />_Optional_<br />Type: `object|string` |
| options.globals           | Postman Global Variables can be optionally passed on to a collection run in form of path to a file or URL. It also accepts variables as an `object`.<br /><br />_Optional_<br />Type: `object|string` |
| options.iterationCount    | Specify the number of iterations to run on the collection. This is usually accompanied by providing a data file reference as `options.iterationData`.<br /><br />_Optional_<br />Type: `number`, Default value: `1` |
| options.iterationData     | Path to the JSON or CSV file or URL to be used as data source when running multiple iterations on a collection.<br /><br />_Optional_<br />Type: `string` |
| options.folder            | The name or ID of the folder (ItemGroup) in the collection which would be run instead of the entire collection.<br /><br />_Optional_<br />Type: `string` |
| options.timeoutRequest    | Specify the time (in milliseconds) to wait for requests to return a response.<br /><br />_Optional_<br />Type: `number`, Default value: `Infinity` |
| options.delayRequest      | Specify the time (in milliseconds) to wait for between subsequent requests.<br /><br />_Optional_<br />Type: `number`, Default value: `0` |
| options.ignoreRedirects   | This specifies whether newman would automatically follow 3xx responses from servers.<br /><br />_Optional_<br />Type: `boolean`, Default value: `false` |
| options.insecure          | Disables SSL verification checks and allows self-signed SSL certificates.<br /><br />_Optional_<br />Type: `boolean`, Default value: `false` |
| options.bail              | A boolean switch to specify whether or not to gracefully stop a collection run on encountering the first error. Takes no arguments.<br /><br />_Optional_<br />Type: `boolean`, Default value: `false` |
| options.suppressExitCode  | If present, allows overriding the default exit code from the current collection run, useful for bypassing collection result failures. Takes no arguments.<br /><br />_Optional_<br />Type: `boolean`, Default value: `false` |
| options.reporters         | Specify one reporter name as `string` or provide more than one reporter name as an `array`.<br /><br />Available reporters: `cli`, `html` and `junit`.<br /><br />_Optional_<br />Type: `string|array` |
| options.noColor           | Newman attempts to automatically turn off color output to terminals when it detects the lack of color support. With this property, one can forcibly turn off the usage of color in terminal output for reporters and other parts of Newman that output to console.<br /><br />_Optional_<br />Type: `boolean` |
| callback                  | Upon completion of the run, this callback is executed with the `error`, `summary` argument.<br /><br />_Required_<br />Type: `function` |

### newman.run~callback(error: _object_ , summary: _object_)

The `callback` parameter of the `newman.run` function receives two arguments: (1) `error` and (2) `summary`

| Argument  | Description   |
|-----------|---------------|
| error                     | In case newman faces an error during the run, the error is passed on to this argument of callback. By default, only fatal errors, such as the ones caused by any fault inside Newman is passed on to this argument. However, setting `abortOnError:true` or `abortOnFailure:true` as part of run options will cause newman to treat collection script syntax errors and test failures as fatal errors and be passed down here while stopping the run abruptly at that point.<br /><br />Type: `object` |
| summary                   | The run summary will contain information pertaining to the run.<br /><br />Type: `object` |
| summary.error             | An error object which if exists, contains an error message describing the message <br /><br />Type: `object` |
| summary.collection        | This object contains information about the collection being run, it's requests, and their associated pre-request scripts and tests.<br /><br />Type: `object` |
| summary.environment       | An object with environment variables used for the current run, and the usage status for each of those variables.<br /><br />Type: `object` |
| summary.globals           | This object holds details about the globals used within the collection run namespace.<br /><br />Type: `object` |
| summary.run               | A cumulative run summary object that provides information on .<br /><br />Type: `object` |
| summary.run.stats         | An object which provides details about the total, failed, and pending counts for pre request scripts, tests, assertions, requests, and more.<br /><br />Type: `object` |
| summary.run.failures      | An array of failure objects, with each element holding details, including the assertion that failed, and the request.<br /><br />Type: `array.<object>` |
| summary.run.executions    | This object contains information about each request, along with it's associated activities within the scope of the current collection run.<br /><br />Type: `array.<object>` |

### newman.run~events

Newman triggers a whole bunch of events during the run.

```javascript
newman.run({
    collection: require('./sample-collection.json')
}).on('start', function (err, args) { // on start of run, log to console
    console.log('running a collection...');
}).on('done', function (err, summary) {
    if (error || summary.error) {
        console.error('collection run encountered an error.');
    }
    else {
        console.log('collection run completed.');
    }
});
```

All events receive two arguments (1) `error` and (2) `args`. **The list below describes the properties of the second
argument object.**

| Event     | Description   |
|-----------|---------------|
| start                     | The start of a collection run |
| beforeIteration           | Before an iteration commences |
| beforeItem                | Before an item execution begins (the set of prerequest->request->test) |
| beforePrerequest          | Before `prerequest` script is execution starts |
| prerequest                | After `prerequest` script execution completes |
| beforeRequest             | Before an HTTP request is sent |
| request                   | After response of the request is received |
| beforeTest                | Before `test` script is execution starts |
| test                      | After `test` script execution completes |
| beforeScript              | Before any script (of type `test` or `prerequest`) is executed |
| script                    | After any script (of type `test` or `prerequest`) is executed |
| item                      | When an item (the whole set of prerequest->request->test) completes |
| iteration                 | After an iteration completes |
| assertion                 | This event is triggered for every test assertion done within `test` scripts |
| console                   | Every time a `console` function is called from within any script, this event is propagated |
| exception                 | When any asynchronous error happen in `scripts` this event is triggered |
| beforeDone                | An event that is triggered prior to the completion of the run |
| done                      | This event is emitted when a collection run has completed, with or without errors |

<!-- TODO: write about callback summary -->

## Using Newman with the Postman Cloud API

1 [Generate an API key](https://app.getpostman.com/dashboard/integrations)<br/>
2 Fetch a list of your collections from: `https://api.getpostman.com/collections?apikey=$apiKey`<br/>
3 Get the collection link via it's `uid`: `https://api.getpostman.com/collections/$uid?apikey=$apiKey`<br/>
4 Obtain the environment URI from: `https://api.getpostman.com/environments?apikey=$apiKey`<br/>
5 Using the collection and environment URIs acquired in steps 3 and 4, run the collection as follows:
```
newman run <collectionUri> --environment <environmentUri>
```

## Community Support

<img src="https://www.getpostman.com/img/v2/icons/slack.svg" align="right" />
If you are interested in talking to the team and other Newman users, we are there on <a href="https://www.getpostman.com/slack-invite" target="_blank">Slack</a>. Feel free to drop by and say hello. Our upcoming features and beta releases are discussed here along with world peace.

Get your invitation for Postman Slack Community from: <a href="https://www.getpostman.com/slack-invite">https://www.getpostman.com/slack-invite</a>.<br />
Already member? Sign in at <a href="https://postmancommunity.slack.com">https://postmancommunity.slack.com</a>

## License
This software is licensed under Apache-2.0. Copyright Postdot Technologies, Inc. See the [LICENSE.md](LICENSE.md) file for more information.

[![Analytics](https://ga-beacon.appspot.com/UA-43979731-9/newman/readme)](https://www.getpostman.com)
