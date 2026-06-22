# 2.3 The model Database

## The real world example

Imagine a school district that has a standard "new student enrollment form" template. Every time a new student joins any school in the district, the front office photocopies this exact template and fills it in with that one student's specific details. If the district office changes the template itself, every newly enrolled student from that point forward gets the new version. Students who already enrolled under the old template are unaffected: their paperwork stays exactly as it was filled in at the time.

`model` is that template, for every new database created on a SQL Server instance.

## What model actually does

Whenever you run a CREATE DATABASE statement, SQL Server does not build your new database completely from nothing. It copies the contents of `model`, including database options, default settings, and any objects you have placed inside it, into your brand new database, and only then applies whatever specific options you provided in your CREATE DATABASE statement on top of that.

This means if you, as the DBA, modify `model` itself, for example by adding a default table that you want every single new database on this instance to automatically include, every database created afterward will already contain that table the moment it is created. Databases that already existed before you made that change are completely unaffected, exactly like students who already enrolled under the old paperwork template.

## A very important, often overlooked detail: tempdb depends on model

Here is a fact that surprises a lot of beginners and explains why `model` is more critical than its small size would suggest: every time SQL Server starts up, it recreates `tempdb` completely from scratch, and it builds that fresh copy of `tempdb` using `model` as the template. This means `model` must always exist and must always be healthy for SQL Server to start up successfully at all. A problem with `model` is not just "new databases might fail," it can mean "the instance itself cannot start."

## Real world use case: enforcing a company standard

Imagine you work at a company where every single database created on a particular instance is required, by company policy, to include a specific auditing table that records who created the database and when, for compliance reasons. Instead of remembering to manually add that table every single time someone creates a new database, an experienced DBA modifies `model` once, adding that table and any default permissions needed. From that point forward, every new database is born already compliant, with zero extra manual effort, because it inherited that table straight from the template.

This is the real, practical value of understanding `model` deeply: it lets a DBA enforce organization-wide standards automatically, instead of relying on every team member remembering a manual checklist every single time.

## A practical gotcha worth knowing early

If any other connection currently has an active session open against the `model` database itself at the exact moment you try to create a new database, that CREATE DATABASE operation can fail. This is a real, documented behavior, and it is one of those small surprises that can confuse a beginner during early hands-on practice. If your CREATE DATABASE command seems to hang or fail unexpectedly, checking whether something is actively connected to `model` is a reasonable first thing to check.

## Official Microsoft resources

- [System Databases (SQL Server)](https://learn.microsoft.com/en-us/sql/relational-databases/databases/system-databases?view=sql-server-ver16)
- [Create a Database (SQL Server)](https://learn.microsoft.com/en-us/sql/relational-databases/databases/create-a-database?view=sql-server-ver16)
- [tempdb Database](https://learn.microsoft.com/en-us/sql/relational-databases/databases/tempdb-database?view=sql-server-ver17)

## Recommended YouTube learning

- **Kudvenkat**: search his playlist for "model database SQL Server" for a visual walkthrough showing exactly how a change to model is inherited by a newly created database.

## Quick recap before moving on

- model is the template every newly created database is copied from, including its settings and any objects you place inside it.
- Changing model only affects databases created afterward, never databases that already exist.
- tempdb itself is rebuilt from model on every SQL Server startup, which means model must always exist and stay healthy for the instance to start successfully.
- An open connection to model at the exact moment of a CREATE DATABASE attempt can cause that creation to fail.

Next: [`04-msdb-database.md`](04-msdb-database.md)
