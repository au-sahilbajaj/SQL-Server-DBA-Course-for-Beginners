# 1.3 Basic Architecture Components

Now that you know what an instance and a database are, let's open up the instance and look at the major moving parts inside it. You do not need to memorize every internal detail right now. You need a correct mental map, so that later, more advanced topics have somewhere to "click into place."

## The restaurant analogy

The clearest way to understand SQL Server's architecture is to imagine a busy restaurant.

1. **The front door and the host stand** (Protocol Layer): This is where every customer (every client application, every query) first arrives. The host checks who you are and which "language" you are speaking, then walks you inside.
2. **The waiter who takes your order and decides how the kitchen should make it** (Relational Engine / Query Processor): The waiter listens to your order ("I'd like the chicken, no onions"), and works out the most efficient way for the kitchen to prepare exactly that, in what order, using which stations.
3. **The kitchen itself, with the fridge, the pantry, and the cooking stations** (Storage Engine): This is where the actual ingredients (your data) are stored and where they are physically retrieved, written, and updated.
4. **The receipt and the order ticket system** (Transaction Log / Buffer Management): This tracks every change so that if the kitchen catches fire halfway through making your order, the restaurant knows exactly what was promised, what was started, and what to redo when things are back online.

Let's now translate each of these into their real technical names.

## 1. The Protocol Layer

This is the entry point. When an application (SQL Server Management Studio, a website, a reporting tool) wants to talk to SQL Server, it has to connect using one of a small number of supported network protocols. The most common ones are:

- **Shared Memory**: used only when the client and SQL Server are running on the very same machine. Fast, but only works locally.
- **TCP/IP**: the standard protocol for almost all real-world connections, whether on a local network or across the internet.
- **Named Pipes**: an older protocol mostly used on local networks.

Whatever protocol is used, the actual data exchanged between the client and SQL Server is wrapped in a format called **TDS (Tabular Data Stream)**, a protocol originally created by Sybase and now owned and maintained by Microsoft. Think of TDS as the specific "order form language" that the waiter and the kitchen have both agreed to use, regardless of which door the customer walked in through.

## 2. The Relational Engine (also called the Query Processor)

Once your query arrives, it is handed to the Relational Engine. This component has three jobs, performed in sequence:

- **Parsing**: checking that what you typed is actually valid SQL grammar (the "Command Parser")
- **Optimizing**: deciding the most efficient way to actually fetch the data you asked for, out of potentially thousands of possible approaches (the "Query Optimizer")
- **Executing**: running the chosen plan, requesting the actual data pages from the Storage Engine, and assembling the final result set to send back to you

The Query Optimizer is genuinely one of the most sophisticated pieces of engineering in all of SQL Server. It is a cost based optimizer, meaning it estimates the "cost" (in CPU and I/O effort) of many different possible ways to run your query and picks the plan it believes will be fastest overall, not necessarily the plan that uses the least amount of any single resource.

## 3. The Storage Engine

This is the part that actually manages reading data from disk and writing data back to disk. Its core responsibilities include:

- **Access methods**: the logic that decides how to read and write the actual rows and pages on disk
- **Buffer management**: keeping frequently used data in memory (RAM) so SQL Server does not have to go to the much slower physical disk every single time
- **Transaction management**: making sure that a group of changes either completes entirely, or does not happen at all (this property is part of what is called ACID compliance, covered in later modules)

## 4. SQLOS (the SQL Server Operating System layer)

This is a part beginners almost never hear about early on, but it explains a lot of "why is SQL Server doing that" questions later. SQLOS is a thin layer that SQL Server built for itself, sitting between SQL Server and the actual Windows or Linux operating system. Its job is to manage scheduling of work, memory allocation, and synchronization in a way that is tuned specifically for database workloads, rather than relying purely on generic operating system scheduling. We will explain exactly why SQL Server does this in the next two lessons, when we talk about memory, CPU, and workers.

## Putting it all together, in order

Here is the full journey of a single query, from the moment you press "Run" to the moment you see results:

1. Your application sends the query over the network using TDS, through one of the supported protocols.
2. The Relational Engine parses the query to check the grammar is valid.
3. The Query Optimizer decides the cheapest way to actually fetch what you asked for, producing an execution plan.
4. The Relational Engine asks the Storage Engine, "please get me these specific pages of data."
5. The Storage Engine first checks if those data pages are already sitting in memory (the buffer pool). If yes, it grabs them instantly. If no, it reads them off the physical disk into memory first, then grabs them.
6. The result is assembled and sent back to your application, again over TDS.
7. If your query changed any data (an INSERT, UPDATE, or DELETE), the change is also written to the transaction log before SQL Server confirms back to you that the change is safely done.

## Official Microsoft resources

- [SQL Server Guides home page (internals and architecture)](https://learn.microsoft.com/en-us/sql/relational-databases/sql-server-guides?view=sql-server-ver17)
- [Query Processing Architecture Guide](https://learn.microsoft.com/en-us/sql/relational-databases/query-processing-architecture-guide?view=sql-server-ver17)
- [Page and extent architecture guide](https://learn.microsoft.com/en-us/sql/relational-databases/pages-and-extents-architecture-guide?view=sql-server-ver17)
- [SQL Server transaction log architecture and management guide](https://learn.microsoft.com/en-us/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide?view=sql-server-ver17)
- [Trace the network authentication process to the Database Engine](https://learn.microsoft.com/en-us/sql/relational-databases/database-engine-connection-open-network-trace?view=sql-server-ver17)

## Recommended YouTube learning

- **Bob Ward (Microsoft)**: search "Bob Ward SQL Server Internals" or "Bob Ward Under the Hood." Bob is a Microsoft Principal Architect who has worked on the SQL Server engine itself for decades, and his public conference talks are the closest thing to a guided tour of the actual engine source code logic, explained for a human audience.
- **Kudvenkat**: search his SQL Server playlist for the early architecture overview videos, useful as a slower, more beginner-paced companion to Bob Ward's deeper talks.

## Quick recap before moving on

- A query travels through four major stops: Protocol Layer, Relational Engine, Storage Engine, and (in the background) SQLOS managing memory and scheduling.
- The Relational Engine decides "how" to get the data (parsing and optimizing). The Storage Engine actually "gets" the data (reading and writing pages).
- TDS is the shared language that client applications and SQL Server use to talk to each other, no matter which network protocol carried it.
- Frequently used data lives in memory (the buffer pool) so SQL Server does not have to hit the slow physical disk every single time.

Next: [`04-why-is-it-called-relational.md`](04-why-is-it-called-relational.md)
