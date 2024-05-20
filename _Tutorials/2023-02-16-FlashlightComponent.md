---
layout: post
title:  "How To Make A Flashlight Component in UE5 using C++"
thumbnail: "/assets/images/Flashlight/Thumbnail.png"
categories: UE5 C++ Components Flashlight
date: 16-02-2023
regenerate: true
---


# How To Make A Flashlight Component in C++

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

Flashlights are very easy to create in C++ as they are essentially Spotlight Components that are already built into the Engine

![](/assets/images/NewC%2B%2BClass.png)

Select a Spotlight as the Base Class

![](/assets/images/Flashlight/SpotlightComponent.png)

Then choose where it should be save, and under what name. If you have a module to put it into, this is where you select it

![](/assets/images/Flashlight/CreateFlashlightComponent.png)


When it finally loads up in your IDE of choice, you'll see a quite empty object.  
We will start by adding in a few new variables and a function. Let's start in the header file:

```C++
public:
    UFUNCTION(BlueprintCallable)
    void ToggleFlashlight();

protected:
    UPROPERTY(BlueprintReadOnly, EditDefaultsOnly)
    float FlashlightIntensity = 5000;
    
    UPROPERTY(BlueprintReadOnly)
    bool bIsEnabled = true;
```

The ```UPROPERTY``` & ```UFUNCTION``` allow us to declare some more specific behaviour for each variable. 
It is how we declare which ones we want to be visible and editable and where. It is also how we stop certain 
variables from being garbage collected (could use shared pointers here as well) 

Now let's move onto the C++ file where we define the behaviour:
```c++
void UFlashlight_Component::ToggleFlashlight()
{
    // ! == NOT 
    bIsEnabled = !bIsEnabled;
    // Using a One Line Boolean to easily switch intensity (Bool ? True : False)
    SetIntensity(bIsEnabled ? FlashlightIntensity : 0);
}
```

Compile/Build it (Control + B in Visual Studio) and now open Unreal Engine again.
Add it to a Blueprint and hook up the controls

![](/assets/images/Flashlight/BP%20Flashlight.png)


Test it out with the input and you should have a Flashlight that toggles on and off.

If that is all that you need, please feel free to scroll away, but many of you may need a flashlight that runs out of battery so I'll show you that now as well, using timers.

You should have a BeginPlay function in the C++ file already. If not then here is 
what it should look like:

```c++
void UFlashLight_Component::BeginPlay()
{
	Super::BeginPlay();
}
```

If you get an error about how there is no member "BeginPlay" in the component then 
double check 2 things: 
    
- That ```UFlashlight_Component``` is definitely part of the ```void UFlashlight_Component::BeginPlay```
- That ```virtual void BeginPlay() override;``` is in the header file. If not, just copy it from here

If neither of things fix it, then it's time to turn to google unfortunately. 

Moving on, we are going to setup a timer on BeginPlay that will loop to call a function at a given interval. 
To control this interval, we are going to add in a few more variables to the header file and expose them to blueprints so
that we can change them a lot easier and without needing to recompile the project everytime we make a slight tweak. 

First thing we are going to need is the current Flashlight Power, followed by how quickly we want the power to reduce and finally the step to remove from the power each interval. 

```c++
protected:
    	UPROPERTY(BlueprintReadOnly, EditDefaultsOnly)
		float FlashlightPower = 100;
	
		UPROPERTY(BlueprintReadOnly, EditDefaultsOnly)
		float FlashlightPowerDecayRate = 1;

		UPROPERTY(BlueprintReadOnly, EditDefaultsOnly)
		float FlashlightPowerDecayStep = 1;
```

So we have a flashlight power of 100, and when it is active, it will take away 1 from it every 1 second. 

Now we need to declare the function that we are going to call to reduce it. 

```c++
protected:
    UFUNCTION()
    void FlashlightDecay();
```

We have an empty ```UFUNCTION```, but we need this specifier as it allows us to bind a timer to it. 
Without it, it will throw an error when we build.

In the definition, we will take the current Flashlight Power, minus the decay step and check to see if 
it has reached 0. If it has then the flashlight has run out of power so we need to turn it off.

```c++

void UFlashlight_Component::FlashlightDecay(){

    if (bIsEnabled)
        {
        // Flashlight Power is having the step taken away from it and then
        // clamped between the 0 and it's own current value.
            FlashlightPower = 
                FMath::Clamp(FlashlightPower - FlashlightPowerDecayStep, 0, FlashlightPower);
        }

    // Always good to ensure if it goes below 0, especially if it is a float
	if(FlashlightPower <= 0)
	{
	    // Turn off the Flashlight
	    ToggleFlashlight();
	}
}
```

We also need a way to add Flashlight power back, so I'm going to make a quick function to 
reset the battery power to full. 

Header File:

```c++
public:
    UFUNCTION(BlueprintCallable)
    void ResetPower();
    
protected:
    UPROPERTY(BlueprintReadOnly, EditDefaultsOnly)
    float FlashlightPowerMax = 100;
```

CPP file:

```c++
void UFlashlight_Component::ResetPower()
{
    FlashlightPower = FlashlightPowerMax;
}
```

This is nearly ready for the timer, but we need to revisit the first function where we toggle the flashlight. 
We don't want to be able to turn it on if we have no power. 

