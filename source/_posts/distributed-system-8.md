---
title: Distributed System (8)
date: 2024-02-22 19:10:55
img_path: /_posts/
mermaid: true
categories:
- Course Notes
- Distributed Systems
index_img: ../img/post/distributed-system-8-6.png
---

## More efficient B-multicasts

### B-multicast

![](../img/post/distributed-system-8.png){: w="500" }

in physical network view: Redundant packets

![](../img/post/distributed-system-8-1.png){: w="500" }

### Tree-based multicast

construct a minimum spanning tree and unicast along that.

![](../img/post/distributed-system-8-2.png){: w="500" }

then,

![](../img/post/distributed-system-8-3.png){: w="500" }

construct a tree that includes network routers.

![IP multicast](../img/post/distributed-system-8-4.png){: w="500" }
_IP multicast_

### Third approach: Gossip

- Transmit to b random targets.
- Other nodes do the same when they receive a message.

Properties

- No "tree-construction" overhead.
- More efficient than unicasting to all receivers
- no hard guarantees.

![](../img/post/distributed-system-8-5.png){: w="500" }


## Mutual Exclusion

Classical algorithms for mutual exclusion in distributed systems:

- Central server algorithm
- Ring-based algorithm
- Ricart-Agrawala Algorithm
- Maekawa Algorithm

### prerequisites

- Safety (essential):
  - At most one process executes in CS (Critical Section) at any time.
- Liveness (essential):
  - Every request for a CS is granted eventually.
- Ordering (desirable):
  - Requests are granted in the order they were made.

### Central Server Algorithm

- Elect a central server (or leader)
- Leader keeps
  - A queue of waiting requests from processes who wish to access the CS
  - A special token which allows its holder to access CS
- Actions of any process in group:
  - enter()
    - Send a request to leader
    - Wait for token from leader
  - exit()
    - Send back token to leader

Leader:

- On receiving a request from process Pi
  - if (leader has token)
    - Send token to Pi
  - else
    - Add Pi to queue
- On receiving a token from process Pi
  - if (queue is not empty)
    - Dequeue head of queue (say Pj), send that process the token
  - else
    - Retain token

### Ring-based algorithm

the token travel around the ring.

![](../img/post/distributed-system-8-6.png){: w="500" }

- enter()
  - Wait until you get token
- exit() // already have token
  - Pass on token to ring successor
