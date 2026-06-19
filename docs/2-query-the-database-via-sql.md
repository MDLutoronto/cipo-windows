---
title: Query the Database via SQL
parent: Getting Started with the Canadian Intellectual Property Office (CIPO) Patent PostgreSQL Database - Windows
layout: default
nav_order: 2
staff:
    - name: Kara Handren
      link: https://library.utoronto.ca/staff/kara-handren 
maintainer:
    - name: Leslie Barnes
      link: https://library.utoronto.ca/staff/leslie-barnes
created_date: 2023-01-05
---
## Query the Database via SQL

If SQL is a new concept for you, we would first suggest you learn the basics through a tutorial, such as [this one from Tutorial Republic](https://www.tutorialrepublic.com/sql-tutorial/). You may also want to explore the [PostgreSQL documentation](https://www.postgresql.org/docs/14/index.html) to help you with your work.

1. Once logged in [as described earlier](https://mdlutoronto.github.io/cipo-windows/1-access-the-high-performance-computing-environment/), at the prompt, type `module load postgresql` (and press Enter after this and any commands you type into the command prompt going forward)
    * Note there will be no visible change or messages after running some of these commands
2. Type `psql -h ibd1 -d cipo` to start up the command-line interface to PostgreSQL and be able to query the Canadian Intellectual Property Office (CIPO) Patent Database
3. Type `\?` for help with psql commands (whenever you see “—More—” at the bottom of the screen, press the space bar to page through the information)
4. Let’s try a few of these psql commands. Type `\x` to have a nicer expanded display of the outputs/results
5. Type `\dt` to see a list of all the tables available to you. To learn more about these tables, you can also consult [the documentation](https://maps.library.utoronto.ca/docs/postgresql/CIPO/db-structure.pdf)
6. You can type \d <**tablename**> to display columns for a particular table. Type `\d title` to see all the columns for the title table, for example
7. Once you have a better understanding of the database’s organization into tables, you can type SQL statements ending with a semi-colon to query the database and see the results. Remember, you can page through results with the spacebar
8. Before you begin, you might want to type `\h` for help with SQL commands. This will list all of the SQL commands available. If you want to learn more about a particular command, such as SELECT, type `\h select`
9. Let’s try out a few SQL examples relevant to this particular database. If you want to paste these long statements into Terminal, you can copy them directly from our code snippets.
    * If you would like to limit your results to a random sampling (this will speed things up and is great for testing!) the following command can be added to the end of the examples below: `order by random() limit 10`
10. You will know when it is done, as you will see results on the screen or the command prompt will reappear. If you want to re-run a command, a quick way to “re-type” it is to use the up and down arrow keys to cycle through previously entered commands. If the results list seems too long to page through every item, type `q` to stop showing the results.
11. *Important:* When querying claim and disclosure tables, these tables should not be accessed by the print to screen statement below, as the large amount of raw text inside of them can break the display. This raw text is large blocks of text that contain HTML throughout. See # 14 below for more information on how to query these tables.
12. Note: the queries below are searching for English language values. Each patent document contains a *title* and an *abstract* in both English and French. For other fields, values may be in either English or French, and sometimes both.

    1. **Search by Title:** 
        Let’s find patent documents that have the words “solar” and “power” OR "powered" in the title. (The % symbol is a wildcard symbol in SQL when using the ILIKE operator. This also makes your search case insensitive, compared to = which would look for an exact match). Type
        ```
        SELECT * 
        FROM title 
        WHERE title.text_en ILIKE '%solar%' AND title.text_en ILIKE '%power%';
        ```
        * **to limit your results to a random sample of 10**:
        ```
        SELECT * 
        FROM title 
        WHERE title.text_en ILIKE '%solar%' AND title.text_en ILIKE '%power%' order by random() limit 10;
        ```
    2. **Search by Title words and Document ID:** 
        We can also limit searches based on multiple criteria for different fields. Let’s run the same search as above, but limit it to only publications with a patent number greater than 2 million **(**Note that document numbers increase over time, where newer patents will have higher document numbers). Type
        ```
        SELECT * 
        FROM title 
        WHERE title.text_en ILIKE '%solar%' AND title.text_en ILIKE '%power%' AND title.doc_id > 2000000;
        ```
    3. **Search by Title words and Document ID, return specific fields only:** 
        We have been selecting all the fields in the title table, but we can instead only pick the ones of interest. Let’s only output the patent document IDs. Type
        ```
        SELECT title.doc_id
        FROM title 
        WHERE title.text_en ILIKE '%solar%' AND title.text_en ILIKE '%power%' AND title.doc_id > 2000000;
        ```
    4. **Search by Title words and Document ID, but return abstract information as well:** 
        So far these queries have focused on returning data from one table, but you can join tables to get information from multiple tables, such as title and abstract. Let’s run the same search from above, but also get the abstract information in the results. Type
        ```
        SELECT * 
        FROM title 
        INNER JOIN abstract ON title.doc_id=abstract.doc_id 
        WHERE title.text_en ILIKE '%solar%' AND title.text_en ILIKE '%power%' AND title.doc_id > 2000000;
        ```
        (note: not all records will contain an abstract)
    5. **Search by Title words and Document ID, but return Abstract and Inventor information as well:** 
        You can join one table to more than one other table to pull in more information into your results. Let’s run the query from example d, but add the inventor information as well. Type
        ```
        SELECT * 
        FROM title 
        INNER JOIN abstract ON title.doc_id=abstract.doc_id 
        INNER JOIN inventor ON title.doc_id=inventor.doc_id 
        WHERE title.text_en ILIKE '%solar%' AND title.text_en ILIKE '%power%' AND title.doc_id > 2000000;
        ```
        (Note: This may result in document titles being duplicated if there are multiple inventors on a patent document)
    6. **Search by Title words, Document ID and Inventor country - and return specific fields:** 
        You can also limit searches based on information in multiple tables. Let’s run the same search from above, but also limit to inventors from Canada where the country field in the inventor table is equal to "CA". This time, we're also limiting the fields our query returns, similar to example c. Type
        ```
        SELECT title.doc_id, title.text_en, title.text_fr, abstract.text_en, abstract.text_fr, inventor.name, inventor.country 
        FROM title 
        INNER JOIN abstract ON title.doc_id=abstract.doc_id 
        INNER JOIN inventor ON title.doc_id=inventor.doc_id 
        WHERE title.text_en ILIKE '%solar%' AND title.text_en ILIKE '%power%' AND title.doc_id > 2000000 AND inventor.country = 'CA';
        ```
    7. **Search by Year and PCT status, without returning this information in results:** 
        This can continue to get more complicated. You might want to join a table in order to query a field, but aren’t interested in including the data from that table in the final results. The next example illustrates this and introduces you to nested SELECT statements (which will come in handy later). Often these SQL statements are best deciphered reading from the inside out (i.e., reading what is in the innermost parentheses first and working outwards) and breaking the statement into distinct parts. Note you could also construct this query similar to example f above, the only difference is that the columns from the source table are not included here, but are included in example f. For this example, let’s query the database for patent titles, document IDs and inventor names for patents with a Canadian inventor where the patents are PCT compliant (i.e. filed under the Patent Cooperation Treaty) and the application was filed in 2019 or later. These last bits of information we'll need to verify using the document table. Working inside out, this query finds all the patent documents IDs where the value of pct in the documents table is "t" (this is a boolean field, with values of "t" (True) or "f" (False)), and the value of app_date is greater than 2019-01-01. It then uses these document IDs to subset further, pulling only the information we requested for our final results. None of the information from the document table is returned in those results. Type
        ```
        SELECT title.doc_id, title.text_en, inventor.name 
        FROM title 
        INNER JOIN inventor ON title.doc_id = inventor.doc_id 
        WHERE inventor.country = 'CA' AND title.doc_id IN 
        (SELECT title.doc_id 
        FROM title 
        INNER JOIN document ON title.doc_id = document.id
        WHERE document.pct = 't' AND document.app_date > '2019-01-01');
        ```
    8. **Search for patents that are related to a subset of other patents:** 
        CIPO data provides parent information. This means that many component patents may be grouped under a single parent patent, or a patent may represent an improvement to an original parent patent. Let’s query the database to find all the patents that have a small subset of patents as parent documents. The subset is similar to example b above, where we'll find all articles that have the word "solar" in the title, but this time we want to retrieve the parent document title information. This could be useful if you want to find parent documents that are related to the development of solar patents, but don't necessary have 'solar' in their name. This query is also similar to example g in that is uses a nested approach. Working inside out, this query finds all the patent documents IDs that match our search, and grabs their parent patent document IDs. It then pulls title table information for these parent patents. These types of queries are intensive and can take a while to run, so this is a very simple and small example to get you started. Type
        ```
        SELECT *
        FROM title 
        WHERE title.doc_id IN
        (SELECT parent_document.parent_app_num
        FROM parent_document
        INNER JOIN title ON parent_document.doc_id = title.doc_id
        WHERE title.text_en ILIKE '%solar%' AND title.text_en ILIKE '%power%');
        ```
13. After running some test SQL statements, if you are happy with the statement, instead of displaying the results on the screen, you can save them to a CSV file instead (*unless you are querying claims or disclosure tables* - see #14 below). If you want to save the results of our first SQL statement example above to a file called **myfirstciporesult.csv**, type

    ```
    \copy 
    (SELECT * 
    FROM title 
    WHERE title.text_en ILIKE '%solar%' AND title.text_en ILIKE '%power%') 
    TO 'mycipoqueryresult.csv' CSV HEADER;
    ```

    (Notes: You don’t need a semi-colon after the SQL statement in this case, but at the end of the copy command instead. Also, make sure that there is a space between \copy and the open parenthesis. When the command is finished, you will see the word COPY, followed by the number of results.)
14. Like when querying claim and disclosure tables, the large amount of raw text inside of these two tables can make it difficult to retrieve and view the data as a CSV. Please use PostgreSQL XML functions to access these data, and save any results as XML documents. For example:

    ```
    \copy (SELECT * from disclosure where disclosure.doc_id = 3125047) to 'output.xml'
    ```
15. When you are finished querying the database and saving your results, type `\q` to quit the psql program

Throughout the examples, we have been using plain wildcard pattern matching, but you may want to explore [more sophisticated ways to search text](https://www.postgresql.org/docs/14/textsearch.html) as well.

A Note on Query Efficiency: Generally, Postgres is really smart at analyzing what you want to do and querying the database in the most efficient way, so often changing the query structure won't make any difference because Postgres really does the same thing under the hood. One thing that sometimes helps is to increase the number of workers, for example with this command `SET max_parallel_workers_per_gather = 16`, but not all operations in a query can be parallelized or parallelized well. Another thing that potentially helps for complex queries is to use temporary tables instead of table variables. For example, [rewriting example g](http://maps.library.utoronto.ca/docs/postgresql/CIPO/cipo_exampleG_rewritten.txt) to use temporary tables - if this was a large query, it could speed that up from minutes to seconds!

**Technique:** [Searching for maps and data](https://mdlutoronto.github.io/tutorials-search/?technique=Searching+for+maps+and+data), [Text and Data Mining](https://mdlutoronto.github.io/tutorials-search/?technique=Text+and+Data+Mining) \| **Tools:** [CIPO](https://mdlutoronto.github.io/tutorials-search/?tool=CIPO)