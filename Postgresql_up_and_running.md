## Chapter1
### Why we should use PostgresQl
- PostgreSQL invites you to write stored procedures and functions in numerous programming languages. In addition to the prepackaged languages of C, SQL, and PL/pgSQL, you can easily enable support for additional languages such as PL/Perl, PL/Python, PL/V8 (aka PL/JavaScript), PL/Ruby, and PL/R.
- We can use Python for calling web services, the Python SciPy library for scientific computing, and PL/V8 for validating data, processing strings, and wrangling with JSON data
- Postgre comes with a lot of data type, Not only does PostgreSQL come with a larger built-in set than most, but you can define additional data types to suit your needs. Need complex numbers? Create a composite type made up of two floats. Have a triangle fetish? Create a coordinate type, then create a triangle type made up of three coordinate pairs
- Once you create special data type, you can also create opertion on them.So once you’ve created your special number types, don’t forget to define basic arithmetic operations for them. Yes, PostgreSQL will let you customize the meaning of the symbols (+,-,/,*). Whenever you create a type, PostgreSQL automatically creates a companion array type for you. If you created a complex number type, arrays of complex numbers are available to you without additional work.
- PostgreSQL also automatically creates types from any tables you define. For instance, create a table of dogs with columns such as breed, cuteness, and barkiness. Behind the scenes, PostgreSQL maintains a dogs data type for you. Many third-party extensions for PostgreSQL leverage custom types to achieve performance gains, provide domain-specific constructs for shorter and more maintainable code
- Although PostgreSQL is fundamentally relational, you’ll find plenty of facilities to handle nonrelational data. The ltree extension to PostgreSQL has been around since time immemorial and provides support for graphs. The hstore extensions let you store key-value pairs. JSON and JSONB types allow storage of documents similar to MongoDb.
- One common combination you will find is using Redis or Memcache to cache PostgreSQL query results. As another example, SQLite can be used to store a disconnected set of data for offline querying when PostgreSQL is the main database backend for an application
### Where PostgreSQl is not right choice

- If you’re developing lightweight applications where you’re managing security at the application level, Then use a single-user database such as SQLite or a database such as Firebird that can be run either as a client server or in single-user embedded mode.

### Administration Tools
- Four tools widely used with PostgreSQL are psql, pgAdmin, phpPgAdmin, and Adminer.

### PostgreSQL Database Objects
- Databases
- Schemas
- Tables: 
    1. PostgreSQL tables have two remarkable talents: first, they are inheritable.
    2. whenever you create a table, PostgreSQL automatically creates an accompanying custom data type.
- Views: 
    1. Views are generally read-only, but PostgreSQL allows you to update the underlying data by updating the view, provided that the view draws from a single table.
    2. To update data from views that join multiple tables, you need to create a trigger against the view.
- Extension: 
    1. Extensions may depend on other extensions. Prior to PostgreSQL 9.6, you had to know all dependent extensions and install them first. With 9.6, you simply need to add the CASCADE option and PostgreSQL will take care of the rest. for example:
     
     ` CREATE EXTENSION postgis_tiger_geocoder CASCADE;` 
- Functions:
    1. PostgreSQL comes stocked with thousands of functions. You can program your own custom functions to handle data manipulation, perform complex calculations.
- Languages: 
    1. Create functions using a PL. PostgreSQL installs three by default: SQL, PL/pgSQL, and C. You can easily install additional languages using the extension framework or the `CREATE PRODCEDURAL LANGUAGE` command    
- Operators: 
    1. Operators are nothing more than symbolically named aliases such as = or && for functions. In PostgreSQL, you can invent your own.
- Foreign tables and foreign data wrappers:
    1. Foreign tables are virtual tables linked to data outside a PostgreSQL database. Once you’ve configured the link, you can query them like any other table.
    2. Foreign tables can link to CSV files, a PostgreSQL table on another server, a table in a different product such as SQL Server or Oracle, a NoSQL database such as Redis, or even a web service such as Twitter or Salesforce.
    3. Foreign data wrappers (FDWs) facilitate the magic handshake between PostgreSQL and external data sources.