```C++
void UFlashlight_Component::ToggleFlashlight()
{
    if(FlashlightPower <= 0) bIsEnabled = false;
    // ! == NOT
    else bIsEnabled = !bIsEnabled;
    // Using a One Line Boolean to easily switch intensity (Bool ? True : False)
    SetIntensity(bIsEnabled ? FlashlightIntensity : 0);
}
```

With that setup we can implement the final part of this tutorial, the timer.

We need to add in 2 more variables in the header file: 
```c++
private:
	FTimerDelegate FlashlightDecayDelegate;
	
	FTimerHandle FlashlightDecayHandle;
```

The delegate is what we attach the function to on begin play and the handle is what we 
use to reference the timer, so we can pause it or even clear it if necessary.


In the BeginPlay definition in the C++ file, here is how we bind a delegate to a function
and then use the delegate and handle in a timer:

```c++
void UFlashlight_Component::BeginPlay()
{
	Super::BeginPlay();

	FlashlightDecayDelegate.BindUObject(this, &UMySpotLightComponent::FlashlightDecay);

    // Gets the World Timer Manager and assigns the Timer
	GetWorld()->GetTimerManager().SetTimer(FlashlightDecayHandle, FlashlightDecayDelegate, FlashlightPowerDecayRate, true);
}
```

With that last addition, the basic flashlight should be complete.

Complete Code: 

Header file:
``` C++
// Fill out your copyright notice in the Description page of Project Settings.

#pragma once

#include "CoreMinimal.h"
#include "Components/SpotLightComponent.h"
#include "Flashlight_Component.generated.h"

/**
*
*/
UCLASS(Blueprintable, ClassGroup = (Custom), meta = (BlueprintSpawnableComponent))
class PROJECTMIRROR_V9_API UFlashlight_Component : public USpotLightComponent
{
GENERATED_BODY()

	virtual void BeginPlay() override;

public:

	UFUNCTION(BlueprintCallable)
	void ToggleFlashlight();

	UFUNCTION(BlueprintCallable)
	void ResetPower();

protected:
UFUNCTION()
void FlashlightDecay();

	UPROPERTY(BlueprintReadOnly, EditDefaultsOnly)
	float FlashlightIntensity = 5000;
    
	UPROPERTY(BlueprintReadOnly)
	bool bIsEnabled = true;

	UPROPERTY(BlueprintReadOnly, EditDefaultsOnly)
	float FlashlightPower = 100;
	
	UPROPERTY(BlueprintReadOnly, EditDefaultsOnly)
	float MaxFlashlightPower = 100;
	
	UPROPERTY(BlueprintReadOnly, EditDefaultsOnly)
	float FlashlightPowerDecayRate = 1;

	UPROPERTY(BlueprintReadOnly, EditDefaultsOnly)
	float FlashlightPowerDecayStep = 1;

	FTimerDelegate FlashlightDecayDelegate;
	
	FTimerHandle FlashlightDecayHandle;

};
```


C++ file: 

```c++
// Fill out your copyright notice in the Description page of Project Settings.


#include "Flashlight_Component.h"

void UFlashlight_Component::FlashlightDecay(){

	if (bIsEnabled)
	{
		// Flashlight Power is having the step taken away from it and then
		// clamped between the 0 and it's own current value.
		FlashlightPower = 
			FMath::Clamp(FlashlightPower - FlashlightPowerDecayStep, 0, FlashlightPower);
	}

	// Always good to ensure if it goes below 0, especially if it is a float
	if(FlashlightPower <= 0)
	{
		// Turn off the Flashlight
		ToggleFlashlight();
	}
}

void UFlashlight_Component::BeginPlay()
{
	Super::BeginPlay();

	FlashlightDecayDelegate.BindUObject(this, &UFlashlight_Component::FlashlightDecay);

	GetWorld()->GetTimerManager().SetTimer(FlashlightDecayHandle, FlashlightDecayDelegate, FlashlightPowerDecayRate, true);
}

void UFlashlight_Component::ToggleFlashlight()
{
	if(FlashlightPower <= 0) bIsEnabled = false;
	// ! == NOT
	else bIsEnabled = !bIsEnabled;
	// Using a One Line Boolean to easily switch intensity (Bool ? True : False)
	SetIntensity(bIsEnabled ? FlashlightIntensity : 0);
}

void UFlashlight_Component::ResetPower()
{
	FlashlightPower = MaxFlashlightPower;
}

```

For more information:
 - [Unreal Documentation - UPROPERTY Specifiers](https://docs.unrealengine.com/5.0/en-US/unreal-engine-uproperty-specifiers/)
 - [Unreal Documentation - UFUNCTION Specifiers](https://docs.unrealengine.com/5.0/en-US/ufunctions-in-unreal-engine/)
 - [Unreal Documentation - Variables & Timers Quick Start](https://docs.unrealengine.com/5.1/en-US/quick-start-guide-to-variables-timers-and-events-in-unreal-engine-cpp/)
 - [Unreal Engine Absolute Beginner Series - AgentArachnid](https://youtube.com/playlist?list=PL_5_FeTK4JYbJK0wKw1oN2xyhw-LFLgY3)