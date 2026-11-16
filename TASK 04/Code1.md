## 3. Program 1 -- IPC Using Pipe

This program creates a parent and child process. The parent sends six
sensor readings through a pipe, and the child calculates their average
and maximum value.

``` c
#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>

int main()
{
    int fd[2];
    int readings[] = {31, 33, 36, 34, 41, 38};
    int n = 6;

    if (pipe(fd) == -1)
    {
        printf("Pipe creation failed\n");
        return 1;
    }

    pid_t pid = fork();

    if (pid < 0)
    {
        printf("Fork failed\n");
        return 1;
    }

    if (pid == 0)
    {
        close(fd[1]);

        int value;
        int count = 0;
        int sum = 0;
        int max = 0;

        while (read(fd[0], &value, sizeof(value)) > 0)
        {
            sum += value;

            if (count == 0 || value > max)
                max = value;

            count++;
        }

        close(fd[0]);

        if (count > 0)
        {
            printf("analyser: %d readings, average %.1f, max %d\n",
                   count, (double)sum / count, max);
        }

        return 0;
    }
    else
    {
        close(fd[0]);

        for (int i = 0; i < n; i++)
        {
            write(fd[1], &readings[i], sizeof(readings[i]));
        }

        close(fd[1]);
        wait(NULL);

        printf("reader: done\n");
    }

    return 0;
}
```
