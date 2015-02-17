Jinja2 Encoder Macros
=====================

Set of Jinja2 macros which help to encode Python data structure into a different
file format.


Motivation
----------

The motivation to create these macros was to be able to create an universal
description of configuration files in [Ansible](http://ansible.com). Ansible is
using YAML format as the main data input which allows to describe almost any
structure of a configuration file. Ansible converts the YAML data into a Python
data structure using primitive data types (e.g. `dict`, `list`, `string`,
`number`, `boolean`, ...). That data structure is then used in Jinja2 template
files to produce the final configuration file. The encoder macros are used to
convert the Python data structure to another format.


Supported formats
-----------------

- Erlang config
- INI
- JSON
- TOML
- XML
- YAML


Ansible example
---------------

Clone this repo into the directory where your playbook is. Then use the macros
from your playbook (e.g. `site.yaml`) as follows:

```
# Playbook example
- name: Play to test the encoder macros
  hosts: all
  vars:
    data:
      var1: val1
      var2: val2
      section1:
        aaa: asdf
        bbb: 123
        ccc: 'true'
      section2:
        ddd: asdfasd
        eee: 1234
        fff: 'false'
  tasks:
    - name: Create test.ini file
      template:
        src: templates/test.ini.j2
        dest: /tmp/test.ini
```

Where the `templates/test.ini.j2` contains:

```
#
# This file is managed by Ansible.
# Do not edit this file manually.
# Any changes will be automatically reverted.
#

{% from 'macros/ini_encode_macro.j2' import ini_encode with context -%}

{{ ini_encode(data) }}
```

And if you call the playbook:

```
$ ansible-playbook -i hosts ./site.yaml
```

You get the following in the `/tmp/test.ini`:

```
#
# This file is managed by Ansible.
# Do not edit this file manually.
# Any changes will be automatically reverted.
#

var1=val1
var2=val2

[section1]
aaa=asdf
bbb=123
ccc=true

[section2]
ddd=asdfasd
eee=1234
fff=false
```

Look into the `site.yaml` and `templates/*.j2` for more examples.


Macros parameters
-----------------

The first parameter passed to the macro is always the data structure. Any
following parameter should be addressed by a keyword (e.g. `indent="  "` where
`indent` is the keyword).


### `erlang_encode()`

- `item`

  > Variable holding the input data for the macro.

- `convert_bools=true`

  > Indicates whether Boolean values presented as a string should be converted
  > to a real Booblean value. For example `var1: 'True'` would be represented
  > as a string but using the `convert_bools=true` will convert it to a Boolean
  > like it would be defined like this: `var1: true`.

- `convert_nums=false`

  > Indicates whether number presented as a string should be converted to
  > number. For example `var1: '123'` would be represented as a string but
  > using the `convert_nums=true` will convert it to a number like it would
  > be defined like this: `var1: 123`.

- `first=[]`

  > Indicates whether the item being processed is actually a first item. It's
  > used only internally in the macro.

- `indent="  "`

  > Defines the indentation unit.

- `level=0`

  > Indicates the initial level of the indentation. Value `0` starts indenting
  > from the beginning of the line. Setting the value to higher than `0`
  > indents the content by `indent * level`.


### `ini_encode()`
- `item`

  > Variable holding the input data for the macro.

- `delimiter="="`

  > Sign separating the *property* and the *value*. By default it's set to `'='`
  > but it can also be set to `' = '`.

- `first=[]`

  > Indicates whether the item being processed is actually a first item. It's
  > used only internally in the macro.

- `quote=""`

  > Allows to set the *value* quoting. Use `quote="'"` or `quote='"'`.

- `ucase_prop=false`

  > Indicates whether the *property* should be made uppercase.


### `json_encode()`
- `item`

  > Variable holding the input data for the macro.

- `convert_bools=true`

  > Indicates whether Boolean values presented as a string should be converted
  > to a real Booblean value. For example `var1: 'True'` would be represented
  > as a string but using the `convert_bools=true` will convert it to a Boolean
  > like it would be defined like this: `var1: true`.

- `convert_nums=false`

  > Indicates whether number presented as a string should be converted to
  > number. For example `var1: '123'` would be represented as a string but
  > using the `convert_nums=true` will convert it to a number like it would
  > be defined like this: `var1: 123`.

- `indent="  "`

  > Defines the indentation unit.

- `level=0`

  > Indicates the initial level of the indentation. Value `0` starts indenting
  > from the beginning of the line. Setting the value to higher than `0`
  > indents the content by `indent * level`.


### `toml_encode()`

- `item`

  > Variable holding the input data for the macro.

- `convert_bools=true`

  > Indicates whether Boolean values presented as a string should be converted
  > to a real Booblean value. For example `var1: 'True'` would be represented
  > as a string but using the `convert_bools=true` will convert it to a Boolean
  > like it would be defined like this: `var1: true`.

- `convert_nums=false`

  > Indicates whether number presented as a string should be converted to
  > number. For example `var1: '123'` would be represented as a string but
  > using the `convert_nums=true` will convert it to a number like it would
  > be defined like this: `var1: 123`.

- `first=[]`

  > Indicates whether the item being processed is actually a first item. It's
  > used only internally in the macro.

- `indent="  "`

  > Defines the indentation unit.

- `level=0`

  > Indicates the initial level of the indentation. Value `0` starts indenting
  > from the beginning of the line. Setting the value to higher than `0`
  > indents the content by `indent * level`.

- `prevkey=""`

  > Defines the name of the previous key in the recursive macro calls. It's used
  > only internally in the macro.


### `xml_encode()`
- `item`

  > Variable holding the input data for the macro.

- `first=[]`

  > Indicates whether the item being processed is actually a first item. It's
  > used only internally in the macro.

- `indent="  "`

  > Defines the indentation unit.

- `level=0`

  > Indicates the initial level of the indentation. Value `0` starts indenting
  > from the beginning of the line. Setting the value to higher than `0`
  > indents the content by `indent * level`.

- `prevkey=none`

  > Defines the name of the previous key in the recursive macro calls. It's used
  > only internally in the macro.


### `yaml_encode()`
- `item`

  > Variable holding the input data for the macro.

- `convert_bools=true`

  > Indicates whether Boolean values presented as a string should be converted
  > to a real Booblean value. For example `var1: 'True'` would be represented
  > as a string but using the `convert_bools=true` will convert it to a Boolean
  > like it would be defined like this: `var1: true`.

- `convert_nums=false`

  > Indicates whether number presented as a string should be converted to
  > number. For example `var1: '123'` would be represented as a string but
  > using the `convert_nums=true` will convert it to a number like it would
  > be defined like this: `var1: 123`.

- `indent="  "`

  > Defines the indentation unit.

- `level=0`

  > Indicates the initial level of the indentation. Value `0` starts indenting
  > from the beginning of the line. Setting the value to higher than `0`
  > indents the content by `indent * level`.

- `quote='"'`

  > Allows to set the *value* quoting. Use `quote="'"` or `quote='"'`.

- `skip_indent=false`

  > Indicates whether the indentation should be skipped for the specific item.
  > It's used only internally in the macro.


Limitations
-----------

### Erlang, JSON, TOML and YAML macros

In these type-sensitive file formats, the Boolean values represented as string
(`"true"`, `"True"`, `"false"` and `"False"`) are automatically converted to
Boolean value (`true` and `false`). The reason for this behavior is that if you
want to pass a Boolean variable in Ansible, you have to actually quote it (e.g.
`var1: "{{ my_boolean_var }}"`). Once the variable is quoted, it's not Boolean
any more. If you want to use Boolean as a string in your final configuration
file, try to use the value with a space (e.g. `' true'`).


### XML macro

YAML doesn't allow to fully map XML data with attributes. This is why XML
attributes must be defined as part of the element name (e.g. `'elem attr1="val1"
attr2="val2"': Value of the element`). This is also the reason why elements of
the same name can only have the same set of attributes. For example the following YAML
input data:

```
---
root:
  'elem attr1="val1" attr2="val2"':
    - element value1
    - element value2
```

Will produce the following XML file (`elem` has the same set of attributes):

```
<root>
  <elem attr1="val1" attr2="val2">element value1</elem>
  <elem attr1="val1" attr2="val2">element value2</elem>
</root>
```

Another limitation of the XML macro is that attributes can not have value
derivated from a Jinja2 variable in the Ansible context.


Python example
--------------

Although the macros are intended to be used primarily in Ansible, they can also
be used directly from Python:

```
from jinja2 import Template
from yaml import load
import sys

# Load the YAML data to Python data structure
yaml_fh = open('vars/ini_test.yaml')
yaml_data = load(yaml_fh)
yaml_fh.close()

# Take only the content of the data variable
yaml_data = yaml_data['data']

# Read Jinja2 template as text
template_fh = open('macros/ini_encode_macro.j2')
template_text = template_fh.read()
template_fh.close()

# Create Jinja2 template object
t = Template(template_text)
# Convert the YAML data to INI format
output = t.module.ini_encode(yaml_data).encode('utf8')

# Print the result
sys.stdout.write(output)
```

Or you can use the `yaml_converter.py`:

```
$ ./yaml_converter.py -f json -v data -y ./vars/json_test.yaml
```


License
-------

MIT


Author
------

Jiri Tyr
