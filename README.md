# sbt-scoverage

[![Gitter](https://img.shields.io/gitter/room/scoverage/scoverage.svg)](https://gitter.im/scoverage/scoverage)
[![Maven Central](https://maven-badges.herokuapp.com/maven-central/org.scoverage/sbt-scoverage/badge.svg?kill_cache=1)](https://search.maven.org/artifact/org.scoverage/sbt-scoverage/)
[![License](http://img.shields.io/:license-Apache%202-red.svg)](http://www.apache.org/licenses/LICENSE-2.0.txt)

sbt-scoverage is a plugin for sbt that integrates the scoverage code coverage
library. Find out more about
[scoverage](https://github.com/scoverage/scalac-scoverage-plugin).

## How to use

Make sure your sbt version in project/build.properties:
```scala
sbt.version = 1.2.8
```

Add the plugin in `project/plugins.sbt`:
[![Maven Central](https://maven-badges.herokuapp.com/maven-central/org.scoverage/sbt-scoverage/badge.svg?kill_cache=1)](https://search.maven.org/artifact/org.scoverage/sbt-scoverage/)
```scala
addSbtPlugin("org.scoverage" % "sbt-scoverage" % "x.x.x")
```

Run the tests with enabled coverage:
```
$ sbt clean coverage test
```
or if you have integration tests as well
```
$ sbt clean coverage it:test
```

To enable coverage directly in your build, use:
```scala
coverageEnabled := true
```

To generate the coverage reports run
```
$ sbt coverageReport
```

Coverage reports will be in `target/scala-<scala-version>/scoverage-report`. There are HTML and XML reports. The XML is useful if you need to programatically use the results, or if you're writing a tool.

If you're running the coverage reports from within an sbt console session (as
opposed to one command per sbt launch), then the `coverage` command is sticky. To
turn it back off when you're done running reports, use the `coverageOff` command or reset `coverageEnabled` with `set coverageEnabled := false`.

Sample project with scoverage in both sbt and maven - [the scoverage samples project](https://github.com/scoverage/sbt-scoverage-samples).

## Notes on upgrading to version 1.6.x
* ´coverageCleanSubprojectFiles´ key has been removed
* scala version 2.12+ is required as a minimum (due to scoverage suppport)

## Notes on upgrading to version 1.6.0

* `coverageAggregate` aggregates raw coverage data, not coverage xml reports for modules.
There is no requirement to generate individual coverage reports for modules (`coverageReport`)
before generating aggregated report (`coverageAggregate`).

If only aggregated report is required, not executing `coverageReport` can reduce the build time significantly.

## Notes on upgrading to version 1.3.0

* The object containing the keys has changed from nested to top level so you might need to adjust the import. It's also an auto plugin now, so you might not need the import at all.
* There is an issue syncing the binary with the sbt-plugin-releases repo, so in the meantime add `resolvers += Resolver.url("scoverage-bintray", url("https://dl.bintray.com/sksamuel/sbt-plugins/"))(Resolver.ivyStylePatterns)` to your build.

## Notes on upgrading to version 1.0.0

If you are upgrading from 0.99.x then you must remove the `instrumentSettings` from your build.sbt or Build.scala, as that is no longer needed.

Next, the keys have been renamed slightly. The new names begin with coverageXXX, eg coverageExcludedPackages and some have had their full name changed. You can see a full list of keys by opening the object ScoverageKeys.

## Multi project reports

By default, scoverage will generate reports for each project separately. You can merge them into an aggregated report by invoking `sbt coverageAggregate`.

(Note, you must do this after all the coverage data is complete as a separate command, so you cannot do `sbt coverage test coverageAggregate` (at least until a way around this is found).)

(You do not need to run `coverageReport` before `coverageAggregate`; it aggregates over the sub-projects' coverage data directly, not the report xml.)

## Exclude classes and packages

You can exclude classes from being considered for coverage measurement by providing semicolon-separated list of
regular expressions.

Example:
```scala
coverageExcludedPackages := "<empty>;Reverse.*;.*AuthService.*;models\\.data\\..*"
```

The regular expressions are matched against the fully qualified class name, and must match the entire string to take effect.

Any matched classes will not be instrumented or included in the coverage report.

You can also mark sections of code with comments like:

```scala
  // $COVERAGE-OFF$Disabling highlighting by default until a workaround for https://issues.scala-lang.org/browse/SI-8596 is found
  ...
  // $COVERAGE-ON$
```

Any code between two such comments will not be instrumented or included in the coverage report.

## Minimum coverage

Based on minimum coverage, you can fail the build with the following keys

```scala
coverageMinimum := 80
coverageFailOnMinimum := true
```

These settings will be enforced when the reports are generated.
If you generate an aggregate report using `coverageAggregate` then these settings will apply to that report.

## Failing tests
Scoverage does a lot of file writing behind the scenes in order to track which statements have been executed.
If you are running into a scenario where your tests normally pass, but fail when scoverage is enabled, then the culprit can be one of the following:

* timing issues on futures and other async operations, try upping the timeouts by an order of magnitude.
* tests are run in a sandbox mode (such as with `java.security.PrivilegedAction<T>`), try running the tests outside of the sandbox.

## Integrations

### Codacy

[Codacy](https://www.codacy.com) integrates with your favorite coverage tool to provide an in-depth overlook of your project status. Scoverage information can be integrated into Codacy through the [sbt-codacy-coverage plugin](https://github.com/codacy/sbt-codacy-coverage).

### Coveralls

If you have an open source project then you can add code coverage metrics with the excellent website https://coveralls.io/ Scoverage will integrate with coveralls using the [sbt-coveralls](https://github.com/scoverage/sbt-coveralls) plugin.

### Plugin for SonarQube

If you want to visually browse statement coverage reports then use this [plugin for SonarQube](https://github.com/RadoBuransky/sonar-scoverage-plugin).
It allows you to review overall project statement coverage as well as dig deeper into sub-modules, directories and
source code files to see uncovered statements. Statement coverage measurement can become an integral part of your
team's continuous integration process and a required quality standard.

## License
```
This software is licensed under the Apache 2 license, quoted below.

Copyright 2013-2016 Stephen Samuel and contributors

Licensed under the Apache License, Version 2.0 (the "License"); you may not
use this file except in compliance with the License. You may obtain a copy of
the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS, WITHOUT
WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. See the
License for the specific language governing permissions and limitations under
the License.
```
