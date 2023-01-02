# Buffer Overflow

The code below is vulnureable to Buffer Overflow

```
char str1[10];
char str2[]="text";
strcpy(str1,str2);
```

# Why it's vulnerable?
When the strcpy() function is called, it copies the contents of str2 into str1, but str1 is not large enough to hold the entire contents of str2. 

# Impact?
This could lead to a buffer overflow, where data is written outside of the bounds of the str1 array, potentially leading to a crash or other undefined behavior.

# How to fix?
To fix this issue, you can either make sure that str1 is large enough to hold the contents of str2, or use a safer alternative to strcpy() such as strncpy() which allows you to specify the maximum number of characters to be copied.
