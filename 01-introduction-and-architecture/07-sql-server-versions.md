# 1.7 SQL Server Versions

A beginner DBA needs to be comfortable with one confusing but very common reality: SQL Server has both a marketing year name (like "SQL Server 2022") and an internal version number (like "16.x"), and you will see both used constantly in real jobs, in error messages, and in documentation. This lesson untangles that.

## The real world example: car model years

Think about how cars are named. A "2024 Honda Civic" and a "2025 Honda Civic" are both Civics, the same basic product line, but the year tells you which generation and feature set you are dealing with. Under the hood, the manufacturer also tracks an internal model code that engineers use, which does not match the marketing year at all.

SQL Server works exactly the same way. "SQL Server 2022" is the marketing name, the one you will see in job postings and conversations. Underneath that, SQL Server 2022 is internally version 16.x. When you run a diagnostic command against a live server, it often reports the internal version number, not the marketing year, so you need to know how to translate between the two.

## Major recent versions and what changed

Here is a simplified timeline of the versions you are realistically likely to encounter in a real job today, from oldest still commonly seen to newest.

- **SQL Server 2016 (internal version 13.x)**: introduced Query Store (a built-in tool that tracks how query performance changes over time), Always Encrypted (a way to keep sensitive columns encrypted even from database administrators), and native JSON support.
- **SQL Server 2017 (internal version 14.x)**: the landmark release where SQL Server became available on Linux for the first time, not just Windows. Also added adaptive query processing improvements.
- **SQL Server 2019 (internal version 15.x)**: added Big Data Clusters, expanded intelligent query processing, and PolyBase enhancements for querying data sitting outside of SQL Server itself.
- **SQL Server 2022 (internal version 16.x)**: deepened integration with Azure (Microsoft's cloud platform), added the Link feature for near real time replication to Azure SQL Managed Instance, and introduced Ledger, a tamper-evidence feature using blockchain-style verification.
- **SQL Server 2025 (internal version 17.x)**: the current version as of this writing, released November 2025, continuing the trend of deeper AI and Azure integration alongside core engine improvements.

You will likely meet plenty of servers still running SQL Server 2016, 2017, or 2019 in real companies, because upgrading a production database server is a careful, sometimes slow process, and many organizations run "older but still officially supported" versions for years before upgrading.

## How to actually check what version a server is running

As a DBA, one of the very first things you do when you connect to any unfamiliar server is check its exact version, edition, and patch level. You do this by running a query against a special system function. We are intentionally not showing T-SQL code blocks in this particular lesson, but the function you will look up and use is called `SERVERPROPERTY`, and the official Microsoft documentation page below explains exactly how to use it to retrieve the product version, product level, and edition in one go.

## Editions, a separate concept from version year

It is important not to confuse "version" (the year, like 2022) with "edition" (the feature tier, like Standard or Enterprise). Within any single version year, Microsoft sells several editions, each with a different price and a different set of features and limits. The most important ones to know as a beginner are:

- **Enterprise**: the full feature set, highest cost, intended for large, mission-critical production systems
- **Standard**: a solid mid-tier edition with most core features, but with stricter limits on things like CPU cores and memory that the Database Engine can use, and missing some of the most advanced Enterprise-only features
- **Developer**: contains all the same features as Enterprise edition, completely free, but its license only permits using it for development and testing, never for a live production system
- **Express**: a small, free edition with real limits, for example a maximum database size, intended for learning, small applications, and proof of concept work, not for serious production workloads
- **Web**: a lower cost edition specifically licensed for web hosting providers (note that, going forward, Web edition is not available starting with SQL Server 2025 and later)

As a beginner setting up a learning environment, Developer edition is almost always the right choice: full features, completely free, as long as you are not running real production traffic on it.

## Why this matters for a working DBA

A huge part of real DBA work is version and edition aware. Certain features simply do not exist on Standard edition that exist on Enterprise edition. Certain security patches only apply to certain versions. Certain capacity limits, the maximum amount of memory the Database Engine itself can use, the maximum number of CPU cores it can use, are different across editions, even running on the exact same physical hardware. We cover these specific limits in detail in Module 3 of this course, in the lesson on requirements, setup, and limitations.

## Official Microsoft resources

- [Latest updates and version history for SQL Server](https://learn.microsoft.com/en-us/troubleshoot/sql/releases/download-and-install-latest-updates)
- [Editions and Supported Features of SQL Server 2022](https://learn.microsoft.com/en-us/sql/sql-server/editions-and-components-of-sql-server-2022?view=sql-server-ver17)
- [Editions and Supported Features of SQL Server 2025](https://learn.microsoft.com/en-us/sql/sql-server/editions-and-components-of-sql-server-2025?view=sql-server-ver17)
- [Compute capacity limits by edition of SQL Server](https://learn.microsoft.com/en-us/sql/sql-server/compute-capacity-limits-by-edition-of-sql-server?view=sql-server-ver17)
- [What's New in SQL Server 2022](https://learn.microsoft.com/en-us/sql/sql-server/what-s-new-in-sql-server-2022?view=sql-server-ver17)
- [SQL Server 2022 product page (Microsoft.com)](https://www.microsoft.com/en-us/sql-server/sql-server-2022)

## Recommended YouTube learning

- **Kudvenkat**: search his playlist for early videos covering "SQL Server editions" and "how to check SQL Server version," which walk through this exact distinction with screenshots from SQL Server Management Studio.
- **Microsoft's official SQL Server YouTube channel**: search "Microsoft SQL Server What's New" for the official short feature walkthrough videos released alongside each new version.

## Quick recap before moving on

- SQL Server has a marketing year name (2022, 2025) and an internal version number (16.x, 17.x). Both refer to the same product, and you need to recognize both.
- Real companies often run older, still-supported versions for years, so do not assume every server you touch will be the newest one.
- Edition (Enterprise, Standard, Developer, Express, Web) is a separate concept from version year, and it controls feature availability and resource limits.
- Developer edition is the correct, free, full-featured choice for your own learning environment.

This completes Module 1. Continue to Module 2: [`../02-system-databases/01-overview-of-system-databases.md`](../02-system-databases/01-overview-of-system-databases.md)
