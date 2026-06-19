---
title: Query the Database via Python
parent: Getting Started with the Canadian Intellectual Property Office (CIPO) Patent PostgreSQL Database - Windows
layout: default
nav_order: 4
staff:
    - name: Kara Handren
      link: https://library.utoronto.ca/staff/kara-handren 
maintainer:
    - name: Leslie Barnes
      link: https://library.utoronto.ca/staff/leslie-barnes
created_date: 2023-01-05
---

## Query the Database via Python

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
2. Once your Python script is ready, connect to Trillium using MobaXterm [as described earlier](https://mdlutoronto.github.io/cipo-windows/1-access-the-high-performance-computing-environment/)
3. From the MobaXterm interface, you should see a sidebar to the left of your terminal window. Click on the orange globe icon on the far left to open the file explorer tab. This should now list all the files in your personal directory on the Trillium server
4. Click on the upload icon at the top (looks like an arrow pointing up)
5. You should be prompted to select the file you want to upload from your local computer. Select the file and then click on OK
6. Next we need to set up the environment to run our Python script. Type `module load python/3.9.8`
7. Next type `virtualenv --system-site-packages myenv`
8. Next type `source myenv/bin/activate`
9. Finally type `pip install psycopg2-binary`
10. Once the package has installed, you are ready to run your Python script. Type `python mycipopythonscript.py` or substitute in the name of your Python script if you called it something else.  
(Important Note: If querying is only a small part of the overall task, and the majority of computing effort is going into postprocessing the query results, for example, using natural language processing or graph analysis, to be done in parallel, then there are different ways to run your script that involve [submitting it as a job](https://docs.scinet.utoronto.ca/index.php/Niagara_Quickstart#Submitting_jobs) to be run. Feel free to [contact us](https://mdl.library.utoronto.ca/about/contact-form) for help.)
11. It may take a while to run, but when it is finished you will see the command prompt again, and now if you refresh your file directory in MobaXterm or type `ls`, you should see a new CSV file created from the Python script. Download the file ([as described earlier](https://mdlutoronto.github.io/cipo-windows/3-download-the-results/)) and open up the file to see the results

These are just a few examples to help you get started, but of course there is much more you can do. If you have any questions, feel free to [contact us](https://mdl.library.utoronto.ca/about/contact-form).

**Technique:** [Searching for maps and data](https://mdlutoronto.github.io/tutorials-search/?technique=Searching+for+maps+and+data), [Text and Data Mining](https://mdlutoronto.github.io/tutorials-search/?technique=Text+and+Data+Mining) \| **Tools:** [CIPO](https://mdlutoronto.github.io/tutorials-search/?tool=CIPO)
