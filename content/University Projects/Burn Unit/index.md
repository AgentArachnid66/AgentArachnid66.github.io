+++
title = 'Burn Unit'
date = 2024-11-14T11:46:29+01:00
+++

This was my final year project, born out of my passion for a concept called games based learning and what I learned to call Serious Games. These are games that aim to teach the player something, a skill or piece of knowledge for example. In my case, initially I wanted to create a game solely dedicated to teaching the player how to perform first aid, at least to the degree that they'd be useful enough in an actual emergency situation until proper medical attention can be given. I reduced the scope of the project down from a full and comprehensive first aid course to a single aspect, burns. This choice was made as it is one of the most common injuries, has multiple degrees of injury which are quite distinct from one another and there are a few misconceptions about how to treat them. 

This project aimed to create a serious game that utilises the concepts of game-based learning and applies them to potentially train players in practical skills. The focus of this paper will be placed on treating burns and evaluating how effective a serious game is at training players in the sequence of steps required. This was done to see the effectiveness of potentially supplementing traditional first aid training with a serious game designed to safely replicate a first aid inspired scenario in a game state. The previous explorations into this subject have produced positive results which are replicated in this project, though not to the same degree as other projects. Using a custom data collection system and subsets of multiple standard questionnaires, the product created was evaluated for its usability and effectiveness as a learning tool. 

Serious Games are games that are designed or used for a purpose other than entertainment such as military, health care, etc, where Game-Based Learning is a subset of serious games which has defined learning outcomes and only focuses on education. Both have many applications, from the military contracting Atari to create The Bradley Trainer to research with Foldit, which has been used to effectively teach biochemistry.

As evidenced, serious games can be used to great effect to teach health-related subjects, which includes the topic of this project, First Aid. Applying First Aid could have prevented 59% of deaths caused by injuries. Nearly 7000 children per year are admitted to hospital for burns with the most common cause coming in contact with hot drinks, food, fats and cooking oils. Supported by the fact that only 1 in 20 adults are confident in their ability to perform first aid, it is clear that something needs to change.

# Design
The aims of the project are as follows:
- Gain a greater understanding of Serious Games and how they can be applied
- Create a Serious Game that utilises this understanding and applies it to first aid training with a focus on treating burns
- Evaluate and potentially validate the product to establish if there is a statistically significant advantage to using it

The main objectives of the project can be summarised as follows. By investigating Serious Games in greater depth, the creation of the project can be aided by building an understanding of the existing theories and frameworks. This can be further aided by examining applications to find design choices that can be integrated. The product will then take these concepts and apply them to the relevant context to create a serious game prototype. To evaluate the product, a user study will be performed and the data gathered will be analysed. From the results produced, the product will be evaluated on its effectiveness as a serious game and as an application of game-based learning.

# 1 Literature Review
## 1.1 Serious Games
First, an exploration into previous applications to gain a more detailed background into the history, and context for similarly using serious games to what this project aims to accomplish. 2 examples
stand out due to their design choices, implementation and intended learning outcomes. Firstly, Underground was developed with the Nintendo WiiU to be a more engaging way, compared to
traditional methods, to teach Laparoscopic motor skills to keyhole surgeons. It is a validated tool to achieve this result. Re-Mission, on the other hand, was developed to
improve cancer-related knowledge in adolescents, using a commercial game aesthetic to appeal to younger generations, with the underlying learning outcomes integrated into the gameplay. It led to
a statistically significant improvement in knowledge over the course of 3 months of regular exposure compared to the control group. This improvement was small, but it supports a relationship between
gameplay and knowledge gained from the mechanics. How these examples relate to first aid is simple. They both are attempting and, to varying degrees, succeeding to get people to retain useful information and skills through the use of gameplay, modalities and even the narrative. All of these elements combine to keep the player engaged long enough to transfer this knowledge into long term memory and apply it to real-world problems.

