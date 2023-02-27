---
layout: post
title:  "How To Make A Saving & Loading Subsystem in UE5 using C++"
thumbnail: "/assets/images/SaveLoading/Thumbnail.png"
categories: UE5 C++ Subsystem Saving Loading
date: 23-02-2023
regenerate: true
---


# How To Make A Saving & Loading Subsystem in UE5 using C++

<div class="post-categories">
  {% if post %}
    {% assign categories = post.categories %}
  {% else %}
    {% assign categories = page.categories %}
  {% endif %}
  {% for category in categories %}
  <a href="{{site.baseurl}}/categories/#{{category|slugize}}">{{category}}</a>
  {% unless forloop.last %}&nbsp;{% endunless %}
  {% endfor %}
</div>


First of all, we need to add a Game Instance Subsystem into the project

![](/assets/images/NewC%2B%2BClass.png)

![](/assets/images/ChooseGameInstanceSubsystem.png)


When the Subsystem code has loaded up in your IDE of choice, we are going to add in a few functions to start off with in the header file.

```c++

public:
    // Member function of the Subsystem Class
	virtual void Initialize(FSubsystemCollectionBase& Collection) override;

    // Wipes clean the Player Save Object and Refreshes it with a new one 
	UFUNCTION(BlueprintCallable)
	void StartNewSave();

    // Starts the Async Save process
	UFUNCTION(BlueprintCallable)
	void SaveData();

    // Starts the Async Load process
	UFUNCTION(BlueprintCallable)
	void LoadData();
```


So we have our initialisation method that we are going to overwrite slightly to load the data in. 

Also, we can completely wipe the save game to get a clean game, or we can save data to the save game. 

You might notice that we haven't got a reference to a Save Game object, that is because this system is designed to work with any kind of data that you may need. Every project is different so this provides a base class for a saving system that handles the heavy lifting that every project needs and then you can inherit from this class and update it to fit your needs. I'll show an example of how to do this later on. 

For now, let's add in our variables:

```C++
public:
	TSubclassOf<USaveGame> SaveGameClass;

	UPROPERTY(BlueprintReadOnly)
	TObjectPtr<USaveGame> PlayerSaveObject;

	FString PlayerSaveSlot = "PlayerSlot";
```

Also, we need to forward declare the ```USaveGame``` class to be able to use it as a variable. At the top of your file, below the #includes 

```C++
#include "SaveSubsystem.generated.h"

class USaveGame;

```

The TSubclassOf<T> allows us to select a base class for the save game class. There wouldn't be much point in making it editable in defaults or anything as it isn't advised to make Blueprint children of Subsystems. Lots of issues reported with doing this so we will assign this value in the constructor of our child class. 

We need the UPROPERTY on the PlayerSaveObject as it prevents the object from being garbage collected. A TSharedPtr would do a similar job, but it wouldn't allow us to view the object in Blueprint. 

Now let's define the functions in the C++:


```C++
#include "Kismet/GameplayStatics.h"

void USaveSubsystem::Initialize(FSubsystemCollectionBase& Collection)
{
	Super::Initialize(Collection);
	
	LoadData();
}

void USaveSubsystem::SaveData()
{
	// If it isn't Valid, Create a new instance
	if(!PlayerSaveObject)
	{
		PlayerSaveObject = UGameplayStatics::CreateSaveGameObject(SaveGameClass);
	}
	UGameplayStatics::AsyncSaveGameToSlot(PlayerSaveObject, PlayerSaveSlot, 0, asyncSaveDelegate);
}


void USaveSubsystem::LoadData()
{
	// If a save game exists in a slot, then load it
	if(UGameplayStatics::DoesSaveGameExist(PlayerSaveSlot, 0))
	{		
		UGameplayStatics::AsyncLoadGameFromSlot(PlayerSaveSlot, 0, asyncLoadDelegate);
	}

	// Otherwise, create one
	else
	{
		PlayerSaveObject = UGameplayStatics::CreateSaveGameObject(SaveGameClass);
	}
}

```

I prefer Async over Sync as they do not block the main game thread, however they can be some issues with using them. You need to be sure that you are not reading and writing from the same slot at the same time.

One cool thing you can do with them is add a callback function, similar to how you add functions to a timer like in my [Flashlight Component Tutorial](/_Tutorials/2023-02-16-FlashlightComponent.md).

Let's declare the functions and the event delegate first in the header file:

```C++
#pragma region Event Listeners

public:
    DECLARE_DYNAMIC_MULTICAST_DELEGATE_OneParam(FOnPlayerDataLoaded, USaveGame*, PlayerSaveObject);
    UPROPERTY(BlueprintAssignable)
	FOnPlayerDataLoaded OnPlayerDataLoaded;

    // Listens for when the Async Load has completed
	UFUNCTION()
	virtual void OnAsyncLoadFinished(const FString& SlotName, const int32 UserIndex, USaveGame* SaveGame);
	
    // Listens for when the Async Save has completed	
	UFUNCTION()
	virtual void OnAsyncSaveFinished(const FString& SlotName, const int32 UserIndex, bool bSuccess);
#pragma endregion
```

