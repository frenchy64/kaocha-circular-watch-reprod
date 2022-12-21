# Kaocha watch mode exits on circular dependency

Kaocha watch mode is tolerant of many compilation errors, but
in particular will exit on a circular dependency.

## Reproduction

1. Run `./bin/kaocha :checker --watch`.
2. Observe the process exits.

## Full output

```
$ ./bin/kaocha --watch
clojure.lang.ExceptionInfo: Circular dependency between circular2 and circular1
{:reason :lambdaisland.tools.namespace.dependency/circular-dependency, :node circular2, :dependency circular1}
 at lambdaisland.tools.namespace.dependency.MapDependencyGraph.depend (dependency.cljc:89)
    lambdaisland.tools.namespace.track$add_deps$fn__4674$fn__4678.invoke (track.cljc:26)
    clojure.core.protocols$iter_reduce.invokeStatic (protocols.clj:49)
    clojure.core.protocols$fn__8230.invokeStatic (protocols.clj:75)
    clojure.core.protocols/fn (protocols.clj:75)
    clojure.core.protocols$fn__8178$G__8173__8191.invoke (protocols.clj:13)
    clojure.core$reduce.invokeStatic (core.clj:6886)
    clojure.core$reduce.invoke (core.clj:6868)
    lambdaisland.tools.namespace.track$add_deps$fn__4674.invoke (track.cljc:26)
    clojure.core.protocols$iter_reduce.invokeStatic (protocols.clj:49)
    clojure.core.protocols$fn__8230.invokeStatic (protocols.clj:75)
    clojure.core.protocols/fn (protocols.clj:75)
    clojure.core.protocols$fn__8178$G__8173__8191.invoke (protocols.clj:13)
    clojure.core$reduce.invokeStatic (core.clj:6886)
    clojure.core$reduce.invoke (core.clj:6868)
    lambdaisland.tools.namespace.track$add_deps.invokeStatic (track.cljc:25)
    lambdaisland.tools.namespace.track$add_deps.invoke (track.cljc:24)
    lambdaisland.tools.namespace.track$update_deps.invokeStatic (track.cljc:33)
    lambdaisland.tools.namespace.track$update_deps.invoke (track.cljc:30)
    lambdaisland.tools.namespace.track$add.invokeStatic (track.cljc:73)
    lambdaisland.tools.namespace.track$add.invoke (track.cljc:39)
    lambdaisland.tools.namespace.file$add_files.invokeStatic (file.clj:86)
    lambdaisland.tools.namespace.file$add_files.invoke (file.clj:77)
    lambdaisland.tools.namespace.dir$update_files.invokeStatic (dir.clj:42)
    lambdaisland.tools.namespace.dir$update_files.invoke (dir.clj:36)
    lambdaisland.tools.namespace.dir$scan_files.invokeStatic (dir.clj:68)
    lambdaisland.tools.namespace.dir$scan_files.invoke (dir.clj:45)
    lambdaisland.tools.namespace.dir$scan_dirs.invokeStatic (dir.clj:92)
    lambdaisland.tools.namespace.dir$scan_dirs.invoke (dir.clj:71)
    lambdaisland.tools.namespace.dir$scan_dirs.invokeStatic (dir.clj:89)
    lambdaisland.tools.namespace.dir$scan_dirs.invoke (dir.clj:71)
    kaocha.watch$run_STAR_.invokeStatic (watch.clj:294)
    kaocha.watch$run_STAR_.invoke (watch.clj:283)
    kaocha.watch$run$fn__5007.invoke (watch.clj:329)
    clojure.lang.AFn.applyToHelper (AFn.java:152)
    clojure.lang.AFn.applyTo (AFn.java:144)
    clojure.core$apply.invokeStatic (core.clj:667)
    clojure.core$with_bindings_STAR_.invokeStatic (core.clj:1990)
    clojure.core$with_bindings_STAR_.doInvoke (core.clj:1990)
    clojure.lang.RestFn.invoke (RestFn.java:425)
    clojure.lang.AFn.applyToHelper (AFn.java:156)
    clojure.lang.RestFn.applyTo (RestFn.java:132)
    clojure.core$apply.invokeStatic (core.clj:671)
    clojure.core$bound_fn_STAR_$fn__5818.doInvoke (core.clj:2020)
    clojure.lang.RestFn.invoke (RestFn.java:397)
    kaocha.watch$run$fn__5009.invoke (watch.clj:336)
    clojure.core$binding_conveyor_fn$fn__5823.invoke (core.clj:2047)
    clojure.lang.AFn.call (AFn.java:18)
    java.util.concurrent.FutureTask.run (FutureTask.java:317)
    java.util.concurrent.ThreadPoolExecutor.runWorker (ThreadPoolExecutor.java:1144)
    java.util.concurrent.ThreadPoolExecutor$Worker.run (ThreadPoolExecutor.java:642)
    java.lang.Thread.run (Thread.java:1589)
[watch] watching stopped.
$ echo $?
2
```

## Fix notes

Try:
1. starting kaocha with circular dependencies.
2. commenting out `(:require circular2)` in `src/circular1.clj`.

This strange error happens:

