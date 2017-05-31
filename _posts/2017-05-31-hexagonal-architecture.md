---
title: Hexagonal architecture
header:
  overlay_color: "#333"
categories: Architecture
---
Under the N-Tier approach something always ends up leaking beyond their expected boundaries, especially when many developers are working on the same source. With Hexagonal architecture I've been able to keep these boundaries clean and easily spot any attempts to break these boundaries, which is often the case.

## So why's this any different
As mentioned above the key here is project references. You start with a **Core** project where the common domain / business logic lives. If you need something from an external data source you add an interface to **Core** and call this. These data source interfaces are called **Secondary interfaces**. Anything exposed from the **Core** project itself is done so via a **Primary interface**.

### Key terms Port and Adaptor
These interfaces are defined as the ports. The implemented interfaces are the adaptors. One port can have many adaptors, for example a database adaptor and a mock data provider adaptor.

{% capture notice-2 %}
#### Keeping things clean

 - The **Core** project does not reference any other project
 - Any model required by **Core** will be defined within **Core**
 - Every **adaptor** references the core project
 - No **adaptor** references any other **adaptor**
 - One top level project references **Core** and all **adaptors**. This is generally the build .exe and brings all dependencies together.
{% endcapture %}

### Sounds simple
After a bit of practice it becomes second nature and I don't always follow these points.

{% capture notice-2 %}
#### Logging
 One area where break things is logging, here I implement a static AmbientLogger and add that to an **Infrastructure** project. Core then references this infrastructure project.

The AmbientLogger is actually hidden behind an ISystemNotification interface where I add intefaces to logging, metrics, etc. An instance of ISystemNotification is made available via the static class.
{% endcapture %}

{% capture notice-2 %}
#### Database transactions

Another area where things get tricky are transactions. Here I have a **secondary interface** used by Core which is in turn triggered by a **primary interface**.

Let's make things really difficult and say I've got an Oracle Advanced queue as the **primary interface**. Each message from the queue executes an action on Core which in turn executes a DB write with the Oracle data adapter (**secondary interface**).

Passing the transaction through **Core** isn't possible as I'm not allowed to reference any other project in **Core**. The two **adaptors** should know about each other either. Therefore, things need to be wired up in the .exe project in such a way that the active database connection / transaction is shared across the executed code.

The end result should have the **primary interface** initialise and commit or rollback the transaction. This transaction must encapsulate DB calls made by the **secondary interface**.
{% endcapture %}

## Has this created a cleaner architecture?
In my own experience it has. I've been able to call out developers questionable changes by looking for changes breaking the points listed above.

Under N-tier architectures I found each boundary exposes it's own set of Dto's or similar models for consumption, these often crossed boundaries they shouldn't due to developers reusing objects. With the Hexagonal architecture the only models to flow externally are those defined in **Core**.

You could write some unit tests to inspect project references. For **Core** you'd check, no other project reference appears without and for the adaptors **Adaptors**, they should only reference **Core**. There may be exceptions, but I've not yet run into them.