- Triggers and trigger functions:
- Catalogs:Catalogs are system schemas that store PostgreSQL builtin functions and metadata. Every database contains two catalogs
    1. **pg_catalog**, which holds all functions, tables, system views, casts, and types packaged with PostgreSQL.
    2. **information_schema**, which offers views exposing metadata in a format dictated by the ANSI SQL standard.
- Types: 
- Full text search: 
    1. Full text search (FTS) is a natural language–based search. This kind of search has some “intelligence” built in. Unlike regular expression search, FTS can match based on the semantics of an expression, not just its syntactical makeup.
    2. Three objects in PostgreSQL together support FTS: FTS configurations, FTS dictionaries, and FTS parsers.
- Casts: Casts prescribe how to convert from one data type to another. They are backed by functions that actually perform the conversion. In PostgreSQL, you can create your own casts and override or enhance the default casting behavior.
    1. When an implicit cast is not offered, you must cast explicitly. or example, imagine you’re converting zip codes (which are five digits long in the US) to character from integer, in this case you can define custom cast.
- Sequences: 
    1. A sequence controls the autoincrementation of a serial data type. PostgresSQL automatically creates sequences when you define a serial column, but you can easily change the initial value, step, and next available value.
    2. Because sequences are objects in their own right, more than one table can share the same sequence object. This allows you to create a unique key value that can span tables.   Both SQL Server and Oracle have sequence objects, but you must create them manually.

### Few nice faetures
- **Query parallelization improvements**: There are new planner strategies for parallel queries: Parallel Bitmap Heap Scan, Parallel Index Scan, and others.
- **Logical replication**: Logical replication provides two features that streaming replication did not have. You can now replicate just a table or a database (no need for the whole cluster); since you are replicating only part of the data, the slaves can have their own set of data that is not involved in replication.
- **Full text support for JSON and JSONB**
- **ANSI standard XMLTABLE construct**
- **Phrase full text search**
- **Block range indexes (BRIN)**
- **Grouping sets, ROLLUP, AND CUBE SQL predicates**
- **Row-level security** : You now have the ability to set visibility and updatability on rows of a table using policies. This is especially useful for multitenant databases or situations where security cannot be easily isolated by segmenting data into different tables.
- **Dynamic background workers**: You can code these in C to do work that is not available through SQL or functions. 

## Chapter 2: Database Administration

### Configuration Files

- **postgresql.conf** : Controls general settings, such as memory allocation, default storage location for new databases, the IP addresses, log location.
- **pg_hba.conf** : Controls access to the server, dictating which users can log in to which databases, Auth scheme to accept.
- **pg_ident.conf**: If present, this file maps an authenticated OS login to a PostgreSQL user.

#### Location of configuration files:

` SELECT name, setting FROM pg_settings WHERE category = 'File Locations'; `

- Some configuration changes require a PostgreSQL service restart, which closes any active connections from clients. Other changes require just a reload.
- Reload : 
    1. `pg_ctl reload -D your_data_directory_here`
    2. You can also log in as a superuser to any database and execute the following SQL. `SELECT pg_reload_conf();`

### postgresql.conf
- postgresql.conf controls the life-sustaining settings of the PostgreSQL server. You can override many settings at the database, role, session, and even function levels.
- instead of editing postgresql.conf directly, you should override settings using an additional file called **postgresql.auto.conf**

- Getting the pg_settings by querying **pg_settings** :
`SELECT
name,
context ,
unit ,
setting, boot_val, reset_val
FROM pg_settings
WHERE name IN ('listen_addresses','deadlock_timeout','shared_buffers',
'effective_cache_size','work_mem','maintenance_work_mem') ORDER BY context, name;
`

- we can query **pg_file_settings** to get the value set in config file, it has a file column which shows the file path.

`SELECT name, sourcefile, sourceline, setting, applied
FROM pg_file_settings;` 

