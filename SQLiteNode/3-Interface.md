# Use sqlite in program

Here is the example to use SQLite interface in the program

```C
#include "sqlite.h"
#include <stdio.h>
#include <stdlib.h>

static int callback(void *unused, int argc, char **argv, char **col_names) {
    for (int i = 0; i < argc; i++) {
        printf("%s = %s\n", col_names[i], argv[i] ? argv[i] : "NULL");
    }
    return 0;
}

int main(int argc, char **argv) {
    if (argc != 2) {
        printf("Please provide the db name\n");
        exit(1);
    }
    char *errmsg = NULL;
    sqlite *db = sqlite_open(argv[1], 0666, &errmsg);
    if (db == NULL) {
        printf("Cannot open DB\n");
        return 1;
    }

    char *sql1 = "CREATE TABLE users(id INT, name TEXT);";
    char *sql2 = "INSERT INTO users VALUES(1, 'Alice');";
    char *sql3 = "SELECT * FROM users;";

    if (sqlite_exec(db, sql1, callback, NULL, &errmsg) == SQLITE_OK) {
        printf("SQL exec sql1 success: %s\n", sql1);
        if (sqlite_exec(db, sql2, callback, NULL, &errmsg) == SQLITE_OK) {
            printf("SQL exec sql2 success: %s\n", sql2);
            if (sqlite_exec(db, sql3, callback, NULL, &errmsg) == SQLITE_OK) {
                printf("SQL exec sql3 success: %s\n", sql3);
            } else {
                printf("SQL exec sql3[%s] error: %s\n", sql3, errmsg);
                free(errmsg);
            }
        } else {
            printf("SQL exec sql2[%s] error: %s\n", sql2, errmsg);
            free(errmsg);
        }
    } else {
        printf("SQL exec sql1[%s] error: %s\n", sql1, errmsg);
        free(errmsg);
    }

    sqlite_close(db);
    return 0;
}
```

- Build the example
```bash
# copy the C code block and save as example.c
# copy sqlite.h and libsqlite.a from build sqlite folder
$ gcc example.c -I. -static -L. -lsqlite -lgdbm -o example
$ ./example
SQL exec sql1 success: CREATE TABLE users(id INT, name TEXT);
SQL exec sql2 success: INSERT INTO users VALUES(1, 'Alice');
id = 1
name = Alice
SQL exec sql3 success: SELECT * FROM users;
```

