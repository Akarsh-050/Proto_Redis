# Proto_Redis

> A Redis-inspired in-memory data store built from scratch in C++ to explore systems programming fundamentals.

Proto_Redis is a lightweight, Redis-compatible in-memory database implemented in modern C++.  
It supports RESP2 protocol parsing, concurrent multi-client connections, basic Redis data structures, and includes a custom-built CLI for interactive command execution.

This project was built to deeply understand how real-world systems like Redis work internally — including networking, concurrency, memory management, and protocol design.

---

## 🚀 Features

### 🖥️ Server

- RESP2 (Redis Serialization Protocol) implementation
- In-memory key-value store
- Supports core Redis data types:
  - Strings
  - Lists
  - Hashes
- Multi-client concurrency using `std::thread`
- TCP networking (IPv4 & IPv6 support)
- Periodic disk persistence (snapshot-based)
- Modular and extensible command handling system

### 💻 CLI (Command Line Interface)

- Interactive REPL mode (like official `redis-cli`)
- One-shot execution mode
- TCP socket communication with server
- Full RESP response parsing
- Clean terminal output formatting

---

## 🧠 Architecture Overview
          ┌──────────────────────┐
          │   Proto_Redis CLI    │
          │  (RESP Client)       │
          └──────────┬───────────┘
                     │  TCP (RESP2)
                     ▼
          ┌──────────────────────┐
          │   Proto_Redis Server │
          │  - Command Parser    │
          │  - Data Store        │
          │  - Persistence       │
          │  - Thread Manager    │
          └──────────────────────┘


### Server Flow

1. Accept TCP client connection  
2. Spawn a dedicated thread  
3. Parse incoming RESP request  
4. Execute command  
5. Send RESP-formatted response  
6. Repeat until client disconnects  

Each client runs in its own thread while sharing the same in-memory data store.

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

## 🛠️ Tech Stack

- **C++ (Modern C++)**
- **TCP networking using the POSIX socket API**
- **Multithreading (`std::thread`)**
- Custom RESP2 Parser & Serializer
- File I/O for persistence

---


## Server Installation
This project uses a simple Makefile. Ensure you have a C++17 (or later) compiler.
- `make`
- `make clean`

Or compile manually:
```bash
cd Redis-Server-main
g++ -std=c++17 -pthread -Iinclude src/*.cpp -o my_redis_server
```

### Running the Server
Start the server on the default port (6379) or specify a port:
```bash
./my_redis_server            # listens on 6379
./my_redis_server 6380       # listens on 6380
```

### ** CLI Installation **
```bash
git clone https://github.com/Akarsh-050/Proto_Redis.git
cd Redis-CLI-main
make
```
This will generate the executable **`bin/my_redis_cli`**.

---

## Usage
After  generating the executable **`bin/my_redis_cli`**, you can use it.

### Start Interactive Mode (REPL)
```
./bin/my_redis_cli
```

### Execute a Single Command
```
./bin/my_redis_cli set mykey "hello"
OK
./bin/my_redis_cli get mykey 
"hello"
```

### Connect to a Custom Redis Server
```
./bin/my_redis_cli -h 192.168.1.10 -p 6380
```

### Clone Repository

```bash
git clone https://github.com/Akarsh-050/Proto_Redis.git
cd Proto_Redis

```

## 🚀 Future Improvements

- Transactions (`MULTI` / `EXEC`)
- Pub/Sub messaging
- Thread pool implementation
- Epoll-based non-blocking server