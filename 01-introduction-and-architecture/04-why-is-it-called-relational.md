# 1.4 Why Is It Called "Relational"?

This question trips up almost every beginner, because the everyday meaning of "relational" (like "relationship between people") makes you expect something emotional or social. In databases, "relational" has a precise, almost mathematical meaning, but we can build up to it with something everyone already understands.

## Start with something everyone has used: a school

Imagine a school. The school keeps records in three separate lists, like three separate notebooks:

**Notebook 1, Students**

| StudentID | Name | ClassID |
|---|---|---|
| 1 | Maya | 101 |
| 2 | Liam | 102 |
| 3 | Noah | 101 |

**Notebook 2, Classes**

| ClassID | ClassName | TeacherID |
|---|---|---|
| 101 | Mathematics | 9 |
| 102 | History | 12 |

**Notebook 3, Teachers**

| TeacherID | Name |
|---|---|
| 9 | Mrs. Patel |
| 12 | Mr. Ramirez |

Notice something important. The Students notebook does not repeat the teacher's name, the classroom number, or the subject syllabus next to every single student. Instead, it just stores a small reference number, the `ClassID`, and that number points over to the Classes notebook, which itself just stores a small reference number, the `TeacherID`, pointing over to the Teachers notebook.

This is exactly what "relational" means. The tables are **related** to each other through shared values (those ID numbers), instead of every table storing every fact about everything inside itself. The "relation" is the link between `Students.ClassID` and `Classes.ClassID`. That link is the "relationship."

## Why not just put everything in one giant notebook

You might ask, why not just write the teacher's name directly next to every student? The problem appears the moment something changes. Imagine Mrs. Patel gets married and changes her last name. If her name had been copy pasted into every single student's row across the entire school, you would now need to find and fix her name in hundreds of different places, and if you miss even one row, your data becomes inconsistent: some rows say "Patel" and some say her new name, and now nobody can trust the data.

By keeping Teachers as its own separate notebook, and only storing a reference number elsewhere, you only ever need to update her name in exactly one place, the Teachers notebook. Every student record automatically "sees" the correct, current name because it just follows the reference number over to wherever the true, single copy of that name lives.

This idea, store each fact exactly once, and connect tables through shared reference values, is called **normalization**, and it is the entire foundation of why relational databases are organized the way they are.

## The technical name for that reference number

In SQL Server (and any relational database), the "ID number that uniquely identifies a row" is called a **primary key**. The column in another table that holds a copy of that ID, pointing back to the original row, is called a **foreign key**. In our example, `Classes.ClassID` is the primary key of the Classes table, and `Students.ClassID` is a foreign key, pointing back to it.

This pair, primary key and foreign key, is the literal mechanical implementation of a "relation" between two tables. When you hear someone say "these two tables are related," they specifically mean there is a primary key to foreign key link connecting them.

## Where the actual word "relational" came from

This is worth knowing because it surprises most beginners: the word "relational" does not come from the everyday idea of "things relating to each other" at all. It comes from mathematics. In 1970, a researcher named Edgar F. Codd, working at IBM, published a paper describing data as mathematical "relations," a formal term from set theory that essentially just means "a table of rows, where each row is a tuple of values." Codd's relational model became the foundation for every relational database, including SQL Server, Oracle, PostgreSQL, and MySQL, that exists today. So technically, "relational" originally described the mathematical structure of a single table, and the everyday idea that "tables relate to each other through keys" came along as a natural and very useful consequence of that structure.

For teaching beginners, the school notebook example above is the practical, intuitive version of this idea, and it is enough to build correct intuition. If a curious student asks "where did the word actually come from," you now also have the precise historical answer.

## The real world payoff of this design

Because data is organized this way, you can ask SQL Server very natural questions like "show me every student in Mrs. Patel's classes," and the database engine will automatically follow the chain of keys, Teachers to Classes to Students, and assemble the answer for you. This act of pulling related tables back together using their key relationships is called a **JOIN**, and it is one of the very first practical SQL skills any DBA or developer learns.

## Official Microsoft resources

- [Database Engine technical documentation](https://learn.microsoft.com/en-us/sql/sql-server/?view=sql-server-ver17)
- [Database design basics](https://learn.microsoft.com/en-us/office/troubleshoot/access/database-design-basics) (this is an Access page, but Microsoft's own explanation of relational design concepts such as primary keys, foreign keys, and normalization applies the same way to SQL Server)

## Recommended YouTube learning

- **Kudvenkat**, search his playlist specifically for videos titled around "primary key," "foreign key," and "normalization." He builds the exact school style example with simple tables on a whiteboard, which pairs very well with the explanation above.

## Quick recap before moving on

- "Relational" means tables are connected to each other through shared key values, not that everything is crammed into one giant table.
- A primary key uniquely identifies a row in its own table. A foreign key is a copy of that key sitting in another table, pointing back to it.
- This design avoids repeating the same fact in many places, which avoids data becoming inconsistent when something changes.
- The word "relational" actually comes from a 1970 mathematical paper by Edgar F. Codd, describing tables as mathematical "relations."

Next: [`05-how-the-engine-works.md`](05-how-the-engine-works.md)
