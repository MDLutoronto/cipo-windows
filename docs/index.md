---
title: "Getting Started with the Canadian Intellectual Property Office (CIPO) Patent PostgreSQL Database - Windows"
layout: "home"
description: "This tutorial will help you get up and running querying the Canadian Intellectual Property Office (CIPO) Patent PostgreSQL Database. It will cover accessing the high performance computing environment, querying the database via SQL statements and from within a python script, and downloading the results of the query. You will need a Compute Canada account with the proper credentials to access this database. If you haven’t done so already, you should first follow the instructions to get your account set up. Note: This tutorial is intended for Windows users. If you are using a Mac, check out this tutorial instead."
staff:
    - name: Kara Handren
      link: https://library.utoronto.ca/staff/kara-handren 
maintainer:
 - name: Kara Handren
   link: https://library.utoronto.ca/staff/kara-handren
created_date: 2023-01-05
permalink: "/"  #! Remove this if not the homepage
---

# Getting Started with the Canadian Intellectual Property Office (CIPO) Patent PostgreSQL Database - Windows

This tutorial will help you get up and running querying the [Canadian Intellectual Property Office (CIPO) Patent PostgreSQL Database](https://mdl.library.utoronto.ca/technology/text-data-mining-software/cipo-postgresql-database). It will cover accessing the high performance computing environment, querying the database via SQL statements and from within a python script, and downloading the results of the query.

You will need a Compute Canada account with the proper credentials to access this database. If you haven’t done so already, you should first follow the [instructions to get your account set up](https://mdl.library.utoronto.ca/technology/tutorials/how-access-web-science-postgresql-database).

Note: This tutorial is intended for Windows users. If you are using a Mac, check out this [tutorial](https://mdl.library.utoronto.ca/technology/tutorials/getting-started-cipo-postgresql-database-MAC) instead.

### Table of Contents

[Access the High Performance Computing Environment](#access-the-high-performance-computing-environment)

[Query the Database via SQL](#query-the-database-via-sql)

[Download the Results](#download-the-results)

[Query the Database via Python](#query-the-database-via-python)

### Accessing the High Performance Computing Environment
{: #access-the-high-performance-computing-environment}

If working in high performance computing environment is new to you, we would recommend you attend [SciNet workshops](https://education.scinet.utoronto.ca/) to learn more, especially their Intro to SciNet & Triullium workshop (run periodically) or watch [a recording of a previous session](https://www.youtube.com/@scinethpcattheuniversityof8962).

  
But here are some steps to get your started:

1. To access the environment from a Windows machine, you will need an SSH client. We would recommend [MobaXterm](https://mobaxterm.mobatek.net/), and we will be using it in our tutorial examples
2. Once you have installed MobaXterm, start it up
3. From the Session menu, select New Session
4. Select SSH from the top left
5. For the remote host, use this format <computecanadausername>@trillium.scinet.utoronto.ca, substituting in your Compute Canada account username. For example, [doej@trillium.scinet.utoronto.ca](mailto:doej@trillium.scinet.utoronto.ca)
6. Click on the Advanced SSH settings tab below
7. For SSH-browser type, select SCP (enhanced speed)
8. Put a checkmark next to Use private key. Click on the blue page icon to browse to the private key you setup when creating your public key for your Compute Canada account
9. Then click on OK to connect
10. Enter in your Compute Canada account password
11. You are now connected to the server
12. To log out, type `exit` and press Enter. Then press Enter again to close the tab

### Query the Database via SQL
{: #query-the-database-via-sql}

If SQL is a new concept for you, we would first suggest you learn the basics through a tutorial, such as [this one from Tutorial Republic](https://www.tutorialrepublic.com/sql-tutorial/). You may also want to explore the [PostgreSQL documentation](https://www.postgresql.org/docs/14/index.html) to help you with your work.

1. Once logged in [as described earlier](#access-the-high-performance-computing-environment), at the prompt, type `module load postgresql` (and press Enter after this and any commands you type into the command prompt going forward)
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

### Download the Results
{: #download-the-results}

1. From the Trillium prompt, type `ls` to list all the files in your personal directory. If you followed the steps above, you should see a csv file you just saved
2. From the MobaXterm interface, you should see a sidebar to the left of your terminal window. Click on the orange globe icon on the far left to open the file explorer tab. This should now list all the files in your personal directory on the Trillium server
3. Click on the refresh icon (looks like a green circle with a white circular arrow) at the top of that list to refresh the directory items. You should now see your new csv file
4. Highlight your new csv file, and then select the download icon at the top (looks like an arrow pointing down)
5. You should be prompted to select a directory on your local computer where you can save the file. Browse to your desired directory and then click on OK
6. Now if you go to that directory, you should see your new csv file. Open it up and view your results

### Query the Database via Python
{: #query-the-database-via-python}

If you would like to programmatically construct your SQL statements (and programmatically manipulate the results), you may prefer to use Python code to query the database.

If Python is new for you, we would first suggest you learn the basics through a tutorial, such as [this one from W3Schools](https://www.w3schools.com/python/default.asp). You can also consult our recorded workshop [A Friendly Introduction to Python for Absolute Beginners: Part 1](http://play.library.utoronto.ca/watch/d17a5f60462dec00565b7809d2953757), as well as the [Setup Instructions](https://maps.library.utoronto.ca/workshops/PythonPart1/SetupInstructions.pdf) (includes how to get slides, workshop files, etc.) & [Solutions](https://maps.library.utoronto.ca/workshops/PythonPart1/WorkshopSolutions.zip) (packaged in a zip file) for this workshop.

1. In your favourite Python editor, write your script and save your file as a .py file. For this example, we will call it **mycipopythonscript.py**. Here is an example of a Python script that takes a list of companies and finds the patents that they own ([download the script](https://maps.library.utoronto.ca/docs/postgresql/CIPO/mycipopythonscript.py)- note that you may have to right click to save the Python script instead of viewing the text in a browser tab). This script creates a temporary table of our companies, and then joins that table to the assignee table to find the companies' patent document IDs (this is more efficient than calling multiple SELECT statements in a loop, one for each company). Then this table is joined with the title table to find the patent titles. You will see that this script uses the [psycopg2](https://www.psycopg.org/docs/index.html) package and provides information on how to connect to the database. You do not have to specify a username and password, as the system will automatically detect if you have permission. You can use this script and the SQL statement examples above, as guides to create your own Python code to query the database:

    ```

    # You need a couple of packages to query the database and write a CSV file
    import psycopg2
    import csv

    # You will need this database name and host information to create a connection to the database
    database_config = {
        'dbname': 'cipo',
        'host': 'idb1'
    }

    # This is a list of corporate entities we are searching for who are patent owners (assigneees). 
    # Feel free to edit the names to find patent documents owned by companies you are interested in
    companies = [
        'MAZDA MOTOR CORPORATION',
        'TOYOTA JIDOSHA KABUSHIKI KAISHA',
        'SUBARU CORPORATION',
    ]

    # This section of code uses the psycopg2 package to connect to the database
    con = psycopg2.connect(**database_config)
    cur = con.cursor()

    # This executes a SQL statement that creates a temporary table with our list of corporate entities
    cur.execute('CREATE TEMPORARY TABLE _company (coname TEXT)')
    for coname in companies:
        cur.execute('INSERT INTO _company VALUES (%s)', (coname,))

    # This SQL statement joins our list of corporations with the database assigneee table to filter the results 
    # to only the companies we are looking for. This is a more efficient approach than looping through
    # companies and running multiple SELECT statements
    cur.execute("SELECT doc_id, name FROM assignee INNER JOIN _company ON assignee.name \
    ILIKE '%'||_company.coname||'%'")

    # This next section of code goes line by line through the results and adds them to a dictionary 
    # data type in python, where the patent document id is the key and the name of the company 
    # is the value. It also prints it out so you can see the data.
    mylist = dict()
    while result := cur.fetchone():
        print(result)
        mylist[result[0]] = result[1]

    # This next section sets up a CSV that we will use to store the results of our final query
    with open('mycipopythonresults.csv', mode='w', encoding='UTF8', newline='') as csv_file:
        myheader = ['id','title']
        writer = csv.writer(csv_file)
        writer.writerow(myheader)

    # This section goes through each item in the dictionary that we created earlier. For each key(docID)
    # it queries the database to find the title information of their patents. Then it writes that
    # information into the CSV file. It also prints it out so you can see the data.   
        for x in mylist.keys():
            cur.execute("SELECT doc_id, title.text_en, title.text_fr FROM title WHERE title.doc_id=%s", (x,))
            while finalresult := cur.fetchone():
                print(finalresult)
                writer.writerow(finalresult)

    # Finally, all the connections to the database are closed
    cur.close()
    con.close()
    ```
2. Once your Python script is ready, connect to Trillium using MobaXterm [as described earlier](#access-the-high-performance-computing-environment)
3. From the MobaXterm interface, you should see a sidebar to the left of your terminal window. Click on the orange globe icon on the far left to open the file explorer tab. This should now list all the files in your personal directory on the Trillium server
4. Click on the upload icon at the top (looks like an arrow pointing up)
5. You should be prompted to select the file you want to upload from your local computer. Select the file and then click on OK
6. Next we need to set up the environment to run our Python script. Type `module load python/3.9.8`
7. Next type `virtualenv --system-site-packages myenv`
8. Next type `source myenv/bin/activate`
9. Finally type `pip install psycopg2-binary`
10. Once the package has installed, you are ready to run your Python script. Type `python mycipopythonscript.py` or substitute in the name of your Python script if you called it something else.  
(Important Note: If querying is only a small part of the overall task, and the majority of computing effort is going into postprocessing the query results, for example, using natural language processing or graph analysis, to be done in parallel, then there are different ways to run your script that involve [submitting it as a job](https://docs.scinet.utoronto.ca/index.php/Niagara_Quickstart#Submitting_jobs) to be run. Feel free to [contact us](https://mdl.library.utoronto.ca/about/contact-form) for help.)
11. It may take a while to run, but when it is finished you will see the command prompt again, and now if you refresh your file directory in MobaXterm or type `ls`, you should see a new CSV file created from the Python script. Download the file ([as described earlier](#download-the-results)) and open up the file to see the results

These are just a few examples to help you get started, but of course there is much more you can do. If you have any questions, feel free to [contact us](https://mdl.library.utoronto.ca/about/contact-form).
