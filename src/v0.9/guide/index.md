title: Introduction
type: guide
order: 0
version: 0.9
---

## What is Orbit.js?

Orbit is a framework for orchestrating access, transformation, and
synchronization between data sources.

Orbit is written in [Typescript](https://www.typescriptlang.org) and distributed
on [npm](https://www.npmjs.com/org/orbit) as packages containing a variety of
module formats and ES language levels. Orbit is isomorphic - many packages can
be run in modern browsers as well as in the [Node.js](https://nodejs.org/)
runtime.

## Goals

Orbit was primarily designed to support the data needs of ambitious client-side
web applications, including:

* Optimistic and pessimistic UX patterns.

* Pluggable sources that share common interfaces, to allow similar behavior on
  different devices.

* Connection durability by queueing and retrying requests.

* Application durability by persisting all transient state.

* Warm caches of data available immediately on startup.

* Client-first / serverless development.

* Custom request coordination across multiple sources, allowing for priority
  and fallback plans.

* Branching and merging of data caches.

* Deterministic change tracking.

* Undo / redo editing support.

## Basic constraints

In order to meet these ambitious goals, Orbit embraces a set of basic
constraints related to data sources and interactions between them.

<img src="/images/concepts/disparate-sources.png" class="medium-pic right-pic" />

### Disparate sources

Any number of data sources of varying types and faculties may be required to
build any given web application.

<div class="clearfix"></div>

<img src="/images/concepts/disparate-data.png" class="medium-pic right-pic" />

### Disparate data

Sources of data vary widely in how they internally represent data and the
interfaces they expose to access that data.

<div class="clearfix"></div>

<img src="/images/concepts/common-interfaces.png" class="medium-pic right-pic" />

### Compatible interfaces

Communication between sources must happen using a compatible set of interfaces.

<div class="clearfix"></div>

<img src="/images/concepts/normalized-data.png" class="medium-pic right-pic" />

### Normalized data

Data that flows between sources must be normalized to a shared schema.

<div class="clearfix"></div>

<img src="/images/concepts/evented-connections.png" class="medium-pic right-pic" />

### Notifications

Sources need a notification system through which changes can be
observed. Changes in one source must be able to trigger changes in other
sources.

<div class="clearfix"></div>

<img src="/images/concepts/flow-control.png" class="medium-pic right-pic" />

### Flow control

Data flow across sources must be configurable. Flows can be _optimistic_
(successful regardless of their impact) or _pessimistic_ (blocked until
dependent changes have resolved).

<div class="clearfix"></div>

<img src="/images/concepts/change-tracking.png" class="medium-pic right-pic" />

### Change tracking

Mutations, and not just the effects of mutations, must be trackable so that
changes can be logged, diff'd and sync’d across sources, and even reverted if
necessary.

<div class="clearfix"></div>

## Orbit primitives

Orbit's core primitives were developed to align with the goals and
constraints enumerated above.

### Source

Every source of data, from an in-memory store to an IndexedDB database to a
REST server, is represented as a `Source`.

Sources vary widely in their capabilities and may individually support
interfaces that enable updating, querying, etc.

### Schema

All of the models and relationships in a given domain are defined in a shared
`Schema`. Record data is structured to align with this schema.

### Transform

A `Transform` is used to represent mutations to sources. Each transform is
composed of an array of operations. An `Operation` represents a single change to
a record or relationship (e.g. adding a record, updating a field, removing a
relationship, etc.). Transforms must be applied atomically - all operations
succeed or fail together.

### Query

The contents of sources can be interrogated using a `Query`. A query is
composed of a tree of query expressions. A `QueryExpression` can take any
number of arguments, which can be query expressions themselves. A query builder
is provided to improve the ergonomics of composing query expressions.

### Log

A `Log` provide a history of transforms applied to each source.

### Task

Every action performed upon sources, whether an update request or a query, is
modeled as a `Task`. Tasks are queued by sources and performed asynchronously
and serially.

### Bucket

A `Bucket` is used to persist application state, such as queued tasks and
change logs.

### Coordinator

A `Coordinator` provides the declarative "wiring" needed to keep an Orbit
application working smoothly. A coordinator observes a number of sources and
applies coordination strategies to keep them in sync, handle problems, perform
logging, and more. Strategies can be customized to observe only certain events
on specific sources.

## The Orbit Philosophy

The primitives in Orbit were developed to be as composable and interchangeable
as possible. Every source that can be updated understands transforms and
operations. Every source that can be queried understands queries and query
expressions. Every bucket that can persist state supports the same interfaces.

Orbit's primitives allow you to start simple and add complexity gradually
without impacting your working code. Need to support real time sockets or SSE?
Add another source and coordination strategy. Need offline support? Add another
source and coordination strategy. When offline, you can issue the same queries
against your in-memory store as you could against a backend REST server.

Not only does Orbit allow you to incur the cost of complexity gradually, that
cost can be contained. New capabilities can often be added through declarative
upfront "wiring" rather than imperative handlers spread throughout your
codebase.

Although Orbit does not pretend to absorb all the complexity of writing
ambitious data-driven applications, it's hoped that Orbit's composeable and
declarative approach makes it not only attainable but actually enjoyable :)
