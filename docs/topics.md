## Topics

A topic is a named stream of events, typically representative of a feed or a category of information.
Each topic is a unit of storage where Pyroclast initially receives your data. When your data enters
a topic, it is durably stored and replicated for a specified amount of time. Topics have been designed
to have several characteristics that make the highly suitable to event stream processing. We'll go through
each of these in turn.

### Immutability

Once an event be written to a topic, it cannot be deleted unless the entire topic is deleted. This is called
immutability. The topic can only be modified by appending more events to its tail. An event represents a fact
of information about something that occurred. Facts are true as of a particular point in time, and cannot be modified
or erased from history. This mode of thinking enables rich analysis of historical information, and maximizes
knowledge about the present by retaining as much data about the past as possible.

### Retention

Events are stored in a topic for a specified duration - both in terms of number of gigabytes and number of days.
While the events in a topic are immutable, they will be deleted when they go beyond their retention policy. This
is a matter of practicability. Information about the past is typically only useful within a certain bound of time.
For example, if the retention policy for a topic is to store events for no more than 10 days or 50 gigabytes, events
that are either more than 10 days old, *or* events that are not within the most recent 50 gigabytes received will
be deleted.

Once a topic has been created and its retention policy specified, it cannot be modified.

Retention policies are not hard guarantees. Events may be available for processing even if they are beyond their
retention limits. This behavior should not be relied upon.

### Ingesting

Events are sent to a particular topic via push-based tooling. Pyroclast currently offers an HTTP based method of
writing data to a topic, or using Plumes. See the guide on Ingesting events for more information.

### Sharability

Topics are different from traditional databases in that sharing their contents to different consumers is completely safe.

When considering which topic an event should be written to, it might be tempting to write it to multiple topics and
to design a service per topic to each perform a different task. This is an anti-pattern that is discouraged in Pyroclast.
Instead, write each event to exactly one topic and design several services that each consume from the same topic.
Since topics are immutable, there is no penalty to sharing topics across as many services as you'd like. Fewer topics
also means fewer sources of data to manage.

### Replay

The central idea behind Pyroclast is that you author services which read events from a topic and materialize a view
of those events, which is consumed from an HTTP API. Since topics are fully immutable, and thus can be shared, topics
may also be *replayed* by a service. We say that a service *replays* a topic when that service is started, reads same
data from the topic, is stopped, and then started again. When you restart a service, you can specify where
to in the topic events should start to be read from. You can either *resume* from the point in the topic where the
service previously left off, or you can *replay* the topic from the beginning (or any arbitrary point time, for that matter).

This ability is incredibly useful for repairing services that had a bug, or a change in requirements. A brand new view
of events can be materialized without disrupting the contents of the topic.

### Schema

Events that are written to a topic ought to follow a particular schema. Currently, Pyroclast only supports events ingested
in the form of JSON maps. We will extend this in the future to cover more formats.

Pyroclast currently does not support a mechanism to enforce a user defined schema to block malformed data from being written
to a topic. This will be added in the near future, but for the moment it is up to the producers of events to ensure that
the contents of their payloads conforms to their needs.

### Chaining

Just as you can write information to a topic using HTTP or Plumes, services which read information from a topic can transform
events and write them back to a topic. Such types of services are loosely named "data pipelines". They're given their name
because they route events through a sequence of operations and out the other end to a new topic, similar to water flowing
through a pipe. This is most typically used by having one main topic for ingestion, and several other "derived" topics which
contain events that have been transformed from the initial topic. This encourages you to write small, focused services, and thus
gain reusability.

It is possible to create a service that reads from a topic and writes back to the same topic, creating a cycle.