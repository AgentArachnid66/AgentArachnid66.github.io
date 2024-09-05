+++
title = 'Custom Target.cs files and why you should use them'
date = 2024-09-05T14:19:39+01:00
+++

# Background

For my current [project](/_ProfessionalProjects/2024-02-17-CatAndMouse.md), I've implemented EOS and EOSPlus so I can support crossplay and have an easy multiplayer solution. The main problem I've been facing recently though was the ability to target both EOS and Steam independently from each other, and without altering the Config/DefaultEngine.ini directly each time I wanted to make a build for each storefront.

As they use the same Platform (Windows, Linux, Mac, etc), I couldn't easily rely on Platform Configs. The solution came in the form of the [CustomConfig](https://github.com/EpicGames/UnrealEngine/blob/release/Engine/Source/Programs/UnrealBuildTool/Configuration/TargetRules.cs#L817) parameter for the UBT, which can be setup to appended/overwrite certain values in the Config/DefaultEngine.ini file. This opened up a rabbit hole that took me a few days of part time searching to get through, a lot of looking through how [Lyra](https://github.com/EpicGames/UnrealEngine/blob/release/Samples/Games/Lyra) was setup to enable this behaviour. 


# How it works

## Target.cs

**You need to have a [Source Build](https://github.com/EpicGames/UnrealEngine) for this to work.** Follow the steps to gain access to the Github Repo for the Engine Source code [here](https://dev.epicgames.com/documentation/en-us/unreal-engine/building-unreal-engine-from-source?application_version=5.4)

In order to use the CustomConfig, you will need to add in a new Target.cs file to your projects source folder. This will be the name of the new Exe as well and will be used as the unique identifier in the engine. For example, if your project was called `ProjectTest` and you added in a new Target.cs called `ProjectTest_EOS.Target.cs` then in the engine you will get a new option in play settings & packaging settings for `ProjectTest_EOS`. If you package this, it will be called `ProjectTest_EOS.exe`. 

Your project structure should look something like:
- ProjectTest
    - Source
        - ProjectTest.Target.cs
        - ProjectTest_EOS.Target.cs

It is recommended that the Target.cs file for this inherits from the main Game Target.cs, so you can alter the settings for each Platforms and remove repeated sections. So back to the example, your new Target.cs should look something 

``` C#
public class ProjectTest_EOSTarget : ProjectTestTarget
{
	public ProjectTest_EOSTarget(TargetInfo Target) : base(Target)
	{
	}
}
```

Repeat this step for Steam, if you want to target it as well. 


## Custom Default Engine.ini

At this point, we are nearly there. We just now need the settings to use for this Target. I would highly recommend that you have a pretested values, so before this step place your values for the custom config into the Project/Config/DefaultEngine.ini and test that they work as intended. 

Okay, once you have verified that the values that you have work then you can add a new folder in your Config folder called Custom. In that folder, add in new folders and call them EOS and Steam, and in both of those folders add in a file called DefaultEngine.ini. Populate it with the values you used before. I'd also recommend changing some values in the DefaultGame.ini so you can easily visually see if these changes have taken effect. 

- ProjectTest
    - Config    
        - Custom
            - Steam
                - DefaultEngine.ini
                - DefaultGame.ini
            - EOS
                - DefaultEngine.ini
                - DefaultGame.ini


## Final Setup 

Return to the Target.cs files and add in a new line
``` C#
public class ProjectTest_EOSTarget : ProjectTestTarget
{
	public ProjectTest_EOSTarget(TargetInfo Target) : base(Target)
	{
        // Replace with the name of the folder you made 
        // in the Custom Folder for this particular target
        CustomConfig = "EOS"
	}
}
```

At this point, you should be ready to compile the editor and see the changes. In order to actually use these, you will need to package the project using these targets but at this point it should be good to go. 


I hope you found this tutorial helpful, this took me ages to research and debug and I'm just glad that I can hopefully make this journey a lot less painful for you. 
