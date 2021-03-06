# Boutiques schema

The JSON schema for Boutiques descriptors.

## Properties

Mandatory properties are in bold. Note that Boutiques descriptors may contain additional, unspecified properties providing extra information about the tool. It is strongly recommended that implementations do not critically depend on unspecified properties.

* **`name`:** tool name.
* **`description`:** tool description.
* **`schema-version`:** version of the schema used.
* **`tool-version`:** tool version.
* **`command-line`:** a string that describes the tool command line, where input and output values are identified by "keys". At runtime, command-line keys are substituted with flags and values (see details in next Section). Example:
```
my_tool [PARAM1] [IN_FILE] [PARAM2] [OUT_FILE]
```
The format of command line keys is not specified. However, it is recommended to use easily-identifiable strings.
* **`inputs`:** an array of objects that represent inputs with the following properties:
  * **`id`:** a short, unique, informative identifier containing only alphanumeric characters and underscores. Typically used to generate variable names. Example: "data_file".
  * **`name`:** a human-readable input name. Example: "Data File".
  * **`type`:** input type. "File", "String", "Flag", "Enum", or "Number". Type "File" also includes directories.
  * `description`: input description.
  * `value-key`: a string, contained in `command-line` and/or `file-template` and/or `path-template`, substituted by the input value and/or flag at runtime.
  * `list`: a boolean, true if input is a list of value. An input of type "Flag" may not be a list.
  * `optional`: a boolean, true if input is optional.
  * `command-line-flag`: a string involved in the `value-key` substitution. Inputs of type \"Flag\" have to have a command-line flag. Examples: ```-v```, ```--force```.
  * `command-line-flag-separator`: a string inserted between the flag and the value. Defaults to space.
  * `default-value`: default value used by the tool when the input is not specified.
  * `enum-value-choices`: permitted choices for input value. May only be used in conjunction with the Enum type.
  * `integer`: a boolean specifying whether the input should be an integer. May only be used with Number type inputs.
  * `minimum`: the minimum value of the input (inclusive). May only be used with Number type inputs.
  * `maximum`: the maximum value of the input (inclusive). May only be used with Number type inputs.
  * `exclusive-minimum`: a boolean specifying whether an input's minimum value should be exclusive. May only be used with Number type inputs with a minimum.
  * `exclusive-maximum`: a boolean specifying whether an input's maximum value should be exclusive. May only be used with Number type inputs with a maximum.
  * `min-list-entries`: the minimum number of entries in the list. May only be used with List type inputs.
  * `max-list-entries`: the maximum number of entries in the list. May only be used with List type inputs.
  * `requires-inputs`: ids of the inputs which must be active for this input to be available.
  * `disables-inputs`: ids of the inputs that are disabled when this input is active.
  * `uses-absolute-path`: true if file inputs need to be passed as absolute paths. If false, any of absolute or relative path may be used.
* **`output-files`**: an array of objects that represent output files with the following properties:
  * **`id`:** a short, unique, informative identifier containing only alphanumeric characters and underscores. Typically used to generate variable names. Example: "data_file".
  * **`name`**: output name.
  * **`path-template`**: a string that describes the output file path relatively to the execution directory. May contain input `value-keys`. Example: ```results/[INPUT1]_brain.mnc```.
  * `description`: output description.
  * `path-template-stripped-extensions`: list of file extensions that will be stripped from the input values before being substituted in the path template. Example: ```[".nii",".nii.gz"]```.
  * `value-key`: a string, contained in `command-line` and/or `file-template` and/or `path-template`, substituted by the output value/flag at runtime.
  * `list`: a boolean, true if output is a list of value. In this case, `path-template` must contain a '*' standing for any string of characters (as the Linux wildcard).
  * `optional`: a boolean, true if output may not be produced by the tool.
  * `command-line-flag`: option flag of the output, involved in the `value-key` substitution. Examples: ```-o```, ```--output```.
  * `uses-absolute-path`: true if an output filepath must use an absolute path
  * `file-template`: an array of string that will be written in the file before the command is executed. Can be useful to write configuration files. `value-keys` that are contained in the `file-template` will be substituted by input or output values when they are defined (when they are not, e.g., in case of an optional input, the corresponding line in the `file-template` will be deleted).
* `container-image`: an object describing the container where the tool is installed and configured. Has the following properties:
  * **`type`**: "docker", "singularity" or "rootfs".
  * **`image`** (docker only): name of an image where the tool is installed and configured. Example: ```docker.io/neurodebian```.
  * **`index`** (docker only): docker index where the image is available. Default: ```http://index.docker.io```.
  * **`url`** (singularity and rootfs only): URL where the container image is available.
  * `working-directory`: directory where the tool must be launched within the container.
  * `container-hash`: a string containing the sha384 hash of the container. Example: "sha384:83e4403e0f5f37164be88ef05e8f2ee24664b45f685dbf05b7dc61b1a8a429656d0ddee4c7f967150990dbdf17c36d45". 
