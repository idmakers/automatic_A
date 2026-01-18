---
title: "Expire Members inside Sets in Redis"
tags:
  - redis
  - set
  - expiration
---

# Expiring Members Inside Sets in Redis

## Core Problem

When using Redis to store objects in a web application, it's common to need to manage the lifetime of these objects. In this scenario, objects belong to a class and are accessed via an ORM library developed in Ruby. The objects require a TTL (Time To Live) time. Currently, storing keys of class objects inside a set and their info in separate keys is a common approach.

For example, a Redis DB can look like this:
```bash
[SET] 'users' => ['user:1', 'user:2', 'user:3']
[STRING] 'user:1' => '{ JSON REPRESENTATION OF user1 }'
[STRING] 'user:2' => '{ JSON REPRESENTATION OF user2 }'
[STRING] 'user:3' => '{ JSON REPRESENTATION OF user3 }'
```
However, managing the expiration of both object info and their membership in the set can be a challenge.

## Solution & Analysis

To address this issue, we propose adding a new command to Redis that allows us to expire members inside sets. This feature would enable easy management of TTLs for objects in the set.

One possible implementation could involve creating a new command `SREMEX users user1` which removes and expires the specified member from the set.
```bash
SMEMBERS users
# Output: ['user:1', 'user:2', 'user:3']

SREMEX users user1
# Output: ['user:2', 'user:3']
```
Another possible implementation could involve using Redis's built-in `ZADD` and `ZREMRANGEBYSCORE` commands to create a score-based expiration system.

For example, we can create a new field in the set that stores the TTL of each member.
```bash
[ZSET] 'users' => [
  ['user:1', 3600], // expires in 1 hour
  ['user:2', 7200], // expires in 2 hours
  ['user:3', 10800] // expires in 3 hours
]
```
We can then use this field to create a TTL-based expiration system.
```bash
ZREMRANGEBYSCORE users 0 -inf
# Output: ['user:1']

EXPIRE user:1 3600
# TTL for user:1 is set to 1 hour

ZREMRANGEBYSCORE users -1 0
# Output: ['user:2', 'user:3']
```
## Conclusion

By adding this new feature, Redis can now easily manage the expiration of members inside sets, making it easier to implement TTL-based systems for objects in a web application.

## Reference
- [Source](https://github.com/redis/redis/issues/8339)