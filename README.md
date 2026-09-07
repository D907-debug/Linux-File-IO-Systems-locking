# Linux-File-IO-Systems-locking
Ex07-Linux File-IO Systems-locking
# AIM:
To Write a C program that illustrates files copying and locking

# DESIGN STEPS:

### Step 1:

Navigate to any Linux environment installed on the system or installed inside a virtual environment like virtual box/vmware or online linux JSLinux (https://bellard.org/jslinux/vm.html?url=alpine-x86.cfg&mem=192) or docker.

### Step 2:

Write the C Program using Linux IO Systems locking

### Step 3:

Execute the C Program for the desired output. 

# PROGRAM:

## 1.To Write a C program that illustrates files copying 

```
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
    out = open(argv[2], O_WRONLY | O_CREAT | O_TRUNC,
               S_IRUSR | S_IWUSR);

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



## OUTPUT
<img width="613" height="297" alt="Screenshot 2026-09-01 155243" src="https://github.com/user-attachments/assets/5fd42d23-818c-4573-b0b3-d5eb573393c1" />


## 2.To Write a C program that illustrates files locking

```
#include <fcntl.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/file.h>

void display_lslocks()
{
    printf("\nCurrent lslocks output:\n");
    fflush(stdout);
    system("lslocks");
}

int main(int argc, char *argv[])
{
    int fd;

    if (argc != 2)
    {
        fprintf(stderr, "Usage: %s <filename>\n", argv[0]);
        return EXIT_FAILURE;
    }

    printf("Opening %s\n", argv[1]);

    /* Open the file for reading and writing */
    fd = open(argv[1], O_RDWR | O_CREAT, 0644);

    if (fd == -1)
    {
        perror("Error opening file");
        return EXIT_FAILURE;
    }

    /* Acquire shared lock */
    if (flock(fd, LOCK_SH) == -1)
    {
        perror("Error acquiring shared lock");
        close(fd);
        return EXIT_FAILURE;
    }

    printf("Acquired shared lock using flock()\n");
    display_lslocks();

    sleep(2);

    /* Upgrade shared lock to exclusive lock */
    if (flock(fd, LOCK_EX | LOCK_NB) == -1)
    {
        perror("Error upgrading to exclusive lock");
        flock(fd, LOCK_UN);
        close(fd);
        return EXIT_FAILURE;
    }

    printf("Acquired exclusive lock using flock()\n");
    display_lslocks();

    sleep(2);

    /* Release the lock */
    if (flock(fd, LOCK_UN) == -1)
    {
        perror("Error unlocking");
        close(fd);
        return EXIT_FAILURE;
    }

    printf("Unlocked successfully\n");
    display_lslocks();

    close(fd);

    return EXIT_SUCCESS;
}

```



## OUTPUT

<img width="642" height="461" alt="Screenshot 2026-09-01 160107" src="https://github.com/user-attachments/assets/a35a59e9-88d5-4160-9f23-a18b9da8ba22" />


<img width="642" height="497" alt="image" src="https://github.com/user-attachments/assets/10735aa4-e529-4f32-9e32-a3a3575e202d" />

# RESULT:
The programs are executed successfully.


.....
