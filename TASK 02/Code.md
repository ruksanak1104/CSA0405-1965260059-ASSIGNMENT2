##  Complete C Program -- Round Robin Scheduling

**Aim:** To implement the Round Robin CPU scheduling algorithm and
calculate turnaround time, waiting time and response time for each
process.

The following is the corrected and complete C program based on the TODO
given in the assignment. It supports different time quantum values.

``` c
#include <stdio.h>
#include <stdlib.h>

#define N 4
#define MAX 1000

int main(int argc, char *argv[])
{
    const char *name[N] = {"P1", "P2", "P3", "P4"};

    int arrival[N] = {0, 1, 2, 3};
    int burst[N] = {8, 4, 9, 5};

    int quantum = 3;

    if (argc > 1)
    {
        quantum = atoi(argv[1]);

        if (quantum <= 0)
        {
            printf("Invalid time quantum\n");
            return 1;
        }
    }

    int left[N], finish[N], response[N];
    int queue[MAX], added[N] = {0};

    int head = 0, tail = 0;
    int time = 0, done = 0;

    for (int i = 0; i < N; i++)
    {
        left[i] = burst[i];
        response[i] = -1;
    }

    queue[tail++] = 0;
    added[0] = 1;

    printf("Gantt:");

    while (done < N)
    {
        if (head == tail)
        {
            time++;

            for (int i = 0; i < N; i++)
            {
                if (!added[i] && arrival[i] <= time)
                {
                    queue[tail++] = i;
                    added[i] = 1;
                }
            }
            continue;
        }

        int p = queue[head++];

        if (response[p] == -1)
            response[p] = time - arrival[p];

        int run = (left[p] < quantum) ? left[p] : quantum;

        int start = time;
        time += run;
        left[p] -= run;

        printf(" %s(%d-%d)", name[p], start, time);

        for (int i = 0; i < N; i++)
        {
            if (!added[i] && arrival[i] <= time)
            {
                queue[tail++] = i;
                added[i] = 1;
            }
        }

        if (left[p] > 0)
        {
            queue[tail++] = p;
        }
        else
        {
            finish[p] = time;
            done++;
        }
    }

    double total_tat = 0;
    double total_wt = 0;

    printf("\n\nProcess TAT WT RT\n");

    for (int i = 0; i < N; i++)
    {
        int tat = finish[i] - arrival[i];
        int wt = tat - burst[i];

        total_tat += tat;
        total_wt += wt;

        printf("%-7s %3d %3d %3d\n",
               name[i], tat, wt, response[i]);
    }

    printf("\nAverage TAT = %.2f", total_tat / N);
    printf("\nAverage WT = %.2f\n", total_wt / N);

    return 0;
}
```
