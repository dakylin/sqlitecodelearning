# Build in the local
* Since the code is based on e8521fc1, by default, the code might not build success using modern compiler
* The first step is change the code to make sure we can build it in the local environment.

## Local environment
- OS Ubuntu 20.04
- Compiler GCC 11.4

## Build steps
```bash
mkdir -p build/install
cd build
../SQLiteCode/configure --prefix=./install
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
In file included from ../SQLiteCode/tool/lemon.c:29:
/usr/lib/gcc/x86_64-linux-gnu/11/include/varargs.h:4:2: error: #error "GCC no longer implements <varargs.h>."
    4 | #error "GCC no longer implements <varargs.h>."
      |  ^~~~~
/usr/lib/gcc/x86_64-linux-gnu/11/include/varargs.h:5:2: error: #error "Revise your code to use <stdarg.h>."
    5 | #error "Revise your code to use <stdarg.h>."
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