```
RROR in unit (reload.clj:35)
Failed reloading circular-test:
Exception: java.io.FileNotFoundException: Could not locate circular_test__init.class, circular_test.clj or circular_test.cljc on classpath. Please check that namespaces with dashes use underscores in the Clojure file name.
 at clojure.lang.RT.load (RT.java:462)
    clojure.lang.RT.load (RT.java:424)
    clojure.core$load$fn__6908.invoke (core.clj:6161)
    clojure.core$load.invokeStatic (core.clj:6160)
    clojure.core$load.doInvoke (core.clj:6144)
    clojure.lang.RestFn.invoke (RestFn.java:408)
    clojure.core$load_one.invokeStatic (core.clj:5933)
    clojure.core$load_one.invoke (core.clj:5928)
    clojure.core$load_lib$fn__6850.invoke (core.clj:5975)
    clojure.core$load_lib.invokeStatic (core.clj:5974)
    clojure.core$load_lib.doInvoke (core.clj:5953)
    clojure.lang.RestFn.applyTo (RestFn.java:142)
    clojure.core$apply.invokeStatic (core.clj:669)
    clojure.core$load_libs.invokeStatic (core.clj:6016)
    clojure.core$load_libs.doInvoke (core.clj:6000)
    clojure.lang.RestFn.applyTo (RestFn.java:137)
    clojure.core$apply.invokeStatic (core.clj:669)
    clojure.core$require.invokeStatic (core.clj:6038)
    clojure.core$require.doInvoke (core.clj:6038)
    clojure.lang.RestFn.invoke (RestFn.java:421)
    lambdaisland.tools.namespace.reload$track_reload_one.invokeStatic (reload.clj:35)
    lambdaisland.tools.namespace.reload$track_reload_one.invoke (reload.clj:21)
    lambdaisland.tools.namespace.reload$track_reload.invokeStatic (reload.clj:52)
    lambdaisland.tools.namespace.reload$track_reload.invoke (reload.clj:43)
    kaocha.watch$track_reload_BANG_.invokeStatic (watch.clj:61)
    kaocha.watch$track_reload_BANG_.invoke (watch.clj:60)
    kaocha.watch$plugin_pre_load_hook.invokeStatic (watch.clj:242)
    kaocha.watch$plugin_pre_load_hook.invoke (watch.clj:234)
    clojure.lang.AFn.applyToHelper (AFn.java:154)
    clojure.lang.AFn.applyTo (AFn.java:144)
    clojure.core$apply.invokeStatic (core.clj:669)
    clojure.core$apply.invoke (core.clj:662)
    kaocha.plugin$run_hook_STAR_$fn__2172.invoke (plugin.clj:91)
    clojure.lang.PersistentVector.reduce (PersistentVector.java:343)
    clojure.core$reduce.invokeStatic (core.clj:6885)
    clojure.core$reduce.invoke (core.clj:6868)
    kaocha.plugin$run_hook_STAR_.invokeStatic (plugin.clj:89)
    kaocha.plugin$run_hook_STAR_.doInvoke (plugin.clj:88)
    clojure.lang.RestFn.invoke (RestFn.java:445)
    clojure.lang.AFn.applyToHelper (AFn.java:160)
    clojure.lang.RestFn.applyTo (RestFn.java:132)
    clojure.core$apply.invokeStatic (core.clj:673)
    clojure.core$apply.invoke (core.clj:662)
    kaocha.plugin$run_hook.invokeStatic (plugin.clj:100)
    kaocha.plugin$run_hook.doInvoke (plugin.clj:99)
    clojure.lang.RestFn.invoke (RestFn.java:425)
    kaocha.api$test_plan.invokeStatic (api.clj:50)
    kaocha.api$test_plan.invoke (api.clj:49)
    kaocha.api$run$fn__3232.invoke (api.clj:101)
    clojure.lang.AFn.applyToHelper (AFn.java:152)
    clojure.lang.AFn.applyTo (AFn.java:144)
    clojure.core$apply.invokeStatic (core.clj:667)
    clojure.core$with_bindings_STAR_.invokeStatic (core.clj:1990)
    clojure.core$with_bindings_STAR_.doInvoke (core.clj:1990)
    clojure.lang.RestFn.invoke (RestFn.java:425)
    kaocha.api$run.invokeStatic (api.clj:99)
    kaocha.api$run.invoke (api.clj:86)
    kaocha.watch$try_run$fn__4904.invoke (watch.clj:54)
    kaocha.watch$try_run.invokeStatic (watch.clj:53)
    kaocha.watch$try_run.invoke (watch.clj:45)
    kaocha.watch$run_loop.invokeStatic (watch.clj:204)
    kaocha.watch$run_loop.invoke (watch.clj:198)
    kaocha.watch$run_STAR_.invokeStatic (watch.clj:338)
    kaocha.watch$run_STAR_.invoke (watch.clj:294)
    kaocha.watch$run$fn__5016.invoke (watch.clj:348)
    clojure.lang.AFn.applyToHelper (AFn.java:152)
    clojure.lang.AFn.applyTo (AFn.java:144)
    clojure.core$apply.invokeStatic (core.clj:667)
    clojure.core$with_bindings_STAR_.invokeStatic (core.clj:1990)
    clojure.core$with_bindings_STAR_.doInvoke (core.clj:1990)
    clojure.lang.RestFn.invoke (RestFn.java:425)
    clojure.lang.AFn.applyToHelper (AFn.java:156)
    clojure.lang.RestFn.applyTo (RestFn.java:132)
    clojure.core$apply.invokeStatic (core.clj:671)
    clojure.core$bound_fn_STAR_$fn__5818.doInvoke (core.clj:2020)
    clojure.lang.RestFn.invoke (RestFn.java:397)
    kaocha.watch$run$fn__5018.invoke (watch.clj:355)
    clojure.core$binding_conveyor_fn$fn__5823.invoke (core.clj:2047)
    clojure.lang.AFn.call (AFn.java:18)
    java.util.concurrent.FutureTask.run (FutureTask.java:317)
    java.util.concurrent.ThreadPoolExecutor.runWorker (ThreadPoolExecutor.java:1144)
    java.util.concurrent.ThreadPoolExecutor$Worker.run (ThreadPoolExecutor.java:642)
    java.lang.Thread.run (Thread.java:1589)
1 tests, 1 assertions, 1 errors, 0 failures.
```
