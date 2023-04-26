---
layout: post
title:  "Clue System Plugin Version 2"
categories: UE5 C++ 
date: 20-04-2023
regenerate: true
---

[Github Repo](https://github.com/SpyderWeb-Studios/ClueSystem)

This is an update to the original Clue System Plugin, I have since made some significant changes to the codebase. 

First of all, I have finished 2 major refactoring, one to the way that the clues were structured and the other to the how the clues are created.

## Clue Structure

Replacing the old TArray based system, the new system uses a tree based system to allow for branching and nested categories. Replacing the Enum, there is now a PrimaryDataAsset_ClueConfig that holds the following data: 

``` c++

    // The Name of the Location that this Clue Config is associated with
	UPROPERTY(EditDefaultsOnly, Category="ClueSystem")
	FString ClueLocation; 

    // The Widget Class that will handle the display of the information on this branch
	UPROPERTY(EditDefaultsOnly, Category="ClueSystem")
	TSubclassOf<UClueBranchManager> ClueBranchManagerClass;
	
    // The Index of the Clue Config on the main tree 
	UPROPERTY(VisibleAnywhere, Transient, Category="ClueSystem")
	int Index;

    // Child Branches
	UPROPERTY(EditDefaultsOnly, Category="ClueSystem")
	TArray<UPrimaryDataAsset_ClueConfig*> Branches;
	
    // "Leaf" Clue Nodes that are associated with this Clue Config
	UPROPERTY(EditDefaultsOnly, Category="ClueSystem")
	TArray<UPrimaryDataAsset_Clue*> Clues;

```

This holds a lot of highly customisable information, and you can perhaps start to see how the system works. Creating a Root Node with one of these data assets to designate a entry point to the tree that you have created, as well as easy access to each branch without collapsing a massive list of items. You could also procedurally change this structure at runtime and as long as the tree is rebuilt, the system will be able to handle it. 


Fully C++ Asynchronous loading has been implemented as well, using the ```TSoftObject<>``` templates and ```FStreamable``` makes it a lot more memory efficient. As an example, here is how I am loading in the Data Asset when a player interacts with a Clue Actor:

```C++
void AClueBase::AttemptInteractionWithClue()
{
	UE_LOG(LogBlueprint, Display, TEXT("Clue Interacting"));
	
	FStreamableManager StreamableManager;
    
	TSharedPtr<FStreamableHandle> Handle = StreamableManager.RequestAsyncLoad(ClueDataAsset.ToSoftObjectPath(),
	FStreamableDelegate::CreateUObject(this, &AClueBase::OnDataAssetLoaded), 0, false);

}

void AClueBase::OnDataAssetLoaded()
{
	UE_LOG(LogBlueprint, Display, TEXT("Clue Data Asset Loaded"));

	// Get the Clue Data Asset and Validate it before sending it to the Clue Manager
	UPrimaryDataAsset_Clue* ClueData = Cast<UPrimaryDataAsset_Clue>(ClueDataAsset.Get());
	if(!IsValid(ClueData)) return;

	// Log the Clue Data Asset
	UE_LOG(LogBlueprint, Display, TEXT("Clue Data Asset: %s"), *GetNameSafe(ClueData));
	
	UClueManagerSubsystem* ClueManager = GetGameInstance()->GetSubsystem<UClueManagerSubsystem>();
	if(!ClueManager)
	{
		// Log Error
		UE_LOG(LogBlueprint, Error, TEXT("Clue Manager Subsystem Not Found"));
		return;
	}
	if(ClueManager->CollectClue(Clue))
	{
		// Log Success
		UE_LOG(LogBlueprint, Display, TEXT("Clue Collected, Destroying Actor"));
		Destroy();
	}
}

```
 
## Clue Types

In the original system, I had a single boolean to control if the clue was either a Mesh or Image and to render it to the screen appropriately. This way was messy, and not scalable. I have completely replaced it with Clue Types, which are just an extension from the original PrimaryDataAsset_Clue that can handle their own inspection logic individually. This method is a lot more scalable and provides you with the tools to produce your own Clue Types in Blueprint. 

Each Clue Type will have the base members:

```c++

public:
    // Main function to handle a player inspecting a clue. This is what will be overridden in child classes
	UFUNCTION(BlueprintCallable, Category="Clue System|Inspecting")
	virtual bool ViewClue(UWidgetSwitcher* ClueSwitcherSlot);

    // Main function to handle a player trying to put down a clue. This is what will be overridden in child classes
	UFUNCTION(BlueprintNativeEvent, Category="Clue System|Inspecting")
	bool StopViewingClue(UWidgetSwitcher* ClueSwitcherSlot);

protected:
	/**
	 * @brief The Name of the Clue to display in the UI
	 */
	UPROPERTY(BlueprintReadOnly, EditDefaultsOnly, Category="Basic")
	FString ClueName;

	/**
	 * @brief The Information associated with the Clue 
	 */
	UPROPERTY(BlueprintReadOnly, EditDefaultsOnly, Category="Basic")
    FString ClueInformation;

	/**
	 * @brief The Mesh that will be present in the World
	 */
	UPROPERTY(EditDefaultsOnly, Category="Basic")
	TObjectPtr<UStaticMesh> ClueDisplayMesh;

	/**
	 * @brief The Widget responsible for Inspecting the Clue
	 */
	UPROPERTY(EditDefaultsOnly, Category="Basic")
	TSubclassOf<UUserWidget> ClueWidgetClass;

	/**
	 * @brief The Index of the Clue. Will be filled in at Runtime
	 */
	 UPROPERTY(BlueprintReadOnly, VisibleAnywhere, Transient, Category="Setup")
	int ClueIndex = -1;

	/**
	 * @brief The Location of the Clue, will be filled in at Runtime
	 */
	UPROPERTY(BlueprintReadOnly, VisibleAnywhere, Category="Setup")
	FString ClueLocation;

	/**
	 * @brief The Icon to be displayed on the Clue Slot
	 */
	UPROPERTY(BlueprintReadOnly, EditDefaultsOnly, Category="Information")
	TObjectPtr<UTexture2D> ClueIcon;

	/**
	 * @brief The Widget Class that will be placed in the Branch Manager
	 */
	UPROPERTY(BlueprintReadOnly, EditDefaultsOnly, Category="Information")
	TSubclassOf<UClueSlot> ClueSlotClass;

	/**
	 * @brief The Information that is reliant on OTHER clues being collected
	 */
	UPROPERTY(BlueprintReadOnly, EditDefaultsOnly, Category="Information")
	TArray<FAdditionalClueInfo> AdditionalInformation;

private:
	/**
	 * @brief The Widget Instance that is created from the ClueWidgetClass
	 */
	 TObjectPtr<UUserWidget> ClueWidgetInstance;

```


More properties can be added, for example in the Mesh Type:

```c++

#pragma region Mesh
protected:

	/**
	 * @brief The mesh that should be displayed in the Clue Widget. Different from the Display Mesh as this should be the high resolution one
	 * as it will be viewed in the Clue Widget
	 */
	UPROPERTY(BlueprintReadOnly, EditDefaultsOnly, Category="Information")
	UStaticMesh* ClueMesh;

	/**
	 * @brief The Default rotation that the mesh should be displayed at in the Clue Widget
	 */
	UPROPERTY(BlueprintReadOnly, EditDefaultsOnly, Category="Information")
	FRotator DefaultRotation;

	/**
	 * @brief The Offset from the Camera that the mesh should be displayed at in the Clue Widget
	 */
	UPROPERTY(BlueprintReadOnly, EditDefaultsOnly, Category="Information")
	FVector CameraLocationOffset;

	/**
	 * @brief The default distance that the camera should be from the mesh in the Clue Widget
	 */
	UPROPERTY(BlueprintReadOnly, EditDefaultsOnly, Category="Information")
	float CameraDistance;

	/**
	 * @brief The Identification of the Inspector that should be used to display the mesh in the Clue Widget.
	 */
	UPROPERTY(BlueprintReadOnly, EditDefaultsOnly, Category="Setup")
	FString MeshInspectorName = "";

#pragma endregion

public:
	
	UFUNCTION(BlueprintPure, Category="Clue System|Information")
	UStaticMesh* GetClueStaticMesh() const {return ClueMesh;}

	UFUNCTION(BlueprintPure, Category="Clue System|Information")
	FRotator GetDefaultRotation() const {return DefaultRotation;}

	UFUNCTION(BlueprintPure, Category="Clue System|Information")
	FVector GetCameraOffset() const {return CameraLocationOffset;}

	UFUNCTION(BlueprintPure, Category="Clue System|Information")
	float GetCameraDistance() const {return CameraDistance;}

	UFUNCTION(BlueprintPure, Category="Clue System|Information")
	FString GetMeshInspectorName() const {return MeshInspectorName;}

```



## Summary

All of these new changes should make it a lot easier for designers to craft the narrative experience that they want, with interconnected information, nested categories & as many different types of clues as they can imagine.