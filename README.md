# Linux-File-IO-Systems-locking

Ex07-Linux File-IO Systems-locking

# AIM:

To Write a C program that illustrates files copying and locking

# DESIGN STEPS:

### Step 1:

Navigate to any Linux environment installed on the system or installed inside a virtual environment like virtual box/vmware or online linux JSLinux or docker.

### Step 2:

Write the C Program using Linux IO Systems locking

### Step 3:

Execute the C Program for the desired output.

# PROGRAM:

## 1.To Write a C program that illustrates files copying

```c
#include <unistd.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <stdlib.h>
#include <stdio.h>

int main(int argc, char *argv[]) {
    if (argc != 3) {
        fprintf(stderr, "Usage: %s <source_file> <destination_file>\n", argv[0]);
        exit(EXIT_FAILURE);
    }

    char block[1024];
    int in, out;
    ssize_t nread;

    // Open source file
    in = open(argv[1], O_RDONLY);
    if (in == -1) {
        perror("Error opening source file");
        exit(EXIT_FAILURE);
    }

    // Open destination file
    out = open(argv[2], O_WRONLY | O_CREAT | O_TRUNC, S_IRUSR | S_IWUSR);
    if (out == -1) {
        perror("Error opening destination file");
        close(in);
        exit(EXIT_FAILURE);
    }

    // Copy contents
    while ((nread = read(in, block, sizeof(block))) > 0) {
        if (write(out, block, nread) != nread) {
            perror("Error writing to destination file");
            close(in);
            close(out);
            exit(EXIT_FAILURE);
        }
    }

    if (nread == -1) {
        perror("Error reading source file");
    }

    close(in);
    close(out);
    return EXIT_SUCCESS;
}
```

### Compilation

```bash
cc -c filecopy.c -o filecopy.o
cc filecopy.o -o filecopy
```

### Execution

```bash
echo "This is the original file content for copy test" > source.txt
./filecopy source.txt dest.txt
cat dest.txt
```

## 2.To Write a C program that illustrates files locking

```c
#include <fcntl.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/file.h>

void display_lslocks() {
    printf("\nCurrent `lslocks` output:\n");
    fflush(stdout);
    system("lslocks");
}

int main(int argc, char *argv[]) {
    if (argc < 2) {
        fprintf(stderr, "Usage: %s <filename>\n", argv[0]);
        exit(EXIT_FAILURE);
    }

    char *file = argv[1];
    int fd;

    printf("Opening %s\n", file);

    fd = open(file, O_WRONLY);
    if (fd == -1) {
        perror("Error opening file");
        exit(EXIT_FAILURE);
    }

    // Acquire shared lock
    if (flock(fd, LOCK_SH) == -1) {
        perror("Error acquiring shared lock");
        close(fd);
        exit(EXIT_FAILURE);
    }
    printf("Acquired shared lock using flock\n");
    display_lslocks();

    sleep(1); // Simulate waiting before upgrading

    // Try to upgrade to exclusive lock (non-blocking)
    if (flock(fd, LOCK_EX | LOCK_NB) == -1) {
        perror("Error upgrading to exclusive lock");
        flock(fd, LOCK_UN); // Release shared lock if upgrade fails
        close(fd);
        exit(EXIT_FAILURE);
    }
    printf("Acquired exclusive lock using flock\n");
    display_lslocks();

    sleep(1); // Simulate waiting before unlocking

    // Release lock
    if (flock(fd, LOCK_UN) == -1) {
        perror("Error unlocking");
        close(fd);
        exit(EXIT_FAILURE);
    }
    printf("Unlocked\n");
    display_lslocks();

    close(fd);
    return 0;
}
```

### Compilation

```bash
cc -c lock.c -o lock.o
cc lock.o -o lock
```

### Execution

```bash
echo "This file will be locked" > lockfile.txt
./lock lockfile.txt
```

## OUTPUT

1.file copy :
<img width="816" height="646" alt="Screenshot 2026-08-08 140436" src="https://github.com/user-attachments/assets/beac8644-d0e8-40f8-9047-1b1569864ba9" />

2.file locking :
<img width="813" height="662" alt="Screenshot 2026-08-08 140513" src="https://github.com/user-attachments/assets/55b10905-e564-4459-9ba0-ce26f996e057" />
<img width="805" height="667" alt="Screenshot 2026-08-08 140533" src="https://github.com/user-attachments/assets/15171536-8f02-48a2-89a4-a357dcf4193a" />
<img width="815" height="661" alt="Screenshot 2026-08-08 140549" src="https://github.com/user-attachments/assets/4adaa300-2891-4d2c-9242-583a880746b7" />



# RESULT:

The programs are executed successfully.
