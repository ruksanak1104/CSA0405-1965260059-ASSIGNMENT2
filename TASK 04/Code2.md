##  Program 2 -- IPC Using Shared Memory

This program uses shared memory to transfer sensor data between parent
and child processes. A semaphore ensures that the child reads the data
only after the parent has written it.

``` c
#define _DEFAULT_SOURCE
#include <stdio.h>
#include <unistd.h>
#include <sys/mman.h>
#include <sys/wait.h>
#include <semaphore.h>

#define N 6

struct SharedData
{
    sem_t ready;
    int readings[N];
};

int main()
{
    struct SharedData *data;

    data = mmap(NULL, sizeof(struct SharedData),
                PROT_READ | PROT_WRITE,
                MAP_SHARED | MAP_ANONYMOUS, -1, 0);

    if (data == MAP_FAILED)
    {
        perror("mmap");
        return 1;
    }

    if (sem_init(&data->ready, 1, 0) == -1)
    {
        perror("sem_init");
        munmap(data, sizeof(struct SharedData));
        return 1;
    }

    pid_t pid = fork();

    if (pid < 0)
    {
        perror("fork");
        return 1;
    }

    if (pid == 0)
    {
        sem_wait(&data->ready);

        int sum = 0;
        int max = data->readings[0];

        for (int i = 0; i < N; i++)
        {
            sum += data->readings[i];

            if (data->readings[i] > max)
                max = data->readings[i];
        }

        printf("analyser: %d readings, average %.1f, max %d\n",
               N, (double)sum / N, max);

        return 0;
    }
    else
    {
        int values[N] = {31, 33, 36, 34, 41, 38};

        for (int i = 0; i < N; i++)
        {
            data->readings[i] = values[i];
        }

        sem_post(&data->ready);

        wait(NULL);
        printf("reader: done\n");

        sem_destroy(&data->ready);
        munmap(data, sizeof(struct SharedData));
    }

    return 0;
}
```