## 1.2 Use Cases In Health Care
A more direct example of serious games in first aid comes from Boada et al, who designed a game to train people how to react in a choking emergency. It was discovered that, through the use
of several focused minigames to compose the main gameplay, ”all the indicators of the knowledge about how to act in case of a choking emergency were improved through this serious game”. This
is important because it further supports the main aim of this project, due to the healthcare nature of both games.
In a review of Education Game Design Literature, Dondlinger discussed effective game design elements. These included Narrative Context, Motivation, Goals And Rules and finally Interactivity and Multisensory Cues. Narrative Context appears to be one of the most significant factors in engaging the player in learning, but the outcomes must be coupled with the narrative context. While there is no consensus about the source of motivation, it is widely accepted that it is an important aspect of the design and that both extrinsic and intrinsic sources are to be included. Extrinsic sources pull us towards a goal using external rewards and punishments whereas intrinsic sources push us from within because performing the activity by itself is rewarding. Caution should  be taken to not give an extrinsic reward for something the player is intrinsically motivated to do, as the reward could overwrite the intrinsic drive to participate in the activity and when the reward is removed, they no longer want to participate which would be against the aim of engaging the participant in the activity. Rules lead to the completion of the Goal through the player’s understanding of how to navigate the game and how to accomplish the goal set out for them. These goals have different levels of difficulty aiming to motivate the player to continue playing over different time scales.

Interactivity is the main advantage games have over other mediums and should be utilised to greater effect with the use of feedback with multisensory cues. In an evaluation of ScienceSpace, Salzmann et al wrote ”Multisensory cues can engage learners, direct their attention to important behaviours and relationships, help them understand new sensory perspectives, prevent errors through feedback cues, and enhance ease of use.”. Fisch discussed how the use of feedback is important as not only can it encourage players to continue playing, but it is crucial to motivate players to carry on after making an error, by supporting them using supplementary information and explaining the source of the error. This does not need to be in the form of instructional text but can instead be integrated into the gameplay.

## 1.3 Learning Mechanics - Game Mechanics Model
Arnab et al describe a method to analyse the mapping of the learning outcomes into the mechanics with the Learning Mechanics-Game Mechanics model. Although they clarify it is not formulaic, it is a toolset that will be utilised in this project. Using this framework to map learning outcomes to mechanics has been done before with subjects such as engineering which leads to its use in mapping learning outcomes for first aid to game mechanics.


## 1.4 EGDA
EGDA was first proposed by Torrente as a protocol to design and develop serious games for healthcare. This means it can provide a foundation on which to develop around first aid becausethey have very similar learning outcomes. However, there are differences in the use cases and the methods that end up being used will be explained within the context of these limitations. It will be used in this project due to the model having proven usability with at least 7 games being developed with it. It was built with four main principles in mind:
1. Procedure-Centric Approach
 - The healthcare protocol(s) you are trying to train should take central focus in all aspects of the design
2. Collaboration between Experts
 - Domain experts (Game Designers and Healthcare professionals) should work together
3. Agile Development with Authoring Tools
 - Adaptive development techniques to maximise value and minimise cost
4. Low-Cost Game Model
 - Reiterating minimising investment required by limiting the complexity of the product.

EGDA proposes four tasks that are built up by the principles ascertained above, which are to be used as a reproducible method for other researchers. These tasks are split up into 12 subtasks:
1. Analysis
    - (a) Describing the Environment and Settings
    - (b) Capturing and Formalizing the Sequence of Steps
2. Game Design
    - (a) Designing the Virtual World
    - (b) From Procedure to Game Script
    - (c) Decision-Making Support
    - (d) Feedback
    - (e) Gamification of the Design
    - (f) Complexity Balance
3. Implementation
    - (a) Rapid Prototyping
    - (b) Gathering the Art Resources
4. Quality Assurance

## 1.5 Game Design
### 1.5.1 Core Dynamic
A core dynamic is the base idea for what makes the game fun. Brathwaite and Schreiber describe 10 core dynamic archetypes:
1. Territorial Acquisition
 - Control of a Zero-Sum Situation, such as a piece of territory
2. Prediction
 - Making decisions based on the potential outcomes
3. Spatial Reasoning
 - Understanding and Manipulating 3D space and objects it holds
4. Survival
 - Existing in the face of threats
5. Destruction
 - Eliminating targets
6. Building
 - Development of Resources
7. Collection
 - Gathering of Resources
8. Chasing or Evading
 - Contention of Space
