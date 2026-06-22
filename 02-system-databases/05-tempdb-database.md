# 2.5 The tempdb Database

## The real world example

Imagine a busy shared kitchen prep room used by every chef in a large restaurant. Nobody owns this room personally. Any chef, working on any dish, can walk in, use the counters and tools to prep ingredients, sort things, combine things temporarily, and then walk back out to plate the final dish elsewhere. At the very end of every single night, the entire prep room is completely cleared and reset, wiped down to nothing, ready for a brand new day. Nothing left in that room overnight is expected to survive until morning.

`tempdb` is that shared prep room, for the entire SQL Server instance.

## What tempdb actually stores

`tempdb` is a global, shared workspace available to every single connection on the entire instance, used to hold temporary objects and intermediate results. This includes:

- **User-created temporary objects**: things you explicitly create yourself, including local and global temporary tables, temporary stored procedures, table variables, and cursors
- **Internal, system-generated work**: SQL Server itself uses tempdb internally for things like sorting large result sets, building certain types of indexes, processing complex joins, and storing intermediate results during query execution, even when you never explicitly typed anything mentioning "temp" in your own query
- **Row versioning information**: used to support certain concurrency features that let readers and writers avoid blocking each other

The critical thing to understand is that tempdb is used constantly behind the scenes, far more often than most beginners expect, even by queries that look like they have absolutely nothing to do with temporary data.

## Why "you cannot back up tempdb" is actually a feature, not a flaw

A very common beginner question is "how do I back up tempdb, just in case?" The honest answer is: you don't, and you structurally cannot, because tempdb is recreated completely from scratch, based on the `model` database, every single time SQL Server starts up. Its entire contents are destroyed on every restart. Since nothing inside tempdb is ever expected to survive a restart anyway, there is nothing meaningful to preserve through a backup. This is precisely why "restoring tempdb" is not a real, documented procedure. Instead of backing it up, the DBA's job is to configure it well and monitor it actively while it is running, since it is shared across literally everyone using the instance.

## Why tempdb can become an entire instance's bottleneck

Because every single connection and every single database on the instance shares this exact same tempdb, a poorly performing or poorly sized tempdb does not just slow down one user's query. It can slow down every single query running on the entire server at the same time, because everyone is fighting over the same shared resource. This is one of the most well known, classic SQL Server performance problems, often discussed under the term "tempdb contention."

## Real world DBA best practices for tempdb

- **Pre-size tempdb appropriately, and use sensible autogrowth settings**, instead of leaving it at tiny default sizes that force frequent, disruptive autogrowth events under real workload.
- **Use multiple, equally sized data files for tempdb**, with equal growth settings on each one. A widely recommended practice is one data file per logical CPU core, up to a commonly cited maximum of eight files; if your server has more than eight logical processors, you generally do not need more than eight tempdb data files. Since SQL Server 2016, this file count is even configurable directly during installation, reflecting how important this setting is considered.
- **Place tempdb on fast, dedicated storage whenever possible**, ideally not sharing the same physical disk as your busiest user databases, so that tempdb activity does not compete for disk access with your actual production data.
- **Monitor tempdb space usage and growth events actively.** Frequent unexpected growth often signals a deeper issue: a poorly written query causing a large, unexpected sort or spill, or a sudden, real increase in workload.
- **Do not shrink tempdb as a routine habit.** It will typically just grow back under normal usage, and the shrink operation itself can cause its own performance disruption while it runs, often for very little lasting benefit.

## Real world use case: the mystery slowdown

Imagine an entire SQL Server instance, hosting many different unrelated databases for many different departments, suddenly becomes sluggish for everyone, across every single database, all at the same time. No single user database shows an obvious problem. This pattern, a slowdown affecting everything at once with no single obvious culprit, is a classic symptom that should make an experienced DBA immediately suspect tempdb contention or tempdb space pressure, precisely because tempdb is the one resource genuinely shared by absolutely everything on that instance.

## Official Microsoft resources

- [tempdb Database](https://learn.microsoft.com/en-us/sql/relational-databases/databases/tempdb-database?view=sql-server-ver17)
- [Optimizing tempdb performance](https://learn.microsoft.com/en-us/sql/relational-databases/databases/tempdb-database?view=sql-server-ver17#optimizing-tempdb-performance)
- [System Databases (SQL Server)](https://learn.microsoft.com/en-us/sql/relational-databases/databases/system-databases?view=sql-server-ver16)

## Recommended YouTube learning

- **Brent Ozar Unlimited**: search "Brent Ozar tempdb" for some of the most widely respected, practical, real world guidance on configuring and troubleshooting tempdb that exists for free on YouTube.
- **Kudvenkat**: search his playlist for "tempdb SQL Server" for the foundational beginner explanation.

## Quick recap before moving on

- tempdb is a shared, instance-wide scratch space used for both your own explicit temporary objects and SQL Server's own internal work, like sorting and joins.
- It is rebuilt from scratch, from the model database, on every single SQL Server restart, which is why it cannot be backed up or restored, and does not need to be.
- Because it is shared by everyone, a poorly configured or overloaded tempdb can bottleneck the entire instance, not just one database.
- Good practice includes pre-sizing it properly, using multiple equally sized files, placing it on fast storage, and actively monitoring it, rather than routinely shrinking it.

Next: [`06-resource-database-and-summary.md`](06-resource-database-and-summary.md)
