# Enter into interaction mode
- If the parameter only includes the FILENAME or DB Name, sqlite would enter into interaction mode
```bash
$ ./sqlite testdb
Enter ".help" for instructions
sqlite> .help
.dump ?TABLE? ...      Dump the database in an text format
.exit                  Exit this program
.explain               Set output mode suitable for EXPLAIN
.header ON|OFF         Turn display of headers on or off
.help                  Show this message
.indices TABLE         Show names of all indices on TABLE
.mode MODE             Set mode to one of "line", "column", "list", or "html"
.mode insert TABLE     Generate SQL insert statements for TABLE
.output FILENAME       Send output to FILENAME
.output stdout         Send output to the screen
.schema ?TABLE?        Show the CREATE statements
.separator STRING      Change separator string for "list" mode
.tables ?PATTERN?      List names of tables matching a pattern
.timeout MS            Try opening locked tables for MS milliseconds
.width NUM NUM ...     Set column widths for "column" mode

sqlite> select * from students;
1|Jim
sqlite> .header ON
sqlite> select * from students;
id|name
1|Jim
sqlite> .separator ","          
sqlite> select * from students;
id,name
1,Jim
sqlite> .dump students
CREATE TABLE students
(
id INTEGER PRIMARY KEY,
name TEXT NOT NULL
);
COPY 'students' FROM STDIN;
1	Jim
\.
VACUUM 'students';
sqlite> .exit
$ 
```
