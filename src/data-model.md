# KIProtect Data Model

We model the flow of information through KIProtect with five simple models:
**Input, Output, Stream, Config, Action**. These models are responsible
for the following things:

* **Input**: An input source from which we read data, e.g. a database
  or message queue.
* **Output**: An output destination to which we send data, again e.g. a database or
  message queue.
* **Stream**: A flow of data with a specific **input** and a number of **configs**.
* **Config**: Contains set of **actions** that are applied to input items from
  a **stream**. The resulting items are sent to one or several **outputs**.  
* **Item**: An item is a key-value dictionary describing a single package of
  data that runs through the stream processing.
* **Action**: A specific action applied to an item.

## Inputs

An input describes a system from which we read data. It can e.g. be a database,
message queue or a structured file such as a JSON or CSV document. An input
can be connected to multiple streams. When a reader receives items from an input,
it distributes them to all connected streams.

## Outputs

Similar to an input, an output describes a system to which we write transformed
items. An output can be connected to multiple stream configs. Writers write
outgoing items from the configs to the output.

### Data Schemas

Optionally, inputs and outputs can define data schemas, which describe their
field types. Schemas are useful to e.g. automatically define transformations
based on field types or to validate incoming or outgoing data against the
schema, validating the data as it is processed.

## Streams

A stream receives items from one or several inputs and sends them to a number
of different configs for processing. Stream configs can be either active,
disabled or in testing mode. When testing stream configs, items are processed
by the config but the results of the processing are discarded, allowing users
to easily test and debug stream configs without risking data corruption.

## Configs

A stream config describes a specific set of actions that should be applied to
items from the stream. A config contains a numbered list of actions with
specific config parameters, that are applied in sequence to every input item
received by the config.

## Actions

An action describes a specific operation that should be performed on an item
from a given stream config. An action can e.g. be a transformation such as an
aggregation, pseudonymization or encryption of an incoming item field.

Actions are defined independently of stream configs. A given action can be
included in multiple stream configs. It defines a set of action parameters as
well as config parameters that together describe the effect of the action on an
input item from a specific config. The action parameters contain e.g.
encryption keys or data types, while the config parameters describe e.g. to
which specific fields of an item the action should be applied. This is required
to use e.g. the same pseudonymization action on items from different configs
where the field to be pseudonymized is stored under different keys.

## Access Management

KIProtect manages access to resources like streams or actions by using a
role-based access management model. A user can have multiple organization
roles that can be freely defined by an organization administrator,
such as e.g. "stream admin" or "masterchief". Objects define fixed roles such
as "admin" or "viewer" that define what someone who has this object role can
do with/to that object. Organization roles can be mapped to object roles
by an administrator, granting e.g. all users with the "masterchief" role
the "admin" role on a given stream object. This concept makes it easy to
define fine-grained access controls for objects. Some objects are dependent
on other objects. For example, a config is always associated with a stream.
Therefore, configs do not have their own role mappings but instead inherit
them from their stream objects.
