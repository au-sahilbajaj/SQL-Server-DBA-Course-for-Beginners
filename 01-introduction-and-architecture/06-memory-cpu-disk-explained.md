# 1.6 How Memory, CPU, and Disk Work for SQL Server (and What "Workers" Means)

This is one of the most important lessons in the entire Introduction module, because almost every real world SQL Server problem a DBA gets called about traces back to one of these three resources: memory, CPU, or disk, being overworked, misconfigured, or starved.

## Start with a real world example: a busy kitchen

Picture a restaurant kitchen during the dinner rush.

- **The walk-in fridge and the pantry shelves** represent your **disk storage**. This is where all the ingredients (your data) are permanently kept when not actively being used. It holds a huge amount, but walking back there to grab something takes real time.
- **The counter space right next to the stove**, where the chef keeps the ingredients they are actively using for the dish in front of them right now, represents **memory (RAM)**. It is much smaller than the whole pantry, but grabbing something from the counter is instant, far faster than walking to the fridge.
- **The chefs themselves**, the people actually doing the chopping, searing, and plating, represent **CPU (the processor)**. A chef can only physically do one task at a time, but a kitchen can have multiple chefs working in parallel on different parts of different orders.
- **The order tickets being worked on right now** represent **workers** (also called worker threads), the actual units of active work being handled, one ticket per active task.

Keep this kitchen picture in your head. We are going to walk through each piece properly now.

## Disk: the permanent home of your data

Every database's actual data lives permanently on disk, inside files. (You will learn the specific file types, `.mdf`, `.ldf`, `.ndf`, in Module 3 when we cover creating databases.) Disk storage is large and permanent: even if the server is completely turned off and powered back on, the data on disk is still there, exactly like the food in the pantry is still there overnight after the restaurant closes.

The tradeoff is speed. Reading from a physical disk, even a fast modern SSD, is dramatically slower than reading from RAM. This single fact, disk is slow, memory is fast, is the reason almost the entire architecture of SQL Server is built the way it is.

## Memory (RAM): the fast workspace

Because going to disk every single time would make SQL Server painfully slow, SQL Server keeps a large area of memory called the **buffer pool**, which acts as a cache of recently and frequently used data pages. The very first thing SQL Server does when it needs a piece of data is check: "is this page already sitting in my buffer pool?" If yes, it grabs it instantly, exactly like the chef reaching for an ingredient already sitting on the counter instead of walking to the fridge. If no, SQL Server has to go fetch that page from disk, place a copy of it into the buffer pool, and then use it.

This means that, over time, frequently accessed data tends to "stay warm" in memory, while rarely accessed data has to be fetched from disk more often. This is also why giving SQL Server more memory is one of the single most impactful things a DBA can do for performance: a bigger counter means the chef has to walk to the fridge far less often.

SQL Server also uses memory for other jobs besides caching data pages, including holding execution plans it has already optimized (so it does not have to redo that expensive planning work every single time the exact same query runs again), and providing working space for sorting and joining data during query execution.

## CPU: the actual workers doing the thinking

The CPU (the processor, made up of individual cores) is what actually performs every calculation, comparison, and instruction SQL Server needs: comparing values, joining tables together, sorting results, and so on. Modern servers usually have many CPU cores available, sometimes dozens, and SQL Server is specifically built to take advantage of multiple cores at once, splitting a single large query into pieces that different cores can work on simultaneously. This is called **parallelism**, and it is exactly like a kitchen having four chefs work on four different parts of one large catering order simultaneously, instead of forcing one chef to do the entire order alone from start to finish.

## So what exactly is a "worker" or "worker thread"

This is the part beginners find most confusing, so let's be very precise. SQL Server does not let raw CPU cores directly serve your requests. Instead, SQL Server has its own internal scheduling system, called **SQLOS** (mentioned back in lesson 1.3), which manages a pool of **worker threads**.

