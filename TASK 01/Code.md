#include <stdio.h>

#include <unistd.h>

#include <sys/wait.h>

int main()
{
    pid_t pid;

    printf("Parent Process ID: %d\n", getpid());

    for (int i = 1; i <= 3; i++)
    {
        pid = fork();

        if (pid < 0)
        {
            printf("Fork failed\n");
            return 1;
        }
        else if (pid == 0)
        {
            printf("Child %d: PID = %d, PPID = %d\n",
                   i, getpid(), getppid());
            return 0;
        }
    }

    while (wait(NULL) > 0);

    printf("Parent process completed\n");

    return 0;
}

