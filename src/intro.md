# KIProtect - Introduction

KIProtect is a solution for protecting sensitive information in data streams.
It can read data from a variety of input sources and apply transformations
to this data to protect it, e.g. using pseudonymization, anonymization or
encryption. The protected data can then be forwarded to a variety of output
sinks, such as databases or message queues.

The [data model documentation](data-model.md) describes the entities that
KIProtect uses to model data streams and transformations.

## Basic Concepts

To use KIProtect, the following steps are required:

* Defining input data sources from which KIProtect can read.
* Defining output data sinks to which KIProtect can write.
* Defining actions, streams and stream configs to transform data that
  is read from inputs and written to outputs.

KIProtect automatically takes care of managing transformation parameters,
error handling, input and output buffering and data processing for the user.
The user should be able to easily see which streams are currently defined,
how much data they process and whether there are any problems in the data
processing.

## Transforming Data

KIProtect provides a wide range of data transformation and detection mechanisms.
All of these mechanism should be easily configurable via the frontend, making
it easy to test them on example data and see results of the transformations
in real-time.

## Managing Users

KIProtect supports role-based access management using an organization-based
role concept. It should be easy to configure access to resources via the
frontend.
