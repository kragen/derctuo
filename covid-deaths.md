I downloaded [the WHO CSV of covid
data](https://covid19.who.int/WHO-COVID-19-global-data.csv) and
[imported it into
SQLite](https://stackoverflow.com/questions/1045910/how-to-import-load-a-sql-or-csv-file-into-sqlite)
to query it as follows:

    $ sqlite3 covid-data.sqlite3
    SQLite version 3.11.0 2016-02-15 17:29:24
    Enter ".help" for usage hints.
    sqlite> .mode csv who
    sqlite> .import WHO-COVID-19-global-data.csv who
    sqlite> .schema
    CREATE TABLE who(
      "Date_reported" TEXT,
      " Country_code" TEXT,
      " Country" TEXT,
      " WHO_region" TEXT,
      " New_cases" TEXT,
      " Cumulative_cases" TEXT,
      " New_deaths" TEXT,
      " Cumulative_deaths" TEXT
    );
    sqlite> select sum(deaths) from (
       ...> select " Country", max(cast(" Cumulative_deaths" as decimal)) as deaths
       ...> from who
       ...> group by " Country"
       ...> );
    508456
    sqlite> select " Country", max(cast(" Cumulative_deaths" as decimal)) as deaths
       ...> from who
       ...> group by " Country"
       ...> order by deaths desc
       ...> limit 8;
    "United States of America",126573
    Brazil,58314
    "The United Kingdom",43730
    Italy,34767
    France,29760
    Spain,28752
    Mexico,27121
    India,17400
    sqlite> select max(Date_reported) from who;
    2020-07-01

The `cast` is necessary because otherwise the sorting is performed
ASCIIbetically, producing the wrong answer.  Here’s Argentina:

    sqlite> select Date_reported, " Cumulative_deaths", " Cumulative_cases" from who
       ...> where Date_reported in ('2020-05-01', '2020-05-15', '2020-06-01', '2020-06-15', '2020-07-01')
       ...> and " Country" = 'Argentina';
    2020-05-01,215,4304
    2020-05-15,345,6973
    2020-06-01,530,16214
    2020-06-15,819,30295
    2020-07-01,1283,62268
    sqlite> select Date_reported, " Cumulative_deaths", " Cumulative_cases" from who
       ...> where (Date_reported like '%-01' or Date_reported like '%-15')
       ...> and " Country" = 'Argentina';
    2020-03-15,2,45
    2020-04-01,24,966
    2020-04-15,101,2336
    2020-05-01,215,4304
    2020-05-15,345,6973
    2020-06-01,530,16214
    2020-06-15,819,30295
    2020-07-01,1283,62268

For Derctuo I want to be able to do queries like this interactively
and easily (more easily than SQL) and plot the results.  The CSV in
question is 1.07 megabytes, but gzips to 191kB, and I suspect would be
under 100kB with a simple column-oriented database doing
delta-compression.

