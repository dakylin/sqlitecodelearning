# Run sqlite

- When using `./sqlite` to launch sqlite, the output is
```bash
Usage: ./sqlite ?OPTIONS? FILENAME ?SQL?
```

- ? means this parameter is optional.
- FILENAME is the MUST have parameter
- FILENAME is also the name of the DB
- SQL is the command script to operate the DB

```bash
./sqlite -html testdb "select * from students"
```
The above command would output the values in table students of DB testdb with HTML format.
Here is my output
```html
<TR><TD>1</TD>
<TD>Jim</TD>
</TD></TR>
```

# Parameter Example
The below table list all the OPTIONS in the current version of SQLite.
with command:
```bash
./sqlite -[options] testdb "select * from students"
```
|OPTION|Example|
|--|--|
|html|```<TR><TD>1</TD><TD>Jim</TD></TD></TR>```|
|list|1&#124;Jim|
|line|id = 1</br>name = Jim|
|header|id&#124;name</br>1&#124;Jim|
|noheader|1&#124;Jim|
|separator| -- |

- For separator parameter, there was an issue in the code.
```C
}else if( argc>=3 && strcmp(argv[0],"-separator")==0 ){
```
- argv[0] should be always sqlite and would never be "-separator"
- For testing, needs to change to
```C
}else if( argc>=3 && strcmp(argv[1],"-separator")==0 ){
```
- The format is a little difference with others
```bash
./sqlite -separator "," testdb "select * from students"
```
- The output is like:
```bash
1,Jim
```
- The parameter can be used with others like
```bash
./sqlite -separator "," -header testdb "select * from students"
```
The output is like
```bash
id,name
1,Jim
```

- There is no output when the SQL command is not used to query.
E.g
```bash
./sqlite -separator "," -line testdb "delete from students where id=2"
# The command would delete row if the id is 2
# No output after it
# after that, query command would not get the row data with id = 2
# if the SQL exec failed, the output would show the errors E.g

./sqlite -separator "," -line testdb "insert INTO users VALUES(3, 'Charlie');"
SQL error: no such table: users

./sqlite -separator "," -line testdb "insert INTO students VALUES(3, 'Charlie',123);"
SQL error: table students has 2 columns but 3 values were supplied
```
