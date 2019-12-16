[![Build Status](https://travis-ci.org/google/oatts.svg?branch=master)](https://travis-ci.org/google/oatts)

# OpenAPI Test Templates for cypress (oatts-cypress)

> Generate basic cypress snapshot test scaffolding for your [OpenAPI specification](https://www.openapis.org/).

## Disclaimer

`oatts-cypress` is based off of the [oatts](https://github.com/google/oatts) module and the lessons learned during its development.

_This is a work in progress._

## Goal

The goal of `oatts-cypress` is to provide a standalone module for generating cypress snapshot test code scaffolding based on a given OpenAPI specification.

The hope is that by providing such a tool, API developers will be encouraged to test the contract between their spec and backend early, often and continuously as the project grows.

## Usage

There are a couple ways to use `oatts-cypress`.

### Module

Install via `npm`

    npm install --save oatts-cypress


Then use it in code
```js
var oatts = require('oatts-cypress');

var options = {
    // see "Options" section below for available options
};

var tests = oatts.generate('/path/to/openapi.yaml', options);

console.log(tests)
```

### Command line interface

Install globally via `npm`

    npm install -g oatts-cypress


Then use in your command line
```sh
> oatts generate --help

  Usage: generate [options]

  generate unit test scaffolding for a given OpenAPI/Swagger Spec

  Options:

    -h, --help                             output usage information
    --host <host>                          target hostname to use in test generation
    -p, --paths <paths>                    comma separated list of paths to generate tests for
    -e, --samples                          generate sample response bodies rather than schema, if applicable
    -s, --spec <spec>                      path to the target OpenAPI/Swagger spec document to consume
    -w, --writeTo <writeTo>                directory to write the generated tests to file
    -c, --consumes <consumes>              consumes/content-type to use in request when applicable to the API resource
    -o, --produces <produces>              produces/accept to use in request when applicable to the API resource
    -u, --customValues <customValues>      custom request values to be used in generation; takes precedent over a customValuesFile
    --customValuesFile <customValuesFile>  path to JSON file with custom request values to be used in generation
    -m, --scheme <scheme>                  which scheme to use if multiple are present in spec
    -t --templates <templateDir>           path to direcotry of custom templates
    -S, --status-codes <statusCodes>       comma separated list of status codes to explicity generate tests for

> oatts generate -s ./path/to/openapi.yaml -w ./output/dir
> ls ./output/dir
pet-test.js  pet-{petId}-uploadImage-test.js  user-test.js 
. . .
```

### Using the result

The resulting test files are built using the [cypress snapshot](https://www.cypress.io/blog/2018/01/16/end-to-end-snapshot-testing/) 

Please follow the above link to setup your cypress project after generating the tests

### Custom Values
Custom values can be supplied through both the command line and a JSON file. The in-line, command line supplied JSON will take precedent.

An example custom values JSON file can be found [here](./test/process/documents/customValuesTest.json).

### Custom Templates
Custom templates can be supplied via the `templates` option. The directory pointed to by the option must contain 4 [Handlebars](http://handlebarsjs.com/) templates named the same way as those found in `./templates`.

* `topLevel.handlebars`: the top level template for a single test file
* `pathLevel.handlebars`: the path level template, usually the beginning of a test suite for a specific path
* `operationLevel.handlebars`: the operation level template, for a single operation test suite
* `transactionLevel.handlebars`: the template for a single transaction, or a single response code's unit test

The data available to be used in the templates is specified in the `ProcessedSpec` type.

There are also a few helpers available to be used in the Handlebars templates, which can be found in the `templateHelpers` documentation namespace. Use the default templates as examples of how to use them.

## Options

The following options can be passed to the generation function, some/all are exposed in the accompanying CLI:

| Name | CLI Flag | Default | Required | Description |
| ---- |:--------:| -------:| --------:| -----------:|
| `spec` | `--spec -s` | n/a | `true` | Path to a `swagger.yaml` or `openapi.yaml` |
| `host` | `--host` | `spec.host` | `false` | Hostname to put in test requests; defaults to `host` in given spec |
| `paths` | `--paths -p` | `spec.paths` | `false` | API paths to generate tests for; defaults to all paths in given spec |
| `samples` | `--samples -e` | `false` | `false` | Toggle generating sample responses for assertion |
| `writeTo` | `--writeTo -w` | n/a | `false` | Directory to write generated tests to; will create the directory if it doesn't exist |
| `consumes` | `--consumes -c` | `operation.consumes[0]` &#124; &#124; `spec.conumes[0]` | `false` | Consumes header to use in a request when applicable | 
| `produces` | `--produces -o` | `operation.produces[0]` &#124; &#124; `spec.produces[0]` | `false` | Produces header to use in a request when applicable |
| `customValues` | `--customValues -u` | n/a | `false` | Values to be populated in requests where specified; overrides `customValuesFile` |
| `customValuesFile` | `--customValuesFile` | n/a | `false` | Path to a JSON file with values to populate in requests |
| `scheme` | `--scheme -m` | `spec.schemes[0]` | `false` | Override for multiple scheme present in a spec |
| `templates` | `--templates -t` | `'./templates'` | `false` | Path to directory containing custom templates |
| `statusCodes` |`--status-codes -S` | `operation.responses` | `false` | comma separated list of status codes to explicity generate tests for |
| `jsonRefs` | | n/a | `false` | *(See [JsonRefs~JsonRefsOptions](https://github.com/whitlockjc/json-refs/blob/master/docs/API.md#module_JsonRefs..JsonRefsOptions))* |
| `customFormats` | | n/a | `false` | The key/value pair of custom formats *(The keys are the format name and the values are async functions.  See [ZSchema Custom Formats](https://github.com/zaggino/z-schema#register-a-custom-format))* |
| `customFormatGenerators` | | n/a | `false` | The key/value pair of custom format generators *(The keys are the format name and the values are functions.  See [json-schema-mocker Custom Format](https://github.com/json-schema-faker/json-schema-faker#custom-formats))* |
| `customValidators` | | n/a | `false` | The custom validators. See [DocumentValidationFunction](https://github.com/apigee-127/sway/blob/master/docs/API.md#module_sway.DocumentValidationFunction) |

## Testing

To test this module simply use the `npm` script

    npm test

## Contributing

Contributors are welcome! Please see [CONTRIBUTING.md](CONTRIBUTING.md).

## License

See [LICENSE](LICENSE) file.
