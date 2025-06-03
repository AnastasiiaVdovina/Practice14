# Практична робота №14
## Завдання 
### Умова
Змініть програму з прикладу Example program – POSIX interval timers так, щоб вона оновлювала системний лічильник у змінній (наприклад, рахувала тики).
Програма з прикладу:
```
#include <stdio.h>
#include <signal.h>
#include <string.h>
#include <time.h>
#include <unistd.h>

void handler(int sig, siginfo_t *si, void *uc) {
    write(STDOUT_FILENO, "Tick\n", 5);
}

int main() {
    struct sigaction sa = {0};
    sa.sa_flags = SA_SIGINFO;
    sa.sa_sigaction = handler;
    sigaction(SIGRTMIN, &sa, NULL);

    timer_t timerid;
    struct sigevent sev = {0};
    sev.sigev_notify = SIGEV_SIGNAL;
    sev.sigev_signo = SIGRTMIN;
    timer_create(CLOCK_REALTIME, &sev, &timerid);
    struct itimerspec its;
    its.it_value.tv_sec = 1;
    its.it_value.tv_nsec = 0;
    its.it_interval.tv_sec = 1;
    its.it_interval.tv_nsec = 0;

    timer_settime(timerid, 0, &its, NULL);

    while (1)
        pause();
}

```
### Рішення


## Компіляція та запуск
Команда компіляції програми:
```
gcc -Wall task.c -o task -lrt
```

Запуск:
```
./task
```
