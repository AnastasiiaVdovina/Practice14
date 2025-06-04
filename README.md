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
Все, що мені треба було зробити - це трохи видозмінити вже готовий код з лекції. 
Я додала глобальну змінну, яка буде інкрементуватися з кожним "тиком" , тобто рахувати їх.
До неї я додала ключове слово volatile що означає, що ця змінна може бути змінювана декількома потоками, які виконуються одночасно, тобто тут в нас нотка асинхронності.
Всі зміни я вносила лише до функції handler, яка обробляє сигнал. 
Туди я додала постфіксний інкремент змінної tick_count. Далі за допомогою snprintf формується рядок типу "Tick: 3\n".
write — неблокуюча, signal-safe функція для виводу в stdout, яка була в початковому коді. Тут я лише замінила її аргументи.

Тож загалом, цей код реалізує простий лічильник, який інкрементується щосекунди за допомогою POSIX таймера. Програма створює таймер, що надсилає сигнал SIGRTMIN кожну секунду, і обробник сигналу збільшує глобальну змінну tick_count, після чого виводить її значення у термінал. Таймер налаштовується як інтервальний, з початковим затриманням 1 секунда та повторенням щосекунди. У головному циклі програма чекає сигналів за допомогою pause(), не витрачаючи ресурси процесора на зайві обчислення.

## Компіляція та запуск
Команда компіляції програми:
```
gcc -Wall task.c -o task -lrt
```

Запуск:
```
./task
```
