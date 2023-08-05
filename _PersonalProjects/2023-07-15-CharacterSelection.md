---
layout: post
title:  "Character Selection Plugin"
categories: UE5 C++ 
date: 15-07-2023 
regenerate: true
---


Having done some research, a lot of other people are using save objects to maintain a player's choice over level transitioning. I found this method too clunky and fragile, and decided to dig deeper into the engine to find a better alternative. 

Unreal Engine has a feature called Seamless Travel, which gives the developer a chance to transfer data between persistent actors. I have a component on my Player State in the Lobby map, where the player chooses which character they want to play as. I wanted to transfer the data on this component onto the component in the main game, before trying to spawn the Pawn. 

Going to C++ for more control, I added to my main Player State that I use in the Lobby and override the `SeamlessTravelTo` function. Attempt to find the corresponding component on the Player State that will be loaded in the main game, and set the values. 

This will allow the component to be setup but that's only half of the puzzle. The other half is loading it in when the game starts. Luckily, this method makes use of the Game Mode's `Get Default Pawn From Controller` function as the default Pawn Class will already be set by the time that the execution gets to this point. 

Simply by getting the player state from the input controller, and attempting to find the component that holds the data, we can plug in the Pawn Class retrieved into the return value for the `Get Default Pawn From Controller`function.

This method is a lot cleaner, extensible and more in line with the engine design, so is (in my opinion) superior to a lot of other implementations. 

