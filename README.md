# batched-graphql-client

[![npm version](https://badge.fury.io/js/batched-graphql-client.svg)](https://badge.fury.io/js/http-link-dataloader)

## Usage

```ts
import { BatchedGraphQLClient } from 'batched-graphql-client'

const token = 'Auth Token'

const link = new BatchedGraphQLClient({
  uri: `api endpoint`,
  headers: { Authorization: `Bearer ${token}` },
})
```

## Batching

This library uses array-based batching. Querying 2 queries like this creates the following payload:

```graphql
query {
  Item(id: "1") {
    id
    name
    text
  }
}
```

```graphql
query {
  Item(id: "2") {
    id
    name
    text
  }
}
```

Instead of sending 2 separate http requests, it gets combined into one:

```js
;[
  {
    query: `query {
      Item(id: "1") {
        id
        name
        text
      }
    }`,
  },
  {
    query: `query {
      Item(id: "2") {
        id
        name
        text
      }
    }`,
  },
]
```

**Note that the GraphQL Server needs to support the array-based batching!**
(Prisma supports this out of the box)

## Even better batching

A batching that would even be faster is alias-based batching. Instead of creating the array described above, it would generate something like this:

```js
{
  query: `
    query {
      item_1: Item(id: "1") {
        id
        name
        text
      }
      item_2: Item(id: "2") {
        id
        name
        text
      }
    }`
}
```

This requires a lot more logic and resolution magic for aliases, but would be a lot faster than the array based batching as our tests have shown!
Anyone intersted in working on this is more than welcome to do so!
You can either create an issue or just reach out to us in slack and join our #contributors channel.

## Help & Community [![Slack Status](https://slack.graph.cool/badge.svg)](https://slack.graph.cool)

Join our [Slack community](http://slack.graph.cool/) if you run into issues or have questions. We love talking to you!

![](http://i.imgur.com/5RHR6Ku.png)