Moving onto the Implementation in the C++ file:
```C++

void USaveSubsystem::OnAsyncLoadFinished(const FString& SlotName, const int32 UserIndex, USaveGame* SaveGame)
{
	
	if(SaveGame)
	{
		OnPlayerDataLoaded.Broadcast(SaveGame);
	}
	
}

void USaveSubsystem::OnAsyncSaveFinished(const FString& SlotName, const int32 UserIndex, bool bSuccess)
{
	if(bSuccess)
	{
		// Save was Successful
	}
}

```

I've kept it open here and you may notice I made the functions virtual as well. This will allow us to overwrite them in the child class.

Now we need to update the Save and Load functions:

```C++

void USaveSubsystem::SaveData()
{
	
	// If it isn't Valid, Create a new instance
	if(!PlayerSaveObject)
	{
		PlayerSaveObject = UGameplayStatics::CreateSaveGameObject(SaveGameClass);
	}
	FAsyncSaveGameToSlotDelegate asyncSaveDelegate;
	asyncSaveDelegate.BindUObject(this, &USaveSubsystem::OnAsyncSaveFinished);
	UGameplayStatics::AsyncSaveGameToSlot(PlayerSaveObject, PlayerSaveSlot, 0, asyncSaveDelegate);
}

void USaveSubsystem::LoadData()
{
	// If a save game exists in a slot, then load it
	if(UGameplayStatics::DoesSaveGameExist(PlayerSaveSlot, 0))
	{
		FAsyncLoadGameFromSlotDelegate asyncLoadDelegate;
		asyncLoadDelegate.BindUObject(this, &USaveSubsystem::OnAsyncLoadFinished);
		UGameplayStatics::AsyncLoadGameFromSlot(PlayerSaveSlot, 0, asyncLoadDelegate);
	}

	// Otherwise, create one
	else
	{		
		OnAsyncLoadFinished(PlayerSaveSlot, 0, UGameplayStatics::CreateSaveGameObject(SaveGameClass));
	}
}

```

To prevent this base class being generated automatically by Unreal, we need to add the ```Abstract``` UCLASS Specifier.
``` C++
UCLASS(Abstract)
class SAVESYSTEM_API USaveSubsystem : public UGameInstanceSubsystem
```

Making a class abstract basically means that the objects cannot exist as it is now, it needs to be inherited from in order to use it.

Now this should be the subsystem complete, now we need to make a child class. 

![](/assets/images/NewC%2B%2BClass.png)

![](//assets/images/SaveLoading/ChildClass.png)



We also need to make a C++ Save Game Object:

![](/assets/images/SaveLoading/SaveGame.png)

I named mine ```PlayerSaveObject``` and in the C++ I have one variable 

```

public:

	UPROPERTY(BlueprintReadOnly, SaveGame)
	int PlayerLevel = 0;

```



Opening the child class in the IDE, you need to add in 2 functions

```C++

public: 
    UChild_SaveSubsystem();

public:
	UFUNCTION(BlueprintCallable)
	void UpdatePlayerLevel(int Level)

```

In the CPP file:

```C++
UChild_SaveSubsystem::UChild_SaveSubsystem()
{
	SetSaveGameClass(UPlayerSaveGame::StaticClass());
}

UChild_SaveSubsystem::UpdatePlayerLevel(int Level){
   
    if(UPlayerSaveGame* save = Cast<UPlayerSaveGame>(PlayerSaveObject))
		{
			save->PlayerLevel = Level;
			PlayerSaveObject = save;

            SaveData();
		}
}
```

This will update the player level and then save it. You can control when it saves and loads using the blueprint callable functions and you can access the player save object as it is a public property. 

The beauty of subsystems is that they are very easy to get a reference to

``` C++

UGameplayStatics::GetGameInstance(this)->GetSubsystem<UChild_SaveSubsystem>()

```

In Blueprints, you just search for it. 

Of course this will depend on which type of subsystem you select, but it is usually that easy.


Further Information 
 - [Unreal Documentation - Subsystems](https://docs.unrealengine.com/5.1/en-US/API/Runtime/Engine/Subsystems/)
 - [Unreal Community Wiki - Programming Subsystems](https://unrealcommunity.wiki/programming-subsystems-29v70qij)
 - [Unreal Documentation - Async Load From Slot](https://docs.unrealengine.com/5.1/en-US/API/Runtime/Engine/Kismet/UGameplayStatics/AsyncLoadGameFromSlot/)
 - [Unreal Documentation - Async Save From Slot](https://docs.unrealengine.com/5.1/en-US/API/Runtime/Engine/Kismet/UGameplayStatics/AsyncSaveGameToSlot/)
 - [Unreal Forums - Async vs Sync](https://forums.unrealengine.com/t/sync-or-async-save-game-to-slot/477981/6)
 - [Unreal Documentation - Class Specifiers](https://docs.unrealengine.com/4.27/en-US/ProgrammingAndScripting/GameplayArchitecture/Classes/Specifiers/)