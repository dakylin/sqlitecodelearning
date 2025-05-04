# Build in the local
* Since the code is based on e8521fc1, by default, the code might not build success using modern compiler
* The first step is change the code to make sure we can build it in the local environment.

## Local environment
- OS Ubuntu 20.04
- Compiler GCC 11.4
- Installed libgdbm-dev by
```bash
sudo apt install libgdbm-dev

# optional
sudo apt libreadline-dev
```

## Build steps
```bash
mkdir -p build/install
cd build
../SQLiteCode/configure --enable-readline
../SQLiteCode/configure
# If installed libreadline-dev, you can add enable readline to keep history command
# Otherwise, the add_history(zResult) would be replaced by add_history(X)
make
```

## Fix errors
1. The configure process failed
```bash
checking for /usr/X11*/include/tcl.h... ../SQLiteCode/configure: 1: eval: Bad substitution
$ echo $?
2
```

- Fix the issue

```bash
vim ../SQLiteCode/configure
# update the first to to use bash
```

2. Make process failed
```bash
$ In file included from ../SQLiteCode/tool/lemon.c:29:
$ /usr/lib/gcc/x86_64-linux-gnu/11/include/varargs.h:4:2: error: #error "GCC no longer implements $ <varargs.h>."
$     4 | #error "GCC no longer implements <varargs.h>."
$       |  ^~~~~
$ /usr/lib/gcc/x86_64-linux-gnu/11/include/varargs.h:5:2: error: #error "Revise your code to use $ <stdarg.h>."
$     5 | #error "Revise your code to use <stdarg.h>."
```

- The morden gcc doesn't support varargs.h
```C
29 /*#include <varargs.h>*/
30 #include <stdarg.h>

93 /*void ErrorMsg( *//* char *, int, char *, ... */ /*);*/
94 void ErrorMsg( const char *format, ... );


1112 /*void ErrorMsg(va_alist)*/
1113 /*va_dcl*/
1114 void ErrorMsg(const char *format, ...)
1115 {
1116  char *filename;
1117  int lineno;
1118  /*char *format;*/

1127  /*va_start(ap);*/
1128  va_start(ap, format);
```

- After the above code change, lemon.c build success.
- The next step is to fix ./lemon parse.y

3. ./lemon parse.y gets coredump
- Update Makefile, enable DEBUG
- rebuild lemon and run ./lemon parse.y
- gdb core file and find the backtrace
- The issue is in lemon.c
```C
1333 #define NEXT(A) (*(char**)(((int)A)+offset))
..........
1415 list = NEXT(list);
```
- Update the #define
```C
1333 /*#define NEXT(A) (*(char**)(((int)A)+offset))*/
1334 #define NEXT(A) (*(char**)((char*)(A) + offset))
```

4. src/shell.c get build error
```bash
$ error: conflicting types for ‘getline’; have ‘char *(char *)’
$   53 | static char *getline(char *zPrompt){
$      |              ^~~~~~~
$ In file included from ../SQLiteCode/src/shell.c:31:
$ /usr/include/stdio.h:616:18: note: previous declaration of ‘getline’ with type ‘__ssize_t(char ** restrict,  size_t * restrict,  FILE * restrict)’ {aka ‘long int(char ** restrict,  long unsigned int * restrict,  FILE * restrict)’}
```
- In src/shell.c
```C
40 /*# define readline getline*/
41 # define readline getline_s
......
54 /*static char *getline(char *zPrompt){*/
55 static char *getline_s(char *zPrompt){
....
109    /*return getline(0);*/
110    return getline_s(0);
```

## Run SQLite Demo
- After make finished,
```bash
./sqlite testdb
```
- tesedb will be created, create table
```bash
Enter ".help" for instructions
sqlite> CREATE TABLE students
   ...> (
   ...> id INTEGER PRIMARY KEY,
   ...> name TEXT NOT NULL
   ...> );
sqlite> INSERT INTO students
   ...> (id, name)
   ...> values
   ...> (1, 'Jim');
sqlite> select * from students;
1|Jim
sqlite> .exit

```