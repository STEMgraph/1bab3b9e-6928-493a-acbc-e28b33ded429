<!---
{
  "id": "1bab3b9e-6928-493a-acbc-e28b33ded429",
  "depends_on": ["279a01d6-7696-46b7-9cb7-2c44773ad06b", "845d15ed-1fb2-4b2c-8096-732f29e01c93"],
  "author": "Stephan Bökelmann",
  "first_used": "2025-04-01",
  "keywords": ["file handling", "fopen", "fclose", "error handling", "C"]
}
--->

# Safely Opening and Closing Files in C

## 1) Introduction

In the [fprintf](https://github.com/STEMgraph/279a01d6-7696-46b7-9cb7-2c44773ad06b) and [fscanf](https://github.com/STEMgraph/845d15ed-1fb2-4b2c-8096-732f29e01c93) exercises, we started to work with files.
Opening and closing files might seem trivial, but doing so **safely** is crucial for robust C programming. 

When you call `fopen()`, the system might fail to open the file due to several reasons. Maybe the file doesn't exist (when reading) or the path is invalid. Sometimes you don't have sufficient permissions and sometimes the disk might be full when writing.

If you don't check the return value of `fopen()`, you might pass a `NULL` pointer to other functions like `fscanf` or `fprintf`, which leads to **undefined behavior**. 

Similarly, if you forget to `fclose()` an open file, you might lose unwritten data (write buffers not flushed). 
If you are working with a lot of files, it also might happen, that you exhaust the available amount of file descriptors from your operating system. Or you end up corrupting the file you have been working on, especially in append mode.

Here’s an example of how to **safely open and close** a file in C:

```c
#include <stdio.h>

int main(void) {
    FILE *file = fopen("./data.txt", "r");
    if (!file) {
        perror("Error opening file");
        return 1;
    }

    // File operations go here

    if (fclose(file) != 0) {
        perror("Error closing file");
        return 1;
    }

    return 0;
}
```

Take note, that after the `*file` identifier has been assigned with the value of `fopen()`s return, it is checked whether it's `NULL`.
On the other hand, after closing the file it is checked, whether `fclose(file)` returns a `0`. 
Even though, there is nothing left to do, if closing fails, at least the user of the program will get some diagnostics!

## 2) Tasks

1. **Open & Close**: Write a program that opens a file from your filesystem in read mode and immediately closes it.
2. **File Not Found**: Try opening a non-existent file. Check for errors using `if (!file)` and print a helpful message.
3. **Write File**: Open a file in write mode and write `"hello"` into it. Close it safely.
4. **Double Close**: Call `fclose()` twice in your program and observe the behavior.


## 3) Questions

1. What happens if you try to `fscanf()` or `fprintf()` using a NULL file pointer?

2. Why is it important to always check the return value of `fopen()`?

3. What can go wrong if you forget to `fclose()` a file?

4. What is `perror()` and how does it help during file operations?

<details>
  <summary>Unknown Functions</summary>

  Whenever you are confronted with unknown C-functions. Always check [cppreference.com](https://en.cppreference.com/w/c) for a comprehensive explaination.
  
</details>

5. What are some scenarios in which `fopen()` might fail?

## 4) Advice

Always treat file access as a potentially unreliable operation. File systems are shared resources and prone to failure. Always check if `fopen()` succeeds before continuing. Always close your files — even if your program is about to end. And always use `perror()` to understand what went wrong when something fails.