9. Trading
 - Exchange of Resources
10. Race To The End
 - First to Win State

Each one provides a substantial basis to build up from, with most games putting at least one
of these archetypes into their central design.

### 1.5.2 MDA
MDA is a framework for designing games, using three components (Mechanics, Dynamics and Aesthetics) and was first proposed by Hunicke et al in 2004. It is useful to look at a game from the perspective of both a designer from looking at Mechanics on-wards, and from the perspective of a player from looking at Aesthetics first. It will be used here to provide a framework to design the game, following the EGDA model. 

Mechanics are the algorithms that run the components of the game and the data they affect. Dynamics relate to the behaviour of those algorithms at run-time in response to both user input and other dynamics. Aesthetics are the emotional responses elicited from the player by interacting with the game. As dynamics drive the aesthetics and therefore the emotional response of the player, it is important to make sure that they are well defined and support the desired aesthetic. It has been used in a similar context, to improve engagement of gamified systems. By focusing on the dynamics of the system, they provide evidence that dynamics can positively influence the player’s emotions to engage them in a gamified activity. Buttfield-Addison et al goes on to describe how they have used the framework to plan for game jams and assign roles. Game jams have high pace and time-restricted developmental windows, so efficiency is key. They propose splitting up the team to focus on separate portions of the game but go on to say that this may lead to a loss of flexibility. However,this project will only have one developer so that limitation is not applicable.

## 1.6 Telemetry Data
Multiple types of data can be collected for differing reasons and contexts. Mellon describes 3 basic types which are Player Behaviour, Engine Performance and Pipeline Throughput. Player behaviour can be further broken down using a framework put forward by Drachen et al, into Customer, Community and Gameplay Metrics, with the latter being broken downeven further into In-Game, Interface and System Metrics. However, there are simpler frameworks. Drachen also put forward a model called minimum user behaviour telemetry:
1. General Attributes
2. Mechanics
3. Business

The natural next step is collecting qualitative and actually using that data to evaluate.

# 2 Methodology
## 2.1 EGDA Analysis
To begin using the EGDA model, the environment was the starting point. This refers to the environment we are attempting to recreate and transform into a game appropriate level. In this instance, this would be a first aid scenario with a focus on burnt skin. Following this, the next step was to determine what steps must be taken to treat burns and how important they are in relation to one another. To do this, a wide range of sources were found and codified using NVIVO to find the most common steps, which were the ones that were taken further in this project.

The most important steps are:
1. Cool and Running water to reduce damage
2. Removal of restrictive clothing due to swelling
3. Dress the wound
4. Do not apply anything but cool clean fluid
5. Do not remove clothing stuck to the burn
6. Do not excessively prolong or immerse the cooling process. 

Some of the positive and negative can be combined and they can be reorganised to form a hierarchy. Due to the time constraints, not all of these features would be likely to be added. The most important learning outcome, as seen in Figure 4, is that players understand that cooling running water needs to be applied to the burnt area and nothing else. Therefore this will be the first mechanic implemented, followed by the removal of restrictive clothing and so on.

1. Cool, Running Water and nothing else will be applied to the burns
2. Remove Restrictive Clothing except if stuck to burn
3. Dress the wound
4. Do not excessively prolong or immerse the cooling process


## 2.2 Design
### 2.2.1 Engine
The original EGDA model was designed around the traditional 2D point and click games, with the reasons given as it reduces development costs, facilitates deployment and use, allows players  to visually connect to their workplace and finally they are easier to approach by beginners. A 3D first-person style game has been chosen for this project because Unity and other free tools such as Blender and Visual Studio will be used to keep iterations and cost down and the fact that this project will not be deployed to end-users means that this style is viable. To counter the final two points, the participants used for the evaluation have some previous experience with games and as discussed above a recreation of a first aid scenario could be detrimental. Unity was chosen due to it being free for use as well as being widely used so the community is comparably bigger than other game engines. Github was used as the main source of version control due to its ease of use and integration with the chosen engine. Both of these tools made it easier to follow the agile development model to iteratively develop.

