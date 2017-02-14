* [[Plugins]]
* [[Contributing]]
  + [[Style]]
  + [[Releasing]]

## Debugging

Compile without `--release` argument to enable debugging:
```sh
$ scons
```

Then run astroid using `gdb`:

```sh
$ gdb --args ./astroid --no-auto-poll
```

## Profiling

Compile with the `--profiler` option:
```sh
$ scons --profiler
```

