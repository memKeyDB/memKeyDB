This is README for MemKeyDB - Redis fork, adjusted to store objects on both DRAM and Intel Optane Persistent Memory.
The detailed documentation of e.g. data structures, list of commands can be found at [redis.io](https://redis.io).

What is MemKeyDB?
--------------

MemKeyDB is a database that fully uses the advantages of DRAM and Intel Optane Persistent Memory. The main concept is to store the bigger size values on the Intel Optane Persistent Memory, configured as System RAM (KMEM DAX), while the smaller allocations are preffered to be kept on DRAM. The ratio of data stored on PMEM and DRAM is selected in regard to obtaining the best performance.
The data stored on the medium is volatile and the persistency is preserved with RDB/AOF mechanism.

Additional Resources
--------------
Visit our website: https://memkeydb.io/

Memkind allocator website: http://memkind.github.io/memkind/

PMDK blog entry about MemKeyDB: [PMDK blog](https://pmem.io/2020/09/25/memkeydb.html)


Building MemKeyDB
--------------
You will need to install the following required packages on the build system:
* libdaxctl-devel (v66 or later) -- required by memkind

Libmemkind is one submodule of this repo, so you have to init this submodule in order to get code

    % git submodule init
    % git submodule update

It is as simple as:

    % make

To build with TLS support, you'll need OpenSSL development libraries (e.g.
libssl-dev on Debian/Ubuntu) and run:

    % make BUILD_TLS=yes

To build with systemd support, you'll need systemd development libraries (such 
as libsystemd-dev on Debian/Ubuntu or systemd-devel on CentOS) and run:

    % make USE_SYSTEMD=yes

To append a suffix to MemKeyDB program names, use:

    % make PROG_SUFFIX="-alt"

You can run a 32 bit MemKeyDB binary using:

    % make 32bit

After building MemKeyDB, it is a good idea to test it using:

    % make test

Fixing build problems with dependencies or cached build options
---------

MemKeyDB has some dependencies which are included into the `deps` directory.
`make` does not automatically rebuild dependencies even if something in
the source code of dependencies changes.

When you update the source code with `git pull` or when code inside the
dependencies tree is modified in any other way, make sure to use the following
command in order to really clean everything and rebuild from scratch:

    make distclean

This will clean: jemalloc, lua, hiredis, linenoise, memkind.

Also if you force certain build options like 32bit target, no C compiler
optimizations (for debugging purposes), and other similar build time options,
those options are cached indefinitely until you issue a `make distclean`
command.

Allocator
---------

Selecting a non-default memory allocator when building MemKeyDB is done by setting
the `MALLOC` environment variable. MemKeyDB is compiled and linked against
memkind malloc by default.

To force compiling against libc malloc, use:

    % make MALLOC=libc

To compile against jemalloc on Mac OS X systems, use:

    % make MALLOC=jemalloc

To compile against memkind, use:

    % make MALLOC=memkind

Verbose build
-------------

MemKeyDB will build with a user friendly colorized output by default.
If you want to see a more verbose output use the following:

    % make V=1

Running MemKeyDB
-------------

To run MemKeyDB with the default configuration just type:

    % cd src
    % ./redis-server

If you want to provide your redis.conf, you have to run it using an additional
parameter (the path of the configuration file):

    % cd src
    % ./redis-server /path/to/redis.conf

It is possible to alter the MemKeyDB configuration by passing parameters directly
as options using the command line. Examples:

    % ./redis-server --port 9999 --replicaof 127.0.0.1 6379
    % ./redis-server /etc/redis/6379.conf --loglevel debug

All the options in redis.conf are also supported as options using the command
line, with exactly the same name.

Installing MemKeyDB
-----------------

In order to install MemKeyDB binaries into /usr/local/bin just use:

    % make install

You can use `make PREFIX=/some/other/directory install` if you wish to use a
different destination.

Make install will just install binaries in your system, but will not configure
init scripts and configuration files in the appropriate place. This is not
needed if you just want to play a bit with MemKeyDB, but if you are installing
it the proper way for a production system, we have a script that does this
for Ubuntu and Debian systems:

    % cd utils
    % ./install_server.sh

_Note_: `install_server.sh` will not work on Mac OSX; it is built for Linux only.

The script will ask you a few questions and will setup everything you need
to run MemKeyDB properly as a background daemon that will start again on
system reboots.

You'll be able to stop and start MemKeyDB using the script named
`/etc/init.d/redis_<portnumber>`, for instance `/etc/init.d/redis_6379`.

Code contributions
-----------------

Note: by contributing code to the MemKeyDB project in any form, including sending
a pull request via Github, a code fragment or patch via private email or
public discussion groups, you agree to release your code under the terms
of the BSD license that you can find in the [COPYING][1] file included in the Redis
source distribution.

Please see the [CONTRIBUTING][2] file in this source distribution for more
information.

[1]: https://github.com/memKeyDB/memKeyDB/blob/6.0-devel/COPYING
[2]: https://github.com/memKeyDB/memKeyDB/blob/6.0-devel/CONTRIBUTING
