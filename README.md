# batched-graphql-request

[![npm version](https://badge.fury.io/js/batched-graphql-request.svg)](https://badge.fury.io/js/http-link-dataloader)

## Features

- Most simple and lightweight GraphQL client
- Includes batching and caching based dataloader
- Promise-based API (works with async / await)
- Typescript support (Flow coming soon)

## Idea

The idea of this library is to provide query batching and caching for Node.js backends on a per-request basis. That means, per http request to your Node.js backend, you create a new instance of BatchedGraphQLClient which has its own cache and batching. Sharing a BatchedGraphQLClient instance across requests against your webserver is not recommended as that would result in Memory Leaks with the Cache growing infinitely. The batching and caching is based on dataloader

## Install

```
npm install batched-graphql-request
```

## Examples

### Creating a new Client per request

In this example, we proxy requests that come in the form of batched array. Instead of sending each request individually to my-endpoint, all requests are again batched together and send grouped to the underlying endpoint, which increases the performance dramatically.

```ts
import { BatchedGraphQLClient } from 'batched-graphql-request'
import * as express from 'express'
import * as bodyParser from 'body-parser'
 
const app = express()
 
/*
This accepts POST requests to /graphql of this form:
[
  {query: "...", variables: {}},
  {query: "...", variables: {}},
  {query: "...", variables: {}}
]
 */
 
app.use(
  '/graphql',
  bodyParser.json(),
  async (req, res) => {
    const client = new BatchedGraphQLClient('my-endpoint', {
      headers: {
        Authorization: 'Bearer my-jwt-token',
      },
    })
    
    const requests = Array.isArray(req.body) ? req.body : [req.body]
    
    const results = await Promise.all(requests.map(({query, variables}) => client.request(query, variables)))
    
    res.json(results)
  }
)
 
app.listen(3000, () =>
  console.log('Server running.'),
)
```

To learn more about the usage, please check out [graphql-request](https://github.com/prisma/graphql-request)