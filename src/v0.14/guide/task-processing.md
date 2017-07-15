title: Task processing
type: guide
order: 7
version: 0.14
---

Tasks and queues are primitives contained in `@orbit/core`. Although you'll
typically only work with tasks indirectly, understanding how they work can
help you better understand and troubleshoot Orbit applications in general.

## Tasks

Every action performed by sources, from updates to queries, is considered a
"task" to be performed asynchronously.

## Task queues

Tasks are added to queues, which act as FIFO stacks that perform each task
serially.
