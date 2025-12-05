# eXtensible ARchiver
A fork/clone of the subversion xar repository that includes several enhancements
and bug fixes including very basic command line signature support.

The original project page with extensive documentation on signing, pre-bootstrapped
downloads, etc. can be found at [http://mackyle.github.io/xar](http://mackyle.github.io/xar)

## Enhancements
Notable enhancements are command line signature support, `--strip-components` and
`--to-stdout` support, cygwin build support, support for arbitrary checksum
message digests (e.g. sha256, sha512), improved `liblzma` compatibility, many
new command synonyms, and lots and lots of bug fixes and warning eliminations.

## License
See the [LICENSE](./LICENSE) file.  This is a "New BSD License" aka a
"Modified BSD license" or a "3-clause BSD license".

## Building
A standard configure and make will suffice.  If building directly from the git
sources the `autogen.sh` script must be run to generate the `configure` script.
The `autogen.sh` script takes a single, optional `--noconfigure` argument to
suppress running `configure`.  If building from a release tarball, the `configure`
script has already been generated.

After a successful `configure` simply do a standard `make`, but it must be GNU make
(which is typically installed as either `make` or `gmake`).

After a successful build, the standard `sudo make install` (again GNU make must
be used) will install.

See [INSTALL.md](./INSTALL.md) file for additional information.
