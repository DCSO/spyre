[![Build Status](https://travis-ci.org/spyre-project/spyre.svg?branch=master)](https://travis-ci.org/spyre-project/spyre)

# Spyre

***...a simple, self-contained modular host-based IOC scanner***

_Spyre_ is a simple host-based IOC scanner built around the
[YARA](https://github.com/VirusTotal/yara) pattern matching engine and
other scan modules. The main goal of this project is easy
operationalization of YARA rules and other indicators of compromise.

Users need to bring their own rule sets. The
[awesome-yara](https://github.com/InQuest/awesome-yara) repository gives
a good overview of free yara rule sets out there.

_Spyre_ is intended to be used as an investigation tool by incident
responders. It is **not** meant to evolve into any kind of endpoint
protection service.

## Overview

Using _Spyre_ is easy:

1. Add YARA signatures. Per default, YARA rules for file scans are
   read from `filescan.yar`, `procscan.yar` for file scans, process
   memory scans, respectively. The following options exist for
   providing rules files to _Spyre_ (and will be tried in this order):
    1. Add the rule files to ZIP file and append that file to the
      binary.
    2. Add the rule files to a ZIP file name `$PROGRAM.zip`: If the
      Spyre binary is called `spyre` or `spyre.exe`, use `spyre.zip`.
    3. Put the rule files into the same directory as the binary.

   ZIP file contents may be encrypted using the password `infected`
   (AV industry standard) to prevent antivirus software from mistaking
   parts of the ruleset as malicious content and preventing the scan.

   YARA rule files may contain `include` statements.
2. Deploy, run the scanner
3. Collect report

## Configuration

Run-time options can be either passed via command line parameters or
via file that `params.txt`. Empty lines and lines starting with the
`#` character are ignored. Every line is interpreted as a single
command line argument.

If a ZIP file has been appended to the _Spyre_ binary, configuration
and other files such as YARA rules are only read from this ZIP file.
Otherwise, they are read from the directory into which the binary has
been placed.

Some options allow specifying a list of items. This can be done by
separating the items using a semicolon (`;`).

##### `--high-priority`

Normally (unless this switch is enabled), _Spyre_ instructs the OS
scheduler to lower the priorities of CPU time and I/O operations, in
order to avoid disruption of normal system operation.

##### `--set-hostname=NAME`

Explicitly set the hostname that will be used in the log file and in
the report. This is usually not needed.

##### `--loglevel=LEVEL`

Set the log level. Valid: trace, debug, info, notice, warn, error,
quiet.

##### `--report=SPEC`

Set one or more report targets, separated by a semicolon (`;`).
Default: `spyre.log` in the current working directory, using the plain
format.

A different output format can be specified by appending
`,format=FORMAT`. The following formats are currently supported:

- `plain`, the default, a simple human-readable text format
- `tsjson`, a JSON document that can be imported into
  [Timesketch](https://github.com/google/timesketch)

##### `--path=PATHLIST`

Set one or more specific filesystem paths to scan. Default: `/` (Unix)
or all fixed drives (Windows).

##### `--yara-file-rules=FILELIST`

Set list of YARA rule files for scanning files on the system. Default:
Use `filescan.yar` from appended ZIP file, `$PROGRAM.ZIP`, or current
working directory.

##### `--yara-proc-rules=FILELIST`

Set list of YARA rule files for scanning processes' memory
regions. Default: Use `procscan.yar` from appended ZIP file,
`$PROGRAM.ZIP`, or current working directory.

##### `--max-file-size=SIZE`

Set maximum size for files to be scanned using YARA. Default: 32MB

##### `--ioc-file=FILE`

##### `--proc-ignore=NAMELIST`

Set names of processes that will not be scanned.

## Notes about YARA rules

YARA is configured with default settings, plus the following explicit
switches (cf. `3rdparty.mk`):

- `--disable-magic`
- `--disable-cuckoo`
- `--enable-dotnet`
- `--enable-macho`
- `--enable-dex`

## Building

Spyre can be built for 32bit and 64bit Linux and Windows targets.

### Debian Buster (10.x) and later

On a Debian/buster system (or a chroot) in which the following packages
have been installed:

- make
- gcc
- gcc-multilib
- gcc-mingw-w64
- autoconf
- automake
- libtool
- pkg-config
- wget
- patch
- sed
- golang-_$VERSION_-go, e.g. golang-1.8-go. The Makefile will
  automatically select the newest version unless `GOROOT` has been
  set.
- git-core
- ca-certificates
- zip

This describes the build environment that is exercised regularly via
CI.

### Fedora 30 and later

The same build has also been successfully tried on Fedora 30 with the
following packages installed:

- make
- gcc
- mingw{32,64}-gcc
- mingw{32,64}-winpthreads-static
- autoconf
- automake
- libtool
- pkgconf-pkg-config
- wget
- patch
- sed
- golang
- git-core
- ca-certificates
- zip

Once everything has been installed, just type `make`. This should
download archives for _musl-libc_, _openssl_, _yara_, build those and
then build _spyre_.

The bare _spyre_ binaries are created in `_build/<triplet>/`.

Running `make release` creates a ZIP file that contains those binaries
for all supported architectures.

## Coding

See [HACKING.md](HACKING.md)

## Copyright

Copyright 2018-2020 DCSO Deutsche Cyber-Sicherheitsorganisation GmbH

Copyright 2020      Spyre Project Authors (see: AUTHORS.txt)

## License

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU Lesser General Public License as
published by the Free Software Foundation, either version 3 of the
License, or (at your option) any later version.

See the LICENSE file for the full license text.
