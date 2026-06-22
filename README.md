# SQL Server DBA Course for Beginners

This repository is a free, structured learning path for anyone who wants to go from "I have never opened SQL Server" to "I understand how a Database Administrator thinks." It is written in plain language, with real world comparisons, and every technical claim is backed by an official Microsoft Learn link wherever possible.

This is not a copy paste of Microsoft documentation. It is a teaching layer built on top of it. Read the explanation here first, then click through to Microsoft Learn to confirm the details and go deeper.

## Who this is for

You are a complete beginner. You may have used Excel, or written a little bit of code, but you have never managed a real database server. You want to become a SQL Server Database Administrator (DBA) and you need someone to explain things the way you would explain them to a friend, not the way a textbook explains them.

## How to use this repository

Go through the folders in numeric order. Do not skip ahead, because later modules assume you understood the earlier ones. Each folder is one topic area, and inside each folder the files are numbered in the order you should read them.

```
sql-server-dba-course/
├── README.md                                  <- you are here
├── 01-introduction-and-architecture/
│   ├── 01-overview-of-sql-server.md
│   ├── 02-instance-vs-database.md
│   ├── 03-architecture-components.md
│   ├── 04-why-is-it-called-relational.md
│   ├── 05-how-the-engine-works.md
│   ├── 06-memory-cpu-disk-explained.md
│   └── 07-sql-server-versions.md
├── 02-system-databases/
│   ├── 01-overview-of-system-databases.md
│   ├── 02-master-database.md
│   ├── 03-model-database.md
│   ├── 04-msdb-database.md
│   ├── 05-tempdb-database.md
│   └── 06-resource-database-and-summary.md
├── 03-database-creation-and-settings/
│   ├── 01-creating-databases.md
│   ├── 02-filegroups-and-file-types.md
│   ├── 03-database-configuration-settings.md
│   └── 04-requirements-setup-and-limitations.md
└── 04-sql-server-management-studio/
    ├── 01-what-is-ssms.md
    ├── 02-why-we-use-ssms.md
    ├── 03-ssms-versions.md
    ├── 04-options-and-features-in-ssms.md
    ├── 05-most-common-dba-options.md
    └── 06-where-to-get-it-and-install.md
```

## What you will be able to do after finishing this

1. Explain what SQL Server actually is, in your own words, to someone who has never heard of it.
2. Explain the difference between an instance and a database, and why that distinction matters for a DBA's job.
3. Explain, in simple terms, what happens inside SQL Server between the moment a query is sent and the moment data comes back.
4. Know what the four core system databases do, and why a DBA treats them with extra respect.
5. Create a new database correctly, with the right files, filegroups, and settings, instead of just clicking "OK" on every default.
6. Know which SQL Server edition and version you are dealing with, and what limits apply to it.
7. Install and confidently navigate SQL Server Management Studio (SSMS), the primary tool a DBA uses every single day.

## A note on real world examples

Every concept in this course is paired with at least one real world, plain language example. The goal is that a layman, someone with no IT background at all, can read the example and get the right mental picture before we attach the technical name to it.

## A note on official sources

Wherever Microsoft has official documentation on a topic, this course links to it directly from learn.microsoft.com. Microsoft's documentation changes over time as new SQL Server versions are released, so if a link ever looks outdated, search for the page title directly on [Microsoft Learn](https://learn.microsoft.com/en-us/sql/) to find the current version.

## License and usage

Feel free to fork this, translate it, extend it, and use it to teach others. Knowledge about databases should not be locked behind a paywall.

## Next step

Start here: [`01-introduction-and-architecture/01-overview-of-sql-server.md`](01-introduction-and-architecture/01-overview-of-sql-server.md)
