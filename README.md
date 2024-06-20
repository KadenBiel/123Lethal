# 123 Slaughter Me Street Mod

This repo conatins the full source code for the 123Lethal mod. The template used to create this mod is [LC-ExampleEmeny](https://github.com/Hamunii/LC-ExampleEnemy) by [Hamunii](https://github.com/Hamunii).

## Setting Up The Project For Development

### Dependencies

You need to install the following dependencies for this mod to work in the game (these are not installed by the setup script):

- [LethalLib](https://thunderstore.io/c/lethal-company/p/Evaisa/LethalLib/) for registering and adding our enemy.
    - LethalLib depends on [HookGenPatcher](https://thunderstore.io/c/lethal-company/p/Evaisa/HookGenPatcher/).

### Github Fork

You will need git installed on your pc
    1. Create your own [fork](https://github.com/KadenBiel/123Lethal/Fork) on Github, this will make things 1000x easier when dealing with multiple developers.
    2. Clone your fork ('git clone <insert your link here>')

### Setup Script

After copying this repo for yourself, run [SETUP-PROJECT.py](/SETUP-PROJECT.py) from the command-line like this: `python SETUP-PROJECT.py` (Or you can run it from your file manager on Linux). Note that you will have to have Python installed.  
- First, the setup project will copy DLL files over to `UnityProject/Assets/Plugins` directory so we can build our Asset Bundles without any errors.
    - Make sure have [HookGenPatcher](https://thunderstore.io/c/lethal-company/p/Evaisa/HookGenPatcher/) installed and have run the game at least once! This is needed for MMHOOK dll files.
- Second, it will run `dotnet tool restore` in the `Plugin` folder to locally install Unity Netcode Patcher & Thunderstore CLI.
- Third, it will ask you to paste a path to where it will copy over your mod files when you build your project. This is done by generating a `csproj.user` file with the path you inputted. After this, the setup process is done.
- If the script closes instantly after opening it, it means it crashed. This is actually the reason why I told to run it from the command-line. If you use Windows and can make this work, please open a pull request to fix this, thanks!
    - If the script still crashes after running from command-line, try making sure you are running Python 3.

Example `csproj.user` template which is also generated by the setup script:
```xml
<?xml version="1.0" encoding="utf-8"?>
<Project ToolsVersion="Current" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <PropertyGroup>
        <GameDirectory>%programfiles(x86)%/Steam/steamapps/Common/Lethal Company/</GameDirectory>
        <!-- Paste a path to where your mod files get copied to when building.  Include the last slash '/' -->
        <PluginsDirectory>/my/path/to/BepInEx/plugins/</PluginsDirectory>
    </PropertyGroup>

    <!-- Game Directories - Do Not Modify -->
    <PropertyGroup>
        <ManagedDirectory>$(GameDirectory)Lethal Company_Data/Managed/</ManagedDirectory>
    </PropertyGroup>

    <!-- Our mod files get copied over after NetcodePatcher has processed our DLL -->
    <Target Name="CopyToTestProfile" DependsOnTargets="NetcodePatch" AfterTargets="PostBuildEvent">
        <MakeDir
            Directories="$(PluginsDirectory)$(AssemblyName)-DEV/"
            Condition="!Exists('$(PluginsDirectory)$(AssemblyName)-DEV/')"
        />
        <Copy SourceFiles="$(TargetPath)" DestinationFolder="$(PluginsDirectory)$(AssemblyName)-DEV/"/>
        <!-- We will copy the asset bundle named "modassets" over -->
        <Copy SourceFiles="../UnityProject/AssetBundles/StandaloneWindows/modassets" DestinationFolder="$(PluginsDirectory)$(AssemblyName)-DEV/"/>
        <Exec Command="echo '[csproj.user] Mod files copied to $(PluginsDirectory)$(AssemblyName)-DEV/'" />
    </Target>
</Project>
```
You might also want to add this to the `csproj.user` file to also copy it over to our Unity project:
```xml
<!-- Copy the dll to our Unity project -->
<Copy SourceFiles="$(TargetPath)" DestinationFolder="../UnityProject/Assets/Plugins/"/>    
```

That should be all the required setup for this project, and now you can move to coding AI. Good luck!