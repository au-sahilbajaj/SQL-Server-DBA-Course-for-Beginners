# 3.1 Creating Databases

This lesson covers what actually happens when you create a database, in plain language first, and then mapped onto the real tools and settings a DBA uses.

## The real world example: building a new house, not just naming it

A common beginner mistake is to think of "creating a database" the same way you think of "creating a folder" on your computer, just typing a name and pressing enter. In reality, creating a database is closer to building a brand new house on an empty plot of land. You are not just choosing a name. You are deciding the size of the foundation, where the plumbing goes, where the storage rooms are, and how much room to leave for future expansion. Get these decisions wrong at the start, and you will be doing expensive renovation work later, sometimes while the house is already occupied.

## The two ways to create a database

There are two main ways a DBA creates a database in SQL Server:

1. **Using SQL Server Management Studio (SSMS)**, the graphical tool: right-click the "Databases" folder, choose "New Database," type a name, and either accept the defaults or walk through the various configuration pages (file settings, filegroups, options) before clicking OK.
2. **Using a T-SQL script**, with the `CREATE DATABASE` statement: writing out the exact specification of the database, its files, sizes, and growth settings, as a piece of code that you run.

As a beginner, using SSMS's visual wizard is a perfectly fine way to learn what the available options even are. But as you grow into a working DBA, you will overwhelmingly prefer the scripted T-SQL approach for real production work, because a script is repeatable, reviewable by a teammate before running, and can be safely run identically across multiple environments (for example, first on a test server, then later on the real production server), whereas clicking through a wizard by hand is not reliably repeatable.

## What actually gets created underneath a simple CREATE DATABASE

Even the simplest possible database creation, just providing a name and accepting every default, results in SQL Server creating, at minimum, two physical files on disk:

- **A primary data file**, conventionally given the file extension `.mdf`, which is where your actual tables and data begin
- **A transaction log file**, conventionally given the file extension `.ldf`, which records every change made to the database, supporting recovery and transactional integrity

If you do not explicitly specify a transaction log file yourself, SQL Server automatically creates one for you anyway, sized at twenty five percent of the total size of all your data files combined, or 512 KB, whichever is larger, placed in the default log file location for that instance.

## Real world example, walked through end to end

Imagine you are setting up a brand new database for a small online bookstore, to be called `BookstoreDB`. Here is the thinking a careful DBA goes through, even before touching a keyboard:

- **How big is this likely to get?** A small bookstore's catalog and order history is unlikely to be enormous initially, but you do not want to set the initial size so small that SQL Server has to keep automatically growing the file every few minutes under normal daily use, since each of those automatic growth events causes a brief performance hiccup and can fragment the file on disk.
- **Where should the files physically live?** On a real production server, a careful DBA places data files and log files on physically separate disks where possible, since data files are read and written somewhat randomly across the whole file, while the transaction log is written sequentially, and separating them avoids the two competing for the same physical disk heads or I/O bandwidth.
- **What name should I give the logical files?** Microsoft's own naming convention, and most real company conventions, is to give files clear, descriptive logical names, for example `BookstoreDB_Data` and `BookstoreDB_Log`, rather than leaving cryptic auto-generated defaults, because on a server hosting hundreds of files, a clear naming convention saves enormous time during troubleshooting later.

## Why "make the file as large as possible" is genuine Microsoft guidance, not overkill

A beginner might assume it is safer to start small and let the file grow automatically as needed. Microsoft's own documentation actually advises the opposite: when you create a database, make the data files as large as you reasonably expect the maximum amount of data to become, based on your real anticipated workload. The reasoning is that each individual autogrowth event has a real performance cost, however small, and a database that grows in many small increments over its life can end up with its physical file more fragmented on disk than a database that was sized thoughtfully from day one. Starting appropriately large, and configuring sensible additional growth settings as a safety net rather than as the primary growth strategy, is the more professional approach.

## A genuine, real world limit you should know

A single SQL Server instance can have a maximum of 32,767 databases. In practice, almost no real organization gets anywhere near this number on a single instance, but it is a good example of the kind of hard limit a DBA should at least be aware exists, rather than be surprised by.

## Permissions required to create a database

Creating a database is not something every login on an instance can do by default, and this is intentional. To create a database, a login generally needs the `CREATE DATABASE` permission specifically within `master`, or broader permissions such as `CREATE ANY DATABASE` or `ALTER ANY DATABASE`. Because creating databases consumes disk space and instance resources, and because (as you learned in Module 2) it directly touches the critical `master` database, real organizations typically restrict this permission to a small number of trusted DBA logins, rather than granting it broadly.

## Official Microsoft resources

- [Create a Database (SQL Server)](https://learn.microsoft.com/en-us/sql/relational-databases/databases/create-a-database?view=sql-server-ver16)
- [CREATE DATABASE (Transact-SQL)](https://learn.microsoft.com/en-us/sql/t-sql/statements/create-database-transact-sql?view=sql-server-ver17)
- [Databases (SQL Server)](https://learn.microsoft.com/en-us/sql/relational-databases/databases/databases?view=sql-server-ver17)
- [View or change the default locations for data and log files](https://learn.microsoft.com/en-us/sql/database-engine/databases/view-or-change-the-default-locations-for-data-and-log-files?view=sql-server-ver17)

## Recommended YouTube learning

- **Kudvenkat**: search his playlist for "create database SQL Server" for a clear, beginner-paced walkthrough using both SSMS and T-SQL.
- **MSSQLTips**: their related YouTube content and written tutorials on "SQL Create Database" walk through realistic, practical examples including multiple filegroups, useful once you have finished this module.

## Quick recap before moving on

- Creating a database is more like building a house than naming a folder: you are deciding foundation size, file locations, and growth strategy, not just a name.
- Even the simplest database creation produces at minimum a primary data file (.mdf) and a transaction log file (.ldf).
- Microsoft's own guidance is to size data files thoughtfully and large up front, rather than relying on frequent small autogrowth events.
- A single instance can hold a documented maximum of 32,767 databases, and the permission to create databases is rightly treated as a privileged, restricted action.

Next: [`02-filegroups-and-file-types.md`](02-filegroups-and-file-types.md)
