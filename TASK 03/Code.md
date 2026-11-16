##  Complete C Program -- Thread Processing

**Aim:** To implement multithreaded processing using POSIX threads and
compare the execution time of different thread configurations.

The following complete program supports all four configurations and
compares them with sequential execution.

``` c
#include <stdio.h>
#include <pthread.h>
#include <time.h>

#define CAMERAS 64
#define ITERATIONS 200000

unsigned long long results[CAMERAS];

int next_camera = 0;
pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER;

void process_camera(int camera)
{
    volatile unsigned long long value = 0;

    for (int i = 0; i < ITERATIONS; i++)
    {
        value += (unsigned long long)(i + 1) * (camera + 1);
    }

    results[camera] = value;
}

void *worker(void *arg)
{
    (void)arg;

    while (1)
    {
        pthread_mutex_lock(&lock);

        int camera = next_camera++;

        pthread_mutex_unlock(&lock);

        if (camera >= CAMERAS)
            break;

        process_camera(camera);
    }

    return NULL;
}

double get_time()
{
    static struct timespec start;
    clock_gettime(CLOCK_MONOTONIC, &start);

    return start.tv_sec + start.tv_nsec / 1000000000.0;
}

int main(int argc, char *argv[])
{
    int threads = CAMERAS;

    if (argc > 1)
    {
        sscanf(argv[1], "%d", &threads);
    }

    if (threads != 1 && threads != 4 &&
        threads != 8 && threads != 16 &&
        threads != 64)
    {
        printf("Use 1, 4, 8, 16 or 64 threads\n");
        return 1;
    }

    double start, end;

    if (threads == 1)
    {
        start = get_time();

        for (int i = 0; i < CAMERAS; i++)
        {
            process_camera(i);
        }

        end = get_time();

        printf("Sequential processing completed\n");
    }
    else
    {
        pthread_t tid[CAMERAS];

        next_camera = 0;

        start = get_time();

        for (int i = 0; i < threads; i++)
        {
            pthread_create(&tid[i], NULL, worker, NULL);
        }

        for (int i = 0; i < threads; i++)
        {
            pthread_join(tid[i], NULL);
        }

        end = get_time();

        printf("Parallel processing completed\n");
    }

    printf("Number of cameras: %d\n", CAMERAS);
    printf("Number of threads: %d\n", threads);
    printf("Execution time: %.6f seconds\n", end - start);

    pthread_mutex_destroy(&lock);

    return 0;
}
```
