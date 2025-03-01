---
title: Functional Testing Framework CLI commands | Commerce Testing
description: Learn about the bin/mftf command-line interface (CLI) tool for the Functional Testing Framework.
keywords:
  - Tools
---

# CLI commands: vendor/bin/mftf

The Functional Testing Framework (MFTF) introduces the command line interface (CLI) tool `vendor/bin/mftf` to facilitate your interaction with the framework.

Note that `mftf` commands replace the `robo` commands that were used in previous releases.

## Command format

In the project root directory (where you have installed the framework as a composer dependency), run commands using the following format:

```bash
vendor/bin/mftf command [options] [<arguments>] [--remove|-r]
```

## Useful commands

Use the following commands to run commonly performed tasks.

### Apply the configuration parameters

```bash
vendor/bin/mftf build:project
```

### Upgrade the project

```bash
vendor/bin/mftf build:project --upgrade
```

Upgrades all installed test after a major Functional Testing Framework upgrade.

### Generate all tests

```bash
vendor/bin/mftf generate:tests
```

### Generate tests by test name

```bash
vendor/bin/mftf generate:tests AdminLoginSuccessfulTest StorefrontPersistedCustomerLoginTest
```

### Generate tests by testNames.txt file

```bash
vendor/bin/mftf generate:tests -p path/to/your/testNames.txt
```

This command generate all tests specified in a testNames.txt file.

#### Example

```bash
testName1
testName2
testNameN
suiteName:testInSuite
```

### Generate test by test and suite name

```bash
vendor/bin/mftf generate:tests WYSIWYGDisabledSuite:AdminCMSPageCreatePageTest
```

### Generate test dependencies

```bash
vendor/bin/mftf generate:tests -l testEntityJson
```

This command generate json file consist of all test dependent module.

### Generate test dependencies by test name

```bash
vendor/bin/mftf generate:tests testName1  testName2 .. testNameN -l testEntityJson
```

### Generate and run the tests for a specified group

```bash
vendor/bin/mftf run:group product -r
```

This command cleans up the previously generated tests; generates and runs tests for the product group (where `group="product"`).

### Generate and run particular tests

```bash
vendor/bin/mftf run:test AdminLoginSuccessfulTest StorefrontPersistedCustomerLoginTest -r
```

This command cleans up the previously generated tests; generates and runs the `AdminLoginSuccessfulTest` and `StorefrontPersistedCustomerLoginTest` tests.

### Generate and run particular test in a specific suite's context

```bash
vendor/bin/mftf run:test WYSIWYGDisabledSuite:AdminCMSPageCreatePageTest -r
```

This command cleans up previously generated tests; generates and run `AdminCMSPageCreatePageTest` within the context of the `WYSIWYGDisabledSuite`.

### Generate and run a testManifest.txt file

```bash
vendor/bin/mftf run:manifest path/to/your/testManifest.txt
```

This command runs all tests specified in a `testManifest.txt` file. When you generate tests, a `testManifest.txt` file is also generated for you. You can pass this file directly to the `run:manifest` command and it will execute all listed tests. You can also create your own file in the same format to execute a subset of tests. Note: This command does not generate tests.

### Generate previously failed tests

```bash
vendor/bin/mftf generate:failed
```

This command cleans up the previously generated tests; generates the tests listed in `dev/tests/acceptance/tests/_output/failed`.
For more details about `failed`, refer to [Reporting](../reporting.md).

### Run previously failed tests

```bash
vendor/bin/mftf run:failed
```

This command runs the tests listed in `dev/tests/acceptance/tests/_output/failed`.
For more details about `failed`, refer to [Reporting](../reporting.md).

## Error tolerance during generation

Starting from version 3.2.0, the Functional Testing Framework will not fail right away when encountering generation errors.
Instead, the Functional Testing Framework will generate as many tests and suites as it can, log errors to `mftf.log`, and exit with a non-zero generation status.

Note:

- Not all errors are tolerable at generation. For example, schema validation errors, parser errors, and WebApi authentication errors will cause `hard` failures, with no tests or suites being generated.
- Error tolerance in generation is meant to help local test development and testing and is expected to be run locally. All generation errors must be fixed in order to use other framework functionality, pass static checks, and to deliver test.

## Reference

### `build:project`

#### Description

Clone the example configuration files and build the Codeception project.

