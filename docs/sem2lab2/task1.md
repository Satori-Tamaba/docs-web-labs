# Задание 1 
*Задача*: Напишите три различных программы на Python, использующие каждый из подходов: threading, multiprocessing и async. Каждая программа должна решать считать сумму всех чисел от 1 до 10000000000000. Разделите вычисления на несколько параллельных задач для ускорения выполнения.

## Asyncio

```Python

import asyncio
import time

async def partial_sum(start, end):
    return sum(range(start, end))

async def main():
    n = 1_000_000_000
    num_tasks = 10
    chunk_size = n // num_tasks

    start_time = time.time()

    tasks = []
    for i in range(0, n, chunk_size):
        start = i + 1
        end = min(i + chunk_size, n) + 1
        tasks.append(partial_sum(start, end))

    results = await asyncio.gather(*tasks)
    total = sum(results)

    end_time = time.time()
    print(f"Сумма: {total}, время: {end_time - start_time:.2f} сек")

asyncio.run(main())
```
## Multiprocessing 
```Python
import multiprocessing
import os
import time


def partial_sum(start, end, res_queue):
    total = sum(range(start, end))
    res_queue.put(total)


def main():
    n = 1_000_000_000
    num_processes = 4
    chunk_size = n // num_processes

    res_queue = multiprocessing.Queue()
    processes = []

    start_time = time.time()

    for i in range(num_processes):
        start = i * chunk_size + 1
        if i == num_processes - 1:
            end = n + 1
        else:
            end = start + chunk_size

        p = multiprocessing.Process(
            target=partial_sum,
            args=(start, end, res_queue)
        )
        processes.append(p)
        p.start()

    for p in processes:
        p.join()

    results = []
    while not res_queue.empty():
        results.append(res_queue.get())

    total = sum(results)
    end_time = time.time()

    print(f"Сумма: {total}, время: {end_time - start_time:.2f} сек")


if __name__ == '__main__':
    main()
```
## Threading
```Python
import threading
import time

def partial_sum(start, end, res, lock):
    total = sum(range(start, end))
    with lock:
        res.append(total)

def main():
    n = 1_000_000_000
    num_threads = 4
    chunk_size = n // num_threads

    results = []
    lock = threading.Lock()
    threads = []

    start_time = time.time()


    for i in range(0, n, chunk_size):
        start = i + 1
        end = min(i + chunk_size, n) + 1

        t = threading.Thread(
            target=partial_sum,
            args=(start, end, results, lock))
        threads.append(t)
        t.start()

    for t in threads:
        t.join()

    total = sum(results)
    end_time = time.time()

    print(f"Сумма: {total}, время: {end_time - start_time:.2f} сек")

if __name__ == '__main__':
    main()
```