### 2.2.2 Gameplay
As described in the literature review, it is important to select a core dynamic to centre the game around and this dynamic must in itself support the learning mechanics. Survival and Prediction
were selected to form the gameplay loop because of their relevancy and being the most applicable to a first aid orientated game. Altering the nature of the Survival dynamic was applied to further
support this, by making the player a resource to be used by another object instead of the player looking for and using resources. After the core dynamics were selected, designing the MDA breakdown was the next step to have a guide on the implementation.

### 2.2.3 Controls
A gamepad was chosen as the primary form of input, because of its versatility, widespread use and ease of use. Industry standards and player expectations for the controls were considered in
designing and developing the control scheme itself. The movement was controlled by the left analogue stick, allowing for translation along the XZ Plane and the left and right shoulder buttons for vertical translation. Orientation was controlled by the right analogue stick. These were chosen due to most console games having movement bound to the left analogue stick and camera controlled by the right, so would be potentially disorientating for experienced players if these were bound to different inputs. The player can select different mechanics to be used via a radial menu bound to the left trigger.
These mechanics are associated and designed around different learning outcomes and ordered in the menu by the order of application, in an attempt to reinforce the learning outcomes further. To
use the mechanics, the primary use is activated by the right trigger button. Some have additional functionality, which is bound to varying inputs on the controller that have no other functions.

### 2.2.4 Level Design
For the game to be as immersive as possible given the constraints and the core dynamic as well as for it to be as engaging as possible, an environment that is coherent with the end goal is required.
As recreating an actual first aid scenario would be both potentially distressing to play through as well as notoriously difficult to accomplish, an alternative was selected. The main idea of the level
is a living ship, to represent a patient in the player’s care. Tying in with the core dynamic, the player will be charged with taking care of the ship and be responsible for its survival. To do this,
the player avatar will be a repair drone so they are equipped appropriately. This would add to the benefit of the ship being able to direct the player in what they need to do, to provide support to
the player’s decision making, in the form of feedback. As the drone is a prevalent part of the game, it was also important to try to encourage its full usage through the environment by making the level both easy and engaging to navigate. The goal of the level must be visually clear and the path the player takes would be important in making sure they understand the controls and the mechanics. Figure 7 is a screenshot of the area the player starts in, with the enclosed space providing an area to practise the basic controls.

The only doorway in the area in leads to a stairway that forces a collision with an object. This area, shown in, serves the purpose of making sure the player knows how to orient the drone to control its translation direction, as well as introducing them to the pickups that populate the level. This leads the player into the next area without needing to explicitly tell them.

The hallway leads to the main level. There is plenty of space to fly the drone around, with the walls being at least visually clear. There was an attempt to create the environment in line with the original vision of a living ship, with organic matter over the walls and floors of the level and a wooden aesthetic.

### 2.2.5 Feedback
The feedback can be given in two main ways, Immediate and Delayed. Immediate is where games have an advantage over main forms of education as they can respond almost instantly to player actions and direct them in their choices without displaying a single word of text to say this. Delayed feedback came in the form of a score at the end of the session and can be a way to encourage repeating sessions to reinforce the learning outcomes. In this project, both forms were used with the immediate feedback being designed as Visual and Audio feedback to the player and delayed was a score calculated by taking into account the player’s actions throughout the session.

## 2.3 Implementation
Using the Agile development model, it was important to iterate through a cycle of design, implementation and testing of components. Building up from the previous cycle to create the game. The most basic foundation, to begin with, was the controls, then the gameplay itself and finally the User Interface.

### 2.3.1 Controls
Using unity’s new input system, the controls were implemented to be reactive to input instead of constantly checking for input. This made it possible to develop a more advanced control scheme as demonstrated in Figure 10. It began with the basic movement controls and was built up to the control scheme seen in. To gain finer control over the controller axis inputs, such as the movement, each one was evaluated by an Animation Curve which would create a smoother experience. These were mostly stored and accessed on the player script. This was done so any class that may need to listen to specific events can easily be subscribed to those events. This static reference would have to be reloaded and reset in each scene shift, even if the same scene was being reloaded, due to the variable containing a reference to the previous
scene which would no longer exist. This process was followed for the entire control scheme, with the curve evaluation technique being applied when appropriate. For the shooting controls, the value of the axis was passed through to control the pressure value on the player.

