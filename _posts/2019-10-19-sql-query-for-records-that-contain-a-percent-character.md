---
title: "SQL query for records that contain a percent character"
tags:
  - SQL
---

Let's say that you have a table (shown below) and you want to select rows where column `Name` contains a percentage sign. So in this case query should return rows with ids: 3, 5 and 6.

![Table](/images/posts/sql_table_items.png)

If you are going to use the following query:

`SELECT [Id], [Name] FROM [Items] WHERE [Name] LIKE '%'`

you will receive all rows from your table because in this case `%` is wildcard character that means any string of zero or more characters.

To fulfill our requirements we can use followings queries:

1) we can use `[]` wildcard character

`SELECT [Id], [Name] FROM [Items] WHERE [Name] LIKE '%[%]%'`

2) we can define escape character in query - the escape character has no default and must contain only one character

`SELECT [Id], [Name] FROM [Items] WHERE [Name] LIKE '%\%%' ESCAPE '\'`

Notice that in the above queries the first and the last percent character is a wildcard that matches zero or more of any character.
