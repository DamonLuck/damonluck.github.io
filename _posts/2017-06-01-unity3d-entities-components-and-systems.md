---
title: Unity3D - Entities, Components and Systems
header:
  overlay_color: "#333"
tags:
- ECS
- Unity3D
---
{% include base_path %}
The following gives a very high-level flyby of Unity3D as an ECS system. From what I understand a new ECS system will be introduced in the future, particularly to support multiple cores via an approach they've called Jobs. For now, Unity3D has GameObjects (which I believe relate closely to Entities), Components and Systems (the underlying game engine, physics engine, etc).

### Real life use

I recently attended the Unite conference in Amsterdam https://unite.unity.com/2017/europe to find out more about this system. I'm not a game developer and my interest was primarily in understanding the business value offered by this tool. Most of the content was game focused but there were a few business applications; **Fugro Roames** 3D world being particularly impressive.

Another product I particularly liked was **Yousician**. The company behind this had implemented the business value in their own assemblies and built the UX experience in Unity3D.   

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

The power provided by the Unity3D tools allows a team of developers, game designers, artists and more to work together build a product. Developers can design and develop components, game designers can piece together these components and workflows describing real world interactions, artists can build the graphics and animations to bring the world alive.

The editor provides a number of tools but more importantly its extensible and there are thousands of additional tools available for download. This provides more than just software developers with the ability to turn ideas into working software. 
