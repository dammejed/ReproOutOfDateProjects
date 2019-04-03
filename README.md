# ReproOutOfDateProjects
This is a repro for a project being considered "out of date" constantly in the Common project system.
This seems to have started only in VS2019 GA.
When run on any of the 2019 RC releases(1, 2, 3, 4), or in VS2017 (15.9.10), the project is not identified as out-of-date.
I haven't verified if this isolated repro works correct on 2019 RC, because it would mean downgrading. If asked, I could do that.
What's sure is that a more complicated solution configuration was correctly being identified as up-to-date.

To reproduce the problem:
1. Build the solution a few times (F6) without changing anything.

2. Wait for several seconds.

3. Modify the `Tests` class in some meaningless way (even whitespace).

4. Build again.

5. Any further builds now seem to consider the project out of date.


When I enable the Verbose logging on the FastUpToDate, it seems that the wrong config file's timestamp is being checked against the output.

```
-- snip --
1>FastUpToDate: Checking copied output (UpToDateCheckBuilt with Original property) file 'C:\Users\Jeff\source\repos\ReproOutOfDateProjects\ReproOutOfDateProjects\obj\Debug\ReproOutOfDateProjects.dll.withSupportedRuntime.config': (ReproOutOfDateProjects)
1>FastUpToDate:     Source 4/2/2019 9:03:54 PM: 'C:\Users\Jeff\source\repos\ReproOutOfDateProjects\ReproOutOfDateProjects\obj\Debug\ReproOutOfDateProjects.dll.withSupportedRuntime.config'. (ReproOutOfDateProjects)
1>FastUpToDate:     Destination 4/2/2019 8:56:25 PM: 'C:\Users\Jeff\source\repos\ReproOutOfDateProjects\ReproOutOfDateProjects\bin\Debug\ReproOutOfDateProjects.dll.config'. (ReproOutOfDateProjects)
1>FastUpToDate: Source is newer than build output destination, not up to date. (ReproOutOfDateProjects)
1>FastUpToDate: Up to date check completed in 4.8 ms (ReproOutOfDateProjects)
1>------ Build started: Project: ReproOutOfDateProjects, Configuration: Debug Any CPU ------
-- snip --
```

The intermediate config file, `ReproOutOfDateProjects.dll.withSupportedRuntime.config` is the one being checked against the output file, `ReproOutOfDateProjects.dll.config`.
I'd expect that, instead, the config file with the generated binding redirects, `ReproOutOfDateProjects.dll.config` in the obj directory is checked against the output directory.
