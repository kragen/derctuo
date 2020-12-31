A generic universal entity-component simulatorium
=================================================

What's the minimal core of something like a MOO, but using an
entity-component system?  You need to handle incoming telnet
connections, do some kind of parsing on those connections, have a
player object, and have a room object.  You need some way to define
new verbs, to identify objects in commands, to produce descriptions of
rooms and their contents, and to create new rooms, doors, and other
entities.  And you need some kind of scheduling system for future
scheduled events.  You need to be able to checkpoint the world to disk
and to load such a checkpoint at startup.

Components decompose attributes of entities along hypothetically
orthogonal dimensions, like location, description, door connectivity,
and so on.

It's not immediately apparent that there's a best way to resolve verbs
with multiple possible definitions.  Maybe the best way is to
associate methods with components, and when a verb is invoked on an
object with multiple components, activate all the methods.  For
example, rooms might belong to the description component, but also the
room component, and when you describe a room, it should also list its
contents' names.  But I guess that needs to come in a well-defined
sequence.

But maybe I'm overcomplicating things at first, and I could just make
objects be dicts or something.  Or an edge-labeled graph.  