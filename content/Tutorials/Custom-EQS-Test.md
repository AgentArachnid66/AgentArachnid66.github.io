+++
title = 'Custom EQS Test'
date = 2024-09-05T14:19:16+01:00
draft = true
+++

EQS (Environment Query System) is a powerful tool when making an AI in Unreal Engine. Creating your own EQS Tests can open the way
for more advanced interactions and more intelligent decision making. 

In this tutorial, we will be making an EQS Test that can get a value from an actor using an interface.

## EQS Test

We will start by making a C++ Class:

Expand out the All Actors and search for `EnvQueryTest`

Place this Class wherever you would like in your project and open it up. 

In the **header (.h)** file we will only need 2 functions: 

```c++

	UEQ_PlayerScore();
	
	virtual void RunTest(FEnvQueryInstance& QueryInstance) const override;

```

We have here a constructor and a virtual function. Define these in the **.cpp** file and you should have this in that file:

```c++

UEQ_PlayerScore::UEQ_PlayerScore()
{

}

void UEQ_PlayerScore::RunTest(FEnvQueryInstance& QueryInstance) const
{

}

```

The first thing that we need to define is what `Item` is going to be a valid type for this Test.

If you are wondering what an `Item` is, then it is basically what EQS spawns in and performs these Tests on to determine where
the final location is. They are created by the [`Generators`](https://docs.unrealengine.com/5.1/en-US/eqs-node-reference-generators-in-unreal-engine/). There are essentially 2 types of item, Actor and Vector. We need to tell this test that we only care about the Actor base as we cannot run Interface functions on the Vectors.  

To do this, we need a new include so at the top of the **.cpp** file, add: 

```c++
#include "EnvironmentQuery/Items/EnvQueryItemType_ActorBase.h"
```

and in the constructor:

```c++
UEQ_PlayerScore::UEQ_PlayerScore()
{
    ValidItemType = UEnvQueryItemType_ActorBase::StaticClass();
}
```
That is all we need to do, so let's move onto the Test itself. 

First we need to check if the Owner is valid so lets add that check to the `RunTest` function:


```c++
void UEQ_PlayerScore::RunTest(FEnvQueryInstance& QueryInstance) const
{	
	//UDebugFunctionLibrary::DebugLog("Started Player Score Test", EDebugType::DT_Log);
	UObject* QueryOwner = QueryInstance.Owner.Get();
	if (!IsValid(QueryOwner))
	{
		return;
	}
}
```

Now that we know that the Query Owner is valid, we can bind the Min and Max threshold values. Add this after the if statement. We want this to run if it does **NOT** return. 

```c++
    FloatValueMin.BindData(QueryOwner, QueryInstance.QueryID);
	float MinThresholdValue = FloatValueMin.GetValue();

	FloatValueMax.BindData(QueryOwner, QueryInstance.QueryID);
	float MaxThresholdValue = FloatValueMax.GetValue();
```

And finally, we can iterate over the Items. We are going to be adding in the Item Iterator:

```c++
for (FEnvQueryInstance::ItemIterator It(this, QueryInstance); It; ++It)
	{		
		float PlayerScore = 0;
		It.SetScore(TestPurpose, FilterType, PlayerScore, MinThresholdValue, MaxThresholdValue);
	}

```

The SetScore function has a few overloads, but for setting it based on a float and 2 threshold values this is one to use. 

## Interface

After we have the Test setup, we can move onto the interface that we will be trying to call on the item actors. 

Scroll to the bottom of the Common Classes and you'll find the Unreal Interface. Call it whatever you'd like and open it up. For this example, we will just make one function 
that returns a float value. It is important to mark it as a BlueprintNativeEvent. 

In the I(InterfaceName) class, add in: 

```c++

	UFUNCTION(BlueprintNativeEvent, BlueprintCallable)
	float GetPlayerScore();
```

Your Interface **.h** file should look something like:

```c++
// This class does not need to be modified.
UINTERFACE(MinimalAPI)
class UAI_Interface : public UInterface
{
	GENERATED_BODY()
};

/**
 * 
 */
class ARTIFICIALINTELLIGENCESYSTEM_API IAI_Interface
{
	GENERATED_BODY()

	// Add interface functions to this class. This is the class that will be inherited to implement this interface.
public:

	UFUNCTION(BlueprintNativeEvent, BlueprintCallable, Category="AI Interface")
	float GetPlayerScore();
};

```

## Putting it all together

Returning to the EQS Test, we need to add to the Item Iterator to actually run the Interface call. 

Add the following to the `for loop` after declaring `PlayerScore`

```c++
    if(AActor* ItemActor = GetItemActor(QueryInstance, It.GetIndex()))
    {
        PlayerScore = IAI_Interface::Execute_GetPlayerScore(ItemActor);
    }

```

So your final C++ RunTest function should look like:
 
 ```c++
 void UEQ_PlayerScore::RunTest(FEnvQueryInstance& QueryInstance) const
{	
	UObject* QueryOwner = QueryInstance.Owner.Get();
	if (!IsValid(QueryOwner))
	{
		return;
	}

	FloatValueMin.BindData(QueryOwner, QueryInstance.QueryID);
	float MinThresholdValue = FloatValueMin.GetValue();

	FloatValueMax.BindData(QueryOwner, QueryInstance.QueryID);
	float MaxThresholdValue = FloatValueMax.GetValue();

	for (FEnvQueryInstance::ItemIterator It(this, QueryInstance); It; ++It)
	{		

		float PlayerScore = 0;
		if(AActor* ItemActor = GetItemActor(QueryInstance, It.GetIndex()))
		{
			PlayerScore = IAI_Interface::Execute_GetPlayerScore(ItemActor);
		}		
		It.SetScore(TestPurpose, FilterType, PlayerScore, MinThresholdValue, MaxThresholdValue);
	}
}

```

Further Information:
- [Unreal Documentation - EQS](https://docs.unrealengine.com/5.1/en-US/environment-query-system-in-unreal-engine/)
- [Mercana - Writing EQS Test](https://mercuna.com/writing-eqs-test/)
