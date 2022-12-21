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

## Test case

1. start kaocha with circular dependencies.
- a compilation error should occur
2. comment out `(:require circular2)` in `src/circular1.clj`.
- the tests should run
3. undo step 2
- a compilation error should occur
4. comment out `(:require circular1)` in `src/circular2.clj`.
- the tests should run
