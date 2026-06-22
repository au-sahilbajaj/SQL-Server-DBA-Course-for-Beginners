# 3.2 Filegroups and File Types

This is one of those topics that beginners can technically skip and still get a database working, but understanding it properly is what separates someone who can click through a wizard from someone who can actually design a database for real, growing, production use.

## The real world example: a warehouse with labeled storage zones

Imagine a large warehouse storing inventory for an online retailer. The warehouse manager does not just throw every single box anywhere there happens to be floor space. Instead, the warehouse is divided into clearly labeled zones: a zone for fast moving, frequently picked items near the loading dock, a zone for slow moving archive stock further back, and a completely separate, secured area for high value items. Each zone might even be served by different equipment, faster forklifts near the busy zone, slower equipment further back, because the traffic patterns and needs are different in each zone.

A **filegroup**, in SQL Server, is exactly this idea: a logical zone you define, which one or more physical data files belong to, letting you organize and, in some cases, physically separate your data based on how it is actually used.

## The actual physical file types you will encounter

Every SQL Server database is built from at least two kinds of operating system files:

- **Primary data file**, extension `.mdf`: every database has exactly one of these. It contains the startup information for the database, the system tables and metadata, and it points to the locations of all the other files belonging to the database. Think of it as the warehouse's main entrance and front office, the one place that always exists and that always knows where everything else is.
- **Secondary data file(s)**, extension `.ndf`: optional additional data files. A database can have zero, one, or many of these. They hold additional data, spread out across additional files, often for performance or organizational reasons.
- **Log file**, extension `.ldf`: every database has at least one of these. It records every transaction so that SQL Server can guarantee data integrity and support recovery. Important technical note: SQL Server does not allow you to place transaction log files inside a filegroup at all. Filegroups are strictly a data file concept; the log is managed completely separately.

## The filegroups themselves

- **PRIMARY filegroup**: this is the default filegroup, automatically created with every database, and it always contains the primary data file (.mdf). Unless you explicitly say otherwise, all of your system objects, and any user objects you create without specifying a different filegroup, live here.
- **User-defined filegroups**: these are filegroups you, the DBA, deliberately create yourself, typically to hold one or more secondary data files (.ndf). Common real reasons to create your own filegroups include: separating a huge archive table away from your actively used, frequently queried tables, or placing a very large table's data on a particular, faster physical disk separate from the rest of the database.
- **FILESTREAM filegroup**: a special type of filegroup used specifically to store unstructured, large binary data, like documents, images, or video files, in a way that integrates with the database while actually storing the underlying bytes more efficiently on the regular file system.
- **Memory-optimized filegroup**: a special filegroup required specifically to support In-Memory OLTP, a feature for extremely high performance tables that live primarily in memory. If you want to create memory-optimized tables, you are required to create this special kind of filegroup first.

## Why a DBA would deliberately use multiple filegroups

Let's return to the warehouse zones idea with a concrete, real example. Imagine an e-commerce company's `OrdersDB` database has two very different kinds of tables:

- A `CurrentOrders` table, which is constantly read from and written to by live customers checking out and tracking shipments, all day, every day
- A `HistoricalOrdersArchive` table, holding ten years of old, completed orders, rarely queried, mostly kept around for occasional reporting and legal compliance

A DBA might deliberately place `CurrentOrders` on a filegroup backed by very fast SSD storage, since it is hit constantly, while placing `HistoricalOrdersArchive` on a separate filegroup backed by cheaper, slower storage, since it is rarely accessed and does not need premium performance. This gives real cost savings and real performance isolation, the busy table is never competing for the same physical disk resources as the rarely touched archive table.

Another common, very practical reason for multiple filegroups is backup and restore flexibility. SQL Server supports backing up and restoring individual filegroups separately from the rest of the database, which means in some advanced disaster recovery designs, a DBA can choose to restore only the most business-critical filegroup first, getting core operations back online faster, and restore a lower priority, "nice to have" filegroup afterward.

## How proportional fill works, in simple terms

When a filegroup contains more than one data file, SQL Server does not just fill them up one at a time, completely filling the first file before touching the second. Instead, it uses a strategy called **proportional fill**, writing new data across all the files in that filegroup in proportion to how much free space each one currently has. If you give all the files in a filegroup the same starting size and the same growth settings, this naturally tends to keep them filling up at a similar, balanced rate over time, which is exactly why best practice for things like tempdb (covered in Module 2) is to use multiple files of equal size with equal growth settings.

## Official Microsoft resources

- [Database Files and Filegroups (SQL Server)](https://learn.microsoft.com/en-us/sql/relational-databases/databases/database-files-and-filegroups?view=sql-server-ver17)
- [Add Data or Log Files to a Database](https://learn.microsoft.com/en-us/sql/relational-databases/databases/add-data-or-log-files-to-a-database?view=sql-server-ver17)
- [CREATE DATABASE (Transact-SQL)](https://learn.microsoft.com/en-us/sql/t-sql/statements/create-database-transact-sql?view=sql-server-ver17)
- [Page and extent architecture guide](https://learn.microsoft.com/en-us/sql/relational-databases/pages-and-extents-architecture-guide?view=sql-server-ver17)

## Recommended YouTube learning

- **Kudvenkat**: search his playlist for "filegroups SQL Server" for a focused, beginner-friendly visual explanation.
- **Brent Ozar Unlimited**: search "Brent Ozar filegroups" for more advanced, real production reasoning about when multiple filegroups genuinely help versus when they just add unnecessary complexity.

## Quick recap before moving on

- A filegroup is a logical zone made up of one or more physical data files, similar to labeled storage zones in a warehouse.
- Every database has a primary data file (.mdf) in the default PRIMARY filegroup, and at least one log file (.ldf), which can never live inside any filegroup.
- Secondary data files (.ndf) can be added to the PRIMARY filegroup or to user-defined filegroups you create yourself.
- Real reasons to use multiple filegroups include performance isolation (fast storage for busy tables, slower storage for archives) and more flexible, prioritized backup and restore strategies.
- Proportional fill spreads new data across multiple files in a filegroup based on their relative free space, which is why equally sized files with equal growth settings is standard best practice.

Next: [`03-database-configuration-settings.md`](03-database-configuration-settings.md)
