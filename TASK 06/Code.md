##  Complete C Program -- Banker's Algorithm

The following program calculates the Need matrix, checks the safe
sequence and evaluates the three resource requests.

``` c
#include <stdio.h>

#define P 5
#define R 3

int available[R] = {3, 3, 2};

int maximum[P][R] = {
    {7, 5, 3},
    {3, 2, 2},
    {9, 0, 2},
    {2, 2, 2},
    {4, 3, 3}
};

int allocation[P][R] = {
    {0, 1, 0},
    {2, 0, 0},
    {3, 0, 2},
    {2, 1, 1},
    {0, 0, 2}
};

int need[P][R];

void calculateNeed()
{
    for (int i = 0; i < P; i++)
    {
        for (int j = 0; j < R; j++)
        {
            need[i][j] = maximum[i][j] - allocation[i][j];
        }
    }
}

int isSafe()
{
    int work[R];
    int finish[P] = {0};
    int safeSequence[P];
    int count = 0;

    for (int i = 0; i < R; i++)
        work[i] = available[i];

    while (count < P)
    {
        int found = 0;

        for (int i = 0; i < P; i++)
        {
            if (finish[i] == 0)
            {
                int j;

                for (j = 0; j < R; j++)
                {
                    if (need[i][j] > work[j])
                        break;
                }

                if (j == R)
                {
                    for (int k = 0; k < R; k++)
                        work[k] += allocation[i][k];

                    safeSequence[count++] = i;
                    finish[i] = 1;
                    found = 1;
                }
            }
        }

        if (!found)
            return 0;
    }

    printf("Safe sequence: ");

    for (int i = 0; i < P; i++)
        printf("P%d ", safeSequence[i]);

    printf("\n");

    return 1;
}

void requestResources(int process, int request[R])
{
    printf("\nP%d requests [%d %d %d]\n",
           process, request[0], request[1], request[2]);

    for (int j = 0; j < R; j++)
    {
        if (request[j] > need[process][j])
        {
            printf("Error: Request exceeds process need.\n");
            return;
        }

        if (request[j] > available[j])
        {
            printf("Request cannot be granted. Process must wait.\n");
            return;
        }
    }

    for (int j = 0; j < R; j++)
    {
        available[j] -= request[j];
        allocation[process][j] += request[j];
        need[process][j] -= request[j];
    }

    if (isSafe())
    {
        printf("Request granted. System is safe.\n");
    }
    else
    {
        for (int j = 0; j < R; j++)
        {
            available[j] += request[j];
            allocation[process][j] -= request[j];
            need[process][j] += request[j];
        }

        printf("Request denied. System would be unsafe.\n");
    }
}

int main()
{
    calculateNeed();

    printf("Need Matrix:\n");

    for (int i = 0; i < P; i++)
    {
        printf("P%d: ", i);

        for (int j = 0; j < R; j++)
            printf("%d ", need[i][j]);

        printf("\n");
    }

    printf("\nInitial Safety Check:\n");

    if (isSafe())
        printf("System is in a SAFE state.\n");
    else
        printf("System is in an UNSAFE state.\n");

    int request1[R] = {1, 0, 2};
    int request2[R] = {3, 3, 0};
    int request3[R] = {0, 2, 0};

    requestResources(1, request1);
    requestResources(4, request2);
    requestResources(0, request3);

    return 0;
}
```

