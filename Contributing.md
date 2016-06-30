Patches and pull-requests (normally against the [master](https://github.com/astroidmail/astroid) branch) should be submitted either as a [pull-request](https://github.com/gauteh/astroid/pulls) here on github or as a patch (or series of) to:

* notmuch@notmuchmail.org
* astroidmail@googlegroups.com

Useful resources on how to contribute to a project using git can be found in the git-book [Contributing to a Project](http://git-scm.com/book/en/v2/Distributed-Git-Contributing-to-a-Project), specifically the part about [format-patch](http://git-scm.com/book/en/v2/Distributed-Git-Contributing-to-a-Project#Public-Project-over-E-Mail) and the [workflow for using different branches](http://git-scm.com/book/en/v2/Distributed-Git-Contributing-to-a-Project#Public-Project-over-E-Mail) (these will form a pull-request if you choose that path).

Contributions in the form of testing and documentation is also very welcome.

> All contributions must be made under the GNU GPL v3 licence or later.

## Coding style

Please follow the [[Style]] as well as the general conventions in the source code.

## Tests

We use the [Boost Test](http://www.boost.org/doc/libs/1_57_0/libs/test/doc/html/index.html) suite, refer to the [test/](https://github.com/gauteh/astroid/tree/master/test) directory for how to set up a test. The [test_generic](https://github.com/gauteh/astroid/blob/master/test/test_generic.cc) may serve as a starting point. Remember to add the test to [test/.gitignore](https://github.com/gauteh/astroid/blob/master/test/.gitignore) and [test/SConscript](https://github.com/gauteh/astroid/blob/master/test/SConscript).

## Code completion in Vim using [YouCompleteMe](https://github.com/Valloric/YouCompleteMe)

[@ff2000](https://github.com/ff2000) set up YouCompletMe completion for astroid in vim as in issue [#14](https://github.com/gauteh/astroid/issues/14). We now include a default [.ycm_extra_conf.py](https://github.com/gauteh/astroid/blob/master/.ycm_extra_conf.py) created as described in issue [#14](https://github.com/gauteh/astroid/issues/14) and with the standard compile flags for on Arch Linux. To set up a `compile_commands.json` file one option is to use [bear](https://github.com/rizsotto/Bear) and to specify the current directory as the path to this file.

1. Install [YouCompleteMe](https://github.com/Valloric/YouCompleteMe)
1. Install [bear](https://github.com/rizsotto/Bear)
1. Make a `compile_commands.json` file by running: `devel/make_compile_commands.sh` in the source root.

## How to test a pull request

If you need to test a pull request that fixes some issue or implements a feature, you clone the repository from the author and check out the feature branch:
```sh
$ git clone https://github.com/gauteh/astroid # you might have done this already
$ cd astroid.git
$ git pull # or git pull --force in case something broke the history
```

usually you are on the `master` branch. But if you want to check out `feature1` do:
```sh
$ git checkout feature1
$ git pull # maybe add --force
$ scons # re-build
$ scons test
```

whenever you have done a new pull, recompile with:
```sh
$ scons
$ scons test
```

to go back to the master branch (or _update_ the master branch) do:
```sh
$ git checkout master
$ git pull # make sure it's up-to-date
$ scons # recompile master branch
$ scons test
```

Always make sure that tests pass before submitting a pull-request.
