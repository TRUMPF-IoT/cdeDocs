# Developing and Debugging across Repositories

If you want to contribute to one or more of the C-DEngine repositories, you will often want to quickly test your local changes in other repos (SDK/Samples, Plugins etc.) 
or may even want to make use of new functionality in those repos.

There are many ways to achieve this, but here are a few we have used successfully.

## Consume Local NuGets

This is a "no-code-or-project-changes" approach (well almost), but requires you to manually update NuGet references and/or delete local NuGet package caches.

1. In the nuget.config file (in the root of each consuming repo, or in a directory above) add one ore more local NuGet Feeds pointing to the Bin directories of the repos from which you want to consume updates:
Example (assuming your clones are under c:\sources):

```XML
    <add key="cdeLocal" value="C:\Sources\C-DEngine\bin\Debug" />
    <add key="cdeNmiLocal" value="c:\sources\cdeNMI\bin\Debug" />
    <add key="cdeSDKLocal" value="c:\sources\cdeSDK\bin\Debug" />
    <add key="nuget.org" value="https://api.nuget.org/v3/index.json" protocolVersion="3" />
```

> You may need to close and restart Visual Studio and/or clean the solution for these changes to be picked up.

2. If you build pre-release NuGets (the default, and strongly recommended for most developers), you will want to update all package references in the consuming solution/projects from `"5.*"` to `"5.105.1-*"` (adjust to the base version number you are building):

    > This is the "almost-no-project-changes" part: NuGet does not (yet) allow [wild-card references that allow pre-release versions](https://github.com/NuGet/Home/issues/912), so until then (.Net Core SDK 3.1.300 / VS 16.6) you will have to opt-in to pre-release versions manually. Once this feature is available (broadly enough), we plan to can use `"5.*-*"` across our repos to obviate the need for editing the .csproj files.

    a. Update .csproj files directly:

    ```XML
        <PackageReference Include="C-DEngine" Version="5.105.1-*" />
    ```

    b. You can also use Visual Studio to "Manage Packages for Solution" to update the package references to your specific version:

    - Go to "Updates"
    - Choose Packages Source "All"
    - Check "Include prerelease"
    - Select all the cde/C-DEngine packages that you care about
    - Click the Update button: in the confirmation dialog you should see the release versions for each affected project going to your pre-release version.

    > These package references will be to a fixed version, so if you make a commit in the original repo, the version will change and you have to update versions in VS again.

    > Tip: You can do the bulk update in VS first. You will see the changed files in Git/VS's Changes tab, and you can easily find all the diffs and replace them with the `"5.105.1-*"` version (or equivalent) as per step a. above.

3. Whenever you update the NuGet package, you either have to change it's version number (doing a local Git commit will do that, thanks to GitVersion) or delete the package from the local nuget cache (`c:\users\<yourname>\.nuget\packages`, or simply flush all nuget caches).

## Adding Projects to the Consuming Solution

For a more immediate developer/debugging experience (just hit "F5"), you can add the .csproj of the original component that you are working on to the consuming solution.

Example: (again, assuming clones are under c:\sources)

You have made changes to the C-DEngine and want to debug/test it with one or more plug-ins in the cdePlugins repro.

1. Open the `c:\sources\cdePlugins\src\cdePlugins.sln` solution in Visual Studio.
2. Choose "Add existing Project" and add `c:\sources\C-DEngine\src\C-DEngine\C-DEngine.csproj` to the cdePlugins solution.

This will result in the Package Reference to C-DEngine to be dynamically replaced with a Project Reference (due to logic in the Directory.Build.targets file, see [BuildReadme.md](https://github.com/TRUMPF-IoT/C-DEngine/blob/master/BuildTools/BuildReadme.md) for details).

> Note: By default this only works for C-DEngine and CDMyNMIHtml5RT package references, but you can change the PackageReferenceFilter in Directory.Build.targets or add your own PackageReferenceFilter definition to your .csproj files.

> Note: Specifically the C-DEngine's .csproj has logic to build without the cdeUpdater and cdeNewtonsoft.JSON projects (again, see BuildReadme.md for details), so you will want to also add those two .csproj's to your consuming solution if you want full fidelity.

### Variation 1: Add consuming .csproj to the Solution with the changed component

In the example, you would add the plug-in project you are working on to the C-DEngine.sln solution. If you only care about one of the plug-ins, this can be less work.

### Variation 2: Create your own custom solution across all repos

In the example, you could create `c:\sources\MyCustomSolution.sln` and add all the projects that you are working on to that solution.

> Note: for some of the build magic to work correctly, you may need to place the Directory.Build.props and Directory.Build.targets files from the C-DEngine repo into the solution directory or any directory above it. This is especially required if the solution directory is a peer to the cde/C-DEngine repos.

Regardless of which of the above approaches you take, make sure you don't accidentally commit any of these project or solution changes with a pull request.
