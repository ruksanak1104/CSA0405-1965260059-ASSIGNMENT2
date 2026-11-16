##  Producer-Consumer Program Using Semaphore and Mutex

This program uses a bounded buffer of size 5. It supports 1 or 4
producer threads and 1 or 4 consumer threads. Each producer generates 20
unique items.

``` c
#define _POSIX_C_SOURCE 200809L
#include <stdio.h>
#include <stdlib.h>
#include <pthread.h>
#include <semaphore.h>

#define SIZE 5
#define ITEMS 20
#define MAX_THREADS 4

int buffer[SIZE];
int in = 0, out = 0;

sem_t empty, full;
pthread_mutex_t lock = PTHREAD_MUTEX_INITIALIZER;

long consumed_sum = 0;
int consumed_count = 0;

void *producer(void *arg)
{
    int id = *(int *)arg;

    for (int i = 0; i < ITEMS; i++)
    {
        int item = id * ITEMS + i + 1;

        sem_wait(&empty);
        pthread_mutex_lock(&lock);

        buffer[in] = item;
        in = (in + 1) % SIZE;

        pthread_mutex_unlock(&lock);
        sem_post(&full);
    }

    return NULL;
}

void *consumer(void *arg)
{
    (void)arg;

    for (int i = 0; i < ITEMS; i++)
    {
        sem_wait(&full);
        pthread_mutex_lock(&lock);

        int item = buffer[out];
        out = (out + 1) % SIZE;

        consumed_sum += item;
        consumed_count++;

        pthread_mutex_unlock(&lock);
        sem_post(&empty);
    }

    return NULL;
}

int main(int argc, char *argv[])
{
    int producers = 1;
    int consumers = 1;

    if (argc == 3)
    {
        producers = atoi(argv[1]);
        consumers = atoi(argv[2]);
    }

    if (producers < 1 || producers > MAX_THREADS ||
        consumers < 1 || consumers > MAX_THREADS)
    {
        printf("Use producer and consumer counts from 1 to 4\n");
        return 1;
    }

    pthread_t p[MAX_THREADS], c[MAX_THREADS];
    int ids[MAX_THREADS];

    sem_init(&empty, 0, SIZE);
    sem_init(&full, 0, 0);

    for (int i = 0; i < producers; i++)
    {
        ids[i] = i;
        pthread_create(&p[i], NULL, producer, &ids[i]);
    }

    for (int i = 0; i < consumers; i++)
    {
        pthread_create(&c[i], NULL, consumer, NULL);
    }

    for (int i = 0; i < producers; i++)
        pthread_join(p[i], NULL);

    for (int i = 0; i < consumers; i++)
        pthread_join(c[i], NULL);

    printf("Number of producers: %d\n", producers);
    printf("Number of consumers: %d\n", consumers);
    printf("Items consumed: %d\n", consumed_count);
    printf("Sum of consumed items: %ld\n", consumed_sum);

    sem_destroy(&empty);
    sem_destroy(&full);
    pthread_mutex_destroy(&lock);

    return 0;
}
```
