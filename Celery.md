# Celery - Priority Queue & Multiprocess
---
Refer to [ThuruThuru - Celery Architecture](https://thuruthuru.com/post/celery-architecture/)

- Python 
- Multiprocess
- Priority Queue
- Back ground tasks

## Celery Architecture
---
![Imgur: Celery Architecture](https://imgur.com/kUx5Ykj.png)
> #### Why I need celery?
> - Priority Queue
> - Multiprocess (Work around Python GIL)
>   - thread pool with priority
>   - retry task mechanism
>   - task monitor (flower)

## Celery Work Flow
1. Producer will create task and send it to Broker.
2. Broker distributes the task into different queues by the router key.
3. Celery worker fetch from the specific queue.
4. Celery worker acknowledge Broker to delete the task message.
5. Celery worker executes the task and store the result in backend.
6. TODO How result goes back to producer

## Work around Python GIL 
---
Refer to [What is the Python Global Interpreter Lock (GIL)? – Real Python](https://realpython.com/python-gil/)
> #### Why need Python GIL
> - Avoid memory race condition - python object count will never end, which will cause memory leak or worse.
> - Using lock objects might cause multiple lock, which results deadlock.
>
> -> Single lock on the interpreter - only one gets the interpreter at the time
> 
>   - Other languages might use other method - garbage collection
> - Some use GIL and adding boosting features - JIT compilers 

#### Impact on multi-threaded Python programs
> - CPU bound (mathmetical program)
>   - GIL prevented the CPU-bound threads from executing in parellel.
> - I/O bound (network, files)
>   - GIL does not have much impact on the performance of I/O-bound multi-threaded programs
>   - Since no GIL still have to wait for OS to release resource.
>   

## Pratical Experiences and Observations 
---
1. We are able to limit the memory use of each worker.
2. Easily to see task status (success or fail) by using celery flower.
3. Each subprocess also could be set on different core.
4. Great scalibility on workers.



