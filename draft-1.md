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

### MANIFEST.JSON