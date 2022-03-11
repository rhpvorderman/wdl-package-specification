# WDL Package specification draft-1

## Archive format

### USTAR
WDL packages are stored in [UNIX Standard tar format (UStar)](
https://en.wikipedia.org/wiki/Tar_(computing)#UStar_format).

This imposes (among others) the following restrictions on included files:

- Filenames must be ASCII-encoded
- Filenames in the archive can be a maximum of 255 characters long.

### WDL package specific restrictions

WDL package files must be able to build reproducibly and work across multiple
platforms. Therefore the following restrictions apply.

- Individual members of the tar file must be packaged in order of the filename
  inside the tar file. Ordering is done by comparing the ASCII value of the 
  characters.
- Only normal files are allowed. Links must be dereferenced.
- Required values for UStar fields in the table below

field | value 
---|---
file mode | 0644 (-rw-r--r--)
uid | 0
gid | 0
link indicator | 0 (normal file)
type flag | 0 (normal file)
owner user name | NULL (empty string)
owner group name | NULL (empty string)
device major number | 0
device minor number | 0

### Compression

The USTAR package may be compressed with the following compression algorithms

- None, uncompressed using the `.tar` extension.
- DEFLATE, using a `gzip` container and using the `.tar.gz`extension.
- LZMA2, using a `xz` container and using the `.tar.xz` extension.

The above algorithms and file extensions must be supported. Other algorithms
and extensions must not be supported.

## Contents

### The manifest

Each WDL Package file must contain a file called `MANIFEST.json` at the root
level of the archive directory. Hereafter called "the manifest".  The manifest
contains a single dictionary with the following fields:

Field name | field type | required | Description
---|---|---|---
wdl_package_spec_version | String | yes | The version of the WDL package specification that this package adhers too.
version | String | yes | The version of the packaged WDL contents.
license_file | String | yes | The path in the archive where the license for the package contents is stored.
license_id | String | yes | A [SPDX License identifier](https://spdx.org/licenses/). In case the License does not have a SPDX identifier this field must be present too and set to NULL.
main_workflow_url | String | no | The main workflow URL when the wdl package contains a workflow. Not required since packages can also be used to distribute tasks.
additional_files | Array[String] | no | Additional files shipped in the package, such as READMEs, examples, settings files etc.

### License file

A License file is required to let users know what they can and cannot do with
the contents of the package. The filename is not fixed, but the path must be 
specified in the manifest.

### Additional files
Non-WDL files may also be shipped with the package. All additional files must 
be listed in the manifest.

### WDL Files

Any WDL file can be packaged. Preferably these WDL files have either:

- no imports
- file imports that reference other files in the package
- versioned package imports referring a wdl package repository

In the case of http imports the packaging utility must choose one of the 
following actions:

- Download the http imports, include them in the package file and rewrite the
  import statements.
- Fail with an error message.
