# 2.2 The master Database

## The real world example

Imagine an apartment building's main office, the one holding the master list of every single apartment in the building, who lives there, which keys exist, and where the building's own electrical and water shutoffs are located. If that office's records are destroyed, the building itself, its walls, its apartments, might be physically fine, but nobody can prove who lives where, nobody can find the right shutoff valve in an emergency, and the front desk cannot tell visitors which apartment to go to.

`master` is that office for a SQL Server instance.

## What master actually stores

`master` is the most critical system database in the entire instance. It records instance-wide information, not information about any one specific user database, but information about the instance as a whole, including:

- **Logins**: the list of who is allowed to connect to this instance at all (note: this is different from database-level "users," a distinction covered in later security-focused modules)
- **System configuration settings**: instance-wide settings, configured through tools like `sp_configure`
- **Linked servers**: connections SQL Server has been told it can use to reach other database servers
- **Endpoints**: network endpoints used for things like database mirroring
- **The location of every other database**: critically, `master` keeps a record of where every other database's files physically live on disk, even though the actual data for those databases lives in their own separate files

This last point is worth sitting with for a moment, because it explains why `master` is so important. SQL Server does not "remember" your databases by magic. It looks them up, on every startup, using the records stored inside `master`. If those records are gone or corrupted, SQL Server may not be able to find your databases at all, even if the actual data files are sitting safely, untouched, on disk.

## Why losing master is so dangerous

Picture losing the apartment building's master office records, while every individual apartment is still physically standing, untouched, fully furnished. The apartments did not disappear, but nobody, not even the building's own staff, can officially confirm which apartment belongs to which resident, or where the master keys are kept. Recovering from that is possible, but it is a stressful, manual, and entirely avoidable mess.

This is exactly why official Microsoft guidance, and every experienced DBA's personal rule, is the same: **back up master regularly, and back it up immediately whenever you create, modify, or drop a database.** A change like creating a new database is recorded in master, so a master backup taken before that change is now out of date with reality. Microsoft's own documentation explicitly states that the master database should be backed up whenever a user database is created, modified, or dropped.

## Rules and good practices around master

- **Never create your own tables, stored procedures, or other user objects directly inside master.** It is technically possible, but it pollutes the one database the entire instance depends on with content that has nothing to do with instance-level configuration, and it is widely considered a serious anti-pattern.
- **Never set the TRUSTWORTHY property of master to ON.** By default, this setting is OFF, and it should stay that way. Turning it on causes SQL Server to trust code inside master more than it normally would, which is a meaningful security risk.
- **Always keep a recent backup of master.** If your instance has master corrupted with no usable backup, you may be forced into a full system database rebuild, a real and disruptive recovery procedure documented by Microsoft, requiring you to reapply collation settings, hotfixes, and configuration manually afterward.
- **Permission to create databases should be limited.** Because creating a database is recorded in master, and because master's integrity matters so much, Microsoft recommends that the permission to create new databases on an instance is typically limited to a small number of trusted logins.

## A small but important technical fact

The CREATE DATABASE statement, which you will use heavily in Module 3, must run in autocommit mode, meaning it cannot be wrapped inside an explicit transaction along with other statements. This is a direct consequence of how seriously SQL Server treats the act of creating a database, an action that immediately touches `master`.

## Official Microsoft resources

- [System Databases (SQL Server)](https://learn.microsoft.com/en-us/sql/relational-databases/databases/system-databases?view=sql-server-ver16)
- [Create a Database (SQL Server)](https://learn.microsoft.com/en-us/sql/relational-databases/databases/create-a-database?view=sql-server-ver16)
- [Rebuild System Databases (SQL Server)](https://learn.microsoft.com/en-us/sql/relational-databases/databases/rebuild-system-databases?view=sql-server-ver17)

## Recommended YouTube learning

- **Kudvenkat**: search his playlist for "master database SQL Server" for a visual, beginner-paced walkthrough.

## Quick recap before moving on

- master holds instance-wide metadata: logins, configuration, linked servers, and the location of every database on the instance.
- If master is lost or corrupted, the instance may fail to start, or other databases may become unreachable, even if their own data files are intact.
- Never create user objects inside master, never turn TRUSTWORTHY on for master, and always keep master's backup current, especially right after creating, altering, or dropping any database.

Next: [`03-model-database.md`](03-model-database.md)
