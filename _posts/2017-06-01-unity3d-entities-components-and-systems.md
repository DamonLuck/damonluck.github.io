---
title: Unity3D - Entities, Components and Systems
header:
  overlay_color: "#333"
tags:
- ECS
- Unity3D
---
{% include base_path %}
Unity3D has GameObjects (Entities), Components and Systems (the underlying game engine).

Following on from my [Data Driven ECS article]({{ "/data-driven-ECS/" | absolute_url }}) the following takes a high level look at Unity3D.

### Entities

In Unity 3D, GameObjects are the closest thing to Entities.

<figure>
	<img src="/assets/img/2017-06-01/GameObjectCreation.gif" alt="">
	<figcaption>GameObject creation using the Hierarchy window. Taken from Unity3D 5.6.0</figcaption>
</figure>

Every GameObject has one transform component, hence one reason for the parent / child relationships shown above. Having the transform on every GameObject enables scenarios such as the following:

If your creating a car with independently rotating wheels you'd setup a parent / child relationship. In this case, the wheel entities will see the rotation applied and the parent entity for acceleration of the entire car.

Not all GameObject are visual, therefore, this transform may at times be redundant.

### Components

Looking at the inspector window for GameObject1 you can see this default transform component. Below this is the add component button.

As an example, adding a sprite render component and box collider component you have a visual entity capable of interacting with other components. A physics component or a custom script would give this entity motion.

<figure>
	<img src="/assets/img/2017-06-01/AddComponentOverview.gif" alt="">
	<figcaption>Add component via the inspector window. Taken from Unity3D 5.6.0</figcaption>
</figure>

### Systems

The execution lifecycle highlights the different systems in play per frame. The [Unity lifetime image](https://forum.unity3d.com/attachments/unity-lifetime-png.47614/) gives a nice overview of what is happening.

Some key parts here are:

|Category | State | Description |
|:--|:--|:--|
|Startup  |       |Initialization and activation |
|Updates| | Update go through one of three states, Fixed, Update and LastUpdate |
|| Fixed update | As shown in the link this is where Physics updates take place. It's the earliest pointer before other updates |
|         | Update | The point at which most game logic updates take place |
|         | LastUpdate | The final point for any last minute changes |
|Render | | Two render phases happen. First, the scene and anything it contains such as sprite. Secondly the canvas is rendered above this. The canvas displays textboxes, buttons etc. |
| | OnWillRenderObject | Renders the scene             |
|        | OnGui | Renders the canvas            |
|EndOfFrame       | | Once render has finished the game loop runs again |

Different components take part at different points of this flow. GameObjects don't have any direct input here. They are purely a grouping of components. Together this grouping becomes importing. For example, the transform component may be updated by the physics component. Then during the render phase the sprite render component will use this transform component to draw the graphic at the correct point.

## Summary

The real power here is the ability to add components and enhance this interaction with other components. Perhaps you'll create a different type of motion component that runs in a different system phase and interacts with other components if they exist in the GameObject. You can also search the GameObject hierarchy as well as looking on the global level using statics.
