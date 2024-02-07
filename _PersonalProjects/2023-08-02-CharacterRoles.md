---
layout: post
title:  "Character Roles Plugin"
categories: UE5 C++ 
date: 02-08-2023
regenerate: true
---

You can read the Full Documentation I made for this plugin [here](https://spyderweb-character-roles-docs.netlify.app/). It should be released on the Marketplace shortly. 


## The Problem

 The main problem I wanted to solve with this module was I wanted to be able to define a character role, assign it randomly and give control how many this role can be assigned to both the developer and player and I wanted all this to be fully replicated. I also wanted to give any actor the ability to be assigned a role, so that I can generate the role anywhere on the network to control who it replicates to. 

## Solution

 The first step was to be able to register the UObjects to assign the roles. I decided to have a 2 component system to handle this. The first one to manage the roles, which I intend to keep on the server only, and the second which will be more agnostic and handle the registering and receiving of roles. 

 To make it more flexible, I also created an interface to be implemented on the Owning Actor of the Register Component. This is to extend the functionality and also redirect the Register Component to the Management Component if it is placed somewhere other then the Game Mode. 

 The final step in this project was to actually assign the roles. 
 ~~I currently have 2 flags to control if the role is missable and if it can 
 be overwritten by another role.~~ I have updated this system to use an integer
to place priority on the roles, so that the highest priority role cannot be overwritten
by a lower priority role. This is much simpler to use and to implement.

 I also have a variable to control how many of each role can be assigned. 
 Using these 3 values, the roles are assigned to each Registered Component 
 which takes the Subclass and spawns in a new component automatically. It is the
intention to enable the host to change these parameters to control the gameplay
experience and to allow for a more dynamic game.

