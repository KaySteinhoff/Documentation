# Basic C# Mod

## Introduction

The following guide will walk you through step-by-step on how to create a basic C# mod. This mod will add a button to the singleplayer title screen called `Message`. When clicked, this button will output `Hello World` to chat.

## Preparation

#### For this tutorial, we will be naming our project `ExampleMod`.

### Setting up your Module (SubModule.xml)

1. Go to your game files and locate the `Modules` directory.
2. Create a new folder and name it `ExampleMod` (Must be the same as the Id you use for Step #4).
3. Create a new folder named `bin` and inside this directory, create a new folder called `Win64_Shipping_Client`.
4. Create a new `SubModule.xml` file (must be named this) inside the folder you created in Step #2 and then paste the following into it:

   ```xml
    <Module>
        <Name value="Example Mod"/>
        <Id value="ExampleMod"/>
        <Version value="v1.0.0"/>
        <SingleplayerModule value="true"/>
        <MultiplayerModule value="false"/>
        <DependedModules>
            <DependedModule Id="Native"/>
            <DependedModule Id="SandBoxCore"/>
            <DependedModule Id="Sandbox"/>
            <DependedModule Id="CustomBattle"/>
            <DependedModule Id="StoryMode" />
        </DependedModules>
        <SubModules>
            <SubModule>
                <Name value="ExampleMod"/>
                <DLLName value="ExampleMod.dll"/>
                <SubModuleClassType value="ExampleMod.MySubModule"/>
                <Tags>
                    <Tag key="DedicatedServerType" value="none" />
                    <Tag key="IsNoRenderModeElement" value="false" />
                </Tags>
            </SubModule>
        </SubModules>
        <Xmls/>
    </Module>
   ```

    **Note**: `MySubModule` is the name of the class we will be using in the [Programming](#programming) section of the tutorial.

5. If you are using different names, change the above values to match that of your Module/SubModule.
6. Start the launcher and make sure your mod appears under `Singleplayer` &gt; `Mods`.

For more information on the Module folder structure, [Click Here](../_intro/folder-structure.md).

### Setting up your Project (Windows)

Before setting up a project, it is important to know that **this is not required for basic mods** (e.g. changing or adding items/characters/scenes).

1. Start Microsoft Visual Studio and select `Create New Project`.
2. Choose `Class Library (.NET Framework)`.
3. Name your project `ExampleMod` (if you choose another name make sure that your namespace and assembly name are correct) `.NET Framework 4.7.2` as the `Framework`.  If this option is not available for you, [Download it here](https://dotnet.microsoft.com/download/dotnet-framework/net472) (Developer Pack).
4. Now that your project is setup, [set your build path](https://docs.microsoft.com/en-us/visualstudio/ide/how-to-change-the-build-output-directory?view=vs-2019) to the `Modules/ExampleMod/bin/Win64_Shipping_Client` directory in your game files.
5. [Reference](https://docs.microsoft.com/en-us/visualstudio/ide/how-to-add-or-remove-references-by-using-the-reference-manager?view=vs-2019) the `TaleWorlds.*` DLLs in the `bin\Win64_Shipping_Client` directory of your game files (not your module directory).
6. [Reference](https://docs.microsoft.com/en-us/visualstudio/ide/how-to-add-or-remove-references-by-using-the-reference-manager?view=vs-2019) the DLLs for each official module in `Modules\ModuleName\bin\Win64_Shipping_Client`.

### Setting up your Project (Linux)

Before setting up a project, it is important to know that **this is not required for basic mods** (e.g. changing or adding items/characters/scenes).
It is assumed you have basic VSCode knowledge(how to install extensions, how to create .NET projects).

**SDK**

Since Mount & Blade 2: Bannerlord is written using the .NET Framework 4.7.2 you'll have to install the .NET SDK(don't worry about how we'll compile for .NET Framework we'll come back to it).
Luckily it is rather easy to install as Microsoft has a comprehensive tutorial [here](https://learn.microsoft.com/en-us/dotnet/core/install/linux?WT.mc_id=dotnet-35129-website) on how to do it both manually and scripted.
You can choose either but the scirpted one is easier to do.

After installing the latest .NET version run the following command in the terminal:

    which dotnet

If a path is printed the SDK was successfully installed. If not check if the directory `/home/<YourUserName>/.dotnet` exists. If it does run the following command to add it to your `PATH` variable.
**Note that this will not be carried over into the next session. Please check how to permanently add PATH variables if you wish to do that.**

    export PATH=$PATH:/home/<YourUserName>/.dotnet/

If you now run the previous `which` command again you should see a path being printed.

**IDE**

As Visual Studio is a Windows-only application we'll use Visual Studio Code. 
It is of the utmost importance that you install VSCode using the command line as it is otherwise possible that VSCode is unable to open a terminal instance, making it impossible for it to find the installed .NET SDK.
Luckily Visual Studio Code has a tutorial [here](https://code.visualstudio.com/docs/setup/linux) which covers multiple distros such as Ubuntu, Fedora, Arch, etc.

Once you've followed your distobution specific installation guide run the following command:

    code --version

A result simiar to this should be printed:

    1.102.3
    488a1f239235055e34e673291fb8d8c810886f81
    x64

This will confirm that Visual Studio Code has been successfully installed.

Once done start Visual Studio Code by typing:

    code

**Final setup**

Once Visual Studio Code has started and you have **not** received an error message of the .NET SDK not being located a few extensions have to be installed:
- C# Dev Kit
- vscode-solution-explorer(Optional but very helpful)

Once all extensions are installed create a new Class Library project.
Edit the *.csproj file(either provided as a plain text file in the Explorer or Right Click->'Open file' using the vscode-solution-explorer) and modify it to look like this:

   ```xml
   <Project Sdk="Microsoft.NET.Sdk">

     <PropertyGroup>
       <!-- We target multiple Frameworks just to make sure we can at least compile for .NET 8.0(might be a different version depending on what you've installed) -->
       <TargetFrameworks>net8.0;net472</TargetFrameworks>
     </PropertyGroup>

     <ItemGroup>
       <!-- This NuGet Package enables us to compile for the .NET Framework 4.7.2 by creating a dependency to the requested version -->
       <PackageReference Include="Microsoft.NETFramework.ReferenceAssemblies" PrivateAssets="All" Version="1.0.0-preview.2"/>

       <!-- You can also reference the TaleWorlds.*.dll files in the actual bin/Win64_Shipping_Client/ directory of the game files but you'll get some unnecessary Warnings because of some C++ *.dlls that way -->
       <Reference Include="bin/Debug/net472/TaleWorlds.*.dll"/>
     </ItemGroup>
   </Project>
   ```

Now you should be set up to make Mount & Blade 2: Bannerlord on Linux.

### Debugging your Project (Optional)

#### Way 1 (Preferred)
1. Open your project properties and go to the `Debug` tab.
2. Select the `Start external program` option and then browse for `Bannerlord.exe` located in the `bin\Win64_Shipping_Client` directory in your game files (not your module directory).
3. Set your working directory to the `bin\Win64_Shipping_Client` directory in your game files (not your module directory).
4. Add the following command line arguments (be sure to replace ExampleMod with the name of your module):
   * `/singleplayer _MODULES_*Native*SandBoxCore*CustomBattle*SandBox*StoryMode*ExampleMod*_MODULES_`

#### Way 2 (If you want to start your debugging from launcher window)
1. Open your project properties and go to the `Debug` tab.
2. Select the `Start external program` option and then browse for `TaleWorlds.MountAndBlade.Launcher.exe` located in the `bin\Win64_Shipping_Client` directory in your game files (not your module directory).
3. Set your working directory to the `bin\Win64_Shipping_Client` directory in your game files (not your module directory).

## Programming

1. Create a new class in your VS Project and name it `MySubModule`, then open it.
2. Add the following using directives to your class:

   ```csharp
    using TaleWorlds.Library;
    using TaleWorlds.Localization;
    using TaleWorlds.MountAndBlade;
   ```

3. Inherit from the `MBSubModuleBase` class.
4. Setup an override for the `OnSubModuleLoad()` inherited method.
5. Add the following code to your override method:

   ```csharp
    Module.CurrentModule.AddInitialStateOption(new InitialStateOption("Message",
        new TextObject("Message", null),
        9990,
        () => { InformationManager.DisplayMessage(new InformationMessage("Hello World!")); },
        () => { return  (false, null); }));
   ```

6. Compile your project and confirm that it was outputted to `Modules\ExampleMod\bin\Win64_Shipping_Client`.
7. Open the Bannerlord launcher and navigate to `Singleplayer` &gt; `Mods` then make sure that your mod is ticked and start the game.
8. On the title screen, you should now see a button called `Message`, click it and you should see `Hello World` displayed in the bottom-left corner of your screen (in chat).
9. You have now successfully created your first Bannerlord mod!

## Module Templates

If you use Visual Studio 2019 or higher, you can also use this [Bannerlord Module Template](https://github.com/BUTR/Bannerlord.Module.Template) to create a basic C# module automatically!
