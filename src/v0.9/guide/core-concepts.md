title: Core concepts
type: guide
order: 1
version: 0.9
---

Modern web applications have widely varying data needs. Instead of prescribing a
one-size-fits-all solution at the application level, Orbit provides a framework
that can be used to compose a variety of solutions.

## Basic requirements

In order to understand Orbit's architecture, it's helpful to know the
basic requirements that influenced its design.

<img src="/images/concepts/disparate-sources.png" class="medium-pic left-pic" />

### Disparate sources

A number of data sources of varying types and facilities may be required to
build any given web application.

<div class="clearfix"></div>

<img src="/images/concepts/disparate-data.png" class="medium-pic left-pic" />

### Disparate data

Sources of data vary widely in how they internally represent data and the
interfaces they expose to access that data.

<div class="clearfix"></div>

<img src="/images/concepts/common-interfaces.png" class="medium-pic left-pic" />

### Common interfaces

In order to facilitate communication with and between sources, those sources
must be wrapped with a common set of interfaces.

<div class="clearfix"></div>

<img src="/images/concepts/evented-connections.png" class="medium-pic left-pic" />

### Notifications

Sources need a notification system through which changes can be
observed, thereby triggering changes in other sources.

<div class="clearfix"></div>

<img src="/images/concepts/flow-control.png" class="medium-pic left-pic" />

### Flow control

Data flow across sources must be configurable. Flows can be _optimistic_
(successful regardless of their impact) or _pessimistic_ (blocked until
dependent changes have resolved).

<div class="clearfix"></div>