` 
SELECT name, sourcefile, sourceline, setting, applied
FROM pg_file_settings
WHERE name IN ('listen_addresses','deadlock_timeout','shared_buffers',
    'effective_cache_size','work_mem','maintenance_work_mem')
ORDER BY name;`

1. **shared_buffers**: Allocated amount of memory shared among all connections to store recently accessed pages. This setting profoundly affects the speed of your queries.
    - You want this setting to be fairly high, probably as much as 25% of your RAM
2. **maintenance_work_mem**:The total memory allocated for housekeeping activities such as vacuuming (pruning records marked for deletion). You shouldn’t set it higher than about 1 GB.

3. **max_parallel_workers_per_gather**: The setting determines the maximum parallel worker threads that can be spawned for each gather operation. The default setting is 0, which means parallelism is completely turned off. If you have more than one CPU core, you will want to elevate this.

- We can also get the setting by below way,

`SHOW shared_buffers;`

`SHOW deadlock_timeout;`

### Changing the postgresql.conf settings

- change settings using the ALTER SYSTEM SQL command. For example, to set the work_mem globally, enter the following:

`ALTER SYSTEM SET work_mem = '500MB';`
This command is wise enough to not directly edit postgres.conf but will make
the change in postgres.auto.conf.

then reload the conf

`SELECT pg_reload_conf();`

- The easiest way to figure out what you screwed up is to look at the logfile, located at the root of the data folder, or in the pg_log subfolder. Open the latest file and read what the last line says.

### pg_hba.conf File
- The pg_hba.conf file controls which IP addresses and users can connect to the database.
- For each connection request, pg_hba.conf is checked from the top down. As soon as a rule granting access is encountered, a connection is allowed and the server reads no further in the file.
- As soon as a rule rejecting access is encountered, the connection is denied and the server reads no further in the file. If the end of the file is reached without any matching rules, the connection is denied.

### Authentication methods : 
- common authentication methods here:
1. **trust**: This is the least secure authentication, essentially no password is needed. As long as the user and database exist in the system and the request comes from an IP within the allowed range, the user can connect.
2. **md5**: Very common, requires an md5-encrypted password to connect.
3. **password**: Uses clear-text password authentication.
4. **ident**: Uses pg_ident.conf to check whether the OS account of the user trying to connect has a mapping to a PostgreSQL account. Not available on Windows.
5. **peer**: Uses the OS name of the user from the kernel. It is available only for Linux, BSD, macOS, and Solaris, and only for local connections on these systems.
6. **cert**: Stipulates that connections use SSL. The client must have a registered certificate. cert uses an ident file such as pg_ident to map the certificate to a PostgreSQL user and is available on all platforms where SSL connection is enabled.

### Managing Connections
- Retrieve a listing of recent connections and process IDs (PIDs): 
    
    `SELECT * FROM pg_stat_activity; `
- Cancel active queries on a connection with PID 1234:

    `SELECT pg_cancel_backend(1234);`
This does not terminate the connection
- Terminate the connection:
    `SELECT pg_terminate_backend(1234);`

- Even though pg_terminate_backend and pg_cancel_backend act on only one connection at a time, you can kill multiple connections by wrapping them in a SELECT.

- For example, let’s suppose you want to kill all connections belonging to a role
    
`SELECT pg_terminate_backend(pid) FROM pg_stat_activity WHERE usename = 'some_role'; `

- You can set certain operational parameters at the server, database, user, session, or function level. Any queries that exceed the parameter will automatically be cancelled by the server. For example: `deadlock_timeout` param.
- Instead of relying on this setting, you can include a NOWAIT clause
1. `SELECT FOR UPDATE NOWAIT ...`  The query will be automatically cancelled upon encountering a deadlock.
2. `SELECT FOR UPDATE SKIP LOCKED` will skip over locked rows. 

- **statement_timeout**: This is the amount of time a query can run before it is forced to cancel. This defaults to 0, meaning no time limit.

