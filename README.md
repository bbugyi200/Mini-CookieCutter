# cookie [![Tweet](https://img.shields.io/twitter/url/http/shields.io.svg?style=social)](https://twitter.com/intent/tweet?text=Stop%20repeating%20yourself!%20Cookie%20templates%20make%20writing%20scripts,%20LaTeX%20documents,%20Makefiles,%20and%20other%20one-off%20files%20easier%20than%20ever!&url=https://github.com/bbugyi200/funky&via=bryan_bugyi&hashtags=Linux,commandlineftw,developers)

**Stop repeating yourself! Cookie templates make writing scripts, LaTeX documents, Makefiles, and other one-off files easier than ever!**

[![Build Status](https://travis-ci.org/bbugyi200/cookie.svg?branch=master)](https://travis-ci.org/bbugyi200/cookie) [![codecov](https://codecov.io/gh/bbugyi200/cookie/branch/master/graph/badge.svg)](https://codecov.io/gh/bbugyi200/cookie) [![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

![demo]

## Usage
```
Usage: cookie [-d] [-D TARGET_DIR] [-f] [-m MODE] [-v] [-x] TEMPLATE [TARGET]
       cookie -c
       cookie -e TEMPLATE
       cookie -h
       cookie -l [TEMPLATE]
       cookie -r TEMPLATE

Initializes a new file (TARGET) using a predefined template (TEMPLATE).
The target file can be a new script, configuration file, markup file, etc....
After the target file has been initialized, it is opened for editing using the
system's default editor.

Positional Arguments:
    TARGET          The name of the file to initialize.

Optional Arguments:
    -d | --debug
        Enable debug mode.

    -c | --config
        Edit the configuration file.

    -D DIR | --bin-subdir DIR
        Initialize TARGET into DIR, which should be a subdirectory of the
        default bin directory (see the configuration file).

    -e TEMPLATE | --edit TEMPLATE
        Add / edit cookie template.

    -f | --force
        Force TARGET initialization to be relative to the current
        directory. This option essentially overrides the ROOT_DIR
        configuration setting. Enabled by default for non-executable
        targets.

    -h | --help
        View this help message.

    -l [TEMPLATE] | --list [TEMPLATE]
        If TEMPLATE is provided, output template contents to STDOUT.
        Otherwise, list available templates. 

    -m MODE | --mode MODE
        Sets file mode bits. Accepts any form for MODE that is recognized
        by the 'chmod' command.

    -r TEMPLATE | --remove TEMPLATE
        Delete cookie template.

    -x
        Make TARGET executable. Equivalent to '-m +x'.

    -v | --verbose
        Enable verbose output.
```

## Templates

Templates are stored in the directory specified by `$COOKIE_DIR` which, if not specified in the configuration file (see the [Configuration](#config) section), defaults to `~/.cookiecutters` (the same directory used by `cookiecutter` to store templates).

See my personal [templates] for examples on how you can use templates.

### Template Variables and Statements
While not as full-featured as the [jinja] template engine that [cookiecutter] uses, there are a few special variables and statements available. The syntax for these will be familiar if you have used [jinja] in the past.

#### Variable Substitution
cookie also recognizes template variables of the form:
```
{{ foobar }}
```
This string will be replaced by the value of the environment variable `foobar` if it exists. Otherwise, the user will be prompted to provide a value for `foobar` on the command-line.

To ensure compatibility with files in cookiecutter templates, you may also preface the variable with `cookiecutter` followed by a period. Hence, the following statement is equivalent to the one discussed above:
``` 
{{ cookiecutter.foobar }}
```

#### Mark Start Point for Editing (only works when vim is set as the default system editor)
If the following statement is found in the template, vim will start with the cursor positioned on the line and column of the first curly brace (after removing the statement) and will start in INSERT mode:
```
{% INSERT %}
```

The following statement does the same thing but will start vim in NORMAL mode (vim's default behavior):
```
{% NORMAL %}
```

## <a name="config">Configuration</a>

The configuration file can be found at `$XDG_CONFIG_HOME/cookie/config`. The following options are available:

``` ini
# The target file will be initialized in a location relative to this directory
# unless you specify the `-f` option. In which case the target file will be
# initialized relative to the current directory.
#
# Defaults to "./" (the current directory).
ROOT_DIR=

# The target file is initialized in $ROOT_DIR/$DEFAULT_TARGET_DIR
# unless the `-D {DIR}` option is used. In which case the target file will
# be initialized to $ROOT_DIR/{DIR}.
DEFAULT_TARGET_DIR=

# If specified, this command is evaluated after (and if) the target file
# has its executable bit set. This can be used to create symlinks to
# the target file (using `stow`, for example).
#
# The $TARGET variable, which contains the full path of the target file,
# will be injected into the environment of this command.
EXEC_HOOK_CMD=

# The directory used to store cookie templates.
# 
# Defaults to "~/.cookiecutters".
COOKIE_DIR=
```

## Using Shell Aliases / Functions

You can of course run `cookie` directly, but I have not found that to
be very convenient. Instead, I have created a variety of shell aliases and
functions which serve as custom initialization commands that are specific to a
single goal and filetype. Here are a few examples:

``` bash
alias ainit='cookie -t template.awk -D awk -x'
alias binit='cookie -t minimal.sh -x'
alias Binit='cookie -t full.sh -x'
hw() { ASSIGNMENT_NUMBER="$1" cookie -t hw.tex "${@:2}" HW"$1"/hw"$1".tex; }
alias minit='cookie -t c.make Makefile'
alias mtinit='cookie -t gtest.make Makefile'
alias pyinit='cookie -t template.py -x'
pytinit() { cookie -t pytest.py test_"$1".py; }
alias texinit='cookie -t template.tex'
```

## Examples

Let us now take a look at a few examples of how cookie might be useful. 

For reference, my personal cookie templates can be found [here][templates] and these are the configuration settings that I use:
``` bash
PARENT_BIN_DIR="/home/bryan/Dropbox/bin"
DEFAULT_BIN_SUBDIR="main"
EXEC_HOOK_CMD=/usr/local/bin/clinks
```
where `/home/bryan/Dropbox/bin` is a home for [this][scripts] GitHub repository. (The [clinks] script wraps a bunch of [stow] commands which makes creating symlinks to a system `bin` folder a walk in the park while still keeping my scripts organized the way I like in my filesystem.)

To initialize a minimal bash script named `foo` into the `/home/bryan/Dropbox/main` directory (where I keep most of my scripts), I could run the following command:
```
binit foo
```
Suppose instead that I wanted to initialize a new productivity script named `bar` into the `/home/bryan/Dropbox/GTD` directory. Furthermore, suppose that I know that `bar` might get complicated (and thus needs to scale well). I could then choose to run
```
Binit -D GTD bar
```
to initialize a full featured bash script (bells and whistles included) into the `/home/bryan/Dropbox/GTD` directory.

## Advanced Usage

I wrote a short [blog post][blog] describing a few of cookie's more advanced features.

## Similar Projects

* [cookiecutter] - A command-line utility that creates projects from cookiecutters (project templates).
* [j2cli] - Jinja2 Command-Line Tool.
* [plop] - Micro-generator framework that makes it easy for an entire team to create files with a level of uniformity.

## Installation

#### Root Installation
If you have root permissions on your machine, installation is as simple as cloning the repository with `git clone https://github.com/bbugyi200/cookie`, traveling into the project directory (`cd cookie`), and then running `sudo make install`.

#### User Installation
If you do not have root permissions on your machine, you can still install cookie by using an alternate bin directory. This can be accomplished, for example, by cloning the repository and using `cd` to travel to the repository directory (as described in the previous section) and then running the following command:
```
make DESTDIR=/home/<user>/.local PREFIX= install
```
where `<user>` should be replaced with your username. Keep in mind that, for this to work, the `/home/<user>/.local/bin` directory must be added to your system's path.

#### ZSH Completion

The appropriate completion function should be installed automatically. If necessary, however, you can enable ZSH command-line completion manually by copying the [\_cookie][zsh-completion] file to a directory listed by your system's `$fpath` variable (normally the `/usr/share/zsh/site-functions` directory works).

[blog]: https://bryanbugyi.com/blog/tips-and-tricks-for-using-cookie/
[logo]: https://raw.githubusercontent.com/bbugyi200/cookie/master/img/logo.png
[demo]: https://raw.githubusercontent.com/bbugyi200/cookie/master/img/demo.gif "Cookie Demonstration GIF"
[jinja]: https://github.com/pallets/jinja
[cookiecutter]: https://github.com/audreyr/cookiecutter
[scripts]: https://github.com/bbugyi200/scripts
[clinks]: https://github.com/bbugyi200/scripts/blob/master/main/clinks
[templates]: https://github.com/bbugyi200/dotfiles/tree/master/.cookiecutters
[stow]: https://www.gnu.org/software/stow/manual/stow.html
[travis]: https://travis-ci.org/bbugyi200/cookie.svg?branch=master
[codecov]: https://codecov.io/gh/bbugyi200/cookie/branch/master/graph/badge.svg
[j2cli]: https://github.com/kolypto/j2cli
[zsh-completion]: https://github.com/bbugyi200/cookie/blob/master/scripts/zsh/_cookie
[plop]: https://github.com/amwmedia/plop

#### macOS

MacOS uses a different version of getopt than Linux does. You can install a GNU version of getopt and set it as the default getopt for your system using brew:

```
brew install gnu-getopt
brew link --force gnu-getopt
```
See this [Stack Overflow answer](https://stackoverflow.com/questions/12152077/how-can-i-make-bash-deal-with-long-param-using-getopt-command-in-mac) before modifying your `gnu-getopt`.
