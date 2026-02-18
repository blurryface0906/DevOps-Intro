# Lab 3 — CI/CD with GitHub Actions

Platform: GitHub Actions

## Task 1

#### Ссылка на успешный ран:
https://github.com/blurryface0906/DevOps-Intro/actions/runs/22152609486

#### Ключевые изученные концепции:
- **Workflow** — автоматизированный процесс, описанный в yml-файле
- **Jobs** — набор шагов, выполняющихся на одном runner
- **Steps** — отдельные задачи внутри job
- **Runner** — виртуальная машина, на которой выполняется job
- **Trigger** — событие, запускающее workflow (например: push, PR и т.д.)

#### Что вызвало запуск:
Событие `push` в ветку `feature/lab3` автоматически запустило workflow. То есть GitHub обнаружил изменения в ветке и инициировал выполнение всех workflow, у которых в триггерах указан `push` для этой ветки.

#### Анализ процесса выполнения:
Run появился во вкладке Actions --> Run открылся --> джоба(job) последовательно выполнила шаги (checkout --> basic info).

Если какой-то шаг падает с ошибкой, следующие шаги не запускаются, а весь workflow помечается как failed.


## Task 2

#### Изменения внесённые в workflow файл:
- Добавлен триггер `workflow_dispatch` в секцию `on`. Это позволит запускать workflow вручную через UI GitHub Actions.
- Добавлены шаги, которые выводят информацию об ОС/CPU/RAM/Диске и окружении раннера.

#### Системная информация, полученная из раннера:
```
    OS
    Linux runnervmjduv7 6.14.0-1017-azure #17~24.04.1-Ubuntu SMP Mon Dec  1 20:10:50 UTC 2025 x86_64 x86_64 x86_64 GNU/Linux
    CPU
    Architecture:                            x86_64
    CPU op-mode(s):                          32-bit, 64-bit
    Address sizes:                           48 bits physical, 48 bits virtual
    Byte Order:                              Little Endian
    CPU(s):                                  4
    On-line CPU(s) list:                     0-3
    Vendor ID:                               AuthenticAMD
    Model name:                              AMD EPYC 7763 64-Core Processor
    CPU family:                              25
    Model:                                   1
    Thread(s) per core:                      2
    ...
    ...
    
    RAM
                   total        used        free      shared  buff/cache   available
    Mem:            15Gi       1.0Gi        13Gi        40Mi       1.8Gi        14Gi
    Swap:          3.0Gi          0B       3.0Gi
    Disk
    Filesystem      Size  Used Avail Use% Mounted on
    /dev/root       145G   53G   92G  37% /
    tmpfs           7.9G   84K  7.9G   1% /dev/shm
    tmpfs           3.2G 1008K  3.2G   1% /run
    tmpfs           5.0M     0  5.0M   0% /run/lock
    efivarfs        128M   26K  128M   1% /sys/firmware/efi/efivars
    /dev/sda16      881M   63M  757M   8% /boot
    /dev/sda15      105M  6.2M   99M   6% /boot/efi
    tmpfs           1.6G   12K  1.6G   1% /run/user/1001
```

```
    Run echo "Runner OS: $RUNNER_OS"
    Runner OS: Linux
    Runner Architecture: X64
    Workflow: lab3
    Action: __run_3
```

#### Сравнение ручного и автоматического workflow триггера:
- Автоматически: запускается сам при push-событиях, то есть когда я пушу коммиты.
- Вручную: событие workflow_dispatch. Запускается из GitHub UI с помощью кнопки `Run workflow`.

#### Анализ окружения и возможностей Runner:
Runner - это виртуальная машина Ubuntu, которую хостит GitHub. Ресурсов этого базового раннера достаточно для выполнения простых задач CI.