- **lock_timeout**: This is the amount of time a query should wait for a lock before giving up, and is most applicable to update queries.
- **idle_in_transaction_session_timeout**: It’s useful for preventing queries from holding on to locks on data indefinitely or eating up a connection.

### Check for Queries Being Blocked

#### pg_stat_activity view

### Roles
- Roles that can log in are called login roles. 
- Roles can also be members of other roles; the roles that contain other roles are called group roles.
- Group roles that can log in are called group login roles.
    -  However, for security, group roles generally cannot log in. This is merely a best-practice suggestion. Nothing stops you

- creating login role ` CREATE ROLE leo LOGIN PASSWORD 'king' VALID UNTIL 'infinity' CREATEDB;`
- Creating superuser roles `CREATE ROLE regina LOGIN PASSWORD 'queen' VALID UNTIL '2020-1-1 00:00' SUPERUSER;`
- the above to user can login, To create roles that cannot log in, omit the LOGIN PASSWORD clause.
- Create a group role `CREATE ROLE royalty INHERIT;`
    - Note the use of the modifier INHERIT. This means that any member of royalty will automatically inherit privileges of the royalty role, except for the superuser privilege.
    - To refrain from passing privileges from the group to its members, create the role with the NOINHERIT modifier.
- To add members to a group role 
    `GRANT royalty TO user1`        
    
### Database Creation:
- `CREATE DATABASE mydb`
- **Template Databases** : A template database is, as the name suggests, a database that serves as a skeleton for new databases.
    - The default PostgreSQL installation comes with two template databases: template0 and template1. If you don’t specify a template database to follow when you create a database, template1 is used.
    - You should never alter template0 because it is the immaculate model that you’ll need to copy from if you screw up your templates.
    - ` CREATE DATABASE my_db TEMPLATE my_template_db;`
    - You can also mark any database as a template database. Once you do, the database is no longer editable and deletable.
    - If ever you need to edit or drop a template database, first set the datistemplate attribute to FALSE. Don’t forget to change the value back after you’re done with edits.
    - `UPDATE pg_database SET datistemplate = TRUE WHERE datname = 'mydb';`
    - PostgreSQL has a little-known variable called user that retrieves the role currently logged in. `SELECT user` returns this name. user is just an alias for current_user, so you can use either.

### <span style="color:red"> Privileges </span>


### Extensions

- Extensions installed ``` SELECT name, default_version, installed_version, left(comment,30) As
comment
FROM pg_available_extensions
WHERE installed_version IS NOT NULL
ORDER BY name;```
- To get more details about a particular extension already installed in your database, enter the following command from psql:
- ` \dx+ fuzzystrmatch`
- Alternatively, execute the following query:
```
SELECT pg_describe_object(D.classid,D.objid,0) AS description
  FROM pg_catalog.pg_depend AS D INNER JOIN pg_catalog.pg_extension AS E
  ON D.refobjid = E.oid
  WHERE
  D.refclassid = 'pg_catalog.pg_extension'::pg_catalog.regclass AND
  deptype = 'e' AND
  E.extname = 'fuzzystrmatch';
  ```
- `SELECT * FROM pg_available_extensions;`

### <span style="color:red"> Backup and Restore </span>

### Managing Disk Storage with Tablespaces:
- PostgreSQL uses tablespaces to ascribe logical names to physical locations on disk. Initializing a PostgreSQL cluster automatically begets two tablespaces: `pg_default`, which stores all user data, and `pg_global`, which stores all system data.
- These are located in the same folder as your default data cluster. You’re free to create tablespaces at will and house them on any serverdisks. You can explicitly assign default tablespaces for new objects by database. You can also move existing database objects to new ones.
- **Creating Tablespaces**: 
    - To create a new tablespace, specify a logical name and a physical folder and make sure that the postgres service account has full access to the physical folder.
    ` CREATE TABLESPACE secondary LOCATION '/usr/data/pgdata94_secondary';`
- **Moving Objects Among Tablespaces**:
    - `ALTER DATABASE mydb SET TABLESPACE secondary;`
    - To move just one table: ` ALTER TABLE mytable SET TABLESPACE secondary;`
    - To move all objects from default tablespace to secondary: `ALTER TABLESPACE pg_default MOVE ALL TO secondary;`

## Chapter 3. psql
- command-line utility packaged with PostgreSQL
- you can forgo specifying your connection settings—host, port, user—by initializing the `PGHOST`, `PGPORT`, and `PGUSER` environment variables.
- To avoid having to retype the password, you can initialize the variable `PGPASSWORD`. For more secure access, create a password file.
- **PSQL_HISTORY**:
- **PSQLRC**: location and name of a custom configuration file. you can place most of your settings in here. At startup, psql will read settings from your configuration file before loading default values.
- **Executing script**: `psql -f some_script_file`
    - `psql -d postgresql_book -c "DROP TABLE IF EXISTS dross; CREATE SCHEMA staging;"`

### psql Customizations:
- you can customize psql prompt and other settings by using psqlrc.conf.
- If you want to bypass the configuration file and start psql with all its defaults, start it with the -X option
- ` \set PROMPT1 '%n@%M:%>%x %/#' ` : This includes whom we are logged in as (%n), the host server (%M), the port (%>), the transaction status (%x), and the database (%/).
    - `postgres@localhost:5442 postgresql_book#`

- **Timing Executions**: Use the `\timing` command to toggle it on and off. If timing is on thenit will show the time taken by the query.

- **Autocommit Commands**: `\set AUTOCOMMIT on|off` to set the auto commit. if auto commit is off then we need to commit by calling `commit`

- **Creating shortcut**: You can use the \set command to create useful keyboard shortcuts.
    - example:  `\set eav 'EXPLAIN ANALYZE VERBOSE' `
    - Now, all you have to type is `:eav` (the colon resolves the variable):
    - `:eav SELECT COUNT(*) FROM pg_tables;`

- **History size**:
    ` \set HISTSIZE 10`   lets you recover the past 10 commands.
    - you may want to have the history of commands piped into separate files for perusal later: 
        `  \set HISTFILE ~/.psql_history - :DBNAME`
- **Executing shell commands on psql prompt**:   you can call out to the OS shell with the \! command  `\! dir`     
- **Watching Statements** : The \watch command in psql  to repeatedly run an SQL statement at fixed intervals so you can monitor the output.
`SELECT datname, query
FROM pg_stat_activity
WHERE state = 'active' AND pid != pg_backend_pid();
\watch 10`
    - example: log traffic in every 5 seconds:
        ```
        SELECT * INTO log_activity
        FROM pg_stat_activity;
        INSERT INTO log_activity
        SELECT * FROM pg_stat_activity; \watch 5
        ```

- **Retrieving Details of Database Objects**: 
    - List tables with `\dt+`
    - `\dt+ pg_catalog.pg_t*` : ist all tables and their sizes on disk in the pg_catalog schema that begins with the letters pg_t
    - If we need more detail then we can use \d+ command. `\d+ pg_ts_dict`

- <span style="color:red"> **Crosstabview** </span> :  greatly simplifies crosstab queries. This labor-saving command is available only in the psql enviroment. 
```
SELECT student, subject, AVG(score)::numeric(5,2) As avg_score
FROM test_scores
GROUP BY student, subject
ORDER BY student, subject
\crosstabview student subject avg_score
```  
- **Importing and Exporting Data**:  psql has a `\copy` command that lets you import data from and export data to a text file. The tab is the default delimiter, but you can specify others
https://www.depesz.com/2013/02/28/waiting-for-9-3-add-support-for-piping-copy-tofrom-an-external-program/

- **Basic Reporting**: psql is capable of producing basic HTML reports. We can also write scripts to generate spphisticated html reports.

## <span style="color:blue">Chapter 4. Using pgAdmin </span>

Will do it later

## Chapter 5. Data Types

- PostgreSQL sprints ahead by adding support for arrays, time zone−aware datetimes, time intervals, ranges, JSON, XML, and many more. If that’s not enough, 
- you can invent custom types also.
- **Serials**: 
    - Serial and its bigger sibling, bigserial, are auto-incrementing integers often used as primary keys of tables
    - When you create a table and specify a column as serial, PostgreSQL first creates an integer column and then creates a sequence object named `table_name_column_name_seq` located in the same schema as the table.
    - In PostgreSQL, the sequence type is a database asset in its own right.
    - you can create them separately from a table using the `CREATE SEQUENCE` command, and you can use the same sequence across multiple tables.
        - The cross-table sharing of the same sequence comes in handy when you’re assigning a universal key in your database.
    - Use it by calling `nextval(sequence_name)` function.
    - ``` CREATE SEQUENCE seq1 START 1;
        CREATE TABLE stuff(id bigint DEFAULT nextval('seq1') PRIMARY KEY, name,text);  ```
 - **Generate Series Function**: `generate_series` generates sequence of integers incremented by some value  or `generate_series` generates  sequence of dates or timestamps incremented by some time interval.
    - with this we can mimic a for loop in sql. generate_series(start, end, step)
    ``` SELECT x FROM generate_series(1,51,13) As x; x```

**Textual**:
- Use `char` only when the values stored are fixed length, such as postal codes, phone numbers, and Social Security numbers in the US.
- There is absolutely no speed performance benefit of using `char` over `varchar` or `text` and char will always take up more disk space.
- When defining `varchar` columns, you should specify the maximum length of a varchar. Text is the most generic of the textual data types. 
- With `text`, you cannot specify a maximum length

### String Functions
- Common string manipulations are padding `(lpad, rpad)`, trimming whitespace `(rtrim, ltrim, trim, btrim)`, extracting substrings `(substring)`, and `concatenating` (||).
- `lpad` truncates instead of padding if the string is too long.

### Splitting Strings into Arrays, Tables, or Substrings:
- The `split_part` function is useful for extracting an element from a delimited string
- The `string_to_array` function is useful for creating an array of elements from a delimited string
- By combining `string_to_array` with the `unnest` function, you can expand the returned array into a set of rows.
- ```SELECT unnest(string_to_array('abc.123.z45', '.')) As x;```
- `unnest` explodes an array into a row set.

### Regular Expressions and Pattern Matching
- PostgreSQL’s regular expression support is downright fantastic. You can return matches as tables or arrays and choreograph replaces and updates. Back-referencing and other fairly advanced search patterns are also supported. example: reformat a phone number.
``` SELECT regexp_replace(
    '6197306254',
    '([0-9]{3})([0-9]{3})([0-9]{4})',
    E'\(\\1\) \\2-\\3'
    ) As x;
     x
    --------------
    (619) 730-6254 
 ```    
- The E' construct is PostgreSQL syntax for denoting that the string to follow should be taken literally.

### time
- PostgreSQL supports time zones, enabling the automatic handling of daylight saving time (DST) conversions by region.
- Specialized data types such as interval offer datetime arithmetic.
- Range types provide support for temporal ranges with a slew of companion operators, functions, and indexes.
- If a type is time zone−aware, the time changes if you change your server’s time zone.
- **date**: Stores the month, day, and year, with no time zone awareness and no concept of hours, minutes, or seconds.
- **time (aka time without time zone)**
- **timestamp (aka timestamp without time zone)**: Stores both calendar dates and time (hours, minutes, seconds) but does not care about the time zone.
- **timestamptz (aka timestamp with time zone)**:A time zone−aware date and time data type. Internally, timestamptz is stored in Coordinated Universal Time (UTC), but its display defaults to timezone of server.
- **timetz (aka time with time zone)**: It is time zone−aware but does not store the date.
- **interval**: A duration of time in hours, days, months, minutes, and others. It comes in handy for datetime arithmetic.
- **tsrange**: Allows you to define opened and closed ranges of timestamp with no timezone. `'[2012-01-01 14:00, 2012-01-01 15:00)'::tsrange` defines a period starting at 14:00 but ending before 15:00
- **tstzrange**
- **daterange**

- PostgreSQL doesn’t store the time zone, but uses it only to convert the datetime to UTC before storage. After that, the time zone information is discarded.
- When PostgreSQL displays datetime, it does so in the default time zone dictated by the session, user, database, or server, in that order.
- `SELECT '2012-02-10 11:00 PM'::timestamp + interval '1 hour';`
- `SELECT '23 hours 20 minutes'::interval + '1 hour'::interval;`
- `SELECT '2012-02-10 11:00 PM'::timestamptz - interval '1 hour';`
- **OVERLAPS**: returns true if two time ranges overlaps 
    - OVERLAPS takes four parameters, the first pair constituting one range and the last pair constituting the other range. An overlap considers the time periods to be half open, meaning that the start time is included but the end time is outside the range.
    - ```
        SELECT
        ('2012-10-25 10:00 AM'::timestamp, '2012-10-25 2:00
        PM'::timestamp)
        OVERLAPS
        ('2012-10-25 11:00 AM'::timestamp,'2012-10-26 2:00
        PM'::timestamp) AS x
        ```
- **Generate time series**: 
    - `SELECT (dt - interval '1 day')::date As eom
    FROM generate_series('2/1/2012', '6/30/2012', interval '1 month') As dt;`

- Another popular activity is to extract or format parts of a datetime value. Here, the functions `date_part` and `to_char` fit the bill
- By default, generate_series assumes timestamptz if you don’t explicitly cast values to timestamp.

### Arrays:
- In PostgreSQL, every data type has a companion array type. If you define your own data type, PostgreSQL creates a corresponding array type in the background for you.

- creating an array `SELECT ARRAY[2001, 2002, 2003] As yrs;`
- or use constructor function, `array()`
    - `  SELECT array(
  SELECT DISTINCT date_part('year', log_ts)
  FROM logs
  ORDER BY date_part('year', log_ts)
  );`
- You can cast a string representation of an array to an array
    - `SELECT '{Alex,Sonia}'::text[] As name, '{46,43}'::smallint[] As age;`
- You can convert delimited strings to an array with the string_to_array function.
    - `SELECT string_to_array('CA.MA.TX', '.') As estados;`
- array_agg is an aggregate function that can take a set of any data type and convert it to an array.
    - `SELECT array_agg(log_ts ORDER BY log_ts) As x
    FROM logs`
- **Creating multidimensional arrays**:
- ``` 
    SELECT array_agg(f.t)
     FROM ( VALUES ('{Alex,Sonia}'::text[]),
    ('{46,43}'::text[] ) ) As f(t);
    array_agg
    ----------------------
    {{Alex,Sonia},{46,43}}
    (1 row)
    ```
- In order to aggregate arrays, they must be of the same data type and the same dimension. To force that in above Example , we cast the ages to text.

- **Unnesting Arrays to Rows**:
- `SELECT unnest('{XOX,OXO,XOX}'::char(3)[]) As tic_tac_toe;`
- if we are unnesting multiple array, then the number of resultant rows from each array must be  balanced, or else we will get some bad result.
    - ```

        SELECT
        unnest('{three,blind,mice}'::text[]) As t,
        unnest('{1,2,3}'::smallint[]) As i;
        t     |i
        ------+-
        three |1
        blind |2
        mice  |3
    ```    
- **Array Slicing and Splicing**: 
- PostgreSQL also supports array slicing using the start:end syntax
- `SELECT fact_subcats[2:4] FROM census.lu_fact_types; `
- To glue two arrays together end to end, use the concatenation operator ||:
- `SELECT fact_subcats[1:2] || fact_subcats[3:4] FROM census.lu_fact_types;`
- PostgreSQL array indexes start at 1. If you try to access an element above the upper bound, you won’t get an error—only NULL will be returned.
- `array_upper` function to get the upper bound of the array. The second required parameter of the function indicates the dimension.

