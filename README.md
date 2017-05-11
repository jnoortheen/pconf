# pconf
Hierarchical python configuration with files, environment variables, command-line arguments. Inspired by [nconf](https://github.com/indexzero/nconf).

## Example

``` python
from pconf import Pconf
import json

"""
Setup pconf config source hierarchy as:
  1. Environment variables
  2. A JSON file located at 'path/to/config.json'
"""
Pconf.env()
Pconf.file('path/to/config.json', encoding='json')

# Get all the config values parsed from the sources
config = Pconf.get()

# Just print everything nicely
print json.dumps(config, sort_keys=True, indent=4)
```
Run the above script:
``` bash
python example.py
```
The output should be something like this:
```
{
    "HOSTNAME": "bb30700d22d8",
    "TERM": "xterm",
    "PATH": "/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
    "PWD": "/",
    "SHLVL": "1",
    "HOME": "/root",
    "no_proxy": "*.local, 169.254/16",
    "_": "/usr/bin/env",
    "example": {
        "another": "stuff",
        "key": "value"
    }
}
```

## Hierarchical configuration
Pconf is designed to be used with multiple sources of configuration values with the user being able define the priority of each of these as a hierarchy. The supported sources described below can be setup in any order without any hardcoded defaults. Priority meaning if a configuration key appears in multiple sources the value from the sources higher up in the hierarchy takes precedence. The order in which the sources are attached defines the priority in the hierarchy.

The available sources (more details about the below) in a sensible order:
1. **overrides** - Loads data passed to it
2. **argv** - Parses command line arguments to the process
3. **env** - Parses environment variables
4. **file** - Parses config files of various formats
5. **defaults** - Loads data passed to it

## Config sources

### Defaults
**Not yet implemented**

### Overrides
**Not yet implemented**

### Argv
**Not yet implemented**

### Env
Responsible for loading values parsesd from `os.environ` into the configuration hierarchy
``` python
# Just load all the variables available for the process
Pconf.env()

# A separator can be specified for nested keys
Pconf.env(separator='__')
# This turns the 'log__file=/log' env variable into the `{'log': {'file': '/log'}}` dict

# Available variables can be whitelisted
Pconf.env(whitelist=['only', 'load', 'variables', 'listed', 'here'])

# A regular expression can be specified for matching keys also
# Keys matched by this expression are considered whitelisted
Pconf.env(match='^REGEX.*')

# Use all at once
Pconf.env(separator='__',
          match='whatever_matches_this_will_be_whitelisted',
          whitelist=['whatever', 'doesnt', 'match', 'but', 'is', 'whitelisted', 'gets', 'loaded', 'too'])
```

### File
Responsible for loading values parsesd from a given file into the configuration hierarchy

By default tries to parse file contents as literal python variables, use the `encoding` parameter to set the file format/encoding.
`/path/to/literal` contents:
``` python
{'this': 'is_a_literal_python_dict'}
```
``` python
Pconf.file('/path/to/literal')
```

Currently the supported built-in encoding is 'json'.
`/path/to/config.json` contents:
``` json
{
    "example": {
        "key": "value",
        "another": "stuff"
    }
}
```
``` python
Pconf.file('/path/to/config.json', encoding='json')
```

#### Using custom file formats
To use custom encodings supply a parser along with an encoding that is not built-in. The parser is a function that expects the file contents as its argument and returns a dict containing the parsed contents.
``` python
def custom_parser(file_contents):
    return {'example': file_contents}

Pconf.file('/path/to/custom/file', encoding='example', parser=custom_parser)
```

## Getting the config values
Use the `get` method to get the processed config values. The method returns all the values as a python dictionary, with nested values and all. Values can be accessed as expected from a `dict`.

``` python
config = Pconf.get()

print config['key']
```

## Run Tests
Test are written using the standard python unittest framework.
First install the dev requirements:
```bash
pip install -r requirements-dev.txt
```
Run the tests from the repository root like so:
```bash
py.test
```

#### Author: [Andras Maroy](https://github.com/andrasmaroy)
#### License: MIT
