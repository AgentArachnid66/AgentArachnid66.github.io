---
layout: post
title:  "Clue System Plugin Version 3"
categories: UE5 C++
date: 02-05-2023
regenerate: true
---
# Clue System Plugin Version 3

Taking from what I learned in the first two iterations on the plugin, I created some criteria for this plugin:
- Multiplayer Support from the ground up
- Simple to use and extend
- Decoupled from each component to allow for easy integration into other systems
- Fully C++ with Blueprints for ease of use
- Fully documented


## The Problem and Solutions

### Multiplayer Support

#### Data Structure
In the previous iterations, I've used either a [simple TArray](2023-01-02-ClueSystem.md) 
or a [tree based structure](2023-04-20-ClueSystem_2.md) to represent the clues. 
This was fine for a single player game, but when it came to multiplayer, 
it was a bit more difficult to handle. I needed to make sure that the clues were 
replicated to all clients, and that the clues were being updated correctly.

TArrays are not the best choice for replicating data, as they replicate the entire
content of the array each time it is updated. This can be very inefficient, especially
if the array is large. The tree based structure was not much better in this respect, 
as the TMap I was using could not be replicated at all. If I wanted to use it in a
multiplayer game, I would have to send the insertions, deletions and mutations of the
tree in an RPC, which would be very inefficient, or in a variable replication, which
would be very complex and error prone. I could use a TArray to represent the tree,
but at that point I would be back to the same problem as before.

To solve this, I needed to use a data structure that was more efficient for replicating
data. I decided to use something called a Fast Array Serialiser, which was designed for 
replicating arrays of data in Unreal Engine. This would allow me to replicate solely the
changes to the array, rather than the entire array each time it was updated. This would
make the replication much more efficient. It also has the advantage of being easy to 
integrate events for each update, so I could easily update the UI when the array was updated.
This became especially helpful for when I used the Listen Server architecture as the server
would be able to update the UI as well as the client without the need for additional checks.

#### Replication

The previous iterations of the plugin were not designed with multiplayer in mind. I was using 
subsystems to manage the clues, which were not capable of replication. I attempted to tack on 
replication to the subsystems, by using components to handle the communication, but it was
very cumbersome and inefficient to do so. I needed to make sure that the plugin was designed
with multiplayer in mind from the ground up.

To make the plugin multiplayer ready, I started with an actor component rather than a subsystem.
This would allow me to easily replicate the data to relevant clients, and would make it easier
to handle the replication.

### Simplicity and Flexibility
The [last iteration](2023-04-20-ClueSystem_2.md) of the plugin was very rigid in how 
it was used. It was designed to be used in a very specific way, and if you wanted to 
use it in a different way, you would have to do a lot of work to get it to work. This came
from how I was using a tree based structure to represent the clues, and how I was updating the 
UI from this. To make it more flexible, I needed to decouple the UI from the data, removed the 
tree structure and made it more of a flat structure. As well as this, I needed to remove the 
base classes for the UI, and opt for interfaces to make it more flexible.

To flatten the structure, I needed to remove the tree based structure but I still needed 
a way to query the data. I decided to use a Gameplay Tags to attach to each clue, and 
then use a TArray to store the clues. This would allow me to query the data easily, and
maintain a flat structure. For example, I could have a clue referencing one area, but is 
crucial to a storyline and then all I'd need to do in the UI is listen for either tag and
display the clue. In the previous iteration, I would have had to traverse the tree to find
the area and then look for the clue. This would have been very inefficient and would have
limited the flexibility of the system by making it impossible to have a clue that was both
crucial to a storyline and in a specific area, whilst displaying it in both places. 

### Decoupling

The previous iterations of the plugin were very tightly coupled. The data was tightly coupled to the UI,
and the UI was tightly coupled to the data. This made it very difficult to use the plugin in other systems,
as you would have to use the UI as well. To make it more decoupled, I needed to remove the base classes for the UI,
and opt for interfaces to make it more flexible. This would allow the plugin to be used in other systems, and
would make it easier to extend the plugin.

Interfaces were incredibly useful in this regard. The clues doesn't have to care about the class of the 
UI that is in charge of displaying it, it just needs to know that the UI implements the interface. This
meant that I could easily swap out the UI for a different one. 

### Fully C++ with Blueprints

The previous iterations of the plugin were mostly C++, with a bit of Blueprints. This made it difficult to
use the plugin in Blueprints, as you would have to use the C++ classes. To make it easier to use the plugin
in Blueprints, I needed to make sure that the plugin was fully C++ with Blueprints for ease of use. To do this,
I made greater use of BlueprintNativeEvents, which allowed me to define events in C++ that could be overridden
in Blueprints. This made it much easier to use the plugin in Blueprints, as you could just override the events
in Blueprints, rather than having to use the C++ classes.

### Fully Documented

The previous iterations of the plugin were not very well documented. This made it difficult to use the plugin,
as you would have to read through the code to understand how it worked. To create the documentation, I used
[docusaurus](https://docusaurus.io/). 
