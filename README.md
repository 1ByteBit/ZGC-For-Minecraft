# ZGC For Minecraft
## Preface
Be warned that it's commonly taken that ZGC must be finely tuned to the machine you're using. While this can be true for java versions below 17, any such settings are now dynamic, and therefore shouldn't need to be configured.
## What is ZGC?
ZGC stands for Z Garbage Collector, a garbage collector, put simply, is a thing that frees up memory for further use within your server. There are a few GCs as well as a number of flags (options) you can use to change how it runs. A common set of GC flags is Aikars flags, but they have their limits since as good as they may be, the GC still has to pause the server to run. ZGC fixes this however, by running mostly in alongside the server, instead of pausing it each time it has to run.
## Who should use ZGC?
ZGC requires the allocation of a large amount of memory, due to this ZGC is best suited for servers with *at least* 8gb of memory allocated, which is fine as anything under that can use G1GC without issues anyhow. Be warned however that ZGC may cause the usage of more memory, and extensive testing hasn't been done yet, so more ram may have to be allocated.
So in other words, only bigger servers should be using ZGC.
## Suggested ZGC flags:
``java -Xms8G -Xmx8G -XX:+UnlockExperimentalVMOptions -XX:+UseZGC -XX:+DisableExplicitGC -XX:+AlwaysPreTouch -XX:+PerfDisableSharedMem -XX:-ZUncommit -XX:+ParallelRefProcEnabled -jar server.jar --nogui``

Please keep in mind that using other flags will either:
1. Increase general CPU usage and provide minimal GC performance gains
2. Lower general CPU usage but have the possibility of the GC being overwhelmed.

Overall, it's best to not change these flags unless you know exactly what you're doing and have a specific reason for changing them. The only known exception to this would be the -XX:+UseLargePages flag, which was not included due it needing some setup beforehand.

### Important notes:
- Change -Xmx/-Xms to however much ram (In GBs) you wish to give the server.
- Change server.jar to the name of the jar file you're using.
- A "+" means that flag is being specifically enabled, while a "-" means that flag is being specifically disabled.

### Reasoning for each flag:

| Flag | Reason
|--------------|-------------|
| -Xms and -Xmx matching | It prevents any performance issues from occuring due to requesting additional memory from the OS and lessens the amount of work the GC has to do before getting additional memory.
| -XX:+UnlockExperimentalVMOptions | Is required for many of the other flags.
| -XX:+UseZGC | Enables ZGC
| -XX:+DisableExplicitGC | Prevents plugins and other code from messing with the GC.
| -XX:+AlwaysPreTouch | Sets up and reserves the memory upon startup. Improves efficiency and access speed.
| -XX:+PerfDisableSharedMem | Prevents the GC from writing to file system, reducing the amount of lag due to I/O. See [here](https://www.evanjones.ca/jvm-mmap-pause.html)
| -XX:-ZUncommit | Prevents ZGC from uncommiting memory and returning it to the OS.
| -XX:+ParallelRefProcEnabled | In Aikars words: "Optimizes the GC process to use multiple threads for weak reference checking. Not sure why this isn't default..."

## Other usable flags for ZGC:

See [here](https://www.oracle.com/java/technologies/javase/vmoptions-jsp.html) for most generic flags.

| Flag | Reason
| --------------|-------------|
| -XX:+UseLargePages | Literally just improves performance with no downsides. Requires some setup however
| -XX:+UseTransparentHugePages | In Aikars words: "Controversial feature but may be usable if you can not configure your host for real HugeTLBFS. We have not measured how THP works for MC or its impact with AlwaysPreTouch, so this section is for the advanced users who want to experiment."
| -XX:ConcGCThreads | The amount of threads for the GC to use. Dynamically set by default
| -XX:ZAllocationSpikeTolerance | ZGC's allocation spike tolerance, the default value is 2. The bigger the value, the higher allocation rate.
| -XX:ZCollectionInterval | This is the minimum time interval for ZGC to occur in seconds. Default is 0
| -XX:ZFragmentationLimit | If the current region fragmentation is greater than this value, the region will be reclaimed (default is 25).
| -XX:ZMarkStackSpaceLimit | Specifies the maximum number of bytes allocated for the mark stack. Default is 8589934592 (8096M).
| -XX:ZProactive | Whether to enable active recycling. Set to true by default
| -XX:UseNUMA | Enabled NUMA support. Automatically set according to the machine by default. Use this flag to force it to be enabled/disabled. 
| -XX:ZUncommitDelay | Sets the amount of time (in seconds) that heap memory must have been idle before being uncommitted (ZUncommit must be enabled). Default is 300

## Main Sources:
[Purpur Discord](https://discord.gg/purpurmc-685683385313919172)

[Aikars Flags](https://docs.papermc.io/paper/aikars-flags)

[Oracle Docs](https://docs.oracle.com/en/java/javase/17/gctuning/index.html)/[Oracle](https://www.oracle.com/java/technologies/javase/vmoptions-jsp.html)

[OpenJDK Wiki](https://wiki.openjdk.org/display/zgc/Main)

[Dev.java](https://dev.java/learn/jvm/tool/garbage-collection/zgc-overview/)