#### Usage

```bash
vendor/bin/mftf build:project [--upgrade] [config_param_options]
```

#### Options

| Option            | Description                                                                                                                                                                                   |
| ----------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `-u`, `--upgrade` | Upgrades all installed test according to requirements of the last major release. Specifying this flag upgrades only those tests in the default location. Example: `build:project --upgrade`. |

You can include options to set configuration parameter values for your environment since the project build process also [sets up the environment](#setupenv).

```bash
vendor/bin/mftf build:project --MAGENTO_BASE_URL=http://magento.local/ --MAGENTO_BACKEND_NAME=admin214365
```

### `doctor`

#### Description

Diagnose the Functional Testing Framework configuration and setup. Currently this command will check the following:

- Verify admin credentials are valid. Allows the Functional Testing Framework to authenticate and run API requests to Adobe Commerce or Magento Open Source through cURL
- Verify that Selenium is up and running and available for MFTF
- Verify that new session of browser can open Admin and store front urls
- Verify that the Functional Testing Framework can run Adobe Commerce and Magento Open Source CLI commands

#### Usage

```bash
vendor/bin/mftf doctor
```

#### Options

### `generate:tests`

#### Description

Perform XML schema validation and generate PHP code from the tests defined in XML files.
The path is set in the `TESTS_MODULE_PATH` [configuration](../configuration.md) parameter.

#### Usage

```bash
vendor/bin/mftf generate:tests [option] [<test name>] [<test name>] [--remove]
```

#### Options

| Option                                              | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `--config=[<default> or <singleRun> or <parallel>]` | Creates a single manifest file with a list of all tests. The default location is `tests/functional/Magento/FunctionalTest/_generated/testManifest.txt`.<br/> You can split the list into multiple groups using `--config=parallel`; the groups will be generated in `_generated/groups/` like `_generated/groups/group1.txt, group2.txt, ...`.<br/> Available values: `default` (default), `singleRun`(same as `default`), and `parallel`.<br/> Example: `generate:tests --config=parallel`.                 |
| `--filter`                                          | Option to filter tests to be generated.<br/>Template: '&lt;filterName&gt;:&lt;filterValue&gt;'.<br/>Existing filter types: severity, includeGroup, excludeGroup.<br/>Existing severity values: BLOCKER, CRITICAL, MAJOR, AVERAGE, MINOR.<br/>Example: `vendor/bin/mftf generate:tests --filter=severity:CRITICAL --filter=severity:BLOCKER --filter=includeGroup:customer`                                                                                                                                   |
| `--force`                                           | Forces test generation, regardless of the module merge order defined in the Adobe Commerce or Magento Open Source instance. Example: `generate:tests --force`.                                                                                                                                                                                                                                                                                                                                                                             |
| `-i,--time`                                         | Set time in minutes to determine the group size when `--config=parallel` is used. <br/>Example: `generate:tests --config=parallel --time=15` <br/>Option `--time` will be the default and the __default value__ is `10` when neither `--time` nor `--groups` is specified. <br/>Example: `generate:tests --config=parallel`                                                                                                                                                                                  |
| `-g,--groups`                                       | Set number of groups to be split into when `--config=parallel` is used. <br/>Example: `generate:tests --config=parallel --groups=300` <br/>Options `--time` and `--groups` are mutually exclusive and only one should be used.                                                                                                                                                                                                                                                                                |
| `--tests`                                           | Defines the test configuration as a JSON string or JSON file path.                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| `--allow-skipped`                                   | Allows the framework to generate and run tests marked with `<skip>.`                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| `--debug`                                           | Performs schema validations on XML files. <br/> DEFAULT: `generate:tests` implicitly performs schema validation on merged files. It does not indicate the file name where the error is encountered. <br/> DEVELOPER: `--debug` performs per-file validation and returns additional debug information (such as the filename where an error occurred) when test generation fails because of an invalid XML schema. This option takes extra processing time. Use it after test generation has failed once.<br/> |
| `-r,--remove`                                       | Removes the existing generated suites and tests cleaning up the `_generated` directory before the actual run. For example, `generate:tests SampleTest --remove` cleans up the entire `_generated` directory and generates `SampleTest` only.                                                                                                                                                                                                                                                                 |
| `-l,--log`                                          | Generate metadata files during test generation. Accepted parameters are: testEntityJson.                                                                                                                                                                                                                                                                                                                                                                                                                     |

#### Examples of the JSON configuration

The configuration to generate a single test with no suites:

```json
{
   "tests":[
      "general_test1"  //Generate the "general_test1" test.
      ],
   "suites": null
}
```

The configuration to generate a single test in the suite:

```json
{
   "tests": null,       // No tests outside the suite configuration will be generated.
   "suites":{
      "sample":[        // The suite that contains the test.
         "suite_test1"  // The test to be generated.
      ]
   }
}
```

Complex configuration to generate a few non-suite tests, a single test in a suite, and an entire suite:

```json
{
   "tests":[
      "general_test1",
      "general_test2",
      "general_test3"
   ],
   "suites":{           //Go to suites.
      "sample":[        //Go to the "sample" suite.
         "suite_test1"  //Generate the "suite_test1" test.
      ],
      "sample2":[]      //Generate all tests in the "sample2" suite.
   }
}
```

The command that encodes this complex configuration:

Command to generate test by json string:

```bash
vendor/bin/mftf generate:tests --tests '{"tests":["general_test1","general_test2","general_test3"],"suites":{"sample":["suite_test1"],"sample2":null}}'
```

Note that the strings must be escaped and surrounded in quotes.

Command to generate test by json file:

```bash
vendor/bin/mftf generate:tests --tests ./foldername/filename.json
```

### `generate:suite`

#### Description

Generates one or more suites based on XML declarations.

#### Usage

```bash
vendor/bin/mftf generate:suite <suite name> [<suite name>] [--remove]
```

#### Options

| Option | Description |
| --- | --- |
| `-r,--remove` | Removes the existing generated suites and tests cleaning up the `_generated` directory before the actual run. For example, `vendor/bin/mftf generate:suite WYSIWYG --remove` cleans up the entire `_generated` directory and generates `WYSIWYG` only. |

#### Example

```bash
vendor/bin/mftf generate:suite suite1 suite2
```

### `generate:urn-catalog`

#### Description

Generates a URN catalog, enabling PhpStorm to recognize and highlight URNs.
It also enables auto-completion in PhpStorm.

#### Usage

```bash
vendor/bin/mftf generate:urn-catalog [--force] [<path to misc.xml>]
```

`misc.xml` is typically located at `<project root>/.idea/misc.xml`.

#### Options

| Option        | Description                                                           |
| ------------- | --------------------------------------------------------------------- |
| `-f, --force` | Creates the `misc.xml` file if it does not exist in the given `path`. |

#### Example

```bash
vendor/bin/mftf generate:urn-catalog .idea/misc.xml
```

### `reset`

#### Description

Cleans any configuration files and generated artifacts from the environment.
The `.env` file is not affected.

#### Usage

```bash
vendor/bin/mftf reset [--hard]
```

#### Options

| Option   | Description                                |
| -------- | ------------------------------------------ |
| `--hard` | Forces a reset of the configuration files. |

#### Example

```bash
vendor/bin/mftf reset --hard
```

### `run:group`

Generates and executes the listed groups of tests using Codeception.

#### Usage

```bash
vendor/bin/mftf run:group [--skip-generate|--remove|--xml] [--] <group1> [<group2>]
```

#### Options

| Option                | Description                                                                                               |
| --------------------- | --------------------------------------------------------------------------------------------------------- |
| `-k, --skip-generate` | Skips generating from the source XML. Instead, the command executes previously-generated groups of tests. |
| `-r, --remove`        | Removes previously generated suites and tests before the actual generation and run.                       |
| `--debug`             | Performs schema validations on XML files. `run:group` implicitly performs schema validation on merged files. It does not indicate the file name where the error is encountered. `--debug` performs per-file validation and returns additional debug information (such as the filename where an error occurred).|
| `--xml`               |  Generate JUnit XML Log (default: "report.xml")                                                             |

#### Examples

Clean up after the last test run; generate from XML and execute the tests with the annotations `group="group1"` and `group="group2"`:

```bash
vendor/bin/mftf -r -- run:group group1 group2
```

Execute previously generated tests with the annotations `group="group1"` and `group="group2"`, skipping the regeneration of the test:

```bash
vendor/bin/mftf run:group -k -- group1 group2
```

### `run:test`

Generates and executes tests by name using Codeception.

#### Usage

```bash
vendor/bin/mftf run:test [--skip-generate|--remove|--xml] [--] <name1> [<name2>]
```

#### Options

| Option                | Description                                                                                               |
|-----------------------|-----------------------------------------------------------------------------------------------------------|
| `-k, --skip-generate` | Skips generating from the source XML. Instead, the command executes previously-generated groups of tests. |
| `-r, --remove`        | Remove previously generated suites and tests.                                                             |
| `--debug`             | Performs schema validations on XML files. `run:test` implicitly performs schema validation on merged files. It does not indicate the file name where the error is encountered. `--debug` performs per-file validation and returns additional debug information (such as the filename where an error occurred).|
| `--xml`               |  Generate JUnit XML Log (default: "report.xml")                                                             |

#### Examples

Generate the `LoginCustomerTest` and `StorefrontCreateCustomerTest` tests from XML and execute all the generated tests:

```bash
vendor/bin/mftf run:test LoginCustomerTest StorefrontCreateCustomerTest
```

### `run:manifest`

Runs a testManifest.txt file.

This command runs all tests specified in a testManifest.xml file. It does not generate tests for you. You must do that as first.

#### Usage

```bash
vendor/bin/mftf run:manifest path/to/your/testManifest.txt
```

#### Example testManifest.xml file

Each line should contain either: one test path or one group (-g) reference.

```terminal
tests/functional/tests/MFTF/_generated/default/AdminLoginSuccessfulTestCest.php
-g PaypalTestSuite
tests/functional/tests/MFTF/_generated/default/SomeOtherTestCest.php
tests/functional/tests/MFTF/_generated/default/ThirdTestCest.php
-g SomeOtherSuite
```

### `run:failed`

Regenerates and reruns tests that previously failed.

This command cleans up previously generated tests. It generates and runs the tests listed in `dev/tests/acceptance/tests/_output/failed`.
For more details about `failed`, refer to [Reporting](../reporting.md).

#### Usage

```bash
vendor/bin/mftf run:failed
```

#### Options

| Option                | Description                                                                                               |
|-----------------------|-----------------------------------------------------------------------------------------------------------|
| `--debug` | Performs schema validations on XML files. `run:failed` implicitly performs schema validation on merged files. It does not indicate the file name where the error is encountered. `--debug` performs per-file validation and returns additional debug information (such as the filename where an error occurred). Use it after test run has failed once.|

#### Examples

Run the tests that failed in the previous run:

```bash
vendor/bin/mftf run:failed
```

### `setup:env`

Updates the [configuration](../configuration.md) parameter values in the [`.env`] file.
Creates the `.env` file if it does not exist.

#### Usage

```bash
vendor/bin/mftf setup:env [config_param_option1=<value>] [config_param_option2=<value>]
```

`config_param` is a configuration parameter from the `.env` file.
The command consumes the parameters in a format of options assigned with values, for example `--MAGENTO_BASE_URL=http://magento.local/`.
If you specify a parameter that the `.env` file does not contain, the command returns an error.

You can also update configuration parameter values when you use the [`build:project`](#buildproject) command.

#### Examples

To change values for the `MAGENTO_BASE_URL` and `BROWSER`:

```bash
vendor/bin/mftf setup:env --MAGENTO_BASE_URL=http://magento.local/ --BROWSER=firefox
```

To create a `.env` file with example parameters:

```bash
vendor/bin/mftf setup:env
```

The example parameters are taken from the `etc/config/.env.example` file.

### `static-checks`

Runs all or specific Functional Testing Framework static-checks on the test codebase that the Functional Testing Framework is currently attached to.
Behavior for determining what tests to run is as follows:

-  If test names are specified, only those tests are run.
-  If no test names are specified, tests are run according to `staticRuleset.json`.
-  If no `staticRuleset.json` is found, all tests are run.

Static checks errors are written to *.txt files under TEST_BP/tests/_output/static-results/

#### Usage

```bash
vendor/bin/mftf static-checks [<names>]...
```

#### Options

| Option                | Description                                                                                               |
|-----------------------|-----------------------------------------------------------------------------------------------------------|
| `-p, --path` | Path to a test module to run "deprecatedEntityUsage" and "pauseActionUsage" static check scripts. Option is ignored by other static check scripts.

#### Examples

To check what existing static check scripts are available

```bash
vendor/bin/mftf static-checks --help
```

To run all existing static check scripts

```bash
vendor/bin/mftf static-checks
```

To run specific static check scripts

```bash
vendor/bin/mftf static-checks testDependencies
```

```bash
vendor/bin/mftf static-checks actionGroupArguments
```

```bash
vendor/bin/mftf static-checks deprecatedEntityUsage
```

```bash
vendor/bin/mftf static-checks pauseActionUsage
```

```bash
vendor/bin/mftf static-checks annotations
```

```bash
vendor/bin/mftf static-checks deprecatedEntityUsage -p path/to/mftf/test/module
```

```bash
vendor/bin/mftf static-checks pauseActionUsage -p path/to/mftf/test/module
```

```bash
vendor/bin/mftf static-checks testDependencies actionGroupArguments
```

#### Existing static checks

| Argument              | Description                                                                                               |
|-----------------------|-----------------------------------------------------------------------------------------------------------|
|`testDependencies`     | Checks that test dependencies do not violate Magento module's composer dependencies.|
|`actionGroupArguments` | Checks that action groups do not have unused arguments.|
|`deprecatedEntityUsage`| Checks that deprecated test entities are not being referenced.|
|`annotations`| Checks various details of test annotations, such as missing annotations or duplicate annotations.|
|`pauseUsage`| Checks that pause action is not used in action groups, tests or suites.|

#### Defining ruleset

The `static-checks` command will look for a `staticRuleset.json` file under either:

-  `dev/tests/acceptance/staticRuleset.json`, if embedded with Magento2
-  `dev/staticRuleset.json`, if standalone

This file works as the default configuration to easily allow for the integration of `static-checks` in a CI environment.
Currently, the ruleset only defines the tests to run. Here is an example of the expected format:

```json
{
  "tests": [
    "actionGroupArguments",
    "anotherTest"
  ]
}
```

### `upgrade:tests`

When the path argument is specified, this `upgrade` command applies all the major version Functional Testing Framework upgrade scripts to a `Test Module` in the given path.
Otherwise, it will apply all the major version Functional Testing Framework upgrade scripts to all installed test components.

`Test Module` should have the directory structure of ActionGroup, Data, Metadata, Page, Section, Test, and Suite.

<InlineAlert variant="info" slots="text" />

The upgrade scripts are meant to be used for Test Modules under source code control. If you have old versions of test modules under vendor, those test modules will get upgraded

#### Usage

```bash
vendor/bin/mftf upgrade:tests [<path>]
```

`<path>` is the path to a Functional Testing Framework `Test Module` that needs to be upgraded.
The command searches recursively for any `*.xml` files to upgrade.

#### Examples

To upgrade all installed test:

```bash
vendor/bin/mftf upgrade:tests
```

To upgrade all test components inside modules in the `dev/tests/acceptance/tests/` directory:

```bash
vendor/bin/mftf upgrade:tests /Users/user/magento2/dev/tests/acceptance/tests/
```

To upgrade all test components inside the `Catalog` module:

```bash
vendor/bin/mftf upgrade:tests /Users/user/magento2/app/code/Magento/Catalog/Test/Mftf/
```

### `codecept:run`

A Functional Testing Framework wrapper command that invokes `vendor/bin/codecept run`. This command runs tests in functional suite. Tests must be generated before using this command.

#### Usage

See the [Run Command](https://codeception.com/docs/reference/Commands#Run).

```bash
vendor/bin/mftf codecept:run [<suite|test>] --[<option(s)>]
```

#### Examples

```bash
# Run all tests in functional suite
vendor/bin/mftf codecept:run functional
```

```bash
# Run all tests in functional suite with options
vendor/bin/mftf codecept:run functional --verbose --steps --debug
```

```bash
# Run one test
vendor/bin/mftf codecept:run functional Magento/_generated/default/AdminCreateCmsPageTestCest.php --debug
```

```bash
# Run all tests in default group
vendor/bin/mftf codecept:run functional --verbose --steps -g default
```

<InlineAlert variant="warning" slots="text" />

You may want to limit the usage of this Codeception command with arguments and options for "acceptance" only, since it is what's supported by MFTF. When using this command, you should change "acceptance" to "functional" when referring to Codeception documentation.
