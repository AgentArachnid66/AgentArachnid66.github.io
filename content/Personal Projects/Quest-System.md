+++
title = 'Multiplayer Quest System - Unreal Engine 5'
date = 2024-04-21T11:57:22+01:00
+++

# Multiplayer Quest System

I decided to challenge myself by creating a multiplayer quest system. 
I wanted to create a system that would allow for a variety of quest types & quest rewards,
and be able to handle multiple players.

The approach I went for was a very Object-Oriented one. Everything is split up into different classes that will handle the different aspects of the quest system. Quests contain stages which hold Objectives, which hold a set of prerequisites and rewards and they can track the progress via a Progress Object.

![](https://imgur.com/m8uNuxN.png)

## Quest Data

The Data Asset can be setup to provide a wide range of possibilities. In the example below, I have setup an quest for the player who was assigned the murderer role. You can see that it has the prerequisite for a role, which prevents this quest being accidentally added to another role. You can add prerequisites for pretty much anything, and you can add as many as you require.  

![](https://imgur.com/ppTRlmT.png)

Each of these components can be extended to create a variety of different quest types. To add on to this, I have set the system up to be easy to use by using instanced objects that can be created in the editor and then used in the game.

![](https://imgur.com/zckujE7.png)

### Progress Objects

The Instanced Objects can be used to create a variety of different quest types. Each of them have their own set of
variables to config the Objective to be used in the game. This allows for a lot of flexibility in the system.

![](https://imgur.com/MCfnLeR.png)

Easily one of the simplest, yet powerful, features of the Progress Object is the Progress Text field. This is a text
field that will be used to display the progress of the Objective to the player using tokens that can be registered 
by the Progress Object. In this instance, it has the token `{Distance}` which will be replaced with the distance the player
has to the target. For example, in the Timer class it will have other tokens such as `{Time}` and `{TimeLeft}`. You can 
add to these tokens in the Blueprint Classes as well.

Each Objective State have their own Pre-requisites and Rewards, so if you have a quest that requires you to kill 10 enemies in
order to complete it, you can set the pre-requisites to `Completed` to be 10 enemies killed and the reward to be 100 gold.

The Progress Priority is used to determine which Objective State should be tracked first. This is usually not needed to be altered,
but it is there to allow for more complex quest types.

### Prerequisite Objects

Before an Objective can be accepted or change state, the Player that owns the Objective must meet the prerequisites. 
This is where the Prerequisite Object comes in. It is a simple class that holds a set of Prerequisites that must be 
met before the action can be taken. These can be as simple as checking if the Player has a certain item in their inventory, 
or implements the right interface, or it can be as complex as a composite that checks that the player has completed a 
Quest and is a certain level.

Currently, it does not return anything if the Player has failed the prerequisite check, but this can be added in the future.
This would be a good feature as it will allow for more complex quest types that can be failed and retried, or to give player
 feedback on why they can't accept the Objective. This feature is a part of the internal roadmap for after releasing the plugin on the marketplace.

### Reward Objects

![](https://imgur.com/7GwfysD.png)

After changing the state of an objective, the Player can receive Rewards. These can be added to any Objective State and 
will be given to the Player when the Objective State is changed. This is a simple class that holds the data for the
reward to give. This can be as simple as giving the Player 100 gold, or as complex as giving the Player a new item. The 
aim of this system was to be able to extend to interact with any system in the game. 

You can create custom reward objects for any purpose and to interact with external systems. In the plugin, I have included 
a simple XP Component and Gold Management Component to give examples on how to do this in both C++ and Blueprint. 

I'd like to add to the base class to be able to retrieve information for the UI. This would allow for the UI to display
the rewards dynamically. 

## Components

The Quest Manager is the main class that will handle the quest system. It will be responsible for receiving the 
quests and tracking the progress on them. This is the component that will be owned by the Player on the network and 
be responsible for replicating the Quest data.

I uses a Fast Array to store the Objectives and Progress Objects separately. This is to allow for quick access to the
relevant data. This is important as the system will be used in a multiplayer game and will need to be able to handle
a lot of data quickly and efficiently. In larger scale games with potentially thousands of Quests, only replicating the
data that has changed will greatly improve the performance of the system. 

Another benefit of using a Fast Array is that it is easier to handle a Listen Server architecture as you can broadcast an event when 
it changes and the clients can broadcast the event when it receives the change in the replication functions 
(`PostReplicatedAdd`, `PostReplicatedChange`). Using traditional `TArray` would require less clean code to achieve the same result.  

When an Objective is added, it will create a duplicate of the Progress Object and add it to the Progress Array. This is
how the system will be able to track the progress of the Objective whilst keeping the original data intact. In earlier 
prototypes, the data assets themselves would keep track of each player's progress, but this was not efficient and would 
not work well in a multiplayer game. I'm glad I took a step back to re-evaluate the system and re-implement it in a
more efficient way.

## Conclusion

I'm happy with the progress I've made on this system. It's been a great learning experience and I've been able to
create a system that is very flexible and can be used in a variety of different games. I'm looking forward to
extending this system to be able to interact with other systems in the game and to be able to create more complex
quest types.

I'm looking into publishing this system to the Unreal Marketplace in the future. I think it would be a great asset
for other developers to use in their games and a great competitor to the other quest systems on the marketplace that 
are more rigid in their design. 


