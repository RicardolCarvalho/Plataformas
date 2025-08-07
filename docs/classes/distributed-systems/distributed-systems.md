
Distributed Systems are systems that consist of multiple independent components that communicate and coordinate with each other to achieve a common goal. These systems are designed to work together despite being located on different machines or networks.

## Overview

Distributed systems can be complex due to the challenges of network communication, data consistency, fault tolerance, and scalability. They are used in various applications, from cloud computing to large-scale web services.

### CAP THEOREM

The CAP theorem states that a distributed system can only guarantee two out of the following three properties at the same time:

1. **Consistency**: All nodes see the same data at the same time.
2. **Availability**: Every request receives a response, either success or failure.
3. **Partition Tolerance**: The system continues to operate despite network partitions.

In practice, this means that when designing a distributed system, trade-offs must be made between these properties based on the specific requirements of the application.
