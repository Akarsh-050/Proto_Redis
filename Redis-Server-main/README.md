# Welcome to My Cpp Redis Server
***
---

## Task
A lightweight Redis-compatible in-memory data store written in C++. Supports strings, lists, and hashes, full Redis Serialization Protocol (RESP) parsing, multi-client concurrency, and periodic disk persistence.

---

## Description
**Name:** `my_redis_server`  
**Default Port:** `6379`  

This project implements a Redis clone in C++, providing common Redis commands over a plain TCP socket using the RESP protocol. It supports:

- **Common Commands:** PING, ECHO, FLUSHALL
- **Key/Value:** SET, GET, KEYS, TYPE, DEL/UNLINK, EXPIRE, RENAME
- **List:** LGET, LLEN, LPUSH/RPUSH (multi-element), LPOP/RPOP, LREM, LINDEX, LSET
- **Hash:** HSET, HGET, HEXISTS, HDEL, HKEYS, HVALS, HLEN, HGETALL, HMSET

Data is automatically dumped to `dump.my_rdb` every 300 seconds and on graceful shutdown; at startup, the server attempts to load from this file.

---

## Repository Structure

```
├── include/                # Public headers
│   ├── RedisCommandHandler.h
│   ├── RedisDatabase.h
│   └── RedisServer.h
├── src/                    # Implementation files
│   ├── RedisCommandHandler.cpp
│   ├── RedisDatabase.cpp
│   ├── RedisServer.cpp
│   └── main.cpp            # Entry point
├── Concepts,UseCases&Tests.md    # Design concepts and command use cases
├── Makefile                # Build rules
├── README.md               # This documentation
└── test_all.sh             # Bash script for all tests
```

---

## Installation
This project uses a simple Makefile. Ensure you have a C++17 (or later) compiler.
- `make`
- `make clean`

```bash
# from project root
make
```

Or compile manually:
```bash
g++ -std=c++17 -pthread -Iinclude src/*.cpp -o my_redis_server
```

---

## Usage
After compiling the server, you can run it and use with client.

### Running the Server

Start the server on the default port (6379) or specify a port:

```bash
./my_redis_server            # listens on 6379
./my_redis_server 6380       # listens on 6380
```

On startup, the server attempts to load `dump.my_rdb` if present:
```
Database Loaded From dump.my_rdb
# or
No dump found or load failed; starting with an empty database.
```

A background thread dumps the database every 5 minutes.

To gracefully shutdown and persist immediately, press `Ctrl+C`.

---

### Using the Server

You can connect with the standard `redis-cli` or custom RESP client `my_redis_cli`.

```bash
# Using redis-cli:
redis-cli -p 6379

# Example session:
127.0.0.1:6379> PING
PONG

127.0.0.1:6379> SET foo "bar"
OK

127.0.0.1:6379> GET foo
"bar"
```

---

## Supported Commands

### Common
- **PING**: `PING` → `PONG`
- **ECHO**: `ECHO <msg>` → `<msg>`
- **FLUSHALL**: `FLUSHALL` → clear all data

### Key/Value
- **SET**: `SET <key> <value>` → store string
- **GET**: `GET <key>` → retrieve string or nil
- **KEYS**: `KEYS *` → list all keys
- **TYPE**: `TYPE <key>` → `string`/`list`/`hash`/`none`
- **DEL/UNLINK**: `DEL <key>` → delete key
- **EXPIRE**: `EXPIRE <key> <seconds>` → set TTL
- **RENAME**: `RENAME <old> <new>` → rename key

### Lists
- **LGET**: `LGET <key>` → all elements
- **LLEN**: `LLEN <key>` → length
- **LPUSH/RPUSH**: `LPUSH <key> <v1> [v2 ...]` / `RPUSH` → push multiple
- **LPOP/RPOP**: `LPOP <key>` / `RPOP <key>` → pop one
- **LREM**: `LREM <key> <count> <value>` → remove occurrences
- **LINDEX**: `LINDEX <key> <index>` → get element
- **LSET**: `LSET <key> <index> <value>` → set element

### Hashes
- **HSET**: `HSET <key> <field> <value>`
- **HGET**: `HGET <key> <field>`
- **HEXISTS**: `HEXISTS <key> <field>`
- **HDEL**: `HDEL <key> <field>`
- **HLEN**: `HLEN <key>` → field count
- **HKEYS**: `HKEYS <key>` → all fields
- **HVALS**: `HVALS <key>` → all values
- **HGETALL**: `HGETALL <key>` → field/value pairs
- **HMSET**: `HMSET <key> <f1> <v1> [f2 v2 ...]`

---

## Design & Architecture

- **Concurrency:** Each client is handled in its own `std::thread`.  
- **Synchronization:** A single `std::mutex db_mutex` guards all in-memory stores.  
- **Data Stores:**  
  - `kv_store` (`unordered_map<string,string>`) for strings  
  - `list_store` (`unordered_map<string,vector<string>>`) for lists  
  - `hash_store` (`unordered_map<string,unordered_map<string,string>>`) for hashes
- **Expiration:** Lazy eviction on each access via `purgeExpired()`, plus TTL map `expiry_map`.  
- **Persistence:** Simplified RDB: text‐based dump/load in `dump.my_rdb`.  
- **Singleton Pattern:** `RedisDatabase::getInstance()` enforces one shared instance.  
- **RESP Parsing:** Custom parser in `RedisCommandHandler` supports both inline and array formats.

---

## Concepts & Use Cases

Refer to [Concepts,UseCases&Tests.md](Concepts,UseCases&Tests.md) for a detailed description of the underlying concepts (TCP sockets, RESP, data structures, etc.) and real‑world usage scenarios for each command.


---
