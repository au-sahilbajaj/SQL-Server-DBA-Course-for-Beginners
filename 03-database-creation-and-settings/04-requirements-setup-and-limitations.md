# 3.4 Database Requirements, Setup, and Limitations per Version

This final lesson of Module 3 connects everything you have learned back to a very practical, real world question every beginner DBA eventually has to answer: "what are we actually allowed to do, and how big can we actually get, on this specific SQL Server installation?"

## The real world example: rental agreements have different limits

Imagine renting an apartment. A basic, budget studio apartment lease might restrict you to one small storage closet and might not allow major renovations. A premium penthouse lease gives you a private garage, multiple storage rooms, and permission to knock down interior walls if you want. Both are still, technically, apartment leases in the very same building, but the actual limits you operate under are completely different, written clearly into the contract you signed.

SQL Server editions work the same way. "Enterprise," "Standard," "Express," and "Developer," which you first learned about in Module 1, are not just marketing labels. Each one comes with a real, documented contract of hard limits on compute capacity (CPU and memory) and, in some cases, database size, and a real DBA needs to know which "lease" they are actually operating under before making promises about what the system can handle.

## Compute capacity limits by edition

Microsoft publishes an official table of compute capacity limits for each edition of SQL Server, and a few of the most important real numbers to know as a beginner, current as of SQL Server 2022, are:

- **Enterprise edition**: under the modern core-based licensing model, there is no hard limit on the number of CPU cores or the amount of memory the Database Engine can use, scaling with whatever hardware you actually have. (Older Server plus Client Access License, or CAL, based licensing, no longer available for new agreements, was historically limited to 20 cores per instance.)
- **Standard edition**: limited to the lesser of 4 sockets or 24 cores (in SQL Server 2022 and earlier versions), and limited to 128 GB of memory for the Database Engine.
- **Express edition**: a much smaller free tier, limited to the lesser of 1 socket or 4 cores, and limited to 1,410 MB of memory for the Database Engine.

These limits apply to a single instance of SQL Server, and they represent the maximum compute capacity that one instance is allowed to use. Importantly, these limits do not restrict the physical server itself, which is exactly why running multiple separate instances on one large physical server (remember the apartment building analogy from Module 1) is actually a documented, efficient strategy for fully using a powerful server's available sockets and cores, when a single instance's edition would otherwise leave capacity unused.

## Maximum database size by edition

The most dramatic, real world consequence of edition choice is maximum database size:

- **Enterprise, Standard, Web, and Developer editions**: support a maximum database size of 524,272 terabytes, which is so enormous that, in practice, it is not a meaningful constraint for almost any real organization.
- **Express edition**: a hard cap that has changed across versions. Starting with SQL Server 2025, Express edition's maximum database size increased to 10 GB (it was previously 10 GB as of recent prior versions as well, having grown from an even smaller 4 GB limit in older versions like SQL Server 2008 R2 and earlier).

A real world consequence of this: a small business that starts its application on free Express edition, intending to "upgrade later if it grows," genuinely can and does hit this hard size ceiling in practice, especially once they accumulate a few years of real transactional history, and at that point, an edition upgrade (typically to Standard or Enterprise) becomes a forced, real, and sometimes urgent project, not just a nice-to-have improvement.

## Other genuinely useful hard limits to know

Microsoft documents a long list of maximum capacity specifications across the whole Database Engine. As a beginner, here are a few of the most practically relevant ones, beyond database count and size:

- **Columns per table**: a maximum of 1,024 columns in a single table.
- **Bytes per row**: a maximum of 8,060 bytes per row for fixed-length data (though variable-length columns can be pushed off-row using row-overflow storage, which effectively raises the practical limit for those specific data types).
- **Foreign key constraints per table**: while technically unlimited, Microsoft's own documentation recommends a practical maximum of 253, since additional foreign keys add real query optimization cost as that number grows.
- **Instances per server**: up to 50 instances can run on a single stand-alone server. If using a failover cluster (a high availability configuration covered in later modules), the limit is lower: 25 failover cluster instances when using a shared cluster drive, or up to 50 when using SMB file shares as the storage option instead.
- **Databases per instance**: as covered in lesson 3.1, a maximum of 32,767 databases per instance.

## Feature availability differences worth knowing as a beginner

Beyond raw capacity numbers, editions also differ in which features even exist at all. As one practical example relevant to high availability (a topic explored more in later modules of a fuller DBA curriculum), Standard edition supports a more limited form of Always On Availability Groups, called Basic Availability Groups, supporting only two replicas and a single database per availability group, while Enterprise edition supports a far more advanced, flexible configuration, including more secondary replicas, multiple databases per group, and additional readable secondary capabilities. As a beginner, you do not need to master high availability yet, but you should walk away knowing this general truth: it is not safe to assume a feature you read about online, or saw a senior colleague use at a previous company, is automatically available on whatever specific edition you happen to be working with today. Always check the specific edition's documented feature list first.

## A genuinely important, very practical takeaway for a working DBA

When you join a new company, or inherit management of a SQL Server instance you did not originally set up, one of your very first real diagnostic tasks should be confirming exactly which version and edition you are actually dealing with (using the techniques from lesson 1.7), and then immediately cross-referencing that against the official Microsoft capacity and feature tables linked below. Many real, embarrassing production incidents have happened simply because someone assumed Enterprise-level capacity or features were available, when the actual running instance was Standard or even Express edition all along.

## Official Microsoft resources

- [Compute capacity limits by edition of SQL Server](https://learn.microsoft.com/en-us/sql/sql-server/compute-capacity-limits-by-edition-of-sql-server?view=sql-server-ver17)
- [Maximum Capacity Specifications for SQL Server](https://learn.microsoft.com/en-us/sql/sql-server/maximum-capacity-specifications-for-sql-server?view=sql-server-ver17)
- [Editions and Supported Features of SQL Server 2022](https://learn.microsoft.com/en-us/sql/sql-server/editions-and-components-of-sql-server-2022?view=sql-server-ver17)
- [Editions and Supported Features of SQL Server 2025](https://learn.microsoft.com/en-us/sql/sql-server/editions-and-components-of-sql-server-2025?view=sql-server-ver17)
- [Hardware and software requirements for SQL Server](https://learn.microsoft.com/en-us/sql/sql-server/install/hardware-and-software-requirements-for-installing-sql-server?view=sql-server-ver17)

## Recommended YouTube learning

- **Kudvenkat**: search his playlist for "SQL Server editions comparison" for a beginner-paced visual breakdown of edition differences.
- **Brent Ozar Unlimited**: search "Brent Ozar which SQL Server edition" for practical, real world, business-oriented guidance on choosing the right edition for an actual production workload, not just a feature checklist.

## Quick recap, and end of Module 3

- Each SQL Server edition is a real "contract" with hard, documented limits on CPU cores, memory, and in some cases maximum database size, similar to how different apartment leases come with very different real limits.
- Express edition's small size cap is a genuine, real world constraint that forces edition upgrades as small applications and businesses grow.
- Beyond capacity numbers, entire features (like advanced Always On Availability Group configurations) can simply not exist on lower editions, regardless of how much you configure correctly.
- A core habit of a competent DBA is confirming the actual version, edition, and documented limits of any instance you are responsible for, rather than assuming based on past experience elsewhere.

This completes Module 3. You now have a correct, real, in-depth foundation in SQL Server architecture, system databases, and database creation and configuration. Continue to Module 4 to learn the tool you will actually use to do all of this hands-on: [`../04-sql-server-management-studio/01-what-is-ssms.md`](../04-sql-server-management-studio/01-what-is-ssms.md)
