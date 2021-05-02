# turdokken
A utility to run any bash script inside of an Alpine Linux Docker container with
given dependencies installed, volumes mounted, capabilities added. Use it as
the 'shebang' line of the script.

I wrote turdokken because managing multiple Node, Ruby, Python, Postgres, Mysql, nginx, redis, etc, etc, etc versions and their associated extensions and libraries on my workstation is a headache. Sometimes you just need to run a script in an environment with certain binaries, languages, or libraries installed, but you don't want to have to install and manage those things on your computers. Turdokken uses the elegant power of Docker to build a capable environment to do some work with no hassle and perfect repeatability, then it goes away. Nobody needs to install anything other than Docker. People can just get work done instead of fiddling with installing a bunch of stuff all the time.

See the `examples/` folder if you are that type of learner. Otherwise, documentation follows...

## Prerequisites
You must run this on a system with [Docker](https://www.docker.com/) and `/bin/bash` installed.

## Usage
Install turdokken somewhere in your $PATH, and use it as the shebang line in your script. For example, if your script is called `myscript.sh` and contains the following:

```
#!/usr/bin/env -S turdokken --install mysql-client

mysql --version
uname -a
echo $HOST_USERNAME
echo $@
```

Then executing `./myscript.sh foo bar` will output:

```
mysql  Ver 15.1 Distrib 10.4.13-MariaDB, for Linux (x86_64) using readline 5.1
Linux 3f73bdfc43c3 4.19.76-linuxkit #1 SMP Tue May 26 11:42:35 UTC 2020 x86_64 Linux
yourlogin
foo bar
```

NOTE: It is critical that the shebang line starts with `#!/usr/bin/env -S turdokken`.

In this case, turdokken builds a Docker image with the `mysql-client` package installed, then runs the container, mounts the script inside the container, then invokes the script using `bash` inside the container. Expanding on this example, you can build a whole suite of MySQL utilities and run them on a computer that does not have any software other than Docker installed.

See the `examples/` directory for, uh, more examples.

### Arguments
#### --install foo
Installs package `foo`. Use as many --install X arguments as you need.

#### --volume /path/to/local:/path/inside/container:optional_flags
Bind-mounts `/path/to/local` in the container as `/path/inside/container` with the given optional flags (e.g. `ro`). Use the [same syntax](https://docs.docker.com/engine/reference/run/#volume-shared-filesystems) as the `docker run` command expects on the command line.

#### --interactive
The script will run interactively, i.e. docker will be run with the `-it` flags.

#### --capability foo
The given capability will be specified on the `docker run` line. Refer to [docker docs](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities) for valid values.

### Other Notes
* The environment variable `$HOST_USERNAME` is defined with the value of `$USER` from the host, i.e. your username. Otherwise, `$USER` in the container is `root`.
* The body of your script is mounted and run in the container at `/home/app/script`.

## Contributing
There are so many things that can be added to turdokken. Please fork and open a pull request if you want your fixes or enhancements to be included.

## Etymology
The name refers to a Turducken -- a turkey, stuffed with a duck, stuffed with a chicken. The model here is a lot like that, with source code and execution contexts at more than one layer, but all in one file. It could have been named `turdocker` but I wanted to give a nod to Don Dokken. And it's kind of a turd, so there's that.
