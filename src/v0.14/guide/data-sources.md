title: Data sources
type: guide
order: 5
version: 0.14
---

Sources provide access to data. They vary widely in their capabilities: some
support interfaces for updating and/or querying records, while others simply
broadcast changes. Orbit includes a number of "standard" sources: an in-memory
store (`@orbit/store`), a JSON API client (`@orbit/jsonapi`), an IndexedDB
source (`@orbit/indexeddb`), and a LocalStorage source (`@orbit/local-storage`).
Custom sources can also be written to provide access to virtually any source of
data.

## Base class

Every source derives from an abstract base class, `Source`, which has a core
set of capabilities.

Sources must be instantiated with a schema. A schema provides sources with an
understanding of the domain-specific data they manage.

Let's create a simple schema and store:

```javascript
import { Schema } from '@orbit/data';
import Store from '@orbit/store';

// Create a schema
const schema = new Schema({
  models: {
    planet: {
      attributes: {
        name: { type: 'string' },
        classification: { type: 'string' }
      }
    }
  }
});

// Create a store that uses the schema
const store = new Store({ schema });
```

All sources can be mutated, although not all sources support _requests_ to
mutate. Some sources may only reflect mutations that come from elsewhere, such
as a source that wraps server-sent events.

Because all mutations in Orbit are trackable, sources maintain a log of changes,
or "transforms". This log represents an ordered history of transforms that have
been applied to a source. The size of this log can be kept in check by
truncating it after related sources have been synchronized.

Sources are also all "evented", meaning that they can emit events which
listeners can subscribe to. All sources support an event, `transform`, that is
emitted when that source changes. Most sources emit additional events as well
(the specifics depend upon their capabilities).

Let's look at an example of a simple mutation triggered by a call to `update`:

```javascript
// Define a record
const jupiter = {
  type: 'planet',
  id: 'jupiter',
  attributes: {
    name: 'Jupiter',
    classification: 'gas giant'
  }
};

// Observe and log all transforms
store.on('transform', t => {
  console.log('transform', t);
});

// Check the size of the transform log before updates
console.log(`transforms: ${store.transformLog.length}`);

// Update the store with a transform that adds a record
store.update(t => t.addRecord(jupiter))
  .then(() => {
    // Verify that the transform log has grown
    console.log(`transforms: ${store.transformLog.length}`);
  });
```

The following should be logged as a result:

```javascript
"transforms: 0",

"transform", {
  operations: [
    {
      op: "addRecord",
      record: {
        type: "planet",
        id: "jupiter",
        attributes: {
          name: "Jupiter",
          classification: "gas giant"
        }
      }
    }
  ],
  options: undefined,
  id: "05e5d20e-02c9-42c4-a083-99662c647fd1"
},

"transforms: 1"
```

> Want to learn more about updating data? [See the guide](./updating-data.html)

## Standard interfaces

Orbit includes a number of standard interfaces that may be implemented by
sources:

* `Updatable` - Allows sources to be updated via an `update` method that takes
  a transform and returns the updated records that result.

* `Queryable` - Allows sources to be queried via a `query` method that receives
  a query expression and returns a recordset as a result.

* `Pushable` - Allows sources to be updated via a `push` method that takes a
  a transform and returns the results of the change and its side effects as an
  array of transforms.

* `Pullable` - Allows sources to be queried via a `pull` method that takes a
  query expression and returns the results as an array of transforms.

* `Syncable` - Applies a transform or transforms to a source via a `sync`
  method.

### Data flows

The `Updatable`, `Queryable`, `Pushable`, and `Pullable` interfaces all
participate in the "request flow", in which requests are made upstream and data
flows back down.

The `Syncable` interface participates in the "sync flow", in which data flowing
downstream is synchronized with other sources.

> Want to learn more about data flows? [See the guide](./data-flows.html)

### Developer-facing interfaces

Generally speaking, only the `Updatable` and `Queryable` interfaces are designed
to be used directly by developers in most applications. The other interfaces are
used to coordinate data requests and synchronization between sources.

> See guides that cover [querying data](./querying-data.html),
  [updating data](./updating-data.html), and
  [configuring coordination strategies](./coordination.html).

