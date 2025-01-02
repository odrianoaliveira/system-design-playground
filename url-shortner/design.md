# URL Shortener Design

This document aims to list all requirements, constraints, assumptions in the scope of designing a URL shortener application.

## Requirements
### Functional
1. The applications must shortener the given URL
2. Given the shortener URL the application must redirect the user to the original URL.
3. Support custom expiration time for short URLs.

### Non-functional
1. Provide high scalability
2. The application must be responsive and provides high availability

## Constraints & Assumptions
## Volume
1. Assume billions URL to start, popular URL shorteners like TinyURL or Bitly handle vast numbers or URL.
2. 10 millions new URL per month.
3. Queries per second (QPS): 1000 read and 100 write operations.

### Storage
1. Assume each URL entry is ~500 bytes. 500Gb for a billion URLs.

## High-level Design

### Components

1. User interface
2. Load balancer: distribute the traffic across backend services.
3. Backend services: where the business logic to create and redirect shortened URL runs.
4. Database: Where the shorter and original URLs mapping are stored.
5. Cache: To fast serve frequently accessed URLs.

![System Architecture Diagram](https://raw.githubusercontent.com/odrianoaliveira/system-design-playground/79e80ddb5ebe91809c662d38c0b90a38d8c5ce11/url-shortner/assets/UrlShortner.drawio.png)

### Database decision

- Cassandra is the most suitable database for scalability and availability, with its built-in TTL feature simplifying implementation. 
- CockroachDB is a great choice for strict consistency needs, albeit at the cost of higher latency and operational complexity. 
- PostgreSQL can be a fallback for lower-scale implementations, but scaling to billions of URLs will require additional effort and infrastructure investment.


| Feature                    | CockroachDB                                   | Cassandra                                  | PostgreSQL (Master-Replica)                                      |
|----------------------------|-----------------------------------------------|--------------------------------------------|------------------------------------------------------------------|
| **Consistency**            | Strong (ACID)                                 | Tunable (eventual by default)              | Strong for writes; eventual for replicas                         |
| **Availability**           | High (handles failover automatically)         | High (no single point of failure)          | Moderate (requires manual failover)                              |
| **Scalability**            | Horizontal; suited for distributed systems    | Exceptional horizontal scaling             | Limited to vertical scaling of master                            |
| **Latency**                | Higher for writes                             | Low for writes (eventual consistency)      | Moderate; replica reads are low latency                          |
| **Query Capabilities**     | Full SQL                                      | Limited CQL                                | Full SQL                                                         |
| **Write Throughput**       | Moderate                                      | Very high                                  | Limited by master node                                           |
| **Read Scalability**       | High (distributed)                            | High (distributed)                         | High (replica reads)                                             |
| **TTL Support**            | Not natively supported; requires custom logic | Native support for TTL at the record level | Requires manual implementation using background jobs or triggers |
| **Operational Complexity** | Moderate                                      | High for large clusters                    | Low                                                              | 

#### Decision

Cassandra is the most suitable database for scalability and availability, with its built-in TTL feature simplifying implementation.
