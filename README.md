C.R.E.A.M.
=====

*Cache Rules Everything Around Me* -- A modern caching service for JSON APIs based on Redis.

**Notes**

This is currently vaporware and exists to organize my thoughts and plans. Work has yet to formally begin. 

For this project I'm using a development strategy I (for better, worse, or ignorance) am calling _Documentation Driven Development_, the documentation drives tests; which, in turn drives code.

# Why

**Background**

The current standard for caching (RoR) HTTP applications due to speed, simplicity, and ubiquity is Memcached. It's battle tested, common, and generally works amazingly. I (we) love us some Memcached. 

**But...**

It's (arguably) biggest advantage _simplicity_, is one of it's biggest shortcomings-- it is only a key-value store. A key and it's value have no knowledge, or relationship, of eachother outside of a 'lookup.' A common example would be where a collection (i.e. search results) of items is cached, when an item in that cache is changed, there is no mechanism to expire that specific collection cache. The collection cache is stale, and you can do nothing but _wait_ (using Memcache's timeout feature).

**Enter Redis**

Until "recently" an alternative that provided the simplicity, speed, or ubiquity didn't exist. Redis however has rather changed the game (I believe) by remaining a simple key-value store but with a few amazing extra features: sets, sorted sets, and hashes.

# What

Cream is an HTTP caching service, backed by Redis, for caching (reading/writing) JSON data. 

It has two basic constructs an _item_ and _collection_. An item is an _identifiable_ resource, and a collection is composed of _identifiable resources_. 

# How

## Item

**Write** `POST /resources/:name/:identifier`

**Read** `GET  /resources/:name/:identifier`

Ex.

`POST /resources/item/posts/id`


```json
{
  "id": 1,
  "body": "stuff"
}
```

`GET /resources/item/posts/id/1`

```json
{
  "id": 1,
  "body": "stuff"
}
```

`POST /resources/collection/id/posts/?params`

```json
[{
  "id": 
}]
```

## Collections

**Write** `POST /resources/item/:name/:identifier/?params`

**Read** `GET  /resources/collection/:name/:identifier/?params`

Ex.

`POST /resources/collection/posts/id/?created_at_desc&join=1`

```json
[
  {
    "id": 1,
    "body": "stuff"
  },
  {
    "id": 2,
    "body": "things"
  }
]
```

`GET /resources/posts/collection/?created_at_desc&join=1`

```json
[
  {
    "id": 1,
    "body": "stuff"
  },
  {
    "id": 2,
    "body": "things"
  }
]
```
