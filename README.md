# Sublime Test Plier

| Master | Develop |
| ------ |-------- |
| [![Build Status][2]][1] | [![Build Status][3]][1] |

This [Sublime Text 3 (only)][4] plugin allows python developers to run (a single or  more) tests quickly from within a project environment.



### Preface

Unlike [other plugins for running python tests][5], which mostly use a regex pattern to find a given test to run, Sublime Test Plier parses the source [AST][6], and locates the class/method/function whose definition contains the caret position. The found results are passed as named arguments to the test command, which can be anything (`py.test`, `nosetests`, `python manage.py` or `your-own-test-runner`).


## Usage

The simplest usage is simply running the build system; <kbd>ctrl+shift+b</kbd> (<kbd>super+shift+b</kbd> on OSX) and select `Run Python Tests` from the build system selection drop-down, after using it once you may hit <kbd>ctrl+b</kbd> (<kbd>super+b</kbd> on OSX) to run the build system again.

_Note: this works by running py.test found using SublimeText's environment, which will work for the simplest of cases, but for most projects you will want to configure the test environment; for more details on this see [configuration section](#configuration) below._

### Run a specific test

This is the main reason for using Test Plier: you can use it to run a specified test module, class or function by placing the caret at the desired test location prior to running test all placeholders are replaced in `cmd` by the located test and selected text.

### Launching an external runner

By default the test command is passed to SublimeText's built-in `exec` command which, by default, outputs test results to a build results panel in the editor.

In most cases this is sufficient; however, occasionally an external terminal window is preferred, most often when user input is required (e.g pdb entered), this is possible by setting an external runner.

If `RunPythonTestsCommand.external_runner` is set (e.g in a subclass), or the build system kwargs contains an "external" property, the given command list/array is executed.

The existing command kwargs are parsed into a "shell-friendly" command that is passed as space delimited arguments to this "external" command.


## Configuration

By default the command we run is `py.test {filename}::{test_class}::{test_func} -k {selection} --doctest-modules -v`.
Let us look at the substitute arguments with more detail:

- `--doctest-modules - ` if no UnitTest class/method given run module unittests
- `-v                - ` verbose test output

Placeholder arguments are optional, and will be cleanly removed where possible:

- `{filename}      - ` test target file
- `{test_class}    - ` test target class
- `{test_func}     - ` test target function/method
- `-k {selection}  - ` use selected text as pattern

You may customize the command and any of it's parameters (all optional) in your `project.sublime-project` settings.
For example Django's test runner can be run like so:

```json
{
    "build_systems":
    [
        {
            "cmd":
            [
                "python",
                "manage.py",
                "test",
                "--noinput",
                "{filename}:{test_class}.{test_func}",
                "--settings=test_settings"
            ],
            "env":
            {
                "PYTHONPATH": "/home/user/.venvs/project/lib/python2.7/site-packages",
                "REUSE_DB": "1"
            },
            "name": "Django Test",
            "target": "run_python_tests",
            "working_dir": "${project_path}/project/"
        }
    ]
}
```

For more info on the SublimeText build-system configuration see [the unofficial documentation][7].


## Sublime ANSI

This plugin supports passing the command through [SublimeANSI][8] to display ANSI colors in the ST output panel. This will be automatically activated if the plugin is installed.

## Contributing

We welcome any and all contributions. For stuff that I'd like to see done first see below:

### _Things on our TODO list_

- [ ] document all arguments the command accepts
- [ ] add an example for external test - how I use it to launch test in an iTerm2 session.

[1]: https://travis-ci.org/asfaltboy/SublimeTestPlier
[2]: https://travis-ci.org/asfaltboy/SublimeTestPlier.svg?branch=master
[3]: https://travis-ci.org/asfaltboy/SublimeTestPlier.svg?branch=develop
[4]: http://www.sublimetext.com/3
[5]: https://packagecontrol.io/search/test+python
[6]: test_parser.py
[7]: http://sublime-text-unofficial-documentation.readthedocs.org/en/latest/reference/build_systems/configuration.html
[8]: https://github.com/aziz/SublimeANSI