
# Grokking the System Design Interview

[Course link](https://www.educative.io/courses/grokking-the-system-design-interview)

# Part 2 Important Concepts

## Key Characteristics of Distributed System

- Scalability
    - horizontal scale
    - vertical scale
- Reliability , Availability
- Effiency
    - response time (or latency)
    - throughput
- Manageability

## Load Balancing

- direct traffic(require, data flow) to different (healthy and online) servers to reduce load —> efficiency ; reliability
- Where to put loading balacer
    - clients and web servers
    - internal server layers (such as web server and application server)
    - server and database (and cache)
- Methods
    - round robin; weighted round robin
    - least active connection; least response time; least bandwidth
    - IP hash
- Types
    - DNS load balancing
    - hardware load balancing
    - software load balancing : NGINX

## Cache

- fast read
- update data methods
    - write-back (update cache first, and then update database later)
    - write-through (update cache and database together )
    - write around (update database and invalidate cache)
- cache eviction
    - LRU least recent used;  MRU most recent used
    - LFU least frequent used
    - FIFO; LIFI
    - RR: random replacement

## Data partition

- Horizontal partition (row by row)
- Vertical partition (by some columns(features))
- director based partition (create a lookup service)
- Partition Methods
    - key or hash based partition
    - List based partition (e,g. data of some cities put in one DB)
    - round robin partition
    - composite partition (compose several methods)
- Drawback (constrains) of data partition
    - join and denormalization (data in diff DBs, hard to do join)
    - Referential integrity (data consistency)
    - Rebalancing (re-distribute data is hard, need downtime)

## Indexing:

- use index for fast search for tables in database (think this as a column of keys that pointing to the right rows and its easy to search this column)
- but write performance will decrease with indexing (every write has to update the index)

## Proxy Server 代理服务器

- between client and back-end server
    - filter requests; log requests; transform requests
    - cache same request results (no need to hit server again)
- Three famous proxy
    - Open proxy  (for any internet used)
        - Anonymous proxy (hide user iP)
        - Transpare proxy
    - Reverse proxy

## Redundancy and Replication

- Redundancy: duplicate import components (remove single point failure; tolerant)
- replication: sharing information to ensure consistency between redundant resources

## SQL vs NoSQL

- Relational database (SQL): structured data, tables,rows,columns
    - easy to scale up (vertical scale)
    - ACID compliant, data consistency
- NoSQL: unstructured, easy to scale out (horizontal scale)
    - key-value database: Amazon DynamoDB
    - wide column database (column families): HBase, Cassandra
    - document database: MongoDB, CouchDB
    - Graph database: nodes, properties and edges:  Neo4j, AWS Neptune
- Use SQL:
    - data structure is fixed;
    - want ACID (like transaction for e-commerce)
- Use NoSQL
    - large volume of data with no structure
    - for distributed system (scale horizontally )
    - for rapid development which require making frequent updates to the data structure

## CAP theorem

- C: consistency
- A: Availability
- P: Partition tolerance
- CAP theorem: we can only choose two from CAP
    - in distributed system, it's between CP and AP
    - CP: BigTable, HBase
    - AP: Cassandra, Dynamo
- PACELC
    - When there is Partition (distributed system): Choose Availability or Consistency
    - Else: Choose Low Latency or Consistency
    

## Consistent Hashing

- divide into more nodes (Virtual nodes) and assign non-contiguous ranges to nodes
- easy to add and remove nodes
- avoid hot nodes

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/23f32765-5f43-4222-b370-2504967c80dc/Untitled.png)

## Polling, Websocket, SSE

- polling: normal client send request to the server and server send data back (short time interval)
- Long polling: client send request  and then can wait for the server, sever sends data back when data is available. There is a timeout
- Websocket: Bi-directional persistent connection between client and server
- SSE (server-sent event): persistent connection between client and server so that server can send data to a client

## Bloom filters

- quick check if a element is
    - **definitely not** in the set
    - or **possibly in the se**t
- use `m` digits and `k` hash functions
    - when add: use `k` hash functions and change `hash_k(key)` to 1
    - when check:
        - if any `hash_i(key)` is 0, then the key is definely not in the set
        - if all `1`s then it's possible
        - 

## Quorum

- a quorum is the minimum number of nodes that a distributed operations needs to be performed successfully before declaring its overall success
- choose quorum N/2+1,
    - 2k+1 nodes —→ quorum choose k+1
    
- Read + Write > N quorum
    - choose 1<read<write<N,  since read is more often than write in most case

## Leader and Follower

- one server as leader, all nodes  accept writes to replicate the leader
- if leader dead, choose another node as leader
- higher availability than quorum

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/e2435951-05eb-4f28-9e16-bd3a95c51827/Untitled.png)

## Heartbeat

- in a distributed system, we need to **know what nodes are alive**
- Each node (server) sends a heartbeat message to the central server periodically to show it's alive. If there is no central server, then send to randomly chosen set of  servers.
- if in a configured timeout period, the system can conclude that the server is not alive anymore and stop sending requests to it and start working on its replacement

## CheckSum

- to make sure the data integrity (data may corrupt or lost in the process, so we need to check the integrity)
- store `data` and `checksum = hash(data)`, when received data, we can check if `hash(data)` is the same as stored `checksum`
    - Hashing methods: MD5, SHA-1, SHA 256, SHA 512
    
    ---

# Part 1: System Design Examples

## 1. TinyURL, Pastebin

- Key generate services + key database
- Metadata + object storage (Amazon S3)

## 2. Instagram, Twitter,  Facebook NewsFeed

- data storage: use Timestamp+id  as feed item ID (easier to retreive the most recent posts)
- data partition (sharding)
- push/pull/hybrid model for news feed
