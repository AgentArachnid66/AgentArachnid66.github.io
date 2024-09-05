+++
title = 'Optimisation'
date = 2024-08-25T14:19:50+01:00
+++

# Unreal Engine Optimisation

Optimisation in Unreal Engine has a few different aspects to it, from optimising the code to optimising the assets. This tutorial will cover some of the ways you can optimise your Unreal Engine project to improve performance.

First thing to keep in mind is that this is going to cover generic optimisation as each project is different and may require different optimisation techniques.

## Unreal Insights

Unreal Insights is a tool that can help you identify performance issues in your project. 
It provides a range of different metrics that can help you identify where performance bottlenecks are occurring. 
You can use this tool to identify areas of your project that need optimisation.

It is the main tool that should be used to optimise, benchmark and profile your project and can even be used with packaged builds. 

### How to use Unreal Insights in the Editor

To open Unreal Insights in the Editor, navigate to the bottom of the window and click on the Insights tab.
![](https://imgur.com/5PuA3fg.png)

From here, you can configure Insights to give you the information that you need. I'd recommend enabling "Open Live Session On Trace Start" so the
window with the data will open automatically when you start a trace.

To start a trace simply press this button here:
![](https://imgur.com/TD1Z4xS.png)

### How to use Unreal Insights in a Packaged Build

To use Unreal Insights in a packaged build, you need to make a shortcut to the packaged build and add the following command line arguments:
-trace=default 

![](https://imgur.com/zAzRumb.png)

This will create a trace file which you can open in the Session Browser.

### Session Browser

To open the Session Browser, navigate to the Insights section and click on the Session Browser button.
![](https://imgur.com/eqBWd1i.png)

You can also open from the file explorer by navigating to the Binaries folder.
![](https://imgur.com/DdFIVI1.png)

From here you can open the trace file that you created and view the data.
![](https://imgur.com/RtopqfR.png)

Now, you can use this data to identify performance bottlenecks in your project and optimise them. After all of this setup, this is the relatively easy part
as Unreal Insights will give you a lot of information on what is causing performance issues.

I will be using one of my projects to show you how to use Unreal Insights to identify performance issues and optimise them.

I played through my Dev Testing map, interacting with a few of my systems to see if I can identify any performance issues.

![](https://imgur.com/aqaZTWc.png)

For the most part, the FPS is stable around 60fps but there are a few spikes. Let's zoom in on one of them:

![](https://imgur.com/IK88yG7.png)

So you can see that the frame took around 24ms, rather than our target of 16ms. This is a good place to start looking for performance issues.
Insights provides us with a stack trace of what is being called on that frame, and how long it took. 

Looking at the information, it looks like the issue is coming from the loading of the Data Asset when attempting to add the Clue to the Clue Manager. 

From that, I looked at the code and found the first suspect:
`const UCluePrimaryDataAsset* LoadedClue = ClueToAdd.LoadSynchronous();`

This will load the Data Asset synchronously, which can cause performance issues as it will not only load the data on the same frame, but will also block the thread until it is loaded.
I changed this to load asynchronously and the performance improved. I used [UE5 Coro](https://github.com/landelare/ue5coro) to easily add in the coroutine to load it in.


## Networking Profiling

When making a multiplayer game, it is important to profile the networking to ensure that it is not causing broadwidth or performance issues. 
Making sure that the networking is optimised can reduce multiplayer bugs such as lag and desync.

### How to profile Networking

To open the Network Profiler, you need to open the console and type in the following command:
- `networkprofile` to toggle the recording
- `networkprofile start` to start the recording
- `networkprofile stop` to stop the recording

To view this data, you need to open up the Network Profiler window which can exclusively be opened in the File Explorer.
![](https://imgur.com/H6GIMCy.png)

After recording the data, you can view it by navigating to the {GameProjectFolder}/Saved/Profiling folder
![](https://imgur.com/IKtPzSO.png)


After opening up the file, you can see the data that was recorded. This can help you identify any networking issues that may be causing performance issues in your project.
![](https://imgur.com/rWxhGpw.png)
![](https://imgur.com/elp9iOD.png)

There is a spike in the outgoing bandwidth, but the outgoing bandwidth is on average 6KB/s, which is within acceptable range for my project. 

As the project grows and more features are added, it is important to keep an eye on the networking to ensure that it is not causing performance issues.


## Optimisation Tips

### General Optimisation Tip

#### TSoftObjectPtr vs Raw/TObjectPtr

Soft Object Ptrs are recommended for data that is not needed immediately, as they are more efficient than Raw/TObjectPtrs. This difference is even more apparent with blueprint
as when you use normal pointers (hard references), when that blueprint is loaded it will also load in all of the hard references as well. This can cause a massive cascading
effect which will result in your blueprint being significantly heavier than it needs to be. Simply converting these hard references to soft object pointers can reduce the
size of your class but can cause issues if the object is not loaded in when you need it. 

Things that can affect the size of the blueprint with hard references:
- Variables
- Casting 
- Selecting the Class in a dropdown (Create Widget, Spawn Actor, etc)

#### Avoiding Tick

Avoid using Tick when possible, as it can cause performance issues. Instead, use Timers or Events to update your Actors.
Alternatively, you could also change the Tick Interval to be less frequent if you need to use Tick, or toggle the tick on and off when needed.

On modern hardware, this advice is less relevant as the performance impact of Tick is less than it used to be, but it is still good practice to avoid using Tick when possible.
This is especially true if you are targeting lower-end hardware, or trying to build scalable systems. 

If you find yourself updating a lot of actors that are doing the same things, I would recommend looking into Mass which is UE's Entity Component System. 

#### Observer Pattern

Unreal allows you to use the Observer Pattern to reduce the number of calls you need to make to update your Actors by creating event delegates. 

This can be useful when you have a lot of Actors that need to be updated when something happens, as it reduces the number of calls you need to make to update them and avoids
the need to use Tick and references.

In C++, it is really easy to define an event:
`DECLARE_DYNAMIC_MULTICAST_DELEGATE_xPARAM()` where x is the number of parameters you want to pass in.

DYNAMIC means that you can bind to it, well dynamically. You can add a listener by getting the delegate and calling AddDynamic or AddUniqueDynamic and 
it will call the function when the event is triggered. Without DYNAMIC, you can still bind to it by calling BindUFunction, BindUObject, etc.

MULTICAST is required for the event to be able to call multiple functions at once, including blueprint. 

The final part of this is the _PARAM which is the parameters that you want to pass in. You can pass in any number of parameters, but you can only pass in up to 8 parameters.

An example of this would be:
```cpp
DECLARE_DYNAMIC_MULTICAST_DELEGATE_OneParam(FOnClueAdded, const UCluePrimaryDataAsset*);

class AClueManager : public AActor
{
  GENERATED_BODY()
  UPROPERTY(BlueprintAssignable, Category = "Clue Manager")
  FOnClueAdded OnClueAdded;
}
```

#### Object Channels

When you have a collision (say for interacting) that is attached or nested within another actor, you can use Object Channels to ignore the collision of any actor that 
it does not need to know about. This can reduce the number of collision checks that are being made and improve performance.

To set up Object Channels, you need to go to the Project Settings and navigate to the Collision section. 
From here, you can add a new Object Channel and set up the collision settings for it.

![](https://imgur.com/stIkgsx.png)

After setting up the Object Channel, you can use it in the Collision settings of your Actors to ignore the collision of other Actors that are not needed.

This is the settings for the interaction collider connected to the player:
![](https://imgur.com/t8URAb2.png)

It will only look for the Interact Object type, so when we want to add in a new Interactable object, we need to ensure that it's collider is set to the Interact Object type.

![](https://imgur.com/duA09Pn.png)

### Networking Optimisation

#### Fast Arrays vs TArrays

The first tip I'd like to give is to use Fast Arrays instead of TArrays when possible. 
Fast Arrays are more efficient than TArrays for networking performance as they only replicate the data that has changed rather than the entire array.

They are a bit more complex to work with than normal TArrays, as you have to mark the elements as dirty after changing them, but they are worth it for the performance benefits.

#### Replication Conditions and Net Update Frequency

Another tip is to use Replication Conditions to only replicate data when it is needed. For example, 
if you have a variable that is only needed on the Owning Actor, then you can use a Replication Condition to only replicate it to the Owning Actor.

Another tip is to use the correct NetUpdateFrequency for your Actor. If you are anticipating the variables on an actor to change frequently, then you should set the NetUpdateFrequency to be higher. Otherwise, it should be lower.

#### Limiting RPCs

RPCs can be expensive, especially if they are being called frequently. You can reduce the number of RPCs being called by utilising Replicated Variables rather than 
calling Client RPCs and by batching RPCs together. 

You can reduce the cost of RPCs by reducing the amount of data being sent over. One thing you can do here is infer information based on other information given. For example, 
you don't need to pass in a boolean to say if an item can stack if you send over an integer with the stack size. We can infer if it can stack based on if this integer
is greater than 1. This may be a small example, saving an entire byte per RPC call, but it can add up especially if this RPC would be called often. 

Another way you could reduce the frequency of RPCs is by throttling them. 
If you have a system that is calling RPCs frequently, you could throttle them so that they are only called every x seconds or when a certain condition is met. A good example
of when this would be necessary is for our interaction system, where we could reasonably expect some players to spam the interaction button. If we called the RPC everytime 
that button was pressed, it would be a lot of RPCs. Instead, we could throttle it so that it is only called every x seconds or when the interaction button is released.

