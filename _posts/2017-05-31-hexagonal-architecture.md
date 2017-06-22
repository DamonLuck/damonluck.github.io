---
title: Hexagonal architecture
header:
  overlay_color: "#333"
tags:
- Architecture
- Clean code
---
Hexagonal architecture has been a bit of an eye opener for me. I'm used to following the standard N-Tier approach and generally ends up with assembly references beyond their expected boundaries, especially when many developers are working on the same source. With Hexagonal architecture I've been able to keep these boundaries clean and easily spot any attempts to break these boundaries.

## So why's this any different
As mentioned above the key here is project references. You start with a **Core** project where the common domain / business logic lives. If you need something from an external data source you add an interface within **Core** and call this. Therefore, everything is currently scoped within one assembly.

Data provides are known as **Secondary interfaces** and consumers of **Core** are **Primary interface**.

### Key terms Port and Adaptor
These interfaces are defined as the ports. The actual implementation of these interfaces are the adaptors. One port can have many adaptors, such as, a database adaptor, a file store adaptor and a mock data provider adaptor.

#### Keeping things clean (the rules I try to follow)

 - The **Core** project does not reference any other project
 - Any model required by **Core** will be defined within **Core**
 - Any external provider or consumer of core will be defined as an interface in core.
 - Every **adaptor** references the core project
 - No **adaptor** references any other **adaptor**
 - One top level project references **Core** and all **adaptors**. This is generally the build .exe which brings all dependencies together.

### Sounds simple
The following section highlights a few cases where I don't follow these rules

{% capture notice-2 %}
#### Logging

 One area where break things is logging, here I implement a static AmbientLogger and add that to an **Infrastructure** project. Core then references this infrastructure project.

For me, my AmbientLogger is actually hidden behind an ISystemNotification interface. Here I add my interfaces for logging, metrics, alerting, etc. An instance of ISystemNotification is made available via the static class AmbientLogger.

{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

{% capture notice-2 %}
#### Database transactions

Another area where things get tricky are transactions. Particularly when they cross boundaries. Such as a **secondary interface** used by Core which is in turn triggered by a **primary interface**.

Take for example an Oracle Advanced queue as the **primary interface**. Each message from the queue executes an action on Core which in turn executes a DB write with the Oracle data adapter (**secondary interface**).

Passing the transaction through **Core** isn't possible as I'm not allowed to reference any other project in **Core**. The two **adaptors** should know about each other either. Therefore, things need to be wired up in the .exe project in such a way that the active database connection / transaction is shared across the executed code.

The end result should have the **primary interface** initialize and commit or rollback the transaction. This transaction must encapsulate DB calls made by the **secondary interface**.

{% endcapture %}
<div class="notice">{{ notice-2 | markdownify }}</div>

## Summary
Under N-tier architectures I found each boundary exposes it's own set of Dto's or similar models for consumption, these often crossed boundaries they shouldn't due to developers reusing objects. With the Hexagonal architecture the only models to flow externally are those defined in **Core**.

For the rules described above you could write some unit tests to inspect project references. For **Core** you'd check, no  **Adaptors** are references, for  **Adaptors** no other  **Adaptors** would be referenced. Every **Adaptors** would reference  **Core**. Any tricks for cross boundary communication of hooked up in the programs main assembly.
