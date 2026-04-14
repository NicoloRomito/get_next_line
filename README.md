# get_next_line

> Read a file descriptor line by line — one call at a time.

`get_next_line` is a 42 Common Core project that implements a function capable of reading one line at a time from any file descriptor, retaining state between calls through static variables. The project includes both a standard and a bonus implementation supporting multiple simultaneous file descriptors.

---

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Project Structure](#project-structure)
- [Requirements](#requirements)
- [Installation & Integration](#installation--integration)
- [Usage](#usage)
- [Bonus — Multiple File Descriptors](#bonus--multiple-file-descriptors)
- [Configuration](#configuration)

---

## Overview

The function reads from a file descriptor and returns the next available line, including the terminating newline character (`\n`) when present. On successive calls, it resumes exactly where it left off. On end-of-file or error, it returns `NULL`.

The key challenge is managing a static read buffer across multiple calls, handling arbitrary `BUFFER_SIZE` values, and avoiding memory leaks.

---

## Features

- Reads one line at a time from any valid file descriptor
- Handles files, standard input (`stdin`), and pipes
- Works correctly with any `BUFFER_SIZE` (1 to very large values)
- Bonus: supports multiple file descriptors open simultaneously
- No memory leaks

---

## Project Structure

```
get_next_line/
├── get_next_line.c            # Core implementation
├── get_next_line.h            # Header — prototype and BUFFER_SIZE macro
├── get_next_line_utils.c      # Helper functions
├── get_next_line_bonus.c      # Bonus: multi-fd implementation
├── get_next_line_bonus.h      # Bonus header
├── get_next_line_utils_bonus.c# Bonus helper functions
└── text.txt                   # Sample text file for testing
```

---

## Requirements

- GCC or Clang
- GNU Make (optional, for integration)
- Unix-based OS (Linux or macOS)

---

## Installation & Integration

Clone the repository:

```bash
git clone https://github.com/NicoloRomito/get_next_line.git
```

To integrate into another project, copy the following files into your source tree:

- `get_next_line.c`
- `get_next_line.h`
- `get_next_line_utils.c`

Then include the header and compile together:

```bash
gcc -D BUFFER_SIZE=42 your_file.c get_next_line.c get_next_line_utils.c -o your_program
```

---

## Usage

```c
#include "get_next_line.h"
#include <fcntl.h>
#include <stdio.h>

int main(void)
{
    int     fd;
    char    *line;

    fd = open("text.txt", O_RDONLY);
    if (fd < 0)
        return (1);
    while ((line = get_next_line(fd)) != NULL)
    {
        printf("%s", line);
        free(line);
    }
    close(fd);
    return (0);
}
```

Compile with a custom buffer size:

```bash
gcc -D BUFFER_SIZE=32 main.c get_next_line.c get_next_line_utils.c -o gnl_test
```

---

## Bonus — Multiple File Descriptors

The bonus implementation uses a static array indexed by file descriptor, allowing simultaneous reading from multiple fds without losing each one's read state:

```c
#include "get_next_line_bonus.h"

// Both file descriptors can be read independently and alternately
char *line1 = get_next_line(fd1);
char *line2 = get_next_line(fd2);
char *line3 = get_next_line(fd1); // continues from where fd1 left off
```

---

## Configuration

The buffer size can be set at compile time using the `-D BUFFER_SIZE=N` flag. The default value in the header is `42` if not specified.

| Value | Effect |
|---|---|
| `1` | Reads one byte at a time (correct but slow) |
| `42` | Default — balanced performance |
| Large values | Faster I/O, larger memory footprint per call |

---

*Project developed at 42 Firenze as part of the Common Core curriculum.*
