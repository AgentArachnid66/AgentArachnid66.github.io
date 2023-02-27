---
layout: post
title:  "Clue System Plugin"
categories: UE5 C++ 
date: 01-01-2023
regenerate: true
---

[Github Repo](https://github.com/SpyderWeb-Studios/ClueSystem)


My Clue System Plugin came into being due to the lack of resources of how to create something like this. I needed this for my game and was aiming for something similar to how The Quarry handles their clues. 

This would mean that my desired end result is a system that could support these features: 
 - The Player is aware they are missing clues and where in the game they could find them
 - The Player is able to pick up the clues and look at them in a UI screen
 - Information about each clue is available and even can be expanded on due to other clues being collected

 <iframe width="560" height="315" src="https://www.youtube.com/embed/JZFsDLoKvSc" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

 From my quick overview video, I think I have achieved all 3 of those objectives in a user friendly way. The way I programmed it was with the end user in mind, and how I wanted even someone with no C++ experience to be able to easily use the system. To do this, I utilised Data Assets as an easy way to add new clues and I used a Subsystem to handle the management of the clues. From their placement in the UI, to signalling that they have been collected, the subsystem was vital in this project. It also means that anyone looking to integrate this plugin will have minimal work to do as the subsystem does not override any native classes in UE5 but simply gets added to the game instance automatically. 

 ```C++
	
	bool CollectClue(UPrimaryDataAsset_Clue* Clue);

	UPROPERTY(BlueprintReadOnly, EditDefaultsOnly)
	TMap<TEnumAsByte<EClueLocation>, FAreaClues> CollectedClues;

 ```

 In the example above, I am showing part of the header file to demonstrate some of the inner workings. I am using a TMap with a Enum Value as the key and a struct which holds another TMap. It's essentially a nested TMap with where the clue is located as the first Key and a pointer to the Clue Data Asset as the second Key. The end Value is a simple Boolean to say it has been collected. 

This is how the Subsystem manages a Clue being Collected.

 ```C++
bool UClueManagerSubsystem::CollectClue(UPrimaryDataAsset_Clue* Clue)
{
	UE_LOG(LogClue, Display, TEXT("Attempting to Collect Clue"));
	
	if(!Clue->IsValidLowLevel()) return false;
	
	UE_LOG(LogClue, Display, TEXT("Collecting Clue: %s"), *Clue->GetClueName());
	FAreaClues AreaClues;
	
	// If the Map has the Enum already, then we need to save a reference to the current value
	if(CollectedClues.Contains(Clue->GetClueLocation()))
	{
	// If the Map has the Enum already, then we need to save a reference to the current value
		AreaClues = CollectedClues[Clue->GetClueLocation()];
		
		UE_LOG(LogClue, Display, TEXT("Clue Location already in Map"));
	}

	// If it already exists then Return False
	if(AreaClues.CollectedClues.Contains(Clue)) return false;

	UE_LOG(LogClue, Display, TEXT("Clue doesn't already exist in Map"), *Clue->GetClueName());
	
	AreaClues.CollectedClues.Add(Clue, true);
	CollectedClues.Add(Clue->GetClueLocation(), AreaClues);
	
	UE_LOG(LogClue, Display, TEXT("Clue Successfully Added"), *Clue->GetClueName());

	OnCollectedClue.Broadcast(Clue);
	
	return true;
}

 ```

 
 The Primary Data Asset is a bit too big to put here but here are 2 variations that you can do with it: 
 ![](https://user-images.githubusercontent.com/50571566/218345121-d8fe86ee-0a47-4778-a2c7-0625f061e276.png)

 ![](https://user-images.githubusercontent.com/50571566/218345138-a62fbc0e-6497-4fb5-a664-c2cc361e1afa.png) 

 The way that you switch between them is by the bUsesMesh boolean that switches the mode of the Data Asset to either send the right Mesh to the Inspector Actor Instance or it 
 renders the image to the inspection screen. This in case that some users want to display a pre rendered image instead of a mesh, and I wanted to provide that flexibility

There are some greyed out areas that the user cannot edit and that is because these are set by the subsystem via the ClueSetupComponent which is how the user controls where the 
clue is associated with and in what order they are displayed in the UI

![](https://user-images.githubusercontent.com/50571566/218345473-370077fd-fe30-445c-8d2b-eada818a4fef.png)


```C++
	UClueManagerSubsystem* manager = UGameplayStatics::GetGameInstance(this)->GetSubsystem<UClueManagerSubsystem>();

	if(manager)
	{
		UMainDebugFunctionLibrary::DebugLogWithObject(this, "Clue Manager Valid", EDebuggingType::DT_Log);
		for(auto clue: ClueConfig)
		{
			clue.UpdateClueIndices(clue.ClueLocation);
			manager->UpdateNumberOfCluesInLocation(clue.ClueLocation, clue.Clues.Num());
		}
	}
```

```C++ 
inline void FClueLocationConfig::UpdateClueIndices(EClueLocation Location)
{
    ClueLocation = Location;
	for(int	i = 0; i < Clues.Num(); i++)
	{
		if(!Clues[i]) continue;
		Clues[i]->SetClueIndex(i);
		Clues[i]->SetClueLocation(ClueLocation);
		
	}
}

```


And lastly, the clues themselves hold onto a ```TSoftObjectPtr``` of the Data Asset which is loaded Asynchronously in Blueprints when the player attempts to pick it up. 
```C++

bool AClueBase::AttemptInteractionWithClue()
{
	UE_LOG(LogBlueprint, Display, TEXT("Clue Interacting"));
	LoadClueAssetData();
	return true;
}

// Called when the game starts or when spawned
void AClueBase::BeginPlay()
{
	Super::BeginPlay();
	OnDataAssetLoaded.AddUniqueDynamic(this, &AClueBase::SendToClueManager);
}

void AClueBase::SendToClueManager(UPrimaryDataAsset_Clue* Clue)
{
	UClueManagerSubsystem* ClueManager = GetGameInstance()->GetSubsystem<UClueManagerSubsystem>();
	if(!ClueManager) return;
	if(ClueManager->CollectClue(Clue)) Destroy();
}


```


There is a lot more that I can do with this plugin, but I want to develop other areas of Project Mirror before revisiting this. A feature that comes to mind immediately is the ability to rotate the 3D Object, which I did have in the previous version of the project but it suffered a lot from Gimbal Lock. 

I hope this plugin provides some value to someone, especially since I think I did a really good job in developing it. 