Think of it this way: the CPU cores are the physical chefs in the kitchen. A "worker" is more like an active order ticket currently being handled. SQL Server creates a limited, configurable number of these workers when it starts up (by default, this number scales based on how many CPU cores and what architecture your server has, and Microsoft documents the exact default formula). When a query comes in and needs to actually run, it is assigned to an available worker thread, which then executes on an actual CPU core, or shares time on a core with other workers if every core is currently busy.

If every available worker thread is currently busy handling other requests, new requests have to wait in line, exactly like new order tickets piling up at a kitchen pass when every chef is already mid-dish. This waiting state has a real, specific name in SQL Server troubleshooting: a query that is ready to run but is waiting for a worker thread or a CPU core to become free is said to be experiencing **CPU pressure** or, depending on the exact reason, sitting on the **scheduler's runnable queue**.

SQLOS uses a cooperative scheduling model, meaning a running worker is expected to voluntarily "yield" the CPU periodically so other waiting workers get a fair turn, rather than the operating system forcibly interrupting it. This is a deliberate design choice that lets SQL Server manage scheduling more efficiently for database-specific workloads than relying purely on the generic operating system scheduler.

## Putting all three together with one final example

Imagine a customer asks the kitchen for a complicated ten-course tasting menu, right at the absolute peak of dinner rush.

- The kitchen needs to check the pantry (disk) for ingredients it does not already have prepared
- It needs counter space (memory) to actually lay out and combine those ingredients efficiently
- It needs available chefs (CPU cores, accessed through worker threads) to actually do the chopping, cooking, and plating

If the pantry is far away and disorganized (slow, poorly configured disk), every dish takes longer. If the counter space is too small (not enough memory allocated to SQL Server), the chef has to keep running back to the pantry for things that should have just stayed nearby. If every chef is already buried in other orders (CPU and worker thread exhaustion), the new ten-course order simply has to wait its turn, no matter how good the kitchen's layout is.

A huge part of a DBA's day to day job, especially in performance troubleceshooting, is figuring out which one of these three resources, disk, memory, or CPU and workers, is the actual bottleneck behind a slow running system, because the fix for each one is completely different.

## Official Microsoft resources

- [Memory management architecture guide](https://learn.microsoft.com/en-us/sql/relational-databases/memory-management-architecture-guide?view=sql-server-ver17)
- [Thread and task architecture guide](https://learn.microsoft.com/en-us/sql/relational-databases/thread-and-task-architecture-guide?view=sql-server-ver17)
- [SQL Server I/O fundamentals](https://learn.microsoft.com/en-us/sql/relational-databases/sql-server-storage-guide?view=sql-server-ver17)
- [Diagnose and resolve spinlock contention on SQL Server](https://learn.microsoft.com/en-us/sql/relational-databases/diagnose-resolve-spinlock-contention?view=sql-server-ver17)

## Recommended YouTube learning

- **Brent Ozar Unlimited**: search "Brent Ozar memory CPU SQL Server" or "Brent Ozar wait stats." Brent's free conference talks on "wait statistics" are widely considered one of the best beginner-friendly entry points into understanding exactly what SQL Server is waiting on, memory, CPU, or disk, at any given moment.
- **Bob Ward (Microsoft)**: search "Bob Ward SQLOS" for a Microsoft-insider deep dive specifically on the internal scheduler.

## Quick recap before moving on

- Disk is large and permanent, but slow. Memory (RAM) is smaller, but extremely fast, and SQL Server caches frequently used data there in the buffer pool.
- CPU cores are the actual physical workers doing computation, and SQL Server can split large queries across multiple cores in parallel.
- A "worker thread" is SQL Server's own internal unit of active work, scheduled by SQLOS, not a raw CPU core itself.
- When every worker thread is busy, new work has to wait, which is one of the most common real world performance problems a DBA diagnoses.

Next: [`07-sql-server-versions.md`](07-sql-server-versions.md)
