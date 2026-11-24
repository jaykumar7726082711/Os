# 4. Write a program(using fork () and/or exec () commands) where parent and child execute:

## i. same program, same code.
```
#include <iostream>
#include <unistd.h>
#include <sys/types.h>
using namespace std;

int main()
{
    fork();

    cout << "Hello from process!" << endl;

    return 0;
}

```

## ii same program, different code.
```
#include <iostream>
#include <unistd.h>
#include <sys/types.h>
using namespace std;

int main()
{
    pid_t pid = fork();

    if (pid == 0)
    {
        cout << "This is Child Process" << endl;
    }
    else if (pid > 0)
    {
        cout << "This is Parent Process" << endl;
    }
    else
    {
        cout << "Fork Failed" << endl;
    }

    return 0;
}
```
## iii. before terminating, the parent waits for the child to finish its task.
```
#include <iostream>
#include <unistd.h>
#include <sys/wait.h>
using namespace std;

int main()
{
    pid_t pid = fork();

    if (pid == 0)
    {
        cout << "Child Process Started" << endl;
        sleep(2);
        cout << "Child Process Finished" << endl;
    }
    else if (pid > 0)
    {
        wait(NULL);
        cout << "Parent Process Finished" << endl;
    }
    else
    {
        cout << "Fork Failed" << endl;
    }

    return 0;
}
```
# 5. Write a program to report behaviour of Linux kernel including kernel version, CPU type andCPU information.
```
#include <iostream>
#include <fstream>
#include <sys/utsname.h>
using namespace std;

int main()
{
    struct utsname info;

    if (uname(&info) == 0)
    {
        cout << "Linux Kernel Information" << endl;

        cout << "System Name: " << info.sysname << endl;
        cout << "Kernel Version: " << info.release << endl;
        cout << "Kernel Details: " << info.version << endl;
        cout << "Machine Type: " << info.machine << endl;
    }

    cout << "\nCPU Information:" << endl;

    ifstream file("/proc/cpuinfo");

    string line;

    while (getline(file, line))
    {
        if (line.find("model name") != string::npos ||
            line.find("vendor_id") != string::npos ||
            line.find("cpu cores") != string::npos)
        {
            cout << line << endl;
        }
    }

    return 0;
}
```
# 6. Write a program to report behaviour of Linux kernel including information on configured memory, amount of free and used memory. (Memory information)
```
#include <iostream>
#include <fstream>
#include <string>
using namespace std;

int main()
{
    ifstream file("/proc/meminfo");

    string line;
    long long total = 0, free = 0, available = 0;

    while (getline(file, line))
    {
        if (line.find("MemTotal:") == 0)
            total = stoll(line.substr(9));

        else if (line.find("MemFree:") == 0)
            free = stoll(line.substr(8));

        else if (line.find("MemAvailable:") == 0)
            available = stoll(line.substr(13));
    }

    cout << "Linux Memory Information" << endl;

    cout << "Total Memory: " << total / 1024 << " MB" << endl;
    cout << "Free Memory: " << free / 1024 << " MB" << endl;
    cout << "Available Memory: " << available / 1024 << " MB" << endl;
    cout << "Used Memory: " << (total - free) / 1024 << " MB" << endl;

    return 0;
}
```
# 7. Write a program to copy files using system calls.
```

```
# 8. Write a program to implement First Come First Serve (FCFS) scheduling algorithm.
```
#include <iostream>
#include <fcntl.h>
#include <unistd.h>
using namespace std;

int main()
{
    int source, destination;
    char buffer[1024];
    int n;

    source = open("source.txt", O_RDONLY);

    if (source < 0)
    {
        cout << "Source file cannot be opened!" << endl;
        return 1;
    }

    destination = open("destination.txt",
                       O_WRONLY | O_CREAT | O_TRUNC, 0644);

    if (destination < 0)
    {
        cout << "Destination file cannot be created!" << endl;
        close(source);
        return 1;
    }

    while ((n = read(source, buffer, sizeof(buffer))) > 0)
    {
        if (write(destination, buffer, n) != n)
        {
            cout << "File copying failed!" << endl;
            close(source);
            close(destination);
            return 1;
        }
    }

    close(source);
    close(destination);

    cout << "File copied successfully!" << endl;

    return 0;
}
```
# 9. Write a program to implement Shortest Job First (SJF) scheduling algorithm.
```
#include <iostream>
#include <algorithm>
using namespace std;

int main()
{
    int n;
    int bt[20], wt[20], tat[20], p[20];

    cout << "Enter number of processes: ";
    cin >> n;

    for (int i = 0; i < n; i++)
    {
        p[i] = i + 1;
        cout << "Enter burst time of P" << i + 1 << ": ";
        cin >> bt[i];
    }

    // Sorting according to burst time
    for (int i = 0; i < n - 1; i++)
    {
        for (int j = i + 1; j < n; j++)
        {
            if (bt[i] > bt[j])
            {
                swap(bt[i], bt[j]);
                swap(p[i], p[j]);
            }
        }
    }

    wt[0] = 0;

    for (int i = 1; i < n; i++)
    {
        wt[i] = wt[i - 1] + bt[i - 1];
    }

    float avgWT = 0, avgTAT = 0;

    cout << "\nProcess\tBT\tWT\tTAT\n";

    for (int i = 0; i < n; i++)
    {
        tat[i] = wt[i] + bt[i];

        avgWT += wt[i];
        avgTAT += tat[i];

        cout << "P" << p[i] << "\t"
             << bt[i] << "\t"
             << wt[i] << "\t"
             << tat[i] << endl;
    }

    cout << "\nAverage Waiting Time = " << avgWT / n;
    cout << "\nAverage Turnaround Time = " << avgTAT / n;

    return 0;
}
```
# 10. Write a program to implement non-preemptive priority-based scheduling algorithm.
```
#include <iostream>
#include <algorithm>
using namespace std;

int main()
{
    int n;
    int bt[20], wt[20], tat[20];
    int p[20], priority[20];

    cout << "Enter number of processes: ";
    cin >> n;

    for (int i = 0; i < n; i++)
    {
        p[i] = i + 1;

        cout << "Enter Burst Time for P" << i + 1 << ": ";
        cin >> bt[i];

        cout << "Enter Priority for P" << i + 1 << ": ";
        cin >> priority[i];
    }

    // Sorting according to priority
    // Lower number means higher priority

    for (int i = 0; i < n - 1; i++)
    {
        for (int j = i + 1; j < n; j++)
        {
            if (priority[i] > priority[j])
            {
                swap(priority[i], priority[j]);
                swap(bt[i], bt[j]);
                swap(p[i], p[j]);
            }
        }
    }

    wt[0] = 0;

    for (int i = 1; i < n; i++)
    {
        wt[i] = wt[i - 1] + bt[i - 1];
    }

    float avgWT = 0, avgTAT = 0;

    cout << "\nProcess\tBT\tPriority\tWT\tTAT\n";

    for (int i = 0; i < n; i++)
    {
        tat[i] = wt[i] + bt[i];

        avgWT += wt[i];
        avgTAT += tat[i];

        cout << "P" << p[i] << "\t"
             << bt[i] << "\t"
             << priority[i] << "\t\t"
             << wt[i] << "\t"
             << tat[i] << endl;
    }

    cout << "\nAverage Waiting Time = " << avgWT / n;
    cout << "\nAverage Turnaround Time = " << avgTAT / n;

    return 0;
}
```
# 11. Write a program to calculate sum of n numbers using Pthreads.
```
#include <iostream>
#include <pthread.h>
using namespace std;

int n, sum = 0;
int arr[100];

void* calculateSum(void* arg)
{
    for (int i = 0; i < n; i++)
    {
        sum += arr[i];
    }

    return NULL;
}

int main()
{
    pthread_t thread;

    cout << "Enter number of elements: ";
    cin >> n;

    cout << "Enter " << n << " numbers: ";

    for (int i = 0; i < n; i++)
    {
        cin >> arr[i];
    }

    pthread_create(&thread, NULL, calculateSum, NULL);

    pthread_join(thread, NULL);

    cout << "Sum of " << n << " numbers = " << sum << endl;

    return 0;
}

```
# 12. Write a program to implement first-fit, best-fit and worst-fit allocation strategies.
```
#include <iostream>
using namespace std;

void firstFit(int block[], int m, int process[], int n)
{
    int allocation[20];

    for (int i = 0; i < n; i++)
        allocation[i] = -1;

    for (int i = 0; i < n; i++)
    {
        for (int j = 0; j < m; j++)
        {
            if (block[j] >= process[i])
            {
                allocation[i] = j;
                block[j] -= process[i];
                break;
            }
        }
    }

    cout << "\nFirst Fit Allocation:\n";

    for (int i = 0; i < n; i++)
    {
        cout << "Process " << i + 1 << " -> ";

        if (allocation[i] != -1)
            cout << "Block " << allocation[i] + 1;
        else
            cout << "Not Allocated";

        cout << endl;
    }
}

void bestFit(int block[], int m, int process[], int n)
{
    int allocation[20];

    for (int i = 0; i < n; i++)
        allocation[i] = -1;

    for (int i = 0; i < n; i++)
    {
        int best = -1;

        for (int j = 0; j < m; j++)
        {
            if (block[j] >= process[i])
            {
                if (best == -1 || block[j] < block[best])
                    best = j;
            }
        }

        if (best != -1)
        {
            allocation[i] = best;
            block[best] -= process[i];
        }
    }

    cout << "\nBest Fit Allocation:\n";

    for (int i = 0; i < n; i++)
    {
        cout << "Process " << i + 1 << " -> ";

        if (allocation[i] != -1)
            cout << "Block " << allocation[i] + 1;
        else
            cout << "Not Allocated";

        cout << endl;
    }
}

void worstFit(int block[], int m, int process[], int n)
{
    int allocation[20];

    for (int i = 0; i < n; i++)
        allocation[i] = -1;

    for (int i = 0; i < n; i++)
    {
        int worst = -1;

        for (int j = 0; j < m; j++)
        {
            if (block[j] >= process[i])
            {
                if (worst == -1 || block[j] > block[worst])
                    worst = j;
            }
        }

        if (worst != -1)
        {
            allocation[i] = worst;
            block[worst] -= process[i];
        }
    }

    cout << "\nWorst Fit Allocation:\n";

    for (int i = 0; i < n; i++)
    {
        cout << "Process " << i + 1 << " -> ";

        if (allocation[i] != -1)
            cout << "Block " << allocation[i] + 1;
        else
            cout << "Not Allocated";

        cout << endl;
    }
}

int main()
{
    int m, n;
    int block[20], process[20];

    cout << "Enter number of memory blocks: ";
    cin >> m;

    cout << "Enter block sizes: ";

    for (int i = 0; i < m; i++)
        cin >> block[i];

    cout << "Enter number of processes: ";
    cin >> n;

    cout << "Enter process sizes: ";

    for (int i = 0; i < n; i++)
        cin >> process[i];

    int b1[20], b2[20], b3[20];

    for (int i = 0; i < m; i++)
    {
        b1[i] = block[i];
        b2[i] = block[i];
        b3[i] = block[i];
    }

    firstFit(b1, m, process, n);
    bestFit(b2, m, process, n);
    worstFit(b3, m, process, n);

    return 0;
}

```
