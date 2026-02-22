---
title: "Database Internals"
date: 2026-02-21
categories:
  - Networking
description: "Overview of how does databases works internally"

---

There’s no common blueprint for database management system design. Every database is built slightly differently, and component boundaries are somewhat hard to see and define. Even if these boundaries exist on paper (e.g., in project documentation), in code seemingly independent components may be coupled because of performance optimizations, handling edge cases, or architectural decisions.

Database management systems use a **client/server model**, where database system instances (nodes) take the role of servers, and application instances take the role of clients.

Client requests arrive through the **transport subsystem**. Requests come in the form of queries, most often expressed in some query language. The transport subsystem is also responsible for communication with other nodes in the database cluster.

{{< img
  src="database.png"
  alt="database component diagram"
  caption="Architecture of a database management system" >}}

Upon receipt, the transport subsystem hands the query over to a **query processor**, which parses, interprets, and validates it. Later, access control checks are performed, as they can be done fully only after the query is interpreted.

The parsed query is passed to the query optimizer, which first eliminates impossible and redundant parts of the query, and then attempts to find the most efficient way to execute it based on internal statistics (index cardinality, approximate intersection size, etc.) and data placement (which nodes in the cluster hold the data and the costs associated with its transfer). The optimizer handles both relational operations required for query resolution, usually presented as a dependency tree, and optimizations, such
as index ordering, cardinality estimation, and choosing access methods.

The query is usually presented in the form of an execution plan (or query plan): a
sequence of operations that have to be carried out for its results to be considered
complete. Since the same query can be satisfied using different execution plans that
can vary in efficiency, the optimizer picks the best available plan.
