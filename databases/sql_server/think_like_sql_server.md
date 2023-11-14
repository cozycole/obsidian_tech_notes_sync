# How to Think Like the SQL Server Engine (by Brent Ozar)

What if a row can't be stored on an 8kb page? For instance, an NVARCHAR(MAX) of an XML document to be used
by an application. Instead, they are moved off-row and saved on separate pages. This means there is a performance impact
when SQL Server has to jump around to get those items off page. (Which makes select * statements worse)

Clustered index: an ordering of the row page data on disk to speed up retrieval based on the index.
Therefore, since data is stored only once on disk, there can only be one clustered index.
Writes take longer for clustered data, but are fast for retrieval.

## Don't SELECT *

**SELECT * can read more data**. I know what you’re thinking: both queries have to read all of the 8KB pages in the table, right? No – go back to the first post in the series when I introduced the Users table. I mentioned that the AboutMe field, a big ol’ NVARCHAR(MAX), might be so large that it ends up getting pushed off-row: stored in different 8KB pages. Our SELECT Id query didn’t need to read those extra pages – but when we SELECT *, we do.

**SELECT * can sort more data**. SQL Server can’t just sort the LastAccessDates – it sorts the whole rows around. That means it’s going to need more CPU time, more memory, and do more spills to disk if it gets those memory estimates wrong.

**SELECT * can take more time to output**. This was always the thing I focused on as a database administrator. I would tell my devs, “Don’t select fields you don’t need, because it takes longer to yell that data out over the network.” Today, that’s the least of my problems: I’m much, much more concerned about the CPU time and memory grants consumed by the sort operator.

## SQL Server caches raw data pages, not query output

SQL Server re-executes the query again from scratch. So we can mitigate this either by caching the results in the application layer or using a non-clustered index.

With indexes, you now have to physical copies of the table (although the index just column/s to index on and the id of the row). This means there are 2 places that need to be updated on updates and deletes making these operations slower.

## Here’s what seeks and scans really mean:
*Index seek*: “I’m going to jump to a specific point and start reading.”
*Index scan*: “I’m going to start at either end of the table and start reading.”

Here’s what they don’t mean:
- How many rows we’re going to read
- Whether we’re seeking on all of the columns in the index, or just the first one (more on that later)
- Whether the index is good or bad for this query

## What SQL Server considers when building a query plan

- What tables your query is trying to reference (which could be obfuscated by views, functions, and nesting)
- How many rows will come back from the tables that you’re filtering
- How many rows will then come back from other tables that you’re joining to
- What indexes are available on all of these tables
- Which indexes should be processed first
- How those indexes should be accessed (seeks or scans)
- How the data should be joined together between tables
- At what point in the process we’re going to need to do sorts
- How much memory each operator will need
- Whether it makes sense to throw multiple CPU cores at each operator

It does all of that WITHOUT looking in your tables


## How does SQL Server decide to use Scans or Seeks (or building a query plan in general) - Statistics

