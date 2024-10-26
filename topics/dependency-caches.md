[//]: # (title: Dependency Caches)


**Maven Cache**, **Gradle Cache**, and **NuGet Cache** are configuration-free build features that speed up builds by allowing related steps to reuse dependencies downloaded during previous builds of the same build configuration.

* Maven Cache — caches dependencies used by [](maven.md) build steps
* Gradle Cache — caches dependencies used by [](gradle.md) build steps
* NuGet Cache — caches NuGet packages used by [](net.md) build steps



## Common Principles

All "... Cache" build features operate in a similar manner. Caches undergo identical creation/publishing/validation/reuse phases regardless of the build feature type.


<deflist type="narrow">
    <def title="Caching">
        The initial cache is formed during the first build run. To do this, a build feature caches the contents of track directories as is, without filtering out any files.<br/>
        Tracked repositories:
        <list type="bullet">
            <li><b>Maven:</b> <a href="https://maven.apache.org/guides/introduction/introduction-to-repositories.html">local Maven repositories</a>. The default path is <code>${user.home}/.m2/repository/</code>.</li>
            <li><b>NuGet:</b> <a href="https://learn.microsoft.com/en-us/nuget/consume-packages/managing-the-global-packages-and-cache-folders">global package folder</a>. The default path is <code>${user.home}/.nuget/packages</code>.</li>
            <li><b>Gradle:</b> <a href="https://docs.gradle.org/current/userguide/dependency_resolution.html#sub:cache_copy">Gradle cache directory</a>. The default path is <code>${user.home}/.gradle/caches</code>.</li>
        </list>
    </def>
    <def title="Publishing">
        After a cache was created, the build feature publishes it to the <a href="configuring-artifacts-storage.md">artifact storage</a>, making it easily accessible to agents that process subsequent builds.
    </def>
    <def title="Validation">
        A cache is routinely checked to determine if it is still valid or needs replacement. A build feature can mark a cache as invalid at the start or end of a build (before or after executing build steps) in the following cases:
        <ul>
            <li>At the start:
                <ul>
                    <li>the cache was not validated for 7 days or longer</li>
                    <li>the cache reached its 30-day lifetime, even if it was recently validated</li>
                    <li>the <code>teamcity.depcache.invalidate</code> <a href="configuring-build-parameters.md">parameter</a> equals <b>true</b></li>
                    <li>the build feature detected a local dependency repository that was missing from the current cache version</li>
                </ul>
            </li>
            <li>At the end:
                <ul>
                    <li>the list of build steps using this cache has changed</li>
                    <li>the project dependency list has changed</li>
                </ul>
            </li>
        </ul>
        When a build feature encounters an invalid cache, it flushes this cache and republishes a newer version after the build ends.
    </def>
    <def title="Usage">
        A build feature unzips cached files from a validated cache to tracked repositories, merging existing file structure with the cached one. If a cache was marked as invalid before the actual build steps started, this build runs without using a cache and re-downloads all required dependencies anew. These fresh dependencies will be used for an updated cache published at the end of this build.
    </def>
</deflist>


## Special Notes and Limitations

* Currently, caching is employed only for builds running on cloud agents with the "After the first build" terminate condition. Such agents are called ephemeral and can be identified by the [`teamcity.agent.ephemeral=true`](predefined-build-parameters.md) parameter.

* To enable dependency caching for [builds running in container](container-wrapper.md), set the `-v <path_to_your_cache>:<path_to_your_cache>` volume.

* Maven Cache: the build feature supports all three local Maven repo types: per-agent, per-build, and Maven default.

* NuGet Cache: the caching is not supported for environments with package locations set via MSBuild-specific ways:

    * via MSBuild command line
    * in a project file
    * in a `Directory.Build.Props` file

* NuGet Cache: the build feature calls the `dotnet list package --format=json --output-version=1 --include-transitive` command to analyze project dependencies and detect invalid caches. The `--format` parameter is available in .NET SDK 7.0.200 and higher, meaning the build feature cannot operate on agents with older SDK versions.