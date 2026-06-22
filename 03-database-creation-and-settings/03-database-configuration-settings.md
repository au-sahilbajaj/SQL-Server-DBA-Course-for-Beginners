# 3.3 Database Configuration Settings

Once a database exists, it has dozens of configurable properties that control how it behaves. You do not need to memorize all of them today, but a few are so important and so commonly discussed in real DBA work that a beginner must understand them properly from the start.

## The real world example: a car's settings, not just the engine

Think about a modern car. The engine (the storage and relational engine you learned about in Module 1) is the same basic technology across many cars, but each individual car has settings: how sensitive the brakes are, whether automatic emergency braking is on, what driving mode (eco, sport, comfort) is active. These settings do not change what the car fundamentally is, but they meaningfully change how it behaves and how it should be driven.

A database's configuration settings work the same way: same underlying engine, but very different real behavior depending on how these settings are configured.

## Recovery Model: the most important setting a beginner must understand

The **recovery model** is a database-level setting that controls how transactions are logged, whether the transaction log can and must be backed up, and what kinds of restore operations are even possible later. There are three recovery models:

- **Simple**: SQL Server automatically reclaims transaction log space, keeping log management effectively automatic, and you do not need to (and cannot) take transaction log backups. The tradeoff is real and important: if a data file is lost or damaged, you can only restore back to your most recent full or differential backup, not to a precise moment in time. Any changes made since that last backup are lost.
- **Full**: every transaction is fully logged, and transaction log backups are required as part of a real backup strategy. This recovery model allows you to restore a database to a precise point in time, for example "restore to exactly 2:47 PM, one minute before someone accidentally deleted important records," and ensures essentially no committed work is lost, as long as you are taking log backups regularly.
- **Bulk-logged**: similar to Full, but certain large-scale bulk operations are minimally logged for better performance during those specific operations. It still requires transaction log backups, but precise point-in-time recovery becomes limited specifically around when those minimally logged bulk operations occurred.

SQL Server Enterprise and Standard editions default to the Full recovery model, while Express edition defaults to Simple. A real world example makes the stakes obvious: imagine an online banking database under the Simple recovery model, where the last full backup was taken at midnight. If a hardware failure happens at 3 PM, every single transaction since midnight, every deposit, every withdrawal, every transfer made by every customer that day, is permanently lost, because Simple recovery model offers no way to recover that gap. This is exactly why genuinely critical, transactional production systems almost always run under the Full recovery model with regular transaction log backups, despite the slightly higher management overhead involved.

## Collation: how SQL Server compares and sorts text

**Collation** controls how SQL Server compares, sorts, and treats the case-sensitivity of text data. Think of it like choosing which dictionary's sorting rules and which language's alphabet rules to follow, since different languages and regions sort accented characters, capital letters, and special characters differently. Collation can be set at the server level, the database level, and even the individual column level, though mixing collations across these levels carelessly can cause confusing comparison errors later. As a beginner, the main thing to know is: collation is decided early, it is awkward to change later on a live database with real data in it, and it directly affects whether a search for "café" also correctly matches "CAFE" or "Cafe," depending on how case and accent sensitivity were configured.

## AUTO_SHRINK: a setting that sounds helpful but usually is not

`AUTO_SHRINK` is a database option that, when turned on, periodically and automatically shrinks data and log files that contain a meaningful amount of unused free space, specifically when more than twenty five percent of a file is unused. It sounds appealing to a beginner: "why wouldn't I want SQL Server to automatically reclaim wasted space?" In practice, official Microsoft guidance, and near-universal real world DBA consensus, is the opposite: do not turn AUTO_SHRINK on unless you have a very specific, deliberate reason to. The shrink operation itself can be resource intensive while it runs, and a database that genuinely needs that space again soon will simply grow right back, often causing fragmentation in the process, essentially wasting effort shrinking and then immediately regrowing.

## AUTO_CREATE_STATISTICS and AUTO_UPDATE_STATISTICS

These two settings control whether SQL Server is allowed to automatically create new statistics, and automatically refresh existing statistics, about the data in your tables, without you manually asking it to. Recall from Module 1 that the Query Optimizer relies heavily on accurate statistics to choose good execution plans. Leaving these settings on (which is the default, and the broadly recommended setting for the vast majority of real workloads) helps the optimizer keep making good decisions as your data changes and grows over time, without requiring constant manual intervention from a DBA.

## Compatibility Level: running "in character" as an older version

The **compatibility level** setting lets a database behave according to the query processing and language behavior rules of an older version of SQL Server, even while physically running on a much newer SQL Server engine installation. This exists specifically to help organizations upgrade their actual SQL Server installation to a newer, more secure, better-performing version, without being forced to immediately deal with every single subtle behavior change that a newer compatibility level might introduce to existing application queries. A real world example: a company upgrades its physical server from SQL Server 2017 to SQL Server 2022 for better hardware support and security patching, but keeps a particular sensitive legacy database's compatibility level set to match SQL Server 2017's query behavior for a while, giving the application team time to test and adjust before eventually raising the compatibility level to take full advantage of newer query optimizer improvements.

## How a DBA actually changes these settings

In SQL Server Management Studio, you change most of these settings visually through the database's "Properties" window, specifically the "Options" page. In T-SQL, the tool for this is the `ALTER DATABASE ... SET` statement, which can change essentially any one of these settings on an existing, already-created database.

## Official Microsoft resources

- [Recovery Models (SQL Server)](https://learn.microsoft.com/en-us/sql/relational-databases/backup-restore/recovery-models-sql-server?view=sql-server-ver17)
- [ALTER DATABASE SET Options (Transact-SQL)](https://learn.microsoft.com/en-us/sql/t-sql/statements/alter-database-transact-sql-set-options?view=sql-server-ver17)
- [Change the Database Collation](https://learn.microsoft.com/en-us/sql/relational-databases/databases/set-or-change-the-database-collation?view=sql-server-ver17)
- [View or Change the Compatibility Level of a Database](https://learn.microsoft.com/en-us/sql/database-engine/install-windows/view-or-change-the-compatibility-level-of-a-database?view=sql-server-ver17)
- [Change the Configuration Settings for a Database](https://learn.microsoft.com/en-us/sql/relational-databases/databases/change-the-configuration-settings-for-a-database?view=sql-server-ver17)

## Recommended YouTube learning

- **Kudvenkat**: search his playlist for "recovery model SQL Server" for a clear, beginner-paced explanation with visual diagrams of Simple versus Full recovery model behavior.
- **Kendra Little (SQLWorkbooks)**: search "Kendra Little recovery model" or browse her YouTube channel; she has excellent, approachable, free content specifically aimed at DBAs on backup strategy and recovery model decisions.

## Quick recap before moving on

- Recovery model (Simple, Full, Bulk-logged) controls how much data you can recover and how, and it is one of the single most consequential settings a DBA configures.
- Collation controls how text is sorted and compared, and it is awkward to change later, so it deserves careful thought at database creation time.
- AUTO_SHRINK sounds helpful but is generally discouraged; AUTO_CREATE_STATISTICS and AUTO_UPDATE_STATISTICS are generally left on, helping the Query Optimizer make good decisions.
- Compatibility level lets a database run with the behavior of an older SQL Server version even on a newer engine, easing the path through upgrades.

Next: [`04-requirements-setup-and-limitations.md`](04-requirements-setup-and-limitations.md)