### 2.3.2 Walls and Water
To optimise the performance, an object pool was implemented for the water globules. What this meant was that at the beginning of the game, a set number of water globules would be instantiated and instantly deactivated in the scene but not destroyed. If one was required, then a request to the pool would be made and if there was a globule in the scene that was inactive then a reference to it would be returned and used. Since the fire rate of the player was dependent on their pressure and distance away from the wall, it was impossible to guarantee that at least one globule would be in the object pool at all times. To combat this, the pool would instantiate new objects during runtime and return those objects into the pool after they were deactivated. To ensure that the water would deactivate, a timer would be started as soon as they are drawn from the pool and would return them to it.

To give the water a more realistic look and not break player immersion by having a perfect sphere act like water, a simple displacement shader was implemented with Unity’s Universal Render Pipeline and Shader Graph. It uses the current position of the globule as an offset that drives Perlin noise. This is then added back to the original normals of the object to provide a simple displacement shader that is independent of time but rather dependent on the position of the object. This property is set for every frame to provide the appropriate value and a smooth transition between frames. A master mask was used to optimally relay information from the gameplay to the shader. Each channel (RGBA) is used to describe the initial state of the wall as described below, the temperature and pressure of the water globules interacting with the wall and where the player hit respectively. This was then used by the shader to dynamically change the appearance of the wall to demonstrate to the player the consequences of each globule. To write to this mask, a water globule would subtract the unit vector equivalent of its current velocity from its transform position, resulting in a position vector behind itself and pointed in its trajectory. Then extend a ray, using this position as its origin, and its velocity as its direction. This was done to ensure that if the globule jumped through the wall, so its origin was on the other side of the wall, it would still result in a hit of the appropriate area. The ray masked out all physics layers except for the one where the walls lay, to ensure that if it hit anything it would have to be a wall. Using the ray cast, it could then retrieve a UV location on the mesh which was used as an input for a draw shader. This simply took a UV location and performed a distance check given a radius and this result was then transferred to the render texture.

To have greater control over the mask, each colour channel in the drawing material was multiplied by its corresponding value after that value had been evaluated by a function defined using an animation curve. This meant that each input could be changed independently from one another and more crucially values could be negative. It was designed so that the lower values are not harmful, but aren’t as optimal as just below the midpoint. However, the steep decline into the negative values was meant to show that too high pressure would be detrimental to the goal.

This was important because it allows the shader to both improve and reduce the degree of burns on the wall with just one mask. This also meant greater control over what values were to be referred to as good and bad to ensure the player was learning the appropriate values. Each wall had its unique master mask, and to optimise this instead of generating a new instance for each wall, which would have been costly on memory, or sharing a material between instances, which would have meant walls that were never hit would gain the appearance of those that have, the approach taken was to use Material Property Blocks(?). Property Blocks allow for specific values to be set on a material on an instance of an object, without creating a new instance of that material or object and, more crucially, lets individual instances have different values for the same property. The process of carrying this out was simple. Create a new Render Texture at the start of the game on each instance of the wall, then get the current Property block for the instance’s material, set the right Property on the right material and set the block back. To encourage players to play through repeatedly, the challenges of each session are unique by making the wall procedural for the severity and location of the burns. A random value between 0 and 12 was generated, then a modulo operation to find the remainder between 0 and 3 then normalised between 0 and 1. 0.25 was then added to this value to bias it towards a higher value and therefore a higher degree of burn.

To have runtime control over this value, the master mask was split with the red and green channels taken to drive the texture. Using one minus these value so that they would be 0 if the
player never interacts with those areas and higher if they do, then multiplying by their respective weights, the sum of the values were multiplied with the Alpha channel and then added to the
original randomly generated value as seen in Figure 16. To use this value to drive the texture of each wall, it was being fed into a series of comparisons to determine its value as seen in, with multiple instances each handling the PBR values for the output material.

The textures for the walls were generated using two pieces of software, with the first-degree burn and normal skin materials created using Substance Painter. However, second and third-degree burnt skin materials were procedurally generated using Blender. On the left side are the noise textures that drive the material, and the connections between maths and colour ramp until the final output shader node

