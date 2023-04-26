---
layout: post
title:  "Dev Log - Project Mirror #1"
categories: UE5 C++ 
date: 01-01-2023
regenerate: true
---


Project Mirror is a project I'm working on through SpyderWeb Studios, my games studio. This is the project that I have been working on since I started games development, it is the project that I opened Unreal Engine for the first time to develop.  
<p></p>
There has obviously been a lot of versions of this project as I have ran into common problems that many people when they start off do. From accidentally deleting the source folder without a backup to the whole project corrupting due a USB stick, I have ran into all of them but never have I done so twice. I have taken each setback in my stride and just carried as much as I can into a new project and continue. 
<p></p>
When I first started, I had no source control, doing pretty much everything on tick on the player character and imported every package that I could find that seemed cool at the time (I was 17 at this time). Since then, I have setup my own server on perforce, and have delegated to components that run their logic on timers that I can more finely control than if it was on tick. And of course, I have been a lot more selective with the marketplace content I bring into the project. Not only that, but I have also been utilising modules in my C++ code to make it easier to encapsulate classes and carry them over into other projects if I want to. 
<p></p>

Onto the actual development I have accomplished over the past few iterations, I have used C++ Modules & Plugins to implement:
<div>
<ul>
<li> Saving and Loading Subsystems that can be used in any project, after some per project customisation </li>
<li> Controllable drone that can fly around the map </li>
<li> Clue System that is completely <a href = "https://github.com/SpyderWeb-Studios/ClueSystem">Open Source</a> and is designed for users who haven't touched C++ before </li>
<li> A Hacking Mini Game that is based on actual Boolean Logic and Logic Gates </li>
<li> An AI Director that can see where the player has been and what they have interacted with the most, to guide the AI Pawns to deliver the best possible experience </li>
</ul>
</div>
<p></p>
In Blueprints, I then derive from the relevant classes as well as prototype new features to convert to C++ later. 

<p></p>
I'm currently on Version 0.0.8 of the project so I am still working on the base game, and I am planning on adding in a multiplayer mode in version 0.1.3 after the base 
gameplay for the singleplayer story mode is nearly complete. It is very much still early days in the development cycle, but I'm confident that with my experience and drive 
to finish this project, this game will get released.  