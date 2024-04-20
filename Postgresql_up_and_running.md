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
