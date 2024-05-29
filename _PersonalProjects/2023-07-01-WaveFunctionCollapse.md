---
layout: post
title:  "Wave Function Collapse Module"
categories: UE5 C++ 
date: 01-07-2023
regenerate: true
---

I knew that I wanted to implement a procedural algorithm for my latest project, and decided on creating a wave function collapse algorithm as it seemed like a good starting point for learning more advanced procedural algorithms. To add more challenge to this, I wasn't using it for generating maps, or textures but instead wanted it to interact with choosing which items to spawn into the map based on a variety of factors that were not known when starting the implementation.

Since I wanted this to be as extensible and useful as possible, I decided to implement it using an interface and the subsystem would hold an array of registered objects. Each UObject would handle how they would receive the propagation from the system, how they define their entropy and how they collapse. 

The result of this experiment is a highly scalable and useful module that can be used from as small as selecting a random colour for a bunch of cubes based on distance to selecting which items to spawn in based on gameplay tags and finally, to the common usage of the wave function collapse algorithm, generating tilemaps. 

![](https://imgur.com/WnU9HI9.png)
