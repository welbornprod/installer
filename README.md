Python Installer
================

This is an installer designed for small python projects. You can drop it in
the top level directory of your project and it will handle the installation
for you. It's configurable by providing an `installer.json` file in the same
directory. It features automatic cleanup on failure, and an uninstall option.

When no configuration is provided the default action is to install
the top-level files (not including directories) into the proper application
directory.

Executable files are symlinked in the proper executable directory.
When no configuration is provided, any file that is executable (`chmod +x`) is
symlinked. If no executables are found, then any python file (`.py`) except
files starting with '__' are used.

See the configuration options and install types below for more information.

I would recommend copying this to your project directory, renaming it to
`install`, and making it executable (`chmod +x install`).


Install types:
--------------

###Global:

When `install.py` is ran with no arguments, the default install type is global.
Global install looks for common application directories such as
`/usr/local/share`, `/usr/share`, and `/opt`. Executables go in the common
`/bin` directories, such as `/usr/local/share/bin`, `/usr/local/bin` or
`/usr/bin`.


###Local:

When `install.py --user` is used, files are installed relative to the
`/home/user` directory. Applications are installed in `~/.local/share`,
`~/local/share`, and executables are install in `~/bin`, `~/.local/bin`,
and `~/local/bin`.


When looking for proper directories, the first existing directory is used.
Installations are aborted if no proper directory can be found.

A warning is emitted if the chosen executable path is not found in `$PATH`.


installer.json
--------------

Configuration is loaded from a config file called `installer.json`, which is
JSON that supports C-style comments.

Configuration is merged with the defaults, so omitting an option means the
default is automatically used.

Current configuration options and defaults:

```

    {
        // Default paths, files, behavior.
        "paths": {
            // Override standard exe dirs and install_type choice.
            "exe_base": null,

            // Override standard app dirs and install_type choice.
            "install_base": null,

            /*  Top level directory name under the main app dir.
                If not provided, it is determined by the cwd.
                So '/home/me/stuff/myapp' would make the toplevel 'myapp'.
                The final install directory would then be something like:
                    /usr/local/share/myapp
            */
            "toplevel": "<Top-level from CWD>",

            /*  Recurse the current directory when looking for included files.
                If true, this will recursively include all sub-directories.
            */
            "recurse": false,

            /*  Included file patterns (relative to the current dir).
                These are regex patterns.
                All relative paths that match these patterns will be included.
            */
            "files": [".+"],

            /*  Excluded file patterns (relative to the current dir).
                These are regex pattern.
                All relative paths from included files that match these
                patterns will be excluded.
            */
            "excludes": [],

            /*  Executable files (for symlinking in the ../bin directory)
                When provided, file names relative to the top-level directory
                must be used, and automatic executable detection is disabled.
                File names must be found in the included files to work.
            */
            "executables": []
        },

        /*  Install type, 'global', 'local', or null.
            When provided, the command-line options have no effect and the
            install type is forced.
            The default is null, which means the install type depends on
            the user's command-line options.
        */
        "install_type": null
    }
```


C-style comments are currently supported by the installer, though they are not
valid JSON. Any config options that are not recognized are ignored.
Misconfiguration of known config settings will cause the installer to abort.


In the future python dependencies may be handled automatically, or at least
warned about. For now you or your users will have to handle them yourself,
sorry.


Automatic Cleanup:
------------------

If the installer encounters a fatal error during the installation, an attempt
is made to remove the half-installed files. A warning is emitted to the user.


Uninstall/Remove:
-----------------

When installation is successful, a file is created (`installed_files.txt`)
that contains a list of all installed files with their full path. A copy of
both the installer and `installed_files.txt` are installed along with the rest
of the application to aid in future removal.

Running `install.py remove` or `install.py uninstall` will read the
list of installed files, and attempt to remove them. Warnings are emitted if
the files cannot be removed.


Compatibility:
--------------

This script uses **Python 3** by default, but is compatible with **Python 2**.
You can change the shebang line (`#!/usr/bin/env python3`) to suit your needs.

Installer Dependencies:
-------------

`install.py` currently depends on `docopt` to parse command-line options.
You can get it by running `pip install docopt`.
