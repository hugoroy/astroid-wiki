* [[Plugins]]
* [[Contributing]]
  + [[Style]]
  + [[Releasing]]

## Debugging using `gdb`

Compile without `--release` argument to enable debugging:
```sh
$ scons
```

Then run astroid using `gdb` (here without starting the automatic polling):

```sh
$ gdb --args ./astroid --no-auto-poll
```

after a crash, type e.g. `bt` at the `gdb` prompt to get a backtrace.

## Profiling using `gprof`

Compile with the `--profiler` option:
```sh
$ scons --profiler
```

To profile, run `astroid`:
```sh
$ astroid --no-auto-poll # no automatic polling
```

this creates the file `gmon.out` in the current directory. Use `gprof` to parse the output:

```sh
$ gprof astroid > prof.out
```

and use `gprof2dot` and `dot` (part of `graphviz`) to graph the output:
```sh
$ gprof2dot prof.out | dot -Tpng -o prof.png
```

now examine `prof.png`.
