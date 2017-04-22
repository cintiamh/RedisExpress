# Using Redis

## Redis CLI

* In-memory data structure store
* Memory lookups are fast
* Redis is a network server

Create a Docker container for Redis (Start Redis server):

```
$ docker run --name packt-redis -p 16379:6379 -d redis:3.2.4
```

Connect Redis-CLI:

```
$ docker run -it --link packt-redis:redis --rm redis redis-cli -h redis -p 6379
```

Now you can run any command through `redis:6379>`

Stop or restart Redis server:

```
$ docker stop packt-redis
$ docker start packt-redis
```

Help:
```
$ docker run -it --rm redis redis-cli --help
```

Once in the Redis-CLI, you can just press tab and go through the available commands in Redis.

You can also start the command with `help` and press tab to go through what we can have help with.

```
redis:6379> help set

  SET key value [EX seconds] [PX milliseconds] [NX|XX]
  summary: Set the string value of a key
  since: 1.0.0
  group: string

redis:6379> help get

  GET key
  summary: Get the value of a key
  since: 1.0.0
  group: string

redis:6379> SET TEST_KEY "test value"
OK
redis:6379> GET TEST_KEY
"test value"
```

## Redis data types

### Data types

* String
* Hashes
* Lists
* Sets
* Sorted Sets

#### String

```
redis:6379> set user:1:username cintia
OK
redis:6379> get user:1:username
"cintia"
```

#### Hashes

Key Value pairs.

```
redis:6379> hmset user:1 first_name Cintia last_name Higashi
OK
redis:6379> hgetall user:1
1) "first_name"
2) "Cintia"
3) "last_name"
4) "Higashi"
```

#### Lists

The lists are faster if run in order. You can have repeated values.

```
redis:6379> lpush user:1:profile_views 5
(integer) 1
redis:6379> lpush user:1:profile_views 10
(integer) 2
redis:6379> lpush user:1:profile_views 15
(integer) 3
redis:6379> lpush user:1:profile_views 18
(integer) 4
redis:6379> lrange user:1:profile_views 0 -1
1) "18"
2) "15"
3) "10"
4) "5"
```

#### Sets

Sets have unique members.

```
redis:6379> sadd post:1:users 1 2
(integer) 2
redis:6379> sadd post:1:users 1
(integer) 0
redis:6379> smembers post:1:users
1) "1"
2) "2"
```

#### Sorted Sets

It's pretty much like a set, but it can have a score

```
redis:6379> zadd logins 500 1
(integer) 1
redis:6379> zadd logins 600 15
(integer) 1
redis:6379> zadd logins 650 18
(integer) 1
redis:6379> zrange logins 0 -1
1) "1"
2) "15"
3) "18"
redis:6379> zrange logins 0 -1 WITHSCORES
1) "1"
2) "500"
3) "20"
4) "550"
5) "15"
6) "600"
7) "18"
8) "650"
redis:6379> zrevrange logins 0 -1
1) "18"
2) "15"
3) "20"
4) "1"
```

## Redis commands

https://redis.io/commands

Previous commands:

```
redis:6379> set user:1 test
OK
redis:6379> get user:1
"test"
```

Increment:
```
redis:6379> set counter 1
OK
redis:6379> incr counter
(integer) 2
redis:6379> get counter
"2"
```

Exists:
```
redis:6379> exists user:1
(integer) 1
redis:6379> exists no
(integer) 0
```

Expires:
```
redis:6379> expire user:1 1
(integer) 1
redis:6379> get user:1
(nil)
redis:6379> set test_key test EX 120
OK
```

Time to live:
```
redis:6379> ttl test_key
(integer) 116
redis:6379> ttl test_key
(integer) 112
```

Hashes:
```
redis:6379> hset hash_key field value
(integer) 1
redis:6379> hget hash_key field
"value"
redis:6379> hmset hash_key first 1 second 2
OK
redis:6379> hgetall hash_key
1) "field"
2) "value"
3) "first"
4) "1"
5) "second"
6) "2"
redis:6379> hkeys hash_key
1) "field"
2) "first"
3) "second"
```

Lists
```
redis:6379> lpush dogs dexter
(integer) 1
redis:6379> lpush dogs gizmo
(integer) 2
redis:6379> lrange dogs 0 -1
1) "gizmo"
2) "dexter"
redis:6379> rpush dogs fido
(integer) 3
redis:6379> lrange dogs 0 -1
1) "gizmo"
2) "dexter"
3) "fido"
```

Sets
```
redis:6379> sadd sdogs dexter
(integer) 1
redis:6379> sadd sdogs dexter
(integer) 0
redis:6379> sadd sdogs gizmo
(integer) 1
redis:6379> smembers sdogs
1) "dexter"
2) "gizmo"
redis:6379> sismember sdogs dexter
(integer) 1
redis:6379> sismember sdogs penny
(integer) 0
redis:6379> srem sdogs dexter
(integer) 1
redis:6379> smembers sdogs
1) "gizmo"
redis:6379> sadd sdogs dexter
(integer) 1
redis:6379> sadd dogs2 fido
(integer) 1
redis:6379> sadd dogs2 dexter
(integer) 1
redis:6379> sdiff sdogs dogs2
1) "gizmo"
redis:6379> sinter sdogs dogs2
1) "dexter"
redis:6379> sunion sdogs dogs2
1) "dexter"
2) "fido"
3) "gizmo"
```

Sorted Sets
```
redis:6379> zadd zdogs 100 dexter
(integer) 1
redis:6379> zadd zdogs 200 gizmo
(integer) 1
redis:6379> zrange adogs 0 -1
(empty list or set)
redis:6379> zrange zdogs 0 -1
1) "dexter"
2) "gizmo"
redis:6379> zrange zdogs 0 -1 WITHSCORES
1) "dexter"
2) "100"
3) "gizmo"
4) "200"
redis:6379> zadd zdogs 300 dexter
(integer) 0
redis:6379> zrange zdogs 0 -1 WITHSCOREWS
(error) ERR syntax error
redis:6379> zrange zdogs 0 -1 WITHSCORES
1) "gizmo"
2) "200"
3) "dexter"
4) "300"
redis:6379> zrangebyscore zdogs 100 250
1) "gizmo"
```
