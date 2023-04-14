---
layout: post
title:  "Dev Log - Project Mirror #2"
categories: UE5 C++ 
date: 14-04-2023
regenerate: true
---

I have made some very good progress on Project Mirror, including:

 - Refactoring the Clue System Plugin to enable nesting using a Tree Structure rather than a List
 - Actors in a certain Area can be associated with a different Volume, essentially allowing level designers to control which objects are affected by which triggers and to be able to fine tune the boundaries of an Area with Brush Editing Tools
 - A highly flexible Tutorial System that utilises Data Assets and Interfaces to provide a data driven system that can be implemented into any project. 

 Over the next few weeks, I plan to implement
 - The Echolocation Shader that can be seen in my [demo](https://youtu.be/v4XDpTfg74E). This is not what it will look like in it's final form, but it provides a great foundation
 - Major mechanic UI (Stamina, Adrenaline, Flashlight, AirTank) 
 - Improvements to the AI Director to get more precise Player Tension
 - Movement Mechanics: 
    - Crouching
    - Going prone
    - Vaulting
 - Major Mechanics:
    - Companion Drone:
        - Moving with the Player
        - On Player Death, Control the Drone to get back to a Respawn Station
    - Airtank
    - Pushable Actors

So far, this project has been going really well though progress is slow. I have been focusing a bit more on optimisation, using techniques such as SoftObjectPtrs and Async Loading to prevent blocking the main game thread when loading in the Clue Data Assets. My main aim at the moment is to allow as many people as possible to play this game.