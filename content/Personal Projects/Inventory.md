+++
title = 'Multiplayer Inventory & Crafting - Unreal Engine 5'
date = 2024-01-16T11:57:04+01:00
+++


I have been working on a project that involves an inventory and crafting system. I have been using Unreal Engine 5 and C++ to create this system.

This system will need to be functional for multiplayer, easy to extend and 
easy to use. I have been using a lot of the built-in Unreal Engine features 
to make this system as robust as possible.

I have integrated the systems into the Editor and made it easy to create new items and recipes.

![](https://imgur.com/y2Hqti9.png)


## Inventory
The Inventory System has been designed to be as easy to use, and easy to extend. 
You can extend the Inventory Component to add in as many additional features as 
you require and overwrite the functions provided to produce custom behaviour.

It uses the FastArraySerializer to only replicate the data that has changed. 
To make working with this easier, I have created plenty of utility functions 
to use this feature.

### Inventory System Features
- Fully Replicated Inventory Component:
  - Feature Set:
    - Adding and Removing Items
    - Transferring Items between Inventories
    - Using Items
    - Dropping Items
    - Utility Functions to retrieve data about the current inventory and the current state of it
    - Events for when the state of the inventory changes, that work on both Server and Client
    - Function Library to expose common Utility functions that aren't natively supported.
- Items:
  - Can be defined as individual assets, so can be loaded separately.
  - Different Item Class for different behaviour
  - Interfaces to retrieve information
  - Separates the Inventory Items from the Actors, to lessen the network load whilst maintaining functionality

Items are their own Assets that can be created in the editor, and can be instantiated like a data asset.

The base Item Class can be extended to add in additional functionality, such as:
  - Gameplay Abilities
  - Attribute Modifiers
  - Crafting Recipes Unlock


Below is an example of an Item that has a Gameplay Ability attached to it.

![](https://imgur.com/OwUw0Se.png)

The Default section is where the Ability can be assigned. It was specialised as a 
BP child of the Item class, which is why the variables that should be hidden are visible.
The Inventory Section is the Item Defaults that are used by the Inventory System for the UI
and Inventory Management functionality. 

### Crafting System

The Crafting System has been designed to be as easy to use, and easy to extend.

The Workflow for the crafting is as follows:

- Player Requests to Craft an Item via the Crafting Component
- The Crafting Component sends a request to a Recipe Management Component
- The Recipe Management Component checks if the player has the required items to craft the item
and if the Player can craft the item
- If the Player can craft the item, the Recipe Management Component will remove the required items from the player's inventory
- Depending on the configuration, the Recipe Management Component will either give the player the crafted item or drop it into the world

By separating the workflow up into these steps, it opens up the possibility to add in additional features such as:
    - Crafting Benches that have different crafting recipes to other Crafting tools
    - Player crafting recipes by themselves without the need for a crafting bench
    - Crafting recipes that require a certain crafting tool to be used
    - Crafting recipes that require a certain crafting bench to be used
    - Adding the item directly into the player's inventory or dropping it into the world on 
    a per crafting table basis
    - Locking players out of recipes until they have reached a certain metric

The Recipe Objects are defined as assets, so they can be loaded separately. This allows for easy extension and modification of the crafting system.

![](https://imgur.com/tslZjc5.png)

The Recipe Object has a few different sections to it. 
The first section is the Recipe Ingredients, which is used by the Recipe Management Component to check if the player can craft the item. 
The second section is the Recipe Results, which is used to define what the player will receive when they craft the item. 
The third section is the Recipe UI, which is used to show the player what they can craft and what they need to craft it on the UI.

With these two systems combined, it opens up a lot of possibilities for the game that I am working on. I am excited to see how this system will evolve as the project progresses.

## Conclusion

This has got to be one of the most complex systems that I have worked on by myself and I cannot imagine that this is only going to be a portion
of the systems that I will need to create for this project. I am excited to see how this system will evolve as the project progresses. I am also
planning on releasing this as a solo plugin on the Unreal Engine Marketplace, and as part of a larger plugin that I am working on for Multiplayer
RPGs. My next plugin will be a Quest System, which is another system that I have not worked on before. I am excited to see how that will turn out.