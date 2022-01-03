# Contributing

This script was originally created by Bedrockstreaming to automate the archiving of a VM running on Xen Orchestra, on a S3 bucket.

The features available for now are only those we need, but you're welcome to open an issue or pull-request if you need more.

## Developping

### Variables

* Convention for variables is to use underscores: `this_is_a_variable`.
* No CamelCase.
* Script wide variables must be in upper case `THIS_IS_A_SCRIPT_WIDE_VARIABLE`.
* All other variables should be lower case.
* Inside a `function`, variables internal to the function must be private: defined with `local` and prefixed with an underscore. E.g, `local _vm_name=$1`

### Functions

* Functions names must be underscored and lower case.
* Functions can only use script-wide variables (those upper case) internally, or use the function parameters (`$1`, `$2`, `$@`, etc.)
