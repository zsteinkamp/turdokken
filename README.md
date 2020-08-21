# turdokken
A utility script to run an optionally interactive bash script inside of an
Alpine Linux container with given dependencies installed, volumes mounted,
capabilities added. Use it as the 'shebang' line of the script.

## Usage
Use turdokken as the shebang line in your script. For example, if your script is called `myscript.sh` and contains the following:

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

NOTE: The shebang line is important that it starts with `#!/usr/bin/env -S turdokken`.

### Arguments
#### --install foo
Installs package `foo`. Use as many --install X arguments as you need.

#### --volume /path/to/local:/path/inside/container:optional_flags
Bind-mounts `/path/to/local` in the container as `/path/inside/container` with the given optional flags (e.g. `ro`). Use the same syntax as the `docker` command expects on the command line.

#### --interactive
The script will run interactively, i.e. docker will be run with the `-it` flags.

#### --capability foo
The given capability will be specified on the `docker run` line. Refer to docker docs for valid values.

### Other Notes
* The environment variable `$HOST_USERNAME` is defined with the value of `$USER` from the host, i.e. your username. Otherwise, `$USER` in the container is `root`.
* The body of your script is mounted and run in the container at `/home/app/script`.

## Contributing
There are so many things that can be added to turdokken. Please fork and open a pull request if you want your fixes or enhancements to be included.

## Etymology
The name refers to a Turducken -- a turkey, stuffed with a duck, stuffed with a chicken. The model here is a lot like that, with source code and execution contexts at more than one layer, but all in one file. It could have been named `turdocker` but I wanted to give a nod to Don Dokken. And it's kind of a turd, so there's that.
