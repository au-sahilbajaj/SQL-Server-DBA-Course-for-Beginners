# 4.5 Most Common Options We Use as a DBA

Lesson 4.4 gave you the full map of SSMS's capabilities. But in real, everyday DBA work, a small handful of these features get used constantly, while many others get opened only occasionally. This lesson is your practical, "what will I actually touch most days" guide.

## The real world example: a doctor's daily bag versus the entire hospital

A hospital contains an enormous range of specialized equipment, MRI machines, surgical robots, blood analysis labs, but a general practitioner doctor's actual daily working bag contains a much smaller, focused set of tools: a stethoscope, a blood pressure cuff, a thermometer, a reflex hammer. These cover the vast majority of daily patient interactions, even though the hospital around them contains far more equipment for rarer, more specialized situations.

This lesson is your DBA's daily working bag, the small set of SSMS tools you will genuinely reach for constantly, pulled out from the much larger hospital of features described in lesson 4.4.

## 1. Object Explorer, used constantly, almost without thinking

You will have Object Explorer open essentially every single time you use SSMS. It becomes the constant backdrop of your work: confirming a table exists, checking a column's data type before writing a query against it, right-clicking a database to quickly check its current size and recovery model, or right-clicking a login to check what permissions it has. Genuine fluency with Object Explorer, knowing where things live in that tree without having to hunt around every time, is one of the clearest signs of an experienced SSMS user versus a true beginner.

## 2. The Query Editor and "New Query," used dozens of times a day

Almost every real diagnostic or administrative task eventually involves writing and running some T-SQL. Whether you are checking a setting, querying a system view to investigate a problem, or running a quick script someone sent you, "New Query" against the right database is probably the single most repeated action in a working DBA's day.

## 3. Execution plans, the DBA's stethoscope

Whenever a query is reported as slow, a DBA's instinctive first move, almost like a doctor reaching for a stethoscope, is to run the query in SSMS and immediately look at its execution plan. This single habit, look at the plan first, before guessing, is one of the fastest ways to separate effective real world troubleshooting from random guesswork.

## 4. Backup and Restore (right-click a database, choose Tasks)

Given everything you learned in Modules 2 and 3 about recovery models and system databases, it should be no surprise that backup and restore operations are an extremely frequent real task. SSMS's graphical Backup and Restore dialogs, accessed by right-clicking a database and choosing Tasks, let a DBA quickly take a one-off backup before making a risky change, or restore a database from a backup file when something has gone wrong, all without writing a backup script from scratch every single time.

## 5. SQL Server Agent Jobs and their history

Recall from Module 2 that msdb stores every scheduled job and its run history. In real practice, checking "did last night's job actually succeed" is one of the very first things many DBAs do at the start of a working day, and SSMS's graphical job history viewer, found by expanding SQL Server Agent in Object Explorer, makes this a quick, routine check rather than a slow investigation.

## 6. Activity Monitor, for "something feels wrong right now"

When a colleague messages you that "the database feels slow," right-clicking the server name in Object Explorer and opening Activity Monitor is one of the fastest ways to get an immediate, live snapshot of what is actually happening: which processes are running, what they are waiting on, and overall resource usage. This is frequently the very first diagnostic step taken in a real, live performance complaint.

## 7. Database Properties, for quick configuration checks

Right-clicking a database and choosing Properties opens a dialog covering exactly the kinds of settings discussed in Module 3: recovery model, collation, compatibility level, file locations, and autogrowth settings. A DBA frequently opens this dialog purely to confirm a setting quickly, rather than always writing a query to check it.

## 8. Security: Logins, Users, and Role memberships

Creating a new login for a new employee, checking which database roles a particular login belongs to, or reviewing who currently has access to a sensitive database, are common, recurring real world tasks, and SSMS's security tree under both the server level (Security > Logins) and the database level (a specific database > Security > Users) is where this work happens visually.

## A genuinely useful habit: keyboard shortcuts worth learning early

A few SSMS keyboard shortcuts are used so frequently by working DBAs that they are worth memorizing early, rather than always reaching for a mouse:

- Executing the currently selected query (commonly F5 or Ctrl+E, depending on version and keyboard scheme)
- Displaying the actual or estimated execution plan for a query, a feature you will use constantly once you start performance troubleshooting
- Commenting and uncommenting blocks of code while drafting or testing a script
- Switching quickly between multiple open query tabs when juggling several investigations at once

Because exact default key bindings can vary slightly between SSMS versions and configured keyboard schemes, the official Microsoft documentation linked below is the most reliable place to confirm the current shortcuts for your specific installed version, rather than relying purely on memory from an older version.

## Official Microsoft resources

- [Quickstart: Connect and query a SQL Server instance using SQL Server Management Studio (SSMS)](https://learn.microsoft.com/en-us/ssms/quickstarts/ssms-connect-query-sql-server)
- [Components and features in SQL Server Management Studio](https://learn.microsoft.com/en-us/ssms/components-features)
- [SQL Server Agent](https://learn.microsoft.com/en-us/sql/ssms/agent/sql-server-agent?view=sql-server-ver17)
- [Back Up and Restore of System Databases (SQL Server)](https://learn.microsoft.com/en-us/sql/relational-databases/backup-restore/back-up-and-restore-of-system-databases-sql-server?view=sql-server-ver17)

## Recommended YouTube learning

- **Brent Ozar Unlimited**: search "Brent Ozar Activity Monitor" and "Brent Ozar execution plans," two of the most practical, frequently recommended free resources in the entire SQL Server community for exactly the daily habits described in this lesson.
- **Kudvenkat**: search his playlist for "SQL Server backup and restore" for a clear, beginner-paced walkthrough of the graphical backup and restore process inside SSMS.

## Quick recap before moving on

- Out of SSMS's enormous feature set, a small, focused group gets used constantly in real DBA work: Object Explorer, the Query Editor, execution plans, backup and restore, SQL Server Agent job history, Activity Monitor, database properties, and security management.
- "Look at the execution plan first" and "check the job history first" are two of the most valuable, repeatable real world habits a new DBA can build early.
- Learning a handful of keyboard shortcuts, especially for running queries and viewing execution plans, meaningfully speeds up daily work once they become second nature.

Next: [`06-where-to-get-it-and-install.md`](06-where-to-get-it-and-install.md)
