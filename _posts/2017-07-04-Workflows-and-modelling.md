---
title: Workflows and modelling?
header:
  overlay_color: "#333"
tags:
- Architecture
- ECS
---
Building on from my overviews of Entity Component Systems and Hexagonal Architecture I'll discuss my thoughts on workflow and modelling. Personally I don't want to keep implementing the same business rules over and over again; or changing a labels text to something different because the business wants it. The tools to do this should lie with the business, the development of these tools is the challenge that interests me.

## Entity Component System vs Hexagonal Architecture
If you've read my previous overviews you'll probably agree these two approaches are not the same. They solve problems in different ways.

| Hexagonal  | ECS                 |
|:-:|:-:|
| Single responsibility data   | Multifaceted data    |
| Static data | Dynamic data       |
| Single responsibility systems | Multifaceted systems       |
| Set workflow | Set workflow       |

### Hexagonal Architecture

The table above highlights a very clear difference. The hexagonal architecture or almost replaceable n-tier approach means a developer has spent time building from the ground up the business rules, unit tests, acceptance tests, UI, etc. Often this is done via the SCRUM process so progress is demoed every couple of weeks during sprint end.

The **Set workflow** could be dragged out to a visual tool, but testing is not as complete. So for the purpose of this article I'm assuming **Set workflow** is a hardcoded set of rules.

### Entity Component System

Dynamic data, multifaceted data and systems are points I'd argue of more ECS specific. By definition a system designer should be able to build up an entity of many data components. Systems will be dependent on one or more of those data components and different systems are likely to reuse common data components.

The workflow is an interesting one. In games it's the game loop executed many times per second. But this game loop could be triggered by another event. This idea of a game loop does point to a **Set workflow**. Some flow of execution needs to hang together to ensure the correct systems are executed when required.

### An attempted ECS implementation

My own attempt at an ECS is here, https://github.com/DamonLuck/Utilities. Hidden away in my utilities project is an ASP.NET application that displays leagues, teams, players, fixtures and results. You can simulate match day to update results and leagues.

You'll see how I have a couple systems tied together in a simple hardcoded game loop. This game loop is run each time the simulate button is pressed. As everything is pieced together with components its possible to make a player a league, or a team a player. The systems will support this so long as the entity has the data it requires.

When implementing this I realized the scope of my components continued to shrink. I'm getting the impression my data will be made up of a name component, a games played component, a place of birth component, etc. The more fine grained I make my components the more flexible my designers can be. So long as the systems demand the component's they depend upon it really doesn't matter.

## Summary

My aim with this article was to highlight a clear different between traditional software development and component based software development. I've approached my own ECS challenge from a traditional point of view building up the classes and data structures I required to support my own goal.

Going forwards I'd like to turn this into a tooling first approach. Working out how to compose a game loop workflow from a set of systems, perhaps enabling and disabling these systems, defining components and composing the components in a higher level representation than C# code.
