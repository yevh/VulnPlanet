# Buffer Overflow

The code below is vulnureable to Buffer Overflow

```c
char str1[10];
char str2[]="text";
strcpy(str1,str2);
```

# Why it's vulnerable?
When the ```strcpy()``` function is called, it copies the contents of ```str2``` into ```str1```, but ```str1``` is not large enough to hold the entire contents of ```str2```. 

# Impact?
This could lead to a buffer overflow, where data is written outside of the bounds of the ```str1``` array, potentially leading to a crash or other undefined behavior.

# How to fix?
To fix this issue, you can either make sure that ```str1``` is large enough to hold the contents of ```str2```, or use a safer alternative to ```strcpy()``` such as ```strncpy()``` which allows you to specify the maximum number of characters to be copied.

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_LENGTH 10

int main() {
    char str1[MAX_LENGTH+1]; // Add 1 to include null terminator
    const char *str2 = "text";

    // Copy at most MAX_LENGTH characters from str2 to str1
    strncpy(str1, str2, MAX_LENGTH);
    str1[MAX_LENGTH] = '\0'; // Ensure null terminator is present

    printf("str1: %s\n", str1);
    return 0;
}
```
