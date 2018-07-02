#Log Analysis Project
The third project in Udacity's full stack web development nanodegree program.

___

##Project Overview
The project requires students to create and use SQL queries to fetch results from a database of a newspaper website.The objective of this project is to demonstrate one's SQL and Query skills. The project requires creating multiple query. The database contains three tables.

* The **authors** table includes information about the authors of articles.
* The **articles** table includes the articles themselves.
* The **log** table includes one entry for each time a user has accessed the site.

___

##Requirements for Project
[Python](https://www.python.org/download/releases/3.0/) - The code uses ver 3.6.4
[Vagrant](https://www.vagrantup.com/downloads.html) - A virtual environment builder and manager.
[VirtualBox](https://www.virtualbox.org/wiki/Downloads) - An open source virtualiztion software.
[Git](https://git-scm.com/downloads) - An open source version control system.

___

##How to access the project?
Follow the steps below to access the code of this project:

1. If you don't already have the latest version of python download it from the link in requirements.
2. If not already installed download Vagrant and VirtualBox.
3. Download or Fork this Udacity folder with preconfigured vagrant settings.
4. Clone this repository.
5. Download this database.
6. Navigate to the Udacity folder downloaded in your command line interface and inside that `cd` into the vagrant folder.
7. Run the command `vagrant up` to download and install your virtual enviroment.
8. Once Vagrant finishes installing use the command `vagrant ssh` to enter your virtual enviroment. If not working try `winpty vagrant ssh`
9. The command line will now show vagrant@vagrant, `cd` into the /vagrant folder. You `ls` and `cd ..` to find the right directory.
10. Move the files of this downloaded repository to the vagrant folder. You can also copy the contents of this repository here.
11. To load the database type `psql -d news -f newsdata.sql`
12. To run the database type `psql -d news`
13. Now with the database installed, enter the command `python db_query.py` to run the query.
14. If succesful the 3 results should be outputed in the terminal.

___

#Queries

##Top Three Articles
```
def get_top_articles():
    """Returns top 3 read articles"""

    # Query String
    query = """
        SELECT articles.title, COUNT(*) AS num
        FROM articles
        JOIN log
        ON log.path LIKE concat('/article/%', articles.slug)
        GROUP BY articles.title
        ORDER BY num DESC
        LIMIT 3;
    """
```

##Top Three Authors
```
def get_top_article_authors():
    """Returns top 3 popular authors"""

    # Query String
    query = """
        SELECT authors.name, COUNT(*) AS num
        FROM authors
        JOIN articles
        ON authors.id = articles.author
        JOIN log
        ON log.path like concat('/article/%', articles.slug)
        GROUP BY authors.name
        ORDER BY num DESC
        LIMIT 3;
    """
```

## Days With More Than 1% Errors
```
def get_days_with_errors():
    """Returns days with more than 1% request errors"""

    # Query String
    query = """
        SELECT total.day,
          ROUND(((errors.error_requests*1.0) / total.requests), 3) AS percent
        FROM (
          SELECT date_trunc('day', time) "day", count(*) AS error_requests
          FROM log
          WHERE status LIKE '404%'
          GROUP BY day
        ) AS errors
        JOIN (
          SELECT date_trunc('day', time) "day", count(*) AS requests
          FROM log
          GROUP BY day
          ) AS total
        ON total.day = errors.day
        WHERE (ROUND(((errors.error_requests*1.0) / total.requests), 3) > 0.01)
        ORDER BY percent DESC;
    """
```
# Expected Output
```
Calculating Results...

TOP THREE ARTICLES BY PAGE VIEWS:
(1) "Candidate is jerk, alleges rival" with 338647 views
(2) "Bears love berries, alleges bear" with 253801 views
(3) "Bad things gone, say good people" with 170098 views

TOP THREE AUTHORS BY VIEWS:
(1) Ursula La Multa has 507594 views
(2) Rudolf von Treppenwitz has 423457 views
(3) Anonymous Contributor has 170098 views

DAYS WITH MORE THAN 1% ERRORS:
July 17, 2016 - 2.3% errors

Query Succesful!
```