# Run sqlite

## Launch sqlite
After build sqlite, the application binary named "sqlite" can be found in the build folder.
The MUST Have parameter is the FILENAME. If launch sqlite without FILENAME, sqlite exits.

- main
Program entry main() is in src/shell.c.
```C
631    data.mode = MODE_List;
632    strcpy(data.separator,"|");
633    data.showHeader = 0;
```
The default mode and header format is set here.

```C
/*Open DB with 666 by default*/
data.db = db = sqlite_open(argv[1], 0666, &zErrMsg);
/*Open DB with 444 if 666 failed*/
data.db = db = sqlite_open(argv[1], 0444, &zErrMsg);
```

## Exec SQL command
If the parameter includes sql command after the FILENAME, program goes into sqlite_exec.
```C
if( argc==3 ){
    if( sqlite_exec(db, argv[2], callback, &data, &zErrMsg)!=0 && zErrMsg!=0 ){
```

```bash
$ ./sqlite -separator "," testdb "select * from students"
1,Jim
$
```

## User Interaction

- meta_command
Otherwise, sqlite enter into interaction mode and waiting for the user input
```C
while( (zLine = one_input_line(zSql, istty))!=0 ){
    if( zLine && zLine[0]=='.' ){
        do_meta_command(zLine, db, &data);
```
If user input command start with ".", goes into function "do_meta_command"
```C
/*E.g. user input .exit */
if( c=='e' && strncmp(azArg[0], "exit", n)==0 ){
    exit(0);
```
Some of the meta_command would get the same output of SQL Command.
```bash
sqlite> .header OFF
sqlite> select name from sqlite_master where type = 'table';
students
sqlite> .table
students
sqlite>
```
The logic can be found inside "do_meta_command"

- SQL Command
If user input is SQL command, program goes into sqlite_complete to see if the command is end with ";"
```C
      case TK_SEMI:
        lastWasSemi = 1;
```
Then goes to function "sqlite_exec"

- Program goes into waiting for user input until the command is ".exit". "sqlite" finished the execution.
