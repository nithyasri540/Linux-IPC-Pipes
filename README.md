# Linux-IPC--Pipes
Linux-IPC-Pipes


# Ex03-Linux IPC - Pipes

# AIM:
To write a C program that illustrate communication between two process using unnamed and named pipes

# DESIGN STEPS:

### Step 1:

Navigate to any Linux environment installed on the system or installed inside a virtual environment like virtual box/vmware or online linux JSLinux (https://bellard.org/jslinux/vm.html?url=alpine-x86.cfg&mem=192) or docker.

### Step 2:

Write the C Program using Linux Process API - pipe(), fifo()

### Step 3:

Testing the C Program for the desired output. 

# PROGRAM:

## C Program that illustrate communication between two process using unnamed pipes using Linux API system calls

```
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
#include <sys/types.h>
#include <sys/wait.h>
#include <fcntl.h>

void server(int rfd, int wfd);
void client(int wfd, int rfd);

int main()
{
    int p1[2], p2[2], pid;

    pipe(p1);
    pipe(p2);

    pid = fork();

    if (pid == 0)
    {
        // Child process - Server
        close(p1[1]);
        close(p2[0]);

        server(p1[0], p2[1]);
        exit(0);
    }
    else
    {
        // Parent process - Client
        close(p1[0]);
        close(p2[1]);

        client(p1[1], p2[0]);

        wait(NULL);
    }

    return 0;
}

void server(int rfd, int wfd)
{
    char fname[2000] = {0};
    char buff[2000];

    int n = read(rfd, fname, sizeof(fname) - 1);
    if (n <= 0)
        return;

    fname[n] = '\0';

    int fd = open(fname, O_RDONLY);

    if (fd < 0)
    {
        char msg[] = "Cannot open file\n";
        write(wfd, msg, strlen(msg));
    }
    else
    {
        n = read(fd, buff, sizeof(buff));
        if (n > 0)
            write(wfd, buff, n);

        close(fd);
    }
}

void client(int wfd, int rfd)
{
    char fname[2000];
    char buff[2000];

    printf("Enter filename: ");
    scanf("%1999s", fname);

    write(wfd, fname, strlen(fname));

    int n = read(rfd, buff, sizeof(buff));

    if (n > 0)
        write(STDOUT_FILENO, buff, n);
}
```



## OUTPUT
<img width="700" height="141" alt="Screenshot 2026-10-28 140358" src="https://github.com/user-attachments/assets/fd2545ae-06b0-4570-ae18-dc1d1f387502" />



## C Program that illustrate communication between two process using named pipes using Linux API system calls
```

#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/stat.h>
#include <fcntl.h>

int main()
{
    int fd;
    char message[] = "Hello from Named Pipe!";
    char buffer[100] = {0};

    mkfifo("myfifo", 0666);

    if (fork() == 0)
    {
        fd = open("myfifo", O_RDONLY);
        read(fd, buffer, sizeof(buffer) - 1);
        printf("Reader received: %s\n", buffer);
        close(fd);
    }
    else
    {
        fd = open("myfifo", O_WRONLY);
        write(fd, message, sizeof(message));
        close(fd);
        wait(NULL);
    }

    unlink("myfifo");
    return 0;
}
```




## OUTPUT
<img width="913" height="142" alt="Screenshot 2026-10-28 140649" src="https://github.com/user-attachments/assets/f91462b1-3e89-4c3c-8d91-4533d1f02049" />


# RESULT:
The program is executed successfully.