### Array Containment Checks
- Arrays also support the following comparison operators: `=, <>, <, >, @>, <@, and &&`. These operators require both sides of the operator to be arrays of the same array data type. If you have a GiST or GIN index on your array column, the comparison operators can utilize them.
- The overlap operator (&&) returns true if two arrays have any elements in common
- `SELECT '{1,2,3}'::int[] @> '{3,2}'::int[] AS contains;`
- `SELECT '{1,2,3}'::int[] <@ '{3,2}'::int[] AS contained_by;`

### Range Types
-  [-2,2). The square bracket indicates a range that is closed on that end, whereas a parenthesis indicates a range that is open on that end.

- **Built-in Range Types**: 
    - **int4range, int8range**
    - **numrange**
    - **daterange**
    - **tsrange, tstzrange**
- For practicality, you can interpret the null to represent either -infinity on the left or infinity on the right.
- `SELECT '[2013-01-05,2013-08-13]'::daterange;`
- ` SELECT '(0,)'::int8range;`
- `SELECT '(2013-01-05 10:00,2013-08-13 14:00]'::tsrange;`
- Create a table with date range:
- ` CREATE TABLE employment (id serial PRIMARY KEY, employee varchar(20), period daterange);` 
- create an index on date range field
- ` CREATE INDEX ix_employment_period ON employment USING gist (period);`
- ` INSERT INTO employment (employee,period) VALUES ('Alex','[2012-04-24, infinity)'::daterange)`
- **Range Operators**: 
- Two range operators tend to be used most often: overlap (&&) and contains (@>)

### JSON
- To create a table to store JSON, define a column as a json type.
- `CREATE TABLE persons (id serial PRIMARY KEY, person json);`

- `INSERT INTO persons (person) VALUES ('person json string') `

- **Querying JSON**:
- The easiest way to traverse the hierarchy of a JSON object is by using pointer symbols.
- ` SELECT person->'name' FROM persons;`
- `SELECT person->'spouse'->'parents'->'father' FROM persons;`
- **path array**: you must use the #> pointer symbol if what comes after is a path
array.
- the above query can be written like below in path array format. `SELECT person#>array['spouse','parents','father'] FROM persons;`
- **JSON arrays**:
- To penetrate JSON arrays, specify the array index. JSON arrays is zero indexed unlike PostgreSQL arrays, whose indexes start at 1.
- `SELECT person->'children'->0->'name' FROM persons;`
- path array equivalent of above: `SELECT person#>array['children','0','name'] FROM persons;`
- **text representation**:
-All queries in the prior examples return the value as JSON primitives (numbers, strings, booleans). To return the text representation, add another greater-than sign.
- `SELECT person->'spouse'->'parents'->>'father' FROM persons;`
- `SELECT person#>>array['children','0','name'] FROM persons;`
- If you are chaining the -> operator, only the very last one can be a ->> operator.
- The `json_array_elements` function takes a JSON array and returns each element of the array as a separate row.
- `SELECT json_array_elements(person->'children')->>'name' As name FROM persons`
- strongly encourage you to use pointer symbols, rather than `json_extract_path` function
- **Outputting JSON**: 
- `row_to_json` to convert a subset of columns 
- `SELECT row_to_json(f) As jsoned_row FROM persons As f;`

### Binary JSON: jsonb
- jsonb is internally stored as a binary object and does not maintain the formatting of the original JSON text as the json data type does
- jsonb does not allow duplicate keys and silently picks one, whereas the json type preserves duplicates.
- jsonb columns can be directly indexed using the GIN index method (covered in “Indexes”), whereas json requires a functional index to extract key elements.
- jsonb has similarly named functions as json, plus some additional ones.
- In addition to the operators supported by json, jsonb has additional comparator operators for equality (=), contains (@>), contained (<@), key exists (?), any of array of keys exists (?|), and all of array of keys exists (?&).

 