* `environment-variables`: an array of items defining environment variable assignment from the following properties:
  * **`name`**: environment variable name (identifier) containing only alphanumeric characters and underscores. Example: "PROGRAM_PATH".
  * **`value`**: value of the environment variable.
  * `description`: description of the environment variable.
* `walltime-estimate`: estimated wall time of a task, in seconds.
* `groups`: an array of objects that represent input groups with the following properties:
  * **`id`:** a short, unique, informative identifier containing only alphanumeric characters and underscores.
  * **`name`:** a human-readable name for the input group.
  * **`members`:** an array containing the IDs of the inputs belonging to this group.
  * `description`: a short, unique, informative identifier containing only alphanumeric characters and underscores. Typically used to generate variable names. Example: ```outfile_group```.
  * `mutually-exclusive`: a boolean, true if only one input in the group may be active at runtime.
  * `one-is-required`: a boolean, true if at least one of the inputs in the group must be active at runtime.
* `invocation-schema`: an JSON schema object that contains the invocation schema of the tool.
* `custom`: an object containing custom properties for the tool. It is recommended to prefix the name of a custom property with the name of the target platform, e.g. "cbrain:". The following custom properties were defined so far:
    * `cbrain:can-submit-new-tasks`: a boolean, true if the tool may submit new tasks to the platform. This is an embryonic support for workflows. Specific to the CBRAIN platform for now but may be added to the specificiation in the future.
    * `cbrain:inherits-from-class`: a string that defines the Ruby class that should be used as parent class for the tool in CBRAIN. Used to define a progress bar for PSOM tools in CBRAIN.
    * `vip:miccai-challenger-email` and `vip:miccai-challenge-team-name`: strings helping VIP categorize tools for the 2016 MICCAI challenges [MSSEG](https://portal.fli-iam.irisa.fr/msseg-challenge/overview) and [PETSEG](https://portal.fli-iam.irisa.fr/petseg-challenge/overview).

## Command-line substitution

At runtime, a __value__ is assigned to each input in ```inputs```.

* Inputs of type "String" may contain any string (encoding is not specified).
* Inputs of type "Number" must contain a string representing a number.
* Inputs of type "File" must contain a string representing a file path (absolute or relative to the execution directory).
* Inputs of type "Flag" must contain a boolean.

When input is a list, __value__ contains the concatenation of all strings in the list, separated by spaces. Spaces included in list elements must be escaped by '\', or the elements must be single-quoted (Linux conventions).  An input of type "Flag" may not be a list.

The tool command line is generated as follows:

1. For each input in ```inputs```, where input has a ```value-key``` and input has __value__:
  * In ```command-line```, replace input ```value-key``` by:
         * ```command-line-flag``` if input is of type "Flag".
         * ```command-line-flag``` ```command-line-flag-separator``` __value__ (i.e. separated only by ```command-line-flag-separator``` if it is present).
         * ```command-line-flag``` __value__ (space-separated) otherwise.
  * For each output in ```output-files```
         * If ```path-template``` contains input ```value-key```, replace ```value-key``` by __value__, having previously removed the last occurrence of character '.' and subsequent characters from __value__ when input is of type "File".
2. For each output in ```output-files```, where output has a ```value-key```:
  * In ```command-line```, replace output ```value-key``` by:
         * ```command-line-flag``` ```path-template``` (space-separated). At this step, input ```value-key```s contained in ```path-template``` are already substituted.

## Examples

An annotated example of a Boutiques application descriptor:
```javascript
{
  // An example Boutiques JSON descriptor
  // Note that real JSON cannot include comments like this
  "name" : "Example Boutiques Tool",
  "tool-version" : "0.0.1",
  "description" : "This property describes the tool or application",
  // The actual command line run will be generated by substituting given input values into this string,
  // using the "value-key" property of the input. Notice we can chain multiple commands easily.
  "command-line" : "exampleTool_1 [STRING_INPUT] [FILE_INPUT] [ENUM_INPUT] | exampleTool_2 [FLAG_INPUT] [NUMBER_INPUT] >> [LOG]",
  // Optionally specify a container image that can be used to execute the tool
  "container-image": {
        "type": "docker",
        "image": "boutiques/examples"
  },
  "schema-version" : 0.3, // The version of the Boutiques schema this descriptor follows
  "inputs" : [{
    // A string parameter
    "id" : "str_input", // A unique id for this input (e.g. for platforms to differentiate inputs)
    "name" : "A string input", // A human readable name for the input parameter (e.g. for UI display)
    "type" : "String",
    "description" : "Describe the use and meaning of the parameter",
    "optional" : false, // Means the parameter is required for the tool
    // This will be prepended to the input value before substitution into the command line
    "command-line-flag" : "-i",
    "value-key" : "[STRING_INPUT]", // Used for substitution into the command-line above
    "list" : true, // When true, allows multiple inputs to be given for this parameter
    // Restrict lists to have only a specific number of members
    "min-list-entries" : 1,
    "max-list-entries" : 3,
    // Default values can also be specified for parameters
    "default-value" : "aStringValue"
  }, {
    // A numerical parameter
    "id" : "num_input",
    "name" : "A number input",
    "type" : "Number",
    "value-key" : "[NUMBER_INPUT]",
    "optional" : true, // Means this parameter is not strictly required for the tool to be used
    // Can alter the prefix flag to use a different separator (e.g. here -n=<inputval>)
    "command-line-flag" : "-n",
    "command-line-separator" : "=",
    // Use constraints to specify a number in (0,1]
    "minimum" : 0,
    "maximum" : 1,
    "exclusive-minimum" : true,
    "exclusive-maximum" : false,
    "integer" : false // Make 'true' to require the number is an integer
  }, {
    // File input parameters are specified like strings
    // Implementation details, like validation of file existence, are left to platforms
    "id" : "file_input",
    "name" : "A file input",
    "type" : "File",
    "optional" : true,
    "value-key" : "[FILE_INPUT]"
  }, {
    // An enum-type input
    "id" : "enum_input",
    "name" : "An enum input",
    "type" : "Enum",
    "enum-value-choices" : ["val1","val2","val3"],
    "value-key" : "[ENUM_INPUT]"
  }, {
    // A flag input: essentially a boolean describing the presence or absence of a command-line flag
    "id" : "flag_input",
    "name" : "A flag input",
    "type" : "Flag",
    // Command line substitution can only be the flag or nothing (no extra values given)
    "command-line-flag" : "-f",
    "value-key" : "[FLAG_INPUT]",
    // Can specify a list of ids of parameters that this input requires to be used
    "requires-inputs" : ["file_input"],
    // Can also specify a list of ids that are not permitted to be used (added to command-line)
    // when this parameter is active (given a value)
    "disables-inputs" : ["num_input"]
  }],
  // This section specifies the set of output files from the tool
  "output-files" : [{
    "id" : "logfile", // As above, a unique id for the output
    "name" : "Log file",
    "description" : "The output log file from the example tool",
    // Path-template specifies where the output file will be
    // Note that it can automatically depend on the input values
    "path-template" : "log-[STRING_INPUT].txt",
    // This specifies the substitution key of path-template into the command-line
    "value-key" : "[LOG]",
    // Any input keys (e.g. here str_input) will have any of the following extensions removed
    // from their value before substitution (e.g. if 'in.csv' was given to str_input, 'in' would
    // would be the resulting substituted value in [STRING_INPUT])
    "path-template-stripped-extensions" : [".txt", ".csv"],
    // As above, optional : false requires that the output file exists
    "optional" : false
  }, {
    // We can also represent sets of output files using a wildcard
    "id" : "output_files",
    "name" : "outfiles",
    // When capturing multiple files, list should be true
    "list" : true,
    // A regular expression style wildcard (*) is used to capture multiple files
    "path-template" : "output/*_exampleOutputTag.resultType",
    "optional" : true // I.e. this output may not necessarily be created
  }],
  // Parameter groups represent semantically related sets of input parameters. They can be used
  // to specify inter-parameter disabling/requirement, and to help automatic UI generation on platforms.
  "groups" : [{
    "id" : "an_example_group",
    "name" : "Example group 1",
    "description" : "This group forces us to choose exactly one of the file or the enum input to use.",
    // The set of ids that form the member parameters of the group
    "members" : ["file_input", "enum_input"],
    // When true, only one of the group members can be active (given as input) at a time
    "mutually-exclusive" : true,
    // When true, at least one of the group members must be specified in the input values
    "one-is-required" : true
  }]
}
```

See the [cbrain-plugins-neuro repo](https://github.com/aces/cbrain-plugins-neuro/tree/master/cbrain_task_descriptors "Boutiques example descriptors at cbrain-plugins-neuro") for some example descriptors currently in use.

More examples (TODO)
* Inputs without value-key.
* List outputs without a value-key.

