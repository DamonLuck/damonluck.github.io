---
title: Data driven with an Entity Component System
header:
  overlay_color: "#333"
categories: ECS
---
An Entity Component System (ECS) as I understand it separates data from systems. I going to explore this statement using a real world example I've seen at work over the past year.

## Real world problem
Until recently a lot of systems supported one country and one language. Due to growth additional countries and languages are now required. Everything I've seen / worked on had been written with one country and one language in mind. Therefore, changes had to be made to underlying data structures and consumers of those structures.

These changes had to be made in various places requiring numerous database and application changes. The applications had typical N-Tier structures with a data layer, business layer, etc. So changes were made from the database upwards for each application. Hopefully that gives a rough idea of the handle turning exercise I'm looking to get across.

## Why the interest in ECS
Some years prior to this I was introduced to the idea of ECS in the gaming world. ECS was proposed as a solution to requirements that frequently changed the responsibilities of different data types but fairly consistent systems. I've been trying to get my head around the idea since.

This is unlikely to be a great solution to this problem but it does highlight a different view of the problem.

## Separates data from systems
When I first tried to get my head around this idea it was difficult. The example that really made it clear to me was a visual of keys and locks.

### Entity
<blockquote>
The Entity is the handle of the key. Its useless on it's own but provides the handle by which each entity can be referenced. In software terms this could be the primary key in a database table or the key in a dictionary structure.  
</blockquote>
<figure>
	<img src="{{ '/assets/img/2017-05-30/EntityKey.jpg' | prepend: site.baseurl }}" alt="">
	<figcaption>Entity represented by the handle of a key</figcaption>
</figure>

### Component
<blockquote>
The Component is a cut on the key. The more cuts the more components that exist for that entity. In the dictionary example, one entity key would related to 0 or more component objects.  
</blockquote>
<figure>
	<img src="{{ '/assets/img/2017-05-30/ComponentKeys.jpg' | prepend: site.baseurl }}" alt="">
	<figcaption>Components represented by cuts of a key</figcaption>
</figure>

```
    Dictionary<EntityId, List<Component>>
```

In the database example each additional column would represent a components data. This one shows the actual components data serialised as Json. Not every entity has every component.

| EntityId        | Component1           | Component2           | Component3  |
|:-:|:-:|:-:|:-:|
| 1      | JsonData   | -|-
| 2      | -  |   JsonData    |-
| 3 | JsonData   |    JsonData    |JsonData   


This gives the system its flexibility. Components can be enabled / disabled per entity. In the language example we could add a language column adding the required language data as and when required.

### System
<blockquote>
The system is therefore the lock. For an entity to be processed by a system the key must fit the lock. This actually means the systems minimum requirements must be met.  
</blockquote>
<figure>
	<img src="{{ '/assets/img/2017-05-30/Systems.jpg' | prepend: site.baseurl }}" alt="">
	<figcaption>Systems represented as a locks activated by the correct keys</figcaption>
</figure>

If the system required **Component1** only then **Entity1** and **Entity3** would be valid in the above scenario.

You may now see adding a language component as a requirement to take part in a system means the data can be changed independently of the system. Data that does not match a systems requirements will be filtered out. Once data has been updated to meet the new requirement it will be included in the system.

## Wrapping things up
So with this fairly quick overview you should see how we add a language component to any entity independent of the system supporting that language component. We don't need to work from the data layer upwards and we can quickly enable / disable support of a component at the entity level.

In my next post I'll look a little closer at components and how their individual scope tends to shrink towards very fine responsibilities